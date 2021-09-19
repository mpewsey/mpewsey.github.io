---
layout: post
title: Transmission Line I-String Insulator Swing Calculations
categories: engineering transmission-line
published: true
---

I-string insulators are insulators that attach to a structure at one end, via a pinned connection, and support a conductor at the other end, via a suspension clamp or other hardware. They typically consist of a series of porcelain or glass insulator bells (Figure 2) or a polymer rod (Figure 1). Their attachment to the structure via a pinned connection allows the insulator to freely swing with imbalances in wire tension loads. This flexibility helps reduce the lateral loads on transmission line structures due to temporary tension imbalances. In the event of a broken wire on a tangent or angle structure, the insulator is also capable of swinging until a new equilibrium is obtained, dissipating energy and reducing the tension that the structure must support under a broken wire scenario.

While the ability of an I-string insulator to swing is beneficial due to its flexibility, excessive flexibility can result in the insulator swinging too far toward the structure or other nearby objects, developing electrical clearance issues. To verify that the swing provides adequate clearance to the structure, designers typically calculate the allowable minimum and maximum swing angles that an insulator of a specified length must reside between in order to maintain clearances. The expected insulator swings due to loads (wire tensions, wind loads, etc.) are then calculated and compared versus these limiting angles to ensure that the design is in conformance.

This post will present derivations of the equations necessary to perform I-string insulator swing checks, as well as to determine the deflected wire attachment location in space for use in sag-tension or other calculations.

