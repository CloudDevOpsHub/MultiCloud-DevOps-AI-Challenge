# Azure DevOps / Platform Engineering – Interview Preparation

## Target Role

**Azure Platform | CI/CD | Deployments | Automation | Monitoring | Production Readiness | Operational Support**

This preparation is based on the given Job Description (JD). The interview is likely to focus heavily on **real-world troubleshooting, enterprise Azure architecture, CI/CD, AKS, security, networking, monitoring, and production support**.

---

# 1. What the Interviewer Is Looking For

You should be able to explain not only **what a service does**, but also **how you used it in a production environment**.

Focus on these areas:

- Microsoft Azure and Azure DevOps
- CI/CD pipeline design and troubleshooting
- Deployment strategies and environment promotion
- Docker and Kubernetes / AKS
- Azure Container Registry
- Azure Key Vault
- Azure VNet, networking and private connectivity
- Managed Identity and Service Principals
- Microsoft Entra ID, SSO and RBAC
- Azure Monitor, Log Analytics, Application Insights and alerts
- Infrastructure as Code using Terraform/Bicep/ARM
- PostgreSQL and cloud databases
- APIs and microservices
- Azure Databricks / Data Lake integration
- Production readiness and go-live support
- Application + infrastructure troubleshooting
- Working with external development teams/partners

---

# 2. Must-Prepare Real-World Scenarios

## Scenario 1: CI/CD Pipeline Design

**Question:** Design a CI/CD pipeline for a microservices application running on AKS.

### Expected approach

Explain the flow:

```text
Developer
   ↓
Git Repository
   ↓
Azure DevOps Pipeline
   ↓
Build + Unit Tests
   ↓
Docker Build
   ↓
Security / Vulnerability Scan
   ↓
Push Image → Azure Container Registry
   ↓
Deploy to DEV
   ↓
Testing
   ↓
QA / UAT Approval
   ↓
Production Approval
   ↓
Deploy to AKS
   ↓
Monitoring + Alerts
```

Mention:

- Branching strategy
- YAML pipelines
- Service connections
- Variable groups / Key Vault
- Artifact or container image versioning
- Approval gates
- Rollback strategy
- Environment-specific configuration

---

# 3. Scenario 2: Production Deployment Failure

**Situation:** A deployment succeeds in Azure DevOps but the application is not working in production.

### Troubleshooting approach

Start from the application layer and move downward:

1. Check pipeline logs.
2. Verify deployed image/version.
3. Check AKS pods.
4. Check pod events.
5. Check container logs.
6. Check Kubernetes deployment/service.
7. Check ingress/load balancer.
8. Check DNS.
9. Check NSG/VNet/private endpoint connectivity.
10. Check Key Vault/secrets.
11. Check database connectivity.
12. Check Application Insights and Azure Monitor.

Useful commands:

```bash
kubectl get pods -n <namespace>
kubectl get deployment -n <namespace>
kubectl get svc -n <namespace>
kubectl get ingress -n <namespace>
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
```

---

# 4. Scenario 3: AKS Pod Is in CrashLoopBackOff

Possible reasons:

- Application startup failure
- Wrong environment variable
- Missing secret
- Database connection failure
- Incorrect image
- Application configuration issue
- Resource limits
- Liveness/readiness probe failure

Troubleshooting:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

Then verify:

- ConfigMaps
- Secrets
- Key Vault integration
- Managed Identity
- Database/network connectivity
- CPU/memory limits

---

# 5. Scenario 4: Image Cannot Be Pulled from ACR

If AKS shows:

```text
ImagePullBackOff
```

Check:

- Image name
- Image tag
- Image exists in ACR
- AKS has permission to pull from ACR
- Managed Identity / Service Principal permissions
- Network connectivity
- Private endpoint configuration
- DNS resolution

Typical permission:

```text
AcrPull
```

---

# 6. Scenario 5: Secret Management

**Question:** How would you securely manage database passwords and API keys?

Do not hardcode secrets in:

- Git
- Dockerfile
- YAML pipeline
- Kubernetes manifests

Preferred architecture:

