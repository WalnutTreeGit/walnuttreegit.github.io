---
layout: page
title: Real-Time Facial Recognition
description: A closed-set facial recognition pipeline using YOLOv8 and Transfer Learning.
img: assets/img/face_rec.jpg # You need to upload a thumbnail image with this name!
importance: 1
category: work
related_publications: false
---

This project implements a complete pipeline for training a real-time facial recognition system using **YOLOv8m**. Developed as part of the *Applied Artificial Intelligence* curriculum, the system moves beyond simple classification to perform simultaneous localization and identification of target subjects in live video feeds.

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

<div class="row">
    <div class="col-sm- mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/yolo_architecture.jpg" title="YOLO Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The system leverages the YOLOv8 architecture for single-shot detection.
</div>

### Code & Usage
The full repository includes the source code for the data collection pipeline and inference scripts. It is designed to allow other researchers to train their own closed-set recognition systems on their own datasets.

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    {% include repository/repo.liquid repository="walnuttreegit/YOUR-REPO-NAME" %}
</div>
