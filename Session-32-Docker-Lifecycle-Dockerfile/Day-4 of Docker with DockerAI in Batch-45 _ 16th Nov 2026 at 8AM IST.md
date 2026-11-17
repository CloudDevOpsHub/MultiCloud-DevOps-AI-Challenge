## Key Outcomes

Day 4 of the Docker training series covered Dockerfile syntax and optimization, day-to-day container operations, troubleshooting commands, and advanced multi-stage builds for image size reduction. The session built on Days 1–3 (Docker basics, container operations, and Dockerfile introduction) and focused on real-world DevOps workflows including tagging, pushing, committing, and cleaning up images and containers. A major practical demonstration compared a bloated 1.24 GB Python image against an optimized multi-stage build, illustrating how slim base images and staged builds reduce size, speed up CI/CD pipelines, and lower cloud storage costs. 

---

## Decisions Made

- **Dockerfile keyword order matters** for optimization; sequence must follow logical dependency flow (e.g., base OS → update → install → copy → expose → run) 
- **`COPY`** **is preferred over** **`ADD`** in interviews and real-world usage: `COPY` is faster, more predictable, and avoids URL-related security/availability failures 
- **`MAINTAINER`** **is deprecated** as of 2025 and should not be included in production Dockerfiles; fingerprinting replaces it 
- **Multi-stage builds** are the recommended approach for image optimization — confirmed as interview-critical knowledge 
- **`Docker system prune`** requires manager/client approval before execution in production environments 
- **`COPY`** **vs** **`ADD`** interview answer: always lead with `COPY` as the better option, then explain `ADD` limitations 
- **Slim/Alpine images** are preferred when optimization is required, with the trade-off that tools like `ping`, `vi`, and others must be manually installed 
- **Docker restart policy** **`--restart always`** resolves the issue of containers not auto-restarting after VM reboot 

---

## Dockerfile Keywords Covered

### Core Instructions

- **`FROM`** — Specifies base OS/image; common choices are Ubuntu, CentOS, Alpine (lightweight), or slim variants 
- **`MAINTAINER`** — Deprecated; historically identified file owner; no longer required 
- **`RUN`** — Executes commands on the OS layer (e.g., `apt-get update`, `apt-get install nginx -y`); `-y` flag auto-approves prompts 
- **`CMD`** — Provides the default command to run inside the container; optional depending on use case 
- **`ENTRYPOINT`** — First command executed when a container starts; analogous to saying "good morning when you arrive" 
- **`COPY`** — Copies files/code from host machine into the container 
- **`ADD`** — Similar to `COPY` but accepts URLs; less preferred due to security and reliability concerns 
- **`ENV`** — Sets environment variables as key-value pairs (e.g., `PATH=/usr/bin`); space issues require double quotes or escape characters 
- **`EXPOSE`** — Declares the container's application port (e.g., port 80 for NGINX, 8000 for Python Flask); reserves the port but does not open it without runtime mapping 
- **`WORKDIR`** — Sets the working directory inside the container for subsequent operations; not mandatory but best practice 
- **`LABEL`** — Adds metadata (key-value) such as environment, client name, version; good practice but adds layers 
- **`VOLUME`** — Declares a mount point; optional, used when persistent data is needed 
- **`ARG`** — Build-time variable; optional, used when build-time parameters are needed 

### Optimization Insights

- **Combining** **`RUN`** **commands** with `&&` reduces image layers (e.g., merging `apt-get update` and `apt-get install` into one `RUN` statement) 
- **Removing** **`MAINTAINER`** reduces unnecessary layers 
- **Using slim/Alpine base images** dramatically reduces image size 
- **`CMD`** **is optional** when the container is not executing a persistent process 
- **`.dockerignore`** excludes sensitive files (e.g., local passwords, secrets) from being copied into the image; functions identically to `.gitignore` 

---

## Practical Demonstrations

### VM Setup & Docker Installation

- New GCP VM created (`day4-docker`), Ubuntu LTS, 30 GB disk, traffic allowed 
- Docker installed via: `sudo apt-get update` then `sudo apt install docker.io -y` 
- Verified with `docker -v` 

### Basic Dockerfile Build

- 5-line Dockerfile cloned from GitHub (Batch 45, Day 3 of Docker repo) 
- Built using: `docker build -t shubham-image .` — the `-t` flag tags the image; `.` specifies current directory 
- Layer-by-layer execution confirmed during build (Step 1, Step 2, Step 3...) 

### Container-to-Image Conversion (`docker commit`)

- Demonstrated converting a running/exited container back into an image 
- Command: `docker commit <container-id> <new-image-name>` 
- Resulting image confirmed via `docker images` 
- Noted: not an officially recommended Docker workflow but possible; analogous to an AWS snapshot concept 

### Image Save/Load (S3 Cost Optimization)

- **`docker save`**: Converts image to `.tar` file for storage (e.g., in S3 for low-cost dev image archiving) 
- **`docker load`**: Restores `.tar` file back to a Docker image 
- Use case: companies use S3 instead of ECR/GCR/Docker Hub to reduce storage costs for dev-level images 

### Multi-Stage Build Demo (Python Flask App)

