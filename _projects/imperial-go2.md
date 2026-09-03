---
layout: page
title: Learning to Walk (Unitree Go2)
description: A one-week team project at the Imperial College Robotics Summer School 2026 - training RL locomotion policies for the Unitree Go2 in massively parallel simulation and deploying them zero-shot onto the real robot.
img: assets/img/go2-hero.jpg
importance: 1
category: work
related_publications: false
---

### Overview
At the **Imperial College Robotics Summer School 2026** (Adaptive & Intelligent Robotics Lab), our group spent a week teaching a **Unitree Go2** quadruped to walk with reinforcement learning. We trained locomotion policies in **massively parallel simulation** (Isaac Gym), logged everything to Weights & Biases, and deployed the resulting walking policy **zero-shot onto the physical robot**, where it ran onboard the Jetson with no fine-tuning.

The project built on **[walk-these-ways](https://github.com/Improbable-AI/walk-these-ways)** (Margolis & Agrawal, CoRL 2022), originally written for the Go1, which we ported to the Go2 through `unitree_sdk2`. Our own work was in the reward design, the training and evaluation loop, and getting the sim-to-real transfer to hold.

---

## The task
Teach a quadruped to track velocity commands over rough terrain, then push the same framework toward harder behaviours (tripod gait, bipedal stance, handstand). Two constraints shaped everything:

- **Large-scale parallel simulation.** Thousands of robots stepping at once on a single GPU, PPO learning a stable walk in minutes rather than days (Rudin et al., CoRL 2021).
- **The sim-to-reality gap.** A policy that looks perfect in simulation is worthless if it falls over on the real robot.

---

## Method

### One policy, many gaits
Following *Walk These Ways*, a single policy is conditioned on two inputs: a **command** \\(c_t = [v_x, v_y, \omega_z]\\) (the velocity the robot should track) and a **behaviour vector** \\(b_t\\) holding gait phase offsets, stepping frequency, footswing height, body pitch, stance width and body height. Changing \\(b_t\\) at deployment changes *how* the robot walks - trot, pace, bound, different footswing - without retraining.

The policy is an MLP `[512, 256, 128]` with ELU activations, taking observation, command, behaviour, timing-reference and action histories and outputting target joint positions.

### Model-based scaffolding around the learned policy
The learned policy sits inside hand-designed structure:

- a **gait clock** turns the behaviour vector into `desired_contact_states` (which foot should be down, and when);
- a **Raibert-style foot-placement term** rewards the policy for stepping where a classical velocity-regulation heuristic says it should;
- an **actuator model** (a network fit to real motor data, or a PD approximation) runs inside the simulator so the policy learns against realistic motor dynamics rather than an ideal torque source. This is the main lever for closing the sim-to-real gap.

### Reward
The reward follows the multiplicative *Walk These Ways* form:

$$
r = P \cdot \exp\!\left(N / \sigma\right), \qquad \sigma = 0.02
$$

where \\(P\\) is a small set of positive tracking terms (`tracking_lin_vel` +1.0, `tracking_ang_vel` +0.5, commanded speed swept 0 to 1.5 m/s) and \\(N \le 0\\) collects roughly seventeen penalty terms in four groups:

| Group | Terms |
|---|---|
| **Gait shaping** | `contacts_shaped_force`, `contacts_shaped_vel`, `feet_clearance`, `raibert_heuristic` |
| **Posture** | `jump` (height), `orientation_control` (tilt) |
| **Smoothness** | `action_rate`, `smoothness_1/2`, `torques`, `dof_vel`, `dof_acc`, `dof_pos_limits` |
| **Safety** | `collision`, `feet_slip`, `lin_vel_z`, `ang_vel_xy` |

"Earn the positive reward, then discount it by how much you violated the constraints." A terrain-difficulty curriculum ramps the environment up as the policy improves.

{% include figure.liquid
  path="assets/img/go2-reward-structure.png"
  class="img-fluid rounded z-depth-1"
  caption="Commands feed a gait clock that defines desired contact states; positive tracking reward P is discounted by the aggregated penalty N via r = P·exp(N/σ)."
%}

{% include figure.liquid
  path="assets/img/go2-policy-diagram.png"
  class="img-fluid rounded z-depth-1"
  caption="One policy π(·|c_t, b_t), MLP [512,256,128] + ELU, wrapped by a gait clock and an actuator model for sim-to-real."
%}

---

## Training

PPO in Isaac Gym Preview 4 (PyTorch 1.13 / CUDA 11.6), packaged in an Apptainer container for reproducibility, logged to Weights & Biases. A study of **number of robots vs. batch size** (128 to 16384 parallel robots) mapped the trade-off between final reward, wall-clock time and run stability; with the curriculum held at maximum difficulty the default walking policy converged in **under 20 minutes**.

{% include figure.liquid
  path="assets/img/go2-training-curves.png"
  class="img-fluid rounded z-depth-1"
  caption="W&B reward curves for the default walking policy: linear- and angular-velocity tracking rise and hold; shaped-contact and torque penalties settle; total reward climbs over ~200k steps."
%}

---

## Sim-to-real

The default trot policy transferred to the real Go2 **zero-shot** - no fine-tuning on hardware - and ran onboard the robot's Jetson via an LCM control bridge.

{% include video.liquid
  path="assets/video/go2-walk-real.mp4"
  class="img-fluid rounded z-depth-1"
  controls=true
  autoplay=true
  loop=true
  muted=true
%}
<div class="caption">The policy trained entirely in simulation, walking on the real robot.</div>

---

## Beyond walking: tripod, bipedal, handstand

We reused the framework to push for harder behaviours by reshaping the reward:

- **Tripod gait** (walking on three legs, for damage recovery) - trained, but needs longer runs to fully stabilise.
- **Bipedal stance** - "almost". The best simulation attempt got the robot up but not robustly.
- **Handstand** - trained in simulation and attempted on the real robot. Two failure modes were instructive: reward terms that quietly conflict so total reward trends the wrong way, and separate runs that collapse outright. When objectives fight each other, it is a good thing the diagnosis happens in simulation.

{% include figure.liquid
  path="assets/img/go2-handstand.jpg"
  class="img-fluid rounded z-depth-1"
  caption="Handstand policy in simulation (left) and the real-robot attempt (right). New behaviours need shaped rewards, more compute and longer training than a plain walk."
%}

---

## Limitations
Honest about what a week allowed:

- **Compute** - a shared GPU capped the number and length of training runs.
- **Reward and hyperparameter search** - little time for anything systematic.
- **Sim-to-real scope** - only the default walking gait was validated on hardware; the harder behaviours stayed mostly in simulation.

These limit how far the results generalise, not the core outcome: **an RL walking policy trained in simulation transfers to the real Go2.**

---

### My contribution
It was a five-person project and I worked inside a framework the team adopted, not one I built. My part was:

- **Reward-function and hyperparameter tuning** for the PPO training - shaping and weighting the penalty terms, sweeping learning settings, and running the number-of-robots vs. batch-size study.
- **The experimentation and evaluation pipeline** - configuring runs, W&B logging and the analysis of training curves and rollouts.
- **The sim-to-real transfer** - getting the default walking policy onto the physical Go2 and running onboard.

I did not design the gait clock or write the actuator network; those came with the `walk-these-ways` codebase and were tuned by the team.

---

### Tools
Python • PyTorch • PPO • Isaac Gym • Weights & Biases • Apptainer / Docker • Unitree Go2 • `unitree_sdk2` • LCM • Jetson
</content>
</invoke>
