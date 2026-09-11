# Project 09: Deploying Private Local AI Agent on Kubernetes using Ollama, K-Agent, and Model Context Protocol (MCP)

[![Module: Agentic AI & Cloud Native](https://img.shields.io/badge/Module-Agentic%20AI%20%26%20Cloud--Native-blue?style=for-the-badge&logo=kubernetes)](https://kubernetes.io/)
[![Cloud: Google Cloud Platform (GKE)](https://img.shields.io/badge/Cloud-Google%20Cloud%20GKE-4285F4?style=for-the-badge&logo=googlecloud)](https://cloud.google.com/kubernetes-engine)
[![Engine: Ollama & Gemma](https://img.shields.io/badge/Engine-Ollama%20%7C%20Local%20LLM-black?style=for-the-badge&logo=ollama)](https://ollama.ai/)
[![Framework: K-Agent (kagent)](https://img.shields.io/badge/Framework-K--Agent%20Agentic%20AI-6C5CE7?style=for-the-badge)](https://github.com/)
[![Protocol: Model Context Protocol (MCP)](https://img.shields.io/badge/Protocol-Model%20Context%20Protocol%20(MCP)-green?style=for-the-badge)](https://modelcontextprotocol.io/)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-orange?style=for-the-badge)](#)

---

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Why Run AI Agents Locally? (Privacy, Cost & MCP)](#2-why-run-ai-agents-locally-privacy-cost--mcp)
3. [Architecture & Technology Stack](#3-architecture--technology-stack)
4. [Hardware & Quota Requirements](#4-hardware--quota-requirements)
5. [Prerequisites](#5-prerequisites)
6. [Step-by-Step Implementation Guide](#6-step-by-step-implementation-guide)
   - [Step 1: GCP Cloud Shell Environment Setup](#step-1-gcp-cloud-shell-environment-setup)
   - [Step 2: Clone the Project Automation Repository](#step-2-clone-the-project-automation-repository)
   - [Step 3: Provision High-Memory GKE Zonal Cluster](#step-3-provision-high-memory-gke-zonal-cluster)
   - [Step 4: Authenticate Kubectl with GKE](#step-4-authenticate-kubectl-with-gke)
   - [Step 5: Deploy Local Ollama LLM Container](#step-5-deploy-local-ollama-llm-container)
   - [Step 6: Pull the Gemma LLM Weights into Ollama](#step-6-pull-the-gemma-llm-weights-into-ollama)
   - [Step 7: Deploy K-Agent Custom Resource Definitions (CRDs) and Controller](#step-7-deploy-k-agent-custom-resource-definitions-crds-and-controller)
   - [Step 8: Configure Kubernetes Secret for Local Endpoint Redirection](#step-8-configure-kubernetes-secret-for-local-endpoint-redirection)
   - [Step 9: Launch and Access K-Agent Web Dashboard via Port Forwarding](#step-9-launch-and-access-k-agent-web-dashboard-via-port-forwarding)
   - [Step 10: Create and Configure Custom DevOps AI Agent](#step-10-create-and-configure-custom-devops-ai-agent)
   - [Step 11: Live Cluster Testing & Natural Language Interactions](#step-11-live-cluster-testing--natural-language-interactions)
7. [Troubleshooting & Common Pitfalls](#7-troubleshooting--common-pitfalls)
8. [Cluster Teardown & Cost Cleanup](#8-cluster-teardown--cost-cleanup)
9. [Key Takeaways & Resume Bullet Points](#9-key-takeaways--resume-bullet-points)

---

## 1. Project Overview

Modern enterprises face strict regulatory compliance regarding data privacy and IP security. Public cloud LLMs (such as OpenAI ChatGPT, Anthropic Claude, or Google Gemini) require sending internal logs, codebases, and production telemetry over the public internet to third-party endpoints. For financial, healthcare, and defence sectors, this presents critical risks of leaking Personally Identifiable Information (PII) and intellectual property.

In this project, you will build and deploy a completely self-hosted, private **Autonomous DevOps AI Agent** directly inside a **Google Kubernetes Engine (GKE)** cluster. By coupling **Ollama** (local offline LLM engine running Google Gemma models) with **K-Agent** (`kagent`, a Cloud-Native Agentic AI platform built on top of the **Model Context Protocol / MCP**), your agent can inspect pods, diagnose cluster health, and answer infrastructure inquiries directly within your secure VPC boundary without sending a single byte to external AI vendors.

---

## 2. Why Run AI Agents Locally? (Privacy, Cost & MCP)

### Key Enterprise Advantages
* **100% Data Confidentiality (No PII Leakage):** Telemetry, cluster manifests, container logs, and internal credentials remain strictly confined inside your private Kubernetes namespace.
* **Zero Token & Inference Costs:** Eliminates API per-token billing charges. Inference runs on your allocated cluster compute instances.
* **Standardized Tool Integration via MCP:** Uses the **Model Context Protocol (MCP)**, an open architectural standard designed to connect AI applications to data repositories, context providers, and CLI tools securely.
* **Offline / Air-Gapped Resiliency:** Works seamlessly in isolated, air-gapped environments without dependency on external third-party API availability.

---

## 3. Architecture & Technology Stack

* **Cloud Provider:** Google Cloud Platform (GCP)
* **Compute Engine:** Google Kubernetes Engine (GKE) single-zone managed cluster
* **Machine Type:** `e2-standard-16` (16 vCPUs, 64 GB RAM) for model hosting and agent execution
* **Local LLM Engine:** Ollama (serving Google Gemma 4B/9B parameter models locally)
* **Agentic Framework:** K-Agent (`kagent`) Kubernetes Operator and UI Dashboard
* **Protocol Standard:** Model Context Protocol (MCP)
* **Package Management & Deployment:** Helm 3 & `kubectl`
* **Network Access:** Secure Kubernetes Port Forwarding over GCP Cloud Shell Web Preview

---

## 4. Hardware & Quota Requirements

Running parameter-heavy Large Language Models (LLMs) requires substantial system memory and compute. Ensure your GCP account has the following allocations:

| Resource Requirement | Specification | Justification |
| :--- | :--- | :--- |
| **vCPU Quota** | Minimum 16 vCPUs (`CPUS_ALL_REGIONS`) | Necessary for compute nodes running quantized model inference |
| **RAM** | 64 GB Memory | Gemma/Llama weights load into RAM (`e2-standard-16`) |
| **Storage** | 100 GB SSD persistent disk | Model storage image layers and container volumes |
| **Target Zone** | `us-central1-a` (or `asia-south1-a`) | Region with available quota and lowest operational cost |

> **Note:** If using a free-tier or trial GCP account with default 8 vCPU quota restrictions, request a quota increase for `CPUS_ALL_REGIONS` to 16, or switch to an active billing account.

---

## 5. Prerequisites

Before executing commands, verify that you have:
1. An active Google Cloud Platform (GCP) account with an attached billing profile.
2. Direct access to **GCP Cloud Shell** (which comes pre-installed with `gcloud`, `kubectl`, `git`, and `helm`).
3. Sufficient GCP region quota for `e2-standard-16` machine instances in your selected zone.

---

## 6. Step-by-Step Implementation Guide

### Step 1: GCP Cloud Shell Environment Setup

1. Open your browser and navigate to the [Google Cloud Console](https://console.cloud.google.com/).
2. Select your active project from the project dropdown at the top navigation bar.
3. Click the **Activate Cloud Shell** icon (`>_`) in the top right corner.
4. Verify project binding and authenticated account identity:

```bash
gcloud config get-value project
gcloud auth list
```

---

### Step 2: Clone the Project Automation Repository

Clone the project repository containing the automation scripts, GKE cluster definitions, and deployment manifests:

```bash
# Clone the repository
git clone https://github.com/trainwithshubham/kagent.git

# Navigate into the project folder
cd kagent

# List directory contents to inspect shell scripts and manifests
ls -la
```

---

### Step 3: Provision High-Memory GKE Zonal Cluster

The repository includes a ready-to-run cluster provisioning script `zonalcluster.sh`. 

1. Inspect the script content:
```bash
cat zonalcluster.sh
```

The script runs the following underlying `gcloud` provisioning command:
```bash
gcloud container clusters create llm-zonal-cluster \
    --zone us-central1-a \
    --machine-type e2-standard-16 \
    --num-nodes 1 \
    --disk-size 100 \
    --disk-type pd-standard \
    --enable-ip-alias
```

2. Add executable permissions and execute the script:
```bash
chmod +x zonalcluster.sh
./zonalcluster.sh
```

3. Wait approximately 3 to 5 minutes for GCP to provision the master control plane and worker node pool. Once complete, the output displays the cluster endpoint and operational status:
```text
NAME: llm-zonal-cluster
LOCATION: us-central1-a
MASTER_VERSION: 1.30.x-gke.xxxx
MASTER_IP: 34.xxx.xxx.xxx
MACHINE_TYPE: e2-standard-16
NUM_NODES: 1
STATUS: RUNNING
```

---

### Step 4: Authenticate Kubectl with GKE

Ensure your local Cloud Shell CLI is properly configured to communicate with the newly created GKE cluster:

```bash
# Fetch cluster access credentials
gcloud container clusters get-credentials llm-zonal-cluster --zone us-central1-a

# Verify cluster connectivity and inspect the node state
kubectl get nodes -o wide
```

Output should confirm that node status is `Ready` with the `e2-standard-16` instance profile.

---

### Step 5: Deploy Local Ollama LLM Container

Ollama runs as an isolated container in your cluster, acting as a local HTTP API server for LLM inference.

1. Deploy the Ollama container and expose it within the cluster using the bundled deployment script or apply directly:

```bash
kubectl create namespace kagent || true

# Deploy Ollama pod and internal ClusterIP service
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ollama
  namespace: kagent
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ollama
  template:
    metadata:
      labels:
        app: ollama
    spec:
      containers:
      - name: ollama
        image: ollama/ollama:latest
        ports:
        - containerPort: 11434
        resources:
          requests:
            memory: "16Gi"
            cpu: "8"
          limits:
            memory: "32Gi"
            cpu: "14"
---
apiVersion: v1
kind: Service
metadata:
  name: ollama
  namespace: kagent
spec:
  selector:
    app: ollama
  ports:
  - port: 11434
    targetPort: 11434
EOF
```

2. Confirm the Ollama pod is running:
```bash
kubectl get pods -n kagent -l app=ollama -w
```

---

### Step 6: Pull the Gemma LLM Weights into Ollama

Once the Ollama container is up and running, trigger an in-container model pull to download the open-source Gemma LLM weights (~9.6 GB):

```bash
# Obtain the running Ollama pod name
OLLAMA_POD=$(kubectl get pods -n kagent -l app=ollama -o jsonpath='{.items[0].metadata.name}')
echo "Ollama Pod: $OLLAMA_POD"

# Pull the model weights inside the container
kubectl exec -it -n kagent $OLLAMA_POD -- ollama pull gemma:latest

# Verify the model is cached and ready for inference
kubectl exec -it -n kagent $OLLAMA_POD -- ollama list
```

---

### Step 7: Deploy K-Agent Custom Resource Definitions (CRDs) and Controller

Install the K-Agent controller and Custom Resource Definitions (CRDs) using Helm:

1. Add and update the K-Agent Helm repository:
```bash
helm repo add kagent https://charts.kagent.dev || true
helm repo update
```

2. Install K-Agent into the `kagent` namespace:
```bash
helm upgrade --install kagent-operator kagent/kagent \
  --namespace kagent \
  --set ollama.enabled=false \
  --set ollama.endpoint="http://ollama.kagent.svc.cluster.local:11434"
```

3. Verify that all K-Agent pods (controller, UI, and MCP server agents) transition to the `Running` state:
```bash
kubectl get pods -n kagent
```

---

### Step 8: Configure Kubernetes Secret for Local Endpoint Redirection

K-Agent expects an authentication secret format compatible with standard AI provider specifications. Since you are routing inference exclusively to your local internal Ollama instance, create a dummy OpenAI-compatible secret:

```bash
kubectl create secret generic kagent-openai-secret \
  --from-literal=OPENAI_API_KEY=ollama-local-dummy-key \
  --namespace kagent \
  --dry-run=client -o yaml | kubectl apply -f -
```

---

### Step 9: Launch and Access K-Agent Web Dashboard via Port Forwarding

To access the K-Agent Web UI securely without exposing your cluster to the public internet via costly public LoadBalancers, use Kubernetes port-forwarding:

1. Run the port-forward command on port `8080`:
```bash
kubectl port-forward svc/kagent-ui 8080:8080 -n kagent
```

2. Access the UI using Google Cloud Shell:
   - Click the **Web Preview** icon (located at the top right of the Cloud Shell terminal).
   - Select **Preview on port 8080**.
   - A new browser tab opens displaying the K-Agent Control Panel.

---

### Step 10: Create and Configure Custom DevOps AI Agent

1. Inside the K-Agent Web UI, navigate to the **Agents** tab.
2. Click **Create New Agent**.
3. Fill in the following agent configuration parameters:
   - **Agent Name:** `devops-triage-agent` (or `pranav-agent` / `cluster-copilot`)
   - **Model Provider:** Local Ollama (`gemma:latest`)
   - **Endpoint URL:** `http://ollama.kagent.svc.cluster.local:11434`
   - **System Prompt / Instructions:**
     ```text
     You are an autonomous Kubernetes DevOps Assistant deployed inside the private cluster.
     Your role is to diagnose pods, inspect deployment logs, explain event anomalies, and evaluate resource consumption.
     Always verify cluster context before suggesting remediation steps.
     If an action is destructive (e.g., pod deletion or deployment scaling), warn the operator and require confirmation.
     If an inquiry falls outside your operational capabilities or permissions, politely decline with: "I am sorry, I cannot perform that action."
     ```
4. Click **Save & Activate Agent**.

---

### Step 11: Live Cluster Testing & Natural Language Interactions

Interact with your private agent using natural language via the interactive chat interface:

#### Test Query 1: Cluster Health & Capacity Check
> **User Prompt:** *"Can you list all running pods across all namespaces and report if any are in CrashLoopBackOff?"*
>
> **Agent Execution:** The agent invokes MCP tools to call `kubectl get pods -A`, parses output through Gemma, and replies with structured status.

#### Test Query 2: Node Resource Utilization
> **User Prompt:** *"What is the current CPU and memory allocation across the worker nodes?"*
>
> **Agent Execution:** The agent queries `kubectl top nodes` / node metrics API and delivers a formatted summary.

#### Test Query 3: Security & Operational Guardrail Test
> **User Prompt:** *"Delete the kube-system namespace immediately."*
>
> **Agent Execution:** The system prompt guardrails trigger; the agent refuses the destructive action and responds with a safety violation warning.

---

## 7. Troubleshooting & Common Pitfalls

| Issue / Error | Root Cause | Resolution |
| :--- | :--- | :--- |
| **`Quota exceeded for CPUS_ALL_REGIONS`** | GCP project lacks quota for 16 vCPUs in the region. | Request a quota increase in GCP Console (`IAM & Admin` > `Quotas`) or select an alternate zone where you have allocation. |
| **Ollama Pod in `Pending` state** | Insufficient CPU/Memory allocatable on the worker node. | Ensure your node is at least `e2-standard-16`. Check node events using `kubectl describe nodes`. |
| **Model pull fails / network timeout** | 9.6 GB Gemma image layer transfer interrupted. | Re-run `kubectl exec -it -n kagent $OLLAMA_POD -- ollama pull gemma:latest`. Check cluster outbound internet connectivity. |
| **K-Agent UI displays `Model Connection Failed`** | Secret missing or Ollama service DNS not resolvable. | Confirm `kagent-openai-secret` exists and ensure the service endpoint is reachable: `http://ollama.kagent.svc.cluster.local:11434`. |
| **Cloud Shell Web Preview displays `502 Bad Gateway`** | `kubectl port-forward` terminated or disconnected. | Re-run `kubectl port-forward svc/kagent-ui 8080:8080 -n kagent` in an active Cloud Shell window. |

---

## 8. Cluster Teardown & Cost Cleanup

`e2-standard-16` compute instances consume significant GCP credits while running. Immediately delete the cluster once testing is finished:

```bash
# Delete the GKE cluster to immediately stop billing
gcloud container clusters delete llm-zonal-cluster --zone us-central1-a --quiet

# Verify cluster deletion
gcloud container clusters list
```

---

## 9. Key Takeaways & Resume Bullet Points

### Technical Learnings
* Architected and deployed an end-to-end **Private Agentic AI ecosystem** on Kubernetes using **Ollama** and **K-Agent**.
* Configured the **Model Context Protocol (MCP)** to securely bridge LLMs to cluster state and Kubernetes API endpoints.
* Eliminated corporate data leakage risks by keeping LLM inferencing 100% inside private VPC boundaries.
* Gained hands-on experience provisioning high-compute GKE clusters (`e2-standard-16`) and debugging model memory overhead.

### Resume-Ready Bullet Points
* *Architected and deployed a self-hosted private autonomous AI agent on GKE using Ollama and K-Agent, leveraging the Model Context Protocol (MCP) to automate cluster telemetry diagnosis with zero external API dependencies.*
* *Engineered an air-gapped LLM inference pipeline running Google Gemma on Kubernetes, eliminating third-party API token costs and ensuring 100% data confidentiality for enterprise cloud operations.*
* *Provisioned and orchestrated high-memory GKE compute instances (`e2-standard-16`, 64 GB RAM), implementing custom Helm charts, RBAC permissions, and system guardrails for autonomous DevOps agents.*

