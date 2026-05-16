# EV Routing with Stochastic Congestion and Discrete Partial Charging

> Joint optimisation of delivery route, speed profile, and EV charging
> decisions under time-dependent stochastic traffic — implemented as a
> finite-horizon MDP and solved with a Dueling Deep Q-Network.

**Design Lab Project — AI69002, IIT Kharagpur (April 2026)**  
**Supervisor:** Prof. Arnab Sarkar, ATDC, IIT Kharagpur &
Dipankar Mandal, Dept. of AI, IIT Kharagpur

---

## Overview

This project implements an end-to-end pipeline for energy-efficient
Electric Vehicle (EV) routing on a real urban road network. Unlike
classical EV routing formulations, this work jointly decides:

- **Which delivery node to visit next** — the route sequence is not fixed
- **What speed to travel at** on each road segment
- **Whether and where to charge** — selecting charging station and
  discrete charge level
- **How much to charge** — using a nonlinear piecewise-linear charging
  model adopted from [Mandal et al., 2025]

The problem is formalised as a **finite-horizon Markov Decision Process
(MDP)** and solved under two separate objectives:

| Mode | Objective |
|------|-----------|
| **Mode A** | Minimise total number of charging stops |
| **Mode B** | Minimise total monetary cost of charging |

---

## Key Features

- **Real road network** — 11,147-node South Los Angeles graph via OSMnx
- **Real traffic data** — 6 days of Caltrans PeMS District 7 five-minute
  speed observations (359,424 records, 208 sensors, 4,992 speed
  distributions)
- **Real charging infrastructure** — 5 stations from Kaggle EV
  Charging Station dataset, snapped to OSMnx nodes
- **Nonlinear charging model** — piecewise-linear SoC-dependent
  charging curve from Mandal et al. (2025), with stochastic queue
  waiting times
- **Load-dependent energy model** — energy consumption per km
  decreases as deliveries are made and vehicle becomes lighter
- **Abstract graph abstraction** — pre-computed shortest paths between
  11 key nodes make the RL problem tractable (~7–10 hops per episode)
- **Dueling DQN** with warm-start guided exploration, experience
  replay, and target network stabilisation
- **Interactive Folium maps** — best route and sub-optimal alternatives
  visualised on a real geographic base map
- **Animated training evolution** — 60-snapshot HTML animation showing
  how the agent's routing strategy evolves across 3,000 training episodes

---

## Results Summary

| Metric | Dijkstra A | DQN A | Dijkstra B | DQN B |
|--------|-----------|-------|-----------|-------|
| Mean Reward | 705.4 | 532.4 | 705.0 | 471.7 |
| Mean Energy (kWh) | 14.15 | 34.84 | 14.09 | 35.61 |
| Charging Cost ($) | 1.365 | 5.629 | 1.364 | 5.836 |
| Charging Stops | 1.00 | 4.40 | 1.00 | 4.48 |
| **Completion Rate** | **100%** | **82%** | **100%** | **75%** |

Best single greedy evaluation episode (exploitation only, ε=0):

| | Mode A | Mode B |
|--|--------|--------|
| Hops | 7 | 9 |
| Energy | 20.9 kWh | 32.2 kWh |
| Charging stops | **1** (minimum feasible) | **3** (AC Slow preferred) |
| Task completed | ✓ | ✓ |

---

## Project Structure
