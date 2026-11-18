## Key Outcomes

The session introduced Kubernetes (K8s) as a container orchestration platform, covering its architecture, core components, and rationale for adoption over standalone Docker. Participants created their first Kubernetes cluster on Google Cloud Platform (GCP), ran basic `kubectl` commands, and were introduced to namespaces and deployment manifests via YAML. The session emphasized both administrator and application deployment job roles within Kubernetes, and concluded with guidance on GCP cost management practices.

---

## Decisions Made

- **Kubernetes over Docker for scale:** Docker is sufficient for managing 1–10 containers, but Kubernetes is required for orchestrating thousands of containers (e.g., Netflix runs ~500,000 containers daily). 
- **Standard cluster mode selected** (not Autopilot) so participants manage the cluster themselves, mirroring real-world DevOps responsibilities. 
- **Odd number of nodes mandated:** Clusters must use odd node counts (3, 5, 7…) to ensure majority voting (N/2 + 1) for master election and high availability. 
- **GCP as primary Kubernetes learning platform:** GCP's managed GKE was chosen as the training environment due to its maturity and ease of cluster creation; AWS EKS will be used during the project phase. 
- **All clusters must be deleted after each session** to avoid unnecessary billing charges, especially since a 3-node Kubernetes cluster spins up 3 VMs. 
- **CLI-first approach:** All Kubernetes operations to be learned via `kubectl` commands, not UI, to build interview-ready skills. 

---

## Why Kubernetes: Architecture Rationale

### Monolithic vs. Microservices

- **Monolithic architecture:** Single infra hosts frontend, middleware, and database — failure in one component affects everything. 
- **Microservices architecture:** Each service is independent, allowing selective scaling (e.g., scale only the pizza service when demand spikes, not the entire restaurant). 
- **Benefit of microservices:** Enables plugging in ready-made services (Java app, email service, Python AI module) into a single infrastructure without full rewrites. 

### Docker vs. Kubernetes

|     Concern     |         Docker          |       Kubernetes        |
|-----------------|-------------------------|-------------------------|
| Container count | Works well for 1–10     | Designed for thousands+ |
| Automation      | Manual commands         | Orchestration built-in  |
| Scaling         | Manual                  | Auto-scaling            |
| Load balancing  | Separate purchase (ELB) | Built-in                |
| Rollbacks       | Manual                  | Automatic               |

- **Key term for interviews:** Kubernetes is a **container orchestration system** — "orchestration" is the preferred word over "management" or "automation." 

---

## Kubernetes: Core Concepts Covered

### What is Kubernetes?

- Open-source container orchestration system originally designed by **Google**, released **7 June 2014**, now maintained by **CNCF (Cloud Native Computing Foundation)**. 
- Written **98% in Go language**. 
- Available as managed services: **EKS** (AWS), **AKS** (Azure), **GKE** (GCP) — same Kubernetes, different infra providers. 
- Free and open-source; cloud providers charge only for the underlying infrastructure. 

### Cluster & Node Architecture

- **Cluster:** The largest unit — a collection of multiple machines (nodes). 
- **Node:** A single machine (VM or physical) within a cluster; provides CPU, memory, and storage for running containers. 
- **Master node:** Manages all worker nodes; receives commands via `kubectl`; not directly allocated as a separate VM in managed cloud services. 
- **Worker nodes:** Run the actual application containers; each contains a **Kubelet** agent that reports node status back to the master. 
- **Why 3 nodes minimum:** Ensures at least one node remains active if another fails; odd count enables majority-based leader election (N/2 + 1 threshold). 
- **Cluster OS:** GCP uses **Container-Optimized OS (COS)** by default — not Ubuntu, CentOS, or Windows — optimized for security and container performance. 

### Pod

- **Pod = the smallest deployable unit** in Kubernetes, roughly equivalent to a container (or group of containers). 
- A pod can contain a **single container** or **multiple containers**, and optionally a **volume**. 
- Pods run inside nodes; nodes provide CPU/memory resources to pods. 