![i-string-rod](https://user-images.githubusercontent.com/23442063/133910315-f4e2e29b-1cb1-47fd-aab3-3605ab103cc4.jpg)

**Figure 1: Double I-String Insulator with Rods**

![i-string-bell](https://user-images.githubusercontent.com/23442063/133910316-dc962bee-29ea-4465-a756-63ee941425c8.jpg)

**Figure 2: I-String Insulator with Bells**

<!--excerpt-->

### 2D Insulator Swing
Figure 3 shows the geometry of a swinging insulator with the following applied loads:

* $ I_h = $ horizontal insulator wind load
* $ I_v = $ vertical insulator weight and/or ice load
* $ W_h = $ horizontal wire wind and/or tension load
* $ W_v = $ vertical wire weight and/or ice load

![insl-swing](https://user-images.githubusercontent.com/23442063/133910317-7d82dbd0-5b06-4b24-998b-ba0ae7789c3a.png)

**Figure 3: 2D I-String Insulator Geometry**

To find the swing angle resulting from the above loads, we can sum moments about the
structure attachment:

$$ \sum M: W_h L \cos\theta - W_v L \sin\theta + I_h K L \cos\theta - I_v K L \sin\theta = 0 $$

Dividing by $\cos\theta$ and solving for the swing angle, we get:

$$ \theta = \text{atan} \bigg(\frac{W_h + I_h K}{W_v + I_v K}\bigg) $$

Typically, $ K $ is taken as $ 1/2 $ the insulator length. However, in situations
where the center of pressure or center of weight differ from this assumption,
this value could theoretically be modified for each.

While the above equation specifically accounts for the most common insulator
calculations, it may also be of interest to note a more generic equation
relating the length-normalized moments, $\mu$, to the swing angle:

$$ \theta = \text{atan} \bigg(\frac{\mu_h}{\mu_v}\bigg) $$

where

* $ \mu_h = \frac{\sum M_h}{L} = \sum(\text{Insulator Length Fraction} \times \text{Horizontal Load}) = $ length-normalized horizontal moment
* $ \mu_v = \frac{\sum M_v}{L} = \sum(\text{Insulator Length Fraction} \times \text{Vertical Load}) = $ length-normalized vertical moment


### 3D Insulator Swing

While insulator swing is often evaluated for tangent structures, which tend
to have the majority of their swing occur within a single plane, in the case
of imbalanced span loads or a dead-end insulator string, the evaluation of
the wire attachment position in 3D may be of interest.

To evaluate these deflections, we can simply decompose the 2D insulator swing equations (Equation 3) into their deflection components in the x-y, y-z, and x-z planes:

$$
\begin{cases}
\frac{\mu_x}{\mu_y} = \frac{\Delta x}{\Delta y} \\
\frac{\mu_y}{\mu_z} = \frac{\Delta y}{\Delta z} \\
\frac{\mu_x}{\mu_z} = \frac{\Delta x}{\Delta z} \\
\end{cases}
$$

We also know that the total length of the insulator is related to the deflections by:

$$ L^2 = \Delta x^2 + \Delta y^2 + \Delta z^2 $$

Combining this equation with the deflections developed previously, we get the wire attachment position vector, relative to the insulator attachment:

$$ \vec{\Delta} = \bigg(\frac{\text{<} \mu_x, \mu_y, \mu_z \text{>}}{\sqrt{\mu_x^2 + \mu_y^2 + \mu_z^2}} \bigg) L = \vec{\mu_u} L $$

Here, $ \vec{\mu_u} $ is the unit vector of the length-normalized moment.


### Segmented Insulator Analysis

For insulators consisting of multiple insulator units, evaluating the insulator
swing on a unit by unit basis may be more accurate than modeling the entire insulator
as a single rigid element. In situations where the applied wire loads are much
greater than those of the applied insulator loads, the difference between a rigid
and segmented analysis will be negligible, as shown in Figure 4. However, when the applied
wire loads approach that of the insulator loads, divergent scenarios such as
those pictured in Figures 5 and 6 can occur.

![insl-comparison-3](https://user-images.githubusercontent.com/23442063/133910318-8e0749c2-9741-46a6-8b01-b3079fcf1961.png)

**Figure 4: FE Insulator Comparison (Wire Loads $>>$ Insulator Loads)**

![insl-comparison-1](https://user-images.githubusercontent.com/23442063/133910322-c83e1f9b-a316-4a51-923e-77afbabb3666.png)

**Figure 5: FE Insulator Comparison (Wire Loads $ \propto $ Insulator Loads)**

![insl-comparison-2](https://user-images.githubusercontent.com/23442063/133910323-ae3ba5cf-0e07-4e25-86d7-f67601f9c55c.png)

**Figure 6: FE Insulator Comparison (Uplift Wire Loads $ \propto $ Insulator Loads)**

The analysis of a segmented insulator is relatively simple: using the insulator
deflection equations developed previously, simply calculate the deflections on
a unit by unit basis and sum them together to get the total deflection at the
wire attachment. For each unit, the calculation should consist of applying the
wire loads on the end, plus that of the loads of the insulator units located
below it. The load of the insulator unit itself can be applied at its midpoint
similar to what has been shown for the rigid insulator previously.


### Allowable Swing Calculations

The allowable swing angles are typically defined as the minimum (angle to the left) and maximum (angle to the right) values in which an insulator of a given length still maintains structural clearances. Rather than the exact swing angles, which were developed previously, these values can be calculated generally for a family of structures.

One way of determining the allowable swing angles of a structure would be to draw the structure to scale in an CAD program, then to rotate the insulator and wire clearance circle until it touches some part of the structure. This method is excellent for visualization but comes with the downfall that CAD programs typically do not have the snaps necessary to acquire an exact swing angle. Rather, the eye-balled intersection could result in swing angles around a degree of two of the actual value. In addition, this method takes time to draw the required section of the structure to scale. For calculating the swings for a large number of structures, it may, therefore, be more beneficial to use an analytic approach that applies the value for various common directional constraints.

The most common constraints posed to an I-string insulator are to poles or tower bodies (horizontal constraints) and to their supporting tower crossarms or davit arms (vertical constraints). The following subsections will present solutions for these two common constraints. The controlling value of the the horizontal and vertical constraints should be used for swing check comparisons.

#### Horizontally Constrained Insulator
The geometry for a simple horizontally constrained insulator is shown in Figure 5. Here, $L$ is the length of the insulator and $C$ is the horizontal clearance requirement to the object of interest. Depending on the material composing the horizontally constraining feature, this clearance value may vary. For example, clearances between wood and steel components generally differ. The value of $H$ may also be reduced to account for any additional width that one might wish to account for in electrical clearance calculations, such as the width of the insulator suspension clamp or bundled conductor hardware.

![horz-swing](https://user-images.githubusercontent.com/23442063/133910329-5d5ff032-84ed-42e6-b54a-7c23b0f4516f.png)

**Figure 5: Horizontally Constrained Swing Geometry**

To calculate the allowable swing angle, $\theta$, we will rotate the insulator to the left by angle $\beta$ to simplify the geometry, then write the equation along a horizontal line:

$$ H\cos\beta - V\sin\beta = L\sin(\theta + \beta) + C $$

Solving this equation for the allowable swing angle, $ \theta $, yields:

$$
\theta =
\begin{cases}
\text{asin}\big( \frac{H\cos\beta - V\sin\beta - C}{L} \big) - \beta, & H\cos\beta - V\sin\beta - C \leq L \\
\text{Unconstrained}, & \text{Otherwise}
\end{cases}
$$

The above scenario represents the allowable swing angle to a constraint to the right. For a constraint to the left, simply apply a negative sign to the value acquired to the right. For some directions, a horizontal constraint may not exist. In such a case, a suitable value to restrict uplift should be selected.

#### Vertically Constrained Insulator
The geometry for a simple vertically constrained insulator is shown in Figure 6. Here, $C$ is the vertical clearance requirement to the object of interest.

![vert-swing](https://user-images.githubusercontent.com/23442063/133910327-715eac4a-ffd4-42cb-8a4d-4fd918a7950e.png)

**Figure 6: Vertically Constrained Swing Geometry**

To calculate the allowable swing angle, $\theta$, we will rotate the insulator of the left by angle $\beta$ to simplify the geometry, as was done with the horizontally constrained case. However, instead, we will write the equation along the resulting vertical line.

$$ C - L\cos(\theta + \beta) = V\cos\beta $$

Solving this equation for the allowable swing angle, $ \theta $, yields:

$$
\theta =
\begin{cases}
\text{acos}\big( \frac{C - V\cos\beta}{L} \big) - \beta, & C - V\cos\beta \leq L \\
\text{Unconstrained}, & \text{Otherwise}
\end{cases}
$$

Similar to the horizontally constrained case, for swing angles to the left, simply calculate the angle to the right and apply a negative sign.

### Checking Constraints
Using the previously presented equations, the minimum and maximum swing angles can be calculated based on the structure constraints as $ \theta_{min} = \max(\theta_{\text{horz,left}}, \theta_{\text{vert,left}}) $ and $ \theta_{max} = \min(\theta_{\text{horz,right}}, \theta_{\text{vert,right}}) $. If the actual swing angle due to loading, $\theta$, satisfies the inequality $ \theta_{min} \leq \theta \leq \theta_{max} $, then we can conclude that adequate electrical clearances are maintained for the associated loading condition.

### Attachments

* [3D I-String Insulator Calculation Spreadsheet](https://docs.google.com/spreadsheets/d/1AZXMwEpOvPdXZ72lcm0LFi-GTuZdMfjuQqDEj00kZeY/edit?usp=sharing)
* [Allowable I-String Insulator Swing Spreadsheet](https://docs.google.com/spreadsheets/d/1WT5FlqcNCQpfO0sFViIeza3D37eUp0kb27IZgHnAe84/edit?usp=sharing)
