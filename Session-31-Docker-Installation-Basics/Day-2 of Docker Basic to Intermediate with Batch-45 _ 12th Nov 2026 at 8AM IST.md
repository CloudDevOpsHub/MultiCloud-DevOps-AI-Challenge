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

---

# Docker Interview Preparation

The following questions are based on the concepts and practicals covered in Day 2: Docker fundamentals, image sizes, Docker architecture, container lifecycle, detached mode, port mapping, interactive access, NGINX, and volume mounting.

## 20 Basic-to-Advanced Docker Interview Questions and Answers

### 1. What is Docker?

**Answer:** Docker is a containerization platform used to build, package, ship, and run applications in containers. A container includes the application and the dependencies it needs, helping it run consistently across different environments.

### 2. What is a container?

**Answer:** A container is a running instance of a Docker image. It provides an isolated environment for an application and its required processes, libraries, and configuration.

### 3. What is a Docker image?

**Answer:** A Docker image is a read-only template used to create containers. It contains the application code, runtime, libraries, and other required files.

### 4. What is the difference between a Docker image and a container?

**Answer:** An image is the template or package; a container is the running instance created from that image. One image can be used to create multiple containers.

### 5. What is a Dockerfile?

**Answer:** A Dockerfile is a text file containing instructions to build a Docker image. It defines the base image, files to copy, commands to run, ports to document, and the default command or entry point.

### 6. Explain the basic Docker workflow.

**Answer:** First, create a Dockerfile. Then build an image using `docker build`. Finally, create and start a container from that image using `docker run`.

```bash
docker build -t myapp:1.0 .
docker run -d --name myapp myapp:1.0
```

### 7. Why are Docker images generally smaller than virtual machine images?

**Answer:** A typical container image contains the user-space files and dependencies required by the application. It does not package a separate guest kernel or bootloader like a full virtual machine. Containers use the host operating system kernel, which reduces overhead.

### 8. What is Alpine Linux, and why is it used in Docker?

**Answer:** Alpine Linux is a minimal Linux distribution with a small footprint. Its small base image can reduce image download and storage size. It can be used in production, provided the application and its dependencies are compatible and properly tested.

### 9. Is a smaller Docker image always better?

**Answer:** Not automatically. Smaller images can improve download speed and reduce storage usage, but the image must still contain all required dependencies, security updates, certificates, and debugging or runtime components needed by the application. Reliability and security matter more than size alone.

### 10. What is the purpose of `docker pull`?

**Answer:** `docker pull` downloads an image from a container registry to the local Docker host.

```bash
docker pull nginx
docker pull ubuntu
```

### 11. What is the purpose of `docker run`?

**Answer:** `docker run` creates a container from an image and starts it. It can also configure options such as the container name, port mapping, volumes, and detached or interactive mode.

```bash
docker run -d --name web nginx
```

### 12. What does the `-d` flag do in Docker?

**Answer:** The `-d` or `--detach` flag runs a container in the background and returns control to the terminal.

```bash
docker run -d nginx
```

Use `docker logs <container>` to view its output later.

### 13. What is the difference between `docker run` and `docker start`?

**Answer:** `docker run` creates a new container from an image and starts it. `docker start` starts an already-created, stopped container.

```bash
docker run -d --name web nginx
docker stop web
docker start web
```

### 14. How do you list running and stopped containers?

**Answer:** Use `docker ps` to list running containers and `docker ps -a` to list all containers, including stopped ones.

```bash
docker ps
docker ps -a
```

### 15. How do you access a running container?

**Answer:** Use `docker exec -it` to start an interactive process, such as a shell, inside a running container. Use a shell that actually exists in the image.

```bash
docker exec -it web /bin/sh
```

Some images include `/bin/bash`; minimal images may provide only `/bin/sh`.

### 16. What is port mapping in Docker?

**Answer:** Port mapping publishes a container port on the Docker host so that clients can access the application from outside the container.

```bash
docker run -d --name web -p 8080:80 nginx
```

Here, host port `8080` forwards traffic to container port `80`.

### 17. What is a Docker volume, and why is it needed?

**Answer:** A Docker volume provides persistent storage that exists independently of a container's writable layer. It is useful when data must remain available after a container is removed or recreated.

```bash
docker volume create appdata
docker run -d --name web -v appdata:/usr/share/nginx/html nginx
```

### 18. What is the difference between a bind mount and a named volume?

**Answer:** A bind mount maps a specific path from the host into a container, while a named volume is managed by Docker and is referenced by its volume name. Bind mounts are useful for sharing local project files; named volumes are commonly used for persistent application data.

