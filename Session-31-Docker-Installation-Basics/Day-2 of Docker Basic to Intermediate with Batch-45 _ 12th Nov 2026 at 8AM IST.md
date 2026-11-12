## Key Outcomes

Day 2 of the Docker training session focused on practical containerization, covering Docker's core advantages, image sizing, container lifecycle management, port mapping, and volume mounting. Participants successfully installed Docker, pulled images (Ubuntu, CentOS, Alpine), ran containers interactively, and built personalized containerized web applications using NGINX. The session culminated in a live Q&A addressing real-world scenarios including Kubernetes troubleshooting, GitLab verification requirements, and GCP billing issues.

---

## Decisions Made

- **Docker is the industry-standard tool** for building, shipping, and running containerized applications; it is used across all major companies due to platform independence and lightweight image sizes. 
- **Alpine Linux confirmed as production-ready** — students who answered "no" were corrected; Alpine (~13 MB) can and is used in production environments. 
- **Hyphen** **`-d`** **(detach mode)** is the correct flag to run containers in the background without displaying logs in the terminal. 
- **Volume mounting (****`-v`****)** is the required approach to persist data (e.g., HTML files) across container restarts, since containers are ephemeral by nature. 
- **`docker exec -it`** is the correct command to enter a running container without stopping it; the shell (`/bin/bash` or `/bin/sh`) is identified via `docker ps` output. 
- **GitLab now requires credit card verification** even for local runner usage, due to crypto mining abuse prevention — students unable to verify can skip the GitLab practical without impact. 

---

## Core Concepts Covered

### Why Docker Became Popular

- **Platform independence**: Applications run identically on Linux or Windows regardless of host environment. 
- **"Works on my machine" problem solved**: Docker packages code, binaries, libraries, configurations, environment variables, and dependencies into a single image that can be moved across environments. 
- **Lightweight images**: Docker images exclude bootloaders, kernel files, and unnecessary UI/services, resulting in dramatically smaller sizes compared to full OS installations. 

### Image Size Comparison (Practical Demonstration)

|   OS   |             Full VM Size              | Docker Image Size |
|--------|---------------------------------------|-------------------|
| Ubuntu | ~2.6 GB (download) / ~10 GB extracted | ~163 MB           |
| CentOS | ~1.4 GB                               | ~163 MB           |
| Alpine | N/A                                   | **~13 MB**        |

- Size reduction explained by Mandhir: Docker images omit bootloader and kernel files, using the **host's own bootloader and resources** instead. 
- Alpine is the most popular **lightweight OS** used in production — a key interview answer. 

### Docker Architecture & Workflow

- **Dockerfile** → blueprint of the application, written primarily by developers (90%) with DevOps involvement (~10%). 
- **Build/compile/pull/push** → primarily DevOps activity. 
- **Run/pull in production** → automated via CI/CD (e.g., Jenkins), managed by operations/production engineering teams (L1/L2/L3 support). 
- Core flow: `Dockerfile` → `docker build` → **Image** → `docker run` → **Container**. 

---

## Practical Commands Demonstrated

### Installation

```bash
sudo -i
apt-get update
apt install docker.io
docker -v   # confirmed version 29.9 installed
```