### Namespace

- **Logical isolation unit** within a cluster — separates environments (dev, QA, prod) without requiring separate clusters. 
- Default namespaces present after cluster creation: `default`, `kube-system`, `kube-public`, `kube-node-lease`. 
- Dev and QA namespaces can coexist in a cluster; production is typically isolated. 

### Services, Volumes, Deployment

- **Minimum Kubernetes concepts for freshers:** Pod, Services, Volume, Namespace. 
- **Deployment manifest:** Defines how an application is deployed — includes API version, kind, metadata (name, namespace), labels, selectors, and replica count. 
- **Labels & Selectors:** Used to tag and identify pods so services can route traffic to the correct group (analogous to tagging resources). 
- **Kubernetes features include:** Self-healing, auto-scaling, automatic rollbacks to previous versions, built-in load balancing, batch execution, scheduling, and monitoring. 

---

## Application Deployment Flow (End-to-End)

1. **Developer writes code** on local machine using an IDE. 
2. **Code pushed to Git repository**; unit testing and build/compile triggered (via Jenkins, GitLab, etc.). 
3. **Artifact created** — a packaged version of the application (JAR, WAR, etc.). 
4. **Dockerfile written** — copies artifact into a base OS image, defines recipe for the container. 
5. **Docker image built** using `docker build -t <name>:<version> .`. 
6. **Image pushed to registry** (Docker Hub, Nexus, JFrog, ECR, GCR, etc.). 
7. **Kubernetes manifest file (YAML) written** — references the image and defines deployment configuration. 
8. **Deployed to Kubernetes cluster** using `kubectl apply -f <file>.yaml`. 

---

## Hands-On: Cluster Creation on GCP

### Cluster Configuration Used

- **Platform:** Google Kubernetes Engine (GKE), Standard mode
- **Cluster name:** `day-one`
- **Zones:** 3 zones selected (US region by default); 1 node per zone = **3 nodes total**
- **Node spec:** 2 vCPUs, 1 core, 4 GB RAM per node → **Total: 6 vCPUs, 12 GB RAM**
- **Storage:** 30 GB per node → **Total: 90 GB**
- **OS:** Container-Optimized OS (COS) — default, unchanged 

### Key Commands Executed

```bash
# Connect to GKE cluster (command from GCP console "Connect" option)
gcloud container clusters get-credentials <cluster-name> --zone <zone> --project <project>

# Verify nodes
kubectl get nodes

# List namespaces
kubectl get ns

# Create namespace via command
kubectl create ns dev

# Create namespace via YAML
kubectl apply -f nsqa.yaml

# Verify namespaces created
kubectl get ns
```

---

# Interview Preparation: Kubernetes Day 1

The following questions are based on the topics covered in this session: Kubernetes fundamentals, Docker vs. Kubernetes, cluster and node architecture, Pods, namespaces, deployments, labels and selectors, GKE cluster creation, `kubectl`, application deployment flow, and cloud cost management.

## Part 1: 20 Kubernetes Interview Questions and Answers

### 1. What is Kubernetes?

**Answer:** Kubernetes (K8s) is an open-source container orchestration platform. It helps deploy, scale, manage, and maintain containerized applications across a cluster of machines. It provides capabilities such as scheduling, self-healing, scaling, service discovery, and rolling updates.

### 2. Why do we need Kubernetes when we already have Docker?

**Answer:** Docker can run containers, but managing a large number of containers manually becomes difficult. Kubernetes orchestrates containers across multiple nodes. It helps with scheduling, scaling, service discovery, load balancing, self-healing, and application rollouts. Docker is useful for building and running containers; Kubernetes coordinates containerized workloads at scale.

### 3. What is the difference between Docker and Kubernetes?

**Answer:** Docker is primarily used to build and run containers. Kubernetes is used to orchestrate containerized applications across a cluster. With Docker alone, an operator may need to manage container placement, scaling, and recovery manually. Kubernetes provides declarative deployment and orchestration features to automate much of this work.

