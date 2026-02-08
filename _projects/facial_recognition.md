---
layout: page
title: Real-Time Facial Recognition
description: A closed-set facial recognition pipeline using YOLOv8 and Transfer Learning.
img: assets/img/face_rec.webp
importance: 1
category: work
related_publications: false
---

This project implements a complete pipeline for training a real-time facial recognition system using **YOLOv8m**. Developed as part of the *Applied Artificial Intelligence* curriculum, the system performs simultaneous localization and identification of target subjects in live video feeds. Below is a demo video, recorded on a CPU-only machine without GPU acceleration, so playback appears less smooth than real-time performance on a GPU.

<div style="width:min(900px, 96vw); margin: 1rem auto;">
  <video controls playsinline preload="metadata"
         style="width:100%; height:auto; border-radius: 12px; display:block;">
    <source src="{{ '/assets/video/facial-recognition-demo.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <p style="text-align:center; font-size: 0.95rem; margin-top: 0.5rem;">
    Demo: real-time detection + closed-set identity recognition (YOLOv8 fine-tuned head).
  </p>
</div>


### Technical Implementation
The core of the system leverages **Transfer Learning**. By freezing the initial feature extraction layers of a pre-trained YOLOv8 model and fine-tuning the detection head, the system can learn to identify new individuals with a relatively small custom dataset.

**Key Features:**
* **Architecture:** YOLOv8m (Medium) for a balance of speed and accuracy.
* **Pipeline:** Automated data collection script $\rightarrow$ Annotation (YOLO format) $\rightarrow$ Training on GPU.
* **Performance:** Capable of real-time inference on standard hardware.

### Methodology
The project addresses the challenge of distinguishing specific individuals from background subjects (Open-Set vs. Closed-Set). The training workflow utilizes:
1.  **Data Collection:** A custom OpenCV script captures raw frames of target subjects.
2.  **Annotation:** Images are labeled using PascalVOC/YOLO formats.
3.  **Training:** The model is fine-tuned over 800 epochs using Stochastic Gradient Descent (SGD).



### Code & Usage
The full repository includes the source code for the data collection pipeline and inference scripts. It is designed to allow other researchers to train their own closed-set recognition systems on their own datasets.

[View Project on GitHub](https://github.com/walnuttreegit/real-time-facial-recognition)
