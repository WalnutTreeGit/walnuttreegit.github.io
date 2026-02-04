---
layout: page
title: Middleware & Digital Twin for Collision Avoidance
description: C++ “physical car” simulation + MQTT pub/sub to maintain server-side digital twins and issue collision-avoidance commands in real time.
img: assets/img/projects/digital-twin.webp
importance: 5
category: work
---

### Overview
Built a publish/subscribe **middleware + simulation framework** for traffic monitoring using **digital twin** principles: simulated cars publish state updates, a monitoring server maintains real-time replicas, predicts collision risk, and sends avoidance commands back to the cars. 

**Backend (cars/simulation):** https://github.com/vvquocanh/car-simulation-digital-twin  
**Visualization / server demo (Unity, teammate-owned):** https://github.com/vvquocanh/unity-digital-twin

---

## System architecture
Three main components: **(1) Physical objects (cars), (2) MQTT brokers, (3) Monitoring server**. Cars act as both publishers and subscribers. 

- **Broker layer:** MQTT topic-based pub/sub using an **EMQX broker cluster** (3 nodes via docker-compose) for throughput and resilience. 
- **Monitoring server:** maintains digital twins and runs collision detection; publishes commands back to cars. 

---

## Communication procedure
1) **Registration:** car publishes static specs (size/shape/color) + entry point.   
2) **Confirmation:** server creates the digital twin and returns initial position/direction (coordinate-system alignment).   
3) **Moving loop:** car updates position/velocity/direction in real time; server updates the twin, checks collision risk, and sends commands if needed until the car exits (then the twin is deleted).   

---

## Collision detection (server-side)
Collision detection predicts each vehicle’s **future motion vector** over a “safe time” horizon derived from current velocity, then checks **intersection between motion vectors** to flag collision potential. 

To avoid naive all-pairs scaling, the environment is partitioned into **sub-regions**, each handled by a replica of the collision service in parallel, checking only cars within its region. 

---

## Performance / networking decisions
- Cars publish **registration/spec data with QoS 2** (reliability).   
- Cars publish **motion updates with QoS 0** (latency), with an update cap of **60 Hz per car** to reduce broker load.   
- Server commands use **QoS 2** since instructions are safety-critical.   
- Cars can be simulated concurrently using **multiprocessing**, and the car simulation is containerized with **Docker**. :contentReference[oaicite:13]{index=13}  

---

## Results / demo images
{% include figure.liquid
  path="assets/img/projects/digital-twin/architecture.png"
  class="img-fluid rounded z-depth-1"
  caption="Architecture: cars publish/subscribe via an EMQX MQTT cluster; monitoring server maintains digital twins and sends avoidance commands."
%}

{% include figure.liquid
  path="assets/img/projects/digital-twin/demo.png"
  class="img-fluid rounded z-depth-1"
  caption="Demo snapshot: server-side digital twins updated in real time from car telemetry; commands issued back to cars when collision risk is detected."
%}

---

### Tools
C++ • MQTT • EMQX • Docker • docker-compose 
