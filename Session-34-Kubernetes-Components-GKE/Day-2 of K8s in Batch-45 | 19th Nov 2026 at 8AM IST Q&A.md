## Key Outcomes

This was Day 2 of a Kubernetes training session covering the full Kubernetes architecture (master and worker node components), hands-on cluster creation on Google Kubernetes Engine (GKE), and practical pod management using `kubectl`. Students created a GKE standard cluster, ran their first NGINX pod, exposed it via a LoadBalancer service, and debugged an intentional `ImagePullBackOff` error. The session concluded with discussions on resume building, service account best practices, and real-world troubleshooting scenarios.

---

## Decisions Made

- **Standard cluster over Autopilot:** GKE Standard mode selected so students manage the cluster themselves; Autopilot is Google-managed and was explicitly avoided for learning purposes. 
- **Kubernetes version in use:** Version **1.36** confirmed as the current production-stable version used in real environments. 
- **Default maximum pods per node:** **110 pods per node** is the Kubernetes default; this is the expected interview answer — avoid saying "up to," "max," or "minimum." 
- **Node OS:** **Container-Optimized OS (COS)** is the best practice for GKE worker nodes, though Ubuntu and Windows are also available. 
- **Service types for pod exposure:** Three service types confirmed — **ClusterIP** (cluster-internal), **NodePort** (node-level), **LoadBalancer** (internet-facing, chargeable). 
- **Weekend task:** Students instructed to revise all Docker and Kubernetes content covered so far and review previous batch recordings. 
- **Tomorrow's class:** Dedicated **resume-building session** planned, covering how to write project experience, bullet points, cover letters, and day-to-day activity answers. 

---

## Kubernetes Architecture: Master Node (Control Plane)

The master node — also called the **control plane** — contains four core components. 

- **API Server:** Every request (kubectl command, YAML apply, integration) goes through the API server first; it is the single entry point for all cluster communication. 
- **ETCD:** The memory/state store of Kubernetes. Stores all cluster state as **key-value pairs**. Should be referred to as "state" in interviews, not simply "database," though it functions like one. 
- **Scheduler:** Finds the best node for a new pod. Communicates with kubelet via the API server to identify available space; does not execute the placement itself. 
- **Controller Manager:** Performs the actual work of maintaining cluster state — manages replicas, namespaces, service accounts, and resources. Distinct from "control plane" (a common confusion point). 
> 
> **Interview framing reinforced:** "Control plane" = the entire master node; "Controller Manager" = one specific component inside it. 

---

## Kubernetes Architecture: Worker Node

Each worker node runs three components. 

- **Kubelet:** An agent running on every worker node. Passes information between the worker and the master (API server). Responsible for pulling images and creating containers/pods as instructed. Distinct from `kubectl` — kubelet is the agent; kubectl is the CLI utility. 
    - Kubelet continuously retries failed operations (e.g., image pulls) and generates events on each attempt, which is how troubleshooting visibility is provided. 
- **Kube-proxy:** Handles networking and exposes pods to the internet or internal cluster traffic. Manifests as a **Service** object in Kubernetes. 
- **Container Runtime:** Runs the actual application containers inside the worker node (Docker/dockerized environment). Everything inside Kubernetes ultimately runs within a Docker-compatible runtime. 

---

## Pod Concepts and Varieties

- A **pod** is a single instance of a running process (container) in a Kubernetes cluster — Kubernetes wraps a container with an additional layer and calls it a pod. 
- Pods can contain: a single container; a container + volume; multiple containers + single volume; multiple containers + multiple volumes — all are valid. 
- **Best practice (real-world):** Single container per pod is most common; sidecar patterns (multiple containers) exist but are more advanced. 
- Pods can include config maps, volumes, and separate configurations alongside the container. 

---

## Cluster Creation on GKE (Hands-On)

**Steps demonstrated:** 

