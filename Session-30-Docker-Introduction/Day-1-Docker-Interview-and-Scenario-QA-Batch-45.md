## Key Outcomes

The session introduced Docker from first principles, covering the motivation behind containerization, the evolution from physical machines to virtual machines to containers, and Docker's core architecture. Students gained conceptual clarity on Docker vs. Kubernetes, microservices architecture, and the Docker workflow (Dockerfile → Image → Container → Registry). The session concluded with a live hands-on demonstration: installing Docker on a GCP Ubuntu VM and running the first container using the `hello-world` image. 

---

## Decisions Made

- Sessions will follow a **theory-first, then practical** approach for Docker topics 
- **30 GB disk size** recommended for VMs going forward to accommodate multiple containers 
- **Ubuntu 24.04 LTS** (non-minimal) selected as the standard OS for Docker practicals 
- **Google Cloud (GCP)** designated as the primary platform for Kubernetes labs 
- VM instances must be **deleted after each session** to avoid unnecessary billing charges 

---

## Core Concepts Covered

### Why Docker Exists: The Environment Problem

- Classic developer complaint: *"It works on my machine"* — code runs fine in dev but fails in ops/production 
- Root cause is never the code itself; it is always an **environment or configuration mismatch** 
- Docker solves this by packing code, configuration, tools, frameworks, and dependencies into a single portable unit 

### Virtual Machines: The Predecessor and Its Failures

- **Virtualization** = accessing another machine's resources over the internet via software abstraction 
- **Setup chain:** Physical hardware (Dell/HP/IBM) → Host OS → **Hypervisor** (e.g., Oracle VirtualBox) → Guest OS → Application 
- Virtualization must be **enabled in BIOS/CPU settings**; students were asked to verify and type `E` (enabled) or `D` (disabled) in chat 
- Oracle VirtualBox is a ~170 MB open-source hypervisor download; requires 50%+ free RAM and CPU to run 
- Guest OS images (e.g., CentOS at 1.4 GB zip → ~4–5 GB extracted → ~10–15 GB with app) make VMs extremely heavy 

**VM Advantages:**

- Run multiple OS environments simultaneously on one machine 
- Offers same architecture as a real physical computer 
- Useful for testing across Ubuntu, CentOS, Windows, Mac in parallel 

**VM Disadvantages (reasons the concept partially failed):** 

- Single hardware or host OS failure brings down **all** VMs
- Very high resource consumption (CPU, RAM, disk)
- Heavy size makes **migration between environments extremely difficult**
- Resources are reserved even when idle — no pay-as-you-go
- Manual management of 100+ VMs is impractical 
- Scaling (sudden traffic spikes) is nearly impossible 
- Hardware dependency limits portability 

### Containers vs. Virtual Machines

- **Container** = a standard, lightweight, packed unit of software containing binary, libraries, dependencies, and code 
- **Image** = the packed/static version of the application (like a zip file) 
- **Container** = the running instance of an image (like an unzipped, executing application) 
- One image can spawn **multiple containers** across different environments 
- Containers are far lighter than VMs because they share the host OS kernel — no guest OS needed per service 

### Docker Defined

- **Docker** is a tool for building, shipping, and running any application from any environment to any environment 
- It ensures an image created in one environment runs identically in all others 
- DevOps engineers' response to "it works on my machine": *"No worries — we'll deploy your machine to production"* 

---

## Docker Architecture Deep Dive

### Three-Part Architecture 

1. **Docker Client** — the terminal/CLI where commands are typed
2. **Docker Host (Daemon)** — background service (`dockerd`) that processes all commands; runs as a daemon (constantly running in the background) 
3. **Docker Registry (Docker Hub)** — centralized image storage; 3.5 million+ publicly available images 

### Command Flow (Hello World Walkthrough) 

When `docker run hello-world` is executed:

1. **Docker client** connects to the **Docker daemon**
2. Daemon checks locally — image not found
3. Daemon pulls the `hello-world` image from **Docker Hub**
4. Daemon creates a **new container** from the image
5. Daemon **streams output** back to the Docker client terminal

### Image Registries Available 

- **Docker Hub** — most popular, public registry
- **ECR** (Elastic Container Registry) — AWS
- **ACR** (Azure Container Registry) — Azure
- **GCR** (Google Container Registry) — GCP
- **JFrog Artifactory** — open source
- **Nexus** — open source
- Teams choose their registry based on cloud provider or enterprise policy; images must be explicitly pushed — they are not auto-uploaded 

