# Project 4: Enterprise DevSecOps CI/CD Pipeline for 3-Tier Application Deployment

[![Module: DevSecOps CI/CD](https://img.shields.io/badge/Module-DevSecOps%20CI%2FCD-D33833?style=for-the-badge&logo=jenkins&logoColor=white)](README.md)
[![Cloud: AWS EC2](https://img.shields.io/badge/Cloud-AWS%20EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![Security: Trivy & SonarQube](https://img.shields.io/badge/Security-Trivy%20%26%20SonarQube-4E9A06?style=for-the-badge&logo=sonarqube&logoColor=white)](README.md)
[![Containers: Docker](https://img.shields.io/badge/Containers-Docker%20%26%20Hub-2496ED?style=for-the-badge&logo=docker&logoColor=white)](README.md)
[![Database: MySQL](https://img.shields.io/badge/Database-MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents

1. [Project Overview & Business Problem](#1-project-overview--business-problem)
2. [What is DevSecOps? (The Shift-Left Principle)](#2-what-is-devsecops-the-shift-left-principle)
3. [The Golden Image Concept & Compliance Governance](#3-the-golden-image-concept--compliance-governance)
4. [3-Tier Application Architecture Breakdown](#4-3-tier-application-architecture-breakdown)
5. [End-to-End DevSecOps Pipeline Flow](#5-end-to-end-devsecops-pipeline-flow)
6. [Prerequisites & AWS EC2 Host Setup](#6-prerequisites--aws-ec2-host-setup)
7. [Step 1: Security Group & Port Hardening](#7-step-1-security-group--port-hardening)
8. [Step 2: Server Host Preparation](#8-step-2-server-host-preparation)
9. [Step 3: Jenkins Server Installation & Initial Unlock](#9-step-3-jenkins-server-installation--initial-unlock)
10. [Step 4: Automated Tool Provisioning via Shell Scripts](#10-step-4-automated-tool-provisioning-via-shell-scripts)
    - [10.1 Docker Engine Installation (`second.sh` / `docker.sh`)](#101-docker-engine-installation-secondsh--dockersh)
    - [10.2 Python Runtime & Group Permissions Setup (`3.sh`)](#102-python-runtime--group-permissions-setup-3sh)
    - [10.3 Aquasec Trivy Security Scanner Installation (`4.sh`)](#103-aquasec-trivy-security-scanner-installation-4sh)
    - [10.4 SonarQube Server Deployment via Docker](#104-sonarqube-server-deployment-via-docker)
11. [Step 5: Jenkins Plugins Installation](#11-step-5-jenkins-plugins-installation)
12. [Step 6: Global Tool Configuration in Jenkins](#12-step-6-global-tool-configuration-in-jenkins)
13. [Step 7: Credentials & Webhook Integration](#13-step-7-credentials--webhook-integration)
    - [13.1 Docker Hub Global Credentials](#131-docker-hub-global-credentials)
    - [13.2 SonarQube Security Token Creation](#132-sonarqube-security-token-creation)
    - [13.3 SonarQube Server Integration in Jenkins](#133-sonarqube-server-integration-in-jenkins)
    - [13.4 SonarQube Quality Gate Webhook to Jenkins](#134-sonarqube-quality-gate-webhook-to-jenkins)
14. [Step 8: Complete Declarative Jenkinsfile (The DevSecOps Pipeline)](#14-step-8-complete-declarative-jenkinsfile-the-devsecops-pipeline)
15. [Step 9: Pipeline Execution & Stage-by-Stage Verification](#15-step-9-pipeline-execution--stage-by-stage-verification)
16. [Step 10: Live 3-Tier Application Verification & Database Test](#16-step-10-live-3-tier-application-verification--database-test)
17. [Step 11: Trivy Vulnerability Scanning Deep-Dive](#17-step-11-trivy-vulnerability-scanning-deep-dive)
18. [Troubleshooting & Debugging Matrix (Real Issues from Session)](#18-troubleshooting--debugging-matrix-real-issues-from-session)
19. [Infrastructure Cleanup & Cost Optimization](#19-infrastructure-cleanup--cost-optimization)
20. [Project in One Page](#20-project-in-one-page)
21. [Top 10 Technical Interview Questions & Answers](#21-top-10-technical-interview-questions--answers)
22. [Top 10 Scenario-Based Production Interview Questions & Solutions](#22-top-10-scenario-based-production-interview-questions--solutions)

---

## 1. Project Overview & Business Problem

In standard DevOps, software pipelines focus primarily on speed: checking out code, building binaries, packaging Docker images, and deploying quickly to servers. However, deploying code without automated security scanning introduces severe risks:
- Vulnerable third-party libraries and dependencies (e.g., Log4j, outdated npm/pip packages).
- Docker images pulled blindly from public registries containing unpatched CVE vulnerabilities.
- Hardcoded secrets, API keys, and SQL injection flaws inside application code.
- Failing regulatory compliance and data governance standards.

This capstone project implements an **Enterprise DevSecOps Continuous Integration & Continuous Deployment (CI/CD) Pipeline** on **AWS EC2**. The pipeline builds, tests, scans, packages, and deploys a real-world **3-Tier Web Application (Online Examination System)** while enforcing security gates at every single stage before code reaches the runtime environment.

### Core Objectives
1. Implement **Shift-Left Security** in Jenkins using automated code quality and vulnerability scanners.
2. Configure **SonarQube** for Static Application Security Testing (SAST) and enforce SonarQube Quality Gates.
3. Integrate **Aquasec Trivy** for Software Composition Analysis (SCA) filesystem scanning and container image scanning against the Common Vulnerabilities and Exposures (CVE) database.
4. Containerize the application layers using **Docker** and publish hardened images to **Docker Hub**.
5. Deploy and verify a fully operational **3-Tier Application** (Frontend UI, Middleware Backend, and MySQL Database container) on port `5000`.
6. Inspect database persistence directly inside the MySQL container via Docker CLI.

---

## 2. What is DevSecOps? (The Shift-Left Principle)

**DevSecOps** stands for **Development, Security, and Operations**. It is the philosophy and practice of integrating security testing into every phase of the Software Development Life Cycle (SDLC), rather than treating security as an isolated audit at the end.

```text
Traditional DevOps Flow (Vulnerable):
[ Code ] ---> [ Build ] ---> [ Deploy to Production ] ---> [ Security Audit / Breach! ]
                                                            (Costly, risky fixes on-the-fly)

DevSecOps Flow (Shift-Left Security):
[ Code ] ---> [ SAST (SonarQube) ] ---> [ SCA (Trivy FS) ] ---> [ Docker Build ] ---> [ Image Scan (Trivy) ] ---> [ Quality Gate ] ---> [ Deploy ]
  ^                 |                         |                                              |                         |
  |                 v                         v                                              v                         v
  +----------- Block on Flaw <----------- Block on CVE <-------------------------------- Block on CVE <------- Fail Pipeline
```

### Key Pillars
- **Pre-Check vs On-the-Fly:** Just like preparing for an exam before entering the examination hall, security checks must pass *before* deployment.
- **Fail Fast, Fail Cheap:** Identifying a vulnerability during code commit costs 10x to 100x less to remediate than patching an exploited breach in production.
- **Clear Role Boundaries:**
  - **DevOps/DevSecOps Engineers:** Build the automation pipelines, integrate scanners, configure quality gates, establish dashboards, and maintain infrastructure.
  - **Developers:** Own the code, remediate code smells, fix bugs, upgrade vulnerable libraries, and resolve reported CVEs.

---

## 3. The Golden Image Concept & Compliance Governance

During the live session, real-world security scenarios were discussed to illustrate why container scanning is non-negotiable:

### 1. The Danger of Random Docker Hub Images
Developers frequently search Docker Hub for an image (e.g., MongoDB, Redis, or Node) that "just works" when official images fail. These unvetted public images frequently contain:
- Unpatched Linux kernel vulnerabilities.
- Cryptomining malware or embedded backdoors.
- Incompatible SSL/TLS ciphers.

### 2. The Golden Image Standard
In enterprise environments, DevOps teams maintain **Golden Images**:
- Verified, standardized, hardened base images (e.g., minimal Alpine or Ubuntu LTS).
- Pre-scanned with zero High or Critical vulnerabilities.
- Certified by internal security and compliance teams.
- Stored exclusively in private container registries (AWS ECR, Azure ACR, or JFrog Artifactory).

### 3. Data Residency & Regulatory Compliance
Certain industries (e.g., State Bank of India / Banking, Healthcare, Defense) operate under strict regulatory and statutory requirements:
- Data must never leave designated geographical boundaries (e.g., Indian data centers).
- Pipeline tools and target deployments must enforce region-locking and encryption at rest and in transit.

---

## 4. 3-Tier Application Architecture Breakdown

The project deploys an interactive **3-Tier Online Examination & Assessment System**:

```text
+---------------------------------------------------------------------------------------+
|                                    AWS EC2 HOST                                       |
|                                                                                       |
|  [ Tier 1: Presentation Layer ]                                                        |
|    - Online Exam Web Portal (Port 5000)                                               |
|    - Student enters Name, Email, Gender and submits 10 test questions                 |
|                                                                                       |
|                               |                                                       |
|                               v HTTP Requests / API Calls                             |
|                                                                                       |
|  [ Tier 2: Application / Middleware Layer ]                                           |
|    - Exam Logic Engine / Backend API Router                                          |
|    - Validates answers, calculates test scores, formats payloads                      |
|                                                                                       |
|                               |                                                       |
|                               v TCP Connection (Port 3306)                            |
|                                                                                       |
|  [ Tier 3: Database / Persistence Layer ]                                             |
|    - MySQL 8.x Container (`devops_exam` Database)                                     |
|    - Stores candidate profiles, assessment submissions, and evaluation scores         |
+---------------------------------------------------------------------------------------+
```

---

## 5. End-to-End DevSecOps Pipeline Flow

```text
+-------------------------------------------------------------------------------------------------------+
|                                        JENKINS PIPELINE WORKFLOW                                       |
|                                                                                                       |
|  1. [ Checkout SCM ]        Pulls source code and Docker configs from GitHub repository              |
|           |                                                                                           |
|           v                                                                                           |
|  2. [ Trivy FS Scan ]       Scans application files and dependencies for CVE vulnerabilities          |
|           |                                                                                           |
|           v                                                                                           |
|  3. [ SonarQube Analysis ]  Static Application Security Testing (SAST) for bugs, smells & vulnerabilities|
|           |                                                                                           |
|           v                                                                                           |
|  4. [ Quality Gate ]        Halts pipeline if code fails predefined SonarQube quality criteria        |
|           |                                                                                           |
|           v                                                                                           |
|  5. [ Docker Build ]        Builds container image with Dockerfile using current build tag            |
|           |                                                                                           |
|           v                                                                                           |
|  6. [ Trivy Image Scan ]    Deep-scans container image layers for High & Critical CVEs                |
|           |                                                                                           |
|           v                                                                                           |
|  7. [ Push to Docker Hub ]  Authenticates with Docker Hub and publishes tagged container image        |
|           |                                                                                           |
|           v                                                                                           |
|  8. [ Deploy Application ]  Runs 3-Tier container stack exposing web interface on Port 5000           |
+-------------------------------------------------------------------------------------------------------+
```

---

## 6. Prerequisites & AWS EC2 Host Setup

To ensure sufficient CPU and memory for Jenkins, SonarQube (JVM-based), Docker, and database containers running concurrently:

| Requirement | Recommended Specification |
| :--- | :--- |
| **Cloud Provider** | AWS (Amazon Web Services) |
| **Instance Type** | `t2.large` or `t3.large` (2 vCPU, 8 GB RAM) — *Minimum `t2.medium` (4 GB RAM)* |
| **Operating System** | Ubuntu 24.04 LTS or Ubuntu 22.04 LTS (AMI: 64-bit x86) |
| **Storage (EBS)** | 30 GB gp3 General Purpose SSD |
| **Authentication** | SSH Key Pair (`.pem` file) |

---

## 7. Step 1: Security Group & Port Hardening

Attach an AWS Security Group with the following inbound rules:

| Port Number | Protocol | Source | Purpose |
| :--- | :--- | :--- | :--- |
| **22** | TCP | `0.0.0.0/0` (or Admin IP) | SSH Remote Server Access |
| **8080** | TCP | `0.0.0.0/0` | Jenkins Automation Server Web UI |
| **9000** | TCP | `0.0.0.0/0` | SonarQube Code Quality Server Web UI |
| **5000** | TCP | `0.0.0.0/0` | 3-Tier Web Application (Exam Portal) |
| **3000** | TCP | `0.0.0.0/0` | Secondary App / Monitoring Port |
| **25** | TCP | `0.0.0.0/0` | SMTP Mail Server Alerts (Optional) |

---

## 8. Step 2: Server Host Preparation

Connect to your EC2 instance using SSH:

```bash
ssh -i your-key.pem ubuntu@<EC2_PUBLIC_IP>
```

Switch to root privileges and update all system packages:

```bash
# Elevate to root
sudo -i

# Update package index and upgrade existing packages
apt update -y && apt upgrade -y
```

Clone the project repository containing the 3-tier application and automation scripts:

```bash
# Clone the repository
git clone https://github.com/CloudDevOpsHub/3-tier-application.git

# Navigate into the project directory
cd 3-tier-application

# Verify contents
ls -la

# Grant executable permissions to all shell scripts
chmod +x *.sh
```

---

## 9. Step 3: Jenkins Server Installation & Initial Unlock

### 9.1 Install Java (OpenJDK 21) & Jenkins LTS

```bash
# Install OpenJDK 21 LTS
apt install openjdk-21-jdk -y
java -version

# Add Jenkins official repository key and list entry
wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update repository metadata and install Jenkins
apt update -y
apt install jenkins -y

# Start and enable Jenkins service
systemctl enable --now jenkins
systemctl status jenkins --no-pager
```

### 9.2 Unlock Jenkins & Setup Admin Account

1. Open your browser and navigate to: `http://<EC2_PUBLIC_IP>:8080`
2. Retrieve the initial administrator password:
   ```bash
   cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
3. Paste the password into the Jenkins unlock prompt and click **Continue**.
4. Select **Install suggested plugins** and allow the installation to finish.
5. Create First Admin User (e.g., Username: `admin`, Password: `YourSecurePassword`, Name: `Admin`, Email: `admin@example.com`).
6. Complete instance configuration and click **Start using Jenkins**.

---

## 10. Step 4: Automated Tool Provisioning via Shell Scripts

To accelerate setup, execute the modular automation scripts provided in the repository:

### 10.1 Docker Engine Installation (`second.sh` / `docker.sh`)

Install Docker Engine, Docker CLI, and containerd:

```bash
# Run Docker installation script
./second.sh
# Alternatively, if named docker.sh:
# ./docker.sh

# Verify Docker version
docker --version
systemctl status docker --no-pager
```

*Under the hood, `second.sh` executes:*
```bash
apt-get install -y ca-certificates curl gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update -y
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 10.2 Python Runtime & Group Permissions Setup (`3.sh`)

Install Python libraries and grant Docker daemon access to the `jenkins` user:

```bash
# Execute Script 3
./3.sh
```

*Under the hood, `3.sh` executes:*
```bash
# Install Python3, pip and build essentials
apt install -y python3 python3-pip python3-venv

# Add jenkins and ubuntu users to docker group
usermod -aG docker jenkins
usermod -aG docker ubuntu

# Restart Jenkins daemon to reload group memberships
systemctl restart jenkins
```

> **CRITICAL:** If `jenkins` is not added to the `docker` group, any pipeline step executing `docker build` or `docker run` will fail with: `Got permission denied while trying to connect to the Docker daemon socket`.

### 10.3 Aquasec Trivy Security Scanner Installation (`4.sh`)

Install Trivy to perform filesystem and container image vulnerability scans:

```bash
# Execute Script 4
./4.sh

# Verify Trivy installation
trivy --version
```

*Under the hood, `4.sh` executes:*
```bash
apt-get install -y wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | tee -a /etc/apt/sources.list.d/trivy.list
apt-get update -y
apt-get install -y trivy
```

### 10.4 SonarQube Server Deployment via Docker

Deploy SonarQube Community Edition in an isolated Docker container:

```bash
# Run SonarQube LTS container mapped to port 9000
docker run -d --name sonarqube -p 9000:9000 --restart always sonarqube:lts-community

# Verify container status
docker ps
```

Access the SonarQube dashboard in your browser: `http://<EC2_PUBLIC_IP>:9000`
- Default Username: `admin`
- Default Password: `admin`
- On first login, update the administrator password to a secure new password.

---

## 11. Step 5: Jenkins Plugins Installation

Navigate to **Manage Jenkins** > **Plugins** > **Available plugins** and install the following plugins:

1. **SonarQube Scanner** (`sonar`)
2. **Docker Pipeline** (`docker-workflow`)
3. **Docker** (`docker-plugin`)
4. **Pipeline: Stage View** (`pipeline-stage-view`)

Check **Restart Jenkins when installation is complete and no jobs are running** or trigger a safe restart manually:
```bash
systemctl restart jenkins
```

---

## 12. Step 6: Global Tool Configuration in Jenkins

Navigate to **Manage Jenkins** > **Tools**:

### 1. SonarQube Scanner Installation
- Scroll to **SonarQube Scanner installations**.
- Click **Add SonarQube Scanner**.
- **Name:** `sonar-scanner`
- Check **Install automatically**.
- **Version:** Select `SonarQube Scanner 7.1.0.489` (or latest LTS).

### 2. Docker Installation
- Scroll to **Docker installations**.
- Click **Add Docker**.
- **Name:** `docker`
- Check **Install automatically** (or specify `/usr/bin/docker`).
- Click **Save**.

---

## 13. Step 7: Credentials & Webhook Integration

### 13.1 Docker Hub Global Credentials
1. Navigate to **Manage Jenkins** > **Credentials** > **System** > **Global credentials (unrestricted)** > **Add Credentials**.
2. **Kind:** `Username with password`
3. **Username:** Your Docker Hub username (e.g., `johndoe`)
4. **Password:** Your Docker Hub Personal Access Token (PAT)
5. **ID:** `docker-cred`
6. **Description:** `Docker Hub Global Credentials`
7. Click **Create**.

### 13.2 SonarQube Security Token Creation
1. Open SonarQube Web UI: `http://<EC2_PUBLIC_IP>:9000`
2. Navigate to: **Administration** > **Security** > **Users**.
3. Click the tokens icon (or ellipsis menu) next to the `Administrator` user.
4. **Token Name:** `jenkins-sonar-token`
5. **Type:** `User Token`
6. **Expires in:** `90 days` (or 365 days)
7. Click **Generate** and **copy the generated token immediately**.

### 13.3 SonarQube Server Integration in Jenkins
1. Go to Jenkins: **Manage Jenkins** > **Credentials** > **System** > **Global credentials** > **Add Credentials**.
   - **Kind:** `Secret text`
   - **Secret:** Paste the SonarQube token copied in Step 13.2.
   - **ID:** `sonar-token`
   - **Description:** `SonarQube User Token`
   - Click **Create**.
2. Go to Jenkins: **Manage Jenkins** > **System**.
3. Scroll to **SonarQube servers**:
   - Check **Environment variables** (`Enable injection of SonarQube server configuration as environment variables`).
   - Click **Add SonarQube**.
   - **Name:** `sonar-server`
   - **Server URL:** `http://<EC2_PUBLIC_IP>:9000` *(WARNING: Do NOT include a trailing slash `/`)*
   - **Server authentication token:** Select `sonar-token`.
   - Click **Save**.

### 13.4 SonarQube Quality Gate Webhook to Jenkins
To allow SonarQube to notify Jenkins when code analysis passes or fails Quality Gates:
1. Open SonarQube: **Administration** > **Configuration** > **Webhooks**.
2. Click **Create**.
3. **Name:** `Jenkins`
4. **URL:** `http://<EC2_PUBLIC_IP>:8080/sonarqube-webhook/` *(Make sure to include the trailing slash here)*
5. Click **Create**.

---

## 14. Step 8: Complete Declarative Jenkinsfile (The DevSecOps Pipeline)

Create a new Jenkins Pipeline Job:
1. In Jenkins Dashboard, click **New Item**.
2. **Item Name:** `3-tier-application-pipeline`
3. Select **Pipeline** and click **OK**.
4. Under the **Pipeline** section, paste the following Declarative Pipeline:

```groovy
pipeline {
    agent any

    environment {
        // REPLACE with your actual Docker Hub username!
        DOCKER_IMAGE   = "your-dockerhub-username/devsecops-exam-app:${BUILD_NUMBER}"
        DOCKER_LATEST  = "your-dockerhub-username/devsecops-exam-app:latest"
        DOCKER_CRED_ID = "docker-cred"
        SONAR_SERVER   = "sonar-server"
        SCANNER_HOME   = tool 'sonar-scanner'
    }

    stages {
        stage('1. Checkout SCM') {
            steps {
                echo '=== Checking out application source code from GitHub ==='
                git branch: 'main', url: 'https://github.com/CloudDevOpsHub/3-tier-application.git'
            }
        }

        stage('2. Trivy Filesystem Scan (SCA)') {
            steps {
                echo '=== Running Aquasec Trivy Filesystem Vulnerability Scan ==='
                sh 'trivy fs --severity HIGH,CRITICAL --format table -o trivy-fs-report.txt .'
                sh 'cat trivy-fs-report.txt'
            }
        }

        stage('3. SonarQube Code Analysis (SAST)') {
            steps {
                echo '=== Running SonarQube Static Application Security Testing ==='
                withSonarQubeEnv("${SONAR_SERVER}") {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                            -Dsonar.projectKey=3-tier-application \
                            -Dsonar.projectName=3-tier-application \
                            -Dsonar.sources=. \
                            -Dsonar.exclusions=**/*.sh,**/tests/**
                    """
                }
            }
        }

        stage('4. SonarQube Quality Gate') {
            steps {
                echo '=== Evaluating SonarQube Quality Gate Status ==='
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('5. Docker Build') {
            steps {
                echo '=== Building Docker Container Image ==='
                sh "docker build -t ${DOCKER_IMAGE} -t ${DOCKER_LATEST} ."
            }
        }

        stage('6. Trivy Image Scan') {
            steps {
                echo '=== Scanning Docker Image for Container Vulnerabilities ==='
                sh "trivy image --severity HIGH,CRITICAL --format table -o trivy-image-report.txt ${DOCKER_IMAGE}"
                sh 'cat trivy-image-report.txt'
            }
        }

        stage('7. Docker Login & Push to Registry') {
            steps {
                echo '=== Authenticating and Pushing Image to Docker Hub ==='
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}"
                    sh "docker push ${DOCKER_LATEST}"
                }
            }
        }

        stage('8. Deploy 3-Tier Application') {
            steps {
                echo '=== Deploying 3-Tier Application Stack ==='
                sh '''
                    # Stop and remove existing containers if present
                    docker stop app-frontend mysql-db || true
                    docker rm app-frontend mysql-db || true

                    # Deploy MySQL Database Container
                    docker run -d \
                        --name mysql-db \
                        --network host \
                        -e MYSQL_ROOT_PASSWORD=RootPass \
                        -e MYSQL_DATABASE=devops_exam \
                        mysql:8.0

                    # Wait for MySQL to initialize
                    sleep 15

                    # Deploy Application Web Container on Port 5000
                    docker run -d \
                        --name app-frontend \
                        -p 5000:5000 \
                        --network host \
                        -e DB_HOST=127.0.0.1 \
                        -e DB_USER=root \
                        -e DB_PASSWORD=RootPass \
                        -e DB_NAME=devops_exam \
                        ''' + "${DOCKER_LATEST}" + '''
                '''
            }
        }
    }

    post {
        always {
            echo '=== DevSecOps Pipeline Execution Finished ==='
            archiveArtifacts artifacts: '*.txt', allowEmptyArchive: true
        }
        success {
            echo '=== Pipeline Succeeded: 3-Tier Application Deployed & Verified! ==='
        }
        failure {
            echo '=== Pipeline Failed! Check logs and security scan reports. ==='
        }
    }
}
```

---

## 15. Step 9: Pipeline Execution & Stage-by-Stage Verification

1. In your Jenkins pipeline page, click **Build Now**.
2. Open the build run and click **Console Output** to observe real-time execution:
   - **Checkout:** Clones the code into the Jenkins workspace.
   - **Trivy FS:** Scans source code and dependencies for known CVEs.
   - **SonarQube SAST:** Sends code to `http://<EC2-IP>:9000` for analysis.
   - **Quality Gate:** Webhook confirms that code quality, bugs, and security hotspots pass thresholds.
   - **Docker Build:** Builds container layers and tags them.
   - **Trivy Image Scan:** Scans the newly created container image layers.
   - **Docker Push:** Authenticates securely via masked credentials and pushes to Docker Hub.
   - **Deploy:** Runs MySQL and web app containers on the host.

---

## 16. Step 10: Live 3-Tier Application Verification & Database Test

### 16.1 Testing the Web Interface (Tier 1 & Tier 2)
1. Open your browser and navigate to: `http://<EC2_PUBLIC_IP>:5000`
2. You will see the **Online Examination / Assessment Portal**.
3. Fill out the registration form:
   - **Candidate Name:** `Your Name`
   - **Gender:** `Male / Female`
   - **Email:** `yourname@example.com`
4. Click **Start Exam**.
5. Complete the 10 questions and click **Submit Exam**.
6. Observe the immediate evaluation score presented on the screen.

### 16.2 Verifying Data Persistence inside MySQL (Tier 3)
Connect directly to the running MySQL database container to verify that the submission was recorded:

```bash
# Exec into the running MySQL container
docker exec -it mysql-db mysql -u root -pRootPass

# Inside the MySQL client prompt:
SHOW DATABASES;

# Switch to the application database
USE devops_exam;

# List tables created by the application
SHOW TABLES;

# Query submitted candidate exam records
SELECT * FROM students;
-- or
SELECT * FROM exam_results;
```

You will observe candidate entries, contact details, submitted responses, and calculated scores persisted in the database table!

---

## 17. Step 11: Trivy Vulnerability Scanning Deep-Dive

Trivy scans applications and containers against the **National Vulnerability Database (NVD)** and **CVE** feeds:

```bash
# Scan a specific Docker image
trivy image --severity HIGH,CRITICAL mysql:8.0

# Scan the local filesystem for misconfigurations and vulnerable libraries
trivy fs --severity CRITICAL .

# Generate a detailed JSON report for enterprise compliance audits
trivy image --format json -o audit-report.json <username>/devsecops-exam-app:latest
```

### Understanding Trivy Severity Levels:
- **LOW / MEDIUM:** Minor version mismatches or theoretical flaws with no exploit available. Generally documented or accepted.
- **HIGH / CRITICAL:** Known exploitable flaws (Remote Code Execution, Privilege Escalation). Pipelines should enforce blocking gates on Critical CVEs before production deployment.

---

## 18. Troubleshooting & Debugging Matrix (Real Issues from Session)

| Error Message / Symptom | Root Cause | Exact Resolution |
| :--- | :--- | :--- |
| **`denied: requested access to the resource is denied`** | The pipeline script has hardcoded `vikas...` or an incorrect Docker Hub username in `DOCKER_IMAGE`. | Edit Line 5 of the Jenkinsfile. Replace `vikas...` with your own personal Docker Hub username where you have push authorization. |
| **`Got permission denied while trying to connect to the Docker daemon socket`** | The `jenkins` user on Ubuntu does not belong to the `docker` Linux group. | Run `sudo usermod -aG docker jenkins && sudo systemctl restart jenkins`. |
| **SonarQube Webhook Fails / Quality Gate times out** | SonarQube server URL in Jenkins has a trailing slash (`http://<IP>:9000/`) or Webhook URL in SonarQube lacks trailing slash (`http://<IP>:8080/sonarqube-webhook`). | Jenkins SonarQube URL must be `http://<IP>:9000` (no slash). SonarQube webhook URL must be `http://<IP>:8080/sonarqube-webhook/` (with slash). |
| **Application fails to connect to Database on Port 5000** | MySQL container was still initializing tables and InnoDB buffers when the web app container booted. | Add a `sleep 15` or container healthcheck (`depends_on: condition: service_healthy`) before launching the web tier. |
| **Port 5000, 8080, or 9000 not reachable in browser** | AWS EC2 Security Group inbound rules have not permitted traffic on ports 5000, 8080, or 9000. | Go to AWS Console > EC2 > Instances > Select Instance > Security Tab > Inbound Rules > Add Rules for Custom TCP `8080`, `9000`, `5000` from `0.0.0.0/0`. |
| **`sonar-scanner: command not found`** | Tool name in Jenkinsfile (`tool 'sonar-scanner'`) does not match the name defined in Jenkins Global Tool Configuration. | Go to **Manage Jenkins** > **Tools** > Verify the scanner name is exactly `sonar-scanner`. |

---

## 19. Infrastructure Cleanup & Cost Optimization

To avoid incurring unexpected AWS charges after completing the project:

```bash
# Stop and remove all running project containers
docker stop app-frontend mysql-db sonarqube || true
docker rm app-frontend mysql-db sonarqube || true

# Prune unused Docker images to free disk space
docker system prune -af

# Terminate the AWS EC2 instance
# In AWS Console: EC2 Dashboard > Instances > Select Instance > Instance State > Terminate Instance.
```

---

## 20. Project in One Page

```text
+---------------------------------------------------------------------------------------------------------------+
|                                      PROJECT 4: DEVSECOPS 3-TIER DEPLOYMENT                                   |
+---------------------------------------------------------------------------------------------------------------+
| 1. Infrastructure     | AWS EC2 (Ubuntu 24.04 LTS, t2.large, Ports 22, 8080, 9000, 5000, 3000 opened).       |
| 2. CI/CD Orchestrator | Jenkins LTS on OpenJDK 21, Git SCM, Docker Pipeline, Stage View.                      |
| 3. Security (SAST)    | SonarQube Community LTS (Port 9000), sonar-scanner, Quality Gate enforcement.         |
| 4. Security (SCA)     | Aquasec Trivy (Filesystem CVE scan & Docker Container Image CVE scan).                |
| 5. Artifact Registry  | Docker Hub (Automated authentication via Jenkins Credential Manager).                 |
| 6. Application Stack  | 3-Tier Exam Web Application (Frontend/Middleware on Port 5000, MySQL on Port 3306).  |
| 7. Security Concept   | Shift-Left Security: Scan code & container early; block bad builds before deployment.|
+---------------------------------------------------------------------------------------------------------------+
```

---

## 21. Top 10 Technical Interview Questions & Answers

### Q1: What is the fundamental difference between DevOps and DevSecOps?
**Answer:** DevOps focuses on rapid, continuous software delivery through automated build, test, and release cycles. DevSecOps embeds security practices, automated vulnerability scanning, and compliance policies into every stage of that delivery cycle ("Shift-Left Security") so that security is a shared responsibility rather than a bottleneck at the end of the SDLC.

### Q2: What is the difference between SAST, DAST, and SCA?
**Answer:**
- **SAST (Static Application Security Testing):** Analyzes raw source code without executing it (e.g., SonarQube) to find syntax errors, code smells, bugs, and SQL injection flaws.
- **SCA (Software Composition Analysis):** Scans third-party open-source libraries, packages, and dependencies for known CVEs (e.g., Trivy filesystem scan, OWASP Dependency-Check).
- **DAST (Dynamic Application Security Testing):** Tests a running application from the outside (e.g., OWASP ZAP) by simulating cyberattacks against live HTTP endpoints.

### Q3: How does Aquasec Trivy detect vulnerabilities in Docker images?
**Answer:** Trivy inspects the OS packages (apt, apk, yum) and application dependency manifests (`package.json`, `requirements.txt`, `pom.xml`) inside the container layers. It queries its cached vulnerability database, which continuously syncs with the National Vulnerability Database (NVD), Red Hat Security Data, Alpine SecDB, and Debian Security Bug Tracker to match package versions against known CVEs.

### Q4: Why is a Quality Gate critical in a Jenkins CI/CD pipeline?
**Answer:** A Quality Gate establishes a strict pass/fail criteria (e.g., zero new Critical bugs, code coverage > 80%, duplicated lines < 3%). Using `waitForQualityGate abortPipeline: true`, Jenkins automatically stops the pipeline if the code fails the criteria, preventing sub-standard or insecure code from being packaged into Docker images or deployed.

### Q5: What is a "Golden Image" and why should organizations enforce it?
**Answer:** A Golden Image is a standardized, pre-configured, hardened, and security-scanned base image created and maintained by enterprise platform teams. Enforcing golden images prevents developers from pulling arbitrary public images from Docker Hub that may contain malware, obsolete kernel versions, or unpatched vulnerabilities.

### Q6: How does Jenkins communicate securely with SonarQube during a build?
**Answer:** Jenkins uses a two-way integration:
1. **Outbound (Jenkins to SonarQube):** Jenkins executes `withSonarQubeEnv`, injecting the server URL and a secure API User Token (stored in Jenkins Credentials as Secret Text) to submit analysis results.
2. **Inbound (SonarQube to Jenkins):** After computing the Quality Gate, SonarQube triggers an HTTP POST webhook back to `http://<jenkins-url>:8080/sonarqube-webhook/` so Jenkins can resume or fail the pipeline step.

### Q7: Why do we scan both the filesystem (SCA) and the container image?
**Answer:** 
- The **filesystem scan** checks the application's immediate code dependencies (e.g., Node/Python/Java libraries).
- The **container image scan** inspects the underlying operating system layers, shared system libraries (`glibc`, `openssl`, `libssl`), and system binaries added by the base image (`FROM ubuntu` or `FROM node`).

### Q8: What Linux user permissions are required for Jenkins to run Docker commands?
**Answer:** The `jenkins` system user must be added to the `docker` group (`usermod -aG docker jenkins`). Docker commands interact with the Unix domain socket at `/var/run/docker.sock`, which is owned by `root:docker`. Without group membership, Jenkins cannot communicate with the Docker daemon.

### Q9: What happens if a vulnerability in a verified base image has no available patch?
**Answer:** In enterprise operations, if a reported CVE has no available fix from the upstream maintainer ("Won't Fix" or zero-day without patch), the team performs a risk assessment:
1. Determine if the vulnerable module is exposed or reachable.
2. Implement compensatory security controls (e.g., Web Application Firewall / WAF rules).
3. Add a temporary documented exception in the security policy scanner (`.trivyignore`) with a defined review expiration date.

### Q10: How does container-based 3-tier architecture communicate internally?
**Answer:** Containers communicate either through the host network (`--network host`) or via user-defined Docker bridge networks (`docker network create app-net`). Inside a custom bridge network, containers discover each other using container names as DNS hostnames (e.g., the web container connects to `mysql-db:3306`), preventing the database from needing to expose public ports.

---

## 22. Top 10 Scenario-Based Production Interview Questions & Solutions

### Scenario 1: Docker Push Fails with "Access Denied" in CI
- **Problem:** Pipeline fails at the `docker push` stage with `denied: requested access to the resource is denied`.
- **Diagnosis:** The Docker Hub repository path in the pipeline script does not match the authenticated user credentials, or the user lacks write permissions to the organization repository.
- **Solution:** Verify that the image is tagged as `<authenticated_username>/<repo_name>:<tag>`. Ensure that the Jenkins credential ID matches the username injected into the `docker login` step.

### Scenario 2: Pipeline Quality Gate Times Out After 10 Minutes
- **Problem:** SonarQube analysis completes, but Jenkins hangs at `waitForQualityGate` until timing out.
- **Diagnosis:** SonarQube failed to deliver the webhook payload back to Jenkins due to network isolation, firewall blocking, or an incorrect webhook URL.
- **Solution:** Verify the webhook in SonarQube (**Administration** > **Configuration** > **Webhooks**). Ensure the URL is `http://<Jenkins_IP>:8080/sonarqube-webhook/` with the trailing slash, and ensure AWS Security Groups permit traffic between SonarQube and Jenkins.

### Scenario 3: Container Starts and Immediately Exits (CrashLoop)
- **Problem:** Web application container exits with code `1` immediately upon deployment.
- **Diagnosis:** Inspection via `docker logs <container-name>` reveals `Error: ECONNREFUSED 127.0.0.1:3306` because the MySQL database container was not ready to accept connections.
- **Solution:** Implement an initialization wait loop or container health check. In Docker Compose, configure:
  ```yaml
  depends_on:
    mysql-db:
      condition: service_healthy
  ```

### Scenario 4: Trivy Scan Fails the Pipeline on Non-Exploitable CVEs
- **Problem:** Enterprise build fails because Trivy discovers an unpatched CVE in an obsolete utility bundled inside the base image.
- **Diagnosis:** Strict build failure rules (`--exit-code 1`) without severity filtering block critical delivery deadlines.
- **Solution:** Filter scans using `--severity HIGH,CRITICAL` and ignore unpatched vulnerabilities using `--ignore-unfixed`. If approved by security leads, add specific CVE IDs to `.trivyignore`.

### Scenario 5: High Disk Space Consumption on Jenkins Worker
- **Problem:** After 50 builds, the EC2 instance runs out of disk space (`No space left on device`).
- **Diagnosis:** Dangling Docker images, untagged build layers (`<none>:<none>`), and SonarQube scanner cache consume all EBS storage.
- **Solution:** Add a post-build cleanup step in the Jenkinsfile:
  ```groovy
  post {
    always {
      sh 'docker image prune -f'
      cleanWs()
    }
  }
  ```

### Scenario 6: Database Root Password Exposed in Pipeline Console Output
- **Problem:** Build logs print plain-text database credentials during the `docker run` deployment step.
- **Diagnosis:** Environment variables were echoed directly in shell scripts without Jenkins credential binding masking.
- **Solution:** Wrap database passwords inside Jenkins `withCredentials([string(credentialsId: 'db-pass', variable: 'DB_PASS')])` so Jenkins automatically masks occurrences with `****` in console logs.

### Scenario 7: Data Sovereignty / Compliance Policy Violation
- **Problem:** An auditor discovers that customer database backups are being staged in an AWS S3 bucket in `us-east-1` for a banking client based in Mumbai.
- **Diagnosis:** Pipeline scripts defaulted to standard AWS CLI regions without enforcing geographical compliance policies.
- **Solution:** Enforce AWS IAM service control policies (SCPs) restricting API actions to `ap-south-1` (Mumbai) and embed automated policy checks using tools like Checkov or Terraform Compliance in the CI pipeline.

### Scenario 8: Developers Complain of Slow CI Builds Due to Trivy Downloads
- **Problem:** Each pipeline execution takes 6+ minutes downloading the Trivy vulnerability database from GitHub.
- **Diagnosis:** Trivy downloads fresh database definitions on every ephemeral run.
- **Solution:** Cache the Trivy database directory (`~/.cache/trivy`) on the host or mount a persistent volume across pipeline runs so only incremental delta updates are downloaded.

### Scenario 9: SonarQube Out of Memory (OOM) During Static Analysis
- **Problem:** The SonarQube container crashes during large codebase analysis with `java.lang.OutOfMemoryError: Java heap space`.
- **Diagnosis:** The EC2 host ran out of RAM, or the SonarQube JVM heap size was constrained by default limits.
- **Solution:** Upgrade host from `t2.medium` to `t2.large` (8 GB RAM). Configure Linux virtual memory settings required by Elasticsearch/SonarQube:
  ```bash
  sysctl -w vm.max_map_count=262144
  sysctl -w fs.file-max=65536
  ```

### Scenario 10: Secret Leakage Detected in Git History
- **Problem:** A developer accidentally committed an AWS secret access key to the GitHub repository.
- **Diagnosis:** Security scanning was only performed post-commit in Jenkins, after the credential had already been published to remote GitHub history.
- **Solution:** Implement **pre-commit hooks** using `git-secrets` or `Talisman` on developer workstations to block commits containing API keys locally, and immediately revoke/rotate the compromised AWS credential in AWS IAM.