1. Navigate to GKE in Google Cloud Console → click **Create**.
2. Select **Standard** (not Autopilot) — use the "Switch to Standard Cluster" button if Autopilot loads by default.
3. Set cluster name (e.g., `day-two`), select region/zone, confirm Kubernetes version (1.36).
4. Key configuration options reviewed:
    - **Nodes per zone:** Setting 3 nodes × 3 zones = **9 total worker nodes**. 
    - **Node size:** CPU/RAM based on application load (e.g., 8-core, 16-core, 32-core); all nodes in a cluster are the same size. 
    - **Auto-scaling:** Scales at the **node level** (not pod/container level) — nodes are added/removed based on traffic. 
    - **Backup plan:** Configurable retention (e.g., daily backup, 7-day retention = RPO of 1 day). 
    - **Surge upgrade / Blue-Green deployment:** Node upgrade strategy; blue-green means running two environments simultaneously, routing traffic gradually (10% → 20% → 100%), then retiring the old version — doubles infrastructure cost. 
    - **Canary deployment:** Traffic is split incrementally (10%, 20%, 100%) to the new version, as opposed to blue-green's full parallel environment. 
    - **Networking / CNI:** VPC selection, IP management, Container Network Interface options available. 
    - **Metadata / Labels:** Used for firewall rules, access control, and node identification (e.g., label `env=prod`). 
    - **Secure Boot:** Optional; enables malware/rootkit security checks. 
5. Cluster creation takes approximately **13 minutes** when using the automation command via Cloud Shell. 

**Connecting to the cluster:** 

- In GKE console → select cluster → click **Connect** → copy the `gcloud container clusters get-credentials` command.
- Execute in Cloud Shell; this generates a `~/.kube/config` file with cluster credentials.
- Verify connection: `kubectl cluster-info` (no `get` needed — direct command). 
- Check nodes: `kubectl get nodes`

---

## kubectl Commands Covered

|                 Command                  |                           Purpose                            |
|------------------------------------------|--------------------------------------------------------------|
| `kubectl cluster-info`                   | Display cluster endpoint information                         |
| `kubectl get nodes`                      | List all worker nodes                                        |
| `kubectl run nginx --image=nginx:latest` | Create a pod (equivalent to `docker run nginx`)              |
| `kubectl get pods`                       | List all pods                                                |
| `kubectl get svc`                        | List all services                                            |
| `kubectl describe pod <name>`            | Full pod details including events (key for troubleshooting)  |
| `kubectl logs <pod-name>`                | View application logs (only works when container is running) |
| `kubectl delete pod <name>`              | Delete a pod                                                 |
| `kubectl get pods -o wide`               | Extended output showing node assignment                      |
| `kubectl get svc -w`                     | Watch service (continuous — waits for IP assignment)         |


---

## Kubernetes Interview Questions and Answers

### 1. What is Kubernetes?

**Answer:** Kubernetes is a container orchestration platform used to deploy, manage, and scale containerized applications. It provides a cluster of control-plane and worker-node components. In this session, we used GKE to create a Kubernetes cluster and deployed an NGINX pod.

### 2. What is a Kubernetes cluster?

**Answer:** A Kubernetes cluster is a group of machines that work together to run containerized applications. It includes the control plane, which manages the cluster, and worker nodes, which run application workloads.

### 3. What is the control plane?

**Answer:** The control plane is the management layer of Kubernetes. It contains components such as the API Server, ETCD, Scheduler, and Controller Manager. It is responsible for accepting requests, storing cluster state, selecting nodes for pods, and maintaining the desired state.

### 4. What is the role of the Kubernetes API Server?

**Answer:** The API Server is the entry point for communication with the Kubernetes cluster. Requests from `kubectl`, YAML manifests, and integrations go through the API Server before the cluster processes them.

### 5. What is ETCD, and what does it store?

**Answer:** ETCD is Kubernetes' key-value state store. It stores the cluster's state and configuration information. In an interview, describe it as the Kubernetes state store rather than simply calling it a database.

### 6. What does the Kubernetes Scheduler do?

**Answer:** The Scheduler identifies a suitable worker node for a newly created pod. It considers available node capacity and other scheduling requirements. It selects the node; it does not itself run the container.

### 7. What is the Controller Manager?

**Answer:** The Controller Manager runs controllers that work to keep the actual cluster state aligned with the desired state. The session discussed its role in managing resources such as replicas, namespaces, and service accounts. It is one component of the control plane, not another name for the entire control plane.

### 8. What are the main components of a Kubernetes worker node?

