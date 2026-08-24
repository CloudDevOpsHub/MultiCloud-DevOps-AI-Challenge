# 🚀 DevOps Batch-44 | Terraform Day 1: Infrastructure as Code (IaC) & AWS Automation

[![Module: Terraform & IaC](https://img.shields.io/badge/Module-Terraform%20%26%20IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](README.md)
[![Cloud: AWS](https://img.shields.io/badge/Cloud-AWS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Interview Q&A: 20+ Questions](https://img.shields.io/badge/Interview%20Q%26A-20%2B%20Included-success?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents
1. [Session Overview](#-session-overview)
2. [Why Automation & IaC Are Required](#1-why-automation--iac-are-required)
3. [Understanding Infrastructure as Code (IaC)](#2-understanding-infrastructure-as-code-iac)
4. [Terraform vs. Ansible: Key Differences](#3-terraform-vs-ansible-key-differences)
5. [Core Terraform Terminology](#4-core-terraform-terminology)
6. [The Terraform Core Workflow](#5-the-terraform-core-workflow)
7. [Declarative vs. Imperative Approaches](#6-declarative-vs-imperative-approaches)
8. [Multi-Cloud IaC Ecosystem](#7-multi-cloud-iac-ecosystem)
9. [HashiCorp Configuration Language (HCL)](#8-hashicorp-configuration-language-hcl)
10. [Prerequisites & Hands-on Setup Guide](#9-prerequisites--hands-on-setup-guide)
    - [1. Terraform CLI Installation](#91-terraform-cli-installation)
    - [2. AWS CLI Installation & Configuration](#92-aws-cli-installation--configuration)
    - [3. AWS IAM User, Policies & Security Credentials](#93-aws-iam-user-policies--security-credentials)
11. [First Terraform Project Walkthrough (`main.tf`)](#10-first-terraform-project-walkthrough-maintf)
12. [Real-World Architecture & Best Practices](#11-real-world-architecture--best-practices)
13. [Key Commands Quick Reference](#12-key-commands-quick-reference)
14. [Top 10 Technical Interview Questions & Answers](#13-top-10-technical-interview-questions--answers)
15. [Top 10 Scenario-Based Interview Questions & Solutions](#14-top-10-scenario-based-interview-questions--solutions)

---

## 🎯 Session Overview

In this foundational session for **DevOps Batch-44**, we transition from manual cloud infrastructure management to enterprise-grade **Infrastructure as Code (IaC)** using **Terraform**.

### Primary Objectives:
- Understand the business and engineering pain points of manual cloud provisioning.
- Demystify the distinct roles of **Terraform (Provisioning)** vs. **Ansible (Configuration Management)**.
- Master core Terraform terminology (**Providers, Resources, State, Modules, Data Sources, Backends**).
- Set up and configure the local developer toolchain (**Terraform CLI, AWS CLI, IAM Access Keys**).
- Write, initialize, plan, and execute our first Terraform project targeting **AWS**.

---

## 1. Why Automation & IaC Are Required

Manually creating infrastructure through a cloud web console (ClickOps) is suitable only for initial ad-hoc experiments. In enterprise production environments, manual provisioning fails quickly.

```
Manual Console (ClickOps) Issues:
┌────────────────────┐    ┌────────────────────┐    ┌────────────────────┐
│   Human Errors     │───▶│ Non-Reproducible   │───▶│ Config Drift &     │
│  (Wrong Subnet/IP) │    │ Environments       │    │ High Downtime Risk │
└────────────────────┘    └────────────────────┘    └────────────────────┘
```

### Challenges with Manual Infrastructure (ClickOps)
1. **Human Inconsistency & Error:** A single missed checkbox or wrong CIDR block can break networking or introduce security vulnerabilities.
2. **Slow & Non-Scalable:** Provisioning 40+ resources across **Dev, QA, Staging, and Production** takes hours or days manually.
3. **Configuration Drift:** Manual tweaks lead to untracked environment differences.
4. **Single Point of Failure (Knowledge Silos):** If an engineer leaves the organization, undocumented infrastructure setups are lost.
5. **No Audit Trail or Rollbacks:** Cloud consoles lack native commit histories, code reviews, and one-click rollbacks.

> [!IMPORTANT]
> **Core Principle:** When infrastructure is codified into source code, it becomes **version-controlled, reviewable, reusable, repeatable, and automated**.

---

## 2. Understanding Infrastructure as Code (IaC)

**Infrastructure as Code (IaC)** is the architectural practice of defining, provisioning, and managing compute, storage, networking, and application runtimes using structured, declarative machine-readable configuration files.

### Key Benefits of IaC

| Benefit | Description |
| :--- | :--- |
| **Automation** | Creates complex multi-tier topologies without human intervention. |
| **Consistency & Parity** | Guarantees identical environments across Dev, QA, and Production. |
| **Version Control** | Tracks changes in Git with commit messages, pull requests, and peer reviews. |
| **Disaster Recovery** | Recreates entire cloud environments in minutes during outages or migrations. |
| **Cost Optimization** | Easily spins up temporary test environments and destroys them after testing. |
| **Compliance & Security** | Enables automated security scans (Static Code Analysis) before deployment. |

---

## 3. Terraform vs. Ansible: Key Differences

A common industry question is when to use **Terraform** vs. **Ansible**. They are complementary tools designed for different phases of the infrastructure lifecycle.

```
                  COMPLETE DEVOPS AUTOMATION FLOW
                  
   ┌─────────────────────────────────────────────────────────────┐
   │                    1. TERRAFORM (IaC)                       │
   │  Provisions Base Infrastructure: Cloud, Network & Compute    │
   │  [ VPC ] ──▶ [ Subnets / SG ] ──▶ [ EC2 / EKS / S3 / RDS ]  │
   └──────────────────────────────┬──────────────────────────────┘
                                  │
                                  ▼
   ┌─────────────────────────────────────────────────────────────┐
   │             2. ANSIBLE (Configuration Management)           │
   │  Configures OS, Packages & Software on Created Compute      │
   │  [ Install Docker/Java ] ──▶ [ Nginx Config ] ──▶ [ Deploy] │
   └─────────────────────────────────────────────────────────────┘
```

### Detailed Comparison Table

| Parameter | Terraform | Ansible |
| :--- | :--- | :--- |
| **Primary Domain** | **Infrastructure Provisioning** (Day 0 / Day 1) | **Configuration Management** (Day 1 / Day 2) |
| **Main Function** | Creates and destroys cloud/virtual infrastructure | Installs software, patches OS, deploys applications |
| **Typical Resources** | VPC, Subnets, EC2, S3, IAM, Route53, RDS, EKS | Packages (Java, Python, Docker), Users, Config files |
| **Configuration Style** | **Declarative** (Defines desired end state) | **Procedural & Declarative** (Playbooks / Tasks) |
| **State Management** | **Stateful** (Tracks resources via `terraform.tfstate`) | **Stateless** (Checks current system status dynamically) |
| **Language** | HCL (HashiCorp Configuration Language) | YAML |
| **Agent Requirement** | **Agentless** (Communicates via Cloud APIs) | **Agentless** (Communicates over SSH / WinRM) |

> [!TIP]
> **Rule of Thumb:**
> - Use **Terraform** to create the server and networking.
> - Use **Ansible** to configure software inside that server.

---

## 4. Core Terraform Terminology

```
                         TERRAFORM ARCHITECTURE
                         
                        ┌───────────────────────┐
                        │   Terraform Config    │
                        │      (*.tf / HCL)     │
                        └───────────┬───────────┘
                                    │
                                    ▼
                        ┌───────────────────────┐
                        │     Terraform CLI     │
                        │  (Core Engine & DAG)  │
                        └─────┬───────────┬─────┘
                              │           │
                     Reads/   │           │ Invokes
                     Writes   │           │ Plugins
                              ▼           ▼
        ┌─────────────────────────┐   ┌─────────────────────────┐
        │   State File (tfstate)  │   │  Provider Plugins (AWS) │
        └─────────────────────────┘   └───────────┬─────────────┘
                                                  │
                                                  ▼ (Cloud API Calls)
                                      ┌─────────────────────────┐
                                      │     AWS Cloud / IAM     │
                                      └─────────────────────────┘
```

### 1. Provider
Plugins that allow Terraform to communicate with APIs of target platforms (AWS, Azure, GCP, Kubernetes, GitHub, Cloudflare).
> **Provider = WHERE Terraform executes changes.**

### 2. Resource
Defines specific infrastructure components to be created and managed (e.g., `aws_instance`, `aws_s3_bucket`, `aws_vpc`, `aws_iam_user`).
> **Resource = WHAT infrastructure components to create.**

### 3. Variable (`variables.tf`)
Input parameters that make Terraform configurations dynamic and reusable across environments without changing code.

### 4. Output (`outputs.tf`)
Values returned after execution (e.g., public IP addresses, instance IDs, ARN strings, VPC IDs) for downstream systems or users.

### 5. State File (`terraform.tfstate`)
Terraform's metadata database storing resource mappings and tracking actual cloud infrastructure against defined configurations.

### 6. Module
A container for multiple resources configured together. Allows standardized, reusable infrastructure blocks (e.g., standard production VPC module).

### 7. Data Source (`data "..."`)
Allows Terraform to fetch existing information from external platforms or existing cloud resources without managing them.

### 8. Backend & State Locking
Defines where state snapshots are stored (e.g., local disk vs. remote AWS S3) and how state locks are managed (e.g., AWS DynamoDB) to prevent concurrent conflicting runs.

### 9. Dependency Graph (Directed Acyclic Graph - DAG)
Terraform automatically calculates resource dependencies (e.g., creating a VPC before creating a Subnet inside it) and provisions independent resources in parallel.

### 10. Workspaces
Logical partitions within a single backend to isolate state files for different environments (Dev, QA, Prod).

---

## 5. The Terraform Core Workflow

The standard Terraform lifecycle follows 5 structured steps:

```
  ┌───────────────┐     ┌────────────────────┐     ┌──────────────────┐
  │   1. INIT     │────▶│   2. VALIDATE      │────▶│    3. PLAN       │
  │ Plugin & State│     │ Syntax/Config Check│     │ Dry-run Preview  │
  └───────────────┘     └────────────────────┘     └────────┬─────────┘
                                                            │
                                                            ▼
  ┌───────────────┐                                ┌──────────────────┐
  │  5. DESTROY   │◀───────────────────────────────│    4. APPLY      │
  │ Clean Cleanup │        (When Teardown Needed)  │ Real Execution   │
  └───────────────┘                                └──────────────────┘
```

### Step 1: `terraform init`
- Initializes the working directory.
- Downloads required provider plugins from the Terraform Registry.
- Configures and connects to the specified backend.

### Step 2: `terraform validate`
- Verifies syntax, variable consistency, and structural validity of `.tf` files.

### Step 3: `terraform plan`
- Performs a dry run and generates an execution plan.
- Compares current state with desired configuration and displays additions (`+`), modifications (`~`), and deletions (`-`).

### Step 4: `terraform apply`
- Executes the planned actions via provider APIs.
- Updates the `terraform.tfstate` file with actual resource details.

### Step 5: `terraform destroy`
- Reads the state file and removes all infrastructure managed by the current configuration.

---

## 6. Declarative vs. Imperative Approaches

| Paradigm | How it Works | Example Tools | Concept |
| :--- | :--- | :--- | :--- |
| **Declarative (Terraform)** | You define **WHAT** the end-state should be. The engine determines how to achieve it. | Terraform, Kubernetes, CloudFormation | *"I need 3 EC2 instances in us-west-2."* |
| **Imperative (Procedural)** | You specify **STEP-BY-STEP HOW** commands must execute in exact order. | Bash scripts, AWS CLI scripts, Python Boto3 | *"1. Call RunInstances; 2. Wait; 3. Attach IP..."* |

---

## 7. Multi-Cloud IaC Ecosystem

Terraform is widely adopted because it provides a unified configuration workflow across multiple cloud providers and on-premise platforms:

```
                                  TERRAFORM CORE
                                         │
        ┌──────────────────┬─────────────┴────────────┬──────────────────┐
        ▼                  ▼                          ▼                  ▼
   [AWS Provider]   [Azure Provider]           [GCP Provider]   [K8s / Others]
        │                  │                          │                  │
        ▼                  ▼                          ▼                  ▼
    AWS Cloud        Microsoft Azure             Google Cloud       Kubernetes
```

- **CloudFormation:** AWS-specific only.
- **ARM Templates / Bicep:** Azure-specific only.
- **Terraform:** Universal provider ecosystem (over 7,000+ available community and official providers).

---

## 8. HashiCorp Configuration Language (HCL)

Terraform configurations are written in `.tf` files using **HCL (HashiCorp Configuration Language)**.

### HCL Syntax Characteristics:
- Human-readable and machine-friendly.
- Block-structured format (`<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" { ... }`).
- Supports variables, conditional expressions, functions, loops (`for_each`, `count`), and dynamic blocks.

```hcl
# Example HCL block structure
resource "aws_iam_user" "example_user" {
  name = "devops-engineer"
  tags = {
    Environment = "Dev"
    ManagedBy   = "Terraform"
  }
}
```

---

## 9. Prerequisites & Hands-on Setup Guide

### 9.1 Terraform CLI Installation

#### Windows (Binary / Path Configuration)
1. Download the latest 64-bit Windows binary zip from [Terraform Official Downloads](https://developer.hashicorp.com/terraform/install).
2. Extract `terraform.exe` to a permanent folder (e.g., `C:\terraform\bin`).
3. Add `C:\terraform\bin` to the System **Environment Variables -> PATH**.
4. Verify installation:
   ```cmd
   terraform --version
   ```

#### Linux (Ubuntu/Debian)
```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt-get update && sudo apt-get install terraform -y
terraform --version
```

#### macOS (Homebrew)
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
terraform --version
```

---

### 9.2 AWS CLI Installation & Configuration

1. Install the AWS CLI v2 for your operating system.
2. Authenticate the local environment using:
   ```bash
   aws configure
   ```
3. Provide the requested credentials:
   ```text
   AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
   AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   Default region name [None]: us-west-2
   Default output format [None]: json
   ```
4. Verify authentication:
   ```bash
   aws sts get-caller-identity
   ```

---

### 9.3 AWS IAM User, Policies & Security Credentials

```
  AWS Console ──▶ IAM ──▶ Users ──▶ Create User ──▶ Attach Admin/Custom Policy ──▶ Security Credentials ──▶ Create Access Key (CLI)
```

1. **Login to AWS Management Console** and navigate to **IAM (Identity and Access Management)**.
   > [!NOTE]
   > IAM is a **global AWS service**; it does not require region selection in the AWS Console.
2. **Create IAM User & User Group:**
   - Create a dedicated user (e.g., `terraform-admin`).
   - Create a group (e.g., `DevOpsAdmins`) with required permissions (`AdministratorAccess` for lab sandbox, or least-privilege policies for production).
3. **Generate Access Keys:**
   - Go to **Security credentials** -> **Create access key**.
   - Select **Command Line Interface (CLI)** as the use case.
   - Download the `.csv` file containing the **Access Key ID** and **Secret Access Key**.

> [!CAUTION]
> **Security Best Practice:** Never commit AWS Access Keys or credentials to Git repositories or share them in code. Use environment variables, IAM roles, or local AWS credential profiles (`~/.aws/credentials`).

---

## 10. First Terraform Project Walkthrough (`main.tf`)

Let's examine the structure of our initial Terraform project file:

### Directory Structure
```text
terraform-first-project/
├── main.tf              # Main configuration file
├── variables.tf         # Input variable declarations (future)
├── outputs.tf           # Output values (future)
└── .gitignore           # Ignores .terraform/ and *.tfstate
```

### Complete `main.tf` Example
```hcl
# 1. Terraform Core Settings & Required Providers Block
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# 2. AWS Provider Configuration
provider "aws" {
  region = "us-west-2"
}

# 3. Resource Block: Create an IAM User
resource "aws_iam_user" "batch44_user" {
  name = "batch44-terraform-user"

  tags = {
    Batch       = "DevOps-44"
    Environment = "Training"
    ManagedBy   = "Terraform"
  }
}
```

### Hands-on Step-by-Step Execution:

#### Step 1: Initialize the Project
```bash
terraform init
```
*Output: Terraform initializes the backend and downloads the AWS provider plugin into `.terraform/`.*

#### Step 2: Validate Syntax
```bash
terraform validate
```
*Output: `Success! The configuration is valid.`*

#### Step 3: Preview Changes
```bash
terraform plan
```
*Output: Displays `Plan: 1 to add, 0 to change, 0 to destroy.`*

#### Step 4: Apply Changes
```bash
terraform apply -auto-approve
```
*Output: `Apply complete! Resources: 1 added, 0 changed, 0 destroyed.`*

#### Step 5: Verify in AWS Console / CLI
```bash
aws iam get-user --user-name batch44-terraform-user
```

#### Step 6: Clean Up Resources
```bash
terraform destroy -auto-approve
```
*Output: `Destroy complete! Resources: 1 destroyed.`*

---

## 11. Real-World Architecture & Best Practices

### 1. Eliminating the "Hero Engineer" Single Point of Failure
When infrastructure is configured manually by a single engineer, company operations are vulnerable if that engineer leaves. With Terraform, infrastructure definitions live in Git, ensuring business continuity, auditable changes, and team-wide accessibility.

### 2. Enterprise State Management Architecture

```
                  ENTERPRISE REMOTE BACKEND PATTERN
                  
      DevOps Engineer 1                         DevOps Engineer 2
             │                                         │
             ▼                                         ▼
      [terraform plan/apply]                   [terraform plan/apply]
             │                                         │
             ├───────────────────┬─────────────────────┤
             │                   │                     │
             ▼                   ▼                     ▼
     ┌───────────────┐   ┌───────────────┐     ┌───────────────┐
     │ AWS S3 Bucket │   │ AWS DynamoDB  │     │ AWS Cloud     │
     │(Encrypted &   │   │(State Locking │───▶ │(Actual Target │
     │ Versioned     │   │ & Concurrency │     │ Infrastructure│
     │ State Storage)│   │ Control)      │     │ Provisioning) │
     └───────────────┘   └───────────────┘     └───────────────┘
```

- **Remote Backend (AWS S3):** Stores `terraform.tfstate` centrally with encryption and versioning.
- **State Locking (DynamoDB):** Acquires a distributed lock during execution to prevent two engineers from applying changes at the same time.

---

## 12. Key Commands Quick Reference

| Command | Purpose |
| :--- | :--- |
| `terraform init` | Initializes directory, downloads provider plugins, configures backend. |
| `terraform validate` | Validates configuration files for syntax and internal consistency. |
| `terraform fmt` | Rewrites configuration files to canonical format and style. |
| `terraform plan` | Generates and previews execution plan without making changes. |
| `terraform apply` | Creates, modifies, or provisions infrastructure resources. |
| `terraform apply -auto-approve` | Applies changes skipping the interactive yes/no confirmation prompt. |
| `terraform destroy` | Deletes and tears down all managed infrastructure. |
| `terraform show` | Inspects current state or saved plan in human-readable format. |
| `terraform state list` | Lists all resources tracked in the current state file. |
| `aws configure` | Interactively sets up AWS credentials, region, and output format. |

---

## 13. Top 10 Technical Interview Questions & Answers

### Q1: What is Terraform and how does it work?
**Answer:**
Terraform is an open-source **Infrastructure as Code (IaC)** software tool created by HashiCorp. It allows engineers to define cloud and on-premise resources in human-readable HCL configuration files. Terraform reads configurations, determines required actions by comparing them against the current state file, and communicates with cloud APIs via provider plugins to provision and manage resources.

---

### Q2: What is the significance of the Terraform state file (`terraform.tfstate`)?
**Answer:**
The state file acts as Terraform's database and memory. It maps the configuration files to real-world resources in the cloud, records resource metadata and dependencies, and tracks configuration drift. Without the state file, Terraform cannot know which real-world resources correspond to the code blocks.

---

### Q3: What happens behind the scenes during `terraform init`?
**Answer:**
During `terraform init`, Terraform:
1. Reads all `.tf` files to identify required providers and modules.
2. Downloads necessary provider plugins from the Terraform Registry into the local `.terraform` directory.
3. Downloads any referenced child modules.
4. Initializes the configured backend (local or remote like AWS S3).
5. Creates or updates the dependency lock file (`.terraform.lock.hcl`).

---

### Q4: Explain the difference between `terraform plan` and `terraform apply`.
**Answer:**
- **`terraform plan`:** A read-only dry run that queries cloud provider APIs and compares actual infrastructure against configuration and state. It previews additions, modifications, and deletions without changing anything.
- **`terraform apply`:** Executes the actual API calls to provision, modify, or delete infrastructure, updating the state file upon completion.

---

### Q5: What is a Terraform Provider? Give examples.
**Answer:**
A provider is a plugin that exposes resource types and data sources for a specific platform by translating HCL definitions into API calls.
*Examples:* `aws`, `azurerm`, `google`, `kubernetes`, `helm`, `github`, `docker`.

---

### Q6: How does Terraform manage resource dependencies?
**Answer:**
Terraform automatically constructs a **Directed Acyclic Graph (DAG)** of all resources.
- **Implicit Dependency:** Created when one resource references an attribute of another (e.g., `subnet_id = aws_subnet.main.id`).
- **Explicit Dependency:** Declared using the `depends_on` meta-argument when Terraform cannot infer order automatically.
Independent resources are provisioned concurrently to maximize speed.

---

### Q7: What is a Terraform Module and why is it used?
**Answer:**
A module is a set of Terraform configuration files grouped together in a single directory. Modules promote the **DRY (Don't Repeat Yourself)** principle, encourage standardization, simplify code maintenance, and enable reusable infrastructure patterns across teams.

---

### Q8: What is the purpose of `terraform validate`?
**Answer:**
`terraform validate` verifies whether configuration files are syntactically valid and internally consistent (checking attribute names, types, and required parameters). It runs locally without needing remote API access or cloud authentication.

---

### Q9: Why is IAM considered a global service in AWS?
**Answer:**
IAM data (users, groups, roles, permissions) is replicated globally across all AWS regions automatically. IAM endpoints do not require region-specific configurations, and an IAM identity created in one region is instantly recognized across all regions worldwide.

---

### Q10: How do you format and maintain clean Terraform code?
**Answer:**
Use the built-in `terraform fmt` command. Running `terraform fmt -recursive` automatically formats all `.tf` files to adhere to HashiCorp's canonical style conventions for spacing, alignment, and indentation.

---

## 14. Top 10 Scenario-Based Interview Questions & Solutions

### Scenario 1: Provisioning 50 EC2 Instances Across Environments
**Question:** A project requires 50 identical EC2 instances in Dev and 50 in QA. How would you design this in Terraform without duplicating code?
**Solution:**
Create a reusable EC2 module and use input variables for environment-specific differences (AMI, instance type, tags). Use `count` or `for_each` inside the module to generate the required number of instances:
```hcl
module "app_servers" {
  source        = "./modules/ec2_instance"
  instance_count = var.instance_count
  instance_type = var.env == "prod" ? "t3.large" : "t3.micro"
}
```

---

### Scenario 2: Remote Backend & Team Collaboration
**Question:** Multiple DevOps engineers run Terraform simultaneously on the same project from their local machines. What risks arise, and how do you resolve them?
**Solution:**
- **Risk:** Race conditions, concurrent state overwrites, and corrupted infrastructure state.
- **Solution:** Configure a remote backend using **AWS S3** (with encryption & versioning) for shared state storage and **AWS DynamoDB** for distributed state locking. When one engineer runs `apply`, a lock is acquired in DynamoDB, blocking concurrent executions until completion.

---

### Scenario 3: `terraform plan` Shows Unintended Resource Recreation
**Question:** You run `terraform plan` and notice Terraform wants to destroy and recreate a critical production database. How do you handle this?
**Solution:**
1. **Never run `apply` immediately.**
2. Inspect the plan output for attributes marked with `forces replacement` (indicated by `~` and `-/+`).
3. Determine if the attribute change was intentional or an accidental configuration mismatch.
4. Protect critical production resources by adding the `lifecycle { prevent_destroy = true }` meta-argument.

---

### Scenario 4: Managing Pre-Existing Cloud Resources
**Question:** An EC2 instance was manually created in the AWS Console. How do you bring it under Terraform management without recreating or stopping it?
**Solution:**
1. Write the corresponding `resource "aws_instance" "legacy_server" {}` block in `.tf`.
2. Run `terraform import aws_instance.legacy_server <i-instance_id>`.
3. Adjust the Terraform configuration attributes until `terraform plan` reports `No changes. Infrastructure matches the configuration.`

---

### Scenario 5: Debugging Provider Download Failures during `terraform init`
**Question:** Running `terraform init` fails with `Failed to query available provider packages`. What steps do you take to troubleshoot?
**Solution:**
1. Check internet connectivity and corporate proxy/firewall settings.
2. Verify provider source and version constraints in the `required_providers` block.
3. Check access to `registry.terraform.io`.
4. Delete the local `.terraform` directory and `.terraform.lock.hcl` and retry `terraform init -upgrade`.

---

### Scenario 6: Combined Terraform & Ansible Workflow
**Question:** An enterprise needs to provision a VPC, launch an EC2 instance, and immediately configure Docker and NGINX on it. How do you structure this pipeline?
**Solution:**
1. **Phase 1 (Terraform):** Provision VPC, Subnets, Security Groups, and EC2 instances. Export the public IP as an output (`output "instance_ip"`).
2. **Phase 2 (CI/CD Pipeline):** Pass the generated IP into an Ansible dynamic inventory.
3. **Phase 3 (Ansible):** Connect to the instance over SSH and run playbooks to install Docker and configure NGINX.

---

### Scenario 7: Securing AWS Credentials in CI/CD
**Question:** You need to run Terraform inside a GitHub Actions / Jenkins CI/CD pipeline. Should you hardcode access keys in `main.tf`?
**Solution:**
Never hardcode credentials. Best practice:
- Use **OIDC (OpenID Connect)** with IAM Roles for GitHub Actions / GitLab CI.
- For Jenkins on EC2, attach an **IAM Instance Profile / Role** to the Jenkins agent.
- For local machines, use temporary session tokens via AWS SSO / AWS Vault.

---

### Scenario 8: Clean Teardown of Sandbox Infrastructure
**Question:** At the end of every work day, developers need to tear down their sandbox AWS resources to save cloud costs. How should this be automated?
**Solution:**
Run `terraform destroy -auto-approve` via a scheduled cron job or CI/CD pipeline. Because Terraform tracks all created resources in state, it safely tears down all components in reverse dependency order without leaving orphan resources.

---

### Scenario 9: Multi-Environment Isolation (Dev, QA, Prod)
**Question:** What are the recommended approaches to separate Dev, QA, and Prod environments in Terraform?
**Solution:**
1. **Directory-Based Isolation (Recommended for Production):**
   ```text
   environments/
   ├── dev/   (main.tf, terraform.tfvars, backend.tf)
   ├── qa/    (main.tf, terraform.tfvars, backend.tf)
   └── prod/  (main.tf, terraform.tfvars, backend.tf)
   ```
   Provides isolated state files and separate AWS accounts for maximum security.
2. **Workspace-Based Isolation:** Uses `terraform workspace select dev` vs. `prod` within the same directory. Best suited for smaller or single-account setups.

---

### Scenario 10: Infrastructure Drift Detection
**Question:** A developer manually changed an EC2 Security Group in the AWS Console. How does Terraform detect and fix this drift?
**Solution:**
1. Run `terraform plan` or `terraform apply -refresh-only`.
2. Terraform queries the AWS API, detects that the live configuration differs from the state and code, and displays the drift.
3. Running `terraform apply` overwrites the unauthorized manual console change and restores the infrastructure to the desired state defined in code.

---

## 🏁 Summary Checklist for Students

Before moving to Day 2 of Terraform, make sure you can:
- [x] Explain **IaC** and why manual console provisioning fails at scale.
- [x] Clearly articulate the differences between **Terraform** and **Ansible**.
- [x] Define **Provider, Resource, Variable, Output, State, Module, Backend**.
- [x] Successfully install **Terraform CLI** and **AWS CLI** on your local machine.
- [x] Configure AWS credentials via `aws configure`.
- [x] Run the complete lifecycle: `terraform init` ➔ `validate` ➔ `plan` ➔ `apply` ➔ `destroy`.
- [x] Answer core technical and scenario-based interview questions on Terraform basics.

---
*Happy Automating with Terraform! 🚀 Keep coding your infrastructure!*
