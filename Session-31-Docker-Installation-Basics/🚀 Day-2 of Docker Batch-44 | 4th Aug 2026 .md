# 🚀 Day-2 of Docker Batch-44: Docker Architecture & Core Container Commands

[![Module: Docker Containerization](https://img.shields.io/badge/Module-Docker-2496ED?style=for-the-badge&logo=docker)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 4th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-4th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


## 📋 Session Overview

This session focused on one of the most important concepts in Docker: Dockerfiles. Until now, Docker containers had been created using pre-built images downloaded from Docker Hub. In this lecture, the trainer explained how organizations build their own custom Docker images that contain applications, dependencies, configuration files, and startup commands.
The lecture gradually moved from understanding why Dockerfiles are required to building custom images, running containers from those images, understanding image layers, and learning Docker best practices. Most enterprise DevOps pipelines rely on Dockerfiles because they provide consistency, repeatability, and automation across development, testing, and production environments.

## 📌 Executive Summary of Key Topics Covered

### 1. Docker Architecture & Core Concepts
* **Node Definition:** Clarified that a *Node* is any underlying physical server or Virtual Machine (such as a GCP Compute Engine instance) running a software runtime like Docker.
* **Blueprint vs. Runtime Instance:**
* **Docker Image:** Read-only template / snapshot / blueprint containing application code, runtime dependencies, system binaries, and configuration settings.
* **Docker Container:** Lightweight, isolated, running process instantiated from a Docker Image.
* **Platform Independence:** Explained how packaging runtime libraries inside container images ensures consistent execution across different Linux distributions (Ubuntu, RHEL, CentOS) or Cloud Providers.

### 2. Cloud Provisioning & Scripted Docker Installation
* **GCP Cloud Setup:** Provisioned an Ubuntu VM on Google Cloud Platform (GCP) via Cloud Shell and SSH.
* **Automated Script Installation:** Used `curl -fsSL https://get.docker.com -o get-docker.sh` to download the official cross-distribution shell script.
* **Production Best Practice (`--dry-run`):** Highlighted the critical real-world practice of running `sh get-docker.sh --dry-run` before executing installation scripts in production to validate dependencies, detect pre-existing Docker installations, and prevent accidental system degradation.

### 3. Container Lifecycle Management & Execution Modes
* **Foreground Execution:** Demonstrates `docker run -p 8080:80 nginx` where logs attach to stdout. Exiting with `Ctrl + C` sends `SIGINT`, putting the container into an `Exited` status.
* **Detached Execution (`-d`):** Demonstrates `docker run -d -p 8080:80 --name my-web nginx` to run containers asynchronously in the background.
* **Inspection & Maintenance:**
* `docker ps`: Lists active, running containers.
* `docker ps -a`: Lists all containers (active + stopped/exited).
* `docker start <container>` vs `docker run <image>`: Re-executing an existing stopped container vs creating a brand-new container instance.
* `docker rm -f`: Force removing containers.

### 4. Networking, Port Mapping (`-p`), & GCP Firewall Security
* **Port Mapping (`-p host_port:container_port`):** Forwards host VM interface traffic to the internal isolated container bridge network (`172.x.x.x`).
* **Cloud VPC Firewall Rules:** Addressed why containers running on port 80/8080 are unreachable via external browser until GCP Ingress Firewall rules allowing TCP traffic from `0.0.0.0/0` are configured.

### 5. Custom Web Applications, Volume Binding (`-v`), & AI Integration
* **ChatGPT Integration:** Demonstrated generating customized HTML/CSS code using ChatGPT and saving it to an `index.html` file on the host VM.
* **Volume Mounting (`-v`):** Used `-v /host/path:/usr/share/nginx/html` to link host folders with container web roots, enabling **real-time website updates** on file save without needing container restarts or image rebuilds.

### 6. Real-World Troubleshooting
* Solved the classic *"It works on my machine"* developer conflict.
* Diagnosed port allocation conflicts (`bind: address already in use`).
* Addressed `403 Forbidden` errors stemming from missing `index.html` files or incorrect target mount directories.
* Handled VM disk space exhaustion (`No space left on device`) using system pruning.

- --

## 🎯 10 Technical Interview Questions & Answers (Overview)

1. **Difference between Docker Image and Docker Container?**
* *Answer:* Image is a read-only template/blueprint; Container is a runnable, isolated instance with a read-write layer on top.
2. **Foreground execution (`Ctrl + C`) vs. Detached mode (`-d`)?**
* *Answer:* Foreground attaches to terminal stdout; `Ctrl + C` sends `SIGINT` to stop PID 1. Detached mode (`-d`) runs the container process in the background.
3. **Why use `--dry-run` with shell install scripts in production?**
* *Answer:* Performs non-destructive checks for existing packages, dependencies, and OS compatibility before mutating host system files.
4. **How does port mapping `-p 8080:80` function?**
* *Answer:* Docker configures host iptables NAT rules to forward external traffic on host port 8080 to internal container port 80.
5. **Difference between `docker ps` and `docker ps -a`?**
* *Answer:* `docker ps` shows running containers; `docker ps -a` shows all containers (including exited/stopped), essential for post-crash analysis.
6. **Volume Mounting (`-v`) vs. `docker cp`?**
* *Answer:* `-v` creates a continuous 2-way live sync between host and container; `docker cp` is a one-time static file copy.
7. **How to restart a stopped container without recreating it?**
* *Answer:* `docker start <container_id_or_name>`.
8. **Why is a running container on port 80 unreachable via GCP Public IP?**
* *Answer:* GCP VPC Ingress Firewall rules are blocking inbound TCP traffic on port 80.
9. **How to launch an interactive terminal inside a running container?**
* *Answer:* `docker exec -it <container_name_or_id> /bin/bash`.
10. **Command to remove all stopped containers?**
* *Answer:* `docker container prune` or `docker rm -f $(docker ps -a -q)`.

- --

## 💡 20 Scenario-Based Questions & Answers (Overview)

The artifact details 20 practical real-world scenarios directly derived from the class, including:

1. **Environment Inconsistency ("Works on my machine"):** Resolving library mismatches between dev Ubuntu and prod CentOS using containerized packaging.
2. **Foreground Lock & Sudden Shutdown:** Resolving terminal lock by executing containers in detached mode (`-d`).
3. **Port Conflict Failure (`Address already in use`):** Fixing host port 80 collisions by removing old containers or mapping to alternate ports (e.g., `-p 8080:80`).
4. **Cloud Firewall Timeout:** Configuring GCP VPC Ingress Firewall rules for port 8080.
5. **Nginx `403 Forbidden` Error:** Resolving missing `index.html` or invalid permissions in mounted host directories.
6. **UI Code Edits Not Updating Live:** Switching from static containers to volume-mounted host directories (`-v`).
7. **Overwriting Container System Configs:** Avoiding directory-to-file mount mismatches on `/etc/nginx/`.
8. **Script Installation Warnings during `--dry-run`:** Handling pre-existing Docker packages safely.
9. **Host VM Disk Space Exhaustion:** Reclaiming storage via `docker system prune -a --volumes`.
10. **Container Name Collision:** Resolving `--name` conflicts when stopped containers retain existing names.
11. **Multi-Tenant Web Hosting on Single VM:** Running multiple isolated Nginx containers on distinct host ports (`8001:80`, `8002:80`).
12. **Data Recovery from Exited Containers:** Extracting log files using `docker cp` without active volume mounts.
13. **Silent Container Crashes on Startup:** Debugging container exit codes using `docker logs`.
14. **Volume Mount vs Image Rebuild Strategy:** Using volume mounts for local development and image rebuilds for CI/CD production pipelines.
15. **One-Off Network Diagnostics inside Containers:** Running non-intrusive troubleshooting via `docker exec -it web-app ping google.com`.
16. **Container Auto-Restart on VM Reboot:** Applying auto-recovery policies (`--restart always`).
17. **Instant Container Exit on Base OS Images:** Keeping Ubuntu/Alpine base containers alive using `tail -f /dev/null` or `-itd`.
18. **Restricting Service Exposure to Localhost:** Binding container ports strictly to loopback interface (`-p 127.0.0.1:6379:6379`).
19. **ChatGPT Web Template Deployment Workflow:** Complete end-to-end command sequence for publishing AI-generated HTML/CSS sites.
20. **`docker stop` vs `docker kill` vs `docker rm` Lifecycle:** Comparing graceful `SIGTERM` (stop), force `SIGKILL` (kill), and filesystem removal (rm).




