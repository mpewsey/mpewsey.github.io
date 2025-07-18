---
layout: post
title: How to Make Auto-Resizing Text Panels in Godot
tags: game-design godot
mathjax: false
published: false
image: https://github.com/user-attachments/assets/2a747245-f7a1-4f95-b741-d7be108125bc
---

In Godot, have you ever wanted to make a panel that automatically resizes to its text contents, such as in a description panel? While you may initially think a `Panel` node might work for this application, that particular node lacks the necessary resizing functionality. Rather, somewhat obscurely, you can instead use a `Label` node, then override its normal theme style to make it look like a panel. 

<!--excerpt-->

![Description Panel](https://github.com/user-attachments/assets/2a747245-f7a1-4f95-b741-d7be108125bc)

<span class='figure-title'>Figure 1: Description Panel Example</span>

## Steps

To create an auto-resizing text panel like the one shown above, perform the following:

1. Create a `Label` node. This node will serve as the description text of the panel. (A `RichTextLabel` can also be used, but will have some different options from what is shown in this guide.)
2. Set the text to word wrap, and set the min width of the `Label` so the text will be constrained in that direction.
   
    ![First Steps](https://github.com/user-attachments/assets/6cc3c3c2-cb91-4515-aca4-5737ede0cad2)

    <span class='figure-title'>Figure 2: The Panel So Far</span>

4. In `Theme Overrides > Styles > Normal`, create a new `StyleBoxFlat`. This style box will make the label appear as though it has a panel behind it. If you want to mimic the default Godot panel, set the background color to `RGBA(26, 26, 26, 153)`, the corner radiuses to 3px, and corner detail to 5. For now, set the content margins to 4px all around, to give some padding between the text and its border.

    ![Theme Style Panel](https://github.com/user-attachments/assets/b3e81481-e48c-4350-92d3-03c09a8390e6)

    <span class='figure-title'>Figure 3: The Description Label Style Box</span>
    
    ![Theme Style Panel](https://github.com/user-attachments/assets/56558ab3-4ae5-4930-a1c4-2c306d741d4b)

    <span class='figure-title'>Figure 4: The Panel So Far</span>

6. Create a new `Label` as a child of the previous. This node will serve as the title text.
7. Set its resizing anchors to top wide, so that it fits to the top of the panel.
8. In `Theme Overrides > Styles > Normal`, create a new `StyleBoxFlat`, the same as Step 3.
9. Measure the height of the title panel. Go back to the description label, and add this amount to your top content margins, so that the text is no longer behind the title. In my case, the title panel measured 30px high, so I used 34px (30px title + 4px top padding) for the top content margin.

Here's what everything should look like in the end:

![Final Example](https://github.com/user-attachments/assets/f52b54d6-ed1d-452c-b7c0-966a475d3ab1)

<span class='figure-title'>Figure 5: The Final Results</span>

## Extra Remarks

If you want to add buttons or other elements to the panel, such as that shown below, simply go back and update the content margins of the description label to accommodate them.

![Other Example](https://github.com/user-attachments/assets/2c613ea7-bfa5-4608-beb5-d9daae9c28fd)

<span class='figure-title'>Figure 6: Example Confirmation Panel</span>

In this guide, we used style overrides to apply the panel styles to the labels. However, you may wish to consider applying this formatting via a theme style instead if you plan to use the same panels in multiple places throughout your project.
