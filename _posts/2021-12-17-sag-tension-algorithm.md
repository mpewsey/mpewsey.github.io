---
layout: post
title: Wire Sag-Tension Algorithm
tags: transmission-line algorithms
mathjax: true
image: https://user-images.githubusercontent.com/23442063/146563486-08d98f4d-eacc-4279-b255-00f934c19e96.png
---

Sag-tension calculations are a significant aspect of transmission line design. Whether evaluating line clearances, structure design loads, or providing section stringing information to construction, sag-tension calculations must be performed. However, often, documents covering the process only present a high level concept of the variables involved without presenting a concrete or exact calculation algorithm. On occasion, the calculations are presented in old publications that do not cover more modern approaches and considerations. Or the exact details of the calculations are not presented for proprietary reasons, hidden within the design programs of the companies that developed them. This post is intended to present a complete algorithm for performing sag-tension calculations, laying out all of the equations necessary to perform the calculation. If you plan to implement the algorithm, a programmatic approach will almost certainly be required since iterative calculations are necessary.

<!--excerpt-->

![Example Stringing Chart](https://user-images.githubusercontent.com/23442063/146560964-d2748e23-aa87-40d4-be1c-70a8a29fb864.jpg)

<span class='figure-title'>Figure 1: Example Stringing Chart</span>

## Wire Unit Load Development

Wire tensions are calculated based on the resultant unit load on the wire. For the wire cross section shown in Figure 2, the resultant unit load, $w_r$, is calculated as:

$$ w_h = P_w (D_w + 2 r_i) \sin^2(S_{az} - W_{az}) $$

$$ w_v = \rho_i \pi r_i (D_w + r_i) + w $$

$$ w_r = \sqrt{w_h^2 + w_v^2} + K $$

where

* $ w = $ the unit weight of the wire.
* $ w_h = $ the horizontal unit load.
* $ w_v = $ the vertical unit load.
* $ P_w = $ the wind pressure.
* $ D_w = $ the diameter of the wire.
* $ r_i = $ the radial ice on the wire.
* $ \rho_i = $ the ice density, typically 8954 N / m<sup>3</sup> or 57 pcf.
* $ K = $ the NESC constant.
* $ S_{az} = $ the azimuth of the span.
* $ W_{az} = $ the wind azimuth.

![Wire Cross Section](https://user-images.githubusercontent.com/23442063/146645408-e51f05a9-5d5b-49f4-8132-11665555efbc.png)

<span class='figure-title'>Figure 2: Cross Section of Wire</span>

The most simplistic form of wind pressure can be calculated as $ P_w = Q V_w^2 $, where $ Q $ is the wind density factor, typically 0.6125 (N/m<sup>2</sup>) / (m/s)<sup>2</sup> or 0.00256 psf / mph<sup>2</sup>, and $ V_w $ is the wind velocity. The NESC and structural codes often apply additional factors to account for wind speed variations based on the height of the structure above ground, as well as the structure response. For information on applying these additional factors, see the NESC or applicable structural codes.

The worst case horizontal pressure obviously occurs when the wind blows perpendicular to the span or $ \vert S_{az} - W_{az}\vert = 90^{\text{o}} $.


## Catenary External Mechanics

A wire strung between two supports under its own weight forms a special curve known as a catenary. Its shape is similar to that of a parabola but differs slightly due to the fact that the self weight of the wire if applied along its arc length instead of
along the length of the span.

The geometry of interest for the catenary is shown in Figure 3. In the sections to follow, we will define the external or geometric mechanics of the catenary, which, for sag-tension calculations, must ultimately be linked to the internal mechanics of the wire.

![Catenary](https://user-images.githubusercontent.com/23442063/146561305-a8c6a6be-e5a6-4a5d-92d2-313ef959cb65.png)

<span class='figure-title'>Figure 3: Catenary Curve</span>

### Horizontal Tension and the Catenary Constant
While the derivation of the catenary equations is beyond the scope of this post (See <a href='https://en.wikipedia.org/wiki/Catenary'>Wikipedia</a> for the base derivation), one value the reoccurs throughout many of the catenary equations is the ratio of the horizontal tension, $H$, which occurs at the belly or horizontal tangent point of the span, to the wire unit load $w_r$. Due to its recurrence throughout the equations, this value has been given a special name: the catenary constant. The catenary constant is defined as:

$$ C = \frac{H}{w_r} $$

### Distance to Belly of Sag
For a known catenary constant, the horizontal distance between the back support and the belly of the sag is:

$$ L_b = \frac{L}{2} - C \text{ asinh}\bigg(\frac{\Delta z}{2C\sinh\frac{L}{2 C}}\bigg) $$

where

* $ L = $ the span length
* $ \Delta z = $ the elevation change between span attachments

### Stressed Length of Wire
Also of interest to us is the arc length of wire in the span under a loaded condition. This value is of interest to us when calculating the average tension as well as calculating the average strain in the wire. The stressed length is calculated as:

$$ S = \sqrt{\bigg(2 C \sinh \frac{L}{2 C}\bigg)^2 + \Delta z^2} $$

### Midspan Sag
The sag at midspan is typically measured as the vertical distance between the wire and chord between the span attachments. This may be calculated as:

$$ D = \frac{\Delta z}{2} - C \bigg(\cosh\frac{L/2 - L_b}{C} - \cosh\frac{L_b}{C}\bigg) $$

### Maximum Tension
For situations where a maximum tension or a percentage of the rated breaking strength of the wire forms a constraint, the following equation, which relates the catenary constant to the maximum tension, is required:

$$ T_{max} = w_r C \cosh\bigg(\frac{\max(L-L_b, L_b)}{C}\bigg) $$


### Average Tension Due to External Loading
Assuming a value for the catenary constant, $ C $, the average tension due to external loads is calculated as:

$$ T_{avg} = \frac{w_r C^2}{2 S} \bigg(\sinh\frac{L_b}{C} \cosh\frac{L_b}{C} + \sinh\frac{L - L_b}{C} \cosh\frac{L - L_b}{C} + \frac{L}{C}\bigg) $$

The equation is based on the level span equation presented in Winkelman [1] and is found by integrating the resultant tensions in the wire and dividing by the arc length.


## Internal Wire Mechanics

The materials from which a wire is composed generally exhibit elasto-plastic behavior, with the exception of some cables such as ADSS, which exhibit purely elastic behavior. To describe the elasto-plastic behavior, let's use the stress-strain diagram presented in Figure 4. When a new wire is initially loaded, the load path follows from the left of the plot up curve (3), which is an initial loading curve typically characterized by some polynomial. During this process, the wire develops permanent plastic elongation. Therefore, when it is unloaded, it no longer follows back down curve (3). Instead, it follows a linear elastic curve, region (2), back down until it reaches an unloaded or possibly compressive state, region (1), which is usually set to a constant stress limit. On subsequent loadings, the load path no longer follows curve (3) but proceeds up the elastic curve, region (2), where it will either reside on this curve or exceed it, developing additional plastic strain and shifting the elastic curve further to the right by $ \epsilon_p $.

One region that we haven't discussed is region (4), the linear extrapolation portion. This region should be viewed as an extension of the region (3) initial loading curve. Since the region (3) curve is constructed from polynomial coefficients, it may diverge from the actual loading data at some point. To compensate for this, linear extrapolation of the loading curve is instead performed after some strain $ \epsilon_{le} $ for some wires. Often, this value is taken as 0.5% strain.

![Stress-Strain Plot](https://user-images.githubusercontent.com/23442063/146561428-850ed3b6-3639-4209-8ddc-9f99035c2b4c.png)

<span class='figure-title'>Figure 4: Wire Stress-Strain Curve</span>


### Average Tension Due to Mechanical Strain

The internal average mechanical strain, $ e_m $, is related to the external loads by the equation:

$$ \epsilon_m = \frac{S}{S_u} - 1 $$

Here, $ S $ is the stressed length of the wire and $ S_u $ is the unstressed length.

The temperature of each wire layer causes thermal strain that increases or reduces the tension contribution of that layer. When the temperature is greater than the loading curve reference temperature, the tension in the layer decreases, while when the temperature in the layer is lower, the tension is increased. The thermal strain in a layer is calculated as:

$$ \epsilon_t = \alpha (t - t_{ref}) $$

where

* $ \alpha = $ the thermal expansion coefficient of the layer
* $ t = $ the temperature of the layer
* $ t_{ref} = $ the reference temperature at which the stress-strain curves were developed

The stress along the initial loading curve in regions (3) and (4) is shown below. Note that the coefficients for the stress strain polynomials should match the condition for interest. That is, the coefficients should match the creep condition when evaluating creep due to everyday conditions and the initial condition when evaluating the strain due to high loading events.

$$
\sigma_{curve} =
\begin{cases}
\sum_{i=0}^{n-1} c_i (\epsilon_m - \epsilon_t)^i, & \epsilon_m - \epsilon_t \leq \epsilon_{le} \\
& \\
(\epsilon_m - \epsilon_t - \epsilon_{le}) \sum_{i=1}^{n-1} i c_i \epsilon_{le}^{i-1} + \sum_{i=0}^{n-1} c_i \epsilon_{le}^i, & \text{otherwise}
\end{cases}
$$

where

* $ c_i = $ the $i$-th term coefficient of the stress strain polynomial
* $ n = $ the number of coefficients in the polynomial
* $ \epsilon_{le} = $ the strain, if any, at which linear extrapolation will be performed. Typically, this is taken as 0.5% strain.

The stress on the elastic, region (2), portion of the curve is calculated as:

$$
\sigma_{elast} = E (\epsilon_m - \epsilon_t - \epsilon_p)
$$

where $ E $ is the virtual elastic modulus of the layer.

The average tension is calculated as:

$$
T_{avg,layer} = A
\begin{cases}
\sigma_b, & \min(\sigma_{elast}, \sigma_{curve}) < \sigma_b \\
& \\
\sigma_{elast}, & \sigma_{elast} < \sigma_{curve} \\
& \\
\sigma_{curve}, & \text{otherwise}
\end{cases}
$$

where $ A $ is the cross sectional area of the wire and $\sigma_b$ is the bimetallic conductor compression limit. The compression limit may be taken as zero or some additional value at which point the wire strands in the layer buckle and bird cage, capable of taking no additional compressive load.

The plastic strain in the layer is required to evaluate future loadings. The plastic strain resulting from a tension is calculated as:

$$ \epsilon_p = \epsilon_m - \epsilon_t - \frac{T_{avg,layer}}{A E} $$

This value must be greater than or equal to zero.

The total average tension in the wire is simply the sum of the average tensions in the individual layers. For a wire with a core and outer layer, the total average tension is:

$$ T_{avg} = T_{avg,core} + T_{avg,outer} $$


## Sag-Tension Algorithm

Using the previously presented equations, the following algorithm can be applied to acquire the "Initial" and "Final" tensions and sags for a catenary. The "Initial" tensions and sags are often used when installing new wire, which has not experienced creep or high loading events; whereas, the "Final" tensions are to evaluate maximum sags for clearance calculations or when stringing existing wire.

1. Calculate the resultant wire loads for all loading conditions. These values will be used repeatedly for subsequent calculations.

2. Calculate the controlling unstressed length for the constraining loading conditions. These are loading conditions that have a set maximum tension or sag limit associated with them. The controlling unstressed length is the maximum resulting from all constraining loading conditions. For this calculation, assume that the plastic strains are zero; thus the loads always occur along the loading curves. For each constraining loading condition, perform the following:

    1. Determine the catenary constant for the loading condition based on the calculated resultant wire load, $ w_r $.

        * <b>From Horizontal Tension</b> - Calculate the catenary constant by dividing by the resultant unit load.

        * <b>From Maximum Tension</b> - Through iteration, calculate the catenary constant, $ C $, corresponding to the known $ T_{max} $ using the <a href='#maximum-tension'>maximum tension equation</a>.

        * <b>From Sag</b> - Through iteration, calculate the catenary constant, $ C $, corresponding to the known $ D $ using the <a href='#midspan-sag'>sag equation</a>.

        * <b>From Final Sag-Tension Condition</b> - This calculation requires iterating through Steps 2 through 4 of this calculation until the value for the constraining final sag-tension condition is met.

    2. Seeking the unstressed length, perform iterative calculations of the <a href='#average-tension-due-to-external-loading'>external average tension</a> and <a href='#average-tension-due-to-mechanical-strain'>internal average tension</a> until they are equal. The length of the span, $ L $, tends to be a suitable initial value for the unstressed length.

2. Using the previously calculated unstressed length, determine the maximum plastic strains resulting from the creep and load conditions. The applied load conditions should likely represent your district loading, a concurrent wind and ice event, and possibly a heavy ice event. The applied creep condition is generally taken as an everyday temperature, assumed to reflect the average everyday, sustained tension in the wire. To simplify this calculation, the average tensions should always occur along the loading curves. For each creep or load condition, perform the following:

    1. Seeking the catenary constant, perform iterative calculations of the <a href='#average-tension-due-to-external-loading'>external average tension</a> and <a href='#average-tension-due-to-mechanical-strain'>internal average tension</a> until they are equal. From the parabolic equations, a suitable initial value for the catenary constant is $ C = \sqrt{L^3 / (24 (S_u - L))}$. Use the creep curve for creep loading conditions and the initial curve for high loading conditions.

    2. Calculate the plastic strains for the layers using the catenary constant acquired above and the <a href='#average-tension-due-to-mechanical-strain'>internal average tension</a> equations.

4. For each desired loading condition, calculate the sag-tension information. Sag-tension information for the "Initial" condition should be calculated assuming the plastic strains are zero. As such, to simplify the calculations, the values should be located along the loading curves. Sag-tension information for the "Final" condition should be calculated using the maximum plastic strains. Provided that the plastic strains have properly considered the worst loading conditions, the calculations can be simplified by only considering the elastic and compressive regions of the stress-strain plot. With this in mind, for each loading condition for both the "Initial" and "Final" conditions, perform the following:

    1. Seeking the catenary constant, perform iterative calculations of the <a href='#average-tension-due-to-external-loading'>external average tension</a> and <a href='#average-tension-due-to-mechanical-strain'>internal average tension</a> until they are equal. From the parabolic equations, a suitable initial value for the catenary constant is $ C = \sqrt{L^3 / (24 (S_u - L))}$.

    2. With the above catenary constant, calculate the horizontal tension, sag, and maximum tensions using the equations presented previously.


## A Discussion of Constraints and Conditions

The following sections provide example cases that might be considered at different stages of the sag-tension algorithm for the purpose of helping connect input data to the actual application of the algorithm.

### Constraints

An example of possible constraints used for Step 2 of the sag-tension algorithm are shown in the table below. Here, the constraints are all defined as some percentage of the wire rated breaking strength. Therefore, these constraints actually reflect maximum tension values.

To implement the algorithm, we would calculate the unstressed length corresponding to each of these constraints. The maximum unstressed length controls since using smaller unstressed lengths would increase the tension, and thus exceed our constraining criteria. This maximum value is used for all subsequent calculations.

The loading condition -- "Initial" or "Creep" -- must be considered for this calculation. High loading scenarios, such as the NESC Heavy district loading case, should generally have the initial stress-strain polynomial applied. The creep polynomial is typically reserved for an everyday condition representative of the average sustained tensions on the wire.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Temperature (degF)</th>
      <th>Wind Pressure (psf)</th>
      <th>Radial Ice (in)</th>
      <th>K Constant (plf)</th>
      <th>Condition</th>
      <th>Constraint</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>NESC Heavy</td>
      <td>0</td>
      <td>4</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>Initial</td>
      <td>60% RBS</td>
    </tr>
    <tr>
      <td>Everyday Initial</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Initial</td>
      <td>35% RBS</td>
    </tr>
    <tr>
      <td>Everyday Creep</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Creep</td>
      <td>25% RBS</td>
    </tr>
  </tbody>
</table>


### Conditions

An example of possible load and creep conditions used for Step 3 of the sag-tension algorithm are shown in the table below. Concurrent wind and ice and heavy ice cases are not listed here but could be included as well in the event they develop worse plastic strains.

To implement the algorithm, we would calculate the plastic strains occurring in each layer of the wire based on the applied loads. Cases listed with an "Initial" condition correspond to plastic strains developed after load, while cases indicated as "Creep" correspond to plastic strains developed after creep. If we simply wish to get the maximum of "Final" sag, the plastic strains for the creep and load conditions can simply be taken as the maximum of both. However, in the event you wish to calculate the creep and load conditions separately, the maximum plastic strains will likewise need to be tracked separately.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Temperature (degF)</th>
      <th>Wind Pressure (psf)</th>
      <th>Radial Ice (in)</th>
      <th>K Constant (plf)</th>
      <th>Condition</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>NESC Heavy</td>
      <td>0</td>
      <td>4</td>
      <td>0.5</td>
      <td>0.3</td>
      <td>Initial</td>
    </tr>
    <tr>
      <td>Everyday Initial</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Initial</td>
    </tr>
    <tr>
      <td>Everyday Creep</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>Creep</td>
    </tr>
  </tbody>
</table>

### Sag-Tension Conditions

An example of possible load and creep conditions used for Step 4 of the sag-tension algorithm are shown in the table below. This list includes the load cases presented previously along with the maximum operating temperature, for clearance calculations, and a variety of temperatures that might be used for stringing by construction.

The "Initial" sags and tensions can be acquired by assuming the plastic strains for each case are zero; whereas, the "Final" sags and tensions can be acquired by considering the maximum plastic strains. The plastic strains for the creep and load conditions can also be considered separately by applying their plastic strains separately.

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Temperature (degF)</th>
      <th>Wind Pressure (psf)</th>
      <th>Radial Ice (in)</th>
      <th>K Constant (plf)</th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>NESC Heavy</td>
      <td>0</td>
      <td>4</td>
      <td>0.5</td>
      <td>0.3</td>
    </tr>
    <tr>
      <td>Everyday Initial</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Everyday Creep</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>10 degF</td>
      <td>10</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>20 degF</td>
      <td>20</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>30 degF</td>
      <td>30</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>40 degF</td>
      <td>40</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>50 degF</td>
      <td>50</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>60 degF</td>
      <td>60</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>70 degF</td>
      <td>70</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>80 degF</td>
      <td>80</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>90 degF</td>
      <td>90</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>100 degF</td>
      <td>100</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Max Operating Temp.</td>
      <td>212</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>

## Final Remarks

With that, the sag-tension algorithm is complete. This is a fairly complicated post, so I wouldn't be surprised if I've made a mistake somewhere. Feel free to post questions or corrections in the comments.

## References

* [1] Winkelman, Paul F. "Sag-Tension Computations and Field Measurements of Bonneville Power Administration." Feb. 1960.
* [2] Cigre 324. "Sag-Tension Calculation Methods for Overhead Lines." Jun 2007.