**Answer:** The three worker-node components covered in the session are:
- **Kubelet:** Communicates with the API Server and makes sure instructed pods and containers are created.
- **Kube-proxy:** Supports cluster networking and service traffic.
- **Container runtime:** Runs the application containers.

### 9. What is the difference between `kubectl` and kubelet?

**Answer:** `kubectl` is the command-line utility used by an operator to communicate with a Kubernetes cluster. Kubelet is an agent running on each worker node. It communicates with the API Server and carries out pod-related work on its node.

### 10. What is a pod?

**Answer:** A pod is the Kubernetes unit that runs one or more containers. Kubernetes wraps a container in a pod abstraction and can associate it with volumes and configuration.

### 11. Can a pod contain multiple containers?

**Answer:** Yes. A pod can contain one container or multiple containers. Multiple containers may share volumes. However, the common real-world approach is one main application container per pod; sidecar patterns are used for more advanced cases.

### 12. What is a Kubernetes Service?

**Answer:** A Service provides a stable way to expose or reach pods over the network. The session covered ClusterIP, NodePort, and LoadBalancer service types.

### 13. Explain ClusterIP, NodePort, and LoadBalancer.

**Answer:**
- **ClusterIP:** Exposes a service inside the Kubernetes cluster for internal communication.
- **NodePort:** Exposes the service through a port on the worker nodes.
- **LoadBalancer:** Exposes the service through a load balancer, commonly for external or internet-facing access. In GKE, this can create billable cloud resources.

### 14. How do you create a pod running NGINX using kubectl?

**Answer:** Run:

```bash
kubectl run nginx --image=nginx:latest
```

This creates a pod named `nginx` using the `nginx:latest` container image.

### 15. How do you check whether your Kubernetes nodes are ready?

**Answer:** Run:

```bash
kubectl get nodes
```

Review the node status in the output. A node showing `Ready` is reporting that it is ready to accept workloads.

### 16. How do you check the pods running in a cluster?

**Answer:** Run:

```bash
kubectl get pods
```

To see additional details, including the node on which a pod is assigned, run:

```bash
kubectl get pods -o wide
```

### 17. How do you investigate a pod that is not starting?

**Answer:** Start with:

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

The `describe` output includes pod details and events. Events are especially useful for identifying issues such as image-pull failures.

### 18. What is ImagePullBackOff?

**Answer:** `ImagePullBackOff` is a pod status that indicates Kubernetes is unable to pull the required container image and is backing off before trying again. The session demonstrated this as an intentional troubleshooting exercise. Check the pod events with:

```bash
kubectl describe pod <pod-name>
```

### 19. What happens when a container image pull fails?

**Answer:** Kubelet continues retrying the failed image-pull operation and generates events for the attempts. An operator can inspect those events using `kubectl describe pod <pod-name>` to understand the failure.

### 20. How do you view application logs from a pod?

**Answer:** Run:

```bash
kubectl logs <pod-name>
```

As covered in the session, this command is useful when the container is running and producing logs.

### 21. How do you expose an application outside the cluster?

**Answer:** Create or use a Kubernetes Service of type `LoadBalancer` to request an external load balancer. The session used this approach to expose the NGINX workload. Remember that a cloud load balancer can add infrastructure cost.

### 22. How do you check whether a LoadBalancer Service has received an external IP?

**Answer:** Run:

```bash
kubectl get svc -w
```

The `-w` option watches the Service output continuously. Keep watching until the external IP is assigned, where applicable.

### 23. How do you connect kubectl to a GKE cluster?

**Answer:** In the GKE Console, open the cluster, select **Connect**, and copy the provided `gcloud container clusters get-credentials` command. Run it in Cloud Shell. It configures `~/.kube/config` so `kubectl` can connect to the cluster.

### 24. How do you verify that kubectl is connected to the expected cluster?

**Answer:** Run:

```bash
kubectl cluster-info
kubectl get nodes
```

The first command displays cluster endpoint information. The second lists the worker nodes available through the current context.

### 25. What is the difference between GKE Standard and Autopilot?

**Answer:** In the session, GKE Standard was selected because it gives learners direct responsibility for managing cluster configuration and worker nodes. Autopilot is a more Google-managed operating mode. For the hands-on learning exercise, Standard was the chosen mode.

