---
title: An Approach to Weight-Based Battle AI
categories: game-design artificial-intelligence
published: true
---

### Action Probablity Vector

For a set of actions, we will define the probablity vector for selecting any given action as

$$ \vec{p} = \text{softmax} ( w_i w_s w_c w_m ) $$

where

* $ w_i = $ the intelligence weight.
* $ w_s = $ the strategy weight corresponding to the action code of an element.
* $ w_c = $ the weight for the action code of an element.
* $ w_m = $ an optional weight for an element that may be manually specified by the designer.

Here, the [softmax](https://en.wikipedia.org/wiki/Softmax_function) function simply provides a way of exaggerating the differences in the weights between actions.

With this vector, a random action can be selected by drawing a random number between 0 and 1, then finding the first element that is both greater than zero and greater than or equal to the random number.

### Action Code Weight ($ w_c $)

The action code weight will likely be dependent on (1) the actor, (2) the possible targets, and (3) the action itself. In general, when calculating weights for action codes, you will want to identify one or more parameters that can be normalized based on these sources. For example, estimated hit point damage to a target can be normalized by the target's current hit points. This normalized parameter can then be used to linearly interpolate ([Lerp](https://en.wikipedia.org/wiki/Linear_interpolation)) between two weight bounds, $w_{min}$ and $w_{max}$, to produce values for $ w_c $.

If multiple normalized parameters are valid for a code, the interpolated weights may be multiplied together to produce $w_c$. However, $w_{min}$ and $w_{max}$ for each interpolation must be modified to ensure that they reside within the same bounds used by all other codes. Otherwise, the code would inevitably either be more highly favored or disfavored than the other codes. For the instance of two equally weighted parameters, $w_{min}^{0.5}$ and $w_{min}^{0.5}$ may be used for the weight bounds since the exponents, when the terms are multiplied, sum to 1. In other words, $w^{0.5} w^{0.5} = w^1$. Of course, the normalized parameters need not be both of equal importance. For example, another valid weight for an uneven weighting of the normalized parameters would be $w^{0.25} w^{0.75} = w^{1}$.

Based on the need to calculate new bounds for multiple normalized parameters, it is convenient to take $w_{min}$ to be 1, since no additional calculations for the minimum bound are then required. A suitable $w_{max}$ will have to be determined based on the specific scenario; however, I feel that a value of about 3 is a good place to start.

If an action is not valid (e.g. there is not enough MP to cast it, or there is not a valid target to receive the effect) the value of $w_c$ may be set to $-\infty$, since, when passed to the softmax function, a 0% draw chance will be produced for that element.

The following sections provide some examples for action code calculations.

#### Example Action Code: Deals Damage

A suitable normalized parameter for the j-th target combatant is

$$ t_j = \frac{\text{Estimated Hit Point Damage}}{\text{Current Hit Points}} $$

The weight for a target is

$$ w_j = \text{Lerp}(w_{min}, w_{max}, t_j) = \text{Lerp}(1, 3, t_j) $$

The action code weight is the the maximum of the weights calculated for all targets

$$ w_c = \text{max}(\{w_j\}) $$

#### Example Action Code: Restores Hit Points

Two normalized parameters seem suitable for this type of action code

$$ t_j = \frac{\text{min(Max Restored Hit Points, Max Hit Points - Hit Points)}}{\text{Max Restored Hit Points}} $$

and

$$ s_j = 1 - \frac{\text{Target Hit Points}}{\text{Target Max Hit Points}} $$

The first parameter reduces the waste of a better item or more powerful spell when a lesser item or spell could be used. The second parameter increases the likelihood that a target receives healing as its health decreases relative to its maximum value.

For equal parameter weights, the weight for the j-th target is

$$\begin{eqnarray}
w_j &=& \text{Lerp}(w_{min}^{0.5}, w_{max}^{0.5}, t_j) \cdot \text{Lerp}(w_{min}^{0.5}, w_{max}^{0.5}, s_j) \nonumber \\
&=& \text{Lerp}(1, 3^{0.5}, t_j) \cdot \text{Lerp}(1, 3^{0.5}, s_j) \nonumber \\
\end{eqnarray}$$

The action code weight is then the the maximum of the weights calculated for all targets

$$ w_c = \text{max}(\{w_j\}) $$

Or if a target with missing hit points does not exist,

$$ w_c = -\infty $$

#### Example Action Code: Revives Combatant

If there are no downed combatants in the action's target scope,

$$ w_c = -\infty $$

Otherwise, a suitable normalized parameters is

$$ t = \frac{\text{Downed Target Count}}{\text{Total Target Count}} $$

Then,

$$ w_c = \text{Lerp}(w_{min}, w_{max}, t) = \text{Lerp}(1, 3, t) $$

### Strategy Weight ($ w_s $)

The strategy weight allows the battle AI to be modified based on a selected battle strategy or an actor's class, e.g. warrior, cleric, or rogue. The value should likely reside on the interval [1, 2], where the right bound can be fairly flexibly defined, though extremely large values are obviously undesirable for a well rounded AI.

The value is dependent on the definition of the strategy and the action code, which correlates to the action being taken. A perfectly balanced strategy would define this weight as 1 for all action codes. Whereas, an aggressive strategy would define a larger value, say 1.2, for all damage type action codes, then 1 for all others. Likewise, healing strategies would favor healing and revival action codes over damage related ones.


### Intelligence Weight ($ w_i $)

The intelligence weight provides a scale factor to allow variation in the AI's intelligence based on the game difficulty and the combatant type, e.g. player support character, enemy mob, or enemy boss. This value could be derived from a number of factors, but should most likely be confined to the interval (0, 1], with "dumber" AI's intelligence weight located toward the left of the interval, and "smarter" AI to the right.

When the value is just to the right of the left bound (0), the exponential terms in the softmax function will approach 1, meaning that there will be an almost equal probablity that any valid action will be selected. Hence, the "dumber" AI. Meanwhile, "smarter" AI's, using values closer to 1, will see fuller expressions of the other weight terms.

The only reason that I do not define the left bound of this interval inclusively, i.e. to be exactly that of 0, is because the weight $ w_c $ can be set to $ -\infty $ for completely omitted elements, and the overall multiplication will still yield $ -\infty $ and not 0.

An example of this value for some normalized parameter, $ t $, might be

$$ w_i = \text{Lerp}(10^{-7}, 1 ,t) $$

### Manually Specified Weight ($w_m$)

The manually specified weight provides an entry point by which special behavior might be incorporated into a specific AI. Generally, it should simply be taken as 1. However, larger values will increase the chance that a particular action is selected, while smaller values will decrease it.
