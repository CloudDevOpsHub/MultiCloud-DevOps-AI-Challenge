# 🚀 Day-4 of Kubernetes Batch-44 : Kubernetes Production Troubleshooting & Advanced Scheduling

[![Module: Kubernetes Orchestration](https://img.shields.io/badge/Module-Kubernetes-326CE5?style=for-the-badge&logo=kubernetes)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 13th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-13th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


### 1. Full Session Overview

The session started with a detailed comparison between:
Docker Compose
Docker Swarm
Kubernetes
The instructor explained that Docker Compose is primarily useful for simple/local environments and smaller deployments, while Docker Swarm provides basic orchestration capabilities. Kubernetes was presented as the more advanced orchestration platform for large-scale, production and enterprise environments.
The comparison focused on:
Single-host vs multi-host environments
Manual vs automated management
Manual scaling vs autoscaling
High availability
Self-healing
Networking
Monitoring
Rolling updates
Ecosystem and integrations
Multi-node/multi-region environments
The instructor emphasized that Kubernetes provides much more automation and a broader ecosystem.
The discussion then moved toward Kubernetes networking and storage concepts, including CNI and persistent storage.

Docker vs Kubernetes
The instructor explained that Docker/Compose environments can require more manual management, whereas Kubernetes provides mechanisms for:
Scheduling
Automation
Scaling
Self-healing
Rolling updates
Networking
Monitoring
Multi-node deployments
The session also discussed the use cases:
```text
Docker Compose
    ↓
Local / Simple Applications
```


```text
Docker Swarm
    ↓
Basic Container Orchestration
```


```text
Kubernetes
    ↓
```

Enterprise / Production / Large-Scale Orchestration

The instructor specifically highlighted Kubernetes' ecosystem and its ability to integrate with many other tools.

### 2. Docker Networking Practical

A practical Docker networking demonstration was performed.
The instructor created a custom Docker bridge network and then launched multiple containers into that network.
The basic concept demonstrated was:
Custom Bridge Network
|
+----+----+
|         |
Container 1  Container 2
|         |
+----+----+
|
Communication

The containers could communicate with each other because they were connected to the same bridge network.
## 💻 Commands around:

```bash
docker network ls
```


and Docker container execution were discussed.
The instructor used this demonstration to explain the purpose of a bridge network.

### 3. Docker Image and GCR Workflow

A major practical section involved pushing a Docker image to Google Container Registry (GCR).
The workflow discussed was:
```text
Docker Image
     ↓
GCloud Authentication
     ↓
Docker Authentication
     ↓
Tag Image
     ↓
Push Image
     ↓
GCR
```


The instructor demonstrated:
Creating a Google Cloud VM
Installing Docker
Authenticating with GCloud
Setting the GCP project
Configuring Docker authentication
Tagging a Docker image
Checking available images
Pushing the image to GCR
The instructor emphasized that students should understand the commands instead of blindly copying them.

### 4. Google Cloud VM Setup

The instructor created an Ubuntu-based VM in Google Cloud for the practical.
The demonstrated setup included:
Ubuntu
LTS version
Approximately 30 GB disk
Network access
Docker installation
Docker was installed using commands around:
```bash
sudo apt-get update
sudo apt install docker.io -y
```


The purpose was to provide a machine from which Docker and GCloud commands could be executed.

### 5. GCloud Authentication from a VM

One of the practical objectives was to demonstrate that authentication does not have to happen only from Cloud Shell.
The instructor used:
```bash
gcloud auth login
```


from the VM.
The process involved:
Running gcloud auth login
Receiving an authentication URL
Opening the URL in a browser
Selecting the appropriate Google account
Granting permissions
Completing verification
Returning to the VM
Selecting/configuring the correct GCP project
This demonstrated how a VM can be authenticated to interact with Google Cloud resources.

### 6. Configuring Docker Authentication for GCR

After authenticating GCloud, the instructor configured Docker to authenticate with the Google Container Registry.
The general workflow discussed was:
```text
gcloud authentication
       ↓
Configure Docker
       ↓
Docker can communicate with GCR
```


The session emphasized that authentication and Docker registry access are separate steps that need to be configured properly.

### 7. Docker Image Tagging

Before pushing an image to GCR, the instructor explained that the image needs to be tagged with the appropriate registry/repository path.
Conceptually:
```text
Local Image
     ↓
docker tag
     ↓
Registry-specific Image Tag
     ↓
docker push
```


The instructor demonstrated checking images with:
```bash
docker images
```


and then tagging the appropriate image.

### 8. Docker Push

Once the image was authenticated and tagged, it could be pushed to the registry.
The command discussed was:
```bash
docker push <image>
```


The complete flow was:
```text
Docker Image
     ↓
docker tag
     ↓
GCR image path
     ↓
docker push
     ↓
Container Registry
```


This was important because the Kubernetes cluster needs access to a registry from which it can pull application images.

### 9. Kubernetes Troubleshooting

The second major portion of the session focused on real-world Kubernetes troubleshooting.
The instructor repeatedly emphasized that Kubernetes troubleshooting should not be based on guessing.
A basic troubleshooting process discussed was:
```text
Problem
  ↓
kubectl get
  ↓
kubectl describe
  ↓
Check Events
  ↓
kubectl logs
  ↓
Identify Root Cause
  ↓
Fix Configuration/Application/Infrastructure
```


The instructor specifically connected troubleshooting with:
Pod status
Events
Logs
CPU
Memory
Images
Dockerfile
Application behavior
Scheduling
Networking

### 10. kubectl describe

```bash
kubectl describe was repeatedly emphasized as one of the most useful troubleshooting commands.
```

For example:
```bash
kubectl describe pod <pod-name>
```


The instructor explained that the output contains useful information and especially Events.
Events can reveal problems such as:
Image pull failures
Scheduling failures
Resource shortages
Configuration problems
Container failures
A DevOps engineer should therefore read the Events instead of randomly restarting everything. Humanity has suffered enough from "restart and pray" as a troubleshooting methodology.

### 11. Application Logs

The instructor explained that application logs are important when diagnosing failures.
The troubleshooting process discussed was:
```text
Pod Running?
     ↓
Yes
     ↓
Check Logs
     ↓
Application Error?
     ↓
Inform/Fix Application
```


If the infrastructure is healthy but the application itself is generating errors, the problem may need to be investigated by the development team.
This introduced the important DevOps principle of distinguishing between:
Infrastructure problem
Container problem
Application problem
Configuration problem

### 12. Image Pull Errors

The session discussed image pull errors as a real-world Kubernetes issue.
Possible reasons discussed include:
Incorrect image name
Incorrect image tag
Registry issue
Authentication issue
Image not available
Incorrect configuration
The instructor explained that the first step should be checking:
```bash
kubectl describe pod <pod-name>
```


and examining the Events.

### 13. Liveness Probe

One of the most important topics in the session was Liveness Probe.
A liveness probe checks whether the application/container is still alive and functioning sufficiently to remain running.
Conceptually:
```text
Kubernetes
    ↓
Liveness Probe
    ↓
Is application alive?
    ↓
Yes → Continue
No  → Kubernetes can restart container
```


The instructor described it as checking whether the application/container/Pod is alive.

### 14. Readiness Probe

The instructor compared Readiness Probe with Liveness Probe.
A Readiness Probe determines whether the application is ready to receive traffic.
For example:
```text
Application Started
       ↓
Still initializing
       ↓
Readiness = FALSE
       ↓
No traffic
       ↓
Application becomes ready
       ↓
Readiness = TRUE
       ↓
Traffic allowed
```


The instructor gave the important conceptual distinction:
Liveness asks: "Is the application alive?"
Readiness asks: "Is the application ready to take traffic?"
This is one of the most important interview concepts from the session.

### 15. Startup Time and Probe Configuration

The session discussed an important practical issue with probes.
Some applications take time to start.
For example:
```text
Application starts
      ↓
30 seconds initialization
      ↓
Application ready
```


If a probe is configured too aggressively, Kubernetes may assume that the application is unhealthy before it has finished starting.
Therefore, probe timing needs to be configured according to the actual application's startup behavior.
The instructor emphasized that students should understand that real applications may need an appropriate waiting period before probes are evaluated aggressively.

### 16. Kubernetes Service as a Best Practice

The instructor discussed the importance of using a Service when exposing an application.
The idea was that putting all communication directly around individual Pods is not a good approach because Pods can change.
Instead:
```text
Client
  ↓
Service
  ↓
Pod
```


The Service provides a stable communication layer.
The instructor described Service usage as a practical best practice when working with Kubernetes applications.

### 17. CrashLoopBackOff

The session specifically discussed CrashLoopBackOff.
This occurs when a container repeatedly starts and then crashes, causing Kubernetes to repeatedly restart it with increasing delays.
The conceptual pattern is:
```text
Container starts
      ↓
Container crashes
      ↓
Restart
      ↓
Container crashes
      ↓
Restart
      ↓
CrashLoopBackOff
```


The instructor discussed several possible causes:
Application failure
Incorrect configuration
Incorrect Dockerfile
Incorrect image
Insufficient CPU
Insufficient memory
Application startup issue
Incorrect environment/configuration

### 18. Troubleshooting CrashLoopBackOff

The instructor emphasized checking logs and Pod details.
A practical process is:
```text
CrashLoopBackOff
      ↓
kubectl describe pod
      ↓
Check Events
      ↓
kubectl logs
      ↓
Check application
      ↓
Check CPU/Memory
      ↓
Check image/Dockerfile
      ↓
Fix root cause
```


The instructor specifically warned against immediately increasing CPU or memory.
The correct approach is:
First determine whether the application actually requires more resources or whether the application/configuration itself is broken.
This is particularly important for cost optimization.

### 19. Resource Problems

The discussion connected repeated crashes with resource constraints.
For example:
```text
Application
    ↓
High Memory Usage
    ↓
Memory Limit Reached
    ↓
Container Failure
```


Similarly, insufficient CPU can affect application performance.
The instructor explained that increasing resources can be a solution when the workload genuinely requires them, but optimization should be considered first where appropriate.

### 20. Node Selector

The session introduced Node Selector.
Node Selector can be used to influence which node a Pod should run on based on node labels.
Conceptually:
Nodes

Node 1 → environment=dev
Node 2 → environment=prod

```text
Pod
 ↓
nodeSelector: environment=prod
 ↓
Node 2
```


This is useful when certain workloads need to run on particular types of nodes.

### 21. Node Affinity

The instructor then discussed Node Affinity.
Node Affinity provides more flexible control over Pod placement than a basic node selector.
The session described affinity as a more advanced scheduling mechanism.
Conceptually:
```text
Pod
 ↓
Node Affinity Rules
 ↓
Eligible Nodes
 ↓
Scheduler
 ↓
Selected Node
```


The discussion contrasted Node Selector and Node Affinity, with the latter providing more sophisticated placement rules.

### 22. Taints and Tolerations

This was another major scheduling topic.
The instructor explained the relationship as:
Taint is applied to a Node.
Toleration is configured for a Pod.
Conceptually:
```text
Node
 ↓
Taint
 ↓
"Do not schedule ordinary Pods here"
```


A Pod with a matching toleration can then be scheduled onto that node.
```text
Pod
 ↓
Toleration
 ↓
Allowed onto tainted Node
```



### 23. Real-World Use Case for Taints and Tolerations

The instructor gave the example of having several applications and wanting a particular application to run on a particular node.
For example:
```text
Node A
├── Application 1
├── Application 2
├── Application 3
└── Application 4
```


```text
Node B
└── Special Application
```


Taints and tolerations can be used to control which workloads are allowed onto Node B.
This is particularly useful when certain nodes are reserved for specific workloads.

### 24. Node Maintenance

The session also discussed a scenario where a node needs to be restarted or taken out of service while applications are already running on it.
The discussion connected this with scheduling rules and moving workloads to other nodes.
The key idea was:
```text
Node 1
  ↓
Maintenance
  ↓
Workloads need another suitable location
  ↓
Node 2 / Other eligible node
```


Scheduling rules must be designed carefully so they do not prevent Kubernetes from finding another suitable node.

### 25. Scheduling Conflicts

The instructor discussed situations where manually hard-coded scheduling rules can conflict with Kubernetes' automated scheduling behavior.
For example, if a workload is heavily restricted to one node and that node becomes unavailable, Kubernetes may not have another valid location to place the Pod.
This is why scheduling configurations should be designed carefully.
The important lesson:
Do not over-constrain Kubernetes scheduling unless there is a real requirement.

### 26. Ingress

The session introduced Ingress as another important networking topic.
The instructor explained that using a separate external LoadBalancer for every application can become expensive.
Instead, Ingress can help route traffic to different applications.
Conceptually:
```text
                Internet
                    ↓
                 Ingress
              /     |      \
             /      |       \
            ↓       ↓        ↓
        App 1     App 2     App 3
```



### 27. Path-Based / URL-Based Routing

The instructor used an example similar to a large e-commerce platform where different applications are accessed through different URLs or paths.
For example:
example.com/
example.com/grocery
example.com/delivery
example.com/products

Ingress can route different requests to different backend Services.
Conceptually:
```text
URL / Path
    ↓
Ingress
    ↓
Service
    ↓
Pod
```


This allows multiple applications to share an external entry point.

### 28. Why Ingress Can Reduce LoadBalancer Costs

The instructor discussed that external cloud LoadBalancers can be expensive when every application has its own one.
Instead:
Without Ingress

Internet
|
+-- LoadBalancer → App 1
+-- LoadBalancer → App 2
+-- LoadBalancer → App 3

With an Ingress-based architecture:
Internet
|
LoadBalancer / Ingress
|
+-- App 1
+-- App 2
+-- App 3

The session presented this as a practical approach for handling multiple applications and routing traffic.

### 29. CNI

The instructor introduced CNI, referring to the Container Network Interface.
The discussion connected CNI with Kubernetes/container networking.
The important takeaway from the session was:
CNI is related to how networking is provided and configured for containers and Kubernetes workloads.
The instructor intentionally kept the explanation simple rather than diving deeply into individual CNI implementations.

### 30. Persistent Storage

The session also touched on persistent storage.
The instructor explained the difference between temporary container storage and persistent storage.
The basic idea was:
```text
Container
    ↓
Container deleted
    ↓
Temporary data may disappear
```


Persistent storage is intended to survive workload/container replacement.
The session mentioned persistent storage concepts and later connected them with questions about PV and PVC.

### 31. Rolling Updates

Rolling updates were discussed as a way to update applications gradually instead of taking everything down simultaneously.
For example:
Version 1
Pod 1
Pod 2
Pod 3

Instead of updating all three at once:
Pod 1 → Update
Pod 2 → Update
Pod 3 → Update

The update progresses gradually.
The goal is to minimize or avoid downtime.

### 32. Rolling Rollbacks

The instructor also discussed rolling rollback.
If a new deployment causes a problem, Kubernetes can return toward a previous working version.
Conceptually:
```text
Version 1
   ↓
Rolling Update
   ↓
Version 2
   ↓
Problem
   ↓
Rollback
   ↓
Version 1
```


This is extremely useful in production because not every deployment behaves nicely just because someone confidently clicked "Deploy."

### 33. Sidecar Containers

The session discussed Sidecar Containers.
A sidecar container runs alongside the primary application container within the same Pod.
Conceptually:
Pod
|
+-- Main Application Container
|
+-- Sidecar Container

The sidecar can support the primary application by handling responsibilities such as:
Logging
Monitoring
Networking-related support
Auxiliary processing
The instructor used logging/monitoring as examples.

### 34. Init Container vs Sidecar Container

The instructor asked students to distinguish between Init Containers and Sidecar Containers.
The key conceptual difference is:
Init Container
Runs initialization work before the main application container starts.
```text
Init Container
      ↓
Completes
      ↓
Application Container
```


Sidecar Container
Runs alongside the main application container.
Pod
|
+-- Application Container
|
+-- Sidecar Container

Therefore:
Init Container = initialization before application
Sidecar = supporting container running alongside application

### 35. RBAC

The session also revisited Role-Based Access Control.
The instructor gave a practical example where a team member might only need read access rather than full administrative access.
Conceptually:
```text
User
 ↓
Role
 ↓
Permissions
 ↓
Allowed Kubernetes Operations
```


RBAC can control what a user or service account is allowed to do.
For example:
Developer A → Read
Developer B → Read + Write
Admin → Full Access

The instructor highlighted RBAC as an important Kubernetes security feature.

### 36. Kubernetes Ecosystem

The instructor emphasized that Kubernetes has a large ecosystem.
Tools can be integrated with Kubernetes for:
Monitoring
Logging
Networking
Security
CI/CD
Storage
Service management
The session specifically mentioned tools such as Prometheus and Grafana during the monitoring discussion.
The main message was that Kubernetes itself does not need to perform every function. Its ecosystem allows specialized tools to integrate with it.

### 37. Production Troubleshooting Mindset

One of the strongest themes throughout Day 4 was real-world troubleshooting.
The instructor repeatedly pushed students to think in terms of:
```text
Symptom
   ↓
Evidence
   ↓
Root Cause
   ↓
Solution
```


For example:
Pod keeps restarting
Don't immediately increase CPU.
Instead:
```text
CrashLoopBackOff
      ↓
Describe
      ↓
Events
      ↓
Logs
      ↓
Application?
Dockerfile?
Image?
Memory?
CPU?
Configuration?
      ↓
Fix root cause
```


This is much closer to actual production work than memorizing 50 Kubernetes commands and hoping the interviewer doesn't notice.

### 38. Main Key Topics to Prioritize

From the entire session, these are the topics I would consider the most important for DevOps/Kubernetes interviews and real-world work.
🔥 1. Docker Compose vs Docker Swarm vs Kubernetes
You should be able to explain:
Feature
Docker Compose
Docker Swarm
Kubernetes
Main use
Simple/local multi-container apps
Basic orchestration
Advanced orchestration
Scaling
More manual
Basic
Advanced/automated
Self-healing
Limited
Available
Strong
Scheduling
Limited
Basic
Advanced
Ecosystem
Smaller
Smaller
Very large
Production scale
Limited
Possible
Strong
Networking
Basic
Distributed
Advanced
Rolling updates
Limited/basic
Supported
Strong
Multi-node
Limited
Yes
Yes
Multi-region
Not the focus
Limited
Supported architecture

The instructor's central point was that Kubernetes becomes more valuable as application scale and operational complexity increase.

39. 🔥 Kubernetes Troubleshooting
This is probably the most valuable section of Day 4.
When something breaks:
```bash
kubectl get pods
```


Then:
```bash
kubectl describe pod <pod-name>
```


Then:
```bash
kubectl logs <pod-name>
```


Check:
Events
Image
Container status
Restarts
CPU
Memory
Configuration
Scheduling
Application logs
Do not jump directly to changing random settings.

40. 🔥 CrashLoopBackOff
Understand this deeply.
```text
Container starts
      ↓
Application crashes
      ↓
Restart
      ↓
Crashes again
      ↓
Repeated restarts
      ↓
CrashLoopBackOff
```


Possible causes:
Application crash
Wrong configuration
Bad Docker image
Dockerfile problem
Insufficient memory
Insufficient CPU
Startup issue
Dependency failure
Troubleshooting:
```bash
kubectl describe pod <pod>
kubectl logs <pod>
```



41. 🔥 Liveness vs Readiness Probe
Memorize the concept, not merely the words.
Liveness
Is the application alive?
If it repeatedly fails the liveness check, Kubernetes may restart the container.
Readiness
Is the application ready to receive traffic?
If readiness fails, the application can remain running but should not receive normal traffic through the Service.
The classic interview example:
Application is alive
BUT
Application is still initializing

Result:
Liveness → PASS
Readiness → FAIL

This distinction is extremely important.

42. 🔥 Node Selector vs Node Affinity
Node Selector
Simple node placement based on labels.
```text
Pod
 ↓
nodeSelector
 ↓
Matching Node
```


Node Affinity
More expressive scheduling rules.
```text
Pod
 ↓
Affinity Rules
 ↓
Eligible Nodes
```


The session described Node Affinity as the more flexible/advanced mechanism.

43. 🔥 Taints and Tolerations
Remember the simplest rule:
Taint belongs to the Node.
Toleration belongs to the Pod.
Example:
```text
Node
 ↓
Taint
 ↓
Reject ordinary Pods
```


A Pod with a matching toleration can be scheduled there.
This is particularly useful for dedicated/special-purpose nodes.

44. 🔥 Ingress
Understand why Ingress exists.
Instead of:
Internet
|
+-- LB → App 1
+-- LB → App 2
+-- LB → App 3

you can have:
```text
Internet
    ↓
Ingress
    ↓
+---+---+---+
|   |   |   |
App1 App2 App3
```


Ingress can route requests using:
Host-based routing
Path-based routing
This can simplify external traffic management and reduce the need for separate external load-balancing endpoints.

45. 🔥 Rolling Update and Rollback
Rolling Update
Gradually replace the old application version.
v1 → v1/v2 → v2

Rollback
Return to a previous version if the new deployment causes problems.
```text
v1
 ↓
v2
 ↓
Problem
 ↓
Rollback
 ↓
v1
```


This is a critical production deployment concept.

46. 🔥 Sidecar vs Init Container
Init Container
```text
Init
 ↓
Finish
 ↓
Main Application
```


Used for initialization.
Sidecar
Main Application
+
Sidecar

Runs alongside the main application and can provide supporting functionality such as logging or monitoring.

47. 🔥 Docker Image → GCR → Kubernetes
The practical registry workflow from the session can be remembered as:
```text
Docker Image
     ↓
gcloud auth login
     ↓
Configure Project
     ↓
Configure Docker Authentication
     ↓
docker images
     ↓
docker tag
     ↓
docker push
     ↓
GCR
     ↓
Kubernetes pulls image
```


This connects the Docker and Kubernetes portions of the course.

48. 🔥 RBAC
RBAC controls who can perform which operations.
```text
User
 ↓
Role
 ↓
Permissions
```


Example:
Developer → Read Pods
DevOps → Read + Modify
Admin → Full Access

The principle is least privilege: give users only the access they actually need.



Kubernetes Day 4: 10 Interview Questions with Answers

### ❓ Q1: What is the difference between Docker Compose, Docker Swarm, and Kubernetes?

**💡 Answer:**

Docker Compose is mainly used to define and run multiple containers, commonly for local or relatively simple environments.
Docker Swarm provides container orchestration across multiple Docker nodes with features such as scaling and service management.
Kubernetes is a more advanced container orchestration platform designed for production environments and larger-scale workloads.
Feature
Docker Compose
Docker Swarm
Kubernetes
Primary use
Local/simple apps
Basic orchestration
Advanced orchestration
Multi-node
Limited
Yes
Yes
Scaling
Mostly manual
Supported
Advanced
Self-healing
Limited
Supported
Strong
Scheduling
Basic
Basic
Advanced
Ecosystem
Smaller
Smaller
Large

Interview point: Kubernetes provides a broader set of capabilities for production orchestration, scheduling, networking, scaling and automation.

### ❓ Q2: What is the difference between a Liveness Probe and a Readiness Probe?

**💡 Answer:**

A Liveness Probe checks whether the application is still alive and functioning.
If the liveness check repeatedly fails, Kubernetes can restart the container.
A Readiness Probe checks whether the application is ready to receive traffic.
For example, an application may be running but still initializing:
Application
|
+-- Running
|
+-- Still initializing

In this situation:
Liveness  → PASS
Readiness → FAIL

Once initialization completes:
Liveness  → PASS
Readiness → PASS

Simple way to remember:
Liveness = "Are you alive?"
Readiness = "Can you handle traffic?"

3. What is CrashLoopBackOff and how would you troubleshoot it?
**💡 Answer:**

CrashLoopBackOff occurs when a container repeatedly starts, crashes and gets restarted by Kubernetes.
The pattern is:
```text
Container starts
      ↓
Container crashes
      ↓
Restart
      ↓
Container crashes again
      ↓
CrashLoopBackOff
```


Possible causes include:
Application errors
Incorrect configuration
Incorrect Docker image
Dockerfile problems
Insufficient CPU
Insufficient memory
Startup problems
I would start with:
```bash
kubectl get pods
```


Then:
```bash
kubectl describe pod <pod-name>
```


And check the logs:
```bash
kubectl logs <pod-name>
```


I would inspect the Events and application logs before changing resources or restarting everything.

4. What is the purpose of kubectl describe during troubleshooting?
**💡 Answer:**

```bash
kubectl describe provides detailed information about a Kubernetes resource.
```

For a Pod:
```bash
kubectl describe pod <pod-name>
```


It provides information about:
Pod status
Containers
Events
Scheduling
Image-related problems
Restart information
Resource-related issues
The Events section is particularly useful for identifying why Kubernetes couldn't start or schedule a workload.
A typical troubleshooting sequence is:
```text
kubectl get
     ↓
kubectl describe
     ↓
Check Events
     ↓
kubectl logs
     ↓
Find root cause
```



5. What is the difference between Node Selector and Node Affinity?
**💡 Answer:**

Both are used to influence where Pods are scheduled.
Node Selector
Provides a relatively simple mechanism based on node labels.
For example:
Node:
environment=production

A Pod can specify that it should run on nodes having that label.
Node Affinity
Provides more flexible and advanced scheduling rules.
Conceptually:
```text
Pod
 ↓
Affinity Rules
 ↓
Eligible Nodes
 ↓
Scheduler
```


Therefore:
Node Selector = simpler placement requirement
Node Affinity = more flexible placement rules

6. What are taints and tolerations?
**💡 Answer:**

A taint is applied to a node and can prevent ordinary Pods from being scheduled there.
A toleration is configured on a Pod and allows that Pod to tolerate a matching taint.
The easiest way to remember it is:
Taint → Node
Toleration → Pod

For example:
```text
Special Node
    ↓
Taint
    ↓
Ordinary Pods rejected
```


A Pod with the appropriate toleration can be scheduled onto that node.
This is useful when dedicating nodes to specific workloads.

7. What is Kubernetes Ingress and why is it useful?
**💡 Answer:**

Ingress provides a mechanism for routing external HTTP/HTTPS traffic to Kubernetes Services.
For example:
```text
Internet
    ↓
Ingress
   /    \
  /      \
App 1    App 2
```


Ingress can route traffic using:
Host-based routing
Path-based routing
For example:
example.com/
example.com/products
example.com/grocery

Different paths can be routed to different Services.
It can also reduce the need for having a separate externally exposed LoadBalancer for every application, depending on the architecture.

8. What is a rolling update in Kubernetes?
**💡 Answer:**

A rolling update gradually replaces the old version of an application with a new version.
For example:
Version 1
Pod 1
Pod 2
Pod 3

During an update, Kubernetes gradually replaces the old Pods with the new version rather than immediately removing all old Pods.
Conceptually:
```text
v1
 ↓
v1 + v2
 ↓
v2
```


The goal is to update the application while minimizing downtime.
If the new version causes problems, a rollback can be performed.

9. What is the difference between an Init Container and a Sidecar Container?
**💡 Answer:**

An Init Container performs initialization tasks before the main application container starts.
```text
Init Container
      ↓
Completes
      ↓
Main Application
```


A Sidecar Container runs alongside the main application container within the same Pod.
Pod
|
+-- Main Application
|
+-- Sidecar

A sidecar can be used for supporting functionality such as:
Logging
Monitoring
Auxiliary processing
> [!TIP]
> **Interview shortcut:**

Init Container = runs before the application.
Sidecar = runs alongside the application.

### ❓ Q10: What is RBAC in Kubernetes?

**💡 Answer:**

RBAC stands for Role-Based Access Control.
It controls what users or service accounts are allowed to do within Kubernetes.
The basic model is:
```text
User
 ↓
Role
 ↓
Permissions
 ↓
Allowed Operations
```


For example:
Developer → Read Pods
DevOps    → Read + Modify
Admin     → Full Access

RBAC helps implement the least-privilege principle, where users receive only the permissions they actually require.

10 Scenario-Based Kubernetes Interview Questions with Answers
### ❓ Q1: Scenario: A Pod is showing CrashLoopBackOff. What steps would you take?

**💡 Answer:**

I would not immediately increase CPU or memory.
First:
```bash
kubectl get pods
```


Then:
```bash
kubectl describe pod <pod-name>
```


I would inspect the Events.
Next:
```bash
kubectl logs <pod-name>
```


I would investigate:
Application errors
Configuration
Docker image
Dockerfile
CPU
Memory
Startup behavior
The troubleshooting flow would be:
```text
CrashLoopBackOff
       ↓
Describe Pod
       ↓
Check Events
       ↓
Check Logs
       ↓
Find Root Cause
       ↓
Apply Correct Fix
```



2. Scenario: Your application takes 60 seconds to start, but Kubernetes restarts it after 10 seconds. What could be wrong?
**💡 Answer:**

The probe configuration may be too aggressive for the application's startup time.
The application needs enough time to initialize before Kubernetes starts treating it as unhealthy.
I would review:
Probe configuration
Initial delay
Probe frequency
Failure threshold
Startup behavior
The key lesson is:
Probe configuration must match the application's actual startup characteristics.
A slow-starting application should not be treated as failed simply because it needs more time to initialize.

3. Scenario: Your application is running but should not receive traffic until initialization is complete. Which probe would you use?
**💡 Answer:**

I would use a Readiness Probe.
The application can remain running while readiness is false:
```text
Application Running
       ↓
Still Initializing
       ↓
Readiness = FALSE
       ↓
No normal traffic
```


Once initialization finishes:
```text
Readiness = TRUE
       ↓
Traffic Allowed
```


A liveness probe is not the appropriate mechanism for determining whether the application is ready to receive traffic.

4. Scenario: You have a special GPU/high-performance node and want only specific workloads to run there. What Kubernetes features could you use?
**💡 Answer:**

I could use taints and tolerations, along with node labels and scheduling rules.
For example:
```text
Special Node
     ↓
Taint
     ↓
Only Pods with matching toleration
     ↓
Can run there
```


I could also use:
Node Selector
Node Affinity
depending on the exact scheduling requirement.
The purpose would be to prevent unrelated workloads from consuming the specialized node.

5. Scenario: A Pod is scheduled successfully but is running on the wrong type of node. How would you control its placement?
**💡 Answer:**

I would first label the appropriate nodes and then use a scheduling mechanism such as:
Node Selector
Node Affinity
For example:
Node
label:
environment=production

Then configure the Pod to prefer or require nodes matching the appropriate label.
If the requirement is stronger and the node should be reserved for specific workloads, I could also consider taints and tolerations.

6. Scenario: Your company has five applications and each application currently has its own external LoadBalancer. What Kubernetes feature could help simplify this architecture?
**💡 Answer:**

I would evaluate Ingress.
Instead of exposing every application separately:
```text
Internet
 ├── LB → App 1
 ├── LB → App 2
 ├── LB → App 3
 ├── LB → App 4
 └── LB → App 5
```


we could use an Ingress-based routing architecture:
```text
Internet
    ↓
Ingress
 ├── App 1
 ├── App 2
 ├── App 3
 ├── App 4
 └── App 5
```


Ingress can route requests based on hostnames or URL paths.
This can simplify traffic management and potentially reduce external load-balancing costs.

7. Scenario: You deployed a new application version, but users are reporting errors. What would you do if the previous version was working correctly?
**💡 Answer:**

I would investigate the new version using:
Pod status
Events
Application logs
Probe status
Configuration
Image
If the new release is confirmed to be responsible for the issue, I would use a rollback to return toward the previous known-working version.
Conceptually:
```text
v1 → v2
       ↓
    Errors
       ↓
   Rollback
       ↓
      v1
```


After restoring service, I would investigate the root cause of the failed deployment.

8. Scenario: A developer says, "The Pod is Running, so the application must be healthy." Would you agree?
**💡 Answer:**

Not necessarily.
A Pod being in the Running state does not automatically mean the application is ready to serve traffic or functioning correctly.
For example:
Pod = Running
Application = Still Initializing

The Readiness Probe could still be failing.
Similarly, application-level problems may exist even though the container itself is running.
I would check:
```bash
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
```


and examine readiness/liveness status.

9. Scenario: Your Docker image works locally, but Kubernetes cannot start the Pod because it cannot pull the image. What would you investigate?
**💡 Answer:**

I would investigate the image-pull process.
First:
```bash
kubectl describe pod <pod-name>
```


I would check Events for the exact image-pull error.
Then verify:
Image name
Image tag
Registry path
Whether the image actually exists
Registry authentication
Kubernetes access to the registry
The workflow discussed in the session was:
```text
Docker Image
     ↓
Tag Image
     ↓
Authenticate Registry
     ↓
docker push
     ↓
Container Registry
     ↓
Kubernetes pulls image
```


A common mistake is having the image locally but forgetting that the Kubernetes environment needs access to the registry containing that image.

### 📌 10. Scenario: A production node needs maintenance, but several applications are running on it. What should you consider before taking the node down?

**💡 Answer:**

I would first understand how the workloads are distributed and whether Kubernetes can schedule replacement Pods onto other suitable nodes.
I would check:
Number of replicas
Available nodes
Node affinity
Node selectors
Taints and tolerations
Resource capacity
Scheduling restrictions
A problem can occur if a workload is overly restricted to a single node.
For example:
```text
Application
    ↓
Only allowed on Node A
    ↓
Node A goes down
    ↓
No eligible node
    ↓
Pod cannot be scheduled
```


Therefore, scheduling rules should be designed carefully so that maintenance or node failure does not unnecessarily make workloads unavailable.


