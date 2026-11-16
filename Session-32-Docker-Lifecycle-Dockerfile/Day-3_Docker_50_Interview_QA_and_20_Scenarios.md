## Key Outcomes

The session covered hands-on Docker operations including Docker Hub account creation, Docker installation via a scripted approach, day-to-day container commands, running multiple containers with a for loop, and an introduction to Dockerfiles with a complete build-tag-push workflow. Students successfully ran a containerized Jenkins instance, built a custom image from a Dockerfile, and pushed it to Docker Hub. The session concluded with a Q&A covering pipeline persistence, image verification, multi-image applications, and cloud billing management.

---

## Decisions Made

- **Docker Hub account creation** is mandatory for all students; accounts can be created via email/username/password or via Google/GitHub OAuth 
- **Docker installation method** for this session: scripted approach via `getdocker.com` (two commands), replacing the previous `apt install docker.io` method 
- **Jenkins image source**: use the official Jenkins-maintained image (`jenkins/jenkins:latest`) from Docker Hub, not the Docker-provided one, as Jenkins itself maintains it 
- **Dockerfile keyword casing**: all Dockerfile commands (FROM, RUN, CMD, MAINTAINER, etc.) must be in **uppercase**; Linux and Dockerfile syntax are case-sensitive 
- **Docker Compose** acknowledged as a valid tool for pulling multiple images, but the course will focus on **Kubernetes** for multi-image/microservice orchestration instead 
- **Tomorrow's class** will cover advanced Dockerfile commands; Tuesday will include an AI/prompt-based Docker session; a **resume building session** is planned for Tuesday or Wednesday 

---

## Topics Covered

### Docker Hub

- **Purpose**: a registry provided by Docker for storing and maintaining container images 
- **URL**: `hub.docker.com` / `app.docker.com`
- Account tiers: **Personal** (free); paid tiers exist and free features may become chargeable in the future 
- Images pushed to Docker Hub are **public by default**; can be made private via Settings → Visibility Settings → Make Private 

### Docker Installation — Scripted Approach

Four commands from `getdocker.com` were demonstrated: 

- **Line 1**: `curl` command to download the install script to the VM
- **Line 2**: View the contents of the downloaded script (cat)
- **Line 3**: **Dry run** — test the script without executing it; important interview/production concept: always dry-run before running in production 
- **Line 4**: `sh` command to execute the script and install Docker
- Result: Docker version **29.9** installed 
- Key tip: **do not copy the** **`$`** **dollar sign** when copying commands 

### Core Docker Commands Reviewed

|          Command          |                           Purpose                           |
|---------------------------|-------------------------------------------------------------|
| `docker help`             | Lists all available Docker commands and options             |
| `docker -v` / `--version` | Check installed Docker version                              |
| `docker ps`               | Show running containers                                     |
| `docker ps -a`            | Show **all** containers including exited/stopped            |
| `docker pull <image>`     | Download an image from Docker Hub                           |
| `docker images`           | List locally available images                               |
| `docker run <image>`      | Pull image (if not present) + create and start a container  |
| `docker start` / `stop`   | Start or stop an existing container                         |
| `docker kill`             | Stop a running container (cannot be restarted after kill)   |
| `docker rm`               | Remove a container                                          |
| `docker rmi`              | Remove an image by name or ID                               |
| `docker stats`            | Real-time CPU and memory usage of all containers            |
| `docker top`              | View CPU/memory of a specific container                     |

- **Difference between** **`rm`** **and** **`rmi`**: `rm` removes a **container**; `rmi` removes an **image** 
- **Difference between** **`stop`** **and** **`kill`**: `stop` allows the container to be restarted; `kill` terminates it and it cannot be restarted 

### Measuring Container Startup Time

- Use the `time` keyword before any Docker command to measure execution duration 
- Example: `time docker run --rm nginx` returned **~0.8 milliseconds** 
- Interview context: instead of estimating "2–3 seconds," use `time` to provide precise, provable answers 
- `--rm` flag: automatically removes the container after it exits, preventing unnecessary space consumption 

### Running Multiple Containers with a For Loop

- Demonstrated running **34 Alpine containers** simultaneously using a single bash `for` loop 
- Command structure: `for i in {1..34}; do docker run -d alpine sleep 1000; done` 
- **Why Alpine?** It is the smallest Linux image, minimizing resource usage 
- `sleep 1000`: container runs for 1000 seconds then exits automatically, releasing CPU/memory 
- Verified with `docker ps` — all 34 containers running with different auto-generated names, all from **one single image** 
- **Key interview point**: a single VM can function as 100+ servers using Docker containers 
- `docker stats` confirmed all 34 Alpine OS instances running within a **4GB RAM VM**, with lowest memory usage at **0.8 MB** per container 

