# 🚀 Day-2 of Kubernetes Batch-44 : Kubernetes Cluster Administration & Hands-on Setup

[![Module: Kubernetes Orchestration](https://img.shields.io/badge/Module-Kubernetes-326CE5?style=for-the-badge&logo=kubernetes)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 11th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-11th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


### 1. Full Session Overview

The session began with some job/referral and assignment-related announcements. Students were encouraged to apply for relevant opportunities, share their email IDs for tracking, and properly communicate job referrals by including the job ID, role and relevant details rather than simply requesting a job.
After this, the instructor started Kubernetes Day 2 by asking students to explain the complete application-to-Kubernetes flow from Day 1.
The instructor then revisited the application lifecycle:
```text
Developer
   ↓
Source Code
   ↓
Central Repository
   ↓
CI
   ↓
Build / Test
   ↓
Artifact
   ↓
Docker Image
   ↓
Image Registry
   ↓
Kubernetes
```


The discussion covered artifacts, Dockerfiles, image registries such as Docker Hub/ECR/GCR, and how these images eventually become Kubernetes workloads.
The main practical portion began with creating a Google Kubernetes Engine (GKE) cluster. The instructor demonstrated cluster creation, region selection, node configuration, operating system selection, disk configuration, networking and automation options.
The class then moved into the Kubernetes architecture, particularly:
Control Plane
API Server
etcd
Scheduler
Controller Manager
Kubelet
kube-proxy
Worker Nodes
Pods
Services
A major focus was understanding the request flow:
```text
kubectl
   ↓
API Server
   ↓
etcd
   ↓
Scheduler
   ↓
Controller Manager
   ↓
Kubelet
   ↓
Pod / Container
```


The instructor then demonstrated actual Kubernetes commands such as:
```bash
kubectl get nodes
kubectl get pods
kubectl get svc
kubectl logs
kubectl describe pod
```

kubectl cluster-info
```bash
kubectl config get-contexts
kubectl describe node
```


A practical NGINX deployment was created, followed by exposing it using a Kubernetes Service.
The session then explained the major Service types discussed:
LoadBalancer
NodePort
ClusterIP
The instructor demonstrated how a Pod that is running internally can be made accessible externally using a Service and a LoadBalancer.
The class also deliberately created an image-pull failure to demonstrate troubleshooting. Students examined the Pod events and discovered that the image could not be pulled because of an incorrect image path/tag or access issue. This was one of the most useful practical portions of the session.
Later, the instructor deployed Jenkins inside Kubernetes, exposed Jenkins through a Service, accessed the Jenkins Pod using kubectl exec, retrieved the initial Jenkins password, and opened Jenkins through the exposed port.
The session then moved into more advanced conceptual discussions:
Node scaling
Pod scaling
Horizontal and vertical scaling
Node pools
Labels and scheduling
Pod failure and rescheduling
Multiple containers
One-container/one-application best practice
Self-healing
RBAC
Kubernetes vs Docker
Kubernetes cost
Kubernetes UI vs CLI
Minikube/local Kubernetes
Docker Compose
Rancher
Production node pools
Control-plane architecture
The final part was largely Q&A and troubleshooting discussion, followed by instructions to execute the commands and delete the temporary cluster.

### 2. All Key Topics Covered

Kubernetes Architecture
Kubernetes cluster architecture
Control Plane
Worker Nodes
API Server
etcd
Scheduler
Controller Manager
Kubelet
kube-proxy
Kubernetes API communication
kubeconfig
GKE Cluster Creation
Google Kubernetes Engine
Standard cluster
Cluster name
Region
Zones
Node count
Node pool
Machine configuration
CPU
Memory
Disk
SSD
Container-Optimized OS
Networking
Auto-upgrade
Auto-repair
Load balancing
Cluster creation through Cloud Shell
Application Deployment Flow
Developer writes code
Central repository
CI
Compilation/build
Testing
Artifact generation
Dockerfile
Docker image
Image registry
Kubernetes deployment
Kubernetes Workloads
Pods
Containers
Workloads
NGINX Pod
Jenkins Pod
Container images
Image tags
Kubernetes Commands
kubectl
```bash
kubectl get nodes
kubectl get pods
kubectl get svc
kubectl logs
kubectl describe pod
kubectl describe node
```

kubectl cluster-info
```bash
kubectl config get-contexts
kubectl exec
```

kubectl run
kubectl expose
Kubernetes Services
Service concept
kube-proxy
ClusterIP
NodePort
LoadBalancer
Internal access
External access
External IP
Port mapping
Target port
Service exposure
Scheduling
Scheduler
Finding an appropriate node
Available resources
Labels
Node selection
Node pools
Scheduling workloads
Pod placement
Kubernetes State Management
etcd
Cluster state
Key-value storage
Desired/current state
API Server interaction with etcd
Troubleshooting
Pod Pending state
ImagePull errors
Incorrect image path
Incorrect image tag
Private registry access
Permission/access issues
Pod events
```bash
kubectl describe
```

Logs
Reading error messages
Troubleshooting systematically
Scaling
Node scaling
Pod scaling
Horizontal scaling
Vertical scaling
Node pools
Adding capacity
Scaling when nodes are full
High Availability / Resilience
Multiple nodes
Pod failure
Node failure
Workload movement/rescheduling
Self-healing concepts
Multiple control planes
Production architecture
Jenkins on Kubernetes
Deploying Jenkins Pod
Jenkins image
Exposing Jenkins
Port 8080
LoadBalancer
Accessing Jenkins
```bash
kubectl exec
```

Retrieving Jenkins initial password
Security and Access
RBAC
Role-based permissions
Read access
Write/edit access
Kubernetes user permissions
Access to private images
Other Topics Discussed
Kubernetes CLI vs UI
GCP Kubernetes UI
Minikube
Kubernetes on local machines
Kubernetes on AWS EC2
Docker Compose
Docker .dockerignore
Kubernetes cost
Rancher
Node pools
Production vs development environments
Cloud-managed Kubernetes

### 3. Main Key Topics Explained in Detail

3.1 Kubernetes Application Deployment Flow
This was one of the most important concepts carried over from Day 1.
The instructor started from the developer's code and followed it toward Kubernetes.
```text
Developer
   ↓
Source Code
   ↓
Git Repository
   ↓
CI
   ↓
Build & Test
   ↓
Artifact
   ↓
Dockerfile
   ↓
Docker Image
   ↓
Image Registry
   ↓
Kubernetes
   ↓
Pod
   ↓
Container
```


For example, if the application is Java, the build may generate an artifact such as a JAR or WAR. For other languages, the resulting artifact can be different.
The important point was that Kubernetes does not replace the software build process.
The application still needs to be built and packaged before Kubernetes can run its container image.

3.2 Docker Image and Container Registry
The instructor explained that after creating the Docker image, the image can be pushed to a centralized registry.
Examples discussed included:
Docker Hub
ECR
GCR
The flow becomes:
```text
Application
     ↓
Dockerfile
     ↓
Docker Build
     ↓
Docker Image
     ↓
Container Registry
     ↓
Kubernetes
```


This becomes especially important in production because Kubernetes nodes need access to the image.
The session also emphasized that DevOps engineers need to understand the commands used to push images to registries.

3.3 Creating a GKE Cluster
A significant practical section involved creating a Kubernetes cluster in Google Cloud.
The instructor demonstrated configuration including:
Cluster name
Region
Zones
Node pool
Node count
Machine type
CPU
Memory
Disk
Operating system
Networking
Auto-upgrade
Auto-repair
Load balancing
The instructor also explained why selecting a region/zone can affect the number of nodes created.
The important production concept is:
Cluster configuration should be selected according to application requirements rather than blindly copying a fixed configuration.

3.4 Container-Optimized OS
The session discussed COS, meaning Container-Optimized OS.
The instructor explained that Google Cloud provides a container-optimized operating system for Kubernetes workloads.
The transcript also discussed that other operating systems such as Ubuntu or Windows can be used depending on the requirements.
The key learning point from the session was:
Container-Optimized OS is designed specifically with container workloads in mind.

3.5 Kubernetes Control Plane
The instructor divided Kubernetes architecture into two major sections:
Kubernetes Cluster
|
+---+---+
|       |
Control   Worker
Plane     Nodes

The Control Plane is responsible for managing the cluster.
The session focused heavily on these Control Plane components:
API Server
etcd
Scheduler
Controller Manager
The instructor repeatedly emphasized that requests interact through the API Server.

3.6 API Server
The API Server was described as the central point through which Kubernetes requests are handled.
When you run:
```bash
kubectl get pods
```


the request does not simply jump directly to a worker node.
The conceptual flow discussed was:
```text
kubectl
   ↓
API Server
   ↓
Kubernetes Components
```


The API Server therefore acts as the main communication interface for Kubernetes operations.
This is one of the most important interview topics from the session.

3.7 etcd
The instructor described etcd as the component that maintains the state/information of the Kubernetes cluster.
It was discussed as a key-value-based store.
Conceptually:
Key → Value

The important information about cluster state is maintained there.
The instructor specifically stressed that students should understand etcd as the place where Kubernetes maintains cluster state rather than simply memorizing the word "database."

3.8 Scheduler
The Scheduler was explained using the example of creating an NGINX Pod.
When Kubernetes receives a request to create a workload, the Scheduler determines an appropriate node for that workload.
Conceptually:
```text
New Pod
   ↓
Scheduler
   ↓
Check available resources
   ↓
Select suitable node
```


The session emphasized that scheduling considers available space/resources and determines where the workload should run.

3.9 Controller Manager
The Controller Manager was explained as responsible for ensuring that the cluster moves toward the desired state.
The session repeatedly contrasted Scheduler and Controller Manager.
A simplified distinction from the class:
Scheduler:
Where should this workload run?
Controller Manager:
How do we ensure the desired state is maintained?
This distinction is extremely important for interviews.

3.10 Kubelet
Kubelet was described as an agent running on worker nodes.
The basic relationship discussed was:
```text
Control Plane
      ↓
API Server
      ↓
Kubelet
      ↓
Worker Node
      ↓
Pod
      ↓
Container
```


Kubelet communicates with the Kubernetes control plane and participates in managing workloads on its node.
The instructor described it as an agent available on every worker node.

3.11 kube-proxy
The session also introduced kube-proxy.
The instructor associated kube-proxy with networking and directing traffic toward the appropriate workload.
The important conceptual flow was:
```text
External/Internal Traffic
        ↓
Service
        ↓
kube-proxy / networking
        ↓
Pod
```


The class then connected this with Kubernetes Services.

3.12 Kubernetes Services
A Pod running inside Kubernetes does not automatically become publicly accessible.
The instructor demonstrated that a Service is required to expose the application.
The major Service types discussed were:
ClusterIP
Used for internal cluster communication.
```text
Service
   ↓
Pods
```


NodePort
Exposes the service through a node-level port.
```text
Node IP : Port
      ↓
Service
      ↓
Pod
```


LoadBalancer
Used when external/public access is required, particularly in cloud environments.
```text
Internet
   ↓
Load Balancer
   ↓
Service
   ↓
Pod
```


This distinction was repeatedly discussed and is one of the most important topics from the practical.

3.13 NGINX Deployment
The instructor used NGINX as the practical example.
The basic flow was:
```text
NGINX Image
     ↓
Pod
     ↓
Service
     ↓
LoadBalancer
     ↓
External IP
     ↓
Browser
```


Students were asked to create the NGINX workload and then expose it.
This demonstrated the difference between:
Running an application
and
Making an application accessible to users.
That distinction is fundamental in Kubernetes.

3.14 Kubernetes Troubleshooting Using describe
One of the strongest practical sections involved deliberately troubleshooting a failing Pod.
The instructor used:
```bash
kubectl describe pod <pod-name>
```


The output contains information such as:
Pod details
Node
IP
Container
Image
Image source
Start information
Restart information
Events
The instructor emphasized that Events are extremely useful when troubleshooting.

3.15 ImagePullBackOff / Image Pull Problems
A Pod was demonstrated in a pending/non-running state because Kubernetes could not pull the image.
The troubleshooting process was:
```text
Pod not running
      ↓
kubectl describe pod
      ↓
Check Events
      ↓
Image pull error
      ↓
Check image name/path/tag
      ↓
Check registry access
```


Potential causes discussed included:
Incorrect image path
Image not found
Incorrect tag
Private registry
Missing permissions/access
Incorrect registry configuration
This is one of the most valuable real-world troubleshooting lessons from the session.

3.16 kubectl logs
The session introduced:
```bash
kubectl logs <pod-name>
```


This is used to inspect application/container logs.
The instructor contrasted logs with describe.
A useful mental model from the session is:
```text
Application behavior/problem
        ↓
kubectl logs
```


while:
```text
Pod scheduling/configuration/problem
        ↓
kubectl describe pod
```


Both commands are important troubleshooting tools.

3.17 kubectl get
The class repeatedly used:
```bash
kubectl get nodes
kubectl get pods
kubectl get svc
```


The instructor explained that get is primarily used to fetch/list resources and their current information.
Examples:
```bash
kubectl get nodes
kubectl get pods
kubectl get services
```


The same general pattern can be used with many Kubernetes resources.

3.18 kubectl describe
The class demonstrated that describe can be used for detailed resource information.
For example:
```bash
kubectl describe pod nginx
```


and:
```bash
kubectl describe node <node-name>
```


The output can help identify:
Node assignment
Labels
IP information
Container details
Images
Events
Resource-related information
The instructor specifically emphasized using describe during interviews and troubleshooting.

3.19 kubectl cluster-info
The session introduced:
kubectl cluster-info

This provides information about the Kubernetes cluster and its control-plane-related endpoints.
The instructor used it to demonstrate how CLI commands provide information about the cluster.

3.20 kubeconfig and Contexts
The instructor explained that kubectl needs Kubernetes configuration information to communicate with a cluster.
The session discussed kubeconfig and:
```bash
kubectl config get-contexts
```


The idea is that if multiple clusters have been configured, their contexts can be listed and managed.
Conceptually:
```text
kubectl
   ↓
kubeconfig
   ↓
Selected Context
   ↓
Kubernetes Cluster
```



3.21 Scaling
The session discussed what happens when available node capacity becomes insufficient.
If nodes are full, there are broadly two approaches discussed:
Clean/remove unnecessary workloads/resources
Increase capacity by scaling
Scaling concepts mentioned included:
Node scaling
Pod scaling
Horizontal scaling
Vertical scaling
Node pools
The instructor connected scaling with real-world production requirements.

3.22 Node Pools
Node pools were discussed in the context of production Kubernetes environments.
A node pool can be understood as a group of nodes managed together with a particular configuration.
Conceptually:
Cluster
|
+-- Node Pool 1
|     +-- Node
|     +-- Node
|
+-- Node Pool 2
+-- Node
+-- Node

The discussion connected node pools with:
Capacity
Workload separation
Scaling
Different machine configurations
Production requirements

3.23 Labels and Scheduling
The class also discussed labels and controlling where workloads should run.
The concept was illustrated using the idea of assigning a workload to a particular type/location of node.
For example:
Node
|
+-- label: environment=production

A workload can then use node-related selection rules to influence placement.
The key lesson was:
Kubernetes scheduling can use node characteristics and labels rather than simply choosing a random machine.

3.24 Pod Failure and Self-Healing
The class discussed what happens when a Pod or node fails.
The instructor explained that Kubernetes can detect the problem and work toward restoring the desired workload state.
Conceptually:
```text
Pod Running
    ↓
Pod Failure
    ↓
Kubernetes detects difference
    ↓
Replacement/Rescheduling
    ↓
Workload restored
```


This is one of the major advantages of Kubernetes compared with manually running containers.

3.25 Jenkins Deployment on Kubernetes
A practical Jenkins deployment was performed.
The instructor used a Jenkins container image and created a Kubernetes workload.
The process was roughly:
```text
Jenkins Image
     ↓
Jenkins Pod
     ↓
Service
     ↓
Port 8080
     ↓
LoadBalancer
     ↓
External Access
```


Students were then asked to access Jenkins and retrieve the initial password.

3.26 kubectl exec
The session demonstrated entering the Jenkins Pod/container using kubectl exec.
The purpose was to access the running container and retrieve information from inside it.
The conceptual use is:
```text
kubectl exec
      ↓
Running Pod
      ↓
Container Shell
```


The instructor used this to retrieve the initial Jenkins password.

3.27 Port Mapping
The Jenkins deployment also demonstrated the relationship between:
Container port
Service port
Target port
External port
The instructor discussed Jenkins on port:
8080

The broader lesson was that the port exposed by the application must be correctly connected to the Kubernetes Service configuration.

3.28 Docker Compose vs Kubernetes
During Q&A, a student asked about Docker Compose.
The instructor explained that Docker Compose is useful when you want to create/manage multiple containers together.
For example:
Docker Compose
|
+-- Web Container
+-- Backend Container
+-- Database Container

The discussion positioned Docker Compose and Kubernetes as tools serving different levels of container orchestration.

3.29 RBAC
The class briefly discussed Role-Based Access Control (RBAC).
The basic idea was:
```text
User
  ↓
Role
  ↓
Permissions
```


For example, one user might have:
Read access
while another has:
Edit/write access
RBAC becomes important in production Kubernetes environments where not every engineer should have unrestricted access.

3.30 Kubernetes CLI vs UI
A student asked whether Kubernetes has its own UI.
The instructor demonstrated the GCP UI but strongly encouraged students to learn Kubernetes through the CLI.
The reason discussed was that Kubernetes commands are more portable across environments.
The important takeaway was:
Do not learn Kubernetes only through a cloud provider's UI. Learn the CLI and Kubernetes concepts themselves.

3.31 Minikube / Local Kubernetes
The class discussed whether Kubernetes can be installed locally.
The discussion mentioned tools such as Minikube for local learning and experimentation.
The broader point was that Kubernetes concepts and commands can be practiced outside the GCP UI.

3.32 Rancher
During the Q&A, Rancher was mentioned as a Kubernetes management platform/UI.
The discussion was related to organizations managing Kubernetes environments through an additional management interface, particularly in environments where Kubernetes clusters are managed internally.

3.33 Docker .dockerignore
The session also briefly covered .dockerignore.
The purpose discussed was to prevent unwanted files from being included in the Docker build context.
For example:
.dockerignore

can exclude:
Credentials
Unnecessary files
Local configuration
Large directories
Files not required during the build
The instructor connected this to avoiding unnecessary content during image creation.
Final Session Takeaway
Kubernetes Day 2 was primarily a transition from theory to practical Kubernetes administration.
The class did not simply introduce individual components. It demonstrated how they work together when a real application is deployed.
The central flow you should remember is:
Application → Docker Image → Registry → Kubernetes API Server → etcd → Scheduler → Node → Kubelet → Pod → Container → Service → User
And when something breaks:
kubectl get → kubectl describe → check Events → kubectl logs → inspect image/tag → verify registry access → check Service/networking.
The most valuable practical lessons from this session were Kubernetes architecture, GKE cluster creation, API Server/etcd/Scheduler/Controller Manager/Kubelet roles, Pods, Services, LoadBalancer vs NodePort vs ClusterIP, kubectl commands, image-pull troubleshooting, scaling, node pools, and deploying Jenkins/NGINX on Kubernetes.
The final Q&A also made an important career point: learn Kubernetes through the CLI and underlying concepts, not only through a cloud provider's graphical interface. The UI changes, clouds differ, and human beings apparently enjoy redesigning buttons. The Kubernetes concepts and commands are what transfer between environments.


10 Interview Questions with Answers
Based specifically on the Kubernetes Day 2 session, these questions focus on the architecture, commands, services, troubleshooting, scaling, and practical deployments covered in the class.
### ❓ Q1: What are the main components of the Kubernetes Control Plane?

**💡 Answer:**

The major Control Plane components discussed in the session are:
API Server: Handles Kubernetes API requests.
etcd: Stores the cluster state.
Scheduler: Determines which node should run a Pod.
Controller Manager: Continuously works toward maintaining the desired state.
The overall flow is:
```text
kubectl
   ↓
API Server
   ↓
etcd / Scheduler / Controller Manager
   ↓
Worker Node
```



2. What is the role of the Kubernetes API Server?
**💡 Answer:**

The API Server acts as the primary communication interface for the Kubernetes cluster.
When we execute a command such as:
```bash
kubectl get pods
```


kubectl communicates with the Kubernetes API Server.
The API Server then processes the request and interacts with the relevant Kubernetes components.
A good interview answer is:
The Kubernetes API Server is the central interface through which users, tools and Kubernetes components communicate with the cluster.

### ❓ Q3: What is etcd and why is it important?

**💡 Answer:**

etcd is a distributed key-value store used to maintain Kubernetes cluster state.
It stores important information about the cluster and its desired/current configuration.
Conceptually:
```text
Kubernetes
    ↓
API Server
    ↓
etcd
    ↓
Cluster State
```


It is important because Kubernetes needs persistent cluster state to understand what resources exist and what state they should maintain.

4. What is the difference between Scheduler and Controller Manager?
**💡 Answer:**

Scheduler
The Scheduler determines where a Pod should run.
It considers factors such as available node resources and scheduling requirements.
```text
Pod
 ↓
Scheduler
 ↓
Suitable Node
```


Controller Manager
The Controller Manager works to ensure that the actual cluster state moves toward the desired state.
For example, if a workload is expected to have a certain number of replicas, controllers work to maintain that desired state.
Simple interview answer:
Scheduler decides where a workload should run, while Controller Manager works to maintain the desired state of the cluster.

### ❓ Q5: What is Kubelet?

**💡 Answer:**

Kubelet is an agent that runs on Kubernetes worker nodes.
It communicates with the Kubernetes control plane and participates in managing the workloads running on its node.
The simplified flow is:
```text
Control Plane
     ↓
Kubelet
     ↓
Worker Node
     ↓
Pod
     ↓
Container
```



6. What is the difference between ClusterIP, NodePort and LoadBalancer?
**💡 Answer:**

Service
Purpose
ClusterIP
Internal communication within the cluster
NodePort
Exposes a service through a port on a node
LoadBalancer
Provides external access through a cloud load balancer

For example:
ClusterIP:
Pod ↔ Service ↔ Pod

NodePort:
Client → Node IP:Port → Service → Pod

LoadBalancer:
Internet → Load Balancer → Service → Pod

The session demonstrated the LoadBalancer approach while exposing NGINX and Jenkins.

7. How would you troubleshoot a Pod that is not running?
**💡 Answer:**

I would start by checking its status:
```bash
kubectl get pods
```


Then inspect detailed information:
```bash
kubectl describe pod <pod-name>
```


I would particularly check the Events section.
Then I would check application logs:
```bash
kubectl logs <pod-name>
```


I would also verify:
Image name
Image tag
Image registry
Registry permissions
Node availability
Resource availability
Configuration
The important point is to troubleshoot systematically rather than immediately changing the application.

8. What is ImagePullBackOff and what can cause it?
**💡 Answer:**

ImagePullBackOff indicates that Kubernetes is having difficulty pulling the required container image.
Possible causes include:
Incorrect image name
Incorrect image tag
Image does not exist
Incorrect registry path
Private registry
Missing permissions
Registry connectivity problems
The first practical step would be:
```bash
kubectl describe pod <pod-name>
```


Then inspect the Events to identify the actual image-pull error.

9. What is the purpose of kubectl exec?
**💡 Answer:**

```bash
kubectl exec allows us to execute a command inside a running Pod/container.
```

The session used this concept while working with Jenkins.
Conceptually:
```text
kubectl exec
     ↓
Running Pod
     ↓
Container
     ↓
Shell/Command
```


It is useful when we need to inspect or troubleshoot something from inside the running container.

10. What is a Node Pool in Kubernetes?
**💡 Answer:**

A node pool is a group of nodes managed together with a particular configuration.
For example:
Kubernetes Cluster
|
+---+---+
|       |
Pool 1   Pool 2
| |       | |
N N       N N

Node pools can be useful when different workloads require different machine configurations or when we want to scale particular groups of nodes.
The session discussed node pools in the context of production architecture, scaling and workload management.

10 Scenario-Based Interview Questions with Answers
### ❓ Q1: Scenario: You deployed an NGINX Pod, and kubectl get pods shows that the Pod is not running. What will you do?

**💡 Answer:**

First:
```bash
kubectl get pods
```


Then:
```bash
kubectl describe pod <pod-name>
```


I would inspect the Events section.
If the problem is an image-pull issue, I would verify:
Image name
Image tag
Registry
Image availability
Registry permissions
Then I would check logs if the container actually started:
```bash
kubectl logs <pod-name>
```


The important approach is to identify the exact failure rather than guessing.

2. Scenario: Your Pod is running successfully, but users cannot access the application from the internet. What would you check?
**💡 Answer:**

If the Pod is running, I would check the Service.
```bash
kubectl get svc
```


Then verify:
Service type
External IP
Service port
Target port
Pod connectivity
Application listening port
If external access is required in a cloud environment, I would verify that the Service is configured appropriately, such as:
LoadBalancer

The flow should be:
```text
Internet
   ↓
LoadBalancer
   ↓
Service
   ↓
Pod
```



3. Scenario: You created a Service with ClusterIP and tried to access it directly from the internet. It doesn't work. Why?
**💡 Answer:**

Because ClusterIP is intended for internal cluster communication.
The Service can be accessed by workloads within the cluster, but it does not provide the same type of public exposure as a cloud LoadBalancer.
For external access, depending on the architecture, we could use:
NodePort

or:
LoadBalancer

The session specifically demonstrated LoadBalancer for external access.

4. Scenario: Your Kubernetes Pod reports an image-pull error, but the developer says the image exists. What would you check?
**💡 Answer:**

I would not stop at "the image exists."
I would verify:
Exact image name
Exact image tag
Registry URL
Whether the image is public/private
Authentication credentials
Kubernetes permissions
Node connectivity to the registry
For example:
Expected:
myapp:v2

Deployment:
myapp:v1

The image may exist, but the specific tag being requested might not.
I would use:
```bash
kubectl describe pod <pod-name>
```


and inspect the Events.

5. Scenario: Your Kubernetes node has insufficient resources and a new Pod cannot be scheduled. What would you investigate?
**💡 Answer:**

I would check the nodes:
```bash
kubectl get nodes
```


Then inspect the node:
```bash
kubectl describe node <node-name>
```


I would look at available resources and scheduling information.
Possible solutions include:
Removing unnecessary workloads
Increasing node capacity
Adding nodes
Scaling the node pool
Using an appropriate node configuration
The session discussed this as part of node and workload scaling.

6. Scenario: A Pod suddenly fails in production. You manually create another Pod every time this happens. Is this a good Kubernetes approach?
**💡 Answer:**

No.
One of Kubernetes' important capabilities is maintaining the desired state.
If a workload is managed appropriately, Kubernetes can detect that the desired workload is no longer available and work toward restoring it.
The conceptual flow is:
```text
Desired State
     ↓
Pod Running
     ↓
Pod Failure
     ↓
Kubernetes Detects Difference
     ↓
Replacement/Rescheduling
```


Manually recreating Pods repeatedly defeats much of the orchestration benefit Kubernetes provides.

7. Scenario: Your company has two types of workloads. One needs high CPU and another needs more memory. How could node pools help?
**💡 Answer:**

We can create separate node pools with different machine configurations.
For example:
Cluster
|
```text
 +-- CPU-Optimized Pool
 |      ├── Node
 |      └── Node
 |
 +-- Memory-Optimized Pool
        ├── Node
        └── Node
```


Labels and scheduling rules can then help place workloads onto appropriate nodes.
This allows infrastructure to be better matched to workload requirements.

8. Scenario: You are asked to deploy Jenkins on Kubernetes and make it accessible from your browser. Explain the basic process.
**💡 Answer:**

The basic process demonstrated in the session is:
```text
Jenkins Image
     ↓
Jenkins Pod
     ↓
Kubernetes Service
     ↓
LoadBalancer
     ↓
External IP
     ↓
Browser
```


After the Jenkins Pod is running, I would verify:
```bash
kubectl get pods
kubectl get svc
```


Then I would use the exposed address and port to access Jenkins.
The session also demonstrated using kubectl exec to access the Jenkins container and retrieve its initial password.

9. Scenario: You have multiple Kubernetes clusters configured on your laptop. How do you determine which cluster/context you are using?
**💡 Answer:**

I would check the configured contexts:
```bash
kubectl config get-contexts
```


This allows me to see the available Kubernetes contexts.
The general flow is:
```text
kubectl
   ↓
kubeconfig
   ↓
Context
   ↓
Kubernetes Cluster
```


This is particularly important when working with multiple development, staging and production clusters because accidentally running commands against production is a rather effective way to ruin an otherwise peaceful afternoon.

### 📌 10. Scenario: Your team says, "We can manage everything through the GCP Kubernetes UI, so why should we learn kubectl?"

**💡 Answer:**

The UI can make Kubernetes easier to visualize and manage, but the CLI is important because Kubernetes commands are much more portable across environments.
For example:
```bash
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
kubectl get nodes
```


These concepts and commands are useful whether Kubernetes is running on:
GKE
AWS
Azure
Local Kubernetes
Minikube
Other Kubernetes environments
Therefore, I would use the UI as a convenience but learn the Kubernetes concepts and CLI commands themselves.


