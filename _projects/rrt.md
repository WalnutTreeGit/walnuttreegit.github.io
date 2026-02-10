---
layout: page
title: Sampling-Based Motion Planning (RRT / RRT*)
description: Implemented and evaluated RRT vs RRT* under different iteration budgets and step sizes, and improved narrow-corridor planning with obstacle-biased sampling (OBRRT-style).
img: assets/img/rrt-star-demo.png
importance: 2
category: work
---

### Overview
Implemented and benchmarked **RRT** and **RRT\*** for 2D motion planning, then addressed the **narrow passage problem** using an **obstacle-biased sampling** strategy (OBRRT-style) to improve success rate in corridor environments.

---

## RRT* demo
<div style="max-width: 550px; margin: 0 auto;">
{% include figure.liquid
  path="assets/img/rrt-star-demo.png"
  class="img-fluid rounded z-depth-1"
  caption="RRT* run example: explored tree (green) and final path (red) through a cluttered 2D workspace."
%}
</div>

---

## RRT vs RRT*
### Findings
- **Path quality:** RRT\* produced shorter paths than RRT in the benchmark environment.
- **Computation time:** RRT\* was substantially slower due to near-neighbor search and rewiring.

<div style="max-width: 550px; margin: 0 auto;">
{% include figure.liquid
  path="assets/img/rrt-vs-rrtstar.png"
  class="img-fluid rounded z-depth-1"
  style="max-width: 400px; margin: 0 auto; display: block;"  
  caption="RRT vs RRT*: RRT* improves average path length but increases computation time."
%}
</div>
 

---

## Step length sensitivity
Changing `step_len` shifts the standard trade-off:
- **Small `step_len`:** denser exploration and smoother refinement, but slower convergence.
- **Large `step_len`:** faster coverage but more collisions/overshoots in cluttered or narrow regions.

---

## Narrow corridors and obstacle-biased sampling (OBRRT-style)
Uniform sampling rarely lands inside a narrow corridor, so most extension attempts collide with corridor walls. To increase the probability of entering the corridor, I biased a fraction of samples toward points near obstacle corners while keeping samples collision-free.

{% include figure.liquid
  path="assets/img/obrrt-results.png"
  class="img-fluid rounded z-depth-1"
  caption="Obstacle-biased sampling in a narrow corridor: moderate bias improves success rate compared to purely uniform sampling."
%}

---

### Tools
Python • NumPy • Matplotlib
