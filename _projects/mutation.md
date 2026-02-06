---
layout: page
title: ML for Mutation Testing
description: Clustering mutants using structural code features (K-Means / DBSCAN / HDBSCAN) and verifying equivalence with FSM-derived test suites (DS & W).
img: assets/img/mutation-results.png
importance: 4
category: work
---

### Overview
Equivalent mutants inflate mutation testing cost without improving test quality. This project reduces redundancy by:
1) generating mutants,
2) extracting structural features,
3) clustering similar mutants,
4) verifying equivalence within clusters using FSM-derived test suites,
5) pruning to representative mutants.

---

## Pipeline
### 1) Mutant generation
Generated mutants (via MutPy) for an FSM-based flight booking system and extracted them for downstream analysis.

### 2) Feature extraction
Computed structural metrics per mutant (e.g., LOC, function calls, conditionals, operator counts, complexity-oriented signals).

### 3) Clustering
Clustered mutants based on extracted features using:
- **K-Means**
- **DBSCAN**
- **HDBSCAN**

### 4) Equivalence verification
Checked equivalent mutants using **FSM-based test-suite generation**:
- **DS method**
- **W method**

### 5) Mutant pruning
Selected representative mutants per cluster to reduce total executions while retaining fault-detection utility.

---

## Key observations
- Clustering alone only partially groups equivalent mutants; clusters often contain a mix of equivalent and non-equivalent mutants.
- FSM-derived test suites provide a strong basis for verifying equivalence within clusters and exposing where clustering fails.

{% include figure.liquid
  path="assets/img/mutation-results.png"
  class="img-fluid rounded z-depth-1"
  caption="Mutation reduction workflow: feature extraction → clustering → equivalence checking (DS/W) → pruning."
%}

---

### Tools
Python • MutPy • scikit-learn (K-Means, DBSCAN) • HDBSCAN • FSM test-suite generation (DS / W)


[View Code on GitHub](https://github.com/WalnutTreeGit/mutantReduction)

