# \# PACE: Cost-Aware Predictive Autoscaling for Kubernetes

# 

# PACE improves on the default Kubernetes autoscaler (HPA). HPA only reacts after traffic has already increased. PACE predicts the traffic first, then decides how many pods to run by balancing performance and cost.

# 

# \## How it works

# 

# 1\. Forecast: a model (Prophet, optionally LSTM) predicts upcoming traffic.

# 2\. Decide: a cost-aware decision layer chooses the number of pods.

# 3\. Scale: the decision is applied to the Kubernetes cluster.

# 4\. Monitor: Prometheus and Grafana show the results live.

# 

# \## Folders

# 

# \- ml: forecasting model and cost-aware decision logic

# \- k8s: Kubernetes and HPA files

# \- traffic: synthetic traffic generation (k6 / Locust)

# \- monitoring: Prometheus and Grafana setup

# \- app: sample application

# \- docs: synopsis, literature survey, report

# 

# \## Team

# 

# \- Sushrita: project lead, forecasting model, cost-aware decision layer

# \- Harshitha: Kubernetes (Minikube) and HPA setup

# \- Thanishka: traffic generation, Prometheus and Grafana

# \- Bindhu: Git and GitHub

# 

# \## Git rules for everyone

# 

# 1\. Never work directly on main.

# 2\. Before you start: git checkout main, then git pull

# 3\. Make your own branch: git checkout -b feature/your-work

# 4\. Save your work: git add . then git commit -m "short message"

# 5\. Send it: git push -u origin feature/your-work

# 6\. Open a pull request on GitHub. Bindhu checks and merges it.

# 7\. Only edit files in your own folder.

# PACE: Cost-Aware Predictive Autoscaling for Kubernetes

> A mini project (3rd year, AI & Data Science) that predicts incoming traffic and decides the most cost-efficient number of Kubernetes pods to run, instead of waiting for load to spike and reacting late.

*Name shortlist: PACE (Predictive Autoscaling with Cost Efficiency), ScaleWise, SpikeGuard, Foresight. Replace `[Project Name]` once finalized.*

---