### 4. What is a Kubernetes cluster?

**Answer:** A Kubernetes cluster is a group of machines that work together to run containerized applications. It consists of a control plane and worker nodes. The control plane manages the cluster, while worker nodes provide resources and run application Pods.

### 5. What is a node in Kubernetes?

**Answer:** A node is a physical or virtual machine that is part of a Kubernetes cluster. It provides CPU, memory, networking, and storage resources for running Pods. Worker nodes run application workloads and communicate with the control plane.

### 6. What is the role of the control plane (master)?

**Answer:** The control plane manages the overall Kubernetes cluster. It makes decisions about scheduling, maintains the desired state of workloads, and exposes the Kubernetes API. In managed services such as GKE, EKS, and AKS, the cloud provider manages the control plane for the customer.

### 7. What is a worker node?

**Answer:** A worker node is a machine where application workloads run. It hosts Pods and includes components such as the kubelet, which communicates with the control plane and makes sure the containers described for that node are running.

### 8. What is a Pod?

**Answer:** A Pod is the smallest deployable unit in Kubernetes. It can contain one or more closely related containers that share networking and can share storage volumes. In many common application deployments, one application container runs inside one Pod.

### 9. Can a Pod contain multiple containers?

**Answer:** Yes. A Pod can contain multiple containers when they need to work closely together, such as an application container and a helper or sidecar container. Containers in the same Pod share the Pod network namespace and can communicate through `localhost`.

### 10. What is a Kubernetes namespace?

**Answer:** A namespace is a logical way to divide cluster resources. Teams can use namespaces to separate environments or workloads, such as `dev`, `qa`, and `prod`, without creating a separate cluster for every environment. Namespaces provide a scope for many Kubernetes objects, but they are not a complete security boundary by themselves.

### 11. Which namespaces are commonly present in a new Kubernetes cluster?

**Answer:** Common default namespaces include:
- `default` – used when no namespace is specified.
- `kube-system` – contains Kubernetes system components.
- `kube-public` – intended for publicly readable cluster information.
- `kube-node-lease` – holds node lease objects used for node heartbeat information.

### 12. What is a Kubernetes Deployment?

**Answer:** A Deployment is a Kubernetes object used to manage stateless application Pods. It declares the desired number of replicas and the Pod template. Kubernetes works to maintain that desired state and supports controlled updates and rollbacks.

### 13. What information is commonly included in a Deployment YAML file?

**Answer:** A Deployment manifest commonly includes:
- `apiVersion` – the API version for the resource.
- `kind` – the resource type, such as `Deployment`.
- `metadata` – the name, namespace, and labels.
- `spec.replicas` – the desired number of Pods.
- `spec.selector` – identifies the Pods managed by the Deployment.
- `spec.template` – defines Pod metadata and container specifications, including the image.

### 14. What are labels and selectors in Kubernetes?

**Answer:** Labels are key-value pairs attached to Kubernetes objects, such as Pods. Selectors are queries used to identify objects with matching labels. Deployments use selectors to manage their Pods, and Services use selectors to identify the Pods that should receive traffic.

### 15. What is a Kubernetes Service?

**Answer:** A Service provides a stable network endpoint for accessing a group of Pods. Since Pods can be replaced and their IP addresses can change, a Service gives clients a consistent way to reach matching application Pods. Common Service types include `ClusterIP`, `NodePort`, and `LoadBalancer`.

### 16. What is a Volume in Kubernetes?

**Answer:** A Volume provides storage that can be mounted into containers in a Pod. It is useful when application data must be shared between containers or preserved beyond the lifecycle of an individual container. For persistent application data, Kubernetes commonly uses PersistentVolumes and PersistentVolumeClaims.

### 17. What is the difference between GKE, EKS, and AKS?

