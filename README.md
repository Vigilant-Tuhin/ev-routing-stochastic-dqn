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

---

## Data Sources

| Dataset | Source | Usage |
|---------|--------|-------|
| Road network | [OpenStreetMap](https://www.openstreetmap.org) via [OSMnx](https://github.com/gboeing/osmnx) | Graph structure, edge lengths, path computation |
| Traffic speeds | [Caltrans PeMS District 7](https://pems.dot.ca.gov) — 5-min station data | Time-dependent stochastic speed distributions |
| EV charging stations | [Kaggle — EV Charging Station Availability Tracking](https://www.kaggle.com/datasets/likithagedipudi/ev-charging-station-availability-tracking) | Station locations, charger types |
| Charging model | [Mandal et al., 2025](https://doi.org/10.1145/3762188) | Nonlinear piecewise-linear SoC charging curve |

---

## Installation

```bash
git clone https://github.com/YOUR_USERNAME/ev-routing-stochastic-dqn.git
cd ev-routing-stochastic-dqn
pip install -r requirements.txt
```

**requirements.txt:**

osmnx>=2.1.0
networkx>=3.0
numpy>=1.24
pandas>=2.0
matplotlib>=3.7
torch>=2.0
scipy>=1.10
geopandas>=0.13
shapely>=2.0
tqdm>=4.65
folium>=0.14

---

## Usage

Place the following data files in the same directory as the script:

d07_text_station_5min_2025_01_01.txt.gz   # PeMS 5-minute data (6 files)
d07_text_station_5min_2025_01_15.txt.gz
d07_text_station_5min_2025_04_19.txt.gz
d07_text_station_5min_2025_07_04.txt.gz
d07_text_station_5min_2025_09_08.txt.gz
d07_text_station_5min_2025_11_26.txt.gz
d07_text_meta_2025.txt                    # PeMS station metadata
ev_charging_station_availability.csv      # Kaggle charging stations

Then run:

```bash
python temp3-i.py
```

The script runs sequentially through all steps. The OSMnx graph is
cached after the first download — subsequent runs load it instantly.

**Expected runtime:** ~45–90 minutes for full 3,000-episode training
on CPU. With CUDA GPU, approximately 20–35 minutes.

---

## Relation to Mandal et al. (2025)

This project directly extends the **FRVCP-DPC** framework of:

> Mandal, D., Sarkar, A., and Mondal, A. (2025). *A Discrete Partial
> Charging Enabled Dynamic Programming Strategy for Optimal Fixed-Route
> Electric Vehicle Charging.* ACM Transactions on Embedded Computing
> Systems, 24(5s), Article 154.
> https://doi.org/10.1145/3762188

| Dimension | Mandal et al. (BEFRG) | This Work |
|-----------|----------------------|-----------|
| Route | Fixed input | **Decision variable** |
| Speed | Fixed constant | **Controlled + stochastic** |
| Traffic | Deterministic/instantaneous | **Time-indexed distributions** |
| Objective | Minimise route time | **Minimise charges / cost** |
| Method | Optimal DP | **Approximate RL** |

The nonlinear charging model, battery discretisation scheme, and
load-dependent energy formula are adopted directly from their work.

---

## Academic Report

The full academic report is available as:
- `EV_Routing_Paper.tex` — LaTeX source (IEEEtran two-column format)
- `EV_Routing_Report_2.docx` — Word version

---

## License

This project is submitted as academic coursework for AI69002 Design
Laboratory I, IIT Kharagpur. Code is released under the MIT License
for research and educational use.

---

## Citation

If you use this code or report in your work, please cite:

```bibtex
@misc{manik2026evroutingdqn,
  author    = {Biswas Tuhin Manik},
  title     = {Energy-Efficient {EV} Routing with Stochastic Congestion
               and Discrete Partial Charging},
  year      = {2026},
  publisher = {GitHub},
  url       = {https://github.com/YOUR_USERNAME/ev-routing-stochastic-dqn},
  note      = {Design Lab Project, AI69002, IIT Kharagpur}
}
```

and the primary reference this work extends:

```bibtex
@article{mandal2025,
  author  = {Mandal, Dipankar and Sarkar, Arnab and Mondal, Arijit},
  title   = {A Discrete Partial Charging Enabled Dynamic Programming
             Strategy for Optimal Fixed-Route {Electric Vehicle} Charging},
  journal = {ACM Transactions on Embedded Computing Systems},
  volume  = {24},
  number  = {5s},
  pages   = {Article 154},
  year    = {2025},
  doi     = {10.1145/3762188}
}
```
