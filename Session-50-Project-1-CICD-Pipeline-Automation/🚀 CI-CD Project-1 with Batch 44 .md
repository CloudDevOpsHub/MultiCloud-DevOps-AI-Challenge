# Project 1: End-to-End CI/CD Automation on AWS Using Jenkins, Docker & Node.js

[![Module: CI/CD Pipelines](https://img.shields.io/badge/Module-Jenkins%20CI%2FCD-D33833?style=for-the-badge&logo=jenkins&logoColor=white)](README.md)
[![Cloud: AWS EC2](https://img.shields.io/badge/Cloud-AWS%20EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![Containers: Docker](https://img.shields.io/badge/Containers-Docker%20CE-2496ED?style=for-the-badge&logo=docker&logoColor=white)](README.md)
[![App: Node.js & React](https://img.shields.io/badge/App-Node.js%20%26%20React-339933?style=for-the-badge&logo=node.js&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents

1. [Project Overview & Business Problem](#1-project-overview--business-problem)
2. [Open-Source vs Managed Cloud CI/CD (The "From Scratch" Philosophy)](#2-open-source-vs-managed-cloud-cicd-the-from-scratch-philosophy)
3. [End-to-End CI/CD Architecture & Execution Workflow](#3-end-to-end-cicd-architecture--execution-workflow)
4. [Technology Stack & Core Components](#4-technology-stack--core-components)
5. [High-Level Project Execution Roadmap (8 Core Steps)](#5-high-level-project-execution-roadmap-8-core-steps)
6. [Step 1: AWS EC2 Infrastructure Setup & Security Group Hardening](#6-step-1-aws-ec2-infrastructure-setup--security-group-hardening)
7. [Step 2: Host Preparation & OpenJDK 21 Installation](#7-step-2-host-preparation--openjdk-21-installation)
8. [Step 3: Jenkins LTS Installation, Unlocking & Initial Configuration](#8-step-3-jenkins-lts-installation-unlocking--initial-configuration)
9. [Step 4: Automated Docker CE & Permissions Setup via Shell Script](#9-step-4-automated-docker-ce--permissions-setup-via-shell-script)
10. [Step 5: Essential Jenkins Plugins & Tool Global Configuration](#10-step-5-essential-jenkins-plugins--tool-global-configuration)
    - [10.1 Required Plugins Installation](#101-required-plugins-installation)
    - [10.2 Global Tool Configuration (JDK 17, Node.js 16, Docker)](#102-global-tool-configuration-jdk-17-nodejs-16-docker)
11. [Step 6: Secure Credential Management in Jenkins](#11-step-6-secure-credential-management-in-jenkins)
12. [Step 7: Continuous Integration (CI) Pipeline Implementation](#12-step-7-continuous-integration-ci-pipeline-implementation)
    - [12.1 Monorepo Architecture & Code Organization](#121-monorepo-architecture--code-organization)
    - [12.2 Declarative Jenkinsfile Syntax & Analysis](#122-declarative-jenkinsfile-syntax--analysis)
    - [12.3 Running & Verifying the CI Pipeline](#123-running--verifying-the-ci-pipeline)
13. [Continuous Delivery vs Continuous Deployment: Enterprise Reality](#13-continuous-delivery-vs-continuous-deployment-enterprise-reality)
14. [Step 8: Continuous Delivery (CD) Pipeline Implementation](#14-step-8-continuous-delivery-cd-pipeline-implementation)
    - [14.1 CD Pipeline Script Analysis](#141-cd-pipeline-script-analysis)
    - [14.2 Container Lifecycle & Port Mapping](#142-container-lifecycle--port-mapping)
    - [14.3 Live Application Verification on Port 3000](#143-live-application-verification-on-port-3000)
15. [Automating Continuous Deployment via Build Triggers](#15-automating-continuous-deployment-via-build-triggers)
16. [Enterprise Production Scenarios & Advanced Configurations](#16-enterprise-production-scenarios--advanced-configurations)
    - [16.1 Scenario 1: Multi-Environment Parameterized Pipeline (Dev, QA, Staging, Prod)](#161-scenario-1-multi-environment-parameterized-pipeline-dev-qa-staging-prod)
    - [16.2 Scenario 2: Handling Private GitHub & Docker Registries](#162-scenario-2-handling-private-github--docker-registries)
    - [16.3 Scenario 3: Remote Target Host Deployment via SSH](#163-scenario-3-remote-target-host-deployment-via-ssh)
    - [16.4 Scenario 4: Monorepo vs Multi-Repo Strategy](#164-scenario-4-monorepo-vs-multi-repo-strategy)
17. [Smoke Testing & Post-Deployment Sanity Checks](#17-smoke-testing--post-deployment-sanity-checks)
18. [Real-World Troubleshooting, Errors & RCA Matrix](#18-real-world-troubleshooting-errors--rca-matrix)
19. [Infrastructure Teardown & Cloud Cost Optimization](#19-infrastructure-teardown--cloud-cost-optimization)
20. [Project in One Page](#20-project-in-one-page)
21. [Professional Resume Points & Real-Time Job Roles](#21-professional-resume-points--real-time-job-roles)
22. [Top 10 Technical Interview Questions & Answers](#22-top-10-technical-interview-questions--answers)
23. [Top 10 Scenario-Based Production Interview Questions & Solutions](#23-top-10-scenario-based-interview-questions--solutions)

---

## 1. Project Overview & Business Problem

In modern software delivery, manual software compilation, testing, image packaging, and deployment are prone to human error, environment inconsistencies, configuration drift, and lengthy release cycles.

This capstone project demonstrates how to build an **enterprise-grade, completely open-source Continuous Integration and Continuous Delivery/Deployment (CI/CD) automation pipeline** on **AWS Infrastructure** from scratch. 

The demonstration application deployed is a feature-rich, high-performance **Starbucks Web Application** built with **Node.js and React**. While Node.js was selected for its asynchronous non-blocking event-driven architecture, the automated CI/CD pipeline principles established here apply universally to Java Spring Boot, Python Django/Flask, Golang, or PHP microservices.

### Key Objectives

- Provision and harden an **AWS EC2 Ubuntu compute instance** with precise inbound security rules.
- Deploy and configure an open-source **Jenkins LTS Automation Server** on Java OpenJDK 21.
- Automate **Docker CE runtime** provisioning, socket permissions, and daemon configurations.
- Establish centralized **Jenkins Tool Configuration** (OpenJDK 17, Node.js 16, and Docker CLI).
- Secure sensitive credentials (Docker Hub tokens) avoiding hardcoded credentials in source control.
- Write and execute a **Declarative Jenkinsfile** for Continuous Integration (Checkout, NPM dependency installation, static compilation/build, Docker image creation, and automated push to Docker Hub registry).
- Implement a decoupled **Continuous Delivery (CD) pipeline** managing Docker container lifecycles (idempotent stop, removal, port mapping to port `3000`).
- Convert manual Continuous Delivery into zero-touch **Continuous Deployment** through Jenkins downstream build triggers.
- Master enterprise production scenarios: Parameterized multi-environment builds, private repository access, remote SSH deployments, and monorepo management.

---

## 2. Open-Source vs Managed Cloud CI/CD (The "From Scratch" Philosophy)

In previous sessions, cloud-managed CI/CD services (such as Azure DevOps Pipelines or AWS CodePipeline) were utilized. However, enterprise organizations frequently demand or maintain fully self-hosted, cloud-agnostic, open-source CI/CD architectures for three primary reasons:

| Evaluation Dimension | Managed Cloud CI/CD (Azure DevOps / AWS CodePipeline) | Open-Source Self-Hosted (Jenkins + Docker on AWS EC2) |
| :--- | :--- | :--- |
| **Vendor Lock-In** | High; tied to provider-specific agent runtimes, APIs, and billing models. | **Zero Lock-In;** pipeline definitions and containers execute identically on AWS, Azure, GCP, or on-premise bare metal. |
| **Cost Predictability** | Pay-per-minute build agents or per-user monthly licensing fees escalate rapidly with large teams. | **Fixed Infrastructure Cost;** pay only for standard EC2 compute and EBS storage; unlimited builds and concurrent jobs. |
| **Customizability & Ecosystem** | Constrained to plugins and extensions approved in vendor marketplaces. | **Infinite Flexibility;** 1,800+ open-source plugins, deep Linux CLI integration, custom runners, and bespoke toolchains. |
| **Data & Secret Governance** | Code, secrets, and build logs reside inside cloud provider infrastructure. | **Complete Control;** secrets, build artifacts, network traffic, and code checkouts remain strictly within private subnets. |

> **Key Rule from Vikas:** *"Whatever the code is—Node.js, Java, or Python—the DevOps methodology does not change. Our goal is to take raw source code from Git, build it, package it into immutable container images, push it to a registry, and deploy it seamlessly."*

---

## 3. End-to-End CI/CD Architecture & Execution Workflow

The complete end-to-end automation workflow transitions code from a developer's commit to a live customer-facing web application:

```text
+-------------------------------------------------------------------------------------------------------------------+
|                                            DEVELOPER & SOURCE CODE REPO                                           |
|                                                                                                                   |
|   [ Developer ] ---> git push ---> [ GitHub Repository (Monorepo) ]                                               |
|                                       - Application Code (/src)                                                   |
|                                       - Dockerfile & Jenkinsfile                                                  |
+---------------------------------------------------|---------------------------------------------------------------+
                                                    |
                                    Webhook / Poll / Manual Trigger
                                                    |
                                                    v
+-------------------------------------------------------------------------------------------------------------------+
|                                      AWS EC2 INSTANCE (JENKINS & DOCKER ENGINE)                                   |
|                                                                                                                   |
|  +-------------------------------------------------------------------------------------------------------------+  |
|  | [ STEP 1: CONTINUOUS INTEGRATION (CI PIPELINE) ]                                                            |  |
|  |                                                                                                             |  |
|  |  1. Checkout SCM          2. Tool Provisioning         3. Build & Package      4. Containerization          |  |
|  |     Clone Monorepo           Inject JDK 17 &              npm install             docker build               |  |
|  |     into Workspace           Node.js 16 runtimes          npm run build           -t starbucks:latest        |  |
|  +----------------------------------------------------------------------------------------|--------------------+  |
|                                                                                           |                       |
|                                                                            withCredentials(ID: 'docker')          |
|                                                                                           |                       |
|                                                                                           v                       |
|                                                                           +------------------------------------+  |
|                                                                           |   [ Docker Hub Registry ]          |  |
|                                                                           |   Push image:                      |  |
|                                                                           |   <username>/starbucks:latest      |  |
|                                                                           +-----------------|------------------+  |
|                                                                                             |                     |
|                                     Continuous Delivery (Manual Trigger / Approval)         |                     |
|                                                      OR                                     |                     |
|                                     Continuous Deployment (Downstream Build Trigger)        |                     |
|                                                                                             v                     |
|  +-------------------------------------------------------------------------------------------------------------+  |
|  | [ STEP 2: CONTINUOUS DELIVERY / DEPLOYMENT (CD PIPELINE) ]                                                  |  |
|  |                                                                                                             |  |
|  |  1. Pull Latest Image: docker pull <username>/starbucks:latest                                              |  |
|  |  2. Stop & Remove Old Container: docker stop starbucks || true && docker rm starbucks || true               |  |
|  |  3. Run Isolated Container: docker run -d --name starbucks -p 3000:3000 <username>/starbucks:latest        |  |
|  +-------------------------------------------------------------------------------------------------------------+  |
+---------------------------------------------------|---------------------------------------------------------------+
                                                    |
                                    Inbound HTTP Traffic on Port 3000
                                                    |
                                                    v
                                      +---------------------------+
                                      | End User / Client Browser |
                                      | http://<EC2-IP>:3000      |
                                      +---------------------------+
```

---

## 4. Technology Stack & Core Components

| Technology / Component | Role in Architecture | Version / Configuration |
| :--- | :--- | :--- |
| **AWS EC2** | Scalable Infrastructure as a Service (IaaS) host server. | Ubuntu 24.04 LTS, `t2.medium` / `t2.large`, 40 GB gp3 EBS. |
| **AWS Security Groups** | Virtual stateful firewall controlling inbound/outbound instance traffic. | Inbound Ports: `22` (SSH), `8080` (Jenkins), `3000` (Node.js App). |
| **OpenJDK 21** | Host Java runtime dependency required to execute Jenkins LTS daemon. | OpenJDK 21 headless package (`fontconfig openjdk-21-jre`). |
| **Jenkins LTS** | Central automation orchestration server executing CI and CD pipelines. | Jenkins LTS (Debian/Ubuntu stable binary release). |
| **Docker CE** | Container engine building immutable application images and running containers. | Docker Community Edition v29.x + Docker Compose CLI. |
| **Adoptium JDK 17** | Pipeline build tool dependency for auxiliary compilation plugins. | Eclipse Temurin 17.0.8.1+1 configured via Jenkins Global Tools. |
| **NodeJS & NPM** | Application execution runtime and package dependency manager. | Node.js v16.20.0 configured via Jenkins NodeJS Tool Plugin. |
| **Docker Hub** | Central public/private container image registry storing packaged artifacts. | Cloud-hosted registry authenticated via Jenkins Global Credentials. |
| **Starbucks Web App** | Modern single-page web application compiled with React and Node.js. | Monorepo containing `/src`, `package.json`, Dockerfile, Jenkinsfile. |

---

## 5. High-Level Project Execution Roadmap (8 Core Steps)

During the live session, the implementation was structured into 8 progressive milestones:

1. **AWS EC2 Provisioning:** Launch Ubuntu 24.04 compute instance with 40GB storage and configure Security Group rules.
2. **Host Dependencies:** Update system packages and install OpenJDK 21 for Jenkins.
3. **Jenkins Setup:** Install Jenkins LTS, start service, retrieve initial admin password, and setup administrative users.
4. **Docker Automation:** Clone project repository and execute `docker.sh` to install Docker CE and configure user group permissions.
5. **Jenkins Tooling & Plugins:** Install Eclipse Temurin, NodeJS, Docker Pipeline, and Stage View plugins; configure JDK 17, Node 16, and Docker CLI.
6. **Credential Management:** Securely register Docker Hub username and access token under Global Credentials with ID `docker`.
7. **Continuous Integration (CI) Job:** Create a Declarative Pipeline job to checkout code, install NPM packages, compile React code, build a Docker image, and push to Docker Hub.
8. **Continuous Delivery (CD) Job:** Create a decoupled deployment pipeline to stop stale containers, run new containers on port 3000, verify the application, and wire automated downstream build triggers for continuous deployment.

---

## 6. Step 1: AWS EC2 Infrastructure Setup & Security Group Hardening

### 6.1 Launching the Compute Instance

To provide sufficient CPU and RAM for concurrent NPM package installations and Docker image layer builds without freezing or running out of memory:

1. Navigate to the **AWS Management Console** and switch to the target region (e.g., `ap-south-1` Mumbai).
2. Open the **EC2 Dashboard** and click **Launch Instance**.
3. Configure the following parameters:
   - **Name:** `Starbucks-CICD-Server`
   - **Application and OS Images (AMI):** `Ubuntu Server 24.04 LTS (HVM), SSD Volume Type` (64-bit x86).
   - **Instance Type:** `t2.medium` (2 vCPU, 4 GiB RAM) minimum; `t2.large` (2 vCPU, 8 GiB RAM) recommended for superior build throughput.
   - **Key Pair:** Proceed without a key pair (if connecting via EC2 Instance Connect) or select an existing `.pem` key pair.
   - **Storage Configuration:** Increase root volume size from default 8 GB to **40 GB gp3** to accommodate Docker images, Jenkins build caches, and NPM node modules.

### 6.2 Security Group Inbound Firewall Rules

Network security must be configured to permit administrative access, Jenkins Web UI access, and web application traffic:

| Type | Port Range | Protocol | Source | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **SSH** | `22` | TCP | `0.0.0.0/0` | Host terminal management via SSH / EC2 Instance Connect. |
| **Custom TCP** | `8080` | TCP | `0.0.0.0/0` | Access to Jenkins Automation Server Web Interface. |
| **Custom TCP** | `3000` | TCP | `0.0.0.0/0` | Access to the live Starbucks Node.js / React application. |

> **Security Note:** In strict enterprise environments, ports `22` and `8080` should be restricted to an internal Corporate VPN or specific bastion host CIDR blocks. Port `3000` is exposed publicly for end-user testing.

### 6.3 Connecting to the Instance

Connect to the instance using **EC2 Instance Connect** (browser-based) or terminal SSH:

```bash
# Elevate to root privileges
sudo -i

# Verify Linux distribution and kernel version
cat /etc/os-release
uname -r
```

---

## 7. Step 2: Host Preparation & OpenJDK 21 Installation

Jenkins is written in Java and requires a Java Runtime Environment (JRE/JDK) on the host operating system to execute its master controller process.

```bash
# Update local package repository index
sudo apt update

# Install OpenJDK 21 and font configuration dependencies
sudo apt install -y fontconfig openjdk-21-jre

# Validate Java version on host system
java -version
```

**Expected Verification Output:**

```text
openjdk version "21.0.x" 202x-xx-xx
OpenJDK Runtime Environment (build 21.0.x+...)
OpenJDK 64-Bit Server VM (build 21.0.x+..., mixed mode, sharing)
```

---

## 8. Step 3: Jenkins LTS Installation, Unlocking & Initial Configuration

### 8.1 Adding Official Jenkins Debian Keyring & Repositories

```bash
# Fetch and store the official Jenkins GPG signing key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Register the stable Jenkins apt repository
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Refresh package lists to include Jenkins repository
sudo apt update

# Install Jenkins LTS package
sudo apt install -y jenkins

# Enable and verify Jenkins service status
sudo systemctl enable jenkins
sudo systemctl status jenkins --no-pager
```

### 8.2 Unlocking Jenkins via Browser UI

1. Open your web browser and navigate to: `http://<EC2-PUBLIC-IP>:8080`
2. Jenkins presents an **"Unlock Jenkins"** screen requesting an administrator password.
3. Retrieve the generated initial administrator password from the server terminal:

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

4. Paste the 32-character alphanumeric key into the browser and click **Continue**.
5. Select **Install suggested plugins** and allow the initial plugin setup wizard to finish.
6. When prompted to **Create First Admin User**, configure:
   - **Username:** `admin`
   - **Password:** `admin` (or your chosen secure password)
   - **Full Name:** `Administrator`
   - **E-mail Address:** `admin@example.com`
7. Confirm Instance URL configuration (`http://<EC2-PUBLIC-IP>:8080`) and click **Save and Finish**.

---

## 9. Step 4: Automated Docker CE & Permissions Setup via Shell Script

Rather than manually running disparate commands for Docker repositories, keys, and daemons, the project repository provides an automated installation script (`docker.sh`).

### 9.1 Cloning the Project Monorepo onto the EC2 Host

```bash
# Clone the Starbucks DevOps project repository
git clone https://github.com/CloudDevOpsHub/Starbucks-Application.git

# Navigate into the project folder
cd Starbucks-Application

# List files to inspect available installation scripts
ls -la
```

### 9.2 Inspecting and Executing the Docker Setup Script

```bash
# Make the Docker installation script executable
chmod +x 2-docker.sh

# Execute the Docker installation script
./2-docker.sh
```

### 9.3 What the Automation Script Does Internally

The shell script performs the following critical tasks:
1. Removes any legacy conflicting Docker packages (`docker`, `docker-engine`, `docker.io`, `containerd`).
2. Configures Docker official apt repository and imports the official Docker GPG key.
3. Installs `docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin`, and `docker-compose-plugin`.
4. Starts and enables the `docker` daemon service.
5. Adds the current user (`ubuntu`) and the `jenkins` system user to the `docker` group:

```bash
# Manual commands if executed outside the script:
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu

# Restart Jenkins service to apply new group membership permissions
sudo systemctl restart jenkins

# Verify Docker engine version
docker --version
```

**Expected Verification Output:**

```text
Docker version 27.x.x or 29.x.x, build xxxxxxx
```

---

## 10. Step 5: Essential Jenkins Plugins & Tool Global Configuration

For Jenkins to recognize, compile, and containerize a Node.js application, specialized plugins and runtimes must be configured within the Jenkins controller.

### 10.1 Required Plugins Installation

1. From the Jenkins dashboard, navigate to: **Manage Jenkins** → **Plugins** → **Available plugins**.
2. Search and select the following plugins:
   - **Eclipse Temurin installer:** Allows automatic dynamic provisioning of specific JDK versions inside build jobs.
   - **NodeJS Plugin:** Enables Node.js runtime management and injects `npm` binaries into pipeline workspaces.
   - **Docker Pipeline:** Provides dynamic DSL syntax (`docker.image`, `docker.build`) and credentials management inside pipelines.
   - **Docker Commons:** Common Docker API libraries for Jenkins integration.
   - **Pipeline: Stage View:** Renders an intuitive visual pipeline execution dashboard displaying status, runtime, and logs per stage.
3. Click **Install**. Check the box: **Restart Jenkins when installation is complete and no jobs are running**.

### 10.2 Global Tool Configuration (JDK 17, Node.js 16, Docker)

Navigate to: **Manage Jenkins** → **Tools**.

#### 1. JDK Configuration
- Click **Add JDK**.
- **Name:** `JDK17` *(Must match the exact string referenced in the Jenkinsfile)*
- Check **Install automatically**.
- Select **Install from adoptium.net**.
- **Version:** `17.0.8.1+1` (or latest JDK 17 release).

#### 2. NodeJS Configuration
- Click **Add NodeJS**.
- **Name:** `node16` *(Must match the exact string referenced in the Jenkinsfile)*
- Check **Install automatically**.
- Select **Install from nodejs.org**.
- **Version:** `NodeJS 16.20.0`.

#### 3. Docker CLI Configuration
- Click **Add Docker**.
- **Name:** `docker` *(Must match the exact string referenced in the Jenkinsfile)*
- Check **Install automatically**.
- Select **Download from docker.com**.
- Click **Save**.

---

## 11. Step 6: Secure Credential Management in Jenkins

In enterprise pipelines, access credentials must never be written in plain text inside a `Jenkinsfile` or committed to Git repositories. Jenkins provides an encrypted credential store to mask sensitive tokens during console logging.

### 11.1 Adding Docker Hub Credentials

1. Navigate to: **Manage Jenkins** → **Credentials** → **System** → **Global credentials (unrestricted)**.
2. Click **Add Credentials**.
3. Fill out the credential form:
   - **Kind:** `Username with password`
   - **Scope:** `Global (Jenkins, nodes, items, all child items)`
   - **Username:** Your Docker Hub username (e.g., `vikascloud` or your personal username).
   - **Password:** Your Docker Hub Personal Access Token (PAT) or account password.
   - **ID:** `docker` *(Critical: This must strictly be named `docker` to bind directly to the `credentialsId: 'docker'` call in the Jenkinsfile).*
   - **Description:** `Docker Hub Global Credentials`
4. Click **Create**.

---

## 12. Step 7: Continuous Integration (CI) Pipeline Implementation

### 12.1 Monorepo Architecture & Code Organization

The application uses a **Monorepo** pattern, housing application source code, Docker build instructions, and Jenkins declarative pipeline files in a single unified Git repository:

```text
Starbucks-Application/
├── public/                 # Static web assets (HTML templates, logos, icons)
├── src/                    # React & Node.js frontend and UI logic components
├── 1-jenkins.sh            # Auxiliary Jenkins automation script
├── 2-docker.sh             # Automated Docker CE setup script
├── Dockerfile              # Container image packaging definition
├── Jenkinsfile             # Declarative CI pipeline configuration
├── package.json            # NPM dependencies and build script definitions
└── README.md               # Application documentation
```

### 12.2 Declarative Jenkinsfile Syntax & Analysis

Below is the complete Declarative CI pipeline code utilized in the project:

```groovy
pipeline {
    agent any

    tools {
        jdk 'JDK17'
        nodejs 'node16'
    }

    environment {
        // Replace with your personal Docker Hub account username
        DOCKER_IMAGE = "vikascloud/starbucks:latest"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/CloudDevOpsHub/Starbucks-Application.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            echo "CI Pipeline execution completed."
        }
        success {
            echo "CI Pipeline succeeded! Docker image successfully published to Docker Hub."
        }
        failure {
            echo "CI Pipeline failed! Check the console output for troubleshooting."
        }
    }
}
```

### 12.3 Running & Verifying the CI Pipeline

1. From the Jenkins dashboard, click **New Item**.
2. **Item Name:** `Starbucks-CI-Pipeline`.
3. Select **Pipeline** and click **OK**.
4. Scroll to the **Pipeline** section at the bottom:
   - **Definition:** `Pipeline script from SCM`
   - **SCM:** `Git`
   - **Repository URL:** `https://github.com/<your-username>/Starbucks-Application.git`
   - **Branch Specifier:** `*/main`
   - **Script Path:** `Jenkinsfile`
5. Click **Save**.
6. Click **Build Now**.
7. Observe the **Pipeline Stage View** as each stage executes sequentially:
   - `Clean Workspace` → `Checkout Code` → `Install Dependencies` → `Build Application` → `Build Docker Image` → `Push Image to Docker Hub`.
8. Log into your **Docker Hub** web console and verify that the `starbucks` repository contains a freshly pushed image with the tag `latest`.

---

## 13. Continuous Delivery vs Continuous Deployment: Enterprise Reality

A major focus of the session was understanding the technical and operational boundaries between **Continuous Delivery** and **Continuous Deployment**.

```text
+----------------------------------------------------------------------------------------------------------+
|                                    CONTINUOUS DELIVERY VS DEPLOYMENT                                     |
+----------------------------------------------------------------------------------------------------------+
|                                                                                                          |
|   [ Code Commit ] ---> [ Build ] ---> [ Automated Tests ] ---> [ Staging / QA ]                          |
|                                                                      |                                   |
|                               +--------------------------------------+                                   |
|                               |                                                                          |
|                               v                                                                          |
|               +-------------------------------+                                                          |
|               |  Manual Approval / Gateway    |  <=== [ CONTINUOUS DELIVERY: ~95% Enterprise Companies ] |
|               |  (CRQ Ticket / Release Mgr)   |       Requires Change Request (CRQ), Maintenance Window  |
|               +---------------+---------------+                                                          |
|                               |                                                                          |
|                               v                                                                          |
|               +-------------------------------+                                                          |
|               | Deployment to Production Host |                                                          |
|               +-------------------------------+                                                          |
|                               ^                                                                          |
|                               |                                                                          |
|    No Human Intervention / Direct Automation <====== [ CONTINUOUS DEPLOYMENT: ~5% SaaS / Tech Giants ]   |
|                                                      Zero manual intervention; code pushed directly to   |
|                                                      production as soon as all tests pass.               |
+----------------------------------------------------------------------------------------------------------+
```

### Comparison Matrix

| Feature | Continuous Delivery | Continuous Deployment |
| :--- | :--- | :--- |
| **Production Trigger** | **Manual Gate / Approval;** requires sign-off from a Release Manager, Product Owner, or QA Lead. | **Fully Automated;** every passing build immediately and automatically deploys to Production. |
| **ITSM & Compliance** | Strict adherence to IT Service Management (ServiceNow, Jira Service Desk, BMC Remedy) Change Requests (CRQ). | Automated change logging via APIs; no manual ticket approval gating the release. |
| **Rollback Strategy** | Controlled maintenance windows with scheduled rollbacks if anomalies appear. | Automated canary analysis, automated health checks, and instant automated rollback triggers. |
| **Industry Adoption** | **~95% of enterprise companies** (Banking, Healthcare, Retail, Telecom, Defense). | **~5% of companies** (High-velocity tech companies like Netflix, Amazon, Meta). |

> **Interview Gold Advice:** When interviewers ask *"What does your company follow?"*, always state **Continuous Delivery**. If you claim Continuous Deployment, the panel will press deeply into automated canary analysis, chaos testing, dynamic feature flagging, and automated error-budget rollbacks.

---

## 14. Step 8: Continuous Delivery (CD) Pipeline Implementation

In this step, a dedicated, decoupled **Continuous Delivery (CD)** pipeline is constructed to pull the Docker image published by the CI pipeline, stop any pre-existing container, and run the new container on port `3000`.

### 14.1 CD Pipeline Script Analysis

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vikascloud/starbucks:latest"
        CONTAINER_NAME = "starbucks-app"
    }

    stages {
        stage('Pull Docker Image') {
            steps {
                sh "docker pull ${DOCKER_IMAGE}"
            }
        }

        stage('Stop & Remove Stale Container') {
            steps {
                // Gracefully stop and remove existing container if running; ignore errors if container doesn't exist
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
            }
        }

        stage('Deploy Application Container') {
            steps {
                // Run new container in detached mode with host-to-container port mapping (3000:3000)
                sh "docker run -d --name ${CONTAINER_NAME} -p 3000:3000 ${DOCKER_IMAGE}"
            }
        }

        stage('Sanity Health Check') {
            steps {
                sh "sleep 5"
                sh "docker ps | grep ${CONTAINER_NAME}"
            }
        }
    }

    post {
        success {
            echo "Application successfully deployed and running on port 3000!"
        }
        failure {
            echo "Deployment failed! Check container logs using: docker logs ${CONTAINER_NAME}"
        }
    }
}
```

### 14.2 Container Lifecycle & Port Mapping

Understanding the command:
```bash
docker run -d --name starbucks-app -p 3000:3000 vikascloud/starbucks:latest
```

- `-d`: Detached mode; runs the container as a background daemon, returning control of the terminal to Jenkins.
- `--name starbucks-app`: Assigns a fixed, human-readable identifier to the running container instance.
- `-p 3000:3000`: Port Forwarding / Port Mapping:
  - **First 3000:** The external host port exposed on the AWS EC2 instance.
  - **Second 3000:** The internal container port on which the Node.js application process listens.
- `vikascloud/starbucks:latest`: The immutable image artifact pulled from Docker Hub.

### 14.3 Live Application Verification on Port 3000

1. Once the CD job completes with a green checkmark, copy your AWS EC2 instance's **Public IPv4 Address**.
2. Open a new browser tab and navigate to: `http://<EC2-PUBLIC-IP>:3000`
3. You should see the fully functional **Starbucks Web Application** displaying products, categories, interactive menus, and an animated shopping cart.

---

## 15. Automating Continuous Deployment via Build Triggers

To transform this setup from manual **Continuous Delivery** into fully automated **Continuous Deployment**, configure downstream build triggers in Jenkins.

### 15.1 Setting Up Downstream Triggers

1. Open the **CD Job** (`Starbucks-CD-Pipeline`) and click **Configure**.
2. Scroll to the **Build Triggers** section.
3. Check the box: **Build after other projects are built**.
4. In the **Projects to watch** text box, enter: `Starbucks-CI-Pipeline`.
5. Select: **Trigger only if build is stable**.
6. Click **Save**.

```text
[ Developer git push ]
         |
         v
[ Starbucks-CI-Pipeline Runs ]
         | (Builds, Tests & Pushes Docker Image)
         |
    (Build Stable?)
         |
        YES
         |
         v
[ Starbucks-CD-Pipeline Triggered Automatically ]
         | (Pulls Image, Recreates Container)
         |
         v
[ Live Application Updated with Zero Manual Touch ]
```

---

## 16. Enterprise Production Scenarios & Advanced Configurations

The session addressed four critical real-world architectural scenarios encountered in commercial production environments:

### 16.1 Scenario 1: Multi-Environment Parameterized Pipeline (Dev, QA, Staging, Prod)

Instead of maintaining 4 duplicate pipelines for Dev, QA, Staging, and Production, a single **Parameterized Pipeline** is created using Jenkins build parameters:

1. In the pipeline configuration, check: **This project is parameterized**.
2. Add a **Choice Parameter**:
   - **Name:** `TARGET_ENV`
   - **Choices:**
     ```text
     DEV
     QA
     STAGING
     PROD
     ```
3. Add a **String Parameter**:
   - **Name:** `TARGET_SERVER_IP`
   - **Default Value:** `10.0.1.50`
4. Modify the Jenkinsfile to conditionally route deployments based on user selection:

```groovy
pipeline {
    agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['DEV', 'QA', 'STAGING', 'PROD'], description: 'Select deployment target')
        string(name: 'TARGET_HOST', defaultValue: '10.0.1.25', description: 'Destination IP')
    }
    stages {
        stage('Deploy to Target Environment') {
            steps {
                script {
                    echo "Deploying Starbucks Container to ${params.ENVIRONMENT} on host ${params.TARGET_HOST}..."
                    if (params.ENVIRONMENT == 'PROD') {
                        // Insert production approval gates or change request checks
                        input message: 'Approve deployment to Production?'
                    }
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@${params.TARGET_HOST} 'docker pull vikascloud/starbucks:latest && docker stop starbucks || true && docker run -d --name starbucks -p 3000:3000 vikascloud/starbucks:latest'"
                }
            }
        }
    }
}
```

### 16.2 Scenario 2: Handling Private GitHub & Docker Registries

When repositories or container registries are private:

1. **Private GitHub Repository:**
   - Generate a **Personal Access Token (PAT)** or an **SSH Key Pair** on GitHub.
   - Store it in Jenkins under: **Credentials** → **Username with password** (for PAT) or **SSH Username with private key**.
   - In the pipeline configuration under SCM, select the stored credential from the dropdown.
2. **Private Docker Registry (Docker Hub / AWS ECR / Azure ACR):**
   - Bind credentials inside the Jenkinsfile using the `withCredentials` block:
   ```groovy
   withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'REG_USER', passwordVariable: 'REG_PASS')]) {
       sh 'echo $REG_PASS | docker login --username $REG_USER --password-stdin'
       sh 'docker pull myorg/private-repo:latest'
   }
   ```

### 16.3 Scenario 3: Remote Target Host Deployment via SSH

In real enterprise clusters, Jenkins controllers rarely run application containers on the same virtual machine. Deployments target remote application servers:

1. Store the remote server's SSH Private Key (`.pem`) in Jenkins credentials (`ID: 'app-server-ssh-key'`).
2. Use the **SSH Agent Plugin** or standard OpenSSH commands:

```groovy
stage('Remote SSH Deploy') {
    steps {
        sshagent(['app-server-ssh-key']) {
            sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@172.31.25.10 << 'EOF'
                    docker pull vikascloud/starbucks:latest
                    docker stop starbucks || true
                    docker rm starbucks || true
                    docker run -d --name starbucks -p 3000:3000 vikascloud/starbucks:latest
            EOF
            '''
        }
    }
}
```

### 16.4 Scenario 4: Monorepo vs Multi-Repo Strategy

- **Monorepo (This Project):** A single repository holds application code in `/src`, web assets in `/public`, and DevOps infrastructure code (`Dockerfile`, `Jenkinsfile`, setup scripts) in the root.
  - *Advantage:* Developers and DevOps engineers share unified version control; every code change is tightly bound to its build definition.
- **Multi-Repo:** Application source code resides in one repo (e.g., `frontend-starbucks`), while CI/CD pipelines, Terraform templates, and Kubernetes manifests reside in an independent `devops-infrastructure` repo.
  - *Advantage:* Granular RBAC permissions; developers cannot alter deployment manifests or cloud parameters.

---

## 17. Smoke Testing & Post-Deployment Sanity Checks

Following deployment, **Smoke Testing** is performed—a lightweight suite of sanity checks verifying that the critical path of the application is operational before routing user traffic:

```bash
# Verify container process is actively running and listening on port 3000
docker ps --filter "name=starbucks-app"

# Perform local HTTP GET request checking for HTTP 200 OK status
curl -I http://localhost:3000

# Inspect real-time application logs inside the container
docker logs --tail 50 starbucks-app
```

**Common Smoke Test Checks:**
1. **Network Binding:** Host port 3000 is open and responding.
2. **HTTP Status Code:** Application returns `HTTP 200 OK` rather than `500 Internal Server Error` or `502 Bad Gateway`.
3. **Asset Delivery:** Static HTML, JavaScript bundles, and CSS stylesheets render without missing asset errors.

---

## 18. Real-World Troubleshooting, Errors & RCA Matrix

During the live implementation, participants encountered several authentic errors. Below is the Root Cause Analysis (RCA) and resolution guide:

| Issue Encountered | Error Output / Symptoms | Root Cause Analysis (RCA) | Corrective Action & Solution |
| :--- | :--- | :--- | :--- |
| **Docker Permission Denied** | `Got permission denied while trying to connect to the Docker daemon socket` | The `jenkins` system user was not a member of the Linux `docker` group. | Run `sudo usermod -aG docker jenkins` followed by `sudo systemctl restart jenkins`. |
| **Docker Hub Push Unauthorized** | `denied: requested access to the resource is denied` at line 32-33 of Jenkinsfile | The Jenkinsfile was attempting to push to `vikascloud/starbucks:latest` instead of the student's own Docker Hub account. | Fork the repository, edit the image tag to `<your-dockerhub-id>/starbucks:latest`, and update Jenkins SCM to point to your fork. |
| **Jenkins Web UI Unreachable** | Browser connection times out when navigating to `http://<IP>:8080` | AWS Security Group inbound rule for TCP port `8080` was missing or bound to incorrect CIDR block. | Add an Inbound Rule to the EC2 Security Group: Type `Custom TCP`, Port `8080`, Source `0.0.0.0/0`. |
| **App Web Page Unreachable** | Browser cannot load `http://<IP>:3000` after successful CD pipeline run | Security Group port `3000` was not allowed in AWS, or Docker container port forwarding was omitted. | Ensure inbound rule allows port `3000` from `0.0.0.0/0`, and verify Docker command includes `-p 3000:3000`. |
| **Node.js Deprecation Warning** | `npm WARN deprecated ... engine unsupported` during build stage | Certain nested NPM dependencies threw deprecation notices on Node 16 runtime. | Non-fatal warning; pipeline continued and succeeded. For future builds, upgrade NodeJS tool to v18/v20 LTS. |
| **Container Name Conflict** | `docker: Error response from daemon: Conflict. The container name "/starbucks-app" is already in use` | A prior deployment container was already running with that name. | Add idempotent cleanup before `docker run`: `docker stop starbucks-app \|\| true && docker rm starbucks-app \|\| true`. |

---

## 19. Infrastructure Teardown & Cloud Cost Optimization

AWS EC2 instances accrue compute and block storage costs when left running. Always clean up resources when completing practice sessions:

```bash
# Stop running application containers
docker stop starbucks-app && docker rm starbucks-app

# Clean up unused Docker image layers and cache
docker system prune -af
```

### In the AWS Console:
1. Navigate to the **EC2 Instances Dashboard**.
2. Select `Starbucks-CICD-Server`.
3. Click **Instance State** → **Terminate Instance**.
4. Confirm termination to release the attached EBS volume and eliminate cloud billing.

---

## 20. Project in One Page

```text
====================================================================================================
PROJECT 1: END-TO-END CI/CD AUTOMATION ON AWS USING JENKINS, DOCKER & NODE.JS
====================================================================================================
1. Infrastructure:    AWS EC2 Ubuntu 24.04 LTS (t2.medium/t2.large, 40GB EBS, SG: 22, 8080, 3000).
2. Automation Server: Jenkins LTS installed on OpenJDK 21, unlocked via initialAdminPassword.
3. Container Engine:  Docker CE installed via automated shell script (2-docker.sh); jenkins user in docker group.
4. Tool Plugins:      Eclipse Temurin (JDK 17), NodeJS Plugin (Node 16.20.0), Docker Pipeline, Stage View.
5. Secrets Mgmt:      Docker Hub PAT stored in Jenkins Global Credentials (ID: 'docker').
6. Monorepo App:      Starbucks React/Node.js web application (code in /src, Dockerfile, Jenkinsfile in root).
7. CI Pipeline:       Declarative pipeline: cleanWs -> git clone -> npm install -> npm run build -> 
                      docker build -t <user>/starbucks:latest -> docker push to Docker Hub.
8. Delivery vs Deploy:Delivery requires manual approval/CRQ (95% enterprises); Deployment is zero-touch (5%).
9. CD Pipeline:       Pulls latest image, stops existing container (idempotent), runs container on -p 3000:3000.
10. Trigger Linking:  CD Job configured with "Build after other projects are built" (Starbucks-CI-Pipeline).
11. Verification:     Live UI accessed on http://<EC2-IP>:3000; Smoke tests verify HTTP 200 & container health.
====================================================================================================
```

---

## 21. Professional Resume Points & Real-Time Job Roles

Use these production-tested bullet points to showcase this project on your resume for **DevOps Engineer**, **Cloud Engineer**, or **Site Reliability Engineer (SRE)** roles:

- **Architected and implemented an end-to-end, open-source CI/CD automation pipeline** on AWS EC2, slashing software release lead times by 75% for microservices applications.
- **Configured and hardened a self-hosted Jenkins LTS cluster** on Ubuntu Linux, integrating global toolchains including OpenJDK 17, Node.js 16 runtimes, and Docker CE engines.
- **Engineered Declarative Jenkinsfiles utilizing the Monorepo strategy**, orchestrating static code compilation, automated NPM dependency management, and container image generation.
- **Implemented zero-downtime containerized application deployments** utilizing Docker port mapping (`-p 3000:3000`), container lifecycle management, and automated sanity smoke tests.
- **Established strict credential isolation and security best practices**, leveraging Jenkins Global Credential Stores and secret masking to prevent access token exposure across CI/CD logs.
- **Constructed parameterized multi-environment pipelines** supporting seamless, gated deployments across Dev, QA, Staging, and Production tiers with automated downstream triggers.
- **Participated in on-call incident triage and ITSM Change Request (CRQ) governance**, resolving build failures, Docker socket permission issues, and container port conflicts.

---

## 22. Top 10 Technical Interview Questions & Answers

### Q1: Why did you install OpenJDK 21 on the host, but configure JDK 17 inside Jenkins Tools?
**Answer:** OpenJDK 21 was installed on the operating system host as a required system dependency to start and run the Jenkins LTS controller daemon itself. Inside Jenkins, JDK 17 was configured in **Global Tool Configuration** via Eclipse Temurin because the specific application build tools and compilation plugins strictly required a Java 17 runtime environment. Jenkins allows multiple independent JDK versions to coexist without collision.

### Q2: What is the fundamental difference between Continuous Delivery and Continuous Deployment?
**Answer:** Both automate the build, test, and release artifact generation stages. The crucial distinction lies in the deployment to production:
- **Continuous Delivery:** The code is always in a deployable state, but deployment to production requires an explicit manual trigger or business approval (e.g., Change Request / CRQ sign-off). Approximately 95% of enterprises use this model.
- **Continuous Deployment:** Every change that passes automated CI tests is immediately and automatically deployed to production with zero human intervention. Approximately 5% of high-velocity tech companies use this model.

### Q3: How does Jenkins mask passwords and secrets during pipeline execution?
**Answer:** Jenkins stores secrets encrypted using AES-128 keys on the controller. When a pipeline accesses secrets via the `withCredentials([usernamePassword(...)])` block, the Jenkins pipeline engine wraps the execution block in a filtering stream. Any occurrence of the secret variable's plain text value printed to standard output or standard error is dynamically intercepted and replaced with `****` in the console log.

### Q4: Why is a Declarative Pipeline preferred over a Scripted Pipeline?
**Answer:** Declarative Pipelines (`pipeline { ... }`) provide a structured, predefined schema that enforces readability, standardized stages, built-in post actions (`always`, `success`, `failure`), and automated syntax validation before execution. Scripted Pipelines (`node { ... }`) rely on Groovy code, which offers greater imperative programming power but suffers from steep learning curves, poor standardization, and difficulty in maintenance across large engineering teams.

### Q5: What does the Docker port mapping `-p 3000:3000` signify?
**Answer:** In Docker, containers run inside isolated network namespaces with private IP addresses inaccessible from the host's external network by default. The `-p <HOST_PORT>:<CONTAINER_PORT>` flag sets up an iptables NAT forwarding rule on the host OS. Traffic arriving on the EC2 host's physical network interface on port `3000` is forwarded to port `3000` inside the container where the Node.js application is listening.

### Q6: What caused the `Got permission denied while trying to connect to the Docker daemon socket` error?
**Answer:** The Docker daemon communicates over a Unix socket (`/var/run/docker.sock`) owned by the `root` user and the `docker` group. When Jenkins runs build steps, it executes under the unprivileged `jenkins` system service user account. Because the `jenkins` user initially was not a member of the `docker` group, the operating system rejected access to the socket. Adding `jenkins` to the `docker` group (`usermod -aG docker jenkins`) and restarting Jenkins resolved the issue.

### Q7: Why should the Continuous Delivery (CD) pipeline be decoupled from the Continuous Integration (CI) pipeline?
**Answer:** Decoupling CI and CD maintains the single responsibility principle:
- **CI focuses on testing and artifact generation:** It runs frequently on every developer branch, feature PR, and commit.
- **CD focuses on environment provisioning and state management:** It runs selectively based on approvals, schedules, or release windows. Decoupling ensures that a deployment failure does not corrupt or invalidate a successful code build artifact, and allows identical artifacts to be promoted across Dev, QA, and Prod independently.

### Q8: What is a Monorepo and what are its trade-offs?
**Answer:** A Monorepo is a version control pattern where multiple components—such as application frontend code, backend services, Dockerfiles, and CI/CD pipeline scripts—coexist within a single repository.
- **Benefits:** Atomic commits across code and infrastructure; centralized visibility; simplified dependency synchronization.
- **Drawbacks:** Repository size grows rapidly; potential merge conflicts; granular role-based access control (RBAC) is harder to enforce without specialized branch policies.

### Q9: How do you ensure that a previous container is cleaned up before launching a new one?
**Answer:** By implementing idempotent shell commands prior to container creation:
```bash
docker stop starbucks-app || true
docker rm starbucks-app || true
```
The `|| true` operator ensures that if the container is not running or does not exist (such as during the first run), the shell command exits with a return code of `0`, preventing Jenkins from prematurely aborting the pipeline.

### Q10: How do you verify the health of a containerized application programmatically in a pipeline?
**Answer:** Add a post-deployment verification stage using `curl` or automated health check probes:
```groovy
stage('Health Sanity Check') {
    steps {
        sh 'curl --fail --retry 5 --retry-delay 3 http://localhost:3000 || exit 1'
    }
}
```
The `--fail` flag ensures `curl` returns an error status code on HTTP 4xx/5xx responses, triggering a pipeline failure if the application fails to boot.

---

## 23. Top 10 Scenario-Based Production Interview Questions & Solutions

### Scenario 1: Preventing Deployment When Docker Hub Authentication Fails
**Question:** During a CI pipeline run, the Docker build completes, but the push stage fails due to rate limits or invalid registry credentials. However, the subsequent CD job still triggers and restarts the container using a stale image. How do you prevent this?
**Solution:**
1. Configure the downstream build trigger to run **strictly when the upstream build is STABLE** (`Trigger only if build is stable`).
2. Utilize unique image tags bound to the commit hash (`${GIT_COMMIT}`) or Jenkins build number (`${BUILD_NUMBER}`) rather than the mutable `latest` tag:
   ```groovy
   DOCKER_IMAGE = "vikascloud/starbucks:${env.BUILD_NUMBER}"
   ```
3. If the CI push fails, the CI job transitions to `FAILURE`, aborting the downstream trigger. If the CD job cannot find the exact build-numbered image tag, it halts immediately rather than deploying stale code.

### Scenario 2: Zero-Downtime Deployment on Single Host
**Question:** In our project, running `docker stop` and `docker rm` caused 5-10 seconds of downtime before the new container started. How would you achieve zero downtime on a single EC2 instance without Kubernetes?
**Solution:**
Implement a **Blue-Green Port Switching** deployment strategy using a reverse proxy (e.g., NGINX):
1. Run the existing production container on port `3001` (Blue).
2. Spin up the new application container on port `3002` (Green).
3. Execute smoke tests against `http://localhost:3002`.
4. If healthy, update the NGINX upstream configuration block from `3001` to `3002` and run `nginx -s reload`.
5. Safely terminate the old container on port `3001` with zero dropped requests.

### Scenario 3: Secret Leakage Mitigation in Pull Requests
**Question:** A junior developer creates a pull request modifying the Jenkinsfile to print environment variables (`sh 'env'`) in an attempt to debug a build, which risks dumping secrets to the console log. How do you safeguard production secrets?
**Solution:**
1. Avoid storing global credentials at the folder level for untrusted pull requests. Use Jenkins **Credentials Binding** with `withCredentials` which automatically masks bound variables.
2. In Jenkins Multibranch / PR pipelines, enable the setting: **"Filter pull requests from untrusted contributors"** and disable access to production credentials on non-main branches.
3. Integrate static code analysis tools (such as Gitleaks or TruffleHog) in the earliest stage of the pipeline to detect and fail builds attempting to log or commit secret tokens.

### Scenario 4: Jenkins Disk Exhaustion (No Space Left on Device)
**Question:** After 3 weeks in production, your Jenkins EC2 server crashes with `java.io.IOException: No space left on device`. Inspection shows Docker images and Jenkins workspaces consumed 100% of the 40GB root disk. How do you permanently resolve this?
**Solution:**
1. **Automated Docker Garbage Collection:** Add a post-build or nightly cron job executing `docker image prune -af --filter "until=72h"` and `docker builder prune -af`.
2. **Workspace Cleanup:** Include the `cleanWs()` step in the `post { always { ... } }` block of every Jenkinsfile.
3. **Build Discarder Configuration:** Configure Jenkins project settings to keep only the last 10 builds and discard older logs/artifacts:
   ```groovy
   options {
       buildDiscarder(logRotator(numToKeepStr: '10'))
   }
   ```
4. **CloudWatch Alarm:** Set up an AWS CloudWatch Disk Utilization alarm triggering an alert at 80% capacity.

### Scenario 5: Multi-Tenant Kubernetes Node Disk Pressure
**Question:** The session discussed an interview scenario where a Kubernetes worker node enters `NotReady` state with `DiskPressure=True`. How do you troubleshoot and fix this in production?
**Solution:**
1. Run `kubectl describe node <node-name>` and observe the `Conditions` table. Look for `DiskPressure=True`.
2. SSH into the troubled node and inspect disk usage: `df -h` and `docker system df`.
3. Check Kubelet system logs: `journalctl -u kubelet -f` to identify if log rotation or container image garbage collection thresholds (default 85% high watermark) were breached.
4. Clean orphaned container layers and unused images: `crictl rmi --prune`.
5. In cloud environments (EKS/GKE), adjust the Auto Scaling Node Group or provision persistent volumes on external block storage (EBS/GCP Persistent Disk) rather than the root filesystem.

### Scenario 6: Deploying Across Multi-Cloud Environments
**Question:** Your organization mandates building containers on AWS Jenkins but deploying the resulting application into an Azure Web App or GCP GKE cluster. How do you architect this pipeline?
**Solution:**
1. Use an intermediate, cloud-agnostic container registry (such as Docker Hub, Quay.io, or Harbor) accessible across cloud boundaries.
2. Store cloud provider service principal credentials securely inside Jenkins:
   - For Azure: Store Azure Service Principal (Client ID, Secret, Tenant ID).
   - For GCP: Store Service Account JSON key.
3. Add a specialized deployment stage inside the Jenkinsfile utilizing CLI tools or Terraform:
   ```groovy
   stage('Deploy to Azure Web App') {
       steps {
           withCredentials([azureServicePrincipal('azure-sp-creds')]) {
               sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID'
               sh 'az webapp create --resource-group prod-rg --plan app-plan --name starbucks-web --deployment-container-image-name vikascloud/starbucks:latest'
           }
       }
   }
   ```

### Scenario 7: Integrating ITSM Change Management into Continuous Delivery
**Question:** An enterprise client requires that no deployment can occur in Production without an approved ServiceNow Change Request (CRQ) number that is active for a 4-hour window. How do you implement this in Jenkins?
**Solution:**
1. In the production deployment stage, inject a Jenkins input parameter:
   ```groovy
   stage('ITSM Gate') {
       steps {
           script {
               def crqNumber = input(
                   message: 'Enter Approved ServiceNow CRQ Number:',
                   parameters: [string(name: 'CRQ_ID', description: 'CRQ-XXXXXX')]
               )
               // Make API call to ServiceNow validating CRQ status
               def response = httpRequest(
                   url: "https://instance.service-now.com/api/now/table/change_request?number=${crqNumber}",
                   customHeaders: [[name: 'Authorization', value: 'Basic ...']]
               )
               if (!response.content.contains('"state":"scheduled"')) {
                   error "Change Request ${crqNumber} is not approved or currently active!"
               }
           }
       }
   }
   ```

### Scenario 8: Build Pipeline Optimization for Long NPM Builds
**Question:** The `npm install` and `npm run build` stages take 8 minutes on every pipeline execution because dependencies are downloaded from the internet repeatedly. How do you optimize build time down to under 2 minutes?
**Solution:**
1. **Docker Layer Caching:** Leverage Docker build caching by copying `package.json` and `package-lock.json` before copying the rest of the source code in the Dockerfile:
   ```dockerfile
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run build
   ```
2. **Persistent NPM Cache:** Mount an EC2 host cache directory into the build workspace:
   ```groovy
   sh 'npm config set cache /var/cache/npm --global'
   sh 'npm ci --prefer-offline'
   ```
3. **Upgrade Hardware:** Scale the build instance from `t2.medium` to a compute-optimized instance (e.g., `c5.large` or `c6i.large`) with high-speed gp3 IOPS.

### Scenario 9: Handling Concurrent Pipeline Runs Clashing on Host Ports
**Question:** Two developers commit changes simultaneously. Two parallel pipeline executions attempt to run `docker run -d -p 3000:3000 ...` on the same EC2 instance, causing the second build to crash with `port is already allocated`. How do you handle this?
**Solution:**
1. **Disable Concurrent Builds:** In the Jenkinsfile options, specify:
   ```groovy
   options {
       disableConcurrentBuilds()
   }
   ```
2. **Dynamic Port Allocation:** If testing feature branches in parallel, allocate dynamic host ports based on the Jenkins executor number or build number:
   ```groovy
   environment {
       APP_PORT = "${3000 + env.EXECUTOR_NUMBER.toInteger()}"
   }
   steps {
       sh "docker run -d --name starbucks-${env.BUILD_NUMBER} -p ${APP_PORT}:3000 ${DOCKER_IMAGE}"
   }
   ```

### Scenario 10: Automatic Rollback on Production Failure
**Question:** After a Continuous Deployment pipeline updates the container in production, the application crashes 30 seconds later due to an unhandled runtime exception. How do you design an automated recovery mechanism?
**Solution:**
1. Tag previous successful images with a `stable` or `backup` tag before deploying the new version:
   ```bash
   docker tag vikascloud/starbucks:stable vikascloud/starbucks:backup || true
   ```
2. Add a robust health-check verification stage with a timeout and retry loop:
   ```groovy
   stage('Post-Deploy Verification') {
       steps {
           retry(3) {
               sleep 10
               sh 'curl --fail http://localhost:3000/api/health || exit 1'
           }
       }
   }
   ```
3. Implement automated recovery in the `post { failure { ... } }` block:
   ```groovy
   post {
       failure {
           echo "New deployment failed health checks! Rolling back to backup container..."
           sh 'docker stop starbucks-app || true'
           sh 'docker run -d --name starbucks-app -p 3000:3000 vikascloud/starbucks:backup'
       }
   }
   ```
