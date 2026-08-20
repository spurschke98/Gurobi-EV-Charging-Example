# Building an EV Charging Network with Mathematical Optimization

A hands-on `gurobipy` walkthrough that builds an EV charging deployment model from scratch — starting with a single site that could be solvde by hand, and growing it into a multi-site problem where optimization becomes the only practical approach.

This example can be run locally with the included data files or can be accessed via google colab at the link below.

https://colab.research.google.com/github/spurschke98/Gurobi-EV-Charging-Example/blob/main/ev-charging-stations.ipynb

## The Problem

You are deploying EV charging stations for a municipal parking authority, and must decide **how many chargers of each type to install, and where**. Every deployment is constrained by four limited resources: **budget, electrical capacity, physical space, and equipment units**. Faster chargers serve far more vehicles but consume disproportionately more of each, making for an interesting trade-off.

| Charger | Vehicles/day | Install cost | Fixed cost | Power (kW) | Space (m²) | Equipment |
|---|---|---|---|---|---|---|
| Level 1 (120V) | 4 | $2,000 | $5,000 | 1.4 | 12 | 0 |
| Level 2 (240V) | 12 | $6,000 | $12,000 | 7.2 | 12 | 1 |
| DC Fast 50kW | 48 | $18,000 | $25,000 | 50 | 18 | 2 |
| DC Fast 150kW | 72 | $40,000 | $40,000 | 150 | 20 | 4 |
| DC Ultra-Fast 350kW | 127 | $90,000 | $70,000 | 350 | 25 | 6 |

## 1. Demonstrating `gurobipy`

Every model component appears as algebra first and then as code, so you can see how closely `gurobipy` syntax tracks the mathematical notation. Key steps are then rewritten several ways to show how compactly the API expresses the same math. For example, the objective starts written out term-by-term, becomes a `gp.quicksum()` over charger types, and finally collapses into a single `x.prod()` call.

## 2. Why Optimization Matters as Problems Grow

**Part 1 — one site.** Five decision variables, four constraints. A greedy heuristic ("buy the best vehicles-per-dollar chargers until the money runs out") would find the right answer, so the optimization machinery isn't earning its keep yet. The purpose here is to learn the API and anatomy of the model on a problem that can be verified by hand.

**Part 2 — five candidate sites around Boston.** The same problem becomes 25 integer variables, and resources split in two: budget and equipment are **shared across all sites**, while power and space are **specific to each site**. Greedy breaks down immediately, because the best charger for one site now depends on what you installed everywhere else.

From there the notebook keeps changing the rules the way a real stakeholder would. Changes like implementing per-site demand, minimizing cost instead of maximizing vehicles served, a 60% fast-charger service floor, minimum charger counts, fixed permitting costs, then two competing objectives, are each a few lines rather than a full code overhaul. Along the way it surfaces ideas that only appear at scale: **non-binding constraints** (a rule that doesn't change the solution, and why), **infeasibility** (demanding 3,000 vehicles/day when the resources can't support it), and how switching the objective flips which constraints actually matter.

## Contents

- [ev-charging-stations.ipynb](ev-charging-stations.ipynb) — the full walkthrough
- [data_files/](data_files/) — inputs for the multi-site model: charger economics ([chargers_data.csv](data_files/chargers_data.csv)), resource consumption per charger ([requirements.csv](data_files/requirements.csv)), shared budget and equipment ([global_resources_available.csv](data_files/global_resources_available.csv)), and the five candidate sites with their power, space, demand, and coordinates ([sites.csv](data_files/sites.csv))

## Running It

**Colab** — use the link above; no install needed. At the data-loading section, run the Colab cell, which reads the CSVs from this repo over HTTPS.

**Locally** — open the notebook from the repository root so the relative `data_files/` paths resolve, and run the local data-loading cell instead. Dependencies install in the first cell (`pandas`, `gurobipy`, `plotly`, `nbformat`); `plotly` and `nbformat` are only used to map the sites.

These models are small enough for the size-limited license included with `pip install gurobipy`, so no separate license is needed.