- **Bloated image**: Ubuntu base + Python + pip + build-essential + Flask dependencies = **1.24 GB** 
- **Optimized image**: Used `python:slim` as builder stage, copied only required dependencies into a minimal final image 
- Result: Significant size reduction confirmed via `docker images` comparison 
- Same application, same output, same working directory — only the build approach differed 

---

## Day-to-Day Operations Workflow

The following represents the real-world DevOps container workflow discussed as interview-ready content: 

1. **Write Dockerfile** → defines the blueprint
2. **Build image** → `docker build -t <name> .`
3. **Tag image** → version labeling
4. **Push to registry** → Docker Hub, ECR, GCR, or private registry
5. **Pull image** (when needed on target environment)
6. **Run image** → creates container
7. **Container operations**: start, stop, restart, kill, pause, unpause 
8. **Commit** (if container state needs to be saved as image) 

---

## Troubleshooting Commands

|            Command            |                                   Purpose                                   |
|-------------------------------|-----------------------------------------------------------------------------|
| `docker inspect <container>`  | Full container metadata, IP address, restart policy, environment variables  |
| `docker logs <container-id>`  | View application logs of a running container                                |
| `docker stats <container-id>` | Real-time CPU and memory usage of a container                               |
| `docker exec -it <container>` | Enter a running container interactively                                     |
| `docker ps` / `docker ps -a`  | List running / all containers                                               |
| `docker system df`            | Shows disk usage by images, containers, volumes                             |

