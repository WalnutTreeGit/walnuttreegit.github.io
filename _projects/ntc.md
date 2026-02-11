---
layout: page
title: NTC Temperature Sensing + Analog ADC Chain (ELEAPL)
description: End-to-end analog front-end for an NTC temperature sensor. Wheatstone bridge, instrumentation amplifier, comparator with hysteresis, 0–5 V conditioning, and Arduino LCD readout.
img: assets/img/eleapl-adc.png
importance: 4
category: work
---

### Overview
Designed and validated an end-to-end signal chain that converts an **analog temperature measurement** into a **digital-level signal**, using an **NTC 10 kΩ** sensor and a full analog front-end. The system conditions the sensor output into a **0–5 V range**, performs thresholding/digitization, and displays temperature on an **LCD via Arduino**. 

---

## Architecture
The project was structured as a block diagram and implemented in stages: 
1) **NTC sensor (resistive output)**  
2) **Wheatstone bridge** (convert resistance change to differential voltage)  
3) **Instrumentation amplifier (TL084)** to amplify the bridge output   
4) **Comparator** producing ±15 V depending on input, including **hysteresis** behavior   
5) **Signal conditioning** blocks to limit outputs into **0–5 V** (CS1 / CS2)   
6) **A/D stage** using a **Zener diode** to obtain a digital-level output (0 V / 5 V)   
7) **Arduino + LCD** readout of temperature 

{% include figure.liquid
  path="assets/img/eleapl-tinkercad.png"
  class="img-fluid rounded z-depth-1"
  caption="End-to-end demo in Tinkercad: NTC sensing → analog conditioning (CS1/CS2) → Arduino ADC → LCD temperature display."
%}

{% include figure.liquid
  path="assets/img/eleapl-breadboard.jpg"
  class="img-fluid rounded z-depth-1"
  caption="Physical prototype of the analog front-end on breadboard."
%}

---

## Design and validation workflow
- **Calculations in Excel**, circuit validation in **PSPICE for TI**, and Arduino behavior validated in **Tinkercad**. 
- Designed bridge and amplifier gains to map the temperature-dependent sensor behavior into a useful voltage span (targeting 0–5 V at the conditioned output). 
- Verified comparator hysteresis and switching behavior (report includes switching examples around ~24–26°C). 

---

### Tools
Excel • PSPICE for TI • Tinkercad • Arduino • TL084 op-amp • Wheatstone bridge • Comparator with hysteresis 
