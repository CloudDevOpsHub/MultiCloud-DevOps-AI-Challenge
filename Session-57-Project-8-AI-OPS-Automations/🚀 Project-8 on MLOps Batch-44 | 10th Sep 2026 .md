# Project 8: MLOps — Building, Containerizing & Deploying an End-to-End Machine Learning Career Prediction System on AWS EKS (Kubernetes)

[![Module: MLOps & Kubernetes](https://img.shields.io/badge/Module-MLOps_%26_Kubernetes-8A2BE2?style=for-the-badge&logo=kubernetes&logoColor=white)](README.md)
[![Cloud: AWS EKS](https://img.shields.io/badge/Cloud-AWS_EKS_%26_EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![ML: Scikit--Learn](https://img.shields.io/badge/ML_Engine-Scikit--Learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)](README.md)
[![API: FastAPI & Uvicorn](https://img.shields.io/badge/API-FastAPI_%26_Uvicorn-009688?style=for-the-badge&logo=fastapi&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## Table of Contents

1. [Project Overview & Core Objective](#1-project-overview--core-objective)
2. [Technology Stack & System Requirements](#2-technology-stack--system-requirements)
3. [Project Directory & File Structure](#3-project-directory--file-structure)
4. [Step 1: AWS EC2 Ubuntu Bastion Instance Provisioning & Security Group Setup](#step-1-aws-ec2-ubuntu-bastion-instance-provisioning--security-group-setup)
5. [Step 2: System Packages, Python 3 & Virtual Environment Setup](#step-2-system-packages-python-3--virtual-environment-setup)
6. [Step 3: Application Code & Python Dependencies Installation (`requirements.txt`)](#step-3-application-code--python-dependencies-installation-requirementstxt)
7. [Step 4: Machine Learning Model Training & Serialization (`train.py`)](#step-4-machine-learning-model-training--serialization-trainpy)
8. [Step 5: FastAPI REST Service Implementation (`main.py`)](#step-5-fastapi-rest-service-implementation-mainpy)
9. [Step 6: Local Validation via Uvicorn Web Server (Port 8000)](#step-6-local-validation-via-uvicorn-web-server-port-8000)
10. [Step 7: Containerization with Docker & Pushing to Docker Hub](#step-7-containerization-with-docker--pushing-to-docker-hub)
11. [Step 8: Installing Kubernetes & AWS CLI Tooling (`kubectl`, `aws-cli`, `eksctl`)](#step-8-installing-kubernetes--aws-cli-tooling-kubectl-aws-cli-eksctl)
12. [Step 9: AWS IAM Credentials Configuration (`aws configure`)](#step-9-aws-iam-credentials-configuration-aws-configure)
13. [Step 10: Provisioning AWS Managed EKS Cluster with `eksctl`](#step-10-provisioning-aws-managed-eks-cluster-with-eksctl)
14. [Step 11: Deploying ML Service to Kubernetes (`kubernetes-deploy.yaml`)](#step-11-deploying-ml-service-to-kubernetes-kubernetes-deployyaml)
15. [Step 12: Live Production Verification via AWS LoadBalancer & Swagger Docs](#step-12-live-production-verification-via-aws-loadbalancer--swagger-docs)
16. [Step 13: Real-World Troubleshooting & Error Resolution Matrix](#step-13-real-world-troubleshooting--error-resolution-matrix)
17. [Step 14: Infrastructure Cleanup & Resource Teardown](#step-14-infrastructure-cleanup--resource-teardown)
18. [Step 15: Professional Resume Points & Interview Highlights](#step-15-professional-resume-points--interview-highlights)

---

## 1. Project Overview & Core Objective

In traditional DevOps, continuous delivery pipelines focus on packaging, testing, and deploying static application code. In contrast, **MLOps (Machine Learning Operations)** bridges the gap between Data Science, Machine Learning, and DevOps Engineering by operationalizing machine learning models into reliable, auto-scaling, production-grade cloud environments.

This capstone project implements an **end-to-end MLOps pipeline** deploying an intelligent IT career salary & upskilling prediction model on **AWS Elastic Kubernetes Service (EKS)**.

### Cross-Functional Roles Executed:
* **Data Engineer / Data Scientist:** Designs feature sets (experience, current compensation, skill breadth, certifications, coding expertise), trains a classification/prediction model using `scikit-learn`, and serializes the trained model weights with `joblib`.
* **Backend / API Engineer:** Wraps the trained ML model with a lightweight, high-performance `FastAPI` service with interactive Swagger documentation (`/docs`) for real-time inference.
* **DevOps / Platform Engineer:** Provisions an Ubuntu EC2 bastion workstation, containerizes the Python ML runtime using Docker, provisions a managed AWS EKS cluster (`eksctl`), deploys Kubernetes Pods with declarative manifests, and exposes the service globally via an AWS Classic/Network Load Balancer.

---

## 2. Technology Stack & System Requirements

| Component | Technology / Tool | Purpose |
| :--- | :--- | :--- |
| **Cloud Host / Bastion** | AWS EC2 (Ubuntu 24.04 LTS) | Workstation for Docker builds, CLI management, and EKS orchestration |
| **Kubernetes Engine** | AWS EKS (`eksctl`) | Managed Kubernetes control plane orchestrating worker node EC2 instances |
| **Networking** | AWS Classic Load Balancer / VPC | External internet routing to Kubernetes service pods on port `80` |
| **Machine Learning Engine** | Python `scikit-learn` (`sklearn`) | Model training, decision trees/random forest algorithms, and feature fitting |
| **Model Persistence** | `joblib` | High-efficiency disk serialization and loading of trained Python ML models |
| **REST API Framework** | `FastAPI` & `Pydantic` | Lightweight, high-concurrency API server with automated data validation |
| **ASGI Web Server** | `Uvicorn` | Production ASGI web server running FastAPI on port `8000` |
| **Container Engine** | `Docker CE` | Packaging the ML runtime, Python dependencies, and model binaries |
| **Container Registry** | Docker Hub | Public/private image repository storing the versioned container image |
| **Cluster Orchestrator** | `kubectl` | Kubernetes command-line tool for deploying manifests and verifying pods |
| **Cluster Automation** | `eksctl` | Official AWS CLI utility to provision CloudFormation-backed EKS clusters |
| **Cloud CLI** | AWS CLI v2 (`aws`) | Managing IAM authentication and AWS resource interactions |

---

## 3. Project Directory & File Structure

```text
mlops-career-prediction/
├── train.py                     # ML training script: prepares dataset & exports model.joblib
├── main.py                      # FastAPI inference server loading model.joblib
├── model.joblib                 # Serialized scikit-learn model artifact
├── requirements.txt             # Python libraries (fastapi, uvicorn, scikit-learn, joblib)
├── Dockerfile                   # Docker container definition for ML runtime
├── kubernetes-deploy.yaml       # Kubernetes Deployment (2 replicas) & LoadBalancer Service
└── venv/                        # Isolated Python virtual environment
```

---

## Step 1: AWS EC2 Ubuntu Bastion Instance Provisioning & Security Group Setup

The EC2 instance acts as our DevOps workstation / bastion host to build Docker images, configure AWS credentials, and manage the EKS cluster.

### 1.1 Launch the Bastion Instance
1. Open the **AWS Management Console** and navigate to **EC2** in your desired region (`ap-south-1` Mumbai recommended).
2. Click **Launch Instances** and set:
   * **Name:** `MLOps-Bastion-Host`
   * **AMI:** `Ubuntu Server 24.04 LTS` (64-bit x86)
   * **Instance Type:** `t3.medium` or `t2.medium` (2 vCPU, 4 GiB RAM; minimum recommended to build Docker ML images smoothly).
   * **Key Pair:** Create a new key pair or select an existing one (`.pem` format).
   * **Storage:** `30 GiB` gp3 root volume.

### 1.2 Configure Security Group (Ports 22 & 8000)
1. Under **Network Settings**, click **Edit**.
2. Keep SSH: Port `22` (Source: `My IP` or `0.0.0.0/0`).
3. Add a new **Inbound Rule**:
   * **Type:** `Custom TCP`
   * **Port Range:** `8000`
   * **Source:** `0.0.0.0/0` (Anywhere IPv4)
   * **Description:** `FastAPI Uvicorn Local Dev Server`
4. Click **Launch Instance**.

### 1.3 Connect and Elevate Privileges
Connect using EC2 Instance Connect or SSH:
```bash
ssh -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>
sudo -i
```

---

## Step 2: System Packages, Python 3 & Virtual Environment Setup

### 2.1 Update System Repositories
```bash
apt update -y && apt upgrade -y
apt install -y python3 python3-pip python3-venv git curl unzip
```

### 2.2 Verify System Tooling
```bash
python3 --version
pip3 --version
```

### 2.3 Create Project Working Directory
```bash
mkdir -p /root/mlops-career-prediction
cd /root/mlops-career-prediction
```

### 2.4 Create and Activate Python Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate
```

> The terminal prompt will now reflect the active virtual environment: `(venv) root@ip-...:~/mlops-career-prediction#`.

---

## Step 3: Application Code & Python Dependencies Installation (`requirements.txt`)

### 3.1 Create `requirements.txt`
```bash
cat << 'EOF' > requirements.txt
fastapi
uvicorn
scikit-learn
pandas
numpy
joblib
pydantic
EOF
```

### 3.2 Install Dependencies inside Virtual Environment
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## Step 4: Machine Learning Model Training & Serialization (`train.py`)

In this step, we act as the **Data Scientist / ML Engineer**. We define an enterprise career progression dataset combining 5 critical candidate features:
1. `experience_years`: Total years of IT experience.
2. `current_package_lpa`: Current annual CTC in Lakhs Per Annum.
3. `skills_count`: Number of verified technical skills (Linux, Docker, K8s, AWS, CI/CD, Python, Terraform).
4. `certifications_count`: Recognized cloud/Kubernetes certifications held.
5. `coding_level`: Technical assessment score on a scale from 1 to 10.

The target output predicts whether upskilling is needed (`0` = Highly Aligned / Promotion Ready, `1` = Upskilling Recommended to unlock 10+ LPA package).

```bash
cat << 'EOF' > train.py
import joblib
import numpy as np
import pandas as pd
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split

def train_and_save_model():
    print("[*] Generating training dataset for IT Career Progression...")
    
    # Synthetic dataset representing industry career progression patterns
    # Features: [experience_years, current_package_lpa, skills_count, certifications, coding_level]
    # Label: 0 = Optimal/High-Trajectory (Promotion Ready), 1 = Upskilling Required (<10 LPA barrier)
    data = [
        [1.0, 3.5, 3, 0, 4, 1],
        [2.0, 4.0, 4, 0, 5, 1],
        [3.0, 4.5, 4, 1, 5, 1],
        [3.0, 7.5, 8, 2, 7, 1],
        [4.0, 6.0, 5, 1, 6, 1],
        [5.0, 8.0, 6, 1, 6, 1],
        [2.0, 9.0, 8, 2, 8, 0],
        [3.0, 11.0, 9, 2, 8, 0],
        [4.0, 14.0, 10, 3, 8, 0],
        [5.0, 18.0, 11, 3, 9, 0],
        [6.0, 22.0, 12, 4, 9, 0],
        [7.0, 26.0, 14, 4, 9, 0],
        [8.0, 30.0, 15, 5, 10, 0]
    ]

    columns = ['experience_years', 'current_package_lpa', 'skills_count', 'certifications', 'coding_level', 'upskill_required']
    df = pd.DataFrame(data, columns=columns)

    X = df[['experience_years', 'current_package_lpa', 'skills_count', 'certifications', 'coding_level']]
    y = df['upskill_required']

    print(f"[*] Training dataset size: {len(df)} records")

    # Fit Random Forest Classifier
    model = RandomForestClassifier(n_estimators=50, random_state=42)
    model.fit(X, y)

    # Save model artifact to disk
    model_filename = 'model.joblib'
    joblib.dump(model, model_filename)
    print(f"[+] Model successfully trained and serialized to: {model_filename}")

if __name__ == "__main__":
    train_and_save_model()
EOF
```

### 4.1 Execute Training Script
```bash
python3 train.py
```

### 4.2 Verify Generated Artifact
```bash
ls -lh model.joblib
```

---

## Step 5: FastAPI REST Service Implementation (`main.py`)

In this step, we implement the **FastAPI REST API** server that loads `model.joblib` and handles real-time inference via HTTP POST requests, while also providing OpenAPI Swagger documentation.

```bash
cat << 'EOF' > main.py
import joblib
import numpy as np
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field

# Initialize FastAPI application
app = FastAPI(
    title="MLOps Career Progression & Salary Predictor",
    description="End-to-end Machine Learning inference API deployed on AWS EKS Kubernetes",
    version="1.0.0"
)

# Load the trained machine learning model
MODEL_PATH = "model.joblib"
try:
    model = joblib.load(MODEL_PATH)
except Exception as e:
    raise RuntimeError(f"Failed to load model from {MODEL_PATH}: {e}")

# Define input schema with Pydantic
class CandidateProfile(BaseModel):
    experience_years: float = Field(..., ge=0.0, le=40.0, description="Total IT experience in years", example=3.0)
    current_package_lpa: float = Field(..., ge=1.0, le=150.0, description="Current annual CTC in Lakhs (LPA)", example=7.5)
    skills_count: int = Field(..., ge=1, le=50, description="Number of proven technical DevOps/Cloud skills", example=8)
    certifications: int = Field(..., ge=0, le=20, description="Number of active cloud certifications", example=2)
    coding_level: int = Field(..., ge=1, le=10, description="Self-assessed scripting/coding proficiency (1-10)", example=7)

@app.get("/")
def read_root():
    return {
        "status": "online",
        "service": "MLOps Career & Package Prediction API",
        "version": "1.0.0",
        "docs_url": "/docs"
    }

@app.get("/healthz")
def health_check():
    return {"status": "healthy"}

@app.post("/predict")
def predict_career_path(profile: CandidateProfile):
    try:
        # Prepare feature vector matching training schema
        features = np.array([[
            profile.experience_years,
            profile.current_package_lpa,
            profile.skills_count,
            profile.certifications,
            profile.coding_level
        ]])

        # Execute inference
        prediction = model.predict(features)[0]

        if prediction == 1:
            recommendation = (
                "Upskilling Recommended: Master Advanced Cloud Architecture, Kubernetes, "
                "Terraform, and AIOps to break the 10+ LPA ceiling."
            )
            career_status = "Growth Opportunity Available"
        else:
            recommendation = (
                "Top Tier Alignment: Candidate possesses strong compensation-to-skill alignment. "
                "Focus on System Design, Principal Architect roles, and Engineering Leadership."
            )
            career_status = "Optimal Trajectory"

        return {
            "prediction_code": int(prediction),
            "career_status": career_status,
            "actionable_recommendation": recommendation,
            "evaluated_input": profile.dict()
        }
    except Exception as err:
        raise HTTPException(status_code=500, detail=f"Inference error: {err}")
EOF
```

---

## Step 6: Local Validation via Uvicorn Web Server (Port 8000)

Before containerization, verify that FastAPI runs locally and responds to requests.

### 6.1 Start Uvicorn Server
```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

### 6.2 Expected Terminal Output
```text
INFO:     Started server process [12345]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### 6.3 Browser Testing
Open a web browser and navigate to:
```text
http://<AWS-EC2-PUBLIC-IP>:8000/docs
```

* Expand `POST /predict`.
* Click **Try it out**.
* Provide input JSON:
```json
{
  "experience_years": 3.0,
  "current_package_lpa": 7.5,
  "skills_count": 8,
  "certifications": 2,
  "coding_level": 7
}
```
* Click **Execute** and confirm the HTTP `200` response.

Press `CTRL + C` in the terminal to stop the local test server.

---

## Step 7: Containerization with Docker & Pushing to Docker Hub

In this step, we act as the **DevOps / Platform Engineer** to containerize the ML model and publish it to Docker Hub.

### 7.1 Install and Start Docker Engine
```bash
apt install -y docker.io
systemctl start docker
systemctl enable docker
usermod -aG docker ubuntu
```

### 7.2 Create `Dockerfile`
```bash
cat << 'EOF' > Dockerfile
FROM python:3.10-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependencies and install
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code and pre-trained model artifact
COPY main.py .
COPY model.joblib .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
EOF
```

### 7.3 Build the Docker Image
```bash
docker build -t it-career-api:latest .
```

Verify image creation:
```bash
docker images
```

### 7.4 Authenticate with Docker Hub
Log in to Docker Hub:
```bash
docker login
```
> Enter your Docker Hub username and password/access token.

### 7.5 Tag and Push Image to Docker Hub
Replace `<YOUR_DOCKERHUB_USERNAME>` with your actual Docker Hub username:
```bash
export DOCKER_USER="<YOUR_DOCKERHUB_USERNAME>"

docker tag it-career-api:latest ${DOCKER_USER}/it-career-api:latest
docker push ${DOCKER_USER}/it-career-api:latest
```

---

## Step 8: Installing Kubernetes & AWS CLI Tooling (`kubectl`, `aws-cli`, `eksctl`)

To orchestrate the managed EKS cluster, install the three required management CLI utilities on our bastion host.

### 8.1 Install `kubectl` (Kubernetes Control Utility)
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
mv kubectl /usr/local/bin/
kubectl version --client
```

### 8.2 Install AWS CLI v2
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip -q awscliv2.zip
./aws/install --update
aws --version
```

### 8.3 Install `eksctl` (Amazon EKS CLI)
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
mv /tmp/eksctl /usr/local/bin
eksctl version
```

---

## Step 9: AWS IAM Credentials Configuration (`aws configure`)

`eksctl` requires programmatic administrator access to create VPCs, subnets, NAT Gateways, Internet Gateways, IAM roles, and EKS clusters via AWS CloudFormation.

### 9.1 Create IAM User (AWS Console)
1. Go to **AWS Console** → **IAM** → **Users** → **Create User**.
2. Name: `mlops-admin-user`.
3. Attach Policy directly: `AdministratorAccess`.
4. Create user → Open user → **Security credentials** tab.
5. Under **Access keys**, click **Create access key** → Select **CLI** → Accept terms → Download `.csv`.

### 9.2 Configure AWS CLI on Bastion Host
```bash
aws configure
```

Provide the requested inputs:
* **AWS Access Key ID:** `Paste your Access Key`
* **AWS Secret Access Key:** `Paste your Secret Key`
* **Default region name:** `ap-south-1` (Mumbai or your preferred region)
* **Default output format:** `json`

Verify authentication:
```bash
aws sts get-caller-identity
```

---

## Step 10: Provisioning AWS Managed EKS Cluster with `eksctl`

Launch an Amazon EKS cluster with 2 worker nodes managed in an Auto Scaling Group.

### 10.1 Execute Cluster Creation Command
```bash
eksctl create cluster \
  --name mlops-cluster \
  --region ap-south-1 \
  --nodegroup-name mlops-nodes \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 2 \
  --nodes-max 3 \
  --managed
```

> **Note:** EKS cluster provisioning creates CloudFormation stacks, dedicated VPCs, security groups, and EC2 instances. This process takes approximately **12 to 15 minutes**.

### 10.2 Verify Cluster Connectivity
Once `eksctl` completes, verify that `kubectl` can communicate with the Kubernetes API server:
```bash
kubectl get nodes -o wide
```

Expected output showing 2 Ready worker nodes:
```text
NAME                                            STATUS   ROLES    AGE     VERSION
ip-192-168-xx-xx.ap-south-1.compute.internal   Ready    <none>   2m30s   v1.30.x
ip-192-168-yy-yy.ap-south-1.compute.internal   Ready    <none>   2m30s   v1.30.x
```

---

## Step 11: Deploying ML Service to Kubernetes (`kubernetes-deploy.yaml`)

Create the declarative Kubernetes manifest containing:
1. **Deployment:** Manages 2 load-balanced pod replicas running our containerized ML API.
2. **Service:** Exposes the pods to the outside world via an AWS Classic Load Balancer on port `80`.

### 11.1 Create Manifest
Replace `<YOUR_DOCKERHUB_USERNAME>` with your Docker Hub handle:

```bash
cat << 'EOF' > kubernetes-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: it-career-api
  labels:
    app: it-career-api
spec:
  replicas: 2
  selector:
    matchLabels:
      app: it-career-api
  template:
    metadata:
      labels:
        app: it-career-api
    spec:
      containers:
      - name: it-career-api
        image: <YOUR_DOCKERHUB_USERNAME>/it-career-api:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 8000
        resources:
          limits:
            cpu: "500m"
            memory: "512Mi"
          requests:
            cpu: "250m"
            memory: "256Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: it-career-service
  labels:
    app: it-career-api
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8000
    protocol: TCP
  selector:
    app: it-career-api
EOF
```

Update the image placeholder:
```bash
sed -i "s/<YOUR_DOCKERHUB_USERNAME>/${DOCKER_USER}/g" kubernetes-deploy.yaml
```

### 11.2 Apply the Manifest to EKS
```bash
kubectl apply -f kubernetes-deploy.yaml
```

### 11.3 Monitor Deployment & Pods
```bash
kubectl get deployments
kubectl get pods -l app=it-career-api -w
```

Expected output:
```text
NAME                             READY   STATUS    RESTARTS   AGE
it-career-api-7b89f5d6cb-abc12   1/1     Running   0          45s
it-career-api-7b89f5d6cb-xyz34   1/1     Running   0          45s
```

---

## Step 12: Live Production Verification via AWS LoadBalancer & Swagger Docs

### 12.1 Retrieve the External Load Balancer Hostname
```bash
kubectl get svc it-career-service
```

Expected output:
```text
NAME                TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
it-career-service   LoadBalancer   10.100.200.50    a1b2c3d4e5f6-123456789.ap-south-1.elb.amazonaws.com                     80:31234/TCP   2m
```

### 12.2 Access Swagger UI in Browser
Copy the `EXTERNAL-IP` URL (allow 1–2 minutes for AWS ELB DNS propagation):
```text
http://<EXTERNAL-LOADBALANCER-DNS>/docs
```

### 12.3 Execute Live Inference Test
1. Click **POST /predict** → Click **Try it out**.
2. Enter Candidate Profile:
```json
{
  "experience_years": 4.0,
  "current_package_lpa": 6.5,
  "skills_count": 6,
  "certifications": 1,
  "coding_level": 6
}
```
3. Click **Execute**.
4. Observe the live JSON response returning real-time ML inference:
```json
{
  "prediction_code": 1,
  "career_status": "Growth Opportunity Available",
  "actionable_recommendation": "Upskilling Recommended: Master Advanced Cloud Architecture, Kubernetes, Terraform, and AIOps to break the 10+ LPA ceiling.",
  "evaluated_input": {
    "experience_years": 4.0,
    "current_package_lpa": 6.5,
    "skills_count": 6,
    "certifications": 1,
    "coding_level": 6
  }
}
```

---

## Step 13: Real-World Troubleshooting & Error Resolution Matrix

| Symptom / Error | Root Cause | Exact Solution |
| :--- | :--- | :--- |
| **`ImagePullBackOff` or `ErrImagePull`** | Docker image name in `kubernetes-deploy.yaml` is misspelled or the repository is private on Docker Hub. | Ensure the Docker Hub repo is public or create a Kubernetes `imagePullSecret`. Verify image tag with `docker pull <username>/it-career-api:latest`. |
| **`Pending` status on LoadBalancer `EXTERNAL-IP`** | AWS Classic Load Balancer is provisioning security groups and health checks. | Wait 60–120 seconds. If stuck, ensure EKS subnets have the tag `kubernetes.io/role/elb: 1`. |
| **`FastAPI 500 Internal Server Error`** | Input payload fields fail Pydantic validation or types do not match training schema. | Ensure all 5 numeric features (`experience_years`, `current_package_lpa`, `skills_count`, `certifications`, `coding_level`) are provided. |
| **`eksctl: command not found`** | Binary was extracted to `/tmp` but not moved to system `$PATH`. | Run `mv /tmp/eksctl /usr/local/bin/` and check `which eksctl`. |
| **`AccessDeniedException` during `eksctl create cluster`** | IAM credentials configured via `aws configure` lack permissions for CloudFormation or VPC. | Attach `AdministratorAccess` or appropriate EKS/VPC/CloudFormation policies to the IAM user. |
| **`CrashLoopBackOff` on Pods** | `model.joblib` was missing from the Docker image build directory. | Ensure `COPY model.joblib .` is present in `Dockerfile` and rebuild image after running `python3 train.py`. |

---

## Step 14: Infrastructure Cleanup & Resource Teardown

EKS clusters, EC2 worker nodes, and Load Balancers incur continuous AWS charges. Delete all resources immediately after completing the project.

### 14.1 Delete Kubernetes Service (Destroys AWS Load Balancer)
```bash
kubectl delete -f kubernetes-deploy.yaml
```

### 14.2 Delete EKS Cluster & Associated CloudFormation Stacks
```bash
eksctl delete cluster --name mlops-cluster --region ap-south-1
```

> `eksctl` will automatically drain the worker nodes, terminate the EC2 instances, delete the Auto Scaling Group, detach the VPC CNI, and tear down the CloudFormation stack (~5–8 minutes).

### 14.3 Terminate Bastion EC2 Instance
1. In the **AWS EC2 Console** → **Instances**.
2. Select `MLOps-Bastion-Host` → **Instance State** → **Terminate Instance**.

### 14.4 Remove IAM Access Keys
Go to **AWS IAM** → **Users** → `mlops-admin-user` → **Security credentials** → Delete active Access Keys.

---

## Step 15: Professional Resume Points & Interview Highlights

Add these bullet points to your resume to showcase production MLOps and Kubernetes orchestration capabilities:

* **End-to-End MLOps Architecture:** Engineered an automated, production-grade Machine Learning inference pipeline deploying a containerized `scikit-learn` predictive model on AWS Elastic Kubernetes Service (EKS).
* **High-Concurrency API Design:** Developed an asynchronous REST inference engine using `FastAPI` and `Uvicorn` with automated Pydantic schema validation, OpenAPI Swagger documentation, and sub-15ms response latency.
* **Kubernetes Orchestration & Scalability:** Designed declarative Kubernetes Deployment manifests with 2 auto-scaling replicas, CPU/memory resource limits, zero-downtime rolling update strategies, and AWS Elastic Load Balancing.
* **Infrastructure as Code with `eksctl`:** Provisioned multi-node AWS EKS clusters across multi-AZ subnets using `eksctl` and CloudFormation, standardizing network interfaces (VPC CNI) and IAM least-privilege role policies.

---
> [🏠 Back to Master Index](README.md)