### 26. Why was Container-Optimized OS recommended for GKE worker nodes?

**Answer:** Container-Optimized OS (COS) was identified in the session as the recommended option for GKE worker nodes. Ubuntu and Windows were also mentioned as available node OS choices.

### 27. How does node auto-scaling work in the cluster configuration discussed?

**Answer:** The session described cluster node auto-scaling as operating at the node level. It adds or removes worker nodes based on workload demand; it is not the same as scaling the number of pods or containers.

### 28. If a GKE cluster has 3 nodes per zone across 3 zones, how many worker nodes are configured?

**Answer:** There are 9 worker nodes in total:

```text
3 nodes per zone × 3 zones = 9 worker nodes
```

### 29. What is the default number of pods per node stated in the session?

**Answer:** The interview answer emphasized in the session is **110 pods per node**.

### 30. What is the difference between blue-green and canary deployment?

**Answer:** In the session's explanation:
- **Blue-green:** Two environments run in parallel. Traffic is gradually moved to the new environment, and the old environment is retired after the transition. Running both environments can increase infrastructure cost.
- **Canary:** A smaller portion of traffic is sent to the new version first, then the share is increased gradually, such as 10%, 20%, and finally 100%.

---

## Real-World Kubernetes Scenario-Based Questions and Answers

### Scenario 1: Your NGINX pod is stuck in ImagePullBackOff. What will you do?

**Answer:**
First, check the pod status:

```bash
kubectl get pods
```

Then inspect its details and events:

```bash
kubectl describe pod <pod-name>
```

Look for image-pull errors in the events. Verify the image name and tag used by the pod and investigate the specific pull failure shown there. Kubelet retries failed pulls, so events can show repeated attempts.

### Scenario 2: You ran `kubectl run`, but you cannot see the pod. How will you troubleshoot?

**Answer:**
Run:

```bash
kubectl get pods
kubectl get pods -o wide
```

Confirm that you are connected to the intended cluster:

```bash
kubectl cluster-info
```

If the pod appears with an error or pending status, inspect it:

```bash
kubectl describe pod <pod-name>
```

Use the events to identify the next troubleshooting step.

### Scenario 3: Your application pod is running, but users outside the cluster cannot access it. What will you check?

**Answer:**
Check whether the application has a Service and identify its type:

```bash
kubectl get svc
```

A ClusterIP Service is for internal cluster access. For external exposure in the session's example, use a LoadBalancer Service. Watch for its external IP:

```bash
kubectl get svc -w
```

Also remember that creating a cloud load balancer may incur charges.

### Scenario 4: A developer says the application should be publicly accessible, but the Service is ClusterIP. What would you explain?

**Answer:**
ClusterIP exposes the Service only inside the cluster. It does not provide the internet-facing exposure expected by the developer. Explain the available choices: NodePort exposes through a node port, while LoadBalancer requests an external load balancer and is the option demonstrated for internet-facing access in the session.

### Scenario 5: You need to confirm which worker node is running a particular pod. Which command will you use?

**Answer:**
Run:

```bash
kubectl get pods -o wide
```

The wide output includes additional information such as the node assignment.

### Scenario 6: A pod is not starting, and the team asks you for evidence rather than a guess. What will you share?

**Answer:**
Run:

```bash
kubectl describe pod <pod-name>
```

Share the pod details and the Events section. Events provide useful evidence about issues such as image-pull failures and repeated kubelet attempts.

### Scenario 7: You have connected to the wrong GKE cluster context. How will you verify the active connection?

**Answer:**
Run:

```bash
kubectl cluster-info
kubectl get nodes
```

Check the cluster endpoint and the nodes returned. If the connection is not the intended one, return to the GKE Console, select the correct cluster, and run its **Connect** command using `gcloud container clusters get-credentials`.

### Scenario 8: Your team is choosing between GKE Standard and Autopilot for a training lab where students must manage nodes. Which mode would you select?

**Answer:**
Select GKE Standard. The session chose Standard specifically so students could manage the cluster and worker-node configuration themselves. Autopilot was avoided for this learning objective because it is more Google-managed.

