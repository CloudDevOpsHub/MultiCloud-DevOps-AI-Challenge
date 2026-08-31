# Project 3: Deploying a Three-Tier Architecture on AWS Using Terraform

[![Module: Terraform & IaC](https://img.shields.io/badge/Module-Terraform%20%26%20IaC-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](README.md)
[![Cloud: AWS](https://img.shields.io/badge/Cloud-AWS%20Cloud-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![Architecture: 3--Tier](https://img.shields.io/badge/Architecture-3--Tier%20AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents

1. [Project Overview](#1-project-overview)
2. [Why Terraform Is Used](#2-why-terraform-is-used)
3. [High-Level AWS Architecture](#3-high-level-aws-architecture)
4. [Terraform Project Structure](#4-terraform-project-structure)
5. [Role of Each Important Terraform File](#5-role-of-each-important-terraform-file)
   - [5.1 provider.tf](#51-providertf)
   - [5.2 main.tf](#52-maintf)
   - [5.3 variables.tf](#53-variablestf)
   - [5.4 terraform.tfvars](#54-terraformtfvars)
   - [5.5 output.tf](#55-outputtf)
   - [5.6 backend.tf](#56-backendtf)
6. [Networking Module](#6-networking-module)
7. [Web / Frontend Layer](#7-web--frontend-layer)
8. [Auto Scaling Module](#8-auto-scaling-module)
9. [Launch Template](#9-launch-template)
10. [Database Module](#10-database-module)
11. [Prepare the Working Environment](#11-prepare-the-working-environment)
12. [Configure AWS CLI](#12-configure-aws-cli)
13. [Download the Terraform Project](#13-download-the-terraform-project)
14. [Inspect the Project Before Running Terraform](#14-inspect-the-project-before-running-terraform)
15. [Run Terraform Init](#15-run-terraform-init)
16. [Validate the Terraform Code](#16-validate-the-terraform-code)
17. [Understand the Terraform Dependency Graph](#17-understand-the-terraform-dependency-graph)
18. [Run Terraform Plan](#18-run-terraform-plan)
19. [Troubleshooting Problem #1: Invalid / Unavailable AMI](#19-troubleshooting-problem-1-invalid--unavailable-ami)
20. [Troubleshooting Problem #2: Database Instance Class](#20-troubleshooting-problem-2-database-instance-class)
21. [Run Terraform Apply](#21-run-terraform-apply)
22. [Understand Terraform State During Apply](#22-understand-terraform-state-during-apply)
23. [Partial Deployment and Recovery](#23-partial-deployment-and-recovery)
24. [Verify the Application](#24-verify-the-application)
25. [Test the Complete Application Flow](#25-test-the-complete-application-flow)
26. [Auto Scaling Verification](#26-auto-scaling-verification)
27. [Understand Configuration Drift](#27-understand-configuration-drift)
28. [CloudWatch and Monitoring](#28-cloudwatch-and-monitoring)
29. [Performance / Deployment Speed Discussion](#29-performance--deployment-speed-discussion)
30. [Application vs Infrastructure Troubleshooting](#30-application-vs-infrastructure-troubleshooting)
31. [Final Infrastructure Verification Checklist](#31-final-infrastructure-verification-checklist)
32. [Complete Project Execution Flow](#32-complete-project-execution-flow)
33. [Destroy the Infrastructure](#33-destroy-the-infrastructure)
34. [Real-Time Troubleshooting Method](#34-real-time-troubleshooting-method)
35. [Key Troubleshooting Examples From This Project](#35-key-troubleshooting-examples-from-this-project)
36. [Project in One Page](#36-project-in-one-page)
37. [What You Should Be Able to Explain After Completing This Project](#37-what-you-should-be-able-to-explain-after-completing-this-project)
38. [Final Project Lifecycle & Key Takeaways](#38-final-project-lifecycle--key-takeaways)
39. [Top 10 Technical Interview Questions & Answers](#39-top-10-technical-interview-questions--answers)
40. [Top 10 Scenario-Based Interview Questions & Solutions](#40-top-10-scenario-based-interview-questions--solutions)

---

## 1. Project Overview

This project demonstrates how to deploy a **three-tier application architecture on AWS using Terraform Infrastructure as Code (IaC)** instead of manually creating AWS resources.

The project is designed around approximately **40 AWS resources**, organized into reusable Terraform modules. The main objective is to provision the infrastructure in a structured, repeatable, and automated manner.

The three major application layers are:

1. **Presentation Layer**
   - Frontend / Web Server
   - Receives requests from users.

2. **Application / Logic Layer**
   - Application Server
   - Contains the application's business logic.

3. **Database Layer**
   - Database Server
   - Stores application and user data.

The request flow is:

```text
User
  |
  v
Web Server / Frontend
  |
  v
Application Server
  |
  v
Database
  |
  v
Application Response
```

Users should interact with the web layer rather than directly accessing the application server or database. Authentication is therefore an important part of this architecture.

---

## 2. Why Terraform Is Used

Creating approximately 40 AWS resources manually is time-consuming and increases the possibility of configuration mistakes.

Terraform allows the infrastructure to be described as code and then provisioned repeatedly using a standard workflow:

```text
Terraform Code
      |
      v
terraform init
      |
      v
terraform validate
      |
      v
terraform plan
      |
      v
terraform apply
      |
      v
AWS Infrastructure
```

The session specifically emphasized that the infrastructure should be created using Terraform rather than manually.

The general Terraform workflow discussed in the session was:

> **Write/organize code → initialize → validate → plan → apply → verify → troubleshoot if required → destroy when finished.**

---

## 3. High-Level AWS Architecture

The project uses different AWS components to implement the three-tier architecture.

The important resource categories discussed include:

- VPC
- Public and private subnets
- Route tables
- Internet Gateway
- NAT Gateway
- Elastic IP
- Security Groups
- EC2
- Application Load Balancer
- Auto Scaling Group
- Launch Template
- RDS / MySQL database
- DNS-related configuration
- CloudWatch
- IAM-related resources
- Other supporting AWS resources

The exact number of resources may vary depending on the implementation, but the project was presented as approximately **40 AWS resources**.

---

## 4. Terraform Project Structure

The project is not written as one huge Terraform file. It is divided into multiple files and modules.

A conceptual structure from the session is:

```text
project/
│
├── main.tf
├── provider.tf
├── variables.tf
├── terraform.tfvars
├── output.tf
├── backend.tf
│
└── modules/
    │
    ├── networking/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── output.tf
    │
    ├── web/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── output.tf
    │
    ├── autoscaling/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── output.tf
    │
    └── database/
        ├── main.tf
        ├── variables.tf
        └── output.tf
```

The root `main.tf` calls the different modules, while the modules contain the actual resource definitions:

```text
Root main.tf
     |
     +---- Networking Module
     |
     +---- Web / Auto Scaling Module
     |
     +---- Database Module
```

This modular approach prevents the entire infrastructure from becoming one enormous Terraform file that nobody wants to touch.

---

## 5. Role of Each Important Terraform File

### 5.1 provider.tf

The provider configuration identifies the cloud provider. In this project, the provider is **AWS**.

Conceptually:

```hcl
provider "aws" {
  region = ...
}
```

The exact configuration should be taken from the provided project code.

---

### 5.2 main.tf

`main.tf` acts as the main/root Terraform configuration. Instead of defining every AWS resource directly in the root file, it calls the required modules.

Conceptually:

```text
main.tf
   |
   +--> networking module
   |
   +--> web/autoscaling module
   |
   +--> database module
```

The root `main.tf` calls three major modules and those modules contain predefined infrastructure code.

---

### 5.3 variables.tf

Variables make the infrastructure reusable.

For example, instead of hardcoding an application name or environment-specific value throughout the code, the value can be supplied through variables. Project values such as the application name and AWS region can be changed through variables rather than modifying the complete Terraform code.

---

### 5.4 terraform.tfvars

`terraform.tfvars` provides values for the Terraform variables.

The session showed the AWS region being supplied through the Terraform variables configuration, with the demonstrated environment using a region corresponding to `West 2`.

This makes the project easier to reuse for another client, environment, or region:

```text
Client A
   |
   +--> Region A

Client B
   |
   +--> Region B
```

The infrastructure code can remain largely unchanged while the input values change.

---

### 5.5 output.tf

`output.tf` exposes useful information after Terraform creates the infrastructure.

Database passwords should be marked as sensitive so that they are not unnecessarily exposed to users.

Conceptually:

```hcl
output "db_password" {
  value     = ...
  sensitive = true
}
```

---

### 5.6 backend.tf

The project structure shown in the session also included a backend-related configuration. The exact backend settings should be taken from the original project code rather than guessed.

---

## 6. Networking Module

The networking module is responsible for creating the network foundation required by the application, including:

- VPC
- Subnets
- Route tables
- Security groups
- Related networking components

The conceptual flow is:

```text
VPC
 |
 +---- Public Subnet
 |       |
 |       +---- Web / Load Balancer
 |
 +---- Private Subnet
         |
         +---- Application Resources
         |
         +---- Database
```

- **Internet Gateway:** Provides internet connectivity to the VPC.
- **Route Tables:** Control how traffic is distributed.
- **NAT Gateway:** Provides the required private-side connectivity, allowing private resources to reach external destinations without making the private database directly public.

---

## 7. Web / Frontend Layer

The web layer represents the presentation tier.

- The project uses EC2-based infrastructure as part of the web/application environment.
- The EC2 instance acts as the web server or frontend portion of the application.
- A load balancer distributes incoming traffic across available application/web instances.

Conceptually:

```text
Internet
   |
   v
Load Balancer
   |
   +---- EC2 Instance
   |
   +---- EC2 Instance
   |
   +---- EC2 Instance
```

---

## 8. Auto Scaling Module

The Auto Scaling module provides scalability.

The session demonstrates an Auto Scaling Group with:

```text
Desired Capacity = 1
```

meaning one instance should normally remain available. When the workload increases, the group can scale the number of instances upward.

The basic flow is:

```text
Low Traffic
    |
    v
1 EC2 Instance

Traffic Increases
    |
    v
Auto Scaling
    |
    v
Multiple EC2 Instances
```

---

## 9. Launch Template

The Auto Scaling Group needs a consistent definition for launching EC2 instances. It uses a **Launch Template** that can be reused whenever additional instances need to be created.

Conceptually:

```text
Launch Template
       |
       +---- AMI
       +---- Instance Configuration
       +---- Other EC2 Settings
                 |
                 v
          Auto Scaling Group
                 |
          +------+------+
          |      |      |
         EC2    EC2    EC2
```

This ensures that newly created instances follow the exact same configuration.

---

## 10. Database Module

The database module contains database-related resources.

- The session uses **MySQL on Amazon RDS** (demonstrated version: **MySQL 8.4**).
- The database is intended to remain **private** rather than being directly exposed to the public internet.

Application flow:

```text
User
 |
 v
Web Server
 |
 v
Application Server
 |
 v
Private Database
```

The database stores application information, such as the data used by the application during the demonstration.

---

## 11. Prepare the Working Environment

Before deploying the project, several prerequisites are required:

- AWS account
- AWS credentials
- AWS CLI
- Git / Git Bash
- Terraform
- GitHub / project repository
- Terraform project code

The setup can initially be performed on a local machine for learning, while the same process can later be performed on a remote machine in a real project environment.

---

## 12. Configure AWS CLI

AWS credentials are required so that Terraform can communicate with AWS. The AWS CLI allows the machine to understand and execute AWS commands, and credentials authenticate the machine against AWS.

Command:

```bash
aws configure
```

Configure the appropriate AWS credentials and region.

> **Important:** Never hardcode access keys or secret keys inside Terraform source code.

---

## 13. Download the Terraform Project

Create a working directory:

```bash
mkdir project-3
cd project-3
```

Clone the repository and enter the downloaded directory:

```bash
git clone <repository-url>
cd <project-directory>
```

Verify that the Terraform files and module directories are present.

---

## 14. Inspect the Project Before Running Terraform

Before executing anything, inspect the structure:

```text
main.tf
provider.tf
variables.tf
terraform.tfvars
output.tf
backend.tf
modules/
```

Then inspect:

```text
modules/
├── networking/
├── web/
├── autoscaling/
└── database/
```

Understand which module is responsible for which infrastructure component before running any execution commands.

---

## 15. Run Terraform Init

After entering the Terraform project directory, initialize Terraform:

```bash
terraform init
```

`terraform init` initializes the working directory and downloads the required providers, dependencies, and modules.

Expected outcome:

```text
Terraform initialization successful
```

Only proceed after initialization succeeds.

---

## 16. Validate the Terraform Code

Validate the Terraform configuration:

```bash
terraform validate
```

Checks whether the Terraform configuration is syntactically valid and whether Terraform can understand the configuration.

Workflow:

```text
terraform init
       |
       v
terraform validate
```

If validation fails:
1. Read the error.
2. Identify the file and line number.
3. Correct the configuration.
4. Run validation again.

---

## 17. Understand the Terraform Dependency Graph

Terraform manages resource dependencies using a graph:

```bash
terraform graph
```

The graph represents relationships between Terraform resources and modules, answering:
- Which resource depends on which?
- What gets created before another resource?
- How are modules connected?

Terraform determines resource dependencies through its configuration and dependency graph rather than relying on manual sequential steps.

---

## 18. Run Terraform Plan

Once validation succeeds:

```bash
terraform plan
```

Terraform calculates what infrastructure needs to be created, modified, or removed:

```text
Plan:
    X resources to add
    Y resources to change
    Z resources to destroy
```

`terraform plan` provides an opportunity to detect configuration problems before making changes to AWS. Do not skip this stage.

---

## 19. Troubleshooting Problem #1: Invalid / Unavailable AMI

One of the major troubleshooting exercises involved an EC2 AMI that was no longer available.

### Step 1: Read the Terraform error
Do not immediately change random lines. Identify:
- File
- Line number
- Resource
- AMI reference
- Error message

### Step 2: Locate the affected module
Trace the issue to the Auto Scaling module and the AMI configuration.

### Step 3: Check the AMI in AWS
Search for the referenced AMI and verify whether it still exists. The old image was unavailable because it had been removed.

### Step 4: Replace the old AMI reference
Update the Terraform configuration with an available compatible AMI.

### Step 5: Save the code
```text
Ctrl + S
```

### Step 6: Reinitialize and plan again
```bash
terraform init
terraform plan
```

---

## 20. Troubleshooting Problem #2: Database Instance Class

A second major problem occurred during database creation when attempting to use an unsupported/obsolete database instance class.

The error indicated that the previous `db.t2...` class needed to be updated to a `db.t3...` class (demonstrated replacement: `db.t3.micro`).

Troubleshooting process:

```text
terraform apply
       |
       v
Database creation error
       |
       v
Read error
       |
       v
Locate database module
       |
       v
Find instance class
       |
       v
Replace unsupported class
       |
       v
Save
       |
       v
terraform init
       |
       v
terraform plan
       |
       v
terraform apply
```

**Key lesson:** Errors can occur during `apply` even when `plan` succeeds.

---

## 21. Run Terraform Apply

After the plan is successful:

```bash
terraform apply
```

Review the plan and confirm:

```text
yes
```

Terraform begins infrastructure creation, refreshing state and determining which resources exist and which remaining resources need to be created.

---

## 22. Understand Terraform State During Apply

Terraform maintains state to track managed infrastructure:

```text
Already created
       |
       v
Keep / manage

Not created
       |
       v
Create

Changed
       |
       v
Update
```

You do not need to recreate every resource manually every time.

---

## 23. Partial Deployment and Recovery

Suppose the project has approximately 40 resources. During the first apply:

```text
37 resources created
3 resources failed/pending
```

After fixing the problem, run:

```bash
terraform init
terraform plan
terraform apply
```

Terraform determines what is already present and focuses on creating the remaining required changes.

---

## 24. Verify the Application

After successful creation, retrieve the application's DNS endpoint:

```text
Terraform Apply Successful
          |
          v
Find DNS / Load Balancer Endpoint
          |
          v
Open URL
          |
          v
Verify Frontend
          |
          v
Test Application
```

---

## 25. Test the Complete Application Flow

Verify the actual application communication:

1. Open the frontend.
2. Interact with the application.
3. Submit data.
4. Store data in the database.
5. Retrieve data from the database.
6. Verify the response in the frontend.

```text
User
 |
 v
Frontend
 |
 v
Application Server
 |
 v
Database
 |
 v
Response
 |
 v
Frontend
```

---

## 26. Auto Scaling Verification

After deployment, verify the Auto Scaling Group:

```text
Minimum Capacity
        |
        v
Desired Capacity = 1
        |
        v
Maximum Capacity
```

If traffic increases, Auto Scaling launches additional EC2 instances according to configured rules using the Launch Template.

---

## 27. Understand Configuration Drift

Drift occurs when someone manually changes infrastructure outside Terraform:

```text
Terraform Code
     |
     v
Desired = 1 instance

AWS Console
     |
     v
Someone manually changes configuration
     |
     v
Actual infrastructure != Terraform configuration
```

> **Takeaway:** Terraform should remain the source of truth for Terraform-managed infrastructure. Manual console changes create differences between declared configuration and real infrastructure.

---

## 28. CloudWatch and Monitoring

CloudWatch is used to investigate logs and metrics when troubleshooting application or infrastructure behavior:

```text
Application Issue
       |
       v
Check Symptoms
       |
       v
Check Logs / Metrics
       |
       v
CloudWatch
       |
       v
Identify Root Cause
       |
       v
Fix
       |
       v
Verify
```

---

## 29. Performance / Deployment Speed Discussion

Terraform parallelism controls how many resources are processed concurrently:

```text
Normal
10 resources processed concurrently

Higher parallelism
20 resources processed concurrently
```

Increasing parallelism should be done carefully to avoid dependency issues, API throttling, or resource-limit problems.

---

## 30. Application vs Infrastructure Troubleshooting

### Infrastructure Problem
- EC2 not launching
- RDS unavailable
- Load Balancer not working
- Network connectivity failure
- Security Group problem

*(Investigated by DevOps / Infrastructure team)*

### Application Problem
- Application logic incorrect
- Database query incorrect
- Application returns incorrect data
- Frontend functionality broken

*(Investigated by Developers)*

---

## 31. Final Infrastructure Verification Checklist

### Networking
- [ ] VPC created
- [ ] Public subnet created
- [ ] Private subnet created
- [ ] Route tables created
- [ ] Internet Gateway configured
- [ ] NAT Gateway configured
- [ ] Elastic IP associated where required
- [ ] Security Groups configured

### Web Layer
- [ ] EC2 instance launched
- [ ] Launch Template created
- [ ] Auto Scaling Group created
- [ ] Load Balancer created
- [ ] Target resources registered correctly

### Database
- [ ] RDS instance created
- [ ] MySQL configured
- [ ] Database is reachable from the required application layer
- [ ] Database is not unnecessarily exposed publicly
- [ ] Sensitive database information is protected

### Application
- [ ] DNS endpoint available
- [ ] Frontend loads
- [ ] Application server responds
- [ ] Database operations work
- [ ] Application can insert data
- [ ] Application can retrieve data

---

## 32. Complete Project Execution Flow

```text
STEP 1  : Prepare AWS Account
STEP 2  : Install / Configure AWS CLI
STEP 3  : Install Terraform
STEP 4  : Install / Configure Git
STEP 5  : Clone Terraform Repository
STEP 6  : Understand Project Structure
STEP 7  : Check Provider + Variables
STEP 8  : terraform init
STEP 9  : terraform validate
STEP 10 : terraform graph
STEP 11 : terraform plan  --> (If Error: Read -> Fix -> init/validate/plan)
STEP 12 : terraform apply --> (If Error: Read -> Module -> Fix -> init/plan/apply)
STEP 13 : Verify AWS Resources
STEP 14 : Get DNS / Load Balancer Endpoint
STEP 15 : Test Application
STEP 16 : Verify Auto Scaling
STEP 17 : Check Logs / Monitoring
STEP 18 : terraform destroy
```

---

## 33. Destroy the Infrastructure

Once testing is complete, prevent unexpected cloud costs:

```bash
terraform destroy
```

Confirm the operation with `yes`.

Lifecycle summary:

```text
CREATE ➔ TEST ➔ TROUBLESHOOT ➔ VERIFY ➔ DESTROY
```

---

## 34. Real-Time Troubleshooting Method

When Terraform encounters an error:

1. **Step 1: Read the complete error** — Understand the exact complaint before searching online.
2. **Step 2: Identify the resource** — Note Resource Type, Resource Name, Module, File, Line Number.
3. **Step 3: Understand the error** — What exactly is Terraform complaining about?
4. **Step 4: Locate the code** — Open the relevant module and line.
5. **Step 5: Check AWS** — Verify if the referenced resource, AMI, instance class, or dependency exists.
6. **Step 6: Fix the Terraform code** — Make the smallest required change.
7. **Step 7: Re-run Terraform** — `terraform init` → `terraform plan` → `terraform apply`.
8. **Step 8: Verify** — Confirm the resource is created successfully.

---

## 35. Key Troubleshooting Examples From This Project

| Problem | Where to Investigate | Resolution Discussed |
| :--- | :--- | :--- |
| **AMI unavailable** | Auto Scaling module | Find an available compatible AMI and update the configuration |
| **Unsupported DB instance class** | Database module | Change the outdated `db.t2...` class to `db.t3.micro` |
| **AWS credential issue** | AWS CLI / credentials | Verify AWS authentication and configuration |
| **Terraform plan failure** | Terraform code | Read error, locate file/line, fix configuration |
| **Apply failure** | Specific AWS resource | Read AWS/Terraform error and correct resource configuration |
| **Application issue** | Application layer / logs | Determine whether it is infrastructure or application-related |
| **Configuration drift** | AWS console vs Terraform | Compare actual infrastructure with Terraform configuration |
| **Slow provisioning** | Terraform execution | Review dependencies and parallelism |

---

## 36. Project in One Page

- **Project Name:** Three-Tier AWS Architecture Deployment Using Terraform
- **Objective:** Deploy a three-tier application infrastructure on AWS using Terraform and approximately 40 AWS resources.

### Architecture Flow

```text
                 INTERNET
                    |
                    v
              Load Balancer
                    |
                    v
          Web / Frontend Layer
                    |
                    v
         Application / Logic Layer
                    |
                    v
             Private Database
                 MySQL/RDS
```

### Terraform Structure

```text
                  main.tf
                     |
          +----------+----------+
          |          |          |
          v          v          v
    Networking     Web/ASG    Database
      Module       Module      Module
          |          |          |
          v          v          v
       VPC/Subnets  EC2/ALB    RDS/MySQL
       Routes/SG    Launch
       Gateway      Template
```

### Deployment Commands

```bash
git clone <repository-url>
cd <project-directory>
terraform init
terraform validate
terraform graph
terraform plan
terraform apply
```

### Cleanup

```bash
terraform destroy
```

---

## 37. What You Should Be Able to Explain After Completing This Project

1. What a three-tier architecture is.
2. Difference between presentation, application, and database layers.
3. Why Terraform is used instead of manually creating AWS infrastructure.
4. What Infrastructure as Code means.
5. Why Terraform modules are used.
6. Purpose of `main.tf`.
7. Purpose of `provider.tf`.
8. Purpose of `variables.tf`.
9. Purpose of `terraform.tfvars`.
10. Purpose of `output.tf`.
11. Purpose of Terraform state.
12. Why `terraform init` is required.
13. Difference between `terraform validate`, `terraform plan`, and `terraform apply`.
14. How Terraform handles dependencies.
15. Purpose of the networking module.
16. Purpose of VPC and subnets.
17. Purpose of route tables.
18. Purpose of Internet Gateway.
19. Purpose of NAT Gateway.
20. Purpose of Security Groups.
21. Purpose of EC2.
22. Purpose of Load Balancer.
23. Purpose of Auto Scaling Group.
24. Why a Launch Template is required for Auto Scaling.
25. Why an AMI is required.
26. How to troubleshoot an unavailable AMI.
27. How to troubleshoot an unsupported database instance class.
28. Why RDS is placed in a private network.
29. How application traffic flows through the architecture.
30. What configuration drift means.
31. How to verify the deployed application.
32. How CloudWatch can assist with troubleshooting.
33. Why Terraform may continue creating remaining resources after some resources already exist.
34. Why `terraform plan` can succeed while `terraform apply` can still encounter an AWS-side error.
35. Why infrastructure should be destroyed after completing a temporary lab.

---

## 38. Final Project Lifecycle & Key Takeaways

```text
                    PROJECT REQUIREMENT
                           |
                           v
                  DESIGN 3-TIER ARCHITECTURE
                           |
                           v
                    WRITE TERRAFORM CODE
                           |
                           v
                  ORGANIZE INTO MODULES
                           |
                           v
                    CONFIGURE AWS CLI
                           |
                           v
                       GIT CLONE
                           |
                           v
                    TERRAFORM INIT
                           |
                           v
                  TERRAFORM VALIDATE
                           |
                           v
                    TERRAFORM GRAPH
                           |
                           v
                    TERRAFORM PLAN
                           |
                    +------+------+
                    |             |
                  ERROR          OK
                    |             |
                    v             v
               TROUBLESHOOT   TERRAFORM APPLY
                    |             |
                    +------<------+ 
                                  |
                                  v
                       AWS INFRASTRUCTURE
                                  |
                                  v
                         APPLICATION TEST
                                  |
                                  v
                       MONITOR / TROUBLESHOOT
                                  |
                                  v
                        PROJECT VERIFICATION
                                  |
                                  v
                         TERRAFORM DESTROY
                                  |
                                  v
                           CLEAN AWS ACCOUNT
```

### Final Takeaway

This project demonstrates the complete practical workflow of a DevOps engineer working with Terraform:

> **Design → Code → Modularize → Initialize → Validate → Plan → Troubleshoot → Apply → Verify → Monitor → Destroy.**

Knowing Terraform syntax alone is not enough. A real DevOps engineer must be able to **read an error, locate the responsible resource/module, understand the AWS dependency, make the correct change, rerun Terraform, and verify the result**.

---

## 39. Top 10 Technical Interview Questions & Answers

### Q1: What is a Three-Tier Architecture, and how does traffic flow across tiers in this AWS deployment?
**Answer:** A Three-Tier architecture divides an application into three distinct physical and logical layers:
1. **Presentation Layer (Web):** Public-facing Application Load Balancer (ALB) and Web Server instances that receive HTTP/S user requests.
2. **Application Layer (Logic):** Private EC2 instances running business logic and processing application requests.
3. **Database Layer (Data):** An isolated database (such as Amazon RDS MySQL) storing persistence data in private database subnets.
- **Traffic Flow:** User ➔ Internet Gateway (IGW) ➔ Application Load Balancer (Public Subnets) ➔ Web/App EC2 instances (Private Subnets) ➔ Amazon RDS MySQL (Private DB Subnets on Port 3306). Only the presentation tier receives public ingress; all internal tiers communicate via private IPs.

---

### Q2: Why is it recommended to organize Terraform code into modules rather than writing all resources in a single `main.tf`?
**Answer:** A single monolithic `main.tf` file containing ~40 resources creates high complexity, makes code difficult to maintain, and increases the risk of unintended blast radius during updates. Organizing code into dedicated modules (`networking`, `web`, `autoscaling`, `database`):
- **Encapsulates Responsibilities:** Each module handles one clear architectural concern.
- **Enables Reusability:** Modules can be referenced across multiple environments (Dev, QA, Staging, Prod) by passing different input variables.
- **Improves Team Collaboration:** Different engineers can work on separate modules without creating git merge conflicts or state locking bottlenecks.

---

### Q3: Explain the sequential lifecycle of Terraform commands: `init`, `validate`, `graph`, `plan`, `apply`, and `destroy`.
**Answer:**
- `terraform init`: Initializes the working directory, downloads required provider plugins (AWS), and prepares child modules and backends.
- `terraform validate`: Verifies syntax, variable types, and attribute correctness against provider schemas without contacting AWS APIs.
- `terraform graph`: Generates a visual Directed Acyclic Graph (DAG) representing resource dependency relationships.
- `terraform plan`: Performs a read-only dry-run, comparing the desired code state with the current state file and remote AWS infrastructure to calculate the execution delta.
- `terraform apply`: Executes the proposed changes against AWS APIs to provision/modify resources and updates `terraform.tfstate`.
- `terraform destroy`: Safely removes all managed cloud resources tracked in the state file in reverse dependency order.

---

### Q4: What is the role of `terraform.tfstate`, and why is managing state safely critical?
**Answer:** `terraform.tfstate` is the single source of truth mapping Terraform configuration resources to real-world AWS resource IDs, metadata, and attributes. 
- It allows Terraform to determine which resources already exist, which need modifications, and which need deletion.
- Without a state file, Terraform cannot manage existing infrastructure and would attempt to recreate resources, causing duplicate name/CIDR collisions.
- In production, state files must be stored in remote backends (such as Amazon S3 with DynamoDB locking) to ensure team concurrency and prevent corruption.

---

### Q5: Why do we declare database password outputs as `sensitive = true` in Terraform?
**Answer:** Marking an output block with `sensitive = true` instructs Terraform to redact that value from CLI terminal stdout, execution logs, and CI/CD console outputs (displaying `<sensitive>` instead of plaintext strings). This prevents accidental credential exposure in build logs, developer screen shares, and continuous integration reports.

---

### Q6: What is the difference between an Application Load Balancer (ALB) and an Auto Scaling Group (ASG), and how do they interact?
**Answer:**
- **Application Load Balancer (ALB):** Operates at Layer 7 (HTTP/HTTPS), intercepts incoming external client traffic, and distributes it across healthy target instances across multiple Availability Zones.
- **Auto Scaling Group (ASG):** Manages EC2 instance lifecycle, automatically scaling instance count up or down based on workload, CPU metrics, or health check status.
- **Interaction via Target Groups:** When the ASG provisions new EC2 instances using a Launch Template, it automatically registers their private IPs with the ALB Target Group. The ALB performs periodic health checks and routes traffic only to healthy instances.

---

### Q7: Why is Amazon RDS placed in private subnets, and how is network security enforced?
**Answer:**
- Placing Amazon RDS in private subnets with `publicly_accessible = false` ensures the database has no public IP addresses and cannot be reached from the public internet, mitigating DDoS and brute-force vulnerabilities.
- **Security Group Chaining:** The RDS Security Group allows inbound MySQL traffic (Port 3306) **only** from the Web/App Security Group ID (not from CIDR `0.0.0.0/0`). This enforces strict least-privilege access where only authorized application servers can query the database.

---

### Q8: What is the function of a NAT Gateway in this architecture?
**Answer:** Private EC2 instances (in private subnets) have no public IPv4 addresses and no direct route to the Internet Gateway. However, they need outbound internet connectivity to download operating system security patches, application dependencies, or reach external APIs. 
- The **NAT Gateway** resides in a public subnet with an Elastic IP (EIP).
- Private route tables route outbound destination `0.0.0.0/0` to the NAT Gateway, which translates private IPs to its Elastic IP and forwards packets to the Internet Gateway while blocking unsolicited inbound connections from the internet.

---

### Q9: How does Terraform determine the order in which AWS resources are created?
**Answer:** Terraform builds a **Directed Acyclic Graph (DAG)** of all resources:
- It analyzes implicit dependencies (e.g., passing `module.networking.vpc_id` into `module.web` means VPC must be created before the ALB) and explicit dependencies (`depends_on`).
- Independent resources (like independent subnets in different AZs) are created concurrently, while dependent resources are provisioned strictly after their prerequisites are available.

---

### Q10: How does Terraform parallelism work, and what are the trade-offs of increasing it?
**Answer:** By default, Terraform processes up to 10 concurrent resource operations (`-parallelism=10`).
- Setting `-parallelism=20` increases concurrency, accelerating deployment of large architectures (~40 resources).
- **Trade-offs:** Overly high parallelism can lead to AWS API rate limiting / throttling errors (`RequestLimitExceeded`), or race conditions if inter-resource dependencies are not explicitly mapped.

---

## 40. Top 10 Scenario-Based Interview Questions & Solutions

### Scenario 1: Obsolete / Deprecated AMI ID during `terraform plan` / `apply`
**Scenario:** You execute `terraform plan`, and Terraform throws `Error: InvalidAMIID.NotFound: The AMI ID 'ami-xxxxxx' does not exist in region us-west-2` because an older Amazon Linux AMI was deprecated and removed.
**Solution:**
1. Avoid hardcoding static AMI IDs in `main.tf` or `variables.tf`.
2. Locate the Auto Scaling module and query the AWS CLI or SSM Parameter Store for the active Amazon Linux 2023 AMI.
3. Use a dynamic Terraform `data "aws_ami"` block with `most_recent = true` and official owner filters (`amazon`).
4. Re-run `terraform init` and `terraform plan` to confirm resolution.

---

### Scenario 2: Partial Apply Recovery (37 of 40 Resources Created, Database Creation Fails)
**Scenario:** On initial deployment, 37 resources provision successfully, but the RDS creation fails with `Cannot find an engine configuration for mysql 8.4 with instance class db.t2.micro`. How do you recover without deleting existing resources?
**Solution:**
1. Do not manually touch or delete resources in the AWS Console.
2. Recognize that Terraform recorded the 37 created resources in `terraform.tfstate`.
3. Open `modules/database/main.tf` and update `instance_class` from `db.t2.micro` to `db.t3.micro` (supported for MySQL 8.4).
4. Save the file and execute `terraform apply`. Terraform refreshes state, recognizes that the 37 resources already exist, and provisions only the remaining database resources.

---

### Scenario 3: Configuration Drift Caused by Manual AWS Console Modifications
**Scenario:** An on-call engineer manually changes the Auto Scaling Group desired capacity from 1 to 5 via the AWS Console during an incident. How do you detect and remediate this drift using Terraform?
**Solution:**
1. **Detect:** Run `terraform plan`. Terraform refreshes state from AWS APIs, compares it with your code, and flags that `desired_capacity` changed remotely from 1 to 5.
2. **Remediate to Code:** Run `terraform apply` to overwrite the console modification and return desired capacity to 1.
3. **Adopt Drift (if legitimate):** Update `variables.tf` to `desired_capacity = 5` in code so your Terraform configuration reflects the new requirement.

---

### Scenario 4: Application Returns `502 Bad Gateway` via the ALB DNS Endpoint
**Scenario:** You open the ALB URL in your browser, but receive an `HTTP 502 Bad Gateway` error. How do you troubleshoot?
**Solution:**
1. **Check Target Group Health:** Open AWS Console / CLI and inspect target health. If status is `Unhealthy`, the ALB cannot establish an HTTP connection with the backend EC2 instances on port 80.
2. **Inspect Security Groups:** Verify that the Web/App Security Group allows inbound Port 80 from the ALB Security Group.
3. **Inspect EC2 User Data / Application Service:** Check `/var/log/cloud-init-output.log` on the EC2 instances to confirm that web server service (e.g., Apache/Nginx/Node) started cleanly and is listening on port 80.

---

### Scenario 5: EC2 Instances in Private Subnets Fail to Download Packages
**Scenario:** EC2 instances launched by the Auto Scaling Group fail during user-data execution because `yum update` or `apt-get` times out trying to connect to repository mirrors.
**Solution:**
1. Inspect the **Route Table** associated with the Private Application Subnets.
2. Ensure route `0.0.0.0/0` points to the **NAT Gateway ID** (not the Internet Gateway).
3. Verify that the **NAT Gateway** is situated in a **Public Subnet** with an associated Elastic IP (EIP) and that the Public Subnet's route table points `0.0.0.0/0` to the **Internet Gateway (IGW)**.

---

### Scenario 6: Multi-Region Deployment Without Code Duplication
**Scenario:** A client requests deploying this identical Three-Tier stack in both `us-west-2` and `us-east-1`. How do you implement this cleanly in Terraform?
**Solution:**
1. Keep the root modules and child modules generic with parameterized variables (`var.aws_region`, `var.vpc_cidr`, `var.environment`).
2. Create separate environment tfvars files (e.g., `us-west-2.tfvars` and `us-east-1.tfvars`) or use Terraform Workspaces / directory separation:
   ```bash
   terraform apply -var-file="us-west-2.tfvars"
   terraform apply -var-file="us-east-1.tfvars"
   ```
3. The underlying module definitions remain 100% unchanged.

---

### Scenario 7: State Locking & Multi-Engineer Concurrency
**Scenario:** Two DevOps engineers run `terraform apply` simultaneously against the same infrastructure, risking corrupting the state file. How do you prevent this?
**Solution:**
1. Configure a remote backend in `backend.tf` using an **Amazon S3 bucket** for state storage and an **Amazon DynamoDB table** for state locking (`LockID` partition key).
2. When Engineer A starts `terraform apply`, Terraform acquires a lock in DynamoDB.
3. If Engineer B executes `terraform apply`, Terraform outputs `Error: Error acquiring the state lock` and safely aborts until Engineer A's apply completes and releases the lock.

---

### Scenario 8: Secure Database Administration Without Public Exposure
**Scenario:** A database administrator needs to run a schema migration on the private RDS MySQL instance, but policy strictly prohibits making RDS publicly accessible.
**Solution:**
1. Deploy a secure **Bastion Host / Jump Server** in the Public Subnet, or utilize **AWS Systems Manager (SSM) Session Manager** with an EC2 instance profile in the private subnet.
2. Establish an SSH tunnel / port forward via SSM to connect to RDS MySQL on private Port 3306.
3. The database remains completely private with zero public IP addresses or open public ports.

---

### Scenario 9: Newly Launched ASG Instances Marked Unhealthy by ALB
**Scenario:** Under load, Auto Scaling spins up 2 new instances, but the ALB marks them `Unhealthy` after 30 seconds and terminates them prematurely in a continuous loop.
**Solution:**
1. **Health Check Grace Period:** Increase the Auto Scaling Group `health_check_grace_period` (e.g., from 30s to 300s) to give user-data startup scripts enough time to install dependencies and boot the application.
2. **Health Check Path:** Verify the ALB Target Group health check path (e.g., `/` or `/healthz`) returns HTTP 200 OK once the app is running.

---

### Scenario 10: Infrastructure Teardown & Deletion Protection
**Scenario:** You execute `terraform destroy` to tear down the lab, but it errors out with `Cannot delete DB Instance: DeletionProtection is enabled` or dependency violation on a security group.
**Solution:**
1. If RDS has `deletion_protection = true`, update `deletion_protection = false` in `modules/database/main.tf` and run `terraform apply`.
2. Re-run `terraform destroy`.
3. Terraform calculates the reverse dependency graph (destroying ASG and EC2 instances first, freeing up ENIs, then removing Security Groups, Subnets, NAT Gateways, and VPC cleanly without orphaned charges).

