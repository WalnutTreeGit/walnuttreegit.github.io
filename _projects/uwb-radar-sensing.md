---
layout: page
title: UWB Radar Sensing — Movement Tracking & Breath Monitoring
description: IR-UWB radar signal processing for motion tracking (MATLAB) and non-contact respiration monitoring (Python).
img: assets/img/projects/uwb-radar.webp
importance: 6
category: work
---

### Overview
Implemented two IR-UWB radar processing pipelines:
1) **Movement tracking** via slow-time clutter suppression + envelope extraction and range–time visualization.
2) **Breathing monitoring** via Hilbert-envelope extraction and variance-based range-bin selection, with time/frequency checks.

(Work summarized in course deck.)

---

## 1) Movement tracking (MATLAB)
**Setup.** Walking within ~1–3 m; radar configured to a 3 m range window.

**Pipeline.**
- **Stationary reflection reduction:** frame differencing  
  \[
  s'(t)=s(t)-s(t-1)
  \]
- **Envelope extraction:** analytic signal magnitude using Hilbert transform.
- **Range–time plotting:** convert fast-time bins to range using  
  \(c=3\cdot10^8\) m/s, \(f_s=23.328\) GHz; slow-time via \(PRF=30\) Hz.
- **Heuristic enhancement:** per-frame thresholding (mean + 2.33·std) to suppress high-amplitude outliers for visualization.

**Results.**

{% include figure.liquid
  path="assets/img/projects/uwb/tracking_before.png"
  class="img-fluid rounded z-depth-1"
  caption="Range–time visualization after stationary reflection reduction (frame differencing) and Hilbert-envelope extraction."
%}

{% include figure.liquid
  path="assets/img/projects/uwb/tracking_after.png"
  class="img-fluid rounded z-depth-1"
  caption="Range–time visualization after per-frame outlier suppression (threshold = mean + 2.33·std) to increase motion contrast."
%}

---

## 2) Breath monitoring (Python)
**Setup.** Subject at ~2 m breathing normally; radar configured to 3 m.

**Method.**
- Compute Hilbert **envelope** per slow-time frame.
- Select the most respiration-informative **range bin** by maximizing envelope **variance** across time.
- Extract the breathing waveform from that bin and validate in time and frequency.

**Result.**

{% include figure.liquid
  path="assets/img/projects/uwb/breath_waveform_col333.png"
  class="img-fluid rounded z-depth-1"
  caption="Extracted respiration waveform from the variance-selected range bin (Hilbert-envelope + variance-based bin selection). Example shown: column 333."
%}

---

### Tools
MATLAB • Python (NumPy/SciPy/Matplotlib/Pandas)