```bash
# Bind mount
docker run -d -p 8080:80 \
  -v "$PWD/html:/usr/share/nginx/html" nginx

# Named volume
docker run -d -v appdata:/usr/share/nginx/html nginx
```

### 19. What happens to data stored only inside a container when the container is removed?

**Answer:** Data written only to the container's writable layer is removed along with the container. Data stored in a mounted volume or bind-mounted host directory remains outside the container and can be reused.

### 20. How would you make an NGINX container serve your own web page?

**Answer:** Create an HTML file on the host and mount its directory to NGINX's document root. Publish a host port to access the page from a browser.

```bash
mkdir -p ~/mywebsite
echo '<h1>Hello from Docker</h1>' > ~/mywebsite/index.html

docker run -d --name my-nginx -p 8080:80 \
  -v ~/mywebsite:/usr/share/nginx/html:ro nginx
```

Open `http://localhost:8080`. The `:ro` option makes the mounted directory read-only inside the container.

---

## 20 Docker Scenario-Based Interview Questions and Answers

### 1. Your NGINX container starts, but you cannot open the website in your browser. How will you troubleshoot it?

**Answer:**
1. Check whether the container is running: `docker ps`.
2. Check its logs: `docker logs <container>`.
3. Confirm the port mapping: `docker port <container>`.
4. Verify that the host port is reachable and not blocked by a firewall.
5. Test locally with `curl http://localhost:<host-port>`.

For example, if you started it with `-p 8080:80`, test `http://localhost:8080`.

### 2. You started a container with `-d`, and the terminal returned immediately. Is the container stopped?

**Answer:** Not necessarily. Detached mode returns control to the terminal while the container runs in the background. Check with `docker ps`. Use `docker logs <container>` to see its output.

### 3. Your container exits immediately after starting. What will you check?

**Answer:**
- Run `docker ps -a` to confirm its status and exit code.
- Inspect its logs using `docker logs <container>`.
- Check the image's default command or entry point.
- Confirm that the main process does not finish immediately or fail because of missing configuration.

A container normally remains running while its main process is running.

### 4. You updated `index.html` on the host, but the NGINX page still shows old content. What could be wrong?

**Answer:** Check whether the correct host directory is mounted to `/usr/share/nginx/html`. Confirm that you edited the file inside that mounted directory and that the browser is not showing a cached page. Inspect the mount with:

```bash
docker inspect <container>
```

If the file was copied into the image during build rather than mounted, rebuilding the image and recreating the container may be required.

### 5. You removed and recreated an application container, and its data disappeared. How will you prevent this?

**Answer:** Store persistent data in a Docker named volume or bind mount instead of only in the container's writable layer. Reuse the same volume when recreating the container.

```bash
docker volume create appdata
docker run -d --name app -v appdata:/app/data myapp:1.0
```

### 6. Your application listens on port 3000 inside the container, but users cannot access it on host port 8080. What should you do?

**Answer:** Publish the correct container port:

```bash
docker run -d --name app -p 8080:3000 myapp:1.0
```

The left side is the host port and the right side is the container port. Also confirm that the application listens on an address reachable from outside its own container, commonly `0.0.0.0`, rather than only `127.0.0.1`.

### 7. You need to troubleshoot a running container without stopping it. Which command will you use?

**Answer:** Use `docker exec -it` to open a shell inside the running container.

```bash
docker exec -it <container-name> /bin/sh
```

If the image includes Bash, `/bin/bash` can be used instead. `docker exec` does not replace or stop the container's main process.

### 8. You tried `docker exec -it web /bin/bash`, but received “executable file not found”. What is the likely reason?

**Answer:** The image may not include Bash. Minimal images such as Alpine commonly provide `/bin/sh` instead. Try:

```bash
docker exec -it web /bin/sh
```

### 9. Your team wants to use Alpine to reduce image size. What should you verify before moving it to production?

**Answer:** Verify application compatibility, required packages and libraries, runtime behavior, security updates, and the availability of required troubleshooting tools. Build and test the application with the Alpine image in a representative environment before production deployment.

### 10. A developer says, “It works on my machine,” but the application fails on another server. How can Docker help?

**Answer:** Package the application and its required runtime dependencies into an image. Use the same tested image across development, testing, and production, while supplying environment-specific configuration securely at runtime. This reduces differences between environments, though host architecture, kernel features, configuration, and external services can still matter.

### 11. You need to run two copies of the same NGINX image at the same time. Is that possible?

