---
layout: page
title: Embedded Tetris Table
description: Real-time game logic on 8-bit hardware with 2KB RAM constraints.
img: assets/img/Typical_Tetris_Game.svg.png
importance: 2
category: work
related_publications: false
---

This project is a full-stack embedded system designed to run the Tetris game logic on highly constrained hardware. Developed from scratch using **C** on an **ATmega328P**, the system drives a **300-pixel** array of addressable RGB LEDs (WS2812B) while handling asynchronous input from a custom Android application.

The physical build consists of an **80cm x 50cm** table containing a primary **12x20 game matrix** and a secondary **5x4 status matrix** for score and "next piece" visualization. Below is a video recorded during the prototyping phase.

### Video Demo
<div class="row">
    <div class="col-sm- mt-3 mt-md-0">
        <video width="100%" controls autoplay loop muted playsinline class="img-fluid rounded z-depth-1">
            <source src="{{ '/assets/video/tetris.mp4' | relative_url }}" type="video/mp4">
            Your browser does not support the video tag.
        </video>
    </div>
</div>
<div class="caption">
    Demonstration of the LED collision detection and gameplay.
</div>

### Hardware-Software Sync
The WS2812B LEDs require a strict timing protocol (800kHz) where a logic `1` or `0` is defined by pulse width variations in the nanosecond range. Standard interrupts (from the **HC-06 Bluetooth module**) can disrupt this timing, causing display glitches.

To solve this, the firmware uses a **state machine** approach:
1.  **Input Phase:** The CPU accepts UART interrupts from the Bluetooth module.
2.  **Logic Phase:** Collision checks and piece rotation are calculated.
3.  **Render Phase:** The 2D game grid (X, Y) is serialized into a 1D data stream to match the physical "zig-zag" layout of the LED strip, allowing the entire surface to be updated via a single GPIO pin.
  
### The Challenge: 2KB RAM
The primary engineering challenge was memory management. The ATmega328P offers only 2048 bytes of SRAM.
* A standard integer array for the grid would consume too much memory.
* The LED frame buffer alone requires 900 bytes (300 LEDs $\times$ 3 colors).

**Solution:** I implemented the collision grid using bitwise manipulation, effectively storing the game state in a fraction of the memory. The rendering pipeline was optimized to write directly to the LED output stream, minimizing the need for intermediate buffers.

### Project Details
* **Hardware:** ATmega328P, WS2812B (300 LEDs), HC-06 Bluetooth
* **Dimensions:** 80cm x 50cm

[View Code on GitHub](https://github.com/WalnutTreeGit/Tetris)
