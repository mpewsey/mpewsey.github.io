---
layout: post
title: Local Multiplayer Inputs in Godot
tags: game-design
mathjax: false
published: true
image: https://github.com/user-attachments/assets/e34fbe03-cde0-46e7-92a4-253ca2fc8c0f
---

This post explores how to setup local multiplayer inputs in Godot.

<!--excerpt-->

![Local Multiplayer Game Screenshot](https://github.com/user-attachments/assets/e34fbe03-cde0-46e7-92a4-253ca2fc8c0f)

## Input Map Setup

The process for creating input actions for local multiplayer is very similar to single player. However, under `Project > Project Settings > Input Map`, instead of using generic names for inputs, such as `jump` or `fire`, you will need to creates distinct input actions for each player, for instance `p1_jump` or `p2_jump` for players 1 and 2, respectively. Additionally, for controller inputs, you will need to specify the device number, such as `Device 0` and `Device 1` for players 1 and 2, respectively, while editing the event.

Keyboard and mouse inputs are treated as single devices by Godot. If you wish to support multiple keyboards or mice, that will require an advanced solution, possibly involving a custom compilation of the Godot Engine. Such support is beyond the scope of this post.

## Reading Player Inputs

To read and apply each player's inputs, you can follow the same process as you would a single player character. However, since you will need to know the specific player input names, you will either need to assign the input names to properties or construct them by concatenating names with a player ID.

Typically, I prefer to create a generic character scene with interface methods that can be controlled by anything -- human players or AI. Then I create separate player input scenes for each player, which can be instantiated as a child of the generic character to apply the respective player controls.

![Player Inputs Example](https://github.com/user-attachments/assets/946d73d0-deb5-442e-8d1d-8c9a844d6569)

## Global UI Navigation & Input Remapping

Godot's built-in UI navigation only supports single player. For some games, this is enough. However, if you need to have separate player navigation inputs to support keyboard, such as `p1_accept` or `p2_accept`, you will also need to add those inputs under the corresponding built-in action, such as `ui_accept`. I find keeping a collection of player action names paired with their built-in action names is useful for forwarding any initial and remapped player actions onward to the appropriate built-in actions. For example:

| Player Input Name | Built-in Input Name |
| ----------------- | ------------------- |
| `p1_accept` | `ui_accept` |
| `p2_accept` | `ui_accept` |
| `p1_cancel` | `ui_cancel` |
| `p2_cancel` | `ui_cancel` |
| `p1_move_right` | `ui_right` |
| `p2_move_right` | `ui_right` |
| etc. | etc. |

## Independent Player UI Navigation

While Godot does not provide built-in multiplayer UI navigation, it is possible to setup your own navigation through scripting, as demonstrated in the following scripts.

One `PlayerNavigation` node is required per player and is responsible for processing that player's inputs to perform navigation between buttons, as well as dispatching the appropriate button events. These events are defined in the `IPlayerNavigation` interface.

These scripts reuse the existing built-in button implementation as much as possible. This allows button signals to be subscribed to the same as usual, while also allowing mouse interactions to be retained. Each button has their focus mode set to `None`, so that they are no longer considered by the built-in navigation system.

Automatic button navigation is not implemented in these scripts. Rather, the paths to each button's focus neighbors need to be explicitly assigned manually or via other scripts.

```csharp
using Godot;
using System.Collections.Generic;

[GlobalClass]
public partial class PlayerNavigation : Node
{
    private static StringName DefaultSubmit { get; } = "ui_accept";
    private static StringName DefaultMoveUp { get; } = "ui_up";
    private static StringName DefaultMoveDown { get; } = "ui_down";
    private static StringName DefaultMoveLeft { get; } = "ui_left";
    private static StringName DefaultMoveRight { get; } = "ui_right";

    public static int CurrentPlayerId { get; private set; }
    private static Dictionary<int, PlayerNavigation> Navigations { get; } = new Dictionary<int, PlayerNavigation>();

    [Export] public int PlayerId { get; set; } = 1;
    [Export] public float FirstRepeatDelay { get; set; } = 0.2f;
    [Export] public float RepeatDelay { get; set; } = 0.1f;
    [Export(PropertyHint.InputName, "show_builtin")] public StringName Submit { get; set; } = DefaultSubmit;
    [Export(PropertyHint.InputName, "show_builtin")] public StringName MoveUp { get; set; } = DefaultMoveUp;
    [Export(PropertyHint.InputName, "show_builtin")] public StringName MoveDown { get; set; } = DefaultMoveDown;
    [Export(PropertyHint.InputName, "show_builtin")] public StringName MoveLeft { get; set; } = DefaultMoveLeft;
    [Export(PropertyHint.InputName, "show_builtin")] public StringName MoveRight { get; set; } = DefaultMoveRight;

    public Control Focus { get; private set; }
    private int NavigationCount { get; set; }
    private bool NavigationEnabled { get; set; } = true;
    private bool ButtonDown { get; set; }
    private bool ButtonUp { get; set; }
    private Timer DelayTimer { get; set; }
    private Viewport Viewport { get; set; }

    public static PlayerNavigation GetNavigation(int playerId)
    {
        return Navigations[playerId];
    }

    public static int GetFocusCount(Control control)
    {
        var count = 0;

        if (!IsInstanceValid(control))
            return count;

        foreach (var navigation in Navigations.Values)
        {
            if (IsInstanceValid(navigation.Focus) && navigation.Focus == control)
                count++;
        }

        return count;
    }

    public static int GetFocusMask(Control control)
    {
        var mask = 0;

        if (!IsInstanceValid(control))
            return mask;

        foreach (var navigation in Navigations.Values)
        {
            if (IsInstanceValid(navigation.Focus) && navigation.Focus == control)
                mask |= 1 << navigation.PlayerId;
        }

        return mask;
    }

    public override void _Ready()
    {
        base._Ready();
        CreateDelayTimer();
    }

    public override void _EnterTree()
    {
        base._EnterTree();
        Viewport = GetViewport();

        if (Navigations.ContainsKey(PlayerId))
        {
            QueueFree();
            return;
        }

        Navigations.Add(PlayerId, this);
    }

    public override void _ExitTree()
    {
        base._ExitTree();

        if (Navigations.TryGetValue(PlayerId, out var navigation) && navigation == this)
            Navigations.Remove(PlayerId);
    }

    private void CreateDelayTimer()
    {
        DelayTimer = new Timer() { OneShot = true };
        DelayTimer.Timeout += OnDelayTimeout;
        AddChild(DelayTimer);
    }

    private void OnDelayTimeout()
    {
        NavigationEnabled = true;
    }

    public override void _Process(double delta)
    {
        base._Process(delta);
        CurrentPlayerId = PlayerId;
        var direction = Input.GetVector(MoveLeft, MoveRight, MoveUp, MoveDown);
        var noDirectionalInputs = direction.IsZeroApprox();

        if (noDirectionalInputs)
        {
            NavigationCount = 0;
            NavigationEnabled = true;
            DelayTimer.Stop();
        }

        if (!FocusIsVisible())
        {
            ButtonDown = false;
            ButtonUp = false;
            return;
        }

        if (NavigationEnabled && !noDirectionalInputs && !ButtonDown && !ButtonUp)
        {
            if (Mathf.Abs(direction.Y) > Mathf.Abs(direction.X))
            {
                NavigateToNeighbor(direction.Y > 0 ? Side.Bottom : Side.Top);
                return;
            }
            else
            {
                NavigateToNeighbor(direction.X > 0 ? Side.Right : Side.Left);
                return;
            }
        }

        if (ButtonDown)
        {
            ButtonDown = false;
            GetFocusNavigation()?.OnButtonDown();
        }

        if (ButtonUp)
        {
            ButtonUp = false;
            GetFocusNavigation()?.OnButtonUp();
        }
    }

    private void NavigateToNeighbor(Side side)
    {
        var neighborPath = Focus.GetFocusNeighbor(side);

        if (neighborPath != null && !neighborPath.IsEmpty)
        {
            var neighbor = Focus.GetNode<Control>(neighborPath);
            GrabFocus(neighbor);

            NavigationEnabled = false;
            var delay = NavigationCount++ > 0 ? RepeatDelay : FirstRepeatDelay;
            DelayTimer.Start(delay);
        }
    }

    public void GrabFocus(Control focus)
    {
        focus = IsInstanceValid(focus) ? focus : null;
        var lastFocus = IsInstanceValid(Focus) ? Focus : null;

        if (lastFocus != focus)
        {
            ReleaseFocus();
            Focus = focus;
            GetFocusNavigation()?.OnFocusEntered();
        }
    }

    public void ReleaseFocus()
    {
        GetFocusNavigation()?.OnFocusExited();
        Focus = null;
    }

    private IPlayerNavigation GetFocusNavigation()
    {
        if (!IsInstanceValid(Focus))
            return null;

        var count = Focus.GetChildCount();

        for (int i = 0; i < count; i++)
        {
            if (Focus.GetChild(i) is IPlayerNavigation navigation)
                return navigation;
        }

        return null;
    }

    public override void _UnhandledInput(InputEvent input)
    {
        base._UnhandledInput(input);

        if (FocusIsVisible())
        {
            if (Input.IsActionJustPressedByEvent(Submit, input))
            {
                ButtonDown = true;
                Viewport.SetInputAsHandled();
                return;
            }

            if (Input.IsActionJustReleasedByEvent(Submit, input))
            {
                ButtonUp = true;
                Viewport.SetInputAsHandled();
                return;
            }
        }
    }

    private bool FocusIsVisible()
    {
        return IsInstanceValid(Focus) && Focus.IsVisibleInTree();
    }
}
```

The following interface defines the button event methods called by `PlayerNavigation`:

```csharp
public interface IPlayerNavigation
{
    public void OnFocusEntered();
    public void OnFocusExited();
    public void OnButtonDown();
    public void OnButtonUp();
}
```

The following `PlayerButtonBase` node should be added as a child of any button requiring independent player navigation. This class fires the built-in events on the button when appropriate and also performs some theme style swapping to make the button appear the same as if it were being used by the built-in navigation system.

```csharp
using Godot;

[GlobalClass]
public partial class PlayerButtonBase : Node, IPlayerNavigation
{
    private static StringName PanelProperty { get; } = "panel";
    private static StringName NormalProperty { get; } = "normal";
    private static StringName NormalMirroredProperty { get; } = "normal_mirrored";
    private static StringName PressedProperty { get; } = "pressed";
    private static StringName PressedMirroredProperty { get; } = "pressed_mirrored";
    private static StringName FocusProperty { get; } = "focus";

    [Export] public BaseButton Button { get; set; }
    [Export] public Control FocusIndicator { get; set; }
    public bool IsFocused { get; private set; }
    public bool IsPressed { get; private set; }

    public override void _Ready()
    {
        base._Ready();
        Button ??= GetParent<BaseButton>();
        Button.FocusMode = Control.FocusModeEnum.None;
        FocusIndicator ??= CreateFocusIndicator();
        UpdateStyles();
    }

    private Panel CreateFocusIndicator()
    {
        var panel = new Panel
        {
            MouseFilter = Control.MouseFilterEnum.Ignore,
        };

        panel.SetAnchorsPreset(Control.LayoutPreset.FullRect);
        var style = Button.GetThemeStylebox(FocusProperty);
        panel.AddThemeStyleboxOverride(PanelProperty, style);
        CallDeferred(Node.MethodName.AddSibling, panel);
        return panel;
    }

    public void UpdateStyles()
    {
        if (IsInstanceValid(FocusIndicator))
            FocusIndicator.Visible = IsFocused;

        Button.RemoveThemeStyleboxOverride(NormalProperty);
        Button.RemoveThemeStyleboxOverride(NormalMirroredProperty);

        if (IsFocused && IsPressed)
        {
            var pressedStyle = Button.GetThemeStylebox(PressedProperty);
            var pressedMirroredStyle = Button.GetThemeStylebox(PressedMirroredProperty);
            Button.AddThemeStyleboxOverride(NormalProperty, pressedStyle);
            Button.AddThemeStyleboxOverride(NormalMirroredProperty, pressedMirroredStyle);
        }
    }

    public void OnFocusEntered()
    {
        IsFocused = true;
        IsPressed = false;
        UpdateStyles();

        if (Button.IsVisibleInTree())
            Button.EmitSignal(Control.SignalName.FocusEntered);
    }

    public void OnFocusExited()
    {
        IsFocused = false;
        IsPressed = false;
        UpdateStyles();

        if (Button.IsVisibleInTree())
            Button.EmitSignal(Control.SignalName.FocusExited);
    }

    public void OnButtonDown()
    {
        IsPressed = true;
        UpdateStyles();

        if (!Button.Disabled && Button.IsVisibleInTree())
        {
            Button.EmitSignal(BaseButton.SignalName.ButtonDown);

            if (Button.ActionMode == BaseButton.ActionModeEnum.Press)
                Button.EmitSignal(BaseButton.SignalName.Pressed);
        }
    }

    public void OnButtonUp()
    {
        var wasPressed = IsPressed;
        IsPressed = false;
        UpdateStyles();

        if (wasPressed && !Button.Disabled && Button.IsVisibleInTree())
        {
            Button.EmitSignal(BaseButton.SignalName.ButtonUp);

            if (Button.ActionMode == BaseButton.ActionModeEnum.Release)
                Button.EmitSignal(BaseButton.SignalName.Pressed);
        }
    }
}
```