**Answer:** Yes. Create two containers from the same image, give them different names, and publish different host ports.

```bash
docker run -d --name web1 -p 8081:80 nginx
docker run -d --name web2 -p 8082:80 nginx
```

Both containers can listen on port 80 internally because they have separate network namespaces.

### 12. You want to identify which image a running container was created from. How will you check?

**Answer:** Use `docker inspect` to view the container configuration, including its image reference and image ID.

```bash
docker inspect <container-name>
```

You can also use `docker ps --no-trunc` for additional container details.

### 13. Your host already has a process using port 8080, and Docker cannot publish that port. What will you do?

**Answer:** Choose another available host port, for example:

```bash
docker run -d --name web -p 8081:80 nginx
```

Then access the application through host port `8081`. Alternatively, identify and safely reconfigure the process already using port `8080`.

### 14. You want to share a local HTML folder with an NGINX container so that edits are visible without rebuilding the image. What will you use?

**Answer:** Use a bind mount to map the host folder to NGINX's document root.

```bash
docker run -d --name web -p 8080:80 \
  -v "$PWD/html:/usr/share/nginx/html" nginx
```

Changes made to files in the mounted host folder are then visible to the container.

### 15. Your container is running, but the application is returning an error. What is your first troubleshooting approach?

**Answer:** Check the container status and application logs first.

```bash
docker ps -a
docker logs <container-name>
```

Then inspect configuration, environment variables, mounted files, port mappings, and the application process. Use `docker exec -it` if you need to inspect the running container.

### 16. You want to stop a container gracefully and then start it again later. Which commands will you use?

**Answer:**

```bash
docker stop web
docker start web
```

`docker stop` sends a termination signal and allows the main process time to shut down. `docker start` starts the existing stopped container again.

### 17. Your container was removed, but you still need the data stored in its named volume. Is recovery possible?

**Answer:** Yes, provided the volume itself was not removed and the data is still present. List volumes and mount the existing volume into a new container.

```bash
docker volume ls
docker run -d --name new-web -v appdata:/data nginx
```

Removing a container does not normally remove a separately managed named volume. Avoid volume-pruning commands until you have verified what data can be deleted.

### 18. You built a new version of your application image, but the old container still serves the previous version. Why?

**Answer:** Building a new image does not automatically replace an existing container. The existing container continues using the image it was created from. Stop and remove or replace the old container, then create a new one from the updated image.

```bash
docker build -t myapp:2.0 .
docker stop app
docker rm app
docker run -d --name app -p 8080:3000 myapp:2.0
```

For production, use a controlled deployment process and health checks rather than replacing critical containers manually without a rollout plan.

### 19. Your team wants to see why a container stopped during a deployment. What information will you collect?

**Answer:** Check the container's exit status and logs, then inspect its configuration and recent events if needed.

```bash
docker ps -a
docker logs <container-name>
docker inspect <container-name>
docker events
```

Use timestamps and deployment records to correlate the failure with configuration or image changes.

### 20. You are asked to containerize a simple static website using NGINX for a demo. Explain the end-to-end approach.

**Answer:**
1. Create a directory and add an `index.html` file.
2. Pull the NGINX image.
3. Run a container with a host port mapped to container port 80.
4. Mount the website directory into NGINX's document root.
5. Open the mapped host port in a browser.
6. Check logs and container status if the page does not load.

```bash
mkdir -p ~/website
echo '<h1>My First Docker Website</h1>' > ~/website/index.html

docker pull nginx

docker run -d --name demo-web -p 8080:80 \
  -v ~/website:/usr/share/nginx/html:ro nginx

docker ps
docker logs demo-web
```

Open `http://localhost:8080` to verify the website.

---

## Quick Revision: Commands to Remember

| Task | Command |
|---|---|
| Check Docker version | `docker -v` |
| Download an image | `docker pull nginx` |
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| Run a container in background | `docker run -d nginx` |
| Publish a port | `docker run -d -p 8080:80 nginx` |
| View container logs | `docker logs <container>` |
| Enter a running container | `docker exec -it <container> /bin/sh` |
| Stop a container | `docker stop <container>` |
| Start an existing container | `docker start <container>` |
| Create a named volume | `docker volume create appdata` |
| Inspect a container | `docker inspect <container>` |

## Interview Preparation Tip

Do not just memorize these answers. Practise the commands yourself. Be ready to explain what you did, why you used a particular flag, what went wrong, and how you verified the result. In interviews, a clear explanation of a real practical task is often stronger than a memorized definition.
