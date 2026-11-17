## Key Outcomes

Day 5 of the Docker & Day-0 of K8s series covered three major areas: Docker AI (Gordon) in agentic mode, Docker container restart policies, and a foundational introduction to Kubernetes. The session's centerpiece was a live demonstration where Gordon autonomously created a containerized "Ladakh Road Trip Booking" web application — generating the Dockerfile, HTML content, nginx configuration, and troubleshooting port conflicts without manual intervention. The instructor emphasized that while Gordon handles execution, learners must be able to read and understand the full output logs to use it effectively in interviews and real-world scenarios. The session concluded with a hands-on Kubernetes cluster setup walkthrough on Google Cloud (GKE), establishing the foundation for upcoming Kubernetes classes.

---

## Decisions Made

- **Docker AI (Gordon) is the preferred interface** for development-environment Docker workflows; CLI usage (`docker ai`) is recommended over the Desktop UI for interview-readiness 
- **Gordon requires Docker Desktop 4.7+** (demonstrated with v4.99); Docker Desktop is free for development and learning purposes 
- **Database/backend should not be containerized** in production; use managed platform-as-a-service (e.g., AWS RDS) instead, as container restarts risk data corruption 
- **GKE (Google Kubernetes Engine) standard cluster** is the recommended Kubernetes environment for learning and real-time use over local options (K3S, Minikube, KubeADM) 
- **One node per zone** (instead of the default 3) and **30 GB disk** should be selected in GKE free-tier accounts to stay within quota limits 
- **Docker container restart policy default is "no"** (do not restart automatically); always-restart or on-failure policies must be explicitly set 
- **Kubernetes version 1.36** is the production standard at most companies; 1.37 is the latest but 1.36 is the focus for this course 

---

## Docker AI — Gordon (Agentic Mode)

### What Gordon Is

- **Docker Gordon** is an AI-powered assistant embedded in Docker Desktop that acts on Docker workflows using natural language prompts 
- Operates in **agentic mode**: autonomously plans, executes, troubleshoots, and iterates until the task is complete — unlike ChatGPT, which only generates text 
- Accessible two ways: Docker Desktop UI (first option in the sidebar) or CLI command `docker ai "[prompt]"` — both are equivalent; CLI preferred for interviews 
- Requires **Docker Desktop sign-in**; some versions work without login but current versions require it 

### Live Demo — Ladakh Road Trip Application

- **Prompt given:** Create a Dockerfile, generate HTML code for a road trip application for Ladakh Travel Booking (Batch 45 CloudDevOpsHub), and run the application 
- **Gordon's autonomous plan and execution:**
    - Created project directory structure and `index.html` (397 lines of HTML content with travel details, booking UI, and lead capture form) 
    - Wrote a Dockerfile using **Alpine-based nginx image** (optimized for lightweight deployment) 
    - Created a `.dockerignore` file to exclude unnecessary files from the image 
    - Built the Docker image 
    - Attempted to run container on port 80 → **failed** (port already in use by Jenkins) 
    - Diagnosed the conflict via `docker ps -a`, checked listening ports, renamed the container (previous name conflict), and created a custom `nginx.conf` to remap from port 80 to port 9090 
    - Rebuilt image with updated nginx configuration, ran container successfully on **port 9090** 
    - Verified with `docker ps` and container health inspection 
- **Final output:** Application accessible at `localhost:9090` displaying a fully designed Ladakh travel booking page 

### Key Troubleshooting Steps Gordon Performed (Interview-Relevant)

- Checked `docker ps -a` to identify running containers and port allocations 
- Identified port 80 occupied by Jenkins; remapped via nginx config (not just `EXPOSE` mapping) 
- Resolved duplicate container name error by renaming 
- Read and acted on nginx HTTP block configuration errors from logs 
- Used `docker inspect` with `HostConfig.RestartPolicy.Name` to verify container state 

### Critical Lesson for Learners

- Gordon's agentic loop runs until the task is complete, asking confirmation before destructive actions (e.g., overwriting files, removing containers) 
- **Learners must read all log output line by line** — understanding what Gordon did and why is essential for interviews; simply running the tool without comprehension is insufficient 
- In interviews, candidates should explain Docker operations using commands, not just say "Gordon did it" 

### Gordon Capabilities Beyond the Demo

