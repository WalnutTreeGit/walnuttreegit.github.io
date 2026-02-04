---
layout: page
title: SMT(LRA) Constraint Learning from Labelled Examples
description: Solver-backed learning of k-CNF formulas mixing Boolean literals and linear real arithmetic halfspaces (PySMT).
img: assets/img/smt-front.png
importance: 4
category: work
---

### Overview
Implemented a solver-backed learner for **SMT(LRA) k-CNF formulas** from labelled examples (feasible vs infeasible). The learner searches for a CNF where each clause is a disjunction of:
- Boolean literals (e.g., `extended`, `¬extended`)
- Linear inequalities over real variables (halfspaces)

The problem is encoded as SMT constraints, solved, and then the satisfying model is converted into a readable formula.

---

## Problem setting
Input: labelled examples over
- Boolean variables
- Real variables

Goal: learn a **k-clause CNF**, each clause containing up to **h halfspaces**, such that:
- all feasible examples satisfy the learned formula
- all infeasible examples violate at least one clause

---

## SMT encoding (high-level)
The encoding introduces decision variables for:
- Halfspace coefficients and offsets (to define inequalities)
- Inclusion flags for whether a literal / halfspace (or its negation) appears in a clause

Constraints enforce:
1) Whether each example satisfies each halfspace  
2) Whether each example satisfies each clause (OR over enabled literals)  
3) Whether each example satisfies the full CNF (AND over clauses), matching the label

---

## Practical constraints (to avoid degenerate models)
Added constraints to improve interpretability and avoid trivial solutions, including:
- No contradictory literals in the same clause (no `v` and `¬v`; no halfspace and its negation)
- At least one non-zero coefficient per halfspace (avoid “0 ≤ b” junk)
- Bounded coefficients/offsets to keep learned rules stable and readable

---

## Model extraction
After solving, the satisfying model is parsed into:
- explicit halfspace strings (e.g., `-battery + -distance <= -22`)
- clause strings joined with `∨`
- full CNF joined with `∧`

Basic cleanup is applied to reduce trivial/duplicate pieces when possible.

---

## Results
Evaluated by sweeping small values of `k` and `h` (e.g., 1–3). Correctness is judged by whether the learned formula matches all feasible/infeasible labels; redundancy can occur because multiple equivalent CNFs satisfy the constraints.

{% include figure.liquid
  path="assets/img/smt-table.png"
  class="img-fluid rounded z-depth-1"
  caption="Results table: learned SMT(LRA) k-CNF formulas across different (k, h) settings."
%}

---

### Tools
Python • PySMT • SMT solver backend