**Answer:** They are managed Kubernetes services from different cloud providers:
- **GKE** – Google Kubernetes Engine on Google Cloud.
- **EKS** – Amazon Elastic Kubernetes Service on AWS.
- **AKS** – Azure Kubernetes Service on Microsoft Azure.

The Kubernetes concepts and APIs are largely the same, while the cloud integrations and infrastructure management differ.

### 18. What is the purpose of the `kubectl` command?

**Answer:** `kubectl` is the command-line tool used to communicate with a Kubernetes cluster through its API. Engineers use it to inspect resources, apply YAML manifests, troubleshoot workloads, and manage cluster objects. Examples include `kubectl get nodes`, `kubectl get pods`, and `kubectl apply -f deployment.yaml`.

### 19. What is the end-to-end flow for deploying an application to Kubernetes?

**Answer:** A typical flow is:
1. A developer writes and commits application code.
2. A CI tool such as Jenkins or GitLab CI runs tests and builds the application.
3. The pipeline creates an artifact, such as a JAR or WAR file.
4. A Dockerfile packages the artifact into an image.
5. The image is built and pushed to a container registry.
6. A Kubernetes YAML manifest references the image and defines the workload.
7. The manifest is applied to the cluster using `kubectl apply -f`.
8. The team verifies the rollout and exposes the application through a Service or another suitable networking component.

### 20. How can you avoid unnecessary GCP charges while learning Kubernetes?

**Answer:** Use only the resources needed for the lab and delete the cluster when the session is complete. A GKE Standard cluster can create billable worker VMs and use other billable resources. Before finishing, inspect the cluster and related resources, delete what is no longer required, and verify that the resources have been removed. Also check the billing page for any remaining charges or resources.

---

## Part 2: 20 Kubernetes Scenario-Based Interview Questions and Answers

### 1. Your team is running only a few containers on one VM. Do you immediately need Kubernetes?

**Answer:** Not necessarily. For a small workload, Docker on a VM may be enough. Kubernetes becomes more useful when the team needs orchestration across machines, automated recovery, scaling, service discovery, and consistent deployment management. Choose the platform based on operational requirements rather than using Kubernetes for every container workload.

### 2. Your application must run in both development and QA environments. How can you separate them in one cluster?

**Answer:** Create separate namespaces, for example `dev` and `qa`, and deploy each environment's resources into its own namespace.

```bash
kubectl create namespace dev
kubectl create namespace qa
kubectl get namespaces
```

Then specify the namespace in each manifest or use `-n` with commands.

### 3. You created a GKE cluster, but `kubectl get nodes` returns an error saying it cannot connect to the server. What will you check?

**Answer:** First check whether the cluster is running and whether the correct GCP project and location are selected. Then refresh the local kubeconfig credentials:

```bash
gcloud container clusters get-credentials <cluster-name> \
  --zone <zone> \
  --project <project-id>

kubectl get nodes
```

Also verify that the required GCP permissions and network access are available.

### 4. You applied a Deployment manifest, but no Pods are appearing. How will you troubleshoot?

**Answer:** Check whether the Deployment was created in the expected namespace and inspect its events and status.

```bash
kubectl get deployments -A
kubectl describe deployment <deployment-name> -n <namespace>
kubectl get pods -n <namespace>
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

Look for issues such as an invalid manifest, selector/template label mismatch, scheduling constraints, or an image problem. Use the event messages to identify the actual cause.

### 5. Your application needs three identical instances for availability. What will you configure?

**Answer:** Set the Deployment replica count to three. Kubernetes will try to maintain three matching Pods.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports:
            - containerPort: 80
```

