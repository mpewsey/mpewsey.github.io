---
layout: post
title: An Approach to Randomly Distributing Game Collectables
tags: game-design algorithms
mathjax: true
image: https://user-images.githubusercontent.com/23442063/164042507-060febe8-8bc2-4e8e-9243-226fb1e248b3.png
---

At first glance, the problem of distributing game collectables throughout a cell-based procedurally generated map with predefined collectable spots may seem simple: for each collectable, simply draw a random collectable spot and assign the collectable to it. However, this approach is not without its pitfalls. For instance, if the collectable spot density throughout the map is non-uniform, the distributed collectables will also have a similar non-uniform density. Furthermore, a random distribution does nothing to prioritize placing treasures off the main path, in nooks or dead-ends, as an actual game designer might place them. To compensate for these shortcomings, rather than placing collectables purely at random, we can assign higher or lower weights to each collectable spot based on their desirability, then draw random spots based on these weights. This post presents an approach to allocating collectables based on such weights.

<!--excerpt-->

## Selecting a Weight Function

In order to begin assigning weights to collectable spots, we need to decide how such weights will be quantified via a weight function. In selecting this weight function, we need to decide which factors are important to us. We likely don't want the collectables to all be bunched up in one spot, so a factor that increases the draw chance of a location based on how far it is from other collectables will be needed. Many games also tend to place collectables off the main path, often at the end of dead-end branch paths. Therefore, a factor to increase the draw chance of such locations also seems important.

### Neighboring Collectable Weight

For the case of ensuring the collectables are evenly distributed, one approach would be to define a weight proportional to the distance to the closest collectable. For this, let $\vec{d_n}$ be a vector of path distances from the collectable spot to all allocated neighboring collectables within a set bounds, e.g. the collectables within the same room and one room away (See Figure 1). We can then define the neighboring collectable weight $W_n$ to be the minimum of these distances:

$$ W_n = \text{Min}(\vec{d_n}) $$

Since this value is dependent on the distance to all _allocated_ collectables within a set scope, if there are no allocated collectables within a scope, such as at the start of the algorithm, then the weight should be assigned a value at least as large as the path distance to traverse beyond the scope (Arbitrarily, say 1000). As collectables are allocated within the collectable scope, this value is updated and decreased accordingly.

![Neighbor Weight Example](https://user-images.githubusercontent.com/23442063/162529101-4fc2c1ec-baf2-4577-b813-ad2bf16665bc.png)

<span class="figure-title">Figure 1: Neighboring Collectable Path Distances</span>

### Door Weight

Next, for the case of increasing the chances that collectables will be placed off the main path, we need to determine what constitutes a main path. We could determine the minimum distance path between doors, call this the main path, then determine the minimum distance to this route; however, for rooms with limited constraints, such as a simple square room with two doors, there are in fact a large number of minimum distance paths between the doors using adjacent cell traversal (See Figure 2).

![Multiple Paths](https://user-images.githubusercontent.com/23442063/162529098-deb43ab7-3bf1-4cd1-b6eb-7aceba907d84.png)

<span class="figure-title">Figure 2: Multiple Equal Minimum Path Distances Between Doors</span>

To reduce the path down to a single "main" path, we would need to impose further constraints on how that path is defined, which ultimately may not add much benefit to the algorithm. Instead, let's say that a collectable off the main path is the furthest path distance from any of the doors. If $\vec{d_d}$ is a vector of path distances from the collectable spot to all doors in the room, let's define the door weight $W_d$ to be the minimum of these distances:

$$ W_d = \text{Min}(\vec{d_d}) $$

![Door Weight Example](https://user-images.githubusercontent.com/23442063/162529095-7a393a06-bd35-4be9-acb1-5314fbe8a665.png)

<span class="figure-title">Figure 3: Door Path Distances</span>

### Weight Function

Combining the weights previously developed, let's define the effective draw weight for a collectable spot to be a multiplication of these weights:

$$ W = W_n^{P_n} (W_d + 1)^{P_d} $$

Here, the shift of $W_d$ by 1 accounts for the scenario where a collectable spot is in the same cell as a door. Furthermore, the powers $P_n$ and $P_d$ provide additional tuning control over the weights. In most cases, $P_n$ can simply be taken as 1. However, for scenarios where you would like to increase the priority of the door weight, it may be beneficial to use a larger value for $P_d$, such as 2.

## Conclusion

With the weights calculated for each collectable spot, we can draw a collectable spot by computing the cumulative sums of the weights, drawing a random number $w$ between 0 and the total weight, and finding the index where $0 < W[i] \leq w$. Once the collectable spot has been used, we then update the neighbor weights $W_n$ for the remaining collectable spots within the scope of the drawn collectable, and repeat until all collectables have been allocated to a location.