- Review Dockerfiles for best practices and optimize them 
- List all local images and their sizes 
- Show logs for a specific container: `docker ai "show me the logs for my [container] container"` 
- Future support: MCP server integration, Kubernetes-related troubleshooting (currently Docker-scoped, Kubernetes in beta) 
- **Models available** include Ollama and others; skill sets (e.g., Docker expert, Kubernetes expert) can be added as model configurations 

---

## Docker Advanced Concepts

### Container Restart Policies

Four restart policy options for Docker containers: 

|      Policy      |                   Behavior                   |
|------------------|----------------------------------------------|
| `no`             | Never restart automatically (default)        |
| `always`         | Always restart, including after VM reboot    |
| `unless-stopped` | Restart unless explicitly stopped by user    |
| `on-failure`     | Restart only when container exits with error |

- **Recommended default:** `no` — do not restart automatically 
- To update the policy on an **existing container**: use `docker update --restart=[policy] [container]` 
- To verify current policy: run `docker inspect [container]` and search for `HostConfig.RestartPolicy.Name` 

### Docker + VM Restart Behavior

- Container restart policy operates at the **container level**; if Docker daemon itself is not running after a VM reboot, container policies have no effect 
- To ensure Docker starts automatically on VM reboot: **enable Docker as a system service** (`systemctl enable docker`), so Docker starts first, then container policies apply 
- This is a **common interview scenario question**: "What happens to containers if the VM restarts?" 

### Dockerfile — CMD vs ENTRYPOINT (Q&A Clarification)

- Running nginx without `-d` flag runs it in the **foreground**; `-d` sends it to the background 
- If both `ENTRYPOINT` and `CMD` are defined in a Dockerfile, **ENTRYPOINT takes strict precedence** — CMD instructions are overridden 
- This is a catch to be aware of when using Gordon-generated Dockerfiles that include both directives 

### Multi-Stage Dockerfile

- Multiple `FROM` statements create a multi-stage build; intermediate stages can be named with `AS [alias]` (e.g., `AS builder`) 
- **DevSecOps benefit:** Multi-stage builds allow removal of vulnerabilities — shell access, `wget`, unnecessary utilities can be stripped from the final image 
- All stages run on the **same VM**; no separate VMs required per stage 

### Docker Hub Image Storage

- **Free tier:** 1 private image; **unlimited public images** 
- No credit card concerns for public image storage 

---

## Kubernetes — Foundational Concepts

### Core Terminology (Interview Definitions)

- **Node:** A virtual machine (VM) with software/application installed on it. In company context, once Docker or any application is installed on a VM, it is called a node. "VM down" = infra problem; "Node down" = application problem. 
- **Container:** A portable, lightweight, executable unit created from a Docker image. Contains the application, OS dependencies, binaries, libraries, and configuration — everything needed to run. 
- **Cluster:** A collection of nodes (VMs) working together. Minimum 1 worker node required; real-time production requires minimum 3 nodes. Analogous to individual fingers (nodes) forming a palm (cluster). 
- **kubectl:** The command-line utility to communicate with and control a Kubernetes cluster. Equivalent to a translator between the user and the cluster. 
- **Kubelet:** An agent running on every node that passes node health and status information to the master node. It is not a controller — it only reports information upward. 
- **Control Plane:** The master node component managing the cluster; confirmed running via `kubectl cluster-info` 
- **CoreDNS:** Internet-facing DNS component within the Kubernetes cluster 

### Types of Kubernetes Clusters

|                Type                |         Use Case          |                             Notes                              |
|------------------------------------|---------------------------|----------------------------------------------------------------|
| **GKE** (Google Kubernetes Engine) | Cloud / Production        | Fully managed, recommended for learning                        |
| **EKS** (AWS)                      | Cloud / Production        | Valid alternative                                              |
| **AKS** (Azure)                    | Cloud / Production        | Also valid                                                     |
| **KubeADM**                        | On-premises / Data center | Fully manual setup; all components installed one by one        |
| **K3S**                            | Lightweight / Local       | Missing full features; not recommended for production learning |
| **Kind**                           | Local / Docker-based      | Lightweight; Docker-preferred for local Kubernetes             |
| **Minikube**                       | Local                     | Learning only; not production-suitable                         |