Apply and verify:

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods -l app=web-app
```

### 6. One of your application Pods is deleted accidentally. What should happen?

**Answer:** If the Pod is managed by a Deployment, Kubernetes notices that the actual number of matching Pods is below the desired replica count. The Deployment's ReplicaSet creates a replacement Pod to move the workload back toward the desired state.

### 7. Your Service is not sending traffic to the application Pods. What is one of the first things you should verify?

**Answer:** Check whether the Service selector matches the labels on the Pods. A mismatch can leave the Service without the intended endpoints.

```bash
kubectl get service <service-name> -n <namespace> -o yaml
kubectl get pods --show-labels -n <namespace>
kubectl get endpoints <service-name> -n <namespace>
```

Correct the selector or Pod labels so they match, while keeping the Deployment selector consistent with its Pod template labels.

### 8. You created a namespace named `dev`, but your Pod is not visible when you run `kubectl get pods`. What could be wrong?

**Answer:** The command without a namespace checks the current namespace, usually `default`. The Pod may be in `dev`.

```bash
kubectl get pods -n dev
kubectl get pods --all-namespaces
```

You can also set the current namespace in your kubectl context, but using `-n` explicitly is useful during troubleshooting.

### 9. You want to create a namespace using a YAML file instead of an imperative command. What will you do?

**Answer:** Create a manifest such as:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: qa
```

Apply it and verify:

```bash
kubectl apply -f namespace.yaml
kubectl get namespaces
```

### 10. Your team has a new version of an application image. How would you deploy it through a Deployment?

**Answer:** Update the image in the Deployment manifest and apply the file. Kubernetes performs a rolling update according to the Deployment strategy.

```bash
kubectl set image deployment/web-app web=nginx:1.28 -n dev
kubectl rollout status deployment/web-app -n dev
kubectl get pods -n dev
```

The container name (`web`) must match the name in the Pod template.

### 11. The latest application release is causing errors. How can you roll back the Deployment?

**Answer:** If the Deployment has a previous revision available, use the rollout undo command.

```bash
kubectl rollout history deployment/web-app -n dev
kubectl rollout undo deployment/web-app -n dev
kubectl rollout status deployment/web-app -n dev
```

Check the rollout history and application health after the rollback. A rollback is not a substitute for investigating the release failure.

### 12. Your application container image is stored in a private registry and the Pod cannot pull it. What would you investigate?

**Answer:** Inspect the Pod events to confirm whether the failure is an authentication issue, an incorrect image name or tag, or a registry connectivity issue.

```bash
kubectl describe pod <pod-name> -n <namespace>
```

For a private registry, configure an appropriate image pull secret and reference it in the Pod specification using `imagePullSecrets`. Ensure the credentials have permission to pull the image.

### 13. A Pod is stuck in `Pending`. What steps will you take?

**Answer:** Describe the Pod and inspect its events.

```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl get nodes
kubectl get events -n <namespace> --sort-by=.metadata.creationTimestamp
```

Common causes include insufficient CPU or memory, unavailable nodes, storage claims that cannot be bound, or scheduling constraints. Resolve the specific cause shown in the events.

### 14. A Pod is repeatedly restarting. How will you investigate the issue?

**Answer:** Check the Pod status, previous container logs, and events.

```bash
kubectl get pods -n <namespace>
kubectl logs <pod-name> -n <namespace> --previous
kubectl describe pod <pod-name> -n <namespace>
```

Look for application crashes, incorrect configuration, missing dependencies, failed health checks, or resource limits. Fix the root cause rather than repeatedly deleting the Pod.

### 15. Your application has multiple replicas, and users need one stable endpoint to access them. What Kubernetes object will you use?

**Answer:** Create a Service whose selector matches the labels on the application Pods. The Service provides a stable endpoint and distributes connections across eligible endpoints according to the Service implementation and traffic rules.

```bash
kubectl expose deployment web-app \
  --name=web-app-service \
  --port=80 \
  --target-port=80 \
  -n dev

kubectl get service web-app-service -n dev
```

### 16. You need to run a Java application and a helper container that works closely with it and shares its network. How would you model this?

**Answer:** If both containers must share the same network identity and lifecycle, define them in the same Pod. Containers in a Pod share the Pod IP and can communicate over `localhost`. Use a shared volume if they also need to exchange files.

### 17. A developer accidentally deploys a resource into the `default` namespace instead of `dev`. How can you prevent similar mistakes?

