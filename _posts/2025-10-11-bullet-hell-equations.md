---
layout: post
title: 2D Bullet Hell Equations
tags: game-design
mathjax: true
image: https://github.com/user-attachments/assets/7b0d5263-10c6-4bd1-b01b-bfbb0e321939
---

This post provides a listing of waveform equations that may be useful for applying unusual trajectories to bullets in bullet hell games.

<!--excerpt-->

The coordinate system used for the bullet movement equations assumes that the bullet's primary travel direction is to the right (+x), with any wave transformations being applied perpendicular in the y-axis.

The following variables are used throughout the equations:

* $ (x, y)= $ The x and y local positions of the bullet.
* $ (x_0, y_0)= $ The initial x and y local positions of the bullet.
* $ (v_x, v_y)= $ The x and y local velocities of the bullet.
* $ t= $ The total time since the bullet was spawned.
* $ S= $ The speed of the bullet in its primary movement direction. (The speed assuming linear movement.)
* $ A= $ Amplitude of waveform. (How tall the waves are.)
* $ F= $ Frequency of the waveform. (How many waves are in an interval.)

### Linear Movement

![Linear Movement](https://github.com/user-attachments/assets/648fc49e-1245-4d6a-93e7-93d056929cce)

Position:

* $ x(t) = S t + x_0 $
* $ y(t) = y_0 $

Velocity:

* $ v_x(t) = S $
* $ v_y(t) = 0 $

### Sinusoidal Movement

![Sinusoidal Movement](https://github.com/user-attachments/assets/7b0d5263-10c6-4bd1-b01b-bfbb0e321939)

Position:

* $ x(t) = S t + x_0 $
* $ y(t) = A \sin(F t) + y_0 $

Velocity:

* $ v_x(t) = S $
* $ v_y(t) = A F \cos(F t) $

### Sawtooth Movement

![Sawtooth Movement](https://github.com/user-attachments/assets/f7e9fefa-3445-4943-98ad-5bfb49af58a8)

Velocity:

* $ v_x(t) = S $
* $ v_y(t) = \text{sign}(A F \cos(F t)) \cdot 2 A F / \pi $

### Circular Movement

Velocity:

* $ v_x(t) = -A F \sin(F t) + S $
* $ v_y(t) = A F \cos(F t) $

### Applying Rotation

To rotate the coordinates provides in the equations to a desired direction, let's define the primary bullet travel direction to be $\theta$ from the global x-axis. The unit vector in that direction is:

$$ \vec{T} = (\cos\theta, \sin\theta) $$

Likewise, the normal vector is:

$$ \vec{N} = (-\sin\theta, \cos\theta) $$

With these, we can rotate any of the equations presented to the desired bullet direction:

$$ \vec{X} = x \vec{T} + y \vec{N} $$

$$ \vec{V} = v_x \vec{T} + v_y \vec{N} $$

### Applying the Equations

A few approaches for applying the equations to a bullet are as follows:

#### Approach 1: Setting the Position

```
global_position = Vector2(x, y)
```

Simple and obvious, but does not work well with most engine physics objects, since you are warping the object to the position.

#### Approach 2: Setting the Velocity (No Derivative)

```
velocity = (Vector2(x, y) - global_position) / deltaTime
```

Use the last position to approximate the velocity. Works well with engine physics objects. You can't really apply any other forces / velocity contributions.

#### Approach 3: Setting the Velocity (Known Derivative)

```
velocity = Vector2(vx, vy)
```

A pretty obvious approach, but you need to know the velocity function. Works well with engine physics objects that have no external forces.

#### Approach 4: Incrementing the Position (Known Derivative)

```
global_position += Vector2(vx, vy) * deltaTime + other_contributions
```

This has the benefit of allowing you to superimpose waveforms and account for external forces. Does not work well with engine physics objects. Could be susceptible to gradual precision loss for long lived objects.