## Table of Contents
1. [Overview](#overview)
2. [Problem Statement](#problem-statement)
3. [Proposed Solution](#proposed-solution)
4. [Scope: Why "Exam Portal"?](#scope-why-exam-portal)
5. [Architecture](#architecture)
6. [Tech Stack](#tech-stack)
7. [Literature Survey](#literature-survey)
8. [Repository Structure](#repository-structure)
9. [Team and Roles](#team-and-roles)
10. [Getting Started](#getting-started)
11. [Timeline](#timeline)
12. [Evaluation Plan](#evaluation-plan)
13. [References](#references)

---

## Overview

Websites run on servers that must grow during busy periods and shrink when quiet. Kubernetes does this automatically, but only *after* it notices high load. This project builds a system that forecasts traffic ahead of time and chooses a scaling decision that balances **performance** and **cost**, taking the forecast's **confidence** into account.

**This is a general-purpose system** for any web application hosted on Kubernetes (e-commerce, food delivery, ticket booking, banking, and so on). An exam-portal traffic pattern is used only as our test and demo scenario.

---

## Problem Statement

Kubernetes' default autoscaler, the **Horizontal Pod Autoscaler (HPA)**, is *reactive*. It adds pods only after CPU or memory crosses a threshold, which leaves a lag window during sudden spikes where users see slowdowns or failures.

Recent research predicts traffic in advance so scaling can happen earlier. However, existing work focuses mainly on performance and does not resolve the trade-off between:

- **Scaling early and aggressively:** avoids slowdowns but wastes money on idle servers
- **Scaling late and conservatively:** saves money but risks poor performance during real spikes

There is no simple, explainable system that predicts traffic **and** chooses the most cost-efficient scaling response while accounting for how confident the prediction is.

---

## Proposed Solution

The system has three parts:

1. **Traffic forecasting module.** A time-series model (Facebook Prophet as the baseline, LSTM optionally for comparison) learns daily, weekly, and spike patterns and forecasts upcoming load along with a confidence interval.
2. **Cost-aware decision layer (our main contribution).** Combines the forecast, its confidence range, the current pod count, and a cost model (cost per pod per hour, pod capacity, penalty for under-provisioning) to output a target of *N pods*: the cheapest number that stays within a safe margin.
3. **Evaluation dashboard.** Prometheus and Grafana show pod count, load, response time, and cost over time for three strategies side by side:
   - Default reactive HPA (baseline)
   - Simple ML-predictive scaling (what most existing work does)
   - Our cost-aware predictive scaling

---

## Scope: Why "Exam Portal"?

Exam portals are a relatable, realistic benchmark: long quiet periods punctuated by sharp spikes (registration deadlines, result days). We use this pattern to generate synthetic traffic for testing.

**It is a test case, not a limitation.** The same system applies to any bursty web application.

**In scope:** one local Kubernetes cluster (Minikube), one simple sample web app, synthetic traffic generation, a forecasting model, the cost-aware decision layer, and a comparison dashboard.

**Out of scope (saved for later work):** GPU/LLM-serving workloads, multi-cluster or multi-region scaling, real cloud billing (we use a defined cost *model*), and production-grade security or reliability.

---

## Architecture

```
[Traffic Data] --> [Forecasting Model] --> [Cost-Aware Decision Layer] --> [Kubernetes Scaling]
                                                   ^
                                       [Cost Model + Prediction Confidence]

[Prometheus] --collects metrics--> [Grafana dashboards]  (compares all 3 strategies)
```

---

## Tech Stack

| Tool | Used for |
|---|---|
| **Docker** | Packages the sample app into a portable container |
| **Kubernetes (Minikube)** | Runs and scales the containers; runs locally on a laptop |
| **k6 / Locust** | Generates realistic synthetic traffic against the app |
| **Prometheus** | Collects metrics (CPU, pod count, response time) as time-series data |
| **Grafana** | Turns Prometheus data into live comparison dashboards |
| **Prophet (and optionally LSTM)** | Forecasts upcoming traffic |
| **Python** | Forecasting code and the custom cost-aware decision layer |

Everything is open-source and runs on a single laptop; no cloud spend is needed.

---

## Literature Survey

We reviewed recent (2025) work on predictive and cost-aware autoscaling for Kubernetes.

| Work | Approach | Limitation |
|---|---|---|
| Kubernetes HPA | Reactive, threshold-based | Slow to respond to sudden spikes |
| Abdelhamid, Aziz & Soliman (2025) | ML traffic prediction integrated with HPA via AWS EKS, SageMaker, CloudWatch | Optimizes responsiveness only; no cost optimization |
| Guruge & Priyadarshana (2025) | Prophet + LSTM hybrid forecasting, pod count via a Monitor-Analyze-Plan-Execute loop | Accurate forecasting, but the prediction is not tied to a cost-based scaling decision |
| Zhang et al. (2025), **AAPA** | Classifies workloads (SPIKE, PERIODIC, RAMP, STATIONARY) and applies uncertainty-aware scaling | Reduces SLO violations by up to 50% but with 2-8x higher resource usage in bursty conditions; the authors state that ML *"shifts rather than eliminates"* the cost-performance tradeoff |
| Punniyamoorthy et al. (2025) | SLO-driven, cost-aware autoscaling framework | **Closest related work to our angle.** Detailed differentiation from our approach is still being reviewed |
| **This project** | Confidence-aware forecast tied directly to a cost-optimal scaling decision | |

**Gap:** existing predictive autoscalers either ignore cost or acknowledge the cost-performance trade-off without resolving it. Our decision layer addresses it explicitly, in a simple and explainable way suited to a mini-project scope.

---

## Repository Structure

```
project-name/
├── app/          # sample web app + Dockerfile
├── k8s/          # Kubernetes YAML (Deployment, Service, HPA)
├── traffic/      # k6/Locust load-generation scripts
├── monitoring/   # Prometheus and Grafana configs
├── ml/           # traffic generator, forecasting, cost-aware decision layer
│   └── data/     # synthetic traffic CSV
├── docs/         # synopsis, notes, report drafts
└── README.md
```

---

## Team and Roles

| Member | Responsibility |
|---|---|
| **[Sushrita]** (Lead) | ML forecasting and the cost-aware decision layer; overall project direction |
| **[Bindhu]** | Git/GitHub management, team coordination and logistics |
| **[Harshitha]** | Kubernetes cluster and HPA setup, plus Docker containerization of the sample app |
| **[Thanishka]** | Traffic generation (k6/Locust) and monitoring (Prometheus/Grafana) |

We hold a short weekly sync (15-20 minutes) to share progress and catch blockers early.

---

## Getting Started

```bash
git clone <repo-url>
cd <repo-name>
git pull
```

**ML / forecasting (`ml/`):**
```bash
cd ml
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # Mac/Linux
pip install prophet pandas numpy matplotlib scikit-learn jupyter
python generate_traffic.py     # creates data/traffic.csv
```

**Kubernetes (`k8s/`):**
```bash
minikube start --driver=docker --cpus=2 --memory=4096
minikube addons enable metrics-server
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/hpa.yaml
kubectl get hpa -w
```

Prerequisites: Python 3.10+, Docker Desktop, kubectl, Minikube, Helm, and k6 or Locust.

**Workflow tip:** run `git pull` before starting work each day, and work inside your own folder to avoid conflicts.

---

## Timeline

| Weeks | Milestone |
|---|---|
| 1-2 | Literature survey, finalize scenario, local Kubernetes cluster and sample app |
| 3-4 | Synthetic traffic generator; baseline reactive HPA measured |
| 5-6 | Forecasting model built and trained (Prophet, then LSTM if time permits) |
| 7-8 | Cost-aware decision layer built and integrated with the cluster |
| 9-10 | Comparison experiments: reactive vs simple-ML vs cost-aware |
| 11-12 | Grafana dashboards, report, demo |

---

## Evaluation Plan

Each strategy is run against the same traffic pattern and compared on:

- **Performance:** response time and SLO violations during spikes (SLO = Service Level Objective, a measurable target such as "99% of requests under 200 ms")
- **Cost:** total pod-hours used under the defined cost model

**Success looks like:** fewer SLO violations than reactive HPA *and* lower total cost than simple ML-predictive scaling.

---

## References

1. Y. M. Abdelhamid, W. A. Aziz, J. N. Soliman, "Kubernetes Autoscaling with Machine Learning based on traffic load prediction," 2025.
2. P. B. Guruge, Y. H. P. P. Priyadarshana, "Time series forecasting-based Kubernetes autoscaling using Facebook Prophet and Long Short-Term Memory," *Frontiers in Computer Science*, vol. 7, 1509165, 2025. DOI: 10.3389/fcomp.2025.1509165
3. G. Zhang et al., "AAPA: An Archetype-Aware Predictive Autoscaler with Uncertainty Quantification for Serverless Workloads on Kubernetes," arXiv:2507.05653, 2025.
4. V. Punniyamoorthy et al., "An SLO driven and cost-aware autoscaling framework for Kubernetes," arXiv:2512.23415, 2025.

---

*Status: in progress (mini project, 3rd year AI & Data Science).*