```text
Application
    ↓
Managed Identity
    ↓
Azure Key Vault
    ↓
Secrets
```

Explain that Managed Identity can reduce the need to store credentials for Azure resource access.

---

# 7. Scenario 6: Managed Identity vs Service Principal

### Managed Identity

Used when an Azure resource/application needs to authenticate to another Azure service without manually managing credentials.

Example:

```text
AKS / VM / App Service
       ↓
Managed Identity
       ↓
Key Vault / Storage / Azure Resources
```

### Service Principal

An application identity used for authentication to Azure.

Commonly used for automation and CI/CD, although modern Azure implementations should prefer workload identity / federated identity where appropriate to reduce long-lived secrets.

---

# 8. Scenario 7: Azure Networking Issue

**Situation:** Application in AKS cannot connect to PostgreSQL.

Check:

```text
AKS
 ↓
VNet
 ↓
Subnet
 ↓
NSG / Route
 ↓
Private Endpoint / Database
 ↓
DNS
```

Investigate:

- NSG rules
- Route tables
- Private Endpoint
- Private DNS Zone
- Firewall rules
- Port 5432
- VNet peering
- Network policies
- DNS resolution

---

# 9. Scenario 8: Zero-Downtime Deployment

Be prepared to explain:

### Rolling Deployment

Gradually replaces old pods with new pods.

### Blue-Green Deployment

```text
Blue = Current Production
Green = New Version

Traffic → Blue

Test Green

Traffic → Green
```

### Canary Deployment

Release the new version to a small percentage of users first.

Example:

```text
95% → Version 1
5%  → Version 2
```

If healthy, gradually increase Version 2 traffic.

---

# 10. Scenario 9: Production Monitoring

A good monitoring architecture could include:

```text
Application
    ↓
Application Insights
    ↓
Azure Monitor
    ↓
Log Analytics Workspace
    ↓
Dashboards + Alerts
    ↓
Incident / Operations Team
```

Monitor:

- CPU
- Memory
- Pod restarts
- HTTP 4xx/5xx
- Response time
- Availability
- Database performance
- Application exceptions
- AKS node health
- Disk usage
- Deployment failures

---

# 11. Scenario 10: Production Readiness Checklist

Before production go-live, verify:

### Application

- Health checks
- Logging
- Error handling
- Configuration
- Dependency checks

### Infrastructure

- Capacity
- Scaling
- Availability
- Backup
- Disaster recovery

### Security

- RBAC
- Managed Identity
- Key Vault
- Network security
- Least privilege

### CI/CD

- Approval process
- Rollback
- Versioning
- Deployment validation

### Monitoring

- Dashboards
- Alerts
- Application logs
- Infrastructure logs

### Operations

- Runbook
- On-call support
- Incident process
- Rollback procedure

---

# 12. Scenario 11: Terraform Drift

**Situation:** Someone manually changes an Azure resource from the portal.

Terraform state no longer matches the real infrastructure.

Explain:

```bash
terraform plan
```

Terraform detects the difference.

Possible approaches:

- Revert the manual change
- Update Terraform code and apply
- Import resources if required
- Establish process preventing unmanaged production changes

---

# 13. Scenario 12: Environment Promotion

Explain a controlled promotion model:

```text
DEV
 ↓
Automated Tests
 ↓
QA
 ↓
UAT
 ↓
Approval
 ↓
PRODUCTION
```

Important points:

- Same artifact should ideally be promoted between environments.
- Configuration should be environment-specific.
- Production deployment should have approval and rollback.
- Do not rebuild different application artifacts for each environment unless there is a strong reason.

---

# 14. Scenario 13: External Development Partner

The interviewer may ask how you work with external teams.

Good answer structure:

1. Define deployment standards.
2. Provide branching and PR guidelines.
3. Define CI/CD requirements.
4. Establish environment ownership.
5. Define security and access boundaries.
6. Set logging/monitoring requirements.
7. Review production readiness.
8. Coordinate UAT and go-live.
9. Maintain rollback and incident procedures.

---

# 15. Scenario 14: Database Connection Failure

Application reports:

