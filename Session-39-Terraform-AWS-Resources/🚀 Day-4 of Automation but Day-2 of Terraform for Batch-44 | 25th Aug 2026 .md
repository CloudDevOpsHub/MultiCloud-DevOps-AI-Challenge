# 🚀 DevOps Batch-44 | Terraform Day 2: Practical Infrastructure Provisioning, State Management, Modules, Workspaces & GitHub Workflows

[![Module: Terraform & IaC](https://img.shields.io/badge/Module-Terraform%20%26%20IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](README.md)
[![Cloud: AWS](https://img.shields.io/badge/Cloud-AWS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Interview Q&A: 20+ Questions](https://img.shields.io/badge/Interview%20Q%26A-20%2B%20Included-success?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents
1. [Session Overview](#-session-overview)
2. [End-to-End Terraform Practical Workflow](#1-end-to-end-terraform-practical-workflow)
3. [Deep Dive into Core Commands](#2-deep-dive-into-core-commands)
   - [2.1 `terraform init` (Under the Hood)](#21-terraform-init-under-the-hood)
   - [2.2 `terraform validate` (Syntax vs Reality)](#22-terraform-validate-syntax-vs-reality)
   - [2.3 `terraform plan` (Execution Blueprint)](#23-terraform-plan-execution-blueprint)
   - [2.4 `terraform apply` (Real Infrastructure Provisioning)](#24-terraform-apply-real-infrastructure-provisioning)
   - [2.5 `terraform destroy` (Safe Tear-Down)](#25-terraform-destroy-safe-tear-down)
4. [AWS EC2 Practical Lifecycle & Provisioning States](#3-aws-ec2-practical-lifecycle--provisioning-states)
5. [Terraform State Management & State Locking](#4-terraform-state-management--state-locking)
6. [Terraform vs. Ansible: Practical Demarcation](#5-terraform-vs-ansible-practical-demarcation)
7. [Terraform Modules: Reusability & DRY Architecture](#6-terraform-modules-reusability--dry-architecture)
8. [Terraform Dependency Graphs (`DAG`)](#7-terraform-dependency-graphs-dag)
9. [Credential Security & Best Practices](#8-credential-security--best-practices)
10. [Terraform Providers Architecture](#9-terraform-providers-architecture)
11. [Terraform Workspaces: Environment Isolation](#10-terraform-workspaces-environment-isolation)
12. [Terraform Cloud vs. Terraform Enterprise](#11-terraform-cloud-vs-terraform-enterprise)
13. [Real-World DevOps Collaboration with Git & GitHub](#12-real-world-devops-collaboration-with-git--github)
    - [13.1 Git Fork vs. Git Clone](#131-git-fork-vs-git-clone)
    - [13.2 Feature Branch & Pull Request (PR) Workflow](#132-feature-branch--pull-request-pr-workflow)
14. [Real-World Lab: AWS CloudWatch Log Group Automation](#13-real-world-lab-aws-cloudwatch-log-group-automation)
15. [Key Commands Quick Reference](#14-key-commands-quick-reference)
16. [Comprehensive Concept Map](#15-comprehensive-concept-map)
17. [Top 10 Technical Interview Questions & Answers](#16-top-10-technical-interview-questions--answers)
18. [Top 10 Scenario-Based Interview Questions & Solutions](#17-top-10-scenario-based-interview-questions--solutions)
19. [Quick Revision Mindmap](#18-quick-revision-mindmap)

---

## 🎯 Session Overview

This session moves from theoretical Terraform fundamentals into **hands-on practical implementation** with **AWS EC2**. The focus is on mastering the complete infrastructure lifecycle: initializing configurations, validating syntax, generating speculative execution plans, applying infrastructure changes, managing remote state, and destroying resources safely.

Additionally, the session covers enterprise-grade practices:
* **State File & State Locking:** How Terraform understands infrastructure reality vs. desired state.
* **Terraform Modules & Dependency Graphs:** Writing DRY (Don't Repeat Yourself), reusable, and dependency-aware IaC.
* **Workspaces:** Isolating Dev, QA, and Prod environments using shared codebases.
* **Terraform Cloud & Enterprise:** Scaling team collaboration, role-based access control (RBAC), and governance.
* **Git & GitHub Engineering Workflows:** Feature branching, Fork vs. Clone, Pull Requests (PRs), code reviews, and preventing credential leaks.

> [!IMPORTANT]
> **Interview Focus:** Emphasis is placed on **internal mechanics** (what happens behind the scenes during `init`, `plan`, `apply`, and `destroy`) rather than just memorizing command syntax.

---

## 1. End-to-End Terraform Practical Workflow

In modern DevOps teams, infrastructure code follows a structured CI/CD and provisioning lifecycle:

```text
┌────────────────────────┐
│  GitHub Repository     │ ◀── Developer writes & reviews HCL code
└───────────┬────────────┘
            │ git clone / checkout feature-branch
            ▼
┌────────────────────────┐
│   Navigate to Folder   │ ◀── Directory containing *.tf files
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│    terraform init      │ ◀── Initializes directory & downloads Provider plugins (.terraform/)
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│   terraform validate   │ ◀── Syntactic & structural verification of HCL code
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│    terraform plan      │ ◀── Compares State vs Code, outputs diff (+ create, ~ modify, - destroy)
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│    terraform apply     │ ◀── Acquires lock, calls AWS APIs, updates terraform.tfstate
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│ AWS EC2 Running & Live │ ◀── Real-world infrastructure is active
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│   terraform destroy    │ ◀── Safely terminates resources and cleans up state
└────────────────────────┘
```

---

## 2. Deep Dive into Core Commands

### 2.1 `terraform init` (Under the Hood)

The `terraform init` command initializes a working directory containing Terraform configuration files.

```bash
terraform init
```

```text
               WHAT HAPPENS DURING TERRAFORM INIT
               
  ┌─────────────────────────────────────────────────────────────┐
  │ 1. Reads Root Module (*.tf files)                           │
  │    Scans configuration to find required providers & modules │
  └──────────────────────────────┬──────────────────────────────┘
                                 │
                                 ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ 2. Downloads Provider Plugins                               │
  │    Fetches AWS/Azure/GCP provider binaries from registry    │
  └──────────────────────────────┬──────────────────────────────┘
                                 │
                                 ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ 3. Creates .terraform/ Directory & Lock File                │
  │    Stores downloaded plugins & writes .terraform.lock.hcl   │
  └──────────────────────────────┬──────────────────────────────┘
                                 │
                                 ▼
  ┌─────────────────────────────────────────────────────────────┐
  │ 4. Initializes Backend                                      │
  │    Connects to local or remote state (S3, Terraform Cloud)  │
  └─────────────────────────────────────────────────────────────┘
```

> [!NOTE]
> **Key Interview Takeaway:** `terraform init` is idempotent, meaning you can safely run it multiple times. You **must** re-run `terraform init` whenever you add a new provider, install a new module, or change the backend configuration.

---

### 2.2 `terraform validate` (Syntax vs Reality)

```bash
terraform validate
```

* **What it does:** Validates the syntax of HCL files, verifies attribute names, and checks internal consistency of references.
* **What it does NOT do:** It does **not** check cloud credentials, make API calls to AWS, or verify whether an AMI ID or subnet actually exists in your cloud account.

```text
┌───────────────────────────────────────────────────────────────┐
│                    VALIDATION vs EXECUTION                    │
├──────────────────────┬────────────────────────────────────────┤
│ terraform validate   │ Checks local HCL syntax and schemas    │
├──────────────────────┼────────────────────────────────────────┤
│ terraform plan       │ Reads state & compares with cloud APIs │
├──────────────────────┼────────────────────────────────────────┤
│ terraform apply      │ Creates / modifies actual cloud assets │
└──────────────────────┴────────────────────────────────────────┘
```

---

### 2.3 `terraform plan` (Execution Blueprint)

```bash
terraform plan
```

The planning phase creates an execution plan by:
1. Reading the current state file (`terraform.tfstate`).
2. Refreshing state via cloud API calls to read existing real-world resources.
3. Comparing the desired configuration against the actual state.
4. Outputting a precise diff:
   - `+` **create:** Resource to be created.
   - `~` **update in-place:** Resource attributes modified without recreation.
   - `-` **destroy:** Resource to be removed.
   - `-+` / `+-` **replace:** Resource destroyed and recreated (e.g., changing AMI or Subnet).

---

### 2.4 `terraform apply` (Real Infrastructure Provisioning)

```bash
terraform apply
# Or bypass interactive approval prompt in automated CI pipelines:
terraform apply -auto-approve
```

* Acquires a **state lock** to prevent concurrent modifications.
* Executes the dependency graph in parallel where possible.
* Sends authenticated API requests to cloud providers (e.g., AWS EC2 APIs).
* Writes updated metadata and resource IDs to `terraform.tfstate`.
* Releases the state lock.

---

### 2.5 `terraform destroy` (Safe Tear-Down)

```bash
terraform destroy
# Automated execution:
terraform destroy -auto-approve
```

* Identifies all resources currently tracked in the state file.
* Builds a reverse dependency graph (deleting children before parents, e.g., EC2 before Subnet, Subnet before VPC).
* Calls cloud APIs to delete/terminate resources.
* Updates state file to reflect that resources are no longer managed.

---

## 3. AWS EC2 Practical Lifecycle & Provisioning States

When Terraform creates an EC2 instance, the CLI wait time includes both the API call and AWS asynchronous resource provisioning:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ terraform apply │ ────▶ │  EC2 Created    │ ────▶ │  Status:        │
│ calls AWS API   │       │  Instance ID    │       │  Pending        │
└─────────────────┘       └─────────────────┘       └────────┬────────┘
                                                             │
                                                             ▼
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Apply Complete! │ ◀──── │  Terraform State│ ◀──── │  Status:        │
│ Output displayed│       │  File Updated   │       │  Running        │
└─────────────────┘       └─────────────────┘       └─────────────────┘
```

> [!TIP]
> **Why does `terraform apply` take 30-60 seconds for an EC2 instance?**
> The AWS API immediately returns an Instance ID while the machine is in the `pending` state. Terraform continues polling AWS until the instance reaches the `running` state before marking the step complete.

---

## 4. Terraform State Management & State Locking

Terraform requires a source of truth to map real-world cloud resources to your HCL code. This mapping is stored in the **State File** (`terraform.tfstate`).

```text
                    TERRAFORM STATE AS THE CORE BRIDGE
                    
   Desired Config (*.tf)  ◀──────────────────▶  Real Cloud Resources (AWS)
             ▲                                            ▲
             │                                            │
             └───────────────► [ State File ] ────────────┘
                              (terraform.tfstate)
```

### Why State is Critical:
1. **Resource Mapping:** Maps resource declarations (e.g., `aws_instance.web`) to cloud IDs (e.g., `i-0a1b2c3d4e5f6g7h8`).
2. **Performance Metadata:** Caches attributes to reduce the number of expensive API calls required on large infrastructures.
3. **Dependency Tracking:** Tracks resource relationships so tear-downs happen in exact reverse order.

### State Locking:
When multiple team members or CI/CD pipelines run Terraform simultaneously, concurrent writes can corrupt state.
* Backends like **AWS S3 + DynamoDB** or **Terraform Cloud** support **State Locking**.
* Terraform automatically acquires a lock before `plan`/`apply`/`destroy` and releases it once the operation concludes.

---

## 5. Terraform vs. Ansible: Practical Demarcation

Understanding tool boundaries is a core interview topic and architectural best practice:

```text
               DEVOPS INFRASTRUCTURE & CONFIGURATION PIPELINE
               
   ┌─────────────────────────────────────────────────────────────┐
   │                    TERRAFORM (Provisioning)                 │
   │  Creates Cloud Infrastructure: VPC, Subnets, EC2, S3, RDS   │
   └──────────────────────────────┬──────────────────────────────┘
                                  │
                                  ▼
   ┌─────────────────────────────────────────────────────────────┐
   │                    ANSIBLE (Configuration)                  │
   │  Configures OS, Installs Nginx/Java, Hardens Security, Apps │
   └─────────────────────────────────────────────────────────────┘
```

| Feature / Dimension | Terraform | Ansible |
| :--- | :--- | :--- |
| **Primary Role** | Infrastructure Provisioning (IaC) | Configuration Management & App Deployment |
| **Philosophy** | Declarative (Define desired end-state) | Hybrid (Declarative tasks / Procedural playbooks) |
| **State Tracking** | Explicit state file (`terraform.tfstate`) | Stateless (Discovers target machine state dynamically) |
| **Target Layer** | Cloud APIs (AWS, Azure, GCP, K8s) | Operating System & Application runtime inside VMs |
| **Best Used For** | Spinning up VPCs, EC2 instances, DBs | Installing Docker, configuring Nginx, patching Linux |

> [!WARNING]
> **Anti-Pattern:** Avoid using Terraform `local-exec` or SSH scripts to install large application suites onto EC2 instances. Use Ansible, Cloud-Init, or Golden AMI baking (Packer) for OS-level configurations.

---

## 6. Terraform Modules: Reusability & DRY Architecture

Without modules, infrastructure code leads to massive code duplication across environments:

```text
WITHOUT MODULES (Code Duplication):
Dev   ──▶ [ EC2 + VPC + Subnets + SG + S3 ] (500 lines of code)
QA    ──▶ [ EC2 + VPC + Subnets + SG + S3 ] (500 lines of code)
Prod  ──▶ [ EC2 + VPC + Subnets + SG + S3 ] (500 lines of code)

WITH REUSABLE MODULES (Clean & DRY):
                    ┌─────────────────────────┐
                    │      Custom Module      │
                    │  ./modules/aws-vpc-app  │
                    └────────────┬────────────┘
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
   [ Dev Project ]        [ QA Project ]          [ Prod Project ]
    source = "..."         source = "..."          source = "..."
    env    = "dev"         env    = "qa"           env    = "prod"
```

### Calling a Module in HCL:

```hcl
# main.tf (Root Module calling a Child Module)
module "web_server_cluster" {
  source        = "./modules/ec2-cluster"
  instance_type = "t3.micro"
  environment   = "production"
  instance_count= 3
}
```

### Module Sources:
Modules can be sourced from:
* **Local Paths:** `source = "./modules/vpc"`
* **Terraform Registry:** `source = "terraform-aws-modules/vpc/aws"`
* **Git Repositories:** `source = "git::https://github.com/org/terraform-modules.git"`
* **S3 Buckets:** `source = "s3::https://s3.amazonaws.com/my-tf-modules/vpc.zip"`

---

## 7. Terraform Dependency Graphs (`DAG`)

Terraform analyzes configuration files to create a **Directed Acyclic Graph (DAG)** of all resources.

```text
               TERRAFORM DIRECTED ACYCLIC GRAPH (DAG)
               
                         ┌─────────────────┐
                         │   aws_vpc.main  │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │aws_subnet.public│
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │aws_instance.app │
                         └─────────────────┘
```

* **Implicit Dependencies:** Terraform detects references (e.g., `subnet_id = aws_subnet.public.id`) and automatically creates the VPC before the Subnet, and the Subnet before the EC2 instance.
* **Explicit Dependencies:** When dependencies cannot be inferred automatically, use `depends_on = [aws_s3_bucket.data_bucket]`.

---

## 8. Credential Security & Best Practices

> [!CAUTION]
> **Zero Tolerance Security Rule:** Never hard-code cloud credentials (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`) directly inside `.tf` files or commit them to Git repositories.

```text
❌ DANGEROUS / INSECURE (Hard-coding):
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAEXAMPLE123456"      <-- NEVER DO THIS!
  secret_key = "wJalrXUtnFEMI/K7MDENG"  <-- NEVER DO THIS!
}

✅ SECURE PRACTICE (Environment Variables or IAM Roles):
provider "aws" {
  region = "us-east-1"
}
# Credentials are automatically loaded from:
# 1. AWS IAM Instance Profile / Role
# 2. Environment Variables: AWS_ACCESS_KEY_ID & AWS_SECRET_ACCESS_KEY
# 3. AWS Credentials File: ~/.aws/credentials (configured via `aws configure`)
```

### If credentials are leaked:
1. **Immediate Revocation:** Invalidate/delete the IAM access key immediately in AWS IAM.
2. **Rotate Credentials:** Generate a new key pair for authorized systems.
3. **Audit CloudTrail:** Review AWS CloudTrail logs for unauthorized API calls.
4. **Git Scrubbing:** Use tools like `git-filter-repo` or BFG Repo-Cleaner to remove secrets from Git history.

---

## 9. Terraform Providers Architecture

A **Provider** is a plugin that translates Terraform HCL declarations into API calls for a specific target platform.

```text
┌─────────────────────────────────────────────────────────────┐
│                    Terraform Core Engine                    │
│           (Graph computation, State management)             │
└──────────────┬──────────────┬──────────────┬────────────────┘
               │              │              │
               ▼              ▼              ▼
        ┌─────────────┐┌─────────────┐┌─────────────┐
        │AWS Provider ││Azure Provider││GCP Provider │
        │ Plugin (RPC)││ Plugin (RPC)││ Plugin (RPC)│
        └──────┬──────┘└──────┬──────┘└──────┬──────┘
               │              │              │
               ▼              ▼              ▼
            AWS API      Azure ARM API   Google Cloud
```

* **Provider Isolation:** An AWS EC2 resource (`aws_instance`) cannot be deployed to Google Cloud or Azure. Each cloud requires its own provider and resource syntax.
* **Multi-Provider Support:** A single Terraform project can combine multiple providers (e.g., create an AWS EC2 instance and register its IP in Cloudflare DNS).

---

## 10. Terraform Workspaces: Environment Isolation

Workspaces allow you to manage multiple isolated environments (**dev, staging, prod**) from the exact same configuration directory with separate state files.

```text
                          TERRAFORM WORKSPACES
                          
                    ┌──────────────────────────────┐
                    │    Root Terraform Code       │
                    │         (*.tf files)         │
                    └──────────────┬───────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         ▼                         ▼                         ▼
   [ dev Workspace ]        [ qa Workspace ]         [ prod Workspace ]
   State: dev.tfstate       State: qa.tfstate        State: prod.tfstate
```

### Essential Workspace Commands:

```bash
# List all existing workspaces
terraform workspace list

# Create and switch to a new workspace
terraform workspace new dev
terraform workspace new prod

# Show current active workspace
terraform workspace show

# Switch between workspaces
terraform workspace select prod
```

### Dynamic Configuration using Workspace Name:

```hcl
resource "aws_instance" "server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = terraform.workspace == "prod" ? "t3.medium" : "t3.micro"

  tags = {
    Name        = "server-${terraform.workspace}"
    Environment = terraform.workspace
  }
}
```

---

## 11. Terraform Cloud vs. Terraform Enterprise

As teams grow, managing Terraform locally becomes unsustainable. HashiCorp provides managed and self-hosted collaboration platforms:

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                     TERRAFORM COLLABORATION MATRIX                      │
├─────────────────────┬──────────────────┬─────────────────┬──────────────┤
│ Feature             │ Open Source CLI  │ Terraform Cloud │ Enterprise   │
├─────────────────────┼──────────────────┼─────────────────┼──────────────┤
│ Execution           │ Local Workstation│ Managed Runners │ Private VNet │
│ State Storage       │ Local/Remote S3  │ Centralized SaaS│ On-Prem / VPC│
│ State Locking       │ S3 + DynamoDB    │ Built-in Native │ Built-in     │
│ RBAC & Teams        │ ❌ None          │ ✅ Included     │ ✅ Advanced  │
│ SSO (SAML/Okta)     │ ❌ None          │ ✅ Business tier│ ✅ Native    │
│ Policy as Code (OIDC/Sentinel)│ ❌ None│ ✅ Included     │ ✅ Advanced  │
│ Private Registry    │ ❌ None          │ ✅ Included     │ ✅ Included  │
└─────────────────────┴──────────────────┴─────────────────┴──────────────┘
```

---

## 12. Real-World DevOps Collaboration with Git & GitHub

### 13.1 Git Fork vs. Git Clone

```text
┌─────────────────────────────────────────────────────────────────────────┐
│                           FORK vs CLONE                                 │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  [ Upstream GitHub Repo ] ───( Fork )───▶ [ Personal GitHub Repo ]      │
│  (Remote-to-Remote copy)                   (Remote Forked copy)         │
│                                                     │                   │
│                                                  ( Clone )              │
│                                                     │                   │
│                                                     ▼                   │
│                                            [ Local Workstation ]        │
│                                            (Remote-to-Local copy)       │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

* **Fork:** A server-side copy of a repository on GitHub under your account. Enables contributing to projects where you do not have write access.
* **Clone:** Downloads a complete Git repository (with full history and branches) from remote to your local file system.

---

### 13.2 Feature Branch & Pull Request (PR) Workflow

In production DevOps teams, no engineer commits directly to `main`. All changes follow the PR pipeline:

```text
┌──────────────┐     git checkout -b feature/ec2-infra     ┌──────────────────────┐
│  main branch │ ─────────────────────────────────────────▶│    Feature Branch    │
└──────────────┘                                           └──────────┬───────────┘
       ▲                                                              │
       │                                                              ▼
       │                                                   ┌──────────────────────┐
       │                                                   │ Write Terraform Code │
       │                                                   └──────────┬───────────┘
       │                                                              │
       │                                                              ▼
       │                                                   ┌──────────────────────┐
       │                                                   │ git commit & push    │
       │                                                   └──────────┬───────────┘
       │                                                              │
       │                                                              ▼
       │                     Peer Review & Diff            ┌──────────────────────┐
       └────────────────── [ Pull Request (PR) ] ◀─────────┤ Open Pull Request    │
                            Green (+) / Red (-)            └──────────────────────┘
```

1. Create a descriptive feature branch: `git checkout -b feature/cloudwatch-logging`
2. Write and test Terraform code locally: `terraform validate` & `terraform plan`
3. Commit and push: `git push origin feature/cloudwatch-logging`
4. Open a **Pull Request (PR)** against `main`.
5. Team reviews the code diff (**Green = Additions, Red = Deletions**).
6. Automated CI checks run `terraform plan`.
7. Code is approved and merged into `main`.

---

## 13. Real-World Lab: AWS CloudWatch Log Group Automation

During the practical session, students authored Terraform code for managing CloudWatch log groups for a development workload.

```hcl
# cloudwatch.tf
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

# AWS CloudWatch Log Group with 7-day retention for Dev
resource "aws_cloudwatch_log_group" "dev_app_logs" {
  name              = "/aws/dev/ecommerce-backend"
  retention_in_days = 7

  tags = {
    Environment = "Development"
    Application = "ECommerceBackend"
    ManagedBy   = "Terraform"
  }
}
```

> [!TIP]
> Setting log retention periods in CloudWatch is a vital cloud cost optimization practice. Indefinite log retention leads to unexpected storage costs.

---

## 14. Key Commands Quick Reference

| Command | Category | Description |
| :--- | :--- | :--- |
| `terraform init` | Core Workflow | Initializes working directory, installs provider plugins & backends |
| `terraform validate` | Core Workflow | Verifies syntax, arguments, and internal consistency |
| `terraform plan` | Core Workflow | Compares state vs desired code; displays proposed execution plan |
| `terraform apply` | Core Workflow | Provisions infrastructure and updates state file |
| `terraform apply -auto-approve` | CI/CD | Applies changes without manual interactive prompt |
| `terraform destroy` | Core Workflow | Terminates all resources tracked in the current state |
| `terraform show` | Inspection | Inspects state file or plan output in human-readable format |
| `terraform fmt` | Formatting | Rewrites configuration files to canonical format and style |
| `terraform workspace list` | Workspaces | Lists all available workspaces in the repository |
| `terraform workspace new <name>` | Workspaces | Creates and switches to a new environment workspace |
| `terraform workspace select <name>` | Workspaces | Switches active context to an existing workspace |
| `terraform workspace show` | Workspaces | Prints the name of the currently active workspace |

---

## 15. Comprehensive Concept Map

```text
                               TERRAFORM ARCHITECTURE
                               
   ┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
   │    Providers     │      │   State Engine   │      │     Modules      │
   │  AWS, Azure, GCP │      │ Lock, Drift, Sync│      │ Reusable Systems │
   └────────┬─────────┘      └────────┬─────────┘      └────────┬─────────┘
            │                         │                         │
            └─────────────────────────┼─────────────────────────┘
                                      │
                                      ▼
                        ┌───────────────────────────┐
                        │      Core Workflow        │
                        │ init ─▶ validate ─▶ plan  │
                        │   ─▶ apply ─▶ destroy     │
                        └─────────────┬─────────────┘
                                      │
            ┌─────────────────────────┴─────────────────────────┐
            │                                                   │
            ▼                                                   ▼
   ┌──────────────────┐                               ┌──────────────────┐
   │    Workspaces    │                               │ Team & Git Flow  │
   │ Dev / QA / Prod  │                               │ PRs, Diff, Lock  │
   └──────────────────┘                               └──────────────────┘
```

---

## 16. Top 10 Technical Interview Questions & Answers

### Q1. What is Terraform and why is it preferred over ClickOps?
**Answer:**
Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp that lets engineers define, provision, and manage cloud and on-prem infrastructure using declarative configuration files (HCL).
**Key benefits over manual ClickOps:**
* **Consistency & Repeatability:** Eliminates human errors and ensures identical environments across Dev, QA, and Prod.
* **Version Control:** Infrastructure changes are tracked via Git commits and reviewed via Pull Requests.
* **Automated Lifecycle:** Automatically computes dependencies and tears down resources cleanly.

---

### Q2. What specifically happens behind the scenes during `terraform init`?
**Answer:**
During `terraform init`, Terraform:
1. Reads configuration files in the root directory.
2. Identifies required providers (e.g., `hashicorp/aws`) and downloads their binary plugins from the Terraform Registry into the local `.terraform/providers/` directory.
3. Generates or updates the dependency lock file (`.terraform.lock.hcl`).
4. Downloads any referenced child modules.
5. Initializes the configured backend (e.g., S3 or Terraform Cloud) to prepare state access.

---

### Q3. How does `terraform validate` differ from `terraform plan`?
**Answer:**
* `terraform validate` operates purely locally without making remote cloud API calls or inspecting state. It verifies syntax errors, invalid attribute names, and variable type mismatches.
* `terraform plan` connects to the configured backend, refreshes the current state by querying cloud APIs, compares desired code vs. real-world infrastructure, and outputs the exact execution diff.

---

### Q4. What is the role of the Terraform State file (`terraform.tfstate`)?
**Answer:**
The state file acts as the single source of truth mapping declared HCL code to real-world cloud resources (mapping `aws_instance.my_server` to AWS ID `i-07f9c8e1a`). It also stores resource metadata, performance caches, and tracks dependencies so that resources are created or destroyed in the correct topological order.

---

### Q5. What is a Terraform Module and why should you use one?
**Answer:**
A Terraform module is a container for multiple resources that are used together.
**Benefits:**
* **DRY Code:** Avoids duplicating 300+ lines of VPC/EC2 configurations across multiple environments.
* **Standardization:** Enforces organizational compliance, naming standards, and security defaults.
* **Encapsulation:** Consumers only provide input variables without needing to know low-level resource configurations.

---

### Q6. How does Terraform determine the creation order of resources?
**Answer:**
Terraform builds a **Directed Acyclic Graph (DAG)** by analyzing implicit references (e.g., an EC2 instance referencing `aws_subnet.public.id`) and explicit dependencies declared with `depends_on`. It provisions parent resources first and child resources subsequently, running non-dependent resources in parallel.

---

### Q7. What are Terraform Workspaces and when should they be used?
**Answer:**
Terraform workspaces allow you to maintain multiple separate state files using a single configuration codebase. They are ideal for separating lightweight environments (e.g., `dev`, `qa`, `staging`). For complex multi-account production setups, separate directories or state backends per AWS account are recommended.

---

### Q8. What is the difference between Git Fork and Git Clone?
**Answer:**
* **Git Fork:** A remote GitHub-to-GitHub operation that duplicates a repository into your personal GitHub account.
* **Git Clone:** A remote-to-local operation that downloads a Git repository from GitHub onto your local workstation file system.

---

### Q9. Why should cloud access keys never be hard-coded in Terraform code?
**Answer:**
Hard-coded credentials committed to version control systems like GitHub are vulnerable to automated scrapers and malicious actors. This can lead to unauthorized infrastructure provisioning, data breaches, and massive cloud bills. Authentication should always use IAM Roles, environment variables, or encrypted secret vaults.

---

### Q10. What does `terraform destroy` do and how does it determine deletion order?
**Answer:**
`terraform destroy` reads the state file, determines which resources are currently managed by Terraform, builds a reverse dependency graph (deleting leaf/dependent resources before parent resources), prompts the user for confirmation, and issues delete API calls to the cloud provider.

---

## 17. Top 10 Scenario-Based Interview Questions & Solutions

### Scenario 1: `terraform init` fails after cloning a teammate's repository
* **Problem:** You clone a repo and immediately run `terraform plan`, but it throws an error: `Error: Could not load plugin... provider not initialized`.
* **Solution:** Run `terraform init` to download the provider plugins into your local `.terraform` directory. Ensure you have internet access or configured mirror paths if behind a corporate proxy.

---

### Scenario 2: `terraform plan` shows an EC2 instance will be destroyed and recreated
* **Problem:** You made a small change and `terraform plan` shows `-+` (replacement) instead of `~` (update in-place).
* **Solution:** Inspect the plan diff carefully. Changing immutable cloud attributes (such as `ami`, `availability_zone`, or `subnet_id`) forces AWS to terminate the instance and create a new one. If downtime is unacceptable, adjust configuration or use `create_before_destroy` lifecycle rules.

---

### Scenario 3: EC2 provisioning hangs in `terraform apply`
* **Problem:** `terraform apply` prints `aws_instance.app: Still creating... [3m00s elapsed]` and seems stuck.
* **Solution:** Log into the AWS Management Console to check the instance state. An EC2 instance can remain in the `pending` state while AWS allocates physical host hardware, runs health checks, or assigns Elastic IPs. Check if VPC subnet route tables or internet gateways are misconfigured.

---

### Scenario 4: Five teams require identical VPC and network setups
* **Problem:** Five project teams are writing their own duplicate VPC configuration blocks.
* **Solution:** Package the standard networking topology into a shared **Terraform Module** hosted in a central Git repository or private registry. Each team calls the module with their specific CIDR block and environment parameters.

---

### Scenario 5: Isolating Dev and Production state files
* **Problem:** Dev experiments must never risk overwriting or corrupting production state.
* **Solution:**
  1. Use **Terraform Workspaces** (`dev` vs `prod`) for lightweight isolation.
  2. For strict enterprise separation, use distinct AWS S3 backend buckets located in separate AWS Accounts with dedicated IAM permissions.

---

### Scenario 6: Leaked AWS Access Keys in a public Git commit
* **Problem:** An engineer accidentally pushed `.tf` files containing AWS secret keys to GitHub.
* **Solution:**
  1. Immediately deactivate and delete the IAM access key in the AWS IAM Console.
  2. Rotate credentials across authorized services.
  3. Inspect AWS CloudTrail for unusual API activity.
  4. Rewrite repository Git history using `git-filter-repo` or BFG to remove the secret from all historical commits.

---

### Scenario 7: Preventing direct commits to the `main` branch
* **Problem:** Developers are pushing untested Terraform code directly to production `main`.
* **Solution:** Enable **Branch Protection Rules** on GitHub for `main`:
  * Require Pull Request reviews before merging.
  * Require status checks (e.g., CI running `terraform validate` and `terraform plan`) to pass before merging.
  * Restrict push access to repository administrators.

---

### Scenario 8: Terraform runs on engineer's laptop but fails on a teammate's machine
* **Problem:** Configuration applies fine on developer A's machine, but developer B gets version mismatch errors.
* **Solution:**
  * Ensure both run `terraform init`.
  * Pin Terraform and Provider versions inside `terraform.tf` (`required_version` and `required_providers`).
  * Commit `.terraform.lock.hcl` to Git to ensure identical provider versions are installed across machines.

---

### Scenario 9: Resource creation order mismatch during multi-resource provisioning
* **Problem:** Terraform fails to create an EC2 instance because the security group or subnet is not ready.
* **Solution:** Use **Implicit References** (e.g., `vpc_security_group_ids = [aws_security_group.web_sg.id]`). Terraform will automatically parse the dependency and create the Security Group first. Use explicit `depends_on` if an unreferenced dependency exists.

---

### Scenario 10: Scaling from individual laptops to team-wide centralized automation
* **Problem:** Multiple engineers running Terraform locally face state locking conflicts, lack of audit logs, and inconsistent credentials.
* **Solution:** Adopt **Terraform Cloud** or **Terraform Enterprise**:
  * Centralizes remote state storage and native state locking.
  * Triggers automated plans upon GitHub Pull Requests (VCS integration).
  * Enforces Role-Based Access Control (RBAC) and Policy as Code via Sentinel/OPA.

---

## 18. Quick Revision Mindmap

```text
                      TERRAFORM LIFECYCLE REVISION
                      
  terraform init       ──▶ Downloads Providers & Modules into .terraform/
  terraform validate   ──▶ Validates local HCL syntax and configuration schema
  terraform plan       ──▶ Compares State vs Cloud API; outputs execution diff
  terraform apply      ──▶ Provisions real infrastructure & updates terraform.tfstate
  terraform destroy    ──▶ Tears down resources in reverse topological dependency order
  
                      COLLABORATION & GOVERNANCE
                      
  State File           ──▶ Maps code to real-world cloud resources
  State Locking        ──▶ Prevents simultaneous corrupting writes (DynamoDB / TF Cloud)
  Modules              ──▶ Reusable, parameterized infrastructure blueprints
  Workspaces           ──▶ Isolated environments (dev, qa, prod) sharing one codebase
  GitHub PR Flow       ──▶ Feature Branch ─▶ Pull Request ─▶ Code Review ─▶ Merge
```

---
*Happy DevOps Learning! 🚀 Master the mechanics behind the commands, automate fearlessly, and excel in your cloud interviews!*
