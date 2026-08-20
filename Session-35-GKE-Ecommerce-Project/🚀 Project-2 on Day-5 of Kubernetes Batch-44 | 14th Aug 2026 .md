# 🚀 Project-2 on Day-5 of Kubernetes Batch-44: E-Commerce Microservices Deployment on GKE

[![Module: Kubernetes Projects](https://img.shields.io/badge/Module-Kubernetes_Project-E37400?style=for-the-badge&logo=googlecloud)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 14th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-14th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)

### 1. Session Overview

The session focused on deploying a real-world e-commerce microservices application on Kubernetes, with emphasis on understanding the architecture, creating a Kubernetes cluster, deploying multiple microservices, troubleshooting failures, and explaining the project effectively in interviews.
The instructor explained the difference between monolithic and microservices architecture, why microservices are useful for modern applications, how different services can use different programming languages, and how Kubernetes helps manage containerized microservices efficiently.
The practical portion covered creating a Google Kubernetes Engine (GKE) cluster, connecting to the cluster, cloning the project code, understanding the src and release folders, working with Kubernetes YAML manifests, deploying services using kubectl, exposing the frontend, observing Kubernetes self-healing, deleting and recreating pods/deployments, and troubleshooting service failures.
A major part of the session was also dedicated to interview preparation, particularly how to describe this project on a resume and how to answer cross-questions about architecture, deployment, scaling, troubleshooting, and Kubernetes.

### 2. Key Topics Covered

Monolithic vs Microservices Architecture
Advantages of Microservices
Independent Deployment and Scaling
Loose Coupling and Isolation
Multiple Programming Languages in Microservices
Microservices and AI Services
Cost Optimization with Kubernetes
Kubernetes Architecture for Microservices
GKE Cluster Creation
Standard vs Autopilot Cluster
Kubernetes Nodes and Resources
Kubernetes YAML/Manifest Files
Deployment and Service Objects
kubectl Commands
Kubernetes Deployment
Kubernetes Service
Frontend Exposure
LoadBalancer Service
Kubernetes Self-Healing
Pod Recreation
Deployment Deletion
Redeployment
Monorepo Concept
src and release Folder Structure
E-commerce Microservices Architecture
Product Catalog Service
Cart Service
Checkout Service
Payment Service
Currency Service
Recommendation/AI Service
Database Integration
Monitoring and Grafana
Troubleshooting Microservices
Resume and Interview Project Explanation
Manual Deployment vs CI/CD Automation
Future Jenkins Automation

### 3. Important Topics Explained

### 1. Monolithic Architecture

In a monolithic architecture, application components are developed and deployed as a single application/unit.
For example:
Frontend
Backend
Database
Payment
Product
Checkout

are tightly integrated into one application.
The major disadvantage discussed was that if a critical component fails, the entire application can potentially be affected. Scaling individual components is also difficult because the whole application generally needs to be scaled.

### 2. Microservices Architecture

Microservices divide an application into small, independent services.
For the e-commerce application, examples include:
Frontend
Product Catalog
Cart
Checkout
Payment
Currency
Email
Recommendation

Each service can be developed, deployed, scaled, and potentially maintained independently.
The key characteristics discussed were:
Independent services
Isolation
Loose coupling
Independent deployment
Independent scaling
Easier replacement of individual services
Better fault isolation

### 3. Why Different Languages Can Be Used

A major benefit of microservices is that different services do not necessarily need to use the same programming language.
For example:
Frontend Service       → JavaScript/TypeScript
Backend Service        → Node.js
AI Recommendation      → Python
Other Services         → Go/Java/etc.

The instructor specifically explained that Python can be useful for AI-related services, allowing the AI component to be developed separately without forcing the entire application to use Python.

### 4. Kubernetes for Microservices

Running many microservices directly on separate VMs can become expensive.
For example, if there are 10 services and each requires multiple VMs for high availability, infrastructure requirements can increase significantly.
Kubernetes allows multiple containerized workloads to run on a smaller cluster of nodes.
The session therefore connected Kubernetes with:
Resource utilization
Cost optimization
Container orchestration
Scaling
High availability
Self-healing

### 5. GKE Cluster

The practical session used Google Kubernetes Engine (GKE).
The workflow was approximately:
```text
Google Cloud
     ↓
Create GKE Cluster
     ↓
Configure Nodes
     ↓
Connect to Cluster
     ↓
Clone Application
     ↓
Apply Kubernetes Manifest
     ↓
Create Deployments & Services
     ↓
Expose Application
     ↓
Test Application
```


The instructor demonstrated creating a cluster using the Standard configuration and discussed node CPU, memory, disk size, and node count.

### 6. Kubernetes Manifest Files

The Kubernetes deployment configuration was maintained through YAML manifest files.
The manifest describes resources such as:
Deployments
Services
Configuration
Metadata
Ports
Replicas
Other Kubernetes settings
The session pointed out that when many microservices exist, YAML files can contain a significant amount of repeated configuration.
This is one reason templates and automation become useful.

### 7. Monorepo Concept

The project used a monorepo-style structure, where multiple microservices are maintained within a single repository.
A simplified structure discussed was:
```text
microservices-demo/
│
├── src/
│   ├── service-1/
│   ├── service-2/
│   ├── service-3/
│   └── ...
│
└── release/
    └── Kubernetes manifests
```


The key idea is that multiple services can exist in separate folders while still being maintained within one repository.

### 8. Kubernetes Deployment

The practical deployment involved connecting to the GKE cluster and applying Kubernetes configuration.
The instructor demonstrated commands around:
```bash
kubectl get pods
kubectl get svc
kubectl get deployments
kubectl apply -f <manifest-file>
kubectl create -f <manifest-file>
```


Applying the manifest caused Kubernetes to create the required resources for the microservices.

### 9. Kubernetes Services

The session explained that simply running a frontend pod does not automatically make it accessible from the internet.
A Kubernetes Service can provide networking and exposure for workloads.
The discussion mentioned mechanisms such as:
Service
LoadBalancer
Ingress
kube-proxy
For the practical application, a LoadBalancer-based approach was used to expose the frontend externally.

### 10. E-commerce Microservices Flow

The main project was an e-commerce application.
A simplified flow discussed in the session was:
```text
User
 ↓
Frontend
 ↓
Product Catalog
 ↓
Product Details
 ↓
Cart
 ↓
Checkout
 ↓
Payment
 ↓
Order
```


Additional services can support the application:
Currency Service
Recommendation Service
Email Service

The recommendation service was used as an example of where an AI-based microservice could be integrated.

### 11. Currency Service

The session also discussed international e-commerce.
For example:
User → India
```text
Product Price → USD
        ↓
Currency Service
        ↓
INR
```


A separate currency microservice can handle currency conversion instead of embedding this functionality into the entire application.

### 12. Kubernetes Self-Healing

One of the most important practical demonstrations was Kubernetes' self-healing behavior.
A pod was deleted intentionally.
The observed behavior was:
```text
Pod Running
     ↓
Pod Deleted
     ↓
Deployment detects missing replica
     ↓
New Pod Created
     ↓
Application Recovers
```


This happens because the Kubernetes Deployment maintains the desired state.
The important interview point is:
A Deployment manages the desired number of pod replicas, so when a pod disappears, Kubernetes attempts to create a replacement.

### 13. Pod Failure vs Deployment Failure

The session made an important distinction.
If an individual pod is deleted:
```text
Pod deleted
    ↓
Deployment remains
    ↓
New pod created
```


But if the Deployment itself is deleted, Kubernetes no longer has that Deployment object managing the pods.
Therefore, the application/service can be affected until the Deployment is recreated.
This distinction is extremely important for troubleshooting questions.

### 14. Troubleshooting Resource Issues

During deployment, the session encountered a resource-related issue where the cluster did not have sufficient resources for a workload.
The discussion connected the problem with:
CPU availability
Worker nodes
Workloads
Cluster sizing
The lesson was that Kubernetes deployment failures are not always caused by incorrect YAML. Insufficient cluster resources can also prevent workloads from being scheduled.

### 15. Redeployment

When a service or deployment is removed, it may need to be redeployed.
The session demonstrated applying the Kubernetes manifest again to recreate missing resources.
It also explained that using a complete manifest can result in Kubernetes reporting that some resources already exist while creating resources that are missing.
For production environments, targeted deployment of the affected service can be preferable to blindly redeploying everything.

### 16. Monitoring and Troubleshooting

The instructor connected this project with future monitoring concepts.
The planned monitoring approach involves tools such as:
```text
Application
    ↓
Kubernetes
    ↓
Monitoring
    ↓
Prometheus / Grafana
    ↓
Alerts
    ↓
DevOps / L1 / L2 Team
```


Monitoring can help identify failures that users may otherwise discover first.
The session emphasized that in a real production environment, engineers should not depend only on manually checking the application.

### 17. Manual Deployment vs Automation

The practical deployment was intentionally performed manually for learning purposes.
The instructor explained that after understanding the manual process, it can be automated using tools such as Jenkins.
Possible future flow:
```text
Developer
   ↓
Git Repository
   ↓
Build
   ↓
Docker Image
   ↓
Push Image
   ↓
Jenkins / CI-CD
   ↓
Kubernetes
   ↓
Deployment
```


The purpose of first performing the process manually is to understand what automation is actually automating. Humanity occasionally discovers that knowing the thing before automating the thing is useful.

### 4. Practical Workflow Demonstrated

The practical flow of the session can be summarized as:
```text
1. Create GKE Cluster
        ↓
2. Configure Nodes
        ↓
3. Connect to Kubernetes Cluster
        ↓
4. Clone Microservices Repository
        ↓
5. Explore src Folder
        ↓
6. Explore release Folder
        ↓
7. Review Kubernetes Manifest
        ↓
8. Apply/Create Manifest
        ↓
9. Create Deployments
        ↓
10. Create Services
        ↓
11. Check Pods
        ↓
12. Check Deployments
        ↓
13. Check Services
        ↓
14. Expose Frontend
        ↓
15. Access Application
        ↓
16. Delete a Pod
        ↓
17. Observe Self-Healing
        ↓
18. Delete a Deployment
        ↓
19. Observe Application Failure
        ↓
20. Redeploy Required Service
        ↓
21. Restore Application
```



### 5. Important Commands Covered

The transcript clearly demonstrated or discussed commands from the following areas:
```bash
kubectl get pods
kubectl get svc
kubectl get deployments
kubectl apply -f <manifest>
kubectl create -f <manifest>
kubectl delete deployment <deployment-name>
git clone <repository>
```


The exact command spelling for some portions of the live demonstration was not always clear in the transcript, so the summary preserves the commands that were identifiable rather than inventing missing details.

### 6. Interview-Focused Learning

A significant part of the session focused on how to present the project in an interview.
A suitable project explanation based strictly on the session would cover:
E-commerce application
Microservices architecture
Multiple independent services
Kubernetes/GKE deployment
Containerized workloads
Kubernetes Deployments and Services
External application exposure
Self-healing
Resource troubleshooting
Monorepo structure
Kubernetes manifest management
Monitoring
Future CI/CD automation
A strong explanation should focus on what you implemented and what problem Kubernetes solved, rather than merely listing tools.

### 7. Key Takeaways

Microservices divide an application into independent services.
Each service can potentially use a different programming language.
Microservices provide better isolation and independent deployment.
Individual services can be scaled independently.
Kubernetes is well suited for running large numbers of containerized microservices.
GKE was used to create the Kubernetes environment.
Kubernetes manifests define application resources declaratively.
A Deployment maintains the desired state of pods.
Kubernetes can automatically recreate deleted pods, demonstrating self-healing.
Deleting the Deployment itself is different from deleting an individual pod.
Kubernetes Services provide stable networking and can expose applications.
LoadBalancer can be used to expose a frontend externally.
Resource limitations such as CPU can prevent workloads from being scheduled.
A monorepo can maintain multiple microservices within one repository.
Manual deployment should be understood before automating it through CI/CD.
Monitoring tools such as Grafana become important for production troubleshooting.
The project can be presented as a real-world Kubernetes-based e-commerce microservices deployment.
Troubleshooting knowledge is as important as deployment knowledge for a DevOps role.
One-Line Session Summary
The session covered a hands-on Kubernetes microservices e-commerce project on GKE, including architecture, cluster creation, YAML manifests, deployments, services, external exposure, self-healing, troubleshooting, redeployment, monitoring concepts, and how to present the project in DevOps interviews.
10 Interview Questions & Answers
### ❓ Q1: What is the difference between monolithic and microservices architecture?

**💡 Answer:**

In a monolithic architecture, the entire application is developed and deployed as a single unit. In microservices architecture, the application is divided into smaller, independent services. Each service can be developed, deployed, scaled, and maintained independently.

### ❓ Q2: Why did you use Kubernetes for the microservices application?

**💡 Answer:**

Kubernetes helps manage multiple containerized microservices. It provides features such as service discovery, scaling, self-healing, load balancing, and automated deployment. It reduces the effort required to manually manage individual containers.

### ❓ Q3: What is GKE?

**💡 Answer:**

GKE stands for Google Kubernetes Engine. It is Google's managed Kubernetes service that allows us to create and manage Kubernetes clusters on Google Cloud.

### ❓ Q4: What is a Kubernetes Deployment?

**💡 Answer:**

A Deployment manages the desired state of application Pods. It ensures that the required number of replicas are running and creates replacement Pods when existing Pods fail or are deleted.

5. What happens if you manually delete a Pod managed by a Deployment?
**💡 Answer:**

The Deployment detects that the actual number of Pods is lower than the desired number and automatically creates a new Pod. This demonstrates Kubernetes' self-healing capability.

6. What is the difference between a Pod and a Deployment?
**💡 Answer:**

A Pod is the smallest deployable unit in Kubernetes and runs one or more containers. A Deployment manages Pods and ensures that the desired number of replicas are running.

7. Why do we need a Kubernetes Service?
**💡 Answer:**

Pods are temporary and their IP addresses can change. A Service provides a stable networking endpoint for accessing Pods. It can also expose an application internally or externally depending on its type.

8. How did you expose the frontend application?
**💡 Answer:**

The frontend was exposed using a Kubernetes Service with a LoadBalancer configuration. This allowed the application to receive an externally accessible address through the cloud provider's load-balancing infrastructure.

9. What is a Kubernetes YAML manifest?
**💡 Answer:**

A YAML manifest is a declarative configuration file used to define Kubernetes resources such as Deployments and Services. Instead of manually creating every resource, we define the desired state in YAML and apply it using kubectl.

10. What problem can occur if the Kubernetes cluster does not have enough resources?
**💡 Answer:**

If the cluster does not have enough CPU or memory, Kubernetes may not be able to schedule a Pod. The Pod can remain in a pending state until sufficient resources become available or the cluster is scaled.

10 Scenario-Based Interview Questions & Answers
1. A Pod is deleted unexpectedly. What will you do?
**💡 Answer:**

First, I would check the Pods:
```bash
kubectl get pods
```


Then I would check the Deployment:
```bash
kubectl get deployments
```


If the Pod is managed by a Deployment, Kubernetes should automatically create a replacement. I would verify that the new Pod is running and check its events/logs if it does not recover.

2. A Pod is stuck in Pending. How would you troubleshoot it?
**💡 Answer:**

I would first check:
```bash
kubectl get pods
kubectl describe pod <pod-name>
```


I would look for scheduling events such as insufficient CPU or memory. If the cluster does not have enough resources, I would either reduce resource requirements or scale the cluster by adding/expanding nodes.

3. Your frontend Pods are running, but users cannot access the application. What would you check?
**💡 Answer:**

I would verify the Service:
```bash
kubectl get svc
```


Then I would check:
Service type
External IP
Port configuration
Target port
Pod labels/selectors
Pod status
I would also use kubectl describe svc to investigate configuration problems.

4. You deleted a Deployment and the application stopped working. Why didn't Kubernetes recreate the Pods?
**💡 Answer:**

Because the Deployment itself was deleted. Kubernetes can recreate Pods when the Deployment still exists and detects that replicas are missing. Once the Deployment is removed, there is no controller maintaining those Pods.
I would recreate the Deployment using the Kubernetes manifest.

5. One microservice is down, but the other services are working. Why is this possible in microservices architecture?
**💡 Answer:**

Microservices are independently deployed and isolated. Therefore, failure of one service does not necessarily bring down the entire application. However, services that depend on the failed service may also experience errors.
I would identify the failed service and check its Pods, logs, Service, and dependencies.

6. You deployed a microservice, but its Pod keeps restarting. What would you check?
**💡 Answer:**

I would check the Pod status and logs:
```bash
kubectl get pods
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```


I would investigate application errors, configuration problems, missing environment variables, container startup failures, resource limits, and health probes.

7. Your application suddenly requires more capacity. How would you handle it in Kubernetes?
**💡 Answer:**

I would scale the affected microservice rather than scaling the entire application unnecessarily.
For example:
```bash
kubectl scale deployment <deployment-name> --replicas=3
```


For production workloads, I could also use Horizontal Pod Autoscaler (HPA) to automatically adjust replicas based on resource utilization.

8. A developer says, "The application works locally but fails in Kubernetes." How would you troubleshoot it?
**💡 Answer:**

I would compare the local and Kubernetes environments.
I would check:
Container image
Environment variables
Configuration
Ports
Service connectivity
Dependencies
Secrets
Pod logs
Pod events
Commands such as kubectl logs and kubectl describe pod would help identify the actual failure instead of performing the traditional DevOps ritual of staring at the screen and hoping.

9. You have 10 microservices and manually deploying each one is taking too much time. What would you improve?
**💡 Answer:**

I would automate the deployment process using a CI/CD pipeline.
A possible workflow would be:
```text
Developer
   ↓
Git Repository
   ↓
Build
   ↓
Docker Image
   ↓
Container Registry
   ↓
Jenkins/CI Pipeline
   ↓
Kubernetes
   ↓
Deployment
```


This reduces manual effort and provides consistent deployments.

10. Production users report that the application is slow. How would you investigate it?
**💡 Answer:**

I would first identify which component is causing the problem instead of assuming Kubernetes is guilty.
I would check:
```bash
kubectl get pods
kubectl top pods
kubectl top nodes
```


Then I would investigate CPU/memory utilization, Pod health, application logs, service communication, and database performance.
For a production environment, I would use monitoring tools such as Prometheus and Grafana to identify resource and performance trends.


