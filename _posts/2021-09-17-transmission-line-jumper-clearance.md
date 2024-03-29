---
layout: post
title: Transmission Line Jumper Clearance and Minimum Line Angle Calculations
tags: engineering transmission-line
mathjax: true
image: https://user-images.githubusercontent.com/23442063/133910107-3e4faf72-124a-4ac5-880f-aedefddafeb4.png
---

In order to maintain electrical clearances within transmission line dead-end structures, jumper supports, such as horizontal posts or I-string insulators supported on davit arms, are occasionally required in order to restrain the jumper away from the structure. Most often, they are required for in-line dead-ends (structures with no line angle) or dead-ends with small line angles; whereas, structures with large line angles, such as that shown in Figure 1, do not require jumper supports since the length of the insulators alone provide an adequate offset distance from the structure.

This post will present equations to assist in evaluating the clearance between a jumper and pole with and without jumper supports. In addition, an equation to calculate the minimum line angle required in order to maintain electrical clearances to the pole for a structure without a jumper support will be presented, as this value can be beneficial in making general design decisions or specifying limits for structures within design standards.

<!--excerpt-->

![structure-no-jumper-support](https://user-images.githubusercontent.com/23442063/133910107-3e4faf72-124a-4ac5-880f-aedefddafeb4.png)

<span class="figure-title">Figure 1: Structure without Jumper Support</span>


## Clearance to Structure without Jumper Support

A simplified plan geometry for a structure without a jumper support is shown in Figure 2. The variables shown are as follows:

* $C=$ the clearance between the jumper and pole.
* $I=$ the horizontal offset of the insulator attachments from the pole.
* $P=$ the radius of the pole at the position of interest.
* $\Delta=$ the line deflection angle.

![no-jumper-support](https://user-images.githubusercontent.com/23442063/133910109-e7aa777b-8c7c-418b-8856-d588db978276.png)

<span class="figure-title">Figure 2: Clearance to Structure with No Jumper Support</span>

In this figure, the jumper is assumed to follow a straight line between the insulator attachments at the right ends of segments $I$. For short jumpers, this is likely conservative, even when blowout of the wire is considered, since most likely the strain clamps or jumper terminals at the ends of the jumpers are set such that the hardware angles the jumper away from the structure and provides sufficient rigidity to prevent the blow in of the wire towards the pole. In the event the blowout of the jumper towards the structure is a design concern, such as in the case of long jumpers, additional buffers to the required clearance could be added. Also, in the event of steep inclines where the insulators slope downward, the distance used for $I$ should be the horizontal offset of the insulator attachment point rather than the full insulator length.

From this simple geometry, the clearance, $C$, to the structure can be calculated as:

$$ C = (P+I)\sin\bigg(\frac{\Delta}{2}\bigg) - P $$

It is likely that the minimum clearance to the structure is known for a given line voltage. Therefore, rather than calculating specific clearances for all structures of interest, it may be more beneficial to calculate the minimum line angle required in order to maintain the required clearance. Rearranging the above expression results in the following equation for the minimum line angle for a structure without a jumper support.

$$ \Delta_{min} = 2 \text{asin}\bigg(\frac{P+C}{P+I}\bigg)$$


## Clearance to Structure with Jumper Support

A simplified plan geometry for a structure with a jumper support is shown in Figure 3. The variables shown are the same as the case of the geometry without a jumper support, except that the inclusion of the horizontal offset of the jumper support, $S$, has been incorporated.

![jumper-support](https://user-images.githubusercontent.com/23442063/133910112-95cf209d-7651-44a1-86c0-8afcf31e2acc.png)

<span class="figure-title">Figure 3: Clearance to Structure with Jumper Support</span>

From this geometry, the angle $\beta$ is:

$$ \beta = \text{atan}\Bigg(\frac{(P+I)\cos\frac{\Delta}{2}}{(P+S)-(P+I)\sin\frac{\Delta}{2}}\Bigg) $$

The clearance to the jumper, $C$, is perpendicular to the jumper line. Therefore, using the above angle, the clearance may be calculated as:

$$ C = (P+S)\sin\beta - P $$

It is possible that this clearance is greater than the clearances afforded at the jumper attachments. Therefore, the controlling clearance should be taken as the minimum of this value and that of $I$ and $S$. If desired, the minimum line angle may also be acquired via iteration.

## Attachments

* [Jumper Support Clearance Calculation Spreadsheet](https://docs.google.com/spreadsheets/d/15kK1xN_rxp32nFb_gX_IJxdkhPSM7J-ZyGPG0tYTuEs/edit?usp=sharing)