```text
Connection refused / timeout
```

Check:

```text
Application
   ↓
DNS
   ↓
Network
   ↓
Firewall / NSG
   ↓
Database endpoint
   ↓
Authentication
   ↓
Database availability
```

For PostgreSQL, remember:

```text
Default Port = 5432
```

Check connection string, credentials, DNS, firewall/private endpoint and database availability.

---

# 16. Scenario 15: High CPU in Production

Your approach:

1. Confirm whether the issue is application or infrastructure.
2. Check Azure Monitor metrics.
3. Check AKS pod CPU.
4. Check request traffic.
5. Check application logs.
6. Check recent deployment.
7. Check database dependency.
8. Scale if required.
9. Investigate root cause.
10. Document the incident.

Do not simply say:

> "I will increase CPU."

The interviewer wants to see **root-cause analysis**.

---

# 17. 30 Interview Questions

## Azure Platform

### 1. Explain the Azure architecture of an enterprise application you have worked on.

Be ready to explain:

```text
Users
 ↓
Application Gateway / Load Balancer
 ↓
AKS
 ↓
Microservices
 ↓
PostgreSQL / Storage
 ↓
Key Vault
 ↓
Monitoring
```

---

### 2. What Azure services have you worked with in production?

Mention only services you can actually explain.

For every service, prepare:

- Why it was used
- Architecture
- Security
- Troubleshooting
- Cost/scaling considerations

---

### 3. How would you design a highly available application on Azure?

Discuss:

- Availability Zones
- Load balancing
- AKS/node pools
- Horizontal scaling
- Database HA
- Backup
- Disaster recovery
- Monitoring

---

## Azure DevOps & CI/CD

### 4. How would you design a CI/CD pipeline for an application deployed to AKS?

Explain:

```text
Git → Build → Test → Docker → Scan → ACR
→ DEV → QA → UAT → Approval → PROD
```

---

### 5. How do you manage secrets in Azure DevOps pipelines?

Discuss:

- Azure Key Vault
- Variable groups
- Secret variables
- Managed Identity
- Workload identity/federated authentication
- Avoiding secrets in source code

---

### 6. What is the difference between CI and CD?

**CI:** Build and validate code frequently.

**CD:** Automatically or through controlled approvals deliver the application to target environments.

---

### 7. How do you implement rollback in CI/CD?

Possible methods:

- Previous container image
- Kubernetes rollout undo
- Blue-green deployment
- Canary deployment
- Previous application artifact

Example:

```bash
kubectl rollout undo deployment/<deployment-name>
```

---

### 8. How do you promote an application from DEV to QA/UAT and Production?

Discuss:

- Environment stages
- Approvals
- Testing
- Artifact promotion
- Environment variables
- Production gates
- Rollback

---

## Docker & Kubernetes / AKS

### 9. What is the difference between Docker image and container?

An **image** is the packaged application template.

A **container** is a running instance of that image.

---

### 10. Explain Kubernetes Deployment, Service and Ingress.

**Deployment:** Manages application pods and replicas.

**Service:** Provides stable networking to pods.

**Ingress:** Manages external HTTP/HTTPS routing.

---

### 11. What happens when an AKS pod goes into CrashLoopBackOff?

Explain:

```text
kubectl get pods
→ kubectl describe pod
→ kubectl logs
→ kubectl logs --previous
→ Check configuration/secrets
→ Check dependencies
→ Check resources/probes
```

---

### 12. How do you troubleshoot ImagePullBackOff?

Check:

- Image name
- Tag
- ACR image
- Authentication
- AcrPull permission
- Managed Identity
- Network/DNS

---

### 13. How do you achieve high availability in AKS?

Discuss:

- Multiple nodes
- Availability Zones
- Multiple replicas
- Pod anti-affinity/topology spread
- Horizontal Pod Autoscaler
- Cluster Autoscaler
- Readiness/liveness probes
- PodDisruptionBudget

---

### 14. What is the difference between readiness and liveness probes?

**Readiness:** Should this pod receive traffic?

**Liveness:** Is the container healthy enough to keep running?