- **Interview answer:** For real-time company use, answer should reference **GKE, EKS, or AKS** (cloud-managed). KubeADM is the on-premises answer. 
- **Managed Kubernetes:** Cloud provider manages the control plane, APIs, and infrastructure; the team only manages application resources 

### Running Kubernetes Locally (Docker Desktop)

- Enable via Docker Desktop → Settings → Kubernetes → Enable Kubernetes → Apply 
- Components download automatically; `kubectl` becomes available in terminal 
- Verify with: `kubectl get nodes` and `kubectl cluster-info` 
- **Prerequisite:** Virtualization must be enabled in CPU (Task Manager → CPU → Virtualization: Enabled); 50%+ free memory recommended 
- Local Kubernetes is acceptable for demonstration but **cloud (GKE) is strongly preferred** for actual learning 

### GKE Cluster Creation Walkthrough

- Navigate: Google Cloud Console → Kubernetes Engine → Clusters → Create 
- Select **Standard** (not Autopilot) 
- **Free-tier settings:** 1 node per zone (change default from 3), 30 GB disk per node 
- Cluster creation takes a few minutes; delete after use as it is **chargeable** 
- Kubernetes version running in demo: **1.36** 

### Kubernetes Ecosystem Note

- Kubernetes has an extensive ecosystem (Prometheus, Grafana, KEDA, Trivy, Helm, etc.); the course will cover deployment, configuration, services, volumes, namespaces, daemon sets, and stateful sets 
- **KEDA** (event-driven autoscaling) is planned to be included in the course curriculum 

---

## Interview Preparation Highlights

- **Docker AI / Gordon questions:** Be able to explain what Gordon is, how agentic mode differs from generative AI, and walk through troubleshooting steps it performed (port conflict resolution, log reading, nginx config creation) 
- **LinkedIn opportunity:** Gordon-related job requirements are emerging; HR teams are searching for candidates with this skill — post about it on LinkedIn 
- **Kubernetes cluster type question:** Always answer with the cloud-managed variant used at your company (GKE/EKS/AKS); for on-premises, answer KubeADM 
- **Resume alignment:** Interviewers start from your resume and introduction — only mention technologies you have actually practiced 
- **Practical experience is non-negotiable:** Zero hands-on practice = not eligible for entry-level roles; even small practicals count 
- **Day-to-day Docker commands** (build, tag, commit, run, start, stop, load, push, pull) must be fluent — these form the basis of interview answers 

---

## Action Items

- **All students:** Download Docker Desktop (595 MB) from the official link; ensure virtualization is enabled and 50%+ CPU memory is free before installation 
- **Students with WSL errors:** Manually enable WSL via Windows Features ("Turn Windows features on or off") or install the MSF/WSL package via PowerShell as administrator 
- **All students:** Practice reading Gordon's full output logs for the Ladakh demo — understand each step Gordon took and why 
- **All students:** Create a GKE standard cluster (1 node per zone, 30 GB disk), run `kubectl get nodes` and `kubectl cluster-info`, then **delete the cluster immediately** to avoid charges 
- **Vikas:** Add Docker container restart policy content to Day 16 notes and PPT 
- **Vikas:** Draw and share a Kubernetes cluster architecture diagram in the next class 
- **Vikas:** Set up MCP server in Kubernetes for upcoming session; Gordon integration will be visible once configured 
- **Vikas:** Fix billing/permissions for GKE cluster access sharing; revisit in tomorrow's class 
- **Students interested in Q&A platform:** Vikas to add 3-level difficulty tiers (fresher/experienced/all) and leaderboard to the quiz form 

---

## Open Questions

- How to access Gordon logs after Docker Desktop is shut down; log storage location on local drive not fully resolved during session 
- Srinivasan's question about a specific Docker/Kubernetes topic was noted as pending — follow-up via WhatsApp requested 
- Shankar's organization uses **Buildah** (B-U-I-L-D-A-H) as a separate lightweight OCI image-building tool (not Docker-native); identified as a separate tool potentially used in OpenShift environments — full explanation deferred 
- Percona Postgres Operator (PMM) on Kubernetes cluster type (on-premises KubeADM suspected) — Shankar to check internal documentation for cluster setup method


---

# Interview Preparation — 20 Questions and Answers

These questions are based on the Day 5 topics: Docker AI (Gordon), Docker troubleshooting, restart policies, Dockerfile concepts, and Kubernetes fundamentals.