**Answer:** Set `metadata.namespace: dev` in the manifest, or apply the manifest with an explicit namespace:

```bash
kubectl apply -f deployment.yaml -n dev
```

For team environments, use access controls and deployment pipeline checks to restrict which identities can deploy to sensitive namespaces.

### 18. You have created a three-node GKE Standard cluster for a lab. The session is over. What should you do to control cost?

**Answer:** Delete the lab cluster if it is no longer needed, then verify that related billable resources have also been removed.

```bash
gcloud container clusters delete <cluster-name> \
  --zone <zone> \
  --project <project-id>
```

Confirm the deletion in GKE and review the cloud billing/resource pages. Also check for separately created disks, load balancers, static IPs, or other resources that may remain.

### 19. Your organization wants to run Kubernetes on AWS, Azure, and GCP. Do engineers need to learn three completely different orchestration platforms?

**Answer:** No. EKS, AKS, and GKE are managed Kubernetes offerings. Core Kubernetes concepts such as Pods, Deployments, Services, namespaces, YAML manifests, and `kubectl` remain largely consistent. Engineers still need to learn provider-specific networking, identity, storage, load balancing, and operational integrations.

### 20. Your CI/CD pipeline successfully builds a Docker image, but the application is not updated in Kubernetes. What would you check?

**Answer:** Trace the pipeline after the image build. Confirm that the image was pushed successfully, that the manifest references the intended registry and tag, and that the deployment step ran against the correct cluster and namespace.

```bash
kubectl config current-context
kubectl get deployment web-app -n dev -o yaml
kubectl describe deployment web-app -n dev
kubectl rollout status deployment/web-app -n dev
```

Also check whether the pipeline has permission to update the cluster and whether the Deployment's Pod template actually changed. Prefer unique image tags for releases rather than relying only on a mutable `latest` tag.

---

## Quick Revision: Important Commands

| Task | Command |
|---|---|
| Connect to a GKE cluster | `gcloud container clusters get-credentials <cluster> --zone <zone> --project <project>` |
| List nodes | `kubectl get nodes` |
| List namespaces | `kubectl get ns` |
| Create a namespace | `kubectl create ns dev` |
| Apply a YAML manifest | `kubectl apply -f <file>.yaml` |
| List Pods in current namespace | `kubectl get pods` |
| List Pods in a namespace | `kubectl get pods -n dev` |
| List Pods in all namespaces | `kubectl get pods -A` |
| Describe a Pod | `kubectl describe pod <pod-name> -n <namespace>` |
| View Pod logs | `kubectl logs <pod-name> -n <namespace>` |
| View previous container logs | `kubectl logs <pod-name> -n <namespace> --previous` |
| List Deployments | `kubectl get deployments -n <namespace>` |
| List Services | `kubectl get services -n <namespace>` |
| Check rollout status | `kubectl rollout status deployment/<deployment-name> -n <namespace>` |
| View rollout history | `kubectl rollout history deployment/<deployment-name> -n <namespace>` |
| Roll back a Deployment | `kubectl rollout undo deployment/<deployment-name> -n <namespace>` |
| Delete a GKE cluster | `gcloud container clusters delete <cluster> --zone <zone> --project <project>` |

## Practice Tasks for Students

1. Connect to your GKE cluster using `gcloud container clusters get-credentials`.
2. Verify all worker nodes using `kubectl get nodes`.
3. List the default namespaces using `kubectl get ns`.
4. Create `dev` and `qa` namespaces using both commands and YAML.
5. Deploy an Nginx application with two replicas in the `dev` namespace.
6. Verify the Pods and Deployment.
7. Add a Service for the application and inspect its endpoints.
8. Change the image version and observe the rollout.
9. Review the rollout history and practice a rollback.
10. Delete the lab cluster after completing the exercises and verify that no unnecessary resources remain.

> **Interview tip:** Do not memorize only the commands. Explain what you are checking, why you are checking it, and how the result helps you identify the root cause.
