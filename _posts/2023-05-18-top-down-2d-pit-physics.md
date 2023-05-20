---
layout: post
title: Physics for Pits in Top-Down 2D Games
tags: game-design unity
mathjax: true
image: https://github.com/mpewsey/mpewsey.github.io/assets/23442063/9e10241e-8996-4f46-be62-7838b2121c41
---

Lately, I've been interested in making a game with 2D top-down platforming elements, with the ability to jump similar to _The Legend of Zelda: Link's Awakening_. To do this, one of the problems I've needed to solve was how to model the physics of holes or pits. This post explores some realizations I made while attempting to tackle this problem, along with the resulting physics.

<!--excerpt-->

I find that the pit physics can be broken into two problems:

1. How do you model the magnitude of the pit attractive force?
2. How do you determine the direction of the pit, relative to the player?

So, with that in mind, let's discuss the first part...

## The Pit Attractive Force

In my experimenting, I realized that the pit attractive force is the aspect of the pit physics that will likely stand out most within player's minds. It determines the overall pit experience.

If your aim is for your pits to simply act as pits, i.e. an event that progresses as `Step in pit > Fall`, then you can simply apply a constant force magnitude and call it a day. I find the experience this offers to be sudden and abrupt. As soon as you touch a pit, you are pulled in with no chance of altering your fate, and depending on how obvious the location of your player's pit trigger is, you might not even realize you were about to fall in, until you are. I would say that this approach works best for the games that are either very forgiving (a low pit force) or incredibly merciless (a moderate to large pit force). Personally, this was not the experience I was looking for.

While reflecting further on the pits within _Link's Awakening_, I realized the pit falling event in that game has more depth. Rather than a `Step in pit > Fall` event, it progresses more like `Step in pit > Struggle > Fall`. The pit attractive force, therefore, is likely stepped up to give a distinct indicator between the different stages, such as shown in Figure 1.

![Pit Force-Time Curve](https://github.com/mpewsey/mpewsey.github.io/assets/23442063/c790e9a4-87bf-47e8-9663-4adadd1daa8a)

<span class='figure-title'>Figure 1: Pit Force-Time Curve</span>

Here, the force magnitude is dependent on the player's contact time with the pit.

The first part of the curve - coyote time, with a force magnitude of zero - provides players a grace period before the pit force activates, potentially reducing the possibility that players will feel resentment over a perceived missed input. I'm not sure that _Link's Awakening_ actually considers coyote time. However, while play testing, I noticed that if you simply begin applying an attractive force while the player is still able to jump, the player could exploit the pit to boost their movement speed temporarily. I didn't want that, hence why I chose to include it.

The second part of the curve, slip time, applies a magnitude on par with the walk force of the player, providing a period in which the player can struggle before being engulfed in darkness. 

And finally, the third stage provides the finishing blow, essentially timing out the player's ability to escape and forcing them into the pit with an adequately large, irresistible magnitude.

Ultimately, I chose to use this stepped force-time curve. In the Unity game engine, I find that using an Animation Curve for the force-time curve provides the flexibility to model almost any scenario.

## Direction of the Pit Attractive Force

With the force magnitude resolved, we still need to determine the direction for the pit force. There are perhaps a number of ways to approach this, but one way is to use the difference between the player and pit intersecting shape and the player's center.

Depending on the tools available, determining the exact player and pit intersecting shape can actually be somewhat difficult. However, for the purposes of game physics, an estimate is suitable.

![Pit Direction Diagram](https://github.com/mpewsey/mpewsey.github.io/assets/23442063/9e10241e-8996-4f46-be62-7838b2121c41)

<span class='figure-title'>Figure 2: Pit Direction Diagram</span>

To get an approximation, I chose to loop over grid points (shown in blue and red in Figure 2) within the player's pit trigger, then test whether they overlap with a pit area via the Unity 2D physics engine. With those intersecting points (shown in red) known, the normalized direction from the center of the player toward the estimated intersecting shape centroid could be calculated as:

$$ \vec{x_a} = \bigg\lVert \sum_{i = 0}^{n} (\vec{x_i} - \vec{x_0}) \bigg\rVert $$

where

* $ x_i $ = the i-th point contained inside both the player's pit trigger and a pit area.
* $ x_0 $ = the center point of the player.

The bare number of grid points to test would be the corners of the player's pit trigger. However, in my play testing, so few points can sometimes allow you to skirt across diagonal pits. Therefore, using at least a 4 by 4 grid, as shown in the figure, is recommended.

Unfortunately, there are scenarios where symmetrical geometries create equilibrium points where the resulting direction will never pull the player into a pit, despite the player being in contact. For example, in Figure 3, so long as the player center is located on the axis $ x' $, the pit attractive forces cancel out in the perpendicular direction $ y' $. As a result, the player can simply walk with their center along $ x' $ to skirt past the pit without ever falling in.

![Pit Equilibrium Example](https://github.com/mpewsey/mpewsey.github.io/assets/23442063/12852170-bb9a-4359-bb17-b9e4811793ce)

<span class='figure-title'>Figure 3: Pit Equilibrium Example</span>

One way to resolve this issue is to incorporate random numbers into the direction calculation, as seen in the below revised equation. This removes almost any possibility of symmetry occurring, and in the case of our example, will essentially guarantee that some magnitude of attractive force is applied in the $ y' $ direction.

$$ \vec{x_a} = \bigg\lVert \sum_{i = 0}^{n} (\vec{x_i} - \vec{x_0})(R \in [1, 1 + r]) \bigg\rVert $$

where

* $ r $ = the random number variance, a positive number based on how much you wish to skew the symmetry.

The random variance also provides a nice side effect of applying a wobble to the player position when walking against the pit force. This gives the impression that the player character is putting up a struggle to resist falling in the pit.

## Conclusion

With the independent parts of pit attraction force determined, the pit force can simply be calculated as:

$$ \vec{F} = F_m \vec{x_a} $$

where

* $ F_m $ = the force magnitude.
* $ x_a $ = the normalized direction toward the pit.

On the surface, 2D pit physics seem relatively simplistic. However, after delving deeper into this problem, I have a newfound respect for the experiences that game developers, such as those for _Link's Awakening_, have managed to craft in the past. 