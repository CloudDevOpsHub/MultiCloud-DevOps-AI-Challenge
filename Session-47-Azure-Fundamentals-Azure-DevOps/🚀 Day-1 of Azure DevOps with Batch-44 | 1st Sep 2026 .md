# 🚀 Azure Fundamentals & Azure DevOps | Master Session Summary

[![Cloud: Microsoft Azure](https://img.shields.io/badge/Cloud-Microsoft_Azure-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white)](README.md)
[![Platform: Azure DevOps](https://img.shields.io/badge/Platform-Azure_DevOps-0078D7?style=for-the-badge&logo=azuredevops&logoColor=white)](README.md)
[![Module: Multi--Cloud & CI/CD](https://img.shields.io/badge/Module-Multi--Cloud_%26_CI/CD-232F3E?style=for-the-badge)](README.md)
[![Interview Q&A: 20+ Included](https://img.shields.io/badge/Interview%20Q%26A-20%2B%20Included-success?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## 📋 Table of Contents
1. [Session Overview](#1-session-overview)
2. [Why Multi-Cloud Knowledge Matters](#2-why-multi-cloud-knowledge-matters)
3. [Cloud Service Models (IaaS vs. PaaS vs. SaaS)](#3-cloud-service-models-iaas-vs-paas-vs-saas)
4. [Azure Governance Hierarchy & Enterprise Structure](#4-azure-governance-hierarchy--enterprise-structure)
5. [Azure Portal vs. Azure DevOps](#5-azure-portal-vs-azure-devops)
6. [Azure DevOps Core Suite Deep Dive](#6-azure-devops-core-suite-deep-dive)
   - [Azure Boards (Agile & Project Management)](#61-azure-boards)
   - [Azure Repos (Git Source Control & PR Workflow)](#62-azure-repos)
   - [Azure Pipelines (YAML-based CI/CD Automation)](#63-azure-pipelines)
   - [Azure Artifacts & Azure Test Plans](#64-azure-artifacts--test-plans)
   - [Azure App Service (PaaS Hosting Target)](#65-azure-app-service)
7. [Hands-on Account & Project Setup Guide](#7-hands-on-account--project-setup-guide)
8. [Cross-Cloud Mapping Reference (AWS vs. Azure vs. GCP)](#8-cross-cloud-mapping-reference-aws-vs-azure-vs-gcp)
9. [Top 10 Technical Interview Questions & Answers](#9-top-10-technical-interview-questions--answers)
10. [Top 10 Scenario-Based Interview Questions & Solutions](#10-top-10-scenario-based-interview-questions--solutions)
11. [Key Takeaways & Summary](#11-key-takeaways--summary)

---

## 1. Session Overview

This session establishes foundational proficiency in **Microsoft Azure** and **Azure DevOps** for engineers transitioning from single-cloud (AWS) to enterprise **multi-cloud** architectures. 

Rather than learning Azure as an isolated platform from scratch, the curriculum focuses on **conceptual mapping**: linking familiar AWS services (EC2, S3, IAM, CloudWatch, EKS) to their direct Azure counterparts (Virtual Machines, Blob Storage, Entra ID, Azure Monitor, AKS) and mastering the end-to-end software delivery lifecycle with Azure DevOps.

---

## 2. Why Multi-Cloud Knowledge Matters

Enterprise IT environments rarely run entirely on a single cloud vendor. Modern organizations adopt multi-cloud strategies to:
* Avoid single-vendor lock-in and negotiate better pricing.
* Leverage ecosystem synergy (e.g., integrating Microsoft 365 / Active Directory estates directly with Azure).
* Ensure regulatory compliance, data residency, and high-availability disaster recovery.
* Accommodate client preferences in consulting and managed-services environments.

> [!TIP]
> **The Core Learning Principle:** Master the underlying cloud computing concept (compute, object storage, identity, pub/sub messaging, container orchestration). Cloud providers change terminology and UI, but core system architecture patterns remain consistent.

---

## 3. Cloud Service Models (IaaS vs. PaaS vs. SaaS)

Understanding the cloud shared responsibility model is essential for architectural decisions and technical interviews.

| Model | Full Name | Provider Manages | Customer Manages | Primary Azure Examples | AWS Equivalent |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **IaaS** | Infrastructure as a Service | Physical hardware, datacenter facilities, virtualization layer | OS installation, patches, networking rules, runtime, application code, data | Azure Virtual Machines (VMs), Azure Virtual Network, Azure Disks | AWS EC2, EBS, VPC |
| **PaaS** | Platform as a Service | Hardware, OS, runtime environment, database engine patches, auto-scaling | Application source code, database schemas, access configuration | Azure App Service (Web Apps), Azure SQL Database, Azure Functions | AWS Elastic Beanstalk, Amazon RDS, AWS Lambda |
| **SaaS** | Software as a Service | Entire stack (hardware, OS, application, database, maintenance) | User accounts, organizational settings, and tenant data | Microsoft 365, Power BI, GitHub Enterprise | WorkDocs, Salesforce |

---

## 4. Azure Governance Hierarchy & Enterprise Structure

Azure organizes resources within a strict 5-tier enterprise hierarchy to enforce access control, billing boundaries, and compliance policies:

1. **Azure Tenant (Microsoft Entra ID):** The dedicated identity and access boundary representing the entire organization.
2. **Management Groups:** Containers above subscriptions used to apply governance conditions, compliance frameworks, and role-based policies across multiple subscriptions simultaneously.
3. **Subscriptions:** The fundamental billing and quota container. All resource costs roll up to a specific subscription.
4. **Resource Groups:** Logical containers that group related Azure resources sharing the same lifecycle, deployment cadence, and permissions.
5. **Resources:** The individual service instances provisioned inside a resource group (VMs, Storage Accounts, Databases, VNet subnets).

### Azure Landing Zones
An **Azure Landing Zone** is an enterprise multi-subscription cloud architecture framework following Microsoft Cloud Adoption Framework (CAF) best practices. It pre-configures networking, security policies, identity management, logging, and billing governance before application workloads are deployed.

### Cloud Billing Hygiene & Cost Management
* **Single Subscription Rule for Labs:** Use one active free-tier/pay-as-you-go subscription for hands-on exercises to avoid split bills.
* **Immediate Teardown:** Stop or delete compute instances, public IPs, and managed database instances immediately upon completing a lab.
* **Budget Alerts:** Configure Azure Cost Management budget alerts at 50%, 75%, and 90% thresholds.

---

## 5. Azure Portal vs. Azure DevOps

A frequent source of confusion for newcomers is the distinction between Azure's cloud infrastructure portal and its DevOps development suite:

| Feature | Azure Portal (`portal.azure.com`) | Azure DevOps (`dev.azure.com`) |
| :--- | :--- | :--- |
| **Primary Domain** | Cloud Infrastructure & Resource Management | Software Delivery & DevOps Lifecycle Management |
| **Target Users** | Cloud Architects, SysAdmins, Infrastructure Engineers | Developers, DevOps Engineers, QA Engineers, Scrum Masters |
| **Core Functions** | Provisioning and managing VMs, Storage, Databases, VNets, and IAM | Managing source code, tracking Agile work items, running CI/CD pipelines, package hosting |
| **Underlying Unit** | Resource Groups, Subscriptions, Cloud Services | Organizations, Projects, Repositories, Pipelines, Boards |

---

## 6. Azure DevOps Core Suite Deep Dive

Azure DevOps is an integrated, modular platform comprising five core services:

```
Azure DevOps Suite
├── Azure Boards     (Agile project management & work tracking)
├── Azure Repos      (Git source code version control)
├── Azure Pipelines  (Multi-platform automated CI/CD)
├── Azure Artifacts  (Package feeds: NuGet, npm, Maven, Python)
└── Azure Test Plans (Manual & automated test case management)
```

---

### 6.1 Azure Boards

Azure Boards facilitates Agile, Scrum, and Kanban project management, allowing engineering teams to plan, track, and discuss work across the complete development lifecycle.

#### Work Item Hierarchy
* **Epic:** High-level strategic initiative or major business requirement spanning multiple sprints (e.g., *AI Mock Interview Platform*).
* **Feature:** Deliverable capability within an Epic (e.g., *Ollama Local LLM Integration Module*).
* **User Story / Product Backlog Item:** Specific feature requirement from an end-user perspective (e.g., *As a student, I want to submit audio answers for evaluation*).
* **Task:** Granular engineering sub-activity required to implement a story (e.g., *Configure Azure App Service environment variables*).
* **Bug:** A defect or unexpected behavior tracked against a feature or story.

#### Backlog & Sprint Planning
* **Product Backlog:** A centralized, prioritized queue of all pending requirements, enhancements, and bugs not yet committed to an active iteration.
* **Sprint (Iteration):** A fixed time-box (typically 2 weeks) during which the engineering team commits to delivering a specific increment of working software.
* **Capacity Planning:** Allocating tasks based on team member availability, velocity, and skill set rather than overcommitting the backlog.

---

### 6.2 Azure Repos

Azure Repos provides private Git repositories with enterprise code collaboration tools.

#### Standard Git Workflow
1. **Branch Creation:** Developers create dedicated feature branches (`feature/user-auth`) branched off `main`.
2. **Local Commits:** Changes are staged and committed with descriptive, atomic commit messages.
3. **Remote Push:** Branch is pushed to Azure Repos.
4. **Pull Request (PR):** A PR is opened to propose merging changes into `main`.
5. **Code Review & Branch Policies:** Reviewers comment, request changes, and validate automated pipeline checks before approving.
6. **Merge & Tagging:** Changes are merged into `main`, and semantic release tags (e.g., `v1.0.0`) are applied for milestone releases.

> [!IMPORTANT]
> **Branch Protection Best Practice:** Never permit direct pushes to the `main` branch. Enforce Branch Policies requiring minimum reviewer approvals (1-2 approvals) and mandatory successful CI pipeline builds before merge.

---

### 6.3 Azure Pipelines

Azure Pipelines is a cloud-hosted continuous integration and continuous delivery (CI/CD) engine supporting any language, platform, or cloud provider.

#### Jenkins vs. Azure Pipelines Comparison

| Dimension | Jenkins | Azure Pipelines |
| :--- | :--- | :--- |
| **Hosting Model** | Self-managed server / Master-Agent cluster | Fully managed Microsoft cloud SaaS or self-hosted agents |
| **Pipeline Definition** | `Jenkinsfile` using Groovy syntax (Scripted / Declarative) | `azure-pipelines.yml` using declarative YAML syntax |
| **Infrastructure Overhead** | High (Requires OS patching, plugin maintenance, agent scaling) | Low (Zero infrastructure management for Microsoft-hosted agents) |
| **Native Integration** | Requires third-party plugins for Git and cloud providers | Out-of-the-box native integration with Azure Repos, Boards, and App Services |

#### Standard CI/CD Pipeline Flow
1. **Source Trigger:** Developer pushes code to Azure Repos or creates a PR.
2. **Build Stage:** Pipeline pulls dependencies (npm, Maven, pip), compiles code, and produces deployment artifacts.
3. **Test Stage:** Automated unit tests, integration tests, and static code quality scans execute.
4. **Package Stage:** Code is packaged into containers or deployment zip bundles and pushed to an artifact feed.
5. **Deploy Stage:** Automated release deploys the artifact to target environments (e.g., Azure App Service, AKS).

---

### 6.4 Azure Artifacts & Test Plans

* **Azure Artifacts:** Serves as a centralized, secure package repository for hosting, publishing, and sharing dependencies (npm, Maven, Gradle, NuGet, Python wheels). Enables private package sharing across development teams with upstream proxying.
* **Azure Test Plans:** Provides browser-based manual test execution, exploratory testing toolkits, and end-to-end traceability linking test cases directly to user stories and bugs in Azure Boards.

---

### 6.5 Azure App Service

**Azure App Service** is a fully managed Platform as a Service (PaaS) offering for hosting web applications, REST APIs, and mobile backends without managing underlying servers, virtual machines, or web server configurations (IIS/NGINX).

* Supports Node.js, Python, Java, .NET, PHP, and custom Docker containers.
* Features built-in auto-scaling, SSL certificate provisioning, deployment slots (staging/production blue-green swap), and seamless integration with Azure Pipelines.

---

## 7. Hands-on Account & Project Setup Guide

### Step 1: Azure Free Tier Account Setup
1. Navigate to `https://azure.microsoft.com/free` and sign in with a Microsoft account.
2. Provide verification details (valid phone number, credit/debit card for identity validation).
3. Confirm activation of the free subscription (includes 30 days of initial credits + 12 months of popular free services + 55+ always-free services).
4. Access the primary management interface at `https://portal.azure.com`.

### Step 2: Azure DevOps Organization & Project Initialization
1. Navigate to `https://dev.azure.com` and sign in.
2. Create an **Organization** (acts as the top-level container for your engineering teams).
3. Create a new **Project** (e.g., `DevOps-App-Deployment` with visibility set to *Private* and Version Control set to *Git*).
4. Explore the left-hand navigation pane to verify access to **Boards, Repos, Pipelines, Test Plans, and Artifacts**.

---

## 8. Cross-Cloud Mapping Reference (AWS vs. Azure vs. GCP)

| Category | AWS Service | Azure Service | Google Cloud (GCP) Service | Core Architectural Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **Virtual Machines (IaaS)** | EC2 | Azure Virtual Machines | Compute Engine | On-demand resizable virtual computing instances |
| **Object Storage** | Amazon S3 | Azure Blob Storage | Google Cloud Storage | Scalable storage for unstructured files, media, and backups |
| **Serverless Compute** | AWS Lambda | Azure Functions | Cloud Functions | Event-driven, serverless compute execution |
| **Managed Kubernetes** | Amazon EKS | Azure Kubernetes Service (AKS) | Google Kubernetes Engine (GKE) | Managed container orchestration clusters |
| **Managed Relational DB** | Amazon RDS | Azure Database for MySQL/PostgreSQL | Cloud SQL | Automated patching, backups, and scaling for relational DBs |
| **Monitoring & Telemetry** | Amazon CloudWatch | Azure Monitor / Application Insights | Cloud Monitoring | Metrics collection, log aggregation, and system alerting |
| **Message Queues** | Amazon SQS | Azure Service Bus (Queues) | Cloud Pub/Sub | Asynchronous decoupling and message buffering |
| **Publish/Subscribe Messaging** | Amazon SNS | Azure Service Bus (Topics) / Event Grid | Cloud Pub/Sub | Event routing and broadcast push notifications |
| **Identity & Access** | AWS IAM | Microsoft Entra ID (formerly Azure AD) | Google Cloud IAM | Role-based access control, authentication, and policy enforcement |
| **Managed Web Hosting (PaaS)**| AWS Elastic Beanstalk | Azure App Service | App Engine | Platform for deploying web applications without managing OS |
| **Git Repositories** | AWS CodeCommit | Azure Repos | Cloud Source Repositories | Cloud-hosted private Git version control |
| **CI/CD Automation** | AWS CodePipeline | Azure Pipelines | Cloud Build | Automated build, test, and release deployment pipelines |

---

## 9. Top 10 Technical Interview Questions & Answers

### Q1: What is Microsoft Azure, and how does it benefit modern enterprises?
**Answer:** Microsoft Azure is a hyperscale public cloud computing platform providing IaaS, PaaS, and SaaS solutions spanning compute, storage, databases, AI, networking, and DevOps. It allows organizations to build, deploy, scale, and manage global workloads on Microsoft's physical infrastructure, eliminating on-premises capital expenses (CapEx) in favor of operational pay-as-you-go billing (OpEx).

### Q2: What is the fundamental difference between Azure Portal and Azure DevOps?
**Answer:** **Azure Portal** (`portal.azure.com`) is the cloud management console used to provision, configure, monitor, and manage Azure cloud infrastructure resources (such as VMs, Blob Storage, Virtual Networks, and Databases). **Azure DevOps** (`dev.azure.com`) is an application lifecycle management platform used to track work (Boards), store source code (Repos), execute CI/CD workflows (Pipelines), manage test suites (Test Plans), and host private packages (Artifacts).

### Q3: What are the core services that make up Azure DevOps?
**Answer:** Azure DevOps consists of five integrated services:
1. **Azure Boards:** Agile planning and work tracking (Epics, Stories, Sprints).
2. **Azure Repos:** Private Git version control hosting with Pull Request workflows.
3. **Azure Pipelines:** Multi-platform CI/CD engine defined in declarative YAML.
4. **Azure Artifacts:** Package management feeds for npm, NuGet, Maven, and Python.
5. **Azure Test Plans:** Manual and exploratory test case management.

### Q4: How does Azure Blob Storage compare with Amazon S3?
**Answer:** Both are highly scalable, secure, object-storage services engineered to store massive amounts of unstructured data (such as application logs, media files, disk images, and backups). Both offer granular access policies, lifecycle management rules for tiering data (Hot, Cool, Cold, Archive), and server-side encryption. S3 organizes data in buckets; Blob Storage organizes data in Storage Accounts containing Containers.

### Q5: What is an Azure Resource Group, and why is it important?
**Answer:** An Azure Resource Group is a logical container that holds related Azure resources sharing a common lifecycle. It enables administrators to deploy, update, monitor, apply RBAC permissions, and delete all associated resources (VMs, DBs, VNets) as a single operational unit.

### Q6: What is Azure App Service, and what cloud category does it belong to?
**Answer:** Azure App Service is a **Platform as a Service (PaaS)** offering designed for quickly building, hosting, and auto-scaling enterprise web applications, RESTful APIs, and mobile backends. Microsoft manages OS patching, infrastructure provisioning, and web server configuration, allowing developers to focus solely on their code.

### Q7: What is Microsoft Entra ID (formerly Azure Active Directory)?
**Answer:** Microsoft Entra ID is Microsoft's cloud-based identity, authentication, and access management service. It manages user identities, single sign-on (SSO), Multi-Factor Authentication (MFA), and conditional access policies for Azure resources, Microsoft 365, and third-party SaaS applications.

### Q8: How does Azure Pipelines differ from traditional Jenkins?
**Answer:** Jenkins is an open-source, self-hosted CI/CD automation server requiring dedicated infrastructure, manual updates, and plugin management, configured using Groovy scripts (`Jenkinsfile`). Azure Pipelines is a fully managed cloud SaaS service (with optional self-hosted runner support) offering native YAML pipeline definitions, built-in cloud integrations, and zero maintenance overhead for build agents.

### Q9: What is an Azure Landing Zone?
**Answer:** An Azure Landing Zone is an architectural blueprint following the Microsoft Cloud Adoption Framework (CAF). It delivers a pre-configured multi-subscription environment with baseline security, networking topologies, identity access, logging, and governance rules configured before application workloads are deployed.

### Q10: What is the purpose of Git Tags, and when are they used in a release pipeline?
**Answer:** Git tags are immutable reference points pointing to specific commits in Git history, predominantly used to mark semantic release versions (e.g., `v1.0.0`, `v2.1.0-release`). In CI/CD pipelines, pushing a Git tag often automatically triggers production deployment and artifact packaging stages.

---

## 10. Top 10 Scenario-Based Interview Questions & Solutions

### 📌 Scenario 1: Cross-Cloud Skill Migration
**Question:** Your team runs workloads on AWS (EC2, S3, RDS, CloudWatch), but a client requires the next release to run on Microsoft Azure. You have no prior Azure experience. How do you approach the migration?  
**Solution:** I would approach the migration by mapping existing AWS architecture components to their Azure equivalents (EC2 → Azure Virtual Machines, S3 → Azure Blob Storage, RDS → Azure Database for MySQL/PostgreSQL, CloudWatch → Azure Monitor). I would then analyze Azure-specific networking (VNets vs. VPCs), IAM permissions (Entra ID vs. AWS IAM), and deployment automation templates (ARM/Bicep/Terraform) to reproduce identical application behavior on Azure.

### 📌 Scenario 2: Main Branch Protection
**Question:** A junior developer attempts to push untested code directly to the `main` branch in Azure Repos. How do you prevent this and enforce code quality?  
**Solution:** I would enable **Branch Policies** on the `main` branch in Azure Repos. The policy would:
1. Block direct commits and enforce changes through **Pull Requests (PRs)**.
2. Require a minimum of 1–2 senior peer review approvals before merge.
3. Require successful completion of the automated build and test pipeline (Build Validation).
4. Require all discussion comments to be resolved.

### 📌 Scenario 3: Automated CI Triggering
**Question:** You want every code push or PR creation in Azure Repos to automatically run unit tests and report build status back to developers. How do you set this up?  
**Solution:** I would define an `azure-pipelines.yml` file in the root of the repository configuring automated triggers (`trigger: - main` and `pr: - main`). The pipeline will run on a Microsoft-hosted build agent, fetch dependencies, run automated unit test commands, and report pass/fail status directly to the Azure Repos PR interface.

### 📌 Scenario 4: Managing Sprint Capacity in Azure Boards
**Question:** Your team has 80 backlog items, but the upcoming two-week sprint can only accommodate 15 tasks based on team velocity. How do you manage this in Azure Boards?  
**Solution:** I would conduct a Sprint Planning session using Azure Boards. We would prioritize items in the Product Backlog based on business value and dependencies, estimate story points/hours for each item, check team member availability and capacity, and move only the top-priority 15 user stories into the active Sprint Iteration backlog, leaving remaining items in the product backlog for future sprints.

### 📌 Scenario 5: Unexpected Cloud Cost Spikes
**Question:** Your company receives an unexpectedly high Azure cloud bill caused by temporary test environments left running over the weekend. How do you remediate and prevent this?  
**Solution:**
1. **Immediate Remediation:** Inspect Cost Analysis in Azure Portal to pinpoint the costliest resource groups and immediately stop or delete idle test instances.
2. **Preventative Controls:**
   * Configure **Azure Cost Management Budget Alerts** to notify teams when spending reaches 50%, 75%, and 90% of budget.
   * Apply auto-shutdown schedules on test VMs.
   * Implement automated cleanup scripts or tags with expiration dates to decommission temporary test resource groups automatically.

### 📌 Scenario 6: Zero-Downtime Web Application Deployment
**Question:** You need to deploy a Node.js web application to Azure App Service without causing downtime for active users. What strategy do you implement?  
**Solution:** I would utilize **Azure App Service Deployment Slots**. The pipeline deploys the new release into a `staging` slot and runs automated health checks. Once validated, a zero-downtime **Slot Swap** operation exchanges the staging slot with the `production` slot seamlessly via virtual IP switching.

### 📌 Scenario 7: Asynchronous Component Decoupling
**Question:** An e-commerce application needs to process order payments asynchronously so that heavy order spikes do not crash the checkout frontend. Which Azure service would you recommend?  
**Solution:** I would integrate **Azure Service Bus (Queue)**. The frontend order service publishes order messages to the queue immediately and confirms receipt to the customer. A backend worker service pulls and processes payment transactions asynchronously from the queue at a steady rate, decoupling the tiers and ensuring fault tolerance.

### 📌 Scenario 8: Logical Resource Isolation for Multi-Tier Apps
**Question:** Your application includes a frontend Web App, a backend API, a PostgreSQL database, and virtual network subnets. How should these resources be organized in Azure?  
**Solution:** I would deploy all resources sharing the same lifecycle into a dedicated **Azure Resource Group** (e.g., `rg-ecommerce-prod`). For enterprise multi-environment segregation, separate Resource Groups would be maintained for `dev`, `staging`, and `production`, governed by Role-Based Access Control (RBAC) and resource tags (`Environment=Production`, `Owner=DevOpsTeam`).

### 📌 Scenario 9: Private Dependency Sharing Across Multiple Teams
**Question:** Several internal development teams need to share private proprietary npm/NuGet libraries without publishing them to public registries. What Azure service should be used?  
**Solution:** I would configure an **Azure Artifacts Feed**. The feed provides private, authenticated access to internal packages, integrates with developers' local package managers, and caches upstream public dependencies to maintain build reproducibility and security.

### 📌 Scenario 10: Interview Question: "Why learn both AWS and Azure?"
**Question:** How would you concisely answer: *"Why should an engineer learn both AWS and Azure when the fundamental concepts are nearly identical?"*  
**Solution:** *"While foundational cloud concepts (compute, storage, networking, IAM) are universal, enterprises operate across diverse multi-cloud architectures based on technical requirements, cost optimization, and existing Microsoft enterprise integrations. Mastering both platforms allows a DevOps engineer to design cloud-agnostic solutions, lead multi-cloud migrations, and immediately adapt to any client or organizational technology stack."*

---

## 11. Key Takeaways & Summary

1. **Multi-Cloud Fluency:** Treat cloud platforms not as incompatible technologies, but as different implementations of the same fundamental computing, storage, and networking principles.
2. **Infrastructure vs. Delivery Lifecycle:** Use **Azure Portal** to provision and monitor infrastructure resources; use **Azure DevOps** to orchestrate source code, work tracking, and CI/CD pipelines.
3. **Enterprise Governance Structure:** Remember the hierarchy: **Tenant → Management Groups → Subscriptions → Resource Groups → Resources**.
4. **Git Discipline:** Enforce branch policies requiring Pull Request peer reviews and automated CI pipeline checks before merging into `main`.
5. **Modern Pipelines:** Azure Pipelines leverages declarative **YAML** files (`azure-pipelines.yml`) versioned directly within the repository.
6. **Managed Cloud Hosting:** Azure App Service provides a serverless/PaaS hosting model for deploying web applications rapidly without managing VM infrastructure.
7. **Cost Awareness:** Always track resource utilization and decommission idle cloud assets to maintain financial discipline.

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