---

## Dockerfile Explained

### What Is a Dockerfile? 

- A **plain text file** (no extension) that acts as a **blueprint/recipe** for building an image
- The `D` in `Dockerfile` must always be **capitalized**
- Contains instructions/keywords executed in **strict sequential order**
- Typically **8–10 lines** for most use cases 

### Key Keywords (to be covered in depth next session) 

- `FROM` — base image to start from
- `WORKDIR` — sets working directory inside container
- `COPY` — copies files into the image
- `RUN` — executes shell commands during build
- `EXPOSE` — declares the port the container listens on
- `ENTRYPOINT` / `CMD` — defines what runs when the container starts

### Build Workflow 

```plaintext
Dockerfile  →  docker build  →  Docker Image  →  docker run  →  Container
                                     ↓
                               docker push  →  Docker Hub / Registry
                               docker pull  ←  Docker Hub / Registry
```

---

## Docker vs. Kubernetes

|    Aspect    |                                   Docker                                    |                    Kubernetes                    |
|--------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Primary role | **Containerization** — build, ship, run containers                          | **Orchestration** — manage containers at scale   |
| Scale        | Suitable for small/dev environments (up to ~50 containers managed manually) | Required for hundreds/thousands of containers    |
| Management   | Manual CLI commands                                                         | Automated deployment, scaling, self-healing      |
| Use case     | Image creation, single-host container runs                                  | Multi-node clusters, microservices in production |

- When managing 100+ containers manually via commands becomes impractical, **Kubernetes** takes over 
- Kubernetes manages containers through **clusters**: instead of 100 separate VMs, services are co-deployed across 3 shared VMs (e.g., 30+30+30 services) 
- Cost saving: 100 VMs billed individually vs. 3 clustered VMs 

---

## Microservices Architecture

### Definition 

- Application broken into **small, independent, loosely-coupled services**
- Each service runs, deploys, and scales independently
- If one service fails (e.g., payment), the rest of the application (browsing, cart) continues working — unlike **monolithic** architecture where one failure brings everything down 

### Benefits 

- **Independent deployment** — each service updated without redeploying the whole app
- **Fault isolation** — only the affected service goes down
- **Technology flexibility** — each service can use a different language (Java, Python, Go, .NET)
- **Easy integration** — services communicate via APIs 

### How Microservices Communicate 

- Via **Services** (Kubernetes service objects)
- Via **Labels and Selectors**
- Via **ConfigMaps**
- These are the expected interview answers for inter-service communication in Kubernetes 

### Why So Many VMs Per Application? 

- A real application has: static frontend, middleware, background services, database, analytics DB, queues, etc.
- Each component may need 2–3 VMs for high availability
- Across dev → QA → pre-prod → prod environments, this multiplies to 40–50+ VMs per environment, making manual management infeasible

---

## Hands-On: Installing Docker on GCP Ubuntu VM

### VM Setup 

- **Cloud:** Google Cloud Platform
- **VM name:** `docker-day1` (no spaces, no capitals)
- **OS:** Ubuntu 24.04 LTS (64-bit, non-minimal)
- **Disk:** 30 GB
- **Network:** Allow HTTP/HTTPS traffic

### Docker Installation Commands 

```bash
sudo -i                          # Switch to root user
apt-get update                   # Update all packages (best practice)
apt install docker.io -y         # Install Docker (no prompt)
docker --version                 # Verify: Docker 29.1.x installed
```

- Single combined command option: `sudo apt update && sudo apt install docker.io -y` 

### Key Commands Demonstrated 

```bash
docker pull ubuntu               # Download Ubuntu image from Docker Hub
docker images                    # List all locally available images (also: docker image ls)
docker run hello-world           # Run hello-world container (auto-pulls if not local)
docker ps                        # Show running containers
docker ps -a                     # Show all containers (including stopped)
```

### Observations from Live Demo 

- `docker pull ubuntu` downloaded the latest Ubuntu image; confirmed with `docker images`
- `docker run hello-world` triggered the full architecture flow — image pulled from Hub, container created, output streamed to terminal
- `docker ps` showed no running containers (hello-world exits after printing); `docker ps -a` showed the stopped container
- **Docker version installed: 29.1** 

---

## Pending Confirmation

