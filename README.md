# CauseLens

**A white-box Bayesian diagnosis engine that explains *why* retail profits decline.**

Most dashboards tell you *what* happened: sales dipped, margins shrank. CauseLens tells you *what probably caused it*. It combines a hand-designed Bayesian Belief Network, statistical evidence detection, and automated data labelling to rank the most likely root causes behind a profit decline, and every step of the reasoning can be inspected.

---

## Highlights

- **Interactive causal graph**: a live Bayesian network where node colour reflects the probability of under-performance and edge thickness reflects causal impact.
- **Root-cause diagnosis**: the most probable culprits behind a profit drop, with an impact score for each.
- **Market state inference**: the most likely configuration of external factors (competition, economy, season).
- **Data Maturity Mechanism**: evidence is unlocked gradually as the month progresses, so the model never over-reacts to thin data.
- **Automated labelling**: historical records are labelled using statistics and business rules, with no manual annotation.
- **Training controls**: managers can weight historical periods to adapt the model to their business.

---

## How It Works

```
Historical store data (SQLite)
          │
          ▼
Automated labelling        (Label.py)
          │
          ▼
Conditional probability learning
          │
          ▼
Bayesian Belief Network    (Engine.py, pgmpy)
          │
          ▼
Daily evidence detection   (Detection.py)
          │
          ▼
Causal diagnosis + interactive dashboard   (Dashboard.py)
```

### Bayesian variables

Sales, Profit, Pricing, Costs, Stocking, Product Popularity, Competition, Operational Efficiency, Economy and Season.

The dependencies between them were designed by hand from real-world business logic, which keeps the model interpretable and auditable.

### Data Maturity Mechanism

Not every variable can be trusted on day one of a month. CauseLens releases each piece of evidence only once there is enough data behind it:

| Variable | Evidence available after |
|---|---|
| Pricing | Day 1 |
| Season | Day 1 |
| Sales | ~7 days |
| Product Popularity | ~10 days |
| Operational Efficiency | Right after an incident, or after enough observation |

---

## Dashboard Pages

| Route | Purpose |
|---|---|
| `/` | Live causal graph with day selector and root-cause summary |
| `/monthly` | Enter end-of-month evidence and get a full diagnosis |
| `/market` | Most probable market state (competition, economy, season) |
| `/training` | Adjust how historical periods are weighted during training |

---

## Getting Started

**Requirements:** Python 3.9+

```bash
# 1. Install dependencies
pip install -r requirements.txt
pip install flask

# 2. (Optional) Regenerate labelled training data from the database
python Label.py

# 3. Launch the dashboard
python Dashboard.py
```

Then open <http://localhost:5000>.

If `model.pkl` is missing, the engine rebuilds the network's probability tables from `Retail_Data.db` on startup.

---

## Project Structure

```
CauseLens/
├── Dashboard.py     # Flask app and UI (graph, monthly, market, training)
├── Engine.py        # Bayesian network, inference, impact and diagnosis logic
├── Detection.py     # Statistical evidence detection from daily data
├── Label.py         # Automated labelling of historical data
├── Retail_Data.db   # SQLite database (business, product and labelled data)
├── model.pkl        # Saved trained model
├── graph.html       # Generated interactive graph (PyVis)
└── requirements.txt
```

## Tech Stack

Python, Flask, SQLite, Pandas, pgmpy, SciPy, PyVis

---

## Roadmap

- Decision simulation engine
- Automatic corrective suggestions
- Temporal Bayesian networks
- Dynamic structure learning
- Live POS system integration
- Reinforcement learning for strategy evaluation

---

## Philosophy

CauseLens is deliberately a **white-box** system. Every inference can be traced, questioned and verified by the people who run the business. The goal is not only to predict outcomes, but to explain them.

## Disclaimer

Built as a proof of concept for the **Walmart Converge Hackathon**. The current version runs on simulated, statistically generated data. The architecture is designed to work with real retail datasets with minimal changes.