### 1. What is Docker Gordon?
**Answer:** Docker Gordon is an AI-powered assistant embedded in Docker Desktop. It accepts natural-language prompts and can plan and execute Docker-related tasks, troubleshoot issues, and iterate until the task is complete.

### 2. What is the difference between Docker Gordon's agentic mode and a normal generative AI chatbot?
**Answer:** A normal generative AI chatbot generally provides text, suggestions, or commands. Gordon can take action in the Docker environment: it can inspect the setup, create files, build images, run containers, read errors, and attempt fixes.

### 3. How can you access Docker Gordon?
**Answer:** Gordon can be accessed from the Docker Desktop interface or through the CLI using a command such as:
```bash
docker ai "show me the logs for my nginx container"
```
The CLI is useful to practise because it also helps build command-line confidence for interviews.

### 4. What is the minimum Docker Desktop version mentioned for Gordon in this session?
**Answer:** The session notes specify Docker Desktop 4.7 or later. The demonstration used Docker Desktop v4.99.

### 5. What steps would you take if a container fails because port 80 is already in use?
**Answer:** First, identify the process or container using the port. Check running containers with `docker ps` and inspect host processes or listening ports. Then stop or reconfigure the conflicting service, or map the application to another available host port. In the Ladakh demo, Jenkins was using port 80 and the application was moved to port 9090 with an nginx configuration change.

### 6. What is the purpose of `docker ps -a`?
**Answer:** It lists all containers, including running and stopped containers. It is useful when troubleshooting container-name conflicts, checking container status, and identifying which containers are already present.

### 7. What is the difference between `EXPOSE` and Docker port publishing?
**Answer:** `EXPOSE` documents the port the application is expected to listen on inside the container. It does not publish that port on the host. Port publishing is done with `-p`, for example:
```bash
docker run -d -p 9090:80 nginx
```
Here, host port 9090 forwards traffic to container port 80.

### 8. What are Docker restart policies?
**Answer:** Restart policies control whether Docker automatically restarts a container after it exits. The policies covered in the session are `no`, `always`, `unless-stopped`, and `on-failure`.

### 9. What is the default Docker restart policy?
**Answer:** The default is `no`. Docker does not automatically restart the container when it exits.

### 10. Explain `always` and `unless-stopped`.
**Answer:** `always` tells Docker to restart the container whenever it stops, including after a Docker daemon restart. `unless-stopped` also restarts it, except when the container was explicitly stopped by a user; that intentional stop is respected.

### 11. How do you change the restart policy of an existing container?
**Answer:** Use `docker update`:
```bash
docker update --restart=unless-stopped <container_name>
```
You can replace `unless-stopped` with another supported policy.

### 12. How do you verify a container's restart policy?
**Answer:** Inspect the container and check `HostConfig.RestartPolicy.Name`:
```bash
docker inspect <container_name>
```
For a concise output:
```bash
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' <container_name>
```

### 13. What must be configured so containers can restart after a VM reboot?
**Answer:** The Docker service must start automatically when the VM boots, and the containers must have an appropriate restart policy. On a systemd-based Linux VM:
```bash
sudo systemctl enable docker
sudo systemctl start docker
```
The restart policy is applied by Docker after the daemon is running.

### 14. What is a multi-stage Docker build?
**Answer:** A multi-stage build uses multiple `FROM` instructions in one Dockerfile. One stage can compile or prepare the application, and a later stage can copy only the required output into a smaller runtime image. Stages can be named with `AS`, such as `FROM ... AS builder`.

### 15. How can multi-stage builds help with DevSecOps?
**Answer:** They help reduce the final image by leaving build tools and unnecessary utilities out of the runtime stage. A smaller runtime image can have a reduced attack surface and fewer packages that need security updates.

### 16. What is a Docker container?
**Answer:** A container is a lightweight, portable, executable instance created from an image. It packages an application with the dependencies and configuration needed to run.

### 17. What is a Kubernetes node?
**Answer:** A node is a machine, usually a virtual machine in a cloud environment, that runs Kubernetes workloads. A cluster is made up of one or more nodes.

### 18. What is a Kubernetes cluster?
**Answer:** A Kubernetes cluster is a group of machines managed by Kubernetes. It includes the control plane and worker-node capacity used to run applications.