- Students with **virtualization disabled** in BIOS need to enable it via their laptop's BIOS settings before Docker will work 
- Students using **GCP free tier** should monitor billing; accounts nearing expiry should consider creating new accounts or switching to paid 
- GitLab account verification issue: students who signed up via **GitHub OAuth** are automatically verified; those who registered directly may face credit card verification prompts — workaround being investigated 
- Rajesh's GitLab pipeline failure: YAML file validation error identified; root cause under review 

---

## Open Questions

- Shafiul's GCP account showing access-denied errors on VM creation — IAM owner access granted by Vikas as temporary fix; metadata/SSH key configuration still needed 
- Anshuman's GCP account: API not enabled error when trying to create instances — needs to enable Compute Engine API 
- Whether Docker Hub images auto-update when a project changes: **No** — images must be explicitly rebuilt and pushed; Docker Hub stores what you push 
- Running `docker run` without a prior `docker pull`: Docker **automatically pulls** the image if not found locally 
- Windows containers: Docker on Windows can technically build Windows containers, but **Linux containers are standard** and what will be used throughout the course 

---

## Action Items

- **All students:** Verify CPU virtualization is enabled (BIOS); type `E` or `D` in next session chat to confirm 
- **All students:** Download Oracle VirtualBox (~170 MB) and CentOS/Ubuntu VM images from [osboxes.org](https://osboxes.org) for optional VM practical (links shared in chat) 
- **All students:** Ensure GCP VM is **deleted after each session** to avoid billing 
- **All students:** Practice the five core Docker commands: `pull`, `images`, `run`, `ps`, `ps -a` 
- **Vikas:** Send tomorrow's session link/message to confirm schedule 
- **Vikas:** Cover **Dockerfile keywords** in depth in the next session and build the first custom image 
- **Rajesh + Sanket:** Resolve GitLab runner/YAML pipeline issue — check YAML syntax validity 
- **Vivek:** Account deletion/disabling process for GCP will be demonstrated at **batch completion** 
- **Shafiul:** Configure SSH metadata keys on GCP for proper VM access 

---

## Announcements

- This Docker + Kubernetes module spans **10–12 days**; completing it adds a minimum **2 years of experience** shortcut to résumés and can add **10 LPA** to package expectations 
- Students joining the **Zoom link** (as opposed to the primary platform) will not have attendance tracked — stay on the primary platform unless experiencing issues 
- Recording will be available within **one hour** of session end 
- Next session focus: **Dockerfile keywords**, building a custom image, and pushing to Docker Hub


---

# Interview Preparation: Docker Day 1

The following questions are based on the concepts and practical work covered in this session. Answers are kept aligned with the session notes.

## Part 1: 20 Docker Interview Questions and Answers

### 1. What is Docker?

**Answer:** Docker is a tool used to build, ship, and run applications across different environments. It packages application code, configuration, tools, frameworks, and dependencies into a portable unit so that the application behaves consistently across environments.

### 2. Why was Docker introduced?

**Answer:** Docker helps solve the “It works on my machine” problem. An application may work in a developer's environment but fail in testing or production because of differences in configuration, libraries, tools, or dependencies. Docker packages these requirements together to reduce environment mismatch.

### 3. What is a container?

**Answer:** A container is a lightweight, standard unit of software that contains the application code, binaries, libraries, and dependencies needed to run the application.

### 4. What is a Docker image?

**Answer:** A Docker image is the packaged or static version of an application. It acts as the template from which containers are created. One image can be used to create multiple containers.

### 5. What is the difference between an image and a container?

**Answer:** An image is the packaged blueprint of an application, while a container is a running instance created from that image. A useful comparison is that an image is like a zip file and a container is like the extracted, executing application.

### 6. Why are containers lighter than virtual machines?

**Answer:** Containers share the host operating system's kernel and do not need a separate guest operating system for every application. Virtual machines include a guest OS, which increases their CPU, RAM, and disk requirements.

### 7. What is virtualization?

**Answer:** Virtualization uses software to create virtual computing environments from physical hardware resources. A typical setup contains physical hardware, a host operating system, a hypervisor, a guest operating system, and an application.

### 8. What is a hypervisor? Give an example.

**Answer:** A hypervisor is the virtualization layer that allows virtual machines to run on physical hardware. Oracle VirtualBox is an example discussed in the session.

### 9. What are the major disadvantages of virtual machines discussed in the session?

**Answer:** Virtual machines consume significant CPU, RAM, and disk resources; require a guest OS; are comparatively difficult to migrate; can reserve resources while idle; and become difficult to manage and scale when their number grows. A failure in shared hardware or the host OS can also affect all VMs on that host.

### 10. What are the three main parts of Docker architecture?

**Answer:**
1. **Docker Client:** The CLI or terminal where the user runs Docker commands.
2. **Docker Host / Daemon:** The background service, `dockerd`, that processes Docker requests.
3. **Docker Registry:** A location where Docker images are stored, such as Docker Hub.

### 11. What is the Docker daemon?

**Answer:** The Docker daemon is the background service called `dockerd`. It receives and processes requests from the Docker client, such as pulling images and creating containers.

### 12. What happens when you run `docker run hello-world` for the first time?

**Answer:** The Docker client contacts the daemon. The daemon checks whether the image exists locally. If it does not, the daemon pulls it from Docker Hub, creates a container from the image, and returns the container's output to the terminal.

### 13. What is Docker Hub?

**Answer:** Docker Hub is a popular image registry where Docker images can be stored and shared. Docker can pull images from Docker Hub when they are not available locally.

### 14. Name some alternatives to Docker Hub mentioned in the session.

**Answer:** Amazon Elastic Container Registry (ECR), Azure Container Registry (ACR), Google Container Registry (GCR), JFrog Artifactory, and Nexus were mentioned as image registry options.

### 15. What is a Dockerfile?

**Answer:** A Dockerfile is a plain text file that acts as a blueprint or recipe for building a Docker image. Its instructions are processed sequentially. The filename is written as `Dockerfile`, with a capital `D` and no file extension.

### 16. What is the purpose of the `FROM` instruction in a Dockerfile?

**Answer:** `FROM` specifies the base image from which the new Docker image will be built.

### 17. Explain the purpose of `WORKDIR`, `COPY`, and `RUN` in a Dockerfile.

**Answer:**
- `WORKDIR` sets the working directory inside the image.
- `COPY` copies files from the build context into the image.
- `RUN` executes commands during the image build process.

### 18. What is the purpose of `EXPOSE`, `CMD`, and `ENTRYPOINT`?

**Answer:** `EXPOSE` declares the port on which the containerized application listens. `CMD` and `ENTRYPOINT` define what should run when the container starts. The detailed behavior and differences between `CMD` and `ENTRYPOINT` were marked for deeper coverage in a later session.

### 19. What is the difference between Docker and Kubernetes?

**Answer:** Docker is used for containerization: building, shipping, and running containers. Kubernetes is a container orchestration platform used to manage containers at scale, including deployment, scaling, and self-healing across clusters.

### 20. What is microservices architecture?

**Answer:** Microservices architecture breaks an application into small, independent, loosely coupled services. Each service can be deployed and scaled independently. Services may use different programming languages and communicate through APIs. A failure in one service, such as payment, does not necessarily bring down other services, such as browsing or the shopping cart.

---

## Part 2: 20 Scenario-Based Docker Interview Questions and Answers

### 1. Your application works on a developer's laptop but fails in production. What problem could be causing this, and how can Docker help?

**Answer:** The issue may be an environment or configuration mismatch, such as different libraries, tools, frameworks, or dependencies. Docker helps by packaging the application and its required dependencies into an image that can be run consistently in different environments.

### 2. You have created a Docker image on your laptop and want to run the same application on a GCP VM. What is the benefit of using that image?

**Answer:** The image packages the application and its dependencies. It can be used to create a container on the GCP VM, reducing differences between the development and target environments.

### 3. You run `docker run hello-world`, but you have not downloaded the image before. What should happen?

**Answer:** Docker checks whether the image exists locally. If it is missing, the daemon automatically pulls the image from the configured registry, such as Docker Hub, and then creates and runs the container.

### 4. You execute `docker run hello-world` and see the output, but `docker ps` shows no container. Is Docker broken?

**Answer:** No. The `hello-world` container prints its message and exits. `docker ps` displays running containers only, so the stopped container will not appear there. Use `docker ps -a` to see it.

### 5. You want to check all containers, including those that have stopped. Which command should you run?

**Answer:** Run `docker ps -a`. The command lists running as well as stopped containers.

### 6. You want to see only the containers that are currently running. Which command should you use?

**Answer:** Run `docker ps`. It displays currently running containers.

### 7. You want to download the Ubuntu image before creating a container. Which command should you use?

**Answer:** Run `docker pull ubuntu`. This downloads the Ubuntu image from the registry to the local machine.

### 8. You have pulled the Ubuntu image and want to confirm that it is available locally. What should you do?

**Answer:** Run `docker images` or `docker image ls`. These commands list the images available locally.

### 9. Your team has built an image, but another environment cannot pull it from Docker Hub. What should you verify?

**Answer:** Verify that the image was explicitly pushed to Docker Hub or the intended registry. Images are not automatically uploaded just because they were built locally.

### 10. A developer changes the application code, but the old version is still running from the image stored in Docker Hub. Will Docker Hub automatically update that image?

**Answer:** No. Docker Hub stores the image that was pushed. When the project changes, the image must be rebuilt and the updated image must be explicitly pushed to the registry.

### 11. Your team needs to store container images in AWS instead of Docker Hub. Which registry from the session could you use?

**Answer:** Amazon Elastic Container Registry (ECR) is the AWS image registry option mentioned in the session.

### 12. Your organization uses Azure and wants an Azure-based image registry. Which option is relevant?

**Answer:** Azure Container Registry (ACR) is the Azure registry option covered in the session.

### 13. You are asked to create a Docker image for an application. What file should you prepare first, and what naming convention should it follow?

**Answer:** Prepare a plain text file named `Dockerfile`. Use a capital `D` and do not add a file extension.

### 14. While preparing a Dockerfile, you need to choose a starting image, set an internal directory, and copy application files. Which instructions should you consider?

**Answer:** Use `FROM` to select the base image, `WORKDIR` to set the working directory, and `COPY` to copy application files into the image.

### 15. You need to install or execute a command while the Docker image is being built. Which Dockerfile instruction is relevant?

**Answer:** Use the `RUN` instruction. It executes commands during the image build process.

### 16. Your application listens on a particular port inside the container. Which Dockerfile instruction can document that port?

**Answer:** Use `EXPOSE` to declare the port on which the containerized application listens. The instruction itself is a declaration; the session notes do not cover host port publishing.

### 17. Your organization is managing a growing number of containers across multiple machines. Manually running Docker commands is becoming difficult. What technology should you consider?

**Answer:** Consider Kubernetes. Docker is used to build and run containers, while Kubernetes orchestrates containers across clusters and supports automated deployment, scaling, and self-healing.

### 18. An online shopping application has separate browsing, cart, and payment services. The payment service fails, but browsing and cart should continue working. Which architecture supports this approach?

**Answer:** Microservices architecture. Each service is independent and loosely coupled, so a failure in one service does not necessarily bring down the others.

### 19. Your application has services written in Java, Python, and Go. Can this fit the microservices approach discussed in the session?

**Answer:** Yes. Microservices support technology flexibility. Different services can use different programming languages and communicate through APIs.

### 20. You created a GCP Ubuntu VM for a Docker practical. What configuration and cleanup steps should you follow based on the session?

**Answer:** Use Ubuntu 24.04 LTS (non-minimal), a 30 GB disk, and allow HTTP/HTTPS traffic as specified in the lab setup. After the practical, delete the VM to avoid unnecessary billing. Also monitor GCP billing, especially when using a free-tier account.

---

## Quick Practical Revision

Use these commands to revise the hands-on work from Day 1:

```bash
# Switch to the root user
sudo -i

# Update package information
apt-get update

# Install Docker
apt install docker.io -y

# Verify Docker installation
docker --version

# Download the Ubuntu image
docker pull ubuntu

# List locally available images
docker images

# Run the hello-world container
docker run hello-world

# List running containers
docker ps

# List all containers, including stopped ones
docker ps -a
```

## Day 1 Revision Checklist

- [ ] Explain why Docker is needed and the “It works on my machine” problem.
- [ ] Explain physical machines, virtualization, hypervisors, and virtual machines.
- [ ] Differentiate a Docker image from a container.
- [ ] Explain Docker Client, Docker Daemon, and Docker Registry.
- [ ] Walk through what happens during `docker run hello-world`.
- [ ] Explain the purpose of the Dockerfile instructions covered in class.
- [ ] Differentiate Docker and Kubernetes.
- [ ] Explain microservices and fault isolation with an example.
- [ ] Practice `docker pull`, `docker images`, `docker run`, `docker ps`, and `docker ps -a`.
- [ ] Delete the GCP VM after completing the practical to avoid unnecessary charges.

> **Note:** These 40 questions are based on the Day 1 session notes. Topics marked for deeper coverage in a later session—such as detailed `CMD` vs. `ENTRYPOINT` behavior and Dockerfile best practices—have not been expanded beyond what the notes support.