### Running Jenkins as a Container

- Command used: `docker run -p 8080:8080 -it -d jenkins/jenkins:latest` 
- **Port mapping**: `8080:8080` — first value is the VM/host port, second is the container port 
- `-d` flag: detached/background mode; `-it`: interactive mode 
- To find Jenkins among many running containers: `docker ps | grep jenkins` 
- Access Jenkins: use the VM's **external IP** + `:8080` in a browser 
- **Unlocking Jenkins**: the initial admin password is stored **inside the container**, not on the VM 
    - Must first enter the container: `docker exec -it <container_id> /bin/bash` 
    - Then run: `cat /var/jenkins_home/secrets/initialAdminPassword` 
- **Key insight**: the Jenkins image contains all binaries, libraries, Java dependencies, and configuration — no separate Java installation needed 

---

## Dockerfile — Introduction and Hands-On

### What is a Dockerfile?

- A **text document** containing up to ~10 commands (instructions/recipe) that Docker reads to automatically **assemble an image** 
- Analogy: assembling a car — tires and battery are readymade (base OS), you assemble the application on top 
- `docker build` reads the Dockerfile layer by layer; each line = one layer 

### Dockerfile Keywords Covered

|   Keyword    |                                              Purpose                                               |
|--------------|----------------------------------------------------------------------------------------------------|
| `FROM`       | Base operating system (e.g., `FROM ubuntu`)                                                        |
| `MAINTAINER` | Author/owner email of the Dockerfile                                                               |
| `RUN`        | Execute commands at the **OS level** during image build (e.g., `apt update`, `apt install nginx`)  |
| `CMD`        | Command to run when the **container starts** (application level)                                   |

- **RUN vs CMD**: `RUN` operates at OS/build level; `CMD` operates at application/runtime level — analogous to Windows `Run` dialog vs `CMD` command prompt 

### Dockerfile Creation Workflow (Hands-On)

1. Navigate to home directory: `cd ~` 
2. Create file: `vi Dockerfile` — **D must be capital**, no extension, no spaces 
3. Paste 5-line code (FROM ubuntu, MAINTAINER <email>, RUN apt update, RUN apt install nginx, CMD nginx) 
4. Save: `Esc` → `:wq`
5. **Build image**: `docker build -t <name> .` 
    - `-t`: tag/name the image
    - `.`: use Dockerfile in the **current directory**
6. Each line in Dockerfile = one layer executed sequentially; all 5 layers complete = image ready 
7. **Tag image** with Docker Hub account: `docker tag <image_id> <dockerhub_username>/<image_name>:latest` 
8. **Login to Docker Hub**: `docker login` → opens browser activation URL → enter confirmation code 
9. **Push image**: `docker push <dockerhub_username>/<image_name>:latest` 
10. Verify on Docker Hub: image appears under your account; **86 students** downloaded the instructor's pushed image 

### Why Write a Custom Dockerfile?

- Used when your application is **not available as a pre-built image** on Docker Hub 
- Enables full customization: install specific versions, configure environment, add dependencies 
- For large applications like Flipkart (100+ microservices), **hundreds of Dockerfiles** may be created 

---

## Q&A Highlights

- **Q: Will Jenkins pipeline history persist after VM restart?**
A: Yes — pipeline config persists inside the container. Scripted pipelines are stored in GitHub; declarative pipelines in the container's config. For best practice, **map a volume** outside the container so data survives even if the container is destroyed. Persistent Volumes (PV/PVC) are a Kubernetes concept for this. 
- **Q: How to verify a Docker image is genuine/official?**
A: Look for the **blue verified tick** (Docker Official Image badge) on Docker Hub. High download counts (e.g., 10M+) are an additional signal, but the verified badge is the primary indicator. 
- **Q: How to pull multiple images for a multi-service application?**
A: Docker does not natively support pulling multiple images in one command. Use a **shell script** for ad hoc multi-pull, or use **Docker Compose**. The recommended production approach is **Kubernetes with microservices**. 
- **Q: How to uninstall Docker from an instance?**
A: `apt remove docker` (reverse of the apt install command) 
- **Q: Can we run** **`sudo`** **inside a container?**
A: Containers are lightweight and `sudo` is not always available. Some containers allow it, some don't — depends on the image configuration. 
- **Q: How to update Java dependency in 10 out of 100,000 running containers?**
A: Update manually one-by-one for small numbers, or create a **new Dockerfile** with the updated version and rebuild/redeploy. 
- **Q: How to troubleshoot SSH connection errors to VM?**
A: Verify **port 22 is open** in inbound firewall rules; try changing the **data center/region** of the VM. 
- **Q: What is the difference between Docker sandbox and actual VM practice?**
A: Docker sandbox is a browser-based free environment; actual VM practice on GCP/AWS is preferred for real-world experience. The sandbox has limitations compared to a real VM. 