- **`docker inspect -f`** can be used to read specific fields like restart policy 
- **`top`** **/** **`htop`** inside container: must be manually installed; may not work by default 

---

## Cleanup & Housekeeping

### Commands

- **`docker rm`** — Remove containers one by one (day-to-day use) 
- **`docker rmi`** — Remove images one by one 
- **`docker image prune`** — Remove dangling (incomplete/failed build) images 
- **`docker system prune`** — Removes all unused containers, networks, images, and build caches; prompts confirmation 

### Dangling Images

- Created when a build fails mid-way or dependencies are missing 
- Described as "loosely hanging" incomplete images consuming disk space 

### Best Practices

- **`RM`** **vs** **`prune`**: Use `RM` daily for individual cleanup; use `prune` once a month for full housekeeping 
- Always get **manager/client approval** before running `docker system prune` in shared or production environments 
- Running prune repeatedly on build caches will slow down subsequent builds (cache must be rebuilt) 

---

## Container Lifecycle & Restart Behavior

- **Out-of-memory (OOM) kills**: Docker may auto-kill containers when memory is exhausted; restarting provides only a temporary fix 
- **Crash loop**: Repeated OOM restarts result in a crash-back-loop (Kubernetes equivalent: `CrashLoopBackOff`) 
- **Permanent fix**: Requires investigating logs, resizing container resources, or fixing application-level memory issues 
- **`--restart always`** **flag**: Added at `docker run` time ensures container auto-restarts if the VM reboots 
    - For existing containers: use `docker update --restart always <container>` 
- **Re-running vs restarting**: Running `docker run` again creates a **new container**; to preserve data/state, the existing exited container must be **started** (not re-run) 

---

## Interview Questions & Scenarios Highlighted

- **What is a Dockerfile?** → Blueprint/recipe for building a Docker image; set of instructions executed layer by layer 
- **`COPY`** **vs** **`ADD`** → `COPY` is preferred; `ADD` has URL support but introduces security/reliability risks 
- **`CMD`** **vs** **`ENTRYPOINT`** → `ENTRYPOINT` is the first command on container start; `CMD` is the default runtime command 
- **How to optimize a Dockerfile?** → Combine `RUN` commands, remove deprecated keywords, use slim/Alpine images, apply multi-stage builds 
- **What are dangling images?** → Incomplete images from failed builds; removed via `docker image prune` 
- **Container-to-image conversion** → `docker commit <container-id> <image-name>` 
- **How to save images cheaply?** → `docker save` to `.tar`, store in S3 
- **Housekeeping activities?** → `docker system prune` monthly, with approval 
- **What is multi-stage build?** → Two-phase Dockerfile that uses a builder stage and a minimal final image to reduce size 
- **Dockerfile sequence — does order matter?** → Yes; commands must follow logical dependency order 
- **What if IP changes after VM restart?** → Use Elastic IP (adds cost) to keep Jenkins/container URL stable 
- **Dockerfile linting** → Lint tools (e.g., Hadolint) check syntax; some companies maintain internal lint servers 

---

## Multi-Stage Build — Key Concept Summary

**Problem**: A standard Ubuntu-based Python image with all build tools included results in a 1.24 GB image. 

**Solution — Multi-Stage Build**: 

```plaintext
FROM python:slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt

FROM python:slim
WORKDIR /app
COPY --from=builder /app .
CMD ["python3", "app.py"]
```

**Benefits**:

- Significantly smaller final image (build tools not included in final layer) 
- Faster CI/CD pipeline execution 
- Reduced cloud storage costs 
- Faster container start times 
- Same application functionality, same output 

**Key rule**: Only the required runtime dependencies are copied from the builder stage into the final image; build tools, compilers, and intermediate artifacts are discarded. 

---

## LMS Platform Overview

- Students access course content via login at the LMS website using Google SSO 
- Dashboard shows all enrolled courses; Batch 45 module includes day-wise PPTs, recordings, and notes 
- **Module Expert** initiative: students post on LinkedIn to build proof of work; a leaderboard tracks activity 
- Top performers visible on leaderboard (e.g., Ravi, Nikhil, Sugam, Sandeep cited as active) 
- External recruiters and project leads have been known to reach out directly to students visible on the leaderboard 
- **Notes**: Day 3 and Day 4 notes to be uploaded to GitHub repo (public/open-source); students encouraged to check 
- **Quiz platform** (in trial): available via WhatsApp group link; not yet official but contains Docker-related questions 

---

## Pending Confirmation

- Day 3 and Day 4 notes pending upload to the GitHub repository 
- Docker networking session (container-to-container communication without Kubernetes) — scheduled for a future class 
- Docker Desktop usage on Windows — to be covered in the next session 
- 50 Docker interview questions document — to be shared with the batch 

---

## Open Questions

- **Can a container auto-restart after VM reboot without manual intervention?** → Yes, via `--restart always` policy at run time or `docker update` for existing containers 
- **Slim vs Alpine — what's the difference?** → Both are lightweight; `slim` is the Python maintainers' minimal variant; `alpine` is Alpine Linux's variant; both serve the same optimization purpose 
- **Should every project use multi-stage builds?** → Recommended as good practice; reduces pipeline time and storage cost; not always mandatory but always beneficial 
- **Can Dockerfile keywords be lowercase?** → Technically works but uppercase is the standard best practice 
- **Does** **`EXPOSE`** **actually open the port?** → No; it reserves/declares the port. Actual port mapping requires `-p` flag at runtime or firewall rules 

---

## Action Items

- **All students**: Clone the Python app repo, build both bloated and optimized versions, compare image sizes (practical exercise) 
- **All students**: Review and attempt the quiz platform shared via WhatsApp group 
- **All students**: Post on LinkedIn to build proof of work and improve leaderboard ranking 
- **Vikas**: Upload Day 3 and Day 4 notes to the GitHub repository 
- **Vikas**: Share 50 Docker interview questions document with the batch 
- **Vikas**: Conduct Docker networking session (future class) 
- **Vikas**: Cover Docker Desktop on Windows in the next session


---

# Docker Interview Preparation — 50 Questions & Answers (Basic to Advanced)

These questions extend the Day 4 session topics: Dockerfile instructions, image optimization, multi-stage builds, container lifecycle, registries, troubleshooting, cleanup, and production practices.

## Basic Level (1–15)

### 1. What is Docker?
**Answer:** Docker is a containerization platform used to package an application and its dependencies into a portable unit called a container. Containers help applications run consistently across development, testing, and production environments.

### 2. What is a Docker image?
**Answer:** A Docker image is a read-only template used to create containers. It contains the application code, runtime, libraries, dependencies, and configuration needed to run the application.

### 3. What is a Docker container?
**Answer:** A container is a running or stopped instance of a Docker image. Multiple containers can be created from the same image, and each container has its own writable layer.

### 4. What is a Dockerfile?
**Answer:** A Dockerfile is a text file containing instructions to build a Docker image. Common instructions include `FROM`, `RUN`, `COPY`, `WORKDIR`, `EXPOSE`, `ENV`, `CMD`, and `ENTRYPOINT`.

### 5. What is the difference between an image and a container?
**Answer:** An image is the template; a container is an instance created from that template. An image is generally read-only, while a container adds a writable layer on top of the image.

### 6. How do you check the installed Docker version?
**Answer:**
```bash
docker --version
docker version
docker info
```
`docker --version` shows the client version, while `docker version` provides client and server details.

### 7. How do you list running and all containers?
**Answer:**
```bash
docker ps       # Running containers
docker ps -a    # All containers, including stopped ones
```

### 8. How do you list Docker images?
**Answer:**
```bash
docker images
# or
docker image ls
```

### 9. How do you pull an image from Docker Hub?
**Answer:**
```bash
docker pull nginx:latest
```
It downloads the specified image tag from the configured registry.

### 10. How do you run an NGINX container and expose it on port 8080?
**Answer:**
```bash
docker run -d --name web -p 8080:80 nginx
```
The host port `8080` is mapped to container port `80`.

### 11. What is the purpose of the `FROM` instruction?
**Answer:** `FROM` defines the base image for the build. Every standard Docker build stage starts with a `FROM` instruction. Examples include `ubuntu:24.04` and `python:3.12-slim`.

### 12. What is the purpose of `RUN`?
**Answer:** `RUN` executes commands while building the image. For example:
```dockerfile
RUN apt-get update && apt-get install -y nginx
```
The result is captured in an image layer.

### 13. What is the purpose of `COPY`?
**Answer:** `COPY` copies files or directories from the build context into the image. It is usually preferred for straightforward file copying because its behavior is explicit and predictable.

### 14. What is the purpose of `WORKDIR`?
**Answer:** `WORKDIR` sets the working directory for subsequent Dockerfile instructions such as `RUN`, `COPY`, `CMD`, and `ENTRYPOINT`. It creates the directory if it does not exist.

### 15. What does `EXPOSE` do?
**Answer:** `EXPOSE` documents the port on which the application is expected to listen inside the container. It does not publish the port on the host. Use `-p` or `-P` with `docker run` to publish ports.

## Intermediate Level (16–35)

### 16. What is the difference between `CMD` and `ENTRYPOINT`?
**Answer:** `ENTRYPOINT` defines the main executable that runs when the container starts. `CMD` provides default arguments or a default command. With an exec-form `ENTRYPOINT`, a `CMD` instruction commonly supplies default arguments that can be overridden at runtime.

### 17. What is the difference between `COPY` and `ADD`?
**Answer:** Both copy content into an image. `ADD` has extra behavior, such as automatically extracting local tar archives and supporting remote URLs in some cases. Prefer `COPY` for normal file copying; use `ADD` only when its extra behavior is specifically needed.

### 18. What is the difference between `ARG` and `ENV`?
**Answer:** `ARG` defines a build-time variable and is not automatically available in the running container. `ENV` sets an environment variable that persists in the image configuration and is available to the container at runtime. Do not use either as a safe way to store secrets in image layers.

### 19. What is a Docker image layer?
**Answer:** Docker images are composed of layers created by build instructions. Layers can be shared between images, which saves storage and speeds up transfers. A container adds a writable layer above the image's read-only layers.

### 20. What is a `.dockerignore` file?
**Answer:** `.dockerignore` tells Docker which files and directories to exclude from the build context. It can reduce build time and prevent files such as `.git`, local virtual environments, logs, and secret files from being sent to the builder.

Example:
```text
.git
.env
__pycache__/
*.log
venv/
```

### 21. How can you reduce the number of image layers?
**Answer:** Combine related shell commands into a single `RUN` instruction using `&&`, and remove temporary package files in the same instruction. Do not combine commands blindly if it makes the Dockerfile harder to maintain.

### 22. Why should package-manager cache be cleaned in the same `RUN` instruction?
**Answer:** If cache files are created in one image layer and deleted in a later layer, the data can remain in the earlier layer and still contribute to image size. Install packages and clean package caches in the same `RUN` instruction where practical.

### 23. What is a multi-stage Docker build?
**Answer:** A multi-stage build uses multiple `FROM` instructions in one Dockerfile. One stage can compile or build the application, and a later stage copies only the required runtime artifacts. This avoids including compilers and build tools in the final image.

### 24. Why are multi-stage builds useful?
**Answer:** They can reduce image size, reduce the attack surface, avoid shipping unnecessary build tools, and improve image transfer and deployment times.

### 25. What is the difference between `python:3.12` and `python:3.12-slim`?
**Answer:** The standard image includes a broader set of operating-system packages and utilities. The slim variant is smaller and contains fewer packages. Slim images may require additional system libraries or tools for particular applications.

### 26. What is Alpine Linux, and why is it used in Docker images?
**Answer:** Alpine is a lightweight Linux distribution often used as a container base image. It can produce small images, but its musl C library and minimal package set can cause compatibility or dependency differences compared with Debian-based images.

### 27. How do you tag an image?
**Answer:**
```bash
docker tag myapp:latest myusername/myapp:v1
```
A tag gives an image a human-readable name and version. For reproducible deployments, teams should use controlled version tags or image digests rather than relying only on `latest`.

### 28. How do you push an image to Docker Hub?
**Answer:**
```bash
docker login
docker tag myapp:v1 myusername/myapp:v1
docker push myusername/myapp:v1
```
The image must be tagged with the correct registry namespace and repository name.

### 29. What is the difference between `docker save` and `docker export`?
**Answer:** `docker save` packages an image, including its layers and tags, into an archive that can later be restored with `docker load`. `docker export` exports a container's filesystem as a flattened archive; it does not preserve the image's layer history or image metadata.

### 30. What is the difference between `docker load` and `docker import`?
**Answer:** `docker load` restores an image archive created by `docker save`. `docker import` creates a new image from a filesystem archive, such as one produced by `docker export`.

### 31. What does `docker commit` do?
**Answer:** `docker commit` creates a new image from a container's filesystem changes. It can be useful for debugging or capturing an experiment, but it is not a good replacement for a version-controlled Dockerfile and repeatable image build.

### 32. How do you view container logs?
**Answer:**
```bash
docker logs <container>
docker logs -f <container>
docker logs --tail 100 <container>
docker logs --since 10m <container>
```
These commands show logs, follow new output, limit output, or filter by time.

### 33. What is the purpose of `docker exec`?
**Answer:** `docker exec` runs a new process inside an already-running container.
```bash
docker exec -it <container> sh
```
Use `bash` only if it is installed in the image; minimal images often provide `sh` instead.

### 34. How do you inspect a container's configuration?
**Answer:**
```bash
docker inspect <container>
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' <container>
```
Inspect can show configuration, mounts, networking details, environment settings, and restart policy. Be careful because inspect output may contain sensitive values.

### 35. How do you monitor container resource usage?
**Answer:**
```bash
docker stats
docker stats <container>
```
It displays live CPU, memory, network I/O, and block I/O statistics for containers.

## Advanced Level (36–50)

### 36. Explain Docker's container lifecycle.
**Answer:** A typical lifecycle is: create a container, start it, allow it to run, stop or kill it, restart it if required, and finally remove it. Useful commands include `docker create`, `docker start`, `docker stop`, `docker kill`, `docker restart`, and `docker rm`.

### 37. What is the difference between `docker stop` and `docker kill`?
**Answer:** `docker stop` sends a termination signal (normally `SIGTERM`) and waits for a grace period before sending `SIGKILL`. `docker kill` sends `SIGKILL` by default, stopping the container immediately. Applications should handle graceful shutdown signals where possible.

### 38. What are Docker restart policies?
**Answer:** Restart policies control whether Docker restarts a container after it exits. Common options are `no`, `on-failure`, `always`, and `unless-stopped`.
```bash
docker run -d --restart unless-stopped nginx
docker update --restart always <container>
```
Choose the policy based on the required operational behavior.

### 39. What is the difference between `always` and `unless-stopped`?
**Answer:** `always` restarts the container when it exits and generally brings it back after the Docker daemon restarts, even if it had previously been manually stopped. `unless-stopped` behaves similarly but respects a manual stop across daemon restarts.

### 40. Why might a container keep restarting?
**Answer:** Common reasons include application crashes, an incorrect startup command, missing configuration, failed dependencies, health or orchestration actions, and out-of-memory termination. Check `docker logs`, `docker inspect`, and `docker stats` before changing the restart policy.

### 41. What is an OOM kill in Docker?
**Answer:** An out-of-memory kill occurs when a process is terminated because memory pressure exceeds what is available or permitted. Check container memory limits, host memory, application behavior, and kernel or Docker events. Restarting alone does not fix a memory leak or incorrect resource sizing.

### 42. What is the difference between a Docker volume and the container writable layer?
**Answer:** The writable layer belongs to a specific container and is removed when the container is removed. A volume is managed separately by Docker and can persist beyond the lifecycle of a container. Use volumes for data that must survive container replacement.

### 43. What is the difference between a bind mount and a named volume?
**Answer:** A bind mount maps a specific host path into a container. A named volume is managed by Docker and is stored in Docker's volume area. Named volumes are commonly useful for persistent application data; bind mounts are often useful for development workflows and explicit host-path sharing.

### 44. What is Docker networking?
**Answer:** Docker networking allows containers to communicate with one another, the host, and external networks. Common network drivers include `bridge`, `host`, `none`, and `overlay`. The appropriate driver depends on whether containers run on one host or across a cluster.

### 45. How can two containers communicate by name?
**Answer:** Put them on the same user-defined bridge network. Docker provides name-based DNS resolution on user-defined networks.
```bash
docker network create app-net
docker run -d --name backend --network app-net my-backend
docker run --rm --network app-net busybox nslookup backend
```
The application container can then connect to the backend using its container name and listening port.

### 46. What is the difference between `EXPOSE` and `-p`?
**Answer:** `EXPOSE` documents the intended container port in image metadata. `-p host_port:container_port` publishes a port on the host and forwards traffic to the container. `EXPOSE` alone does not make the application reachable from outside.

### 47. How do you troubleshoot a container that starts and exits immediately?
**Answer:** Check its exit status and logs, inspect the configured command, and verify that the application runs as a foreground process.
```bash
docker ps -a
docker logs <container>
docker inspect <container>
```
A container normally stops when its main process exits. Do not add an artificial infinite loop just to keep a failed application container running.

### 48. What is a dangling image?
**Answer:** A dangling image is an untagged image, often left behind after rebuilding an image tag. It may be an intermediate or unused image layer.
```bash
docker image ls -f dangling=true
docker image prune
```
Review what will be removed before cleaning shared or production hosts.

### 49. What does `docker system prune` remove?
**Answer:** It removes unused Docker data such as stopped containers, unused networks, dangling images, and build cache. Options can broaden the cleanup scope.
```bash
docker system df
docker system prune
```
Use `docker system prune -a` carefully because it can remove all images not referenced by existing containers. Review the impact and obtain required approval before running cleanup in production.

### 50. How would you secure and optimize a Docker image for production?
**Answer:** Start from a trusted, maintained base image; pin versions or use digests where appropriate; use multi-stage builds; run as a non-root user; exclude unnecessary files with `.dockerignore`; avoid embedding secrets; scan images for vulnerabilities; keep packages updated; minimize installed tools; and define appropriate resource limits and runtime permissions. Validate the image in CI before publishing it.

---

# Docker Scenario-Based Interview Questions & Answers (20)

These scenarios are designed to test practical troubleshooting and decision-making, not just command memorization.

## Scenario 1: Container does not restart after a VM reboot

**Question:** Your NGINX container was running successfully. The VM restarted overnight, but the container is not running. How would you fix this?

**Answer:**
1. Check the container:
   ```bash
   docker ps -a
   ```
2. Inspect its restart policy:
   ```bash
   docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' <container>
   ```
3. Configure a restart policy:
   ```bash
   docker update --restart unless-stopped <container>
   ```
4. Verify Docker is enabled to start with the OS:
   ```bash
   sudo systemctl enable docker
   sudo systemctl status docker
   ```
A restart policy handles container restart behavior, while the Docker service itself must also start after reboot.

## Scenario 2: Container exits immediately after starting

**Question:** You run `docker run -d nginx`, but the container exits. What do you check?

**Answer:** First inspect the status and logs:
```bash
docker ps -a
docker logs <container>
docker inspect <container>
```
Check whether the image's main process is failing, whether configuration is invalid, or whether a required file or port is missing. A container exits when its main process exits.

## Scenario 3: Docker image size is more than 1 GB

**Question:** Your Python application image is 1.2 GB. How would you reduce it?

**Answer:**
- Use a suitable slim base image.
- Use a multi-stage build if compilation or build tools are required.
- Copy only required application files.
- Add `.dockerignore`.
- Remove package caches and temporary build artifacts in the same build layer.
- Review installed dependencies and remove unnecessary packages.

Compare before and after:
```bash
docker images
docker history <image>
```

## Scenario 4: The application works locally but fails in the container

**Question:** A Python application works on your laptop but fails after being containerized. What is your troubleshooting approach?

**Answer:**
1. Check application logs using `docker logs`.
2. Confirm the correct runtime and dependencies are installed.
3. Verify the `WORKDIR` and file paths.
4. Check environment variables and configuration.
5. Confirm the application listens on `0.0.0.0`, not only `127.0.0.1`, when it must accept traffic through the container network.
6. Verify port mapping and required mounted files.

## Scenario 5: Port 8080 is not reachable from a browser

**Question:** NGINX is running inside a container, but `http://VM-IP:8080` is not working. How do you troubleshoot it?

**Answer:**
```bash
docker ps
docker port <container>
docker inspect <container>
docker logs <container>
```
Confirm the container was started with a mapping such as:
```bash
docker run -d --name web -p 8080:80 nginx
```
Also verify the application listens on container port 80, the VM firewall or cloud firewall allows inbound TCP 8080, and no other service is occupying the host port.

## Scenario 6: A container is consuming too much memory

**Question:** Your production container is consuming nearly all available memory. What will you do?

**Answer:**
```bash
docker stats
docker logs <container>
docker inspect <container>
```
Identify whether usage is expected, caused by a memory leak, or caused by an oversized workload. Review application metrics and host memory. Set an appropriate memory limit after testing:
```bash
docker run -d --memory 512m --name app myapp:v1
```
Do not treat repeated restarts as the permanent solution.

## Scenario 7: A developer accidentally deletes a container

**Question:** A developer removes a container that was storing application data. Can you recover the data?

**Answer:** It depends on where the data was stored. Data in a Docker-managed volume may still exist after the container is removed. Data stored only in the container writable layer is generally lost when the container is deleted.
```bash
docker volume ls
docker volume inspect <volume>
```
For important workloads, store persistent data in volumes or external storage and maintain backups.

## Scenario 8: A Docker build is taking too long

**Question:** Your Docker image takes 10 minutes to build every time. How can you improve build performance?

**Answer:**
- Order Dockerfile instructions to maximize cache reuse.
- Copy dependency manifests before frequently changing source files.
- Install dependencies before copying the full application.
- Use `.dockerignore` to reduce the build context.
- Avoid unnecessary cache deletion.
- Use BuildKit and appropriate cache export/import in CI where available.

Example:
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

## Scenario 9: A secret is visible in a Docker image

**Question:** A developer copied `.env` into the Docker image. How would you handle it?

**Answer:** Treat the exposed secret as compromised. Rotate it, remove it from the build context using `.dockerignore`, and rebuild the image. Inject secrets at runtime through an approved secret manager or platform mechanism. Removing the file in a later Dockerfile layer does not reliably remove it from earlier layers or image history.

## Scenario 10: You need to transfer an image to a server without registry access

**Question:** The target server cannot access Docker Hub or a private registry. How can you transfer the image?

**Answer:** Save the image to an archive, transfer it through an approved secure channel, and load it on the target:
```bash
docker save -o myapp-v1.tar myapp:v1
# Transfer myapp-v1.tar to the target server
docker load -i myapp-v1.tar
docker images
```
Verify the image tag and checksum after transfer.

## Scenario 11: You need to clean disk space on a production VM

**Question:** The Docker host is running out of disk space. What steps will you take safely?

**Answer:**
1. Inspect usage:
   ```bash
   docker system df
   df -h
   ```
2. Identify large images, stopped containers, unused volumes, and build cache.
3. Check whether any data or image is needed by a running or future workload.
4. Obtain required operational approval.
5. Remove specific unused resources first. Use broad prune commands only after reviewing their impact.

Avoid `docker system prune --volumes` unless you have confirmed that the unused volumes do not contain required data.

## Scenario 12: A container cannot resolve another container's hostname

**Question:** Your frontend container cannot resolve `backend`. What would you check?

**Answer:** Confirm both containers are attached to the same user-defined network:
```bash
docker network ls
docker network inspect app-net
docker inspect frontend
docker inspect backend
```
If required, create a network and attach both containers:
```bash
docker network create app-net
docker network connect app-net frontend
docker network connect app-net backend
```
Then test name resolution from a suitable diagnostic container on that network.

## Scenario 13: The container uses the old application code after a new deployment

**Question:** You updated the source code and rebuilt the image, but the container still serves the old version. Why?

**Answer:** The existing container continues to use the image from which it was created. Building a new image does not update an already-created container. Verify the image tag and container configuration, then replace the container using the new image. In production, use a controlled deployment process and versioned tags rather than relying only on `latest`.

## Scenario 14: A Dockerfile build fails because a file is missing

**Question:** Docker reports that `COPY app.py /app/` cannot find the file. What could be wrong?

**Answer:** Check that:
- `app.py` exists in the build context.
- The build command is being run from the expected directory.
- The source path in `COPY` is relative to the build context.
- `.dockerignore` is not excluding the file.
- File-name capitalization matches exactly.

Rebuild from the correct directory:
```bash
docker build -t myapp:v1 .
```

## Scenario 15: You need to run a container as a non-root user

**Question:** A security review asks you to avoid running the application as root. How can you do this?

**Answer:** Create or use an unprivileged user in the image, ensure it has access to the required application files, and set `USER`:
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY --chown=10001:10001 . .
USER 10001
CMD ["python", "app.py"]
```
Test file permissions and required port access before deploying.

## Scenario 16: A container is in a restart loop

**Question:** Your container repeatedly restarts. What is your troubleshooting sequence?

**Answer:**
```bash
docker ps -a
docker logs --tail 200 <container>
docker inspect <container>
docker events
```
Check the exit code, startup command, configuration, dependencies, resource limits, and OOM indicators. Fix the root cause before changing restart behavior. A restart policy can make a failing container restart repeatedly without resolving the failure.

## Scenario 17: You need to roll back to a previous application version

**Question:** A new image release has a critical bug. How would you roll back?

**Answer:** Keep previously tested image versions available in the registry. Update the deployment to use the previous known-good tag or digest, then verify application health and logs. For a simple standalone container, recreate it from the previous image using the same required ports, environment, mounts, and network configuration. Follow the team's deployment and rollback procedure.

## Scenario 18: A Docker image contains build tools that are not needed at runtime

**Question:** Your Java or Python image contains compilers and build dependencies. How would you remove them from the production image?

**Answer:** Use a multi-stage build. Compile or install build-time dependencies in a builder stage, then copy only the runtime artifact and required runtime libraries into a smaller final stage. Test the final image to ensure all runtime dependencies are present.

## Scenario 19: You need to identify which image layer increased image size

**Question:** An image became 700 MB larger after a Dockerfile change. How do you identify the reason?

**Answer:**
```bash
docker history --no-trunc myapp:latest
docker image inspect myapp:latest
```
Compare the image history with the previous version. Look for large package installations, copied build directories, caches, logs, or accidental inclusion of local files. Review the build context and `.dockerignore` as well.

## Scenario 20: Your team wants a repeatable and auditable Docker release process

**Question:** How would you design a basic CI/CD workflow for building and releasing Docker images?

**Answer:**
1. Store the Dockerfile and application source in version control.
2. Run code and dependency checks.
3. Build the image using a controlled build environment.
4. Run automated tests against the image.
5. Scan the image for vulnerabilities and policy violations.
6. Tag it with the commit SHA or release version.
7. Push it to an approved container registry.
8. Deploy the exact tested image digest to the target environment.
9. Verify health, logs, and metrics.
10. Keep a tested previous version available for rollback.

---

## Quick Revision — Important Docker Commands

| Task | Command |
|---|---|
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| List images | `docker images` |
| Pull an image | `docker pull nginx:latest` |
| Build an image | `docker build -t myapp:v1 .` |
| Run a container | `docker run -d --name web -p 8080:80 nginx` |
| Stop a container | `docker stop web` |
| Start an existing container | `docker start web` |
| Restart a container | `docker restart web` |
| View logs | `docker logs web` |
| Follow logs | `docker logs -f web` |
| Execute a command in a container | `docker exec -it web sh` |
| Inspect a container | `docker inspect web` |
| Monitor resource usage | `docker stats` |
| View Docker disk usage | `docker system df` |
| View image layers/history | `docker history myapp:v1` |
| Tag an image | `docker tag myapp:v1 username/myapp:v1` |
| Push an image | `docker push username/myapp:v1` |
| Save an image archive | `docker save -o myapp.tar myapp:v1` |
| Load an image archive | `docker load -i myapp.tar` |
| Set restart policy | `docker update --restart unless-stopped web` |
| Remove a stopped container | `docker rm web` |
| Remove an image | `docker rmi myapp:v1` |
| Remove dangling images | `docker image prune` |

## Student Practice Checklist

- [ ] Explain image vs container without looking at notes.
- [ ] Write a Dockerfile using `FROM`, `WORKDIR`, `COPY`, `RUN`, `EXPOSE`, and `CMD`.
- [ ] Explain `CMD` vs `ENTRYPOINT` with an example.
- [ ] Explain `ARG` vs `ENV`.
- [ ] Build and compare a standard image and a slim image.
- [ ] Create a multi-stage Dockerfile and compare image sizes.
- [ ] Tag and push an image to a registry.
- [ ] Save an image to a TAR file and load it again.
- [ ] Configure a restart policy and test it.
- [ ] Troubleshoot a container using `logs`, `inspect`, `stats`, and `exec`.
- [ ] Explain volumes and why container writable-layer data is not persistent.
- [ ] Explain safe Docker cleanup in a production environment.

**Interview tip:** Do not stop at defining a command. Explain when you used it, what problem it solves, and how you would verify the result in a real environment.


# Docker Container Auto-Restart After VM Reboot

## Objective

Configure Docker containers to start automatically when the virtual machine (VM), such as an AWS EC2 Ubuntu instance, restarts.

Docker provides **restart policies** to control what happens when a container stops or the Docker daemon restarts.

## 1. Docker Restart Policies

| Restart policy | Description |
|---|---|
| `no` | Do not restart the container automatically. This is the default policy. |
| `always` | Always restart the container when it stops. It also starts again after a Docker daemon or VM restart. |
| `unless-stopped` | Restart the container after a Docker daemon or VM restart, unless it was explicitly stopped by the user. |
| `on-failure` | Restart the container only when it exits with a non-zero exit code. |

## 2. Set the Restart Policy While Creating a Container

Use `--restart always` when creating the container:

```bash
docker run -d \
  --name mynginx \
  --restart always \
  -p 80:80 \
  nginx
```

Explanation:

- `docker run`: Creates and starts a container.
- `-d`: Runs the container in detached mode.
- `--name mynginx`: Assigns the container the name `mynginx`.
- `--restart always`: Configures the container to restart automatically.
- `-p 80:80`: Maps host port 80 to container port 80.
- `nginx`: Uses the Nginx image.

## 3. Configure Auto-Restart for an Existing Container

If the container already exists, update its restart policy without recreating it:

```bash
docker update --restart always mynginx
```

Recommended alternative:

```bash
docker update --restart unless-stopped mynginx
```

Use `unless-stopped` when you want the container to return after a VM reboot but stay stopped if you deliberately stopped it.

## 4. Check the Container's Restart Policy

```bash
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' mynginx
```

Expected output:

```text
unless-stopped
```

You can also inspect the full restart policy:

```bash
docker inspect -f '{{json .HostConfig.RestartPolicy}}' mynginx
```

## 5. Ensure Docker Starts When Ubuntu Boots

Enable the Docker service so it starts automatically when the VM boots:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Check the service status:

```bash
sudo systemctl status docker
```

If your installation uses Docker's containerd service, you can also enable it:

```bash
sudo systemctl enable containerd
```

## 6. Test the Complete Scenario

First, check that the container is running:

```bash
docker ps
```

Restart the Ubuntu VM:

```bash
sudo reboot
```

Reconnect to the VM after it boots, then check:

```bash
sudo systemctl status docker
docker ps
```

If the container is not listed as running, inspect all containers and their logs:

```bash
docker ps -a
docker logs mynginx
```

## 7. Practical AWS EC2 Example

Imagine that you deployed an Nginx application inside a Docker container on an AWS EC2 Ubuntu instance.

1. Launch an EC2 Ubuntu instance.
2. Install Docker and start the Docker service.
3. Run the Nginx container with a restart policy.
4. Reboot the EC2 instance.
5. Wait for the instance to become available and reconnect.
6. Run `docker ps` to verify that the Nginx container is running.

Create the container:

```bash
docker run -d \
  --name webapp \
  --restart unless-stopped \
  -p 80:80 \
  nginx
```

Verify:

```bash
docker ps
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' webapp
```

## 8. Useful Commands for Quick Revision

| Task | Command |
|---|---|
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| Create container with auto-restart | `docker run -d --name webapp --restart unless-stopped nginx` |
| Update existing container | `docker update --restart always webapp` |
| Disable restart policy | `docker update --restart no webapp` |
| Check restart policy | `docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' webapp` |
| Start Docker at boot | `sudo systemctl enable docker` |
| Start Docker now | `sudo systemctl start docker` |
| Check Docker service | `sudo systemctl status docker` |
| View container logs | `docker logs webapp` |
| Restart VM | `sudo reboot` |

## 9. Important Notes

- A Docker restart policy works only when the Docker daemon is running.
- Enable the Docker service at boot using `systemctl enable docker`.
- Restart policies do not recover a VM that has been terminated or lost. VM recovery requires separate infrastructure configuration.
- A restart policy does not guarantee that your application is healthy or ready to serve traffic. Use health checks and application monitoring as needed.
- `docker stop` explicitly stops a container. With `unless-stopped`, Docker keeps it stopped across daemon and VM restarts until you start it again.
- Docker restart policies apply to containers, not to the VM itself.

## 10. Recommended Command for Students

For a typical web application container, use:

```bash
docker run -d \
  --name webapp \
  --restart unless-stopped \
  -p 80:80 \
  nginx
```

**Key takeaway:** Use Docker restart policies to automatically recover containers after unexpected exits or VM reboots. For many standalone application containers, `unless-stopped` is a practical default.

