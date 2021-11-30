---
layout: post
title: "Steel Member Tensile Strength: Equivalent Number of Bolts for Net Area Calculations"
tags: engineering
published: true
---

Structural software, such as PLS-Tower, sometimes provides a field for the number of bolts to remove from the gross area of a steel member cross section for tensile strength calculations. For scenarios when bolts at a connection are not staggered, specifying this value is rather trivial. However, for scenarios when bolts are staggered, the $ s^2 / 4g $ factor specified by §D3.2 of \[1] must be incorporated into the input value.

This post presents the equations for calculating the equivalent number of bolts incorporating the $ s^2 / 4g $ value. In addition, to reduce the number of failure paths that must be considered for complex bolt patterns, an equation for the critical pitch spacing, beyond which a staggered tensile failure path will not occur, is presented.

![Staggered Tensile Failure Path](https://user-images.githubusercontent.com/23442063/141645686-b517fe60-62ef-4c7e-b176-08eeab2c3498.png)

**Figure 1: Staggered Tensile Failure Path**
<!--excerpt-->

### Equivalent Number of Bolts

To derive an expression for the equivalent number of bolt holes for a staggered tensile failure path, first note that the net area for a staggered failure path of constant thickness is:

$$ A_n = A_g - A_b + t \sum \frac{s^2}{4g} $$

where

* $ A_g = $ the gross area of the member cross section.
* $ A_b = $ the area of the bolt holes along the failure path.
* $ t = $ the thickness of the cross section.
* $ s = $ the longitudinal center-to-center bolt spacing (pitch).
* $ g = $ the transverse center-to-center bolt spacing (gage).

Breaking this expression into its parts, we can acquire an expression with the equivalent number of bolts to be removed from the cross section, $n_e$:

$$\begin{eqnarray}
A_n &=& A_g - t (d n_b - \sum \frac{s^2}{4g}) \nonumber \\
&=& A_g - t d (n_b - n_s) \nonumber \\
&=& A_g - t d n_e \nonumber \\
\end{eqnarray}$$

where

* $ d = $ the diameter of the bolt holes.
* $ n_b = $ the number of bolt holes along the failure path.
* $ n_s = $ the number of bolt holes equivalent to the staggered gage factor.
* $ n_e = $ the total equivalent number of bolt holes to subtract from the gross area.

From the above equations, the number of bolts equal to the bolt stagger is:

$$ n_s = \frac{\sum \frac{s^2}{4g}}{d} $$

Also, the resulting equivalent number of bolts is:

$$ n_e = n_b - n_s $$

Note that this value need not be a whole number.

### Critical Pitch Spacing

For complex bolt patterns, it may not be immediately obvious which tensile failure path controls. Often times, however, the gage spacing, $g$, for a member is constant at a member connection, whereas the pitch spacing, $s$, and number of holes along the failure paths vary. Rather than calculating the net areas for each failure path, it may be easier to calculate the pitch spacing beyond which a staggered failure path will not control. For the assumption that the gage spacing is constant at the connection, this critical pitch spacing, $ s_{crit} $, occurs when the $ s^2 / 4g $ value is equal to the diameter of one bolt hole:

$$ d = \frac{s_{crit}^2}{4g} $$

In other words,

$$ s_{crit} = 2 \sqrt{g d} $$

For any $ s > s_{crit} $, the failure path will not stagger, allowing it to be omitted from consideration.

### Attachments

* [Equivalent Number of Bolts for Tension Member with Staggered Bolt Chain Spreadsheet](https://docs.google.com/spreadsheets/d/1d7dc8T0IlulbBMIf2ZLVICGpPFjc1OTMy9bnig6wXXo/edit?usp=sharing)

### References

[1] AISC Steel Construction Manual, 13th Ed, p. 16.1-27.
