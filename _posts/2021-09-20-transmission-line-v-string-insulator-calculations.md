---
layout: post
title: Transmission Line V-String Insulator Calculations
tags: engineering transmission-line
published: true
---

A V-string insulator consist of two insulator strings or rods that are attached, at their tops, to two separate points of a structure and, at their bottoms, provide a shared wire attachment. The elevation view looks like a letter "V", hence the name. The main advantage of this insulator type is that the configuration of the V-string, unlike an I-string, restricts transverse (into the structure) swings/deflections of the wire, allowing horizontal framing within the structure to potentially be more compact and eliminating the insulator swing component from wire blowout calculations, potentially reducing the required right-of-way width. In addition, the V-strings are still allowed to swing longitudinally (into the adjacent spans), allowing the same tension imbalance and broken wire adjustments that an I-string insulator affords.

While insulator swing calculations are generally not necessary for V-String insulators unless the longitudinal swing under a loading scenario is of interest, an allowable load angle calculations is generally performed to ensure that no leg of the insulator will go into compression, since the insulator bells and rods are generally not designed to support significant compressive loads, if any. This post will develop a variety of equations necessary for calculating the geometry of a V-string insulator, as well as evaluating the allowable load angles and internal loads for strength calculations. The geometry of interest for the subsequent development is shown in Figure 1, which displays two insulators of length $L_1$ and $L_2$ attached to two structure attachments at separations $\Delta x$ and $\Delta z$. In addition, load $P$ is some wire resultant load applied at angle $\phi$ from vertical.

![v-string](https://user-images.githubusercontent.com/23442063/133932936-39502247-37f4-4c32-90f8-6eec619d0ff9.png)

**Figure 1: V-String Insulator Geometry**

<!--excerpt-->

## Geometry

The slope angle due to the change in elevation between attachments is:

$$ \beta = \text{atan}\bigg(\frac{\Delta z}{\Delta x}\bigg) $$

The internal angles of the triangle can be found using the <a href="https://en.wikipedia.org/wiki/Law_of_cosines">law of cosines</a>:

$$ \theta_1 = \text{acos} \bigg(\frac{L_2^2 + \Delta x^2 + \Delta z^2 - L_1^2}{2 L_2 \sqrt{\Delta x^2 + \Delta z^2}}\bigg) $$

$$ \theta_3 = \text{acos} \bigg(\frac{L_1^2 + L_2^2 -\Delta x^2 - \Delta z^2}{2 L_1 L_2}\bigg) $$

$$ \theta_2 = 180^{\text{o}} - \theta_1 - \theta_3 $$

From simple geometry, the location of the wire attachment relative to the left support is:

$$ x = L_1\cos(\theta_2 - \beta) $$

$$ y = -L_1\sin(\theta_2 - \beta) $$

## Allowable Load Angle

If the load, $P$, is applied in-line with leg $L_1$, the tension in that leg of the insulator is exactly $P$, while the tension in leg $L_2$ is zero. Applying the load at an angle towards the right would begin to apply compression in leg $L_2$. Hence, the in-line angle is a critical point for this transition. Likewise, if the load is applied in line with leg $L_2$, we observe the opposite: an angle applied towards the left would begin to place $L_1$ in compression. Based on this, from geometry alone we can calculate the allowable line angles to prevent any leg of the insulator from going into compression as:

$$ \phi_{min} = \theta_1 + \beta - 90^\text{o} $$

$$ \phi_{max} = \phi_{min} + \theta_3 $$

For comparison against the above minimum and maximum load angles, the applied load angle is simply the arctangent of the ratio of the horizontal and vertical components of the applied load, $P$:

$$ \phi = \text{atan} \bigg(\frac{P_h}{P_v}\bigg)$$

If suspension clamp or other wire attachment hardware clearances are a concern, this angle is likely a good estimate of the hardware swing as well, since the length of the hardware and applied hardware loads are likely small.

## Insulator Tensions

Summing the horizontal and vertical forces at the wire attachment joint, the tensions in each leg of the V-string insulator can be solved by taking the inverse of the resulting matrix:

$$
\begin{bmatrix}
T_1 \\
T_2 \\
\end{bmatrix} =
\frac{1}{\sin\alpha_1 \cos\alpha_2 - \cos\alpha_1 \sin\alpha_2}
\begin{bmatrix}
\sin\alpha_2 &  -\cos\alpha_2 \\
-\sin\alpha_1 & \cos\alpha_1
\end{bmatrix}
\begin{bmatrix}
P_h \\
P_v
\end{bmatrix}
$$

where

* $ \alpha_1 = \phi_1 + \phi_3 + \beta $
* $ \alpha_2 = \phi_1 + \beta $
* $ P_h, P_v $ = the horizontal and vertical load vector

## Support Reactions

The force vectors at the insulator attachments can be acquired by decomposing the tensions in each respective insulator leg. The force vectors at the left insulator attachment are:

$$
\begin{bmatrix}
H_1 \\
V_1
\end{bmatrix} = T_1
\begin{bmatrix}
\cos\alpha_1 \\
\sin\alpha_1
\end{bmatrix}
$$

while the force vectors at the right insulator attachment are:

$$
\begin{bmatrix}
H_2 \\
V_2
\end{bmatrix} = T_2
\begin{bmatrix}
\cos\alpha_2 \\
\sin\alpha_2
\end{bmatrix}
$$

## Attachments

* [V-String Insulator Calculation Spreadsheet](https://docs.google.com/spreadsheets/d/1Z0AhDh9kc47aPcn963ZkrzOnlAIJjOKBbfTyiBObxiI/edit?usp=sharing)