### 19. What are `kubectl` and `kubelet`?
**Answer:** `kubectl` is the command-line tool used by an administrator or developer to communicate with a Kubernetes cluster. `kubelet` is the agent on each node that makes sure the required Pods are running and reports node and workload status.

### 20. What is the difference between GKE, EKS, AKS, and KubeADM?
**Answer:** GKE is Google Cloud's managed Kubernetes service, EKS is AWS's managed Kubernetes service, and AKS is Azure's managed Kubernetes service. KubeADM is a tool used to bootstrap Kubernetes clusters, commonly in self-managed or on-premises environments.

---

# Scenario-Based Interview Questions — 20 Questions and Answers

### 1. Your nginx container does not start because port 80 is already occupied by Jenkins. How will you resolve it?
**Answer:**
1. Check running containers:
   ```bash
   docker ps
   ```
2. Check which host process is listening on port 80:
   ```bash
   sudo ss -lntp | grep ':80'
   ```
3. Keep Jenkins running if it is required.
4. Publish nginx on another free host port, for example:
   ```bash
   docker run -d --name nginx-web -p 9090:80 nginx
   ```
5. Test `http://localhost:9090`.

If the application itself is configured to listen on a different port inside the container, update the application or nginx configuration and make the port mapping match it.

### 2. A container is running, but users cannot access the application from the host. What will you check?
**Answer:** Check whether the container is running with `docker ps`, inspect its port mapping, and review its logs. Confirm that the application is listening on the expected interface and container port. If the port was not published, recreate the container with the correct `-p host_port:container_port` mapping.

### 3. A container stops whenever the application process exits. The requirement is to restart it after a failure. What will you do?
**Answer:** Set an `on-failure` restart policy:
```bash
docker run -d --restart=on-failure:5 --name app <image_name>
```
For an existing container:
```bash
docker update --restart=on-failure:5 app
```
This example allows Docker to retry up to five times when the container exits with a non-zero status.

### 4. Your Linux VM reboots, but the application container does not come back. What could be wrong?
**Answer:** Check both the Docker service and the container restart policy:
```bash
sudo systemctl status docker
sudo systemctl enable docker
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' <container_name>
```
If required, set a policy such as `unless-stopped`:
```bash
docker update --restart=unless-stopped <container_name>
```
A container restart policy cannot help while the Docker daemon itself is not running.

### 5. A container was intentionally stopped by an administrator. It should remain stopped after a VM reboot. Which policy is suitable?
**Answer:** Use `unless-stopped`. It restarts containers after daemon or host restarts unless they were explicitly stopped by a user.

### 6. You run `docker run` and receive a container-name conflict error. How will you fix it?
**Answer:** Find the existing container:
```bash
docker ps -a
```
If the old container is no longer needed, remove it:
```bash
docker rm <old_container_name>
```
Or choose a different name for the new container:
```bash
docker run -d --name nginx-web-2 nginx
```
Do not remove a container until you have confirmed it is safe to do so.

### 7. Gordon generated a Dockerfile and application, but the container fails to start. What should you do before asking Gordon to make another change?
**Answer:** Read the complete output and error logs. Check the generated Dockerfile, image build result, container status, port mappings, and application or nginx configuration. Use commands such as:
```bash
docker ps -a
docker logs <container_name>
docker inspect <container_name>
```
Understand the failure before accepting an automated change.

### 8. Gordon wants to overwrite an existing file or remove a container while completing a task. What should you do?
**Answer:** Review the proposed destructive action and confirm that the file or container is safe to replace or remove. Take a backup or preserve required data first. The session highlighted that Gordon asks for confirmation before destructive actions; users should still understand the impact before approving.

### 9. You need to run a web application in a small Docker image. How would you approach the Dockerfile?
**Answer:** Use a lightweight runtime image appropriate for the application. For a static website, an nginx Alpine image can be a suitable option. Copy only the required website files and configuration into the image, and use a `.dockerignore` file to exclude unnecessary files from the build context.

### 10. Your final Docker image contains compilers and build utilities that are not needed at runtime. How can you improve it?
**Answer:** Use a multi-stage Dockerfile. Build the application in a builder stage, then copy only the compiled output or runtime files into a clean final stage. This reduces image size and avoids carrying unnecessary build tools into production.

