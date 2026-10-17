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

