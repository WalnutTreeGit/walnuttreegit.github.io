---
layout: page
title: Autonomous Dragster Robot
description: Arduino-based embedded control for a 10 m autonomous dragster: light-gate start, 5× IR sensing, PID steering, ICStation motor shield, and brake-on-finish logic.
img: assets/img/dragster.png
importance: 7
category: work
---

### Overview
Built an autonomous dragster to complete a **10 m sprint** at high speed and **stop at the finish marker** using onboard sensing and real-time control.

---

## Hardware / sensing / actuation
- **Start gate (mandatory):** LDR on `A0`. The program waits until the light threshold is reached before starting.
- **Tracking sensors:** five analog IR sensors (`A1..A5`) thresholded to detect track contrast.
- **Motor driver:** **ICStation motor shield** providing H-bridge motor drive. Arduino PWM commands the shield; the shield supplies motor current.
- **Actuation:** differential PWM speed control plus braking control to stop at the finish.

---

## Control pipeline
### 1) Discrete tracking error from sensor pattern
The five sensors are binarized (track vs background) and mapped to a discrete error `erro ∈ [-4, 4]` based on drift direction and magnitude.

### 2) PID steering at fixed rate
A control update runs every **10 ms** (100 Hz). A PID-style controller computes a differential correction and adjusts left/right PWM commands through the ICStation motor shield.

---

## Finish-line detection and stopping
The finish marker is detected when **all five sensors** simultaneously detect the finish region. After a minimum runtime threshold, the controller asserts **braking** through the motor shield to stop at the line.

---

## Demo video (GitHub-hosted)
<video controls playsinline preload="metadata"
       style="width:100%; max-width: 900px; display:block; margin: 0 auto; border-radius: 12px;">
  <source src="{{ '/assets/video/dragster-run.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>

---

### Tools
Arduino (C/C++) • ADC sensing • PWM motor control • ICStation motor shield • PID-style feedback
