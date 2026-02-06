---
layout: page
title: Autonomous Dragster Robot
description: "Arduino-based embedded control for a 10 m autonomous dragster: light-gate start, 5x IR sensing, PID steering, ICSta"
img: assets/img/dragster.png
importance: 7
category: work
---

### Overview
Built an autonomous dragster to complete a **10 m sprint** at high speed and **stop at the finish marker** using onboard sensing and real-time control.

---

## Hardware / sensing / actuation
- **Start gate:** The car starts once a light is turned on, read by an LDR on `A0` that checks the light measurement  threshold.
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

## Demo video
<div style="max-width: 420px; margin: 0 auto;">
  <video controls playsinline preload="metadata"
         style="width:100%; height:auto; aspect-ratio: 9 / 16; display:block; border-radius: 12px;">
    <source src="{{ '/assets/video/dragster-run.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support the video tag.
  </video>
</div>

---

### Tools
Arduino (C/C++) • ADC sensing • PWM motor control • ICStation motor shield • PID-style feedback