### Scenario 9: Your application load increases and the cluster needs more worker capacity. Which scaling setting from the session is relevant?

**Answer:**
Review node auto-scaling. The session explained that this setting scales at the node level by adding or removing worker nodes according to demand. Do not confuse node auto-scaling with increasing the number of pods.

### Scenario 10: You configured 3 nodes in each of 3 zones. A teammate reports that the cluster should have 3 nodes total. How would you clarify?

**Answer:**
Explain that the configured count is per zone. The total is:

```text
3 nodes per zone × 3 zones = 9 worker nodes
```

### Scenario 11: Your organization wants a hardened, container-focused operating system for GKE worker nodes. What did the session recommend?

**Answer:**
The session identified Container-Optimized OS (COS) as the best-practice choice for GKE worker nodes. Ubuntu and Windows were also mentioned as available options.

### Scenario 12: You need to explain why a Kubernetes pod is not the same thing as a container. What would you say?

**Answer:**
A container runs the application process, while a pod is the Kubernetes abstraction that wraps one or more containers and can associate them with shared resources such as volumes and configuration. A pod may contain one container or multiple containers.

### Scenario 13: Your team proposes putting several unrelated application containers into one pod. What would you recommend based on the session?

**Answer:**
Recommend the common pattern of one main application container per pod. Multiple containers in a pod are valid, but they are generally used for related patterns such as sidecars. Explain that multiple containers can share a volume when that design is required.

### Scenario 14: You are asked to explain the request flow when an operator runs a kubectl command. Which component is the entry point?

**Answer:**
The API Server is the entry point for cluster communication. Requests from `kubectl`, YAML applications, and integrations go through it. The control-plane components then participate in processing and maintaining cluster state.

### Scenario 15: A new pod needs a worker node. Which component chooses the node, and which component works on the worker node?

**Answer:**
The Scheduler selects a suitable node for the pod. The kubelet on the selected worker node receives instructions through the API Server and is responsible for ensuring the pod and its containers are created. The Scheduler selects; it does not run the container itself.

### Scenario 16: An interviewer asks whether the Controller Manager and control plane are the same thing. How will you answer?

**Answer:**
No. The control plane refers to the complete Kubernetes management layer. The Controller Manager is one component inside it, alongside the API Server, ETCD, and Scheduler.

### Scenario 17: You need to identify the component responsible for storing Kubernetes cluster state. What will you answer?

**Answer:**
ETCD stores Kubernetes cluster state as key-value pairs. Describe it as the state store in the interview rather than reducing its role to simply "a database."

### Scenario 18: You want to see the application output from a pod after it starts. Which command will you use, and what limitation should you remember?

**Answer:**
Use:

```bash
kubectl logs <pod-name>
```

The session noted that this is useful when the container is running and producing logs. If the pod is failing before the container can run, inspect the pod events with `kubectl describe pod <pod-name>` as well.

### Scenario 19: Your team wants to upgrade worker nodes while gradually moving traffic to a new environment. Which deployment approach from the session matches this requirement?

**Answer:**
Blue-green deployment matches the approach of running two environments in parallel and gradually routing traffic to the new one, for example 10%, then 20%, then 100%. Once the new environment is fully serving traffic, the old one can be retired. Explain that temporarily running both environments can increase infrastructure cost.

### Scenario 20: Your team wants to test a new version with a small percentage of production traffic before increasing exposure. Which approach should you describe?

**Answer:**
Describe a canary deployment. Traffic is gradually shifted to the new version, for example 10%, then 20%, and eventually 100%, based on the rollout plan. This differs from blue-green in that the session described blue-green as running two full environments in parallel.

### Scenario 21: Your GKE cluster creation is taking time. How long did the hands-on session indicate the process may take?

**Answer:**
The session indicated approximately 13 minutes when creating the cluster using the automation command through Cloud Shell. Actual time can depend on the cluster configuration and cloud environment.

### Scenario 22: You need to configure a cluster with a daily backup and seven-day retention. What RPO example was discussed?

**Answer:**
The session used daily backups with seven-day retention as an example and described the RPO as one day. Retention describes how long backups are kept; RPO describes the potential data-loss window.

### Scenario 23: Your platform team wants to identify production worker nodes for operational policies. What cluster configuration feature from the session can help?

