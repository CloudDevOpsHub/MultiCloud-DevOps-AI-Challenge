# 🚀 DevOps Batch-44 | Day 13: Docker Multi-Stage Builds, Volumes & Networking

[![Module: Docker Containerization](https://img.shields.io/badge/Module-Docker-2496ED?style=for-the-badge&logo=docker)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 6th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-6th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


## 📋 Key Outcomes

Day 4 of the Docker training session covered advanced real-world Docker workflows, introduced the Docker AI assistant (Gordon), and demonstrated practical commands for container lifecycle management, image optimization via multi-stage builds, and system cleanup. Participants practiced hands-on operations including running containers, committing containers back to images, saving images as TAR files, and running multiple containers via for loops. The session concluded with a Q&A covering troubleshooting, interview preparation, and real-time production scenarios.

### 💡 Key Decisions Made

- Docker AI (Gordon) will be used as a supplemental learning and productivity tool; participants instructed to post LinkedIn updates about Docker AI usage.
- Local machine chosen for today's practical over cloud to demonstrate developer-environment parity and avoid cloud costs, provided the machine has at least 50% free CPU/memory.
- Alpine base image confirmed as the preferred lightweight base for optimized Docker images over Ubuntu, unless the workload requires otherwise.
- Databases should not be containerized in the same manner as applications; containers are ephemeral and unsuitable for persistent database storage.
- Multi-stage Docker builds adopted as the standard optimization technique to reduce image size and build time.

## 🔑 Core Concepts Taught

- Docker Container Lifecycle
- Container states: Pending → Running → Exited/Killed/Stopped; a container moves from pending to running in ~3–5 seconds.
- Most common real-time failure cause: OOM (Out of Memory) — when too many users hit an application simultaneously, the container exhausts memory and dies or restarts.
- Real-world example shared by Chander: a finance application ("Garvin") became inaccessible to 15+ users (P1 incident); root cause identified via docker inspect and exit code 137 (OOM kill), resolved by checking logs and raising an RCA.
- Key Commands Demonstrated
- Command
- Purpose


```bash
docker ps
```

- List running containers


```bash
docker inspect <id>
```

- Inspect container state, errors, network config


```bash
docker logs <id>
```

- View container logs for troubleshooting


- docker stats
- Live CPU/memory usage for all running containers


- docker top <id>
- Process-level view for a single container (similar to Linux top)


- docker commit <id> <name>
- Convert a running container back into an image


- docker save <image> > file.tar
- Save image as TAR backup file


- docker load
- Restore image from TAR file


- docker system df
- Show disk usage: images, containers, cache


- docker system prune
Remove stopped containers, unused networks, dangling images, unused build cache


- docker image prune -a
- Remove all unused images (freed 414 MB in demo)



- docker stats vs docker top: stats monitors all running containers simultaneously; top inspects processes within one specific container.
- Running commands without entering a container: Use docker exec <container> <command> without -it flag — a noted interview question.

- Docker AI — Gordon
- Gordon is Docker's built-in AI development assistant, accessible via terminal by typing docker ai.
- Capabilities demonstrated:
- Query running containers in plain English (e.g., "How many containers are running?") — Gordon translates to docker ps internally.
- Create and run containers from natural language prompts (e.g., "Create a hello world container").
- Generate complete Dockerfiles from prompts (e.g., "Create a Dockerfile for an NGINX server with container name Saurav exposed on port 80").
- Execute multi-step Docker workflows including build and run commands.
- Anjan's experiment: Used Gordon to customize the NGINX index.html to create a DevOps engineer portfolio page — confirmed working after a browser refresh.
- Gordon works fully offline (no internet required after Docker Desktop installation) and requires no subscription.
- Available via Docker Desktop UI (enable in settings) or CLI (docker ai).
- Requires the latest version of Docker Desktop; older versions need to be updated to see the Gordon option.

- Image Optimization — Multi-Stage Builds
- Problem: A bloated image using Ubuntu + manual Python installation reached 1.22 GB.
- Solution — Multi-Stage Build:
- Stage 1 (builder): Uses python:slim base image instead of full Ubuntu; installs only what's needed.
- Stage 2: Calls COPY --from=builder (line 16 in the demo Dockerfile) to pull only the compiled artifacts from Stage 1.
- Optimized image size: 394 MB — approximately 60–70% reduction.
- Why it works: The slim base image eliminates unnecessary OS packages; the second stage discards build-time dependencies, keeping only the runtime artifact.
- Code retrieved via git clone from the instructor's GitHub Docker repository for live demonstration.

- Advanced Container Operations
- Container → Image (Commit)
- A running container can be converted back to an image using docker commit <container_id> <new_image_name>.
- Demonstrated live: container ID copied → committed as image named "Shubhendu" → visible in docker images.
- Image Backup and Restore
- docker save <image> > avinash.tar — saves image as a TAR file (created in ~37 seconds in demo).
- TAR file can be stored in AWS S3 (object storage) for backup purposes; TAR is an object, S3 is object storage.
- docker load restores the TAR back into a Docker image.
- Running Multiple Containers with a Loop
- For loop command demonstrated to spin up 10 Alpine containers simultaneously from a single image and single command.
- Each container runs for 1000 seconds then exits; --rm flag ensures automatic cleanup post-exit.
- Alpine chosen as the smallest available image to avoid overwhelming local machine CPU/memory.
- Confirmed: hundreds or thousands of containers can run on a single VM.
- System Cleanup (Housekeeping)
- docker system df revealed: 9 total images (6 active, 3 unused); 19 total containers (15 active, 4 unused).
- docker system prune confirmed removal of: all stopped containers, unused networks, dangling images, unused build cache — requires manager/client/downtime approval before running in production.
- This activity is called "housekeeping" in enterprise environments.

- Real-Time Troubleshooting Workflow
1.      Inspect: docker inspect <container> — first command to run; reveals errors, exit codes, network state.
2.      Logs: docker logs <container> — check application logs to identify root cause.
3.      Stats: docker stats — monitor live CPU/memory consumption across all containers.
4.      Exit Code 137: Indicates OOM kill — container was terminated by the kernel due to memory exhaustion.
5.      Scaling Solution: For persistent OOM issues at scale, use HPA (Horizontal Pod Autoscaling) in Kubernetes to automatically increase pod count.

- Docker Desktop Installation (Local Setup)
- Download from the official Docker Desktop for Windows link shared in session.
- During install: select "Use WSL instead of Hyper-V" option → click OK.
- WSL required: If not installed, run wsl --install and wsl --update in PowerShell (admin).
- Virtualization not detected error: Caused at OS level (not BIOS); WSL handles this layer.
- Machine restart required after WSL installation for Docker Desktop to function correctly.
- After install: enable Kubernetes in Docker Desktop Settings → Kubernetes → Enable → Restart.
- Gordon (AI) must be enabled separately in Docker Desktop settings.
- Minimum recommended: 50% free CPU/memory; systems with insufficient resources will struggle.

- Interview Questions Covered
- What is OOM / Exit Code 137? → Out of Memory kill; container terminated due to memory exhaustion.
- How long does it take to start a container? → Milliseconds to seconds (use time docker run hello-world in Linux/Git Bash to measure; Windows CMD does not support the time command).
- How to run commands in a container without entering it? → docker exec <container> <command> without -it.
- Can you convert a container back to an image? → Yes, using docker commit.
- How to reduce Docker image size? → Use slim/Alpine base images; implement multi-stage builds.
- What is the cleanup command? → docker system prune (called "housekeeping activity" in enterprise).
- Docker exec vs Docker run? → exec runs commands in an existing running container; run creates a new container from an image.
- How to troubleshoot a slow containerized application? → Check docker stats for CPU/memory; optimize the application; escalate to Kubernetes HPA if needed.

- Real-Time Production Scenario (Chander's Company)
- Application: Finance application ("Garvin") running in a container.
- Incident: 15+ users unable to access → P1 (Priority 1) raised.
- Investigation steps:
- a.      docker inspect → checked container state.
- b.      docker stats → CPU/memory at 100%.
- c.       Exit code 137 identified → OOM confirmed.
- d.      docker logs → reviewed application logs for RCA.
- Resolution: Root cause found; permanent fix implemented; HPA in Kubernetes recommended to prevent recurrence.

### ### Pending Confirmation / Open Items

- Docker Compose was mentioned but not covered in this session; deferred.
- Docker Networks scheduled for a future session (if time permits before/after Friday's resume building session).
- GCP Artifact Registry / pushing images to cloud registries (AWS ECR, GCP Artifact Registry) — briefly demonstrated (repository created in us-central1) but full workflow deferred.
- DevSecOps / image vulnerability scanning — Avinash raised the question about injected vulnerabilities in images; instructor confirmed this is a DevSecOps topic to be covered separately.
- Participants with Gordon installation issues (e.g., low RAM, unsupported config) advised to restart machine, update Docker Desktop, or use Git Bash for Linux commands.

### 📋 Action Items

- All participants: Do all today's practicals locally or on cloud; share screenshots in the WhatsApp group.
- All participants: Post Docker AI / Gordon practical results on LinkedIn with screenshots.
- All participants: Create or ensure a cloud account (AWS/GCP/Azure) exists for upcoming sessions.
- Participants with old Docker Desktop: Update to latest version to access Gordon.
- Participants without WSL: Run wsl --install + wsl --update in PowerShell admin, then restart machine.
- Instructor (Vikas): Conduct resume building session on Friday.
- Instructor (Vikas): Cover Docker Networks in an upcoming session.

- Announcements
- Tomorrow (Friday) is a Resume Building Session — not a technical Docker session.
- Instructor's Docker-related code and Dockerfiles are available on his GitHub repository for participants to clone and practice.
- Participants encouraged to use Gordon for office troubleshooting — can quickly diagnose and suggest fixes for real-world Docker issues without internet dependency.
- Companies are beginning to limit AI tool usage for employees who become over-dependent and lose independent troubleshooting skills — manual knowledge remains essential.
Based on the uploaded Day-4 Docker Batch-44 Summary, here are 20 Docker Interview Questions & Answers and 20 Real-Time Scenario-Based Interview Questions & Answers. These are strictly based on the session content.

- Docker Interview Questions & Answers (20)
### ❓ Q1: What is Exit Code 137 in Docker?

**💡 Answer:**

Exit Code 137 means the container was killed because of an Out Of Memory (OOM) condition. The Linux kernel terminates the container when it consumes more memory than available.

### ❓ Q2: Which command is used to inspect a container?

**💡 Answer:**

```bash
docker inspect <container_id>
```


It provides details like state, network configuration, exit code, mounts, and metadata.

### ❓ Q3: How do you check logs of a Docker container?

**💡 Answer:**

```bash
docker logs <container_id>
```


It helps identify application-level issues.

4. What is the difference between docker stats and docker top?
**💡 Answer:**

docker stats → Shows CPU and Memory usage of all running containers.
docker top → Displays running processes inside a specific container.

5. How do you execute a command inside a running container without entering it?
**💡 Answer:**

```bash
docker exec <container> <command>
```


Example:
```bash
docker exec nginx ls
```



6. What is Docker Gordon?
**💡 Answer:**

Gordon is Docker's built-in AI assistant that understands natural language and converts it into Docker commands.

7. Can Gordon work without internet?
**💡 Answer:**

Yes.
Once Docker Desktop is installed, Gordon works completely offline.

8. How do you convert a running container into an image?
**💡 Answer:**

docker commit <container_id> myimage


9. How do you backup a Docker image?
**💡 Answer:**

docker save myimage > backup.tar


10. How do you restore a Docker image?
**💡 Answer:**

docker load < backup.tar


11. What is a Multi-Stage Build?
**💡 Answer:**

A Docker optimization technique where one stage builds the application while another stage copies only the required artifacts, reducing image size.

12. Why are Alpine images preferred?
**💡 Answer:**

Because Alpine is very lightweight, making Docker images smaller, faster, and more secure.

13. Why shouldn't databases be treated like normal containers?
**💡 Answer:**

Containers are ephemeral.
Databases require persistent storage, so they need volumes or managed database services.

14. Which command removes unused Docker resources?
**💡 Answer:**

docker system prune


15. What does docker image prune -a do?
**💡 Answer:**

Deletes all unused Docker images.

16. Which command shows Docker disk usage?
**💡 Answer:**

docker system df


17. How do you monitor resource usage of containers?
**💡 Answer:**

docker stats


18. How can Docker AI generate Dockerfiles?
**💡 Answer:**

Using natural language prompts such as:
Create a Dockerfile for an NGINX server.
Gordon automatically generates the Dockerfile.

19. What is housekeeping in Docker?
**💡 Answer:**

Cleaning unused images, containers, build cache, and networks using cleanup commands.

20. What is the biggest advantage of Multi-Stage Builds?
**💡 Answer:**

Smaller image size
Faster deployment
Reduced attack surface
Better production performance

Docker Scenario-Based Interview Questions & Answers (20)
### 📌 Scenario 1

> **❓ Question:**

Your application suddenly stopped responding. Where will you start troubleshooting?
**💡 Answer:**

```bash
docker inspect
docker logs
```

docker stats
Check Exit Code
Identify root cause

### 📌 Scenario 2

> **❓ Question:**

Users complain the application becomes slow during peak traffic.
**💡 Answer:**

Check:
docker stats

If CPU or Memory is high, optimize the application or scale using Kubernetes HPA.

### 📌 Scenario 3

> **❓ Question:**

Your container exits automatically after startup.
**💡 Answer:**

Check:
```bash
docker inspect
docker logs
```


Look for Exit Code and application errors.

### 📌 Scenario 4

> **❓ Question:**

A production image is 1.2 GB. How would you optimize it?
**💡 Answer:**

Use Multi-Stage Build
Replace Ubuntu with Alpine or Slim images
Remove build dependencies

### 📌 Scenario 5

> **❓ Question:**

You modified files inside a running container and want to preserve them.
**💡 Answer:**

docker commit

Convert the container into a new image.

### 📌 Scenario 6

> **❓ Question:**

Your team wants to move Docker images to another server.
**💡 Answer:**

Use:
docker save
docker load


### 📌 Scenario 7

> **❓ Question:**

A developer accidentally deleted the original image but still has the running container.
**💡 Answer:**

Recover using:
docker commit


### 📌 Scenario 8

> **❓ Question:**

Your Docker server is running out of disk space.
**💡 Answer:**

Check:
docker system df

Then clean unused resources using:
docker system prune


### 📌 Scenario 9

> **❓ Question:**

You need to check running processes inside one container.
**💡 Answer:**

docker top


### 📌 Scenario 10

> **❓ Question:**

You need CPU usage of all containers.
**💡 Answer:**

docker stats


### 📌 Scenario 11

> **❓ Question:**

Your manager asks you to run Linux commands inside a container without opening an interactive shell.
**💡 Answer:**

```bash
docker exec nginx ls
```



### 📌 Scenario 12

> **❓ Question:**

A developer wants Docker commands generated automatically using AI.
**💡 Answer:**

Use Docker Gordon.
Example:
Create an nginx container exposing port 80.


### 📌 Scenario 13

> **❓ Question:**

A new developer has only 8 GB RAM. Which image should they use?
**💡 Answer:**

Use Alpine because it consumes fewer resources.

### 📌 Scenario 14

> **❓ Question:**

You need to start 20 identical containers quickly.
**💡 Answer:**

Use a shell loop to create multiple containers from the same image.

### 📌 Scenario 15

> **❓ Question:**

Your organization wants image backups stored in cloud storage.
**💡 Answer:**

Use:
docker save

Store the TAR file in Amazon S3 or another object storage service.

### 📌 Scenario 16

> **❓ Question:**

Your Docker Desktop doesn't show Gordon.
**💡 Answer:**

Update Docker Desktop
Enable Gordon in Docker Desktop settings
Restart Docker Desktop

### 📌 Scenario 17

> **❓ Question:**

Docker Desktop installation fails because WSL is missing.
**💡 Answer:**

Run:
wsl --install
wsl --update

Restart the machine and install Docker Desktop again.

### 📌 Scenario 18

> **❓ Question:**

A container is consuming 100% memory repeatedly.
**💡 Answer:**

Check docker stats
Analyze logs
Fix memory leak
Scale with Kubernetes HPA if required

### 📌 Scenario 19

> **❓ Question:**

Your manager asks why the application image uses Alpine instead of Ubuntu.
**💡 Answer:**

Because Alpine is lightweight, resulting in faster downloads, smaller images, lower storage usage, and quicker deployments.

### 📌 Scenario 20

> **❓ Question:**

A production server contains many stopped containers and unused images.
**💡 Answer:**

Perform housekeeping using:
docker system prune

(after obtaining the required production approvals).
These questions are aligned with the Day-4 Docker session topics, including Docker AI (Gordon), container lifecycle, troubleshooting, image optimization, housekeeping, and real-world production scenarios.




---
> [⬅️ Day 12](Day-12-Dockerfiles-Image-Creation.md) | [🏠 Master Learning Index](README.md) | [Day 14 ➡️](Day-14-Kubernetes-Fundamentals-Architecture.md)