---

## ACR, Key Vault & Identity

### 15. How does AKS securely pull images from Azure Container Registry?

Explain authentication/authorization using Azure identity and appropriate **AcrPull** permissions.

---

### 16. How would you integrate Azure Key Vault with AKS?

Possible architecture:

```text
AKS Workload
   ↓
Managed Identity / Workload Identity
   ↓
Key Vault
   ↓
Secret
```

Discuss the Secrets Store CSI Driver where applicable.

---

### 17. What is Azure RBAC and how do you apply least privilege?

Explain:

```text
Who?
 ↓
What permission?
 ↓
On which resource?
```

Example:

```text
AKS Identity
 ↓
AcrPull
 ↓
Specific ACR
```

---

### 18. Explain Microsoft Entra ID, SSO and RBAC.

Be ready to explain:

- User authentication
- Application authentication
- SSO
- Groups
- Roles
- RBAC
- Conditional Access at a high level

---

## Networking

### 19. Explain Azure VNet, subnet, NSG and private endpoint.

Prepare a simple architecture:

```text
VNet
 ├── AKS Subnet
 ├── Application Subnet
 └── Database / Private Endpoint Subnet
```

Explain how traffic is controlled and how private connectivity works.

---

### 20. An application cannot connect to PostgreSQL. How do you troubleshoot it?

Check:

- DNS
- Port 5432
- NSG
- Firewall
- Private Endpoint
- Private DNS
- Route
- Credentials
- Database availability
- Application configuration

---

## Monitoring & Operations

### 21. What Azure monitoring tools have you used?

Be prepared to explain:

- Azure Monitor
- Log Analytics
- Application Insights
- Alerts
- Dashboards
- AKS monitoring
- Metrics vs logs

---

### 22. How would you monitor an AKS production environment?

Monitor:

```text
Cluster
Nodes
Pods
Containers
Application
Network
Database
```

Important signals:

- CPU/memory
- Pod restarts
- Error rate
- Latency
- Availability
- HTTP 5xx
- Node health

---

### 23. Production API response time suddenly increases. What will you do?

A strong troubleshooting flow:

```text
Alert
 ↓
Check recent deployment
 ↓
Check Application Insights
 ↓
Check API latency
 ↓
Check AKS CPU/memory
 ↓
Check database latency
 ↓
Check network
 ↓
Identify bottleneck
 ↓
Mitigate
 ↓
Root cause
```

---

### 24. How do you design alerts without creating too many false alarms?

Discuss:

- Meaningful thresholds
- Dynamic thresholds where appropriate
- Error-rate based alerts
- Availability alerts
- Alert severity
- Action groups
- Alert suppression/deduplication
- Regular tuning

---

## IaC & Automation

### 25. How have you used Terraform for Azure infrastructure?

Explain:

- Providers
- Modules
- Variables
- State
- Remote backend
- Plan/apply
- CI/CD integration
- Environment separation
- State locking

---

### 26. What is Terraform state and why is it important?

Terraform state maps the Terraform configuration to real infrastructure.

For teams, use a secure remote backend with appropriate locking/concurrency controls.

---

### 27. What is Terraform drift and how do you handle it?

Use:

```bash
terraform plan
```

Then decide whether to:

- Revert manual changes
- Update Terraform code
- Import resources where appropriate

---

## Data, APIs & Microservices

### 28. How would you deploy and expose microservices on AKS?

Discuss:

```text
Docker
 ↓
ACR
 ↓
AKS Deployment
 ↓
Service
 ↓
Ingress / Application Gateway
 ↓
API / Users
```

Also discuss:

- Configuration
- Secrets
- Service discovery
- Scaling
- Monitoring
- Health probes

---

### 29. How would you integrate an Azure application with Data Lake / Databricks?

At a high level:

```text
Application / APIs
       ↓
Azure Data Lake Storage
       ↓
Databricks
       ↓
Data Processing
       ↓
Analytics / Downstream Systems
```

Be ready to discuss:

- Identity
- Storage access
- RBAC
- Data ingestion
- Networking
- Monitoring