**Answer:**
Use node metadata or labels. The session gave `env=prod` as an example and discussed labels for node identification and use in areas such as access control and firewall rules.

### Scenario 24: Your security team asks about enabling additional boot-time protection on worker nodes. Which option was covered?

**Answer:**
Secure Boot was covered as an optional setting. The session described it as enabling malware and rootkit security checks.

### Scenario 25: You need to remove a test pod after completing a lab. Which command should you use?

**Answer:**
First identify the pod name:

```bash
kubectl get pods
```

Then delete it:

```bash
kubectl delete pod <pod-name>
```

Verify the result again with `kubectl get pods`.

### Scenario 26: You need to monitor a Service while waiting for its external IP assignment. Which command is useful?

**Answer:**
Run:

```bash
kubectl get svc -w
```

The `-w` flag watches the output continuously, allowing you to observe changes while the Service is being provisioned.

### Scenario 27: A cluster has many worker nodes, and the team asks how to choose CPU and memory for them. What factors did the session cover?

**Answer:**
Choose node size based on application workload and its CPU/RAM needs. The session discussed examples such as 8-core, 16-core, and 32-core nodes, and noted that nodes in the cluster use the configured node size.

### Scenario 28: You need to explain the role of kube-proxy during an architecture interview. What is a clear answer?

**Answer:**
Kube-proxy handles networking for Kubernetes Services and helps route traffic to workloads inside the cluster. The session connected this role with exposing pods to internal cluster traffic or external access through the appropriate Service configuration.

### Scenario 29: A pod keeps failing to pull its image. Which worker-node component should you mention, and how can you observe its retry activity?

**Answer:**
Mention kubelet. It is the worker-node agent responsible for carrying out pod and container operations, including pulling images. It retries failed operations and generates events. Inspect those events with:

```bash
kubectl describe pod <pod-name>
```

### Scenario 30: You are preparing a basic Kubernetes operational checklist after creating a GKE cluster. Which commands from the session would you include?

**Answer:**
A basic checklist based on the session is:

```bash
kubectl cluster-info
kubectl get nodes
kubectl get pods
kubectl get pods -o wide
kubectl get svc
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

Use `kubectl cluster-info` and `kubectl get nodes` to verify cluster connectivity and nodes, `get pods` and `get svc` to inspect workloads and Services, and `describe` or `logs` to investigate a workload.

---

## Quick Revision: Important Commands

| Command | Use |
|---|---|
| `kubectl cluster-info` | Display cluster endpoint information |
| `kubectl get nodes` | List worker nodes |
| `kubectl run nginx --image=nginx:latest` | Create an NGINX pod |
| `kubectl get pods` | List pods |
| `kubectl get pods -o wide` | Show pods with additional details, including node assignment |
| `kubectl get svc` | List Services |
| `kubectl get svc -w` | Watch Service changes, including external IP assignment |
| `kubectl describe pod <pod-name>` | Inspect pod details and events |
| `kubectl logs <pod-name>` | View container logs when available |
| `kubectl delete pod <pod-name>` | Delete a pod |

## Student Practice Tasks

1. Create a GKE Standard cluster and connect to it using the `gcloud container clusters get-credentials` command shown in the GKE Console.
2. Verify the cluster connection with `kubectl cluster-info`.
3. List the worker nodes using `kubectl get nodes`.
4. Create an NGINX pod using `kubectl run nginx --image=nginx:latest`.
5. Inspect the pod using both `kubectl get pods` and `kubectl get pods -o wide`.
6. Expose the NGINX workload using a LoadBalancer Service and watch the Service with `kubectl get svc -w`.
7. Intentionally investigate an image-pull failure in a lab environment using `kubectl describe pod <pod-name>`.
8. Explain the difference between ClusterIP, NodePort, and LoadBalancer without referring to notes.
9. Draw the control-plane and worker-node architecture and explain the role of every component.
10. Revise the Docker and Kubernetes topics covered in the current and previous sessions.

> **Note:** These questions and answers are based on the topics and explanations recorded in this Day 2 session. Where the session did not provide a detailed implementation command or configuration, the answer stays at the conceptual level rather than adding unverified steps.
