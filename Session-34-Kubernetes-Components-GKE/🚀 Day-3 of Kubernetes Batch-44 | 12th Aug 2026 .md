# 🚀 Day-3 of Kubernetes Batch-44 : Kubernetes Manifests, Pods, Deployments & Services

[![Module: Kubernetes Orchestration](https://img.shields.io/badge/Module-Kubernetes-326CE5?style=for-the-badge&logo=kubernetes)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 12th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-12th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)



### 1. Full Session Overview

The session began with a quick review of what would be covered:
Kubernetes Deployments
YAML
Pushing application artifacts/images to registries such as GCR/ACR
Namespaces
Pods
Node pools
Scaling
Kubernetes manifests
The instructor then created a Google Kubernetes Engine (GKE) Standard cluster and walked through its configuration.
The cluster configuration discussion covered:
Cluster type
Region/zone
Kubernetes version
Networking
Backup configuration
Backup retention
RPO
Node pools
Number of nodes
Cluster autoscaling
Upgrade strategy
Operating system
Machine/CPU configuration
Memory
Disk
Maximum Pods per node
Metadata and labels
The instructor explained that node pools allow different groups of nodes to have different configurations, and demonstrated how adding nodes to a node pool represents node scaling.
The class then moved to using Google Cloud Shell and running GCloud commands. An important practical issue was demonstrated: when running GCP commands from a local machine rather than Cloud Shell, the required Google Cloud SDK/CLI and authentication are necessary.
The command:
```bash
gcloud auth login
```


was discussed for authenticating a local environment.

The next major portion focused on Kubernetes YAML.
Students were asked to create YAML files and install a Kubernetes template extension in Visual Studio Code. The instructor demonstrated how a Kubernetes Pod manifest can be generated and explained the major sections:
apiVersion:
kind:
metadata:
spec:

The Pod configuration included:
Pod name
Labels
Container
Container image
Resource requests/limits
The instructor also emphasized that students should be able to understand and explain the first portion of a Kubernetes manifest during interviews, rather than blindly memorizing every possible YAML field.

The session then moved into Namespaces.
Namespaces were explained as a way of logically isolating resources inside a Kubernetes cluster.
The instructor used the example of:
Development
QA
Pre-Production
Production

Instead of necessarily creating a completely separate Kubernetes cluster for every environment, namespaces can be used to logically separate resources within a cluster.
Commands such as:
```bash
kubectl get ns
kubectl create namespace dev
```


were demonstrated.
The instructor also showed how namespaces can be created through YAML.

The class then discussed IAM-based cluster access.
A practical demonstration showed one person granting another person access to a Google Cloud project/cluster environment.
The discussion included:
IAM
Granting access
Roles
Owner/read/write permissions
Conditional access
Expiring access
Connecting to another user's cluster
This connected Kubernetes administration with the broader cloud security and access-control model.

The final major section focused on Pod communication, Services, replicas and Deployments.
The instructor explained why directly communicating with a Pod's IP address is not considered a reliable application-to-application communication method.
Pods can be recreated, moved or replaced, which means their IP addresses can change.
Therefore:
Pod → Pod

should generally be abstracted through:
Pod → Service → Pod

The Service provides a stable endpoint for communication.

The instructor then introduced replicas and Deployments.
Instead of manually managing individual Pods, a Deployment can manage the desired number of replicas.
For example:
Deployment
|
+-- Pod
+-- Pod
+-- Pod

If one Pod is deleted:
Deployment
|
+-- Pod
+-- Pod
+-- Pod ❌

Kubernetes creates another Pod so that the desired number of replicas is maintained.
The instructor demonstrated this behavior practically by deleting a Pod and showing that a replacement Pod was automatically created.
This was one of the most important practical demonstrations of the session.

### 2. All Key Topics Covered

Kubernetes Cluster Configuration
GKE Standard cluster
Cluster creation
Cluster version
Region and zones
Networking
Backup
Backup retention
RPO
Backup scheduling
Node pools
Node scaling
Cluster autoscaler
Upgrade strategy
Operating system
CPU configuration
Memory configuration
Disk configuration
Pod capacity per node
Cluster metadata
Labels
Google Cloud CLI
Cloud Shell
Google Cloud SDK
GCloud CLI
Local CLI usage
Authentication
```bash
gcloud auth login
```

Active account
Connecting local CLI to GCP
Kubernetes YAML
YAML syntax
YAML indentation
Kubernetes manifest
apiVersion
kind
metadata
name
labels
spec
Containers
Container image
Resource requests
Resource limits
YAML templates
Visual Studio Code
Kubernetes YAML extension
Pods
Pod architecture
Containers inside Pods
Multiple containers
Volumes
Pod IP
Pod lifecycle
Pod replacement
Pod-to-Pod communication
Namespaces
Namespace concept
Resource isolation
Development namespace
QA namespace
Pre-production namespace
Production namespace
Namespace creation through CLI
Namespace creation through YAML
```bash
kubectl get ns
kubectl create namespace
```

IAM and Access
IAM
Project access
Cluster access
User permissions
Owner access
Read access
Write access
Role assignment
Conditional access
Expiring access
Kubernetes Services
Service concept
Stable communication endpoint
Pod IP instability
Service-to-Service communication
DNS/name-based communication
Service mapping
LoadBalancer discussion
Service and port relationship
Replicas
Replica concept
Multiple copies of a Pod
Desired number of Pods
Scaling replicas
Replica management
Deployments
Deployment concept
Creating a Deployment
Deployment managing Pods
Deployment managing replicas
Self-healing
Pod replacement
Scaling through Deployment
Deployment YAML
```bash
kubectl get deployment
```

Kubernetes CLI
```bash
kubectl get
kubectl get ns
kubectl get pods
kubectl get deploy
kubectl create
kubectl create namespace
kubectl create -f
kubectl apply
kubectl delete pod
```

Namespace selection with -n

### 3. Main Key Topics Explained in Detail

3.1 GKE Standard Cluster
The session started with the practical creation of a GKE Standard cluster.
The instructor specifically switched from Autopilot to Standard and explained various configuration options.
A simplified structure is:
GKE Cluster
|
+-- Node Pool
|     |
|     +-- Node
|     +-- Node
|
+-- Kubernetes Workloads

The important lesson was that a cluster isn't simply "click Create and forget about it." You need to understand:
Where it runs
How many nodes it has
What resources those nodes provide
How nodes scale
What OS they use
How upgrades work
How backups are configured

### 4. Node Pools

Node pools were one of the major infrastructure topics.
A node pool is a group of Kubernetes worker nodes managed together.
For example:
Cluster
|
+-- Node Pool
|
+-- Node 1
+-- Node 2
+-- Node 3

If you increase the number of nodes in the pool, you are performing node scaling.
The session also discussed that different node pools can have different configurations.
For example:
Cluster
|
```text
 +-- General Pool
 |     ├── Node
 |     └── Node
 |
 +-- High-CPU Pool
       ├── Node
       └── Node
```


This becomes useful when different applications have different infrastructure requirements.

### 5. Cluster Autoscaler

The instructor demonstrated the Cluster Autoscaler option.
When enabled, Kubernetes/cloud infrastructure can automatically adjust the size of a node pool based on workload requirements.
Conceptually:
```text
More workload
      ↓
More capacity required
      ↓
Node Pool scales
      ↓
Additional Nodes
```


Similarly, when demand decreases, nodes may be reduced according to the configured autoscaling behavior.
This is different from simply increasing the number of Pods.
Important distinction
Pod scaling:
Increase the number of application instances.
Node scaling:
Increase the infrastructure capacity available to run workloads.

### 6. Kubernetes Resource Capacity and Pods per Node

The instructor highlighted the configuration showing a maximum of 110 Pods per node in the demonstrated setup.
This was presented as an interview-relevant point.
The session also clarified an important distinction:
The number of Pods and the number of containers are not necessarily the same thing.
A Pod can contain one or multiple containers.
For example:
Node
|
+-- Pod 1
|    +-- Container
|
+-- Pod 2
|    +-- Container
|
+-- Pod 3
+-- Container
+-- Container

The instructor emphasized the commonly used practical pattern of one main application container per Pod, while also explaining that multiple containers are possible.

### 7. Kubernetes YAML / Manifest

One of the biggest focuses of Day 3 was learning how to write Kubernetes YAML.
The basic structure introduced was:
apiVersion:
kind:
metadata:
spec:

The instructor explained these sections conceptually.
apiVersion
Defines which Kubernetes API version the resource uses.
kind
Defines what Kubernetes object you want to create.
Examples discussed included:
Pod
Deployment
Service

metadata
Contains information about the resource.
For example:
metadata:
name: my-app
labels:
app: web

spec
Defines the desired specification of the resource.
For a Pod, this can include:
Containers
Image
Resource configuration

### 8. Kubernetes Pod YAML

The session demonstrated a Pod manifest containing a container and image.
The conceptual structure was:
apiVersion: v1
kind: Pod
metadata:
name: application
labels:
app: web
spec:
containers:
- name: application
image: nginx

The actual transcript contains a demonstration-specific example and resource configuration, but the important structure is the relationship:
Pod
|
+-- Metadata
|
+-- Specification
|
+-- Container
|
+-- Image
+-- Resources


### 9. Resource Requests and Limits

The instructor discussed CPU and memory configuration inside the container specification.
The important distinction is:
Request
The amount of resource the container asks Kubernetes to reserve/consider for scheduling.
Limit
The maximum amount of that resource the container is allowed to consume according to the configuration.
Conceptually:
Container
|
+-- CPU Request
+-- CPU Limit
+-- Memory Request
+-- Memory Limit

This is important because Kubernetes uses resource information when making scheduling decisions.

### 10. Namespace

Namespace was one of the most important concepts of the session.
The instructor described namespaces as a way of isolating resources logically inside a cluster.
For example:
Kubernetes Cluster
|
+-- dev
|
+-- qa
|
+-- pre-prod
|
+-- prod

Instead of creating a completely separate Kubernetes cluster for every environment, organizations can use namespaces to separate resources where appropriate.
This can reduce infrastructure duplication and therefore reduce cost.
Important clarification
A namespace is primarily a logical isolation and organization mechanism. It should not be treated as a complete security boundary by itself.

### 11. Creating a Namespace

The session demonstrated creating a namespace using:
```bash
kubectl create namespace dev
```


To list namespaces:
```bash
kubectl get ns
```


This allows administrators to see the namespaces available in the cluster.
The session also demonstrated creating namespaces through YAML.

### 12. Namespace Selection with -n

The instructor discussed the use of the -n option.
For example:
```bash
kubectl get pods -n dev
```


This tells kubectl to operate against the dev namespace.
Without specifying -n, commands generally operate against the currently configured/default namespace.
This is particularly important when multiple environments are running inside the same cluster.

### 13. IAM-Based Cluster Access

A practical demonstration was performed where one user gave another user access to a Google Cloud project.
The flow was:
```text
User A
  ↓
IAM
  ↓
Grant Role
  ↓
User B
  ↓
Project/Cluster Access
```


The instructor discussed different access levels and conditional access.
For example, access can potentially be configured to expire after a particular period.
This was explicitly identified as an interview-relevant topic.

### 14. Conditional Access

The session demonstrated that access does not necessarily need to be permanent.
A condition can be attached to access so that it expires at a specified time.
Conceptually:
```text
User
 ↓
IAM Role
 ↓
Condition
 ↓
Access
 ↓
Expiration
```


For example:
```text
Developer
   ↓
Temporary Access
   ↓
Expires after defined period
```


This is useful for temporary troubleshooting or controlled access.

### 15. Pod IP Addresses

The session spent significant time discussing why relying directly on Pod IP addresses is problematic.
Pods can be recreated.
When a Pod is recreated, its IP address can change.
For example:
Pod A
IP: 10.0.0.10

After replacement:
New Pod A
IP: 10.0.0.25

If another application directly uses 10.0.0.10, communication can break.
Therefore, direct Pod-IP communication should not be used as the normal application-discovery mechanism.

### 16. Services Provide Stable Communication

The instructor explained that Kubernetes Services provide a stable abstraction for communicating with workloads.
Instead of:
```text
Application A
     ↓
Pod IP
     ↓
Application B
```


the preferred model discussed was:
```text
Application A
     ↓
Service
     ↓
Pod
```


The Service remains the stable endpoint while the underlying Pods can change.
Conceptually:
Service
|
+-- Pod 1
+-- Pod 2
+-- Pod 3

If Pod 1 disappears and Pod 4 is created:
Service
|
+-- Pod 2
+-- Pod 3
+-- Pod 4

The application consuming the Service does not need to know which individual Pod is currently serving the request.
This was one of the most important concepts of the entire session.

### 17. Service-to-Service Communication

The instructor emphasized the idea that applications should communicate through Services rather than hardcoded Pod IPs.
For example:
```text
Frontend
   ↓
Backend Service
   ↓
Backend Pods
```


And:
```text
Backend
   ↓
Database Service
   ↓
Database Pods
```


This makes the architecture more resilient because Pods can be recreated without requiring every application configuration to be manually updated.

### 18. Replicas

A replica means another running copy of a workload.
For example:
Replicas = 3

Pod 1
Pod 2
Pod 3

If the application requires more capacity, the number of replicas can be increased.
For example:
Replicas = 5

Pod 1
Pod 2
Pod 3
Pod 4
Pod 5

The instructor also emphasized that manually managing each Pod is not the preferred approach.
That leads directly to Deployments.

### 19. Deployment

A Deployment manages application Pods and their desired state.
Instead of manually creating:
Pod 1
Pod 2
Pod 3

you can define a Deployment that says:
Desired replicas = 3

Kubernetes then works to maintain that state.
Conceptually:
Deployment
```text
     |
     ↓
ReplicaSet
     |
     +-- Pod 1
     +-- Pod 2
     +-- Pod 3
```


The instructor demonstrated this behavior practically.

### 20. Kubernetes Self-Healing

This was one of the most important practical demonstrations.
Suppose a Deployment has three replicas:
Deployment
|
+-- Pod 1
+-- Pod 2
+-- Pod 3

If Pod 2 is manually deleted:
Deployment
|
+-- Pod 1
+-- Pod 2 ❌
+-- Pod 3

Kubernetes notices that the desired state is:
3 Pods

but the current state is:
2 Pods

So it creates a replacement:
Deployment
|
+-- Pod 1
+-- Pod 3
+-- Pod 4

This demonstrates Kubernetes' self-healing behavior.

### 21. Scaling Deployments

The instructor demonstrated changing the number of replicas from one to three.
Conceptually:
Before:
replicas: 1

After:
replicas: 3

The result:
Deployment
|
+-- Pod 1
+-- Pod 2
+-- Pod 3

The Deployment manages the Pods automatically.
This is much more practical than manually creating three individual Pods.

### 22. Deployment vs Individual Pods

The instructor made an important practical distinction.
Individual Pod
You manage the Pod directly.
Pod

If the Pod is deleted, there is no Deployment automatically maintaining it.
Deployment
The Deployment maintains the desired number of Pods.
Deployment
|
+-- Pod
+-- Pod
+-- Pod

If one disappears, Kubernetes works to replace it.
Therefore, for normal stateless application workloads, a Deployment is generally preferred over manually managing individual Pods.

### 23. kubectl create vs kubectl apply

The instructor explicitly discussed this as an interview question.
The session's simplified teaching was:
```bash
kubectl create
```

Commonly used when creating a resource for the first time.
```bash
kubectl create -f file.yaml
```


```bash
kubectl apply
```

Used to apply configuration and is especially useful when you want to update an existing resource declaratively.
```bash
kubectl apply -f file.yaml
```


The instructor's practical recommendation was that YAML + apply is generally more convenient for maintaining and modifying Kubernetes configuration.

### 24. Why YAML Is Preferred for Kubernetes Configuration

The instructor explained that very long CLI commands can become difficult to remember and customize.
For example, a large cluster-creation or resource command can become unwieldy.
A YAML manifest provides a reusable template:
```text
YAML
 ↓
Version-controlled
 ↓
Reusable
 ↓
Modifiable
 ↓
kubectl apply
```


This makes YAML particularly useful for:
Version control
CI/CD
Reproducible deployments
Configuration management
Team collaboration

### 25. Important Interview Perspective

The instructor repeatedly emphasized that students should be able to write and explain Kubernetes YAML during interviews.
The goal is not to memorize every Kubernetes property.
You should understand the important structure:
```text
apiVersion
    ↓
kind
    ↓
metadata
    ↓
spec
    ↓
containers
    ↓
image
    ↓
resources
```


You should also understand how changing:
replicas: 1

to:
replicas: 3

changes the desired application capacity.

### 26. Most Important Topics from Day 3

If you are preparing for a DevOps/Kubernetes interview, I would prioritize these topics from this session:
🔥 1. Namespace
Understand why namespaces are used and how they help separate environments/resources.
🔥 2. Pod vs Deployment
Know why production workloads are normally managed through Deployments rather than manually created Pods.
🔥 3. Service vs Pod IP
This is extremely important.
Remember:
Pod IP can change. Service provides a stable endpoint.
🔥 4. Deployment Self-Healing
Be able to explain exactly what happens when a Pod managed by a Deployment is deleted.
🔥 5. Replicas
Understand how replicas provide multiple instances of the same workload.
🔥 6. YAML Structure
Know:
apiVersion:
kind:
metadata:
spec:

and be able to explain each section.
🔥 7. kubectl create vs kubectl apply
This was explicitly discussed as an interview question.
🔥 8. Node Pools and Scaling
Understand the difference between:
Node scaling
Pod/replica scaling
Cluster autoscaling
🔥 9. Resource Requests and Limits
Understand why CPU and memory requests/limits are configured and how they relate to scheduling and resource consumption.
🔥 10. IAM and Conditional Access
Know how cloud IAM can provide access to projects/resources and how temporary/conditional permissions can be configured.

### 27. Day 3 Practical Flow

The whole session can be summarized as:
```text
GKE Cluster
     ↓
Node Pool
     ↓
Nodes
     ↓
Namespace
     ↓
Deployment
     ↓
Replicas
     ↓
Pods
     ↓
Containers
     ↓
Service
     ↓
Stable Application Communication
```


And the configuration flow is:
```text
Kubernetes YAML
      ↓
kubectl create/apply
      ↓
Kubernetes API
      ↓
Resource Created/Updated
      ↓
Deployment
      ↓
Pods
```



### 28. Final Session Takeaway

Kubernetes Day 3 was primarily about moving from Kubernetes concepts into actual resource configuration and workload management.
The most important progression was:
Create/configure a GKE cluster → understand node pools and scaling → create YAML manifests → create namespaces → deploy Pods → expose/communicate through Services → manage Pods using Deployments → configure replicas → observe Kubernetes self-healing.
The session also established a very important production principle:
Do not build an application architecture around individual Pod IP addresses. Pods are replaceable and their IPs can change. Use Kubernetes Services as the stable communication layer.
And the other major practical principle was:
Do not manually manage individual application Pods when a Deployment can manage the desired number of replicas and automatically replace failed Pods.
For interview preparation, the strongest areas from this session are Namespaces, YAML structure, Pod vs Deployment, Services and Pod IPs, replicas, self-healing, kubectl create vs kubectl apply, node pools, scaling, resource requests/limits, and IAM-based access.


Kubernetes Day 3: 10 Interview Questions & Answers
These questions are based on the Day 3 session topics, especially YAML, Namespaces, Pods, Deployments, Services, replicas, node pools, scaling, IAM, and kubectl.

### ❓ Q1: What is a Kubernetes Namespace and why is it used?

**💡 Answer:**

A Namespace provides a logical way to organize and isolate Kubernetes resources within the same cluster.
For example, a company can have:
```text
Kubernetes Cluster
├── dev
├── qa
├── staging
└── production
```


Resources such as Pods, Deployments and Services can be separated by namespace.
We can create a namespace using:
```bash
kubectl create namespace dev
```


And list namespaces using:
```bash
kubectl get ns
```


Interview point: A Namespace provides logical isolation and organization. It does not automatically provide complete security isolation.

### ❓ Q2: What are the main sections of a Kubernetes YAML file?

**💡 Answer:**

A typical Kubernetes manifest contains:
apiVersion:
kind:
metadata:
spec:

apiVersion
Specifies the Kubernetes API version used by the resource.
kind
Defines the resource type, such as:
Pod
Deployment
Service

metadata
Contains information such as:
Name
Labels
Namespace
spec
Defines the desired configuration of the resource.
For a Pod, this can include:
Containers
Image
Ports
Resources
The important interview point is to understand what each section represents rather than simply memorizing YAML.

### ❓ Q3: What is the difference between a Pod and a Deployment?

**💡 Answer:**

A Pod is the basic execution unit in Kubernetes and contains one or more containers.
A Deployment manages application Pods and maintains the desired number of replicas.
For example:
Deployment
|
+-- Pod 1
+-- Pod 2
+-- Pod 3

If one of these Pods is deleted, the Deployment works to create a replacement.
Therefore:
Pod = workload execution unit
Deployment = manages and maintains Pods
For normal stateless applications, we generally use Deployments rather than manually managing individual Pods.

### ❓ Q4: Why should applications not directly depend on Pod IP addresses?

**💡 Answer:**

Pod IP addresses are not guaranteed to remain permanent.
If a Pod is deleted and Kubernetes creates a replacement, the replacement Pod can receive a different IP address.
For example:
Old Pod
```text
10.0.0.10
   ↓
Pod deleted
   ↓
New Pod
10.0.0.25
```


If another application directly uses 10.0.0.10, communication can fail.
Instead, Kubernetes Services provide a stable endpoint:
```text
Application
     ↓
Service
     ↓
Pod(s)
```


This allows Pods to be replaced without requiring the client application to know their individual IP addresses.

5. What is a Kubernetes Service?
**💡 Answer:**

A Service provides a stable network endpoint for accessing a group of Pods.
For example:
```text
Frontend
   ↓
Backend Service
   ↓
Backend Pods
```


The Service selects the appropriate Pods and provides a stable way to communicate with them.
This is particularly important because Pod IP addresses can change.
Services can also be used for different types of access, such as:
Internal communication
Node-level exposure
External/cloud load balancing

6. What is a replica in Kubernetes?
**💡 Answer:**

A replica is an additional running instance of an application workload.
If a Deployment specifies:
replicas: 3

Kubernetes attempts to maintain three Pods:
Deployment
|
+-- Pod 1
+-- Pod 2
+-- Pod 3

If one Pod fails or is deleted, the Deployment works to create another Pod so that the desired number remains three.
Replicas therefore provide availability and scaling for the workload.

7. What happens when a Pod managed by a Deployment is deleted?
**💡 Answer:**

The Deployment has a desired state.
For example:
Desired replicas = 3

If one Pod is deleted:
Current replicas = 2

Kubernetes detects that the current state doesn't match the desired state.
It then creates another Pod:
Pod 1
Pod 2
Pod 3 ← replacement

This demonstrates Kubernetes' self-healing behavior.

8. What is the difference between kubectl create and kubectl apply?
**💡 Answer:**

```bash
kubectl create is commonly used to create a resource.
```

For example:
```bash
kubectl create -f pod.yaml
```


```bash
kubectl apply is used to apply a configuration and is particularly useful when maintaining resources through YAML.
kubectl apply -f deployment.yaml
```


The practical advantage of YAML with apply is that the configuration can be:
Stored in Git
Reviewed
Modified
Reused
Applied repeatedly
This makes declarative configuration very useful for DevOps and CI/CD workflows.

9. What is a node pool?
**💡 Answer:**

A node pool is a group of worker nodes managed together with a particular configuration.
For example:
Cluster
|
+-- General Node Pool
|      +-- Node 1
|      +-- Node 2
|
+-- High-CPU Node Pool
+-- Node 1
+-- Node 2

Different node pools can be configured for different workload requirements.
Node pools are also important for node scaling and cluster capacity management.

10. What are resource requests and limits in Kubernetes?
**💡 Answer:**

Resource requests and limits define how much CPU and memory a container requires and is allowed to consume.
For example:
resources:
requests:
cpu: "250m"
memory: "256Mi"
limits:
cpu: "500m"
memory: "512Mi"

Request
Represents the amount of resource the workload requests and is important for scheduling.
Limit
Defines the maximum resource consumption configured for the container.
These settings help Kubernetes make better scheduling decisions and prevent workloads from consuming unlimited resources.

10 Scenario-Based Kubernetes Interview Questions
### ❓ Q1: Scenario: You have a Deployment with 3 replicas. One Pod is manually deleted. What happens?

**💡 Answer:**

The Deployment continuously tries to maintain the desired state.
Initially:
Desired = 3
Current = 3

After deleting one Pod:
Desired = 3
Current = 2

Kubernetes detects the difference and creates a replacement.
Pod 1
Pod 2
Pod 3 ← New Pod

This is an example of Kubernetes self-healing.

### ❓ Q2: Scenario: Your backend application is configured to connect directly to 10.0.0.15, which is the IP of a Pod. The connection suddenly stops working. What could have happened?

**💡 Answer:**

The Pod may have been deleted and recreated.
The new Pod could have a different IP:
```text
Old Pod → 10.0.0.15
     ↓
Pod deleted
     ↓
New Pod → 10.0.0.27
```


The backend is still trying to contact the old IP.
The correct architecture is:
```text
Backend
   ↓
Backend Service
   ↓
Backend Pod(s)
```


The Service provides a stable endpoint while Pods can change.

3. Scenario: Your organization wants separate environments for Development, QA and Production, but doesn't want to create three Kubernetes clusters. What can you use?
**💡 Answer:**

Namespaces can be used to logically separate the environments:
Cluster
|
+-- dev
+-- qa
+-- production

For example:
```bash
kubectl create namespace dev
kubectl create namespace qa
kubectl create namespace production
```


Resources can then be deployed into their respective namespaces.
However, the organization should still evaluate whether separate clusters are required for stronger isolation, security or operational reasons.

4. Scenario: You changed replicas: 1 to replicas: 5 in your Deployment YAML and ran kubectl apply. What should happen?
**💡 Answer:**

Kubernetes should update the Deployment's desired state from one replica to five.
Before:
Deployment
|
+-- Pod 1

After:
Deployment
|
+-- Pod 1
+-- Pod 2
+-- Pod 3
+-- Pod 4
+-- Pod 5

Assuming sufficient cluster resources are available, Kubernetes will create the additional Pods.

5. Scenario: You created a Deployment, but the new Pods remain Pending. What would you investigate?
**💡 Answer:**

I would first check:
```bash
kubectl get pods
```


Then:
```bash
kubectl describe pod <pod-name>
```


I would inspect the Events and determine whether the problem is related to:
Insufficient CPU
Insufficient memory
Node availability
Scheduling constraints
Resource requests
Node capacity
I would also inspect the nodes:
```bash
kubectl get nodes
kubectl describe node <node-name>
```


If the cluster doesn't have sufficient capacity, I may need to increase the node pool size or enable/use cluster autoscaling.

6. Scenario: Your application has 10 replicas, but all of them are running on the same node. Is that necessarily a problem?
**💡 Answer:**

It can be a reliability concern.
If all replicas are concentrated on one node and that node fails, multiple application instances could become unavailable simultaneously.
The appropriate scheduling and workload-distribution strategy depends on the application's requirements.
In a production environment, we would consider distributing workloads across multiple nodes/zones using appropriate Kubernetes scheduling mechanisms.
The important principle is:
Scaling the number of Pods does not automatically mean the Pods are distributed exactly how you want.

7. Scenario: A developer asks you to give temporary access to a Google Cloud project so they can troubleshoot a Kubernetes issue. What approach would you use?
**💡 Answer:**

I would use IAM to grant the required role rather than sharing credentials.
If supported by the organization's access policy, I would use conditional/temporary access so that the permission automatically expires.
Conceptually:
```text
Developer
    ↓
IAM Role
    ↓
Temporary Condition
    ↓
Kubernetes/Cloud Access
    ↓
Expiration
```


This follows the principle of giving users the required access for the required period rather than permanent excessive permissions.

8. Scenario: Your team has a CPU-intensive application and a memory-intensive application. How could node pools help?
**💡 Answer:**

We could create separate node pools with different machine configurations.
For example:
Cluster
|
+-- CPU-Optimized Pool
|      +-- Nodes
|
+-- Memory-Optimized Pool
+-- Nodes

The workloads can then be scheduled appropriately using Kubernetes scheduling mechanisms.
This allows infrastructure to match workload requirements instead of putting every workload onto identical nodes.

9. Scenario: A junior engineer manually creates five Pods for an application instead of creating a Deployment. Would you recommend this approach?
**💡 Answer:**

Generally, no for a normal stateless application.
Manually created Pods do not provide the same desired-state management offered by a Deployment.
Instead, I would create a Deployment specifying:
spec:
replicas: 5

The Deployment will manage the Pods and maintain the desired number.
If one Pod is deleted:
```text
5 replicas desired
        ↓
4 running
        ↓
Deployment detects difference
        ↓
New Pod created
        ↓
5 running
```


This provides self-healing and easier scaling.

### 📌 10. Scenario: You have created a Kubernetes YAML file, but another engineer says, "Why don't we just use one huge kubectl command instead?" How would you respond?

**💡 Answer:**

For simple one-time operations, CLI commands can be convenient.
However, YAML manifests are generally more practical for maintaining Kubernetes configurations because they can be:
Stored in Git
Version-controlled
Reviewed through pull requests
Reused
Modified
Applied consistently across environments
Integrated into CI/CD
For example:
```bash
kubectl apply -f deployment.yaml
```


This gives the team a declarative configuration that can be maintained as code.
The key DevOps principle is:
Infrastructure and application configuration should be reproducible and version-controlled wherever practical.

Quick Interview Revision Sheet
Topic
One-Line Answer
Namespace
Logical isolation and organization of Kubernetes resources
Pod
Smallest deployable execution unit containing one or more containers
Deployment
Manages Pods and maintains desired replicas
Replica
Additional instance of a workload
Service
Stable network endpoint for accessing Pods
Pod IP
Can change when Pods are recreated
Node Pool
Group of similarly configured worker nodes
YAML
Declarative configuration for Kubernetes resources
```bash
kubectl create
```

Commonly used to create resources
```bash
kubectl apply
```

Applies/updates declarative configuration
Resource Request
Resource amount used when considering scheduling
Resource Limit
Maximum configured resource consumption
IAM
Controls access to cloud resources
Self-Healing
Kubernetes works to restore the desired workload state
Cluster Autoscaling
Adjusts node capacity based on workload requirements




---