---

## Pending Confirmation

- Students in the **US/UK** (Rizwan, Kanchan, Ravi, Gous) to share their email IDs with Vikas for AWS certificate generation, as the platform may be geo-restricted 
- **Swarnadeep's Docker Hub push issue**: username vs. email ID mismatch suspected — needs to verify Docker Hub username (not email) in the tag command 
- **Gopal's Docker push issue**: image tag path format was incorrect; needs to re-practice the full tag → login → push sequence 
- **Mamta's SSH connection error**: changing data center region suggested as next troubleshooting step 
- **Mandhir's VM security tip**: restrict VM firewall to source IP only (find via `ipchicken.com` or "what is my IP") to prevent unauthorized access and unexpected data transfer charges — Vikas to cover this in Monday's class 

---

## Action Items

- **All students**: Create Docker Hub account if not done; type "done" in chat to confirm 
- **All students**: Install Docker locally on Windows machine using the download link provided 
- **All students**: Practice the full Dockerfile workflow — create → build → tag → push 
- **All students (who haven't)**: Complete the AWS recorded course at the provided URL and download the certificate; post on LinkedIn with "Add to Profile" 
- **All students**: Delete VMs over the weekend to avoid billing; submit a support ticket if billed unexpectedly as a student 
- **All students**: Sync LinkedIn profiles on the leaderboard dashboard; submit full name and LinkedIn URL 
- **Mandhir**: Post VM firewall/IP restriction guide in the WhatsApp group 
- **Students with GCP billing concerns**: Disable auto-pay/UPI on GCP; enable VM auto-delete scheduling to limit idle resource costs 
- **Pankaj**: Delete unused GCP project generating ~₹137 forecasted charges from snapshot storage 

---

## Announcements

- **Leaderboard update**: Ravi, Sandeep, and Sunil are currently leading; students encouraged to sync profiles and update rankings 
- **AWS Certificate**: Free training certificate available via the provided URL — complete 100% of the course, then click to download and post on LinkedIn 
- **Monthly Hackathon**: Being prepared with prizes; details to follow after the batch completes 
- **Course roadmap**: Docker (this week) → Kubernetes (next week, ~1 week) → Azure (1 week) → AI (1 week) → Projects (December) 
- **Recording**: Available within one hour of session end


---

# Docker Interview Preparation — 50 Questions & Answers (Basic to Advanced)

The following questions are based on the topics covered in this Day 3 session: Docker Hub, Docker installation, core container commands, running multiple containers, Jenkins in Docker, Dockerfile basics, image build/tag/push workflow, persistence, registries, and cloud VM security and billing.

## Basic Level (1–15)

### 1. What is Docker?
**Answer:** Docker is a platform for building, distributing, and running applications in containers. It packages an application with its dependencies so it can run consistently across different environments.

### 2. What is a Docker image?
**Answer:** A Docker image is a read-only template containing the application, runtime, libraries, dependencies, and configuration required to create a container.

### 3. What is a Docker container?
**Answer:** A container is an instance of an image. It runs an application in an isolated environment and has its own writable layer on top of the image's read-only layers.

### 4. What is Docker Hub?
**Answer:** Docker Hub is a hosted container image registry. Developers can pull public images and push images to repositories under their accounts or organizations. Repository visibility and account limits depend on the plan and settings.

### 5. What is a container registry?
**Answer:** A container registry stores and distributes container images. Examples include Docker Hub, Amazon Elastic Container Registry (ECR), Azure Container Registry (ACR), and Google Artifact Registry.

### 6. How do you check the Docker version?
**Answer:**
```bash
docker --version
docker version
docker info
```
The first shows the CLI version, the second shows client and server versions, and `docker info` provides daemon and environment details.

### 7. How do you see running containers?
**Answer:**
```bash
docker ps
```
To see all containers, including stopped ones:
```bash
docker ps -a
```

### 8. How do you list locally available images?
**Answer:**
```bash
docker images
# or
docker image ls
```

### 9. How do you download an image from Docker Hub?
**Answer:**
```bash
docker pull nginx:latest
```
This downloads the requested image tag from the configured registry.

### 10. What happens when you run `docker run nginx`?
**Answer:** Docker checks whether the image is available locally and pulls it if needed. It then creates and starts a new container from that image. Without detached mode, the process normally attaches to the terminal.

### 11. What is the purpose of `docker help`?
**Answer:** It displays Docker commands and help information. For help on a specific command, use:
```bash
docker run --help
docker build --help
```

### 12. What is the difference between `docker rm` and `docker rmi`?
**Answer:** `docker rm` removes containers, while `docker rmi` removes images. A container generally needs to be stopped before it can be removed unless force removal is requested.

### 13. What is the difference between `docker stop` and `docker kill`?
**Answer:** `docker stop` requests a graceful shutdown by sending a termination signal and waiting for a grace period. `docker kill` sends `SIGKILL` by default and stops the container immediately.

### 14. What does the `-d` option do in `docker run`?
**Answer:** `-d` runs the container in detached mode, in the background, and returns the container ID.

### 15. What does the `--rm` option do?
**Answer:** `--rm` automatically removes the container when its main process exits. It is useful for temporary containers and one-time commands.

## Intermediate Level (16–35)

### 16. What is a Dockerfile?
**Answer:** A Dockerfile is a text file containing instructions that Docker uses to build an image. It defines the base image, files to copy, commands to run, and the default runtime command.

### 17. What is the purpose of the `FROM` instruction?
**Answer:** `FROM` selects the base image for a build stage. For example:
```dockerfile
FROM ubuntu:24.04
```
A Dockerfile can contain multiple `FROM` instructions when using multi-stage builds.

### 18. What is the purpose of `RUN`?
**Answer:** `RUN` executes commands during image build time. For example:
```dockerfile
RUN apt-get update && apt-get install -y nginx
```
The resulting filesystem changes become part of the image.

### 19. What is the purpose of `CMD`?
**Answer:** `CMD` specifies the default command or arguments used when a container starts. Runtime arguments supplied to `docker run` can override the default command behavior.

### 20. What is the difference between `RUN` and `CMD`?
**Answer:** `RUN` executes during image build time and contributes to the image. `CMD` defines the default command or arguments for container runtime.

### 21. What is the purpose of `COPY`?
**Answer:** `COPY` copies files and directories from the build context into the image. Example:
```dockerfile
COPY index.html /usr/share/nginx/html/
```

### 22. Why is the Dockerfile normally named `Dockerfile`?
**Answer:** Docker looks for a file named `Dockerfile` by default when `docker build` is run. A different filename can be used with the `-f` option:
```bash
docker build -f Dockerfile.dev -t myapp:dev .
```

### 23. Why does the Dockerfile build command end with a dot?
**Answer:** In:
```bash
docker build -t myapp:v1 .
```
the dot identifies the current directory as the build context. Docker can access files from that context when processing `COPY` and `ADD`.

### 24. What does the `-t` option do in `docker build`?
**Answer:** `-t` assigns a name and optionally a tag to the resulting image:
```bash
docker build -t myapp:v1 .
```
Here, `myapp` is the repository name and `v1` is the tag.

### 25. What is an image tag?
**Answer:** A tag is a human-readable label used to identify a version or variant of an image, such as `v1`, `staging`, or `latest`. For reliable releases, use controlled version tags or immutable digests rather than depending only on `latest`.

### 26. How do you tag an image for Docker Hub?
**Answer:**
```bash
docker tag myapp:v1 <dockerhub-username>/myapp:v1
```
The destination name must use the correct Docker Hub namespace and repository.

### 27. How do you log in to Docker Hub from the CLI?
**Answer:**
```bash
docker login
```
Authenticate using the supported login method. For automation, use an access token or approved credential mechanism rather than storing a personal password in scripts.

### 28. How do you push an image to Docker Hub?
**Answer:**
```bash
docker tag myapp:v1 <dockerhub-username>/myapp:v1
docker login
docker push <dockerhub-username>/myapp:v1
```
The repository must exist or be creatable under the authenticated account, and the tag must be correct.

### 29. How do you verify that an image was pushed successfully?
**Answer:** Check the command output and exit status, then open the repository in Docker Hub and confirm the tag is listed. You can also test by pulling the exact tag from another machine:
```bash
docker pull <dockerhub-username>/myapp:v1
```

### 30. How do you run Jenkins using Docker?
**Answer:**
```bash
docker run -d --name jenkins \
  -p 8080:8080 \
  jenkins/jenkins:lts
```
This runs Jenkins in the background and maps host port 8080 to container port 8080. For persistent Jenkins data, mount a volume at `/var/jenkins_home`.

### 31. How do you find a Jenkins container among many containers?
**Answer:**
```bash
docker ps
docker ps --filter name=jenkins
```
You can also filter by image:
```bash
docker ps --filter ancestor=jenkins/jenkins:lts
```

### 32. How do you access the Jenkins initial admin password?
**Answer:** For the official Jenkins image, the initial password is stored inside the container at:
```bash
docker exec -it jenkins \
  cat /var/jenkins_home/secrets/initialAdminPassword
```
The exact setup can vary depending on the image and whether Jenkins has already been initialized.

### 33. Why does a Jenkins container need a volume?
**Answer:** Jenkins stores jobs, plugins, configuration, credentials, and other state under `/var/jenkins_home`. A persistent volume allows this data to survive container replacement or recreation.

### 34. What is the difference between a Docker container and a virtual machine?
**Answer:** Containers share the host operating system kernel and isolate application processes. Virtual machines run their own guest operating systems on virtualized hardware. Containers are usually lighter and start faster, while VMs provide a separate OS kernel boundary.

### 35. How can you measure how long a Docker command takes?
**Answer:** On Linux, prefix the command with `time`:
```bash
time docker pull nginx
time docker run --rm nginx
```
This reports timing information for the command. The result depends on factors such as cache, network, image size, and host performance.

## Advanced Level (36–50)

### 36. How can you run multiple containers using a Bash `for` loop?
**Answer:**
```bash
for i in {1..5}; do
  docker run -d --name "alpine-$i" alpine sleep 1000
done
```
This creates five containers from one image. Unique names make them easier to manage. Check them with `docker ps`.

### 37. Can multiple containers be created from the same image?
**Answer:** Yes. An image is a reusable template. Each container has its own container ID, configuration, process space, and writable layer, while the underlying image layers can be shared.

### 38. How do you monitor resource consumption across containers?
**Answer:**
```bash
docker stats
docker stats <container>
```
This shows live CPU, memory, network I/O, and block I/O statistics. Use host-level monitoring as well when diagnosing VM-wide resource pressure.

### 39. How do you see processes running inside a container?
**Answer:**
```bash
docker top <container>
```
This displays processes running in the specified container from the host.

### 40. How do you enter a running Jenkins container?
**Answer:**
```bash
docker exec -it jenkins /bin/bash
```
If Bash is not installed in the image, use:
```bash
docker exec -it jenkins sh
```

### 41. Will Jenkins data survive if the container is removed?
**Answer:** Not necessarily. If Jenkins data exists only in the container writable layer, removing the container can delete it. Mount a named volume or bind mount at `/var/jenkins_home` to preserve the data independently of the container.

### 42. What is Docker Compose, and when would you use it?
**Answer:** Docker Compose defines and manages multi-container applications using a YAML file. It is useful when an application needs services such as a frontend, backend, database, and cache to run together. It can build, pull, start, and manage those services as one application.

### 43. What is the difference between Docker Compose and Kubernetes?
**Answer:** Docker Compose is commonly used to define and run multi-container applications, especially on a single host or in development environments. Kubernetes is a container orchestration platform designed to manage workloads across a cluster, including scheduling, scaling, service discovery, and self-healing.

### 44. How do you pull multiple images for a multi-service application?
**Answer:** Use Docker Compose when the services are defined in a Compose file:
```bash
docker compose pull
```
For a simple ad hoc workflow, a shell script can run multiple `docker pull` commands. Kubernetes pulls images specified in workload definitions when scheduling workloads onto nodes.

### 45. How do you make a Docker Hub repository private?
**Answer:** Configure the repository's visibility settings in Docker Hub, subject to the features and limits of the account plan. Private repositories require authorized users or automation to authenticate before pulling or pushing.

### 46. How can you check whether a Docker Hub image is trustworthy?
**Answer:** Verify the publisher, official-image or verified-publisher status where applicable, repository ownership, source code, release history, documentation, and image provenance. Download count alone does not prove that an image is safe. For production, scan the image and pin a trusted version or digest.

### 47. How do you uninstall Docker from an Ubuntu VM?
**Answer:** First identify how Docker was installed and which packages are present. For packages installed through APT, a package removal may look like:
```bash
sudo apt remove docker.io
```
This does not necessarily remove all Docker data, configuration, or other Docker packages. Review the impact and back up required images, volumes, and data before a complete removal.

### 48. Can you use `sudo` inside a container?
**Answer:** It depends on the image. Many minimal images do not include `sudo`. If a process needs elevated privileges, container configuration and security settings should be designed deliberately; avoid running applications as root without a clear requirement.

### 49. How would you update a Java dependency used by many containers?
**Answer:** Update the dependency in the source or build configuration, rebuild the application image, run tests and security checks, publish a new versioned image, and roll it out through the deployment system. Avoid manually modifying running containers because that creates inconsistent and non-repeatable environments.

### 50. How would you secure a cloud VM running Docker?
**Answer:** Restrict SSH access to trusted source IPs, expose only required application ports, use strong identity and access controls, patch the OS and Docker engine, avoid privileged containers unless required, use trusted images, scan images, protect secrets, monitor logs and resource usage, and remove unused resources. Review cloud billing and shut down or delete resources that are no longer needed.

---

# Docker Scenario-Based Interview Questions & Answers (20)

## Scenario 1: Docker is installed, but the `docker` command returns a permission error

**Question:** A student runs `docker ps` and receives a permission-denied error while accessing the Docker socket. What will you do?

**Answer:**
1. Check whether the Docker service is running:
   ```bash
   sudo systemctl status docker
   ```
2. Test with elevated privileges:
   ```bash
   sudo docker ps
   ```
3. If the organization permits it, add the user to the Docker group:
   ```bash
   sudo usermod -aG docker "$USER"
   ```
4. Log out and log back in, then test again.

**Important:** Membership in the Docker group grants powerful privileges and can effectively provide root-level control over the host. Apply it only to trusted users.

## Scenario 2: Docker Hub push fails with an authorization error

**Question:** Your image builds successfully, but `docker push` returns `denied: requested access to the resource is denied`. How do you troubleshoot it?

**Answer:**
- Confirm you are logged in:
  ```bash
  docker login
  ```
- Confirm the Docker Hub username and repository path.
- Tag the image correctly:
  ```bash
  docker tag myapp:v1 <username>/myapp:v1
  ```
- Push the exact tag:
  ```bash
  docker push <username>/myapp:v1
  ```
- Check that the account has permission to push to the repository.

Use the Docker Hub username or organization namespace in the image reference, not an email address.

## Scenario 3: Docker Hub shows the image, but another student cannot pull it

**Question:** You pushed an image successfully, but another student receives an access-denied error when pulling it. What could be the reason?

**Answer:** The repository may be private, the student may not have been granted access, or the image name/tag may be incorrect. Confirm repository visibility and the exact pull command. If it is intentionally private, grant the required access and have the student authenticate.

## Scenario 4: Jenkins is accessible inside the VM but not from a browser

**Question:** Jenkins is running in Docker, but `http://VM-IP:8080` does not open. What will you check?

**Answer:**
```bash
docker ps
docker port jenkins
docker logs jenkins
```
Confirm the container was started with `-p 8080:8080`, Jenkins is listening on the expected container port, and the cloud firewall allows inbound TCP 8080 from the required source IP. Also check the VM's local firewall and whether the service has completed startup.

## Scenario 5: Jenkins setup asks for an initial admin password

**Question:** Jenkins has started successfully, but the setup page asks for an administrator password. Where do you find it?

**Answer:**
```bash
docker exec -it jenkins \
  cat /var/jenkins_home/secrets/initialAdminPassword
```
Use the password shown by the command in the Jenkins setup screen. Confirm the correct container name or ID if the command cannot find the container.

## Scenario 6: Jenkins loses jobs after container recreation

**Question:** A developer removes and recreates the Jenkins container. All jobs and plugins are missing. Why?

**Answer:** Jenkins data was stored in the container's writable layer rather than a persistent volume. Recreate Jenkins with a named volume mounted at `/var/jenkins_home`:
```bash
docker volume create jenkins_home

docker run -d --name jenkins \
  -p 8080:8080 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```
For an existing installation, back up and migrate the original Jenkins home data before replacing the container.

## Scenario 7: You need to run 20 temporary containers for testing

**Question:** Your team needs 20 Alpine containers for a short-lived test. How can you create them efficiently?

**Answer:**
```bash
for i in {1..20}; do
  docker run -d --name "test-$i" alpine sleep 300
done
```
Verify with:
```bash
docker ps
```
After testing, remove them safely:
```bash
docker rm -f $(docker ps -aq --filter "name=test-")
```
Use a naming pattern that uniquely identifies only the test containers.

## Scenario 8: One container is consuming too much CPU or memory

**Question:** Your VM becomes slow after launching multiple containers. How do you identify the cause?

**Answer:**
```bash
docker stats
docker top <container>
docker ps
```
Identify the container consuming resources, inspect its application logs, and review the workload. Set suitable CPU and memory limits after testing. Also check overall VM resource usage because containers share the host's resources.

## Scenario 9: A Docker container is stopped, and you want to start it again

**Question:** You accidentally stopped a Jenkins container. Should you run `docker run` again?

**Answer:** No. `docker run` creates a new container. Start the existing container instead:
```bash
docker ps -a
docker start jenkins
```
This preserves the existing container configuration and its writable layer. Persistent data should still be stored in a volume.

## Scenario 10: A student removes the wrong Docker image

**Question:** A student runs `docker rmi nginx`, but Docker reports that the image is being used by a container. What should they do?

**Answer:** Identify containers using the image:
```bash
docker ps -a
```
If the container is still required, keep the image. If it is no longer required, stop and remove the relevant container after confirming it is safe:
```bash
docker stop <container>
docker rm <container>
docker rmi nginx
```
Do not force removal without understanding the impact.

## Scenario 11: The Dockerfile builds on one VM but fails on another

**Question:** The same Dockerfile works on a developer's VM but fails on the CI server. What do you investigate?

**Answer:**
- Compare Docker engine and BuildKit versions.
- Check the build context and current working directory.
- Verify required files are not excluded by `.dockerignore`.
- Check network access to package repositories.
- Verify base-image availability and architecture.
- Review file permissions and case-sensitive paths.
- Inspect the exact build error rather than assuming the Dockerfile is the only cause.

## Scenario 12: Docker build cannot find the Dockerfile

**Question:** The command `docker build -t myapp .` reports that it cannot find a Dockerfile. How do you fix it?

**Answer:**
```bash
pwd
ls -la
```
Make sure the file is named exactly `Dockerfile` in the current directory. If it has another name or is in another directory, specify it:
```bash
docker build -f ./docker/Dockerfile -t myapp:v1 .
```
The final dot still identifies the build context.

## Scenario 13: Your application image is pushed under the wrong Docker Hub username

**Question:** The image was built correctly, but the push command used an incorrect namespace. How do you correct it?

**Answer:** Tag the existing image with the correct namespace and push that tag:
```bash
docker tag myapp:v1 correctuser/myapp:v1
docker login
docker push correctuser/myapp:v1
```
Verify the new repository and tag in Docker Hub. Remove the incorrect remote tag only if you have the required permissions and have confirmed it is safe.

## Scenario 14: A cloud VM is generating unexpected charges

**Question:** Students created Docker practice VMs and forgot to delete them. The cloud bill is increasing. What should they do?

**Answer:**
- Review active VMs, disks, snapshots, reserved IPs, and other billable resources in the cloud console.
- Stop or delete resources that are no longer needed, following the lab's cleanup process.
- Check whether persistent disks or snapshots remain after VM deletion.
- Set budgets and billing alerts.
- Use scheduled shutdown or auto-delete policies where appropriate.

Stopping a VM may not stop charges for attached storage, snapshots, or reserved addresses.

## Scenario 15: SSH access to the cloud VM is failing

**Question:** Docker is installed on a VM, but a student cannot SSH into it. What do you check?

**Answer:**
1. Confirm the VM is running and has the correct public IP.
2. Verify TCP port 22 is allowed in cloud firewall or security-group rules.
3. Restrict the source to the student's trusted public IP where possible.
4. Check the SSH username, key, file permissions, and VM access configuration.
5. Review OS-level firewall and SSH service status using an approved access method.

Changing cloud regions should not be the first troubleshooting step; first identify the actual network or authentication failure.

## Scenario 16: You need to update a Docker image used by 100 containers

**Question:** A Java dependency has a security issue. One hundred containers are running the affected image. How would you update them?

**Answer:** Update the dependency, rebuild the image, run tests and vulnerability scans, publish a new versioned image, and roll out the new version using the deployment platform. Verify that all instances are running the intended version. Do not manually modify each running container's filesystem.

## Scenario 17: A student uses the wrong port mapping for Jenkins

**Question:** Jenkins listens on port 8080 inside the container, but the student starts it using `-p 9090:80`. Why does the browser fail?

**Answer:** The host port is mapped to container port 80, but Jenkins is listening on container port 8080. Use:
```bash
docker run -d --name jenkins \
  -p 9090:8080 \
  jenkins/jenkins:lts
```
Then access `http://VM-IP:9090`, assuming the cloud and host firewalls allow the connection.

## Scenario 18: You need to run a multi-container application

**Question:** Your application requires a frontend, backend, and MySQL database. Should you start each container manually?

**Answer:** For a repeatable multi-container setup, Docker Compose is a suitable option. Define services, networks, environment configuration, and persistent volumes in a Compose YAML file, then use:
```bash
docker compose up -d
docker compose ps
docker compose logs
```
For larger production environments requiring cluster scheduling and orchestration, evaluate Kubernetes or another suitable platform.

## Scenario 19: A Docker installation script is downloaded from the internet

**Question:** An engineer downloads a Docker installation script using `curl`. Is it safe to execute it immediately?

**Answer:** No. First verify that the source is trusted, inspect the script, confirm it is appropriate for the OS, and understand the changes it makes. Prefer a documented installation method approved by the organization. If a dry-run option is supported, use it before execution. A dry run is not a substitute for reviewing the script.

## Scenario 20: You need a secure Docker workflow for a training lab on GCP or AWS

**Question:** You are setting up a shared student VM for Docker practice. How would you reduce security and billing risks?

**Answer:**
1. Give each student an individual account or isolated environment where practical.
2. Restrict SSH access to trusted source IPs.
3. Open only the application ports needed for the exercise.
4. Avoid sharing cloud credentials or Docker Hub access tokens.
5. Use trusted images and avoid privileged containers.
6. Set VM and disk sizes appropriate for the lab.
7. Configure budgets and billing alerts.
8. Schedule shutdown or cleanup of temporary resources.
9. Verify that disks, snapshots, and public IP resources are also cleaned up when no longer needed.
10. Provide students with a cleanup checklist before the lab ends.

---

# Quick Revision — Docker Commands

| Task | Command |
|---|---|
| Check Docker version | `docker --version` |
| Docker help | `docker help` |
| List running containers | `docker ps` |
| List all containers | `docker ps -a` |
| Pull an image | `docker pull nginx` |
| List images | `docker images` |
| Run a container | `docker run -d nginx` |
| Run with a name and port | `docker run -d --name web -p 8080:80 nginx` |
| Start a container | `docker start <container>` |
| Stop a container | `docker stop <container>` |
| Kill a container | `docker kill <container>` |
| Remove a container | `docker rm <container>` |
| Remove an image | `docker rmi <image>` |
| View container resource usage | `docker stats` |
| View processes in a container | `docker top <container>` |
| View logs | `docker logs <container>` |
| Enter a container | `docker exec -it <container> sh` |
| Build an image | `docker build -t myapp:v1 .` |
| Tag an image | `docker tag myapp:v1 username/myapp:v1` |
| Log in to Docker Hub | `docker login` |
| Push an image | `docker push username/myapp:v1` |
| Pull Jenkins image | `docker pull jenkins/jenkins:lts` |
| Run Jenkins | `docker run -d --name jenkins -p 8080:8080 jenkins/jenkins:lts` |
| Read Jenkins initial password | `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword` |
| Run a temporary container | `docker run --rm alpine echo hello` |
| List Docker networks | `docker network ls` |
| List Docker volumes | `docker volume ls` |

---

# Student Practice Checklist

- [ ] Create a Docker Hub account and verify the username.
- [ ] Install Docker on an Ubuntu VM and verify the service.
- [ ] Pull and run NGINX and Alpine images.
- [ ] Practice `ps`, `ps -a`, `start`, `stop`, `kill`, `rm`, and `rmi`.
- [ ] Run five or more Alpine containers using a Bash `for` loop.
- [ ] Run Jenkins as a container and access it through a browser.
- [ ] Retrieve the Jenkins initial admin password using `docker exec`.
- [ ] Create a Dockerfile using `FROM`, `RUN`, and `CMD`.
- [ ] Build, tag, log in, and push an image to Docker Hub.
- [ ] Verify the pushed image by pulling it from another environment.
- [ ] Explain why Jenkins needs persistent storage.
- [ ] Review VM firewall rules and restrict SSH access.
- [ ] Delete unused lab resources and verify that disks and snapshots are not left behind.

**Interview tip:** Explain the command, the problem it solves, and how you verify the result. For production questions, also discuss security, persistence, monitoring, rollback, and cost.