This is a preferred skill in the JD, so even exposure-level knowledge can help.

---

# Production Support / Troubleshooting

### 30. Tell me about a major production incident you handled.

Use this structure:

**Situation → Impact → Investigation → Root Cause → Fix → Prevention**

Example:

> A production API started returning 5xx errors after a deployment. I first checked the monitoring alerts and application logs, then compared the deployment version with the previous working version. The AKS pods were healthy, but the application could not connect to PostgreSQL because of a configuration/network issue. We restored service using the rollback procedure, fixed the configuration, validated the connection in UAT, and redeployed. After the incident, we added deployment validation and better alerts to prevent recurrence.

---

# 18. Rapid-Fire Topics to Revise

Before the interview, make sure you can explain these without searching:

```text
Azure VNet
Subnet
NSG
Route Table
Private Endpoint
Private DNS
Azure Load Balancer
Application Gateway
AKS
Kubernetes Deployment
Service
Ingress
ConfigMap
Secret
HPA
Cluster Autoscaler
Liveness Probe
Readiness Probe
ACR
Key Vault
Managed Identity
Service Principal
Entra ID
RBAC
Azure Monitor
Log Analytics
Application Insights
Azure DevOps
YAML Pipeline
Service Connection
Variable Group
Approvals
Terraform
Terraform State
Terraform Drift
Docker
PostgreSQL
Microservices
API Gateway
Blue-Green Deployment
Canary Deployment
Rolling Deployment
Rollback
Production Readiness
Incident Management
```

---

# 19. Interview Answer Formula

For almost every technical question, use this pattern:

### 1. What is it?

Give a simple definition.

### 2. Why did you use it?

Explain the business/technical reason.

### 3. How did you implement it?

Give the architecture or process.

### 4. What problem did you face?

Talk about a real production-type challenge.

### 5. How did you troubleshoot it?

Explain your investigation steps.

### 6. What was the result?

Mention reliability, security, automation, performance or operational improvement.

---

# 20. Final Preparation Challenge 🔥

If you really want to crack this role, don't prepare only definitions.

You should be able to answer questions like:

> "Your pipeline is green, but production is down. What do you check?"

> "AKS pods are running, but users are getting 502 errors. Troubleshoot it."

> "AKS cannot pull an image from ACR. What could be wrong?"

> "Your application cannot access Key Vault. How will you troubleshoot it?"

> "Production PostgreSQL is unreachable from AKS. Walk me through the investigation."

> "A developer manually changed a production resource. Terraform now shows drift. What will you do?"

> "How would you design DEV → QA → UAT → PROD promotion?"

> "How do you make a production deployment zero downtime?"

> "How do you prepare an application for go-live?"

> "Tell me about a production incident you personally handled."

These are the questions where **hands-on experience matters more than memorized definitions**.

---

# Screening Interview Focus

For the first screening round, be ready with:

1. **2-minute self introduction**
2. **One complete Azure project explanation**
3. **One CI/CD pipeline explanation**
4. **One AKS troubleshooting scenario**
5. **One production incident**
6. **One Terraform/IaC example**
7. **One Azure networking issue**
8. **One security/Key Vault/Identity example**
9. **One monitoring/alerting example**
10. **One deployment/rollback example**

Most importantly, do not claim experience with a technology you cannot explain technically.

**The goal is not to know every Azure service. The goal is to show that you can design, deploy, troubleshoot and operate an enterprise application on Azure.**

---

# Interview Preparation Message

**🔥 Azure DevOps / Platform Engineering – Screening Interview**

Read the complete interview preparation document carefully and prepare the scenarios + 30 questions before the screening.

The JD is focused on **Azure, Azure DevOps, CI/CD, AKS, Docker, ACR, Key Vault, Networking, Entra ID, RBAC, Monitoring, Terraform, Production Support and Troubleshooting.**

**Read the message completely and reply to confirm that you are ready.**

I am planning the **first Screening Interview today, 3rd Sep at 9 PM**.

Let's see who can actually crack this Job! 🔥

**Come prepared with real project examples, troubleshooting scenarios and production-level answers.**