### 11. A learner says, "I know Docker because Gordon created my image and container." How would you test whether they really understand Docker?
**Answer:** Ask the learner to explain and demonstrate the generated Dockerfile, image build, container run command, port mapping, logs, and troubleshooting steps. They should be able to explain why each command was used rather than relying only on Gordon.

### 12. You have to choose a Kubernetes environment for a cloud production workload. Which option would you recommend?
**Answer:** Choose a managed Kubernetes service that matches the cloud provider: GKE for Google Cloud, EKS for AWS, or AKS for Azure. The cloud provider manages important control-plane components, while the team manages its application resources and workloads.

### 13. Your organization runs Kubernetes in its own data center and wants to manage the cluster components itself. Which setup approach can you discuss?
**Answer:** KubeADM is a common option for bootstrapping a self-managed Kubernetes cluster. The organization is responsible for planning, installing, configuring, and maintaining the cluster infrastructure and components.

### 14. You enabled Kubernetes in Docker Desktop, but no node appears. What will you check?
**Answer:** Confirm Kubernetes is enabled in Docker Desktop and allow the required components to finish starting. Check that CPU virtualization is enabled, that the machine has sufficient free memory, and then run:
```bash
kubectl get nodes
kubectl cluster-info
```
If the commands fail, inspect the Docker Desktop Kubernetes status and error messages.

### 15. You created a GKE cluster for practice and have finished the lab. What should you do?
**Answer:** Delete the cluster when it is no longer required. The session notes specifically warn that GKE resources are chargeable and recommend deleting the practice cluster immediately after completing the exercise.

### 16. Your GKE free-tier learning account is hitting resource quota limits during cluster creation. Which settings were recommended in the session?
**Answer:** For the learning walkthrough, the session recommended a Standard cluster with one node per zone instead of the default three, and a 30 GB disk per node. Always check the current quota and pricing for the selected region and machine type before creating resources.

### 17. A team member says kubelet is the component that schedules Pods across the cluster. Is that correct?
**Answer:** No. Kubelet runs on each node and makes sure the Pods assigned to that node are running. Scheduling is handled by the Kubernetes scheduler, which is part of the control plane.

### 18. An interviewer asks how you would confirm that your Kubernetes cluster is reachable. Which commands would you use?
**Answer:**
```bash
kubectl cluster-info
kubectl get nodes
```
`kubectl cluster-info` displays cluster endpoint information, while `kubectl get nodes` lists the nodes and their readiness status.

### 19. Your application needs persistent production database storage. Would you place the database inside a general-purpose application container as described in the session?
**Answer:** The session's recommendation was to avoid containerizing the production database for this learning architecture and to use a managed database platform such as AWS RDS. The reason discussed was that container restarts and poorly designed persistence can put data at risk. In real environments, database containers can be used when storage, backups, recovery, and operations are designed appropriately.

### 20. Your company uses GKE, and an interviewer asks you to explain the basic flow from a command to a running workload. What would you say?
**Answer:** I use `kubectl` to send commands to the Kubernetes API. The control plane manages the desired state of the cluster, and the scheduler selects a suitable node for a Pod. The kubelet on that node works with the container runtime to make sure the assigned workload runs. I then use commands such as `kubectl get nodes` and `kubectl get pods` to check the cluster and workload status.

---

## Quick Practical Revision Commands

Run these commands in your lab and be ready to explain the output.

```bash
# List running containers
docker ps

# List all containers
docker ps -a

# View container logs
docker logs <container_name>

# Inspect a container
docker inspect <container_name>

# Check a container's restart policy
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' <container_name>

# Update restart policy
docker update --restart=unless-stopped <container_name>

# Run nginx and publish host port 9090 to container port 80
docker run -d --name nginx-web -p 9090:80 --restart=unless-stopped nginx

# Enable Docker at system startup (systemd-based Linux)
sudo systemctl enable docker

# Check Docker service status
sudo systemctl status docker

# Check Kubernetes cluster
kubectl cluster-info

# List Kubernetes nodes
kubectl get nodes

# List Kubernetes Pods across namespaces
kubectl get pods -A
```

## Interview Preparation Tip

Do not memorize only the answers. Practise the commands, reproduce the port-conflict and restart-policy scenarios, and explain what each command changes. For Gordon-related questions, be ready to describe the actions it performed and the logs you used to verify the result.
