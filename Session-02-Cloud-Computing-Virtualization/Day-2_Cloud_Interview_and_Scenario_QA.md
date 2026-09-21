## Key Outcomes

Day 2 of the Multi-Cloud plus DevOps with AI program focused on building foundational understanding of cloud computing, starting from the problems with physical data centers and progressing through cloud service models (IaaS, PaaS, SaaS), deployment models (public, private, hybrid cloud), data center region selection, and a live hands-on demonstration of creating and connecting to a virtual machine on Google Cloud Platform. Participants also observed Docker installation and container execution on the provisioned VM, illustrating the full stack from infrastructure to platform to software service in real time. 12

---

## Session Context

- **Program:** Multi-Cloud plus DevOps with AI — Day 2 of 55 live sessions 2
- **Instructor:** Vikas
- **Format:** Live interactive session streamed simultaneously on YouTube; private Q&A segment followed the public stream 34
- **Batch:** Batch 45 (freshers and professionals with mixed experience levels) 56
- **Platform:** Zoom with YouTube live stream

---

## Data Centers — Problems with On-Premises Infrastructure

Vikas opened with a foundational scenario: imagine building a company website in 2010 without any cloud option. Participants were asked to identify all the responsibilities involved, surfacing the core pain points of the data center model. 78

**Setup and procurement challenges:**

- Required purchasing a domain, physical hardware (servers, storage, networking equipment), and assembling it on-premises 910
- Operating system installation, web server setup, database configuration, and networking all had to be done manually
- End-to-end setup could take anywhere from **5 to 10 days to one month**, factoring in approvals, procurement, and configuration 10

**Scalability limitations:**

- A server running on 4 GB RAM and 2-core CPU cannot handle a sudden spike from 100 to 10,000 users — load balancers and additional hardware would be required 1112
- One-time investments in hardware for short-lived traffic spikes (e.g., a 3-day "Big Billion Sale") result in wasted capital for the remaining days of the year 12
- Upgrading RAM in a physical laptop or server is constrained by hardware slot limits; replacing 16 GB sticks with 32 GB sticks means discarding existing hardware — a direct financial loss 1314

**Operational and maintenance problems:**

- Equipment failures (hardware, power, network) require physically visiting the data center to diagnose and resolve 15
- Procuring replacement equipment takes **2 to 5 days minimum**, meaning the business faces extended downtime 16
- Bearing 2–5 days of downtime in 2025–2026 is commercially unacceptable — competitors can displace you within that window 16
- Requires a **24/7 monitoring team** to manage infrastructure 17
- Disaster scenarios (earthquake, power outage, fire) can take down the entire data center with no fallback 17

**Cost inefficiency:**

- Unused capacity (hardware sitting idle after a sale event) represents sunk cost with no return 12
- Rented data center space still incurs ongoing costs for power, cooling, and maintenance even during low-usage periods 17

**Conclusion on data centers:** These cumulative problems — slow provisioning, limited scalability, high cost, operational complexity, and disaster vulnerability — drove companies to migrate toward cloud. 18

---

## What Is Cloud Computing

**Core definition established during discussion:**

- Cloud computing = accessing someone else's physical machine (compute resources) **over the internet** 19
- The provider owns and manages the physical hardware; the consumer accesses it remotely
- Virtualization is the enabling technology: if a machine has virtualization enabled, it can be accessed over the internet and treated as a cloud resource 2021
- Pay-per-use model: you pay only for what you consume 22

**Participant-contributed definitions (synthesized):**

- "A physical machine sitting somewhere else which we can access remotely" — Bijay 23
- "A rented virtual machine where RAM, CPU, and configuration can be adjusted" — Abhinav 19
- Cloud providers (AWS, GCP, Azure, IBM, Oracle) manage the physical data centers; users access virtual resources on top of them 24

**Architecture of cloud (as presented):**

- End users (desktops, laptops, smartphones, tablets) connect via the internet to cloud services hosted in provider-managed data centers 25
- The cloud provider's data center is essentially a large-scale, highly managed version of the traditional data center — but with virtualization enabled at scale 26

---

## Data Center Region Selection — Latency-Based Decision

A key practical topic was how to choose the right cloud region/data center for deployment. 2728

**Primary selection criterion: latency**

- The correct approach is to **ping all available AWS/GCP regions** and select the one returning the fastest response 2829
- Vikas demonstrated using a ping test website; Mumbai and Hyderabad consistently returned the fastest responses for India-based participants 2930
- The region selection should reflect **where your customer base is located** — confirmed by participant Mohit: "Where our customer base exists" 27

**High Availability (HA) and Disaster Recovery (DR) considerations:**

- Never place both primary and DR data centers in the **same region** — if a regional disaster (flood, fire, power outage) occurs, both go down simultaneously 3132
- Sachin correctly identified this: selecting both Mumbai and Hyderabad is risky because they are geographically close and could both be affected by an India-wide outage 31
- Best practice: select one data center in India (e.g., Mumbai, based on latency) and a **second in a different country** — Malaysia or Singapore were cited as examples offering strong latency for Indian users 33
- The analogy used: a class monitor and assistant monitor should come from **different groups**, not be best friends — redundancy requires genuine separation 34
- Exception: if government or regulatory data residency laws require data to remain within a specific country (common in EU), then the selection criteria must accommodate compliance constraints 3233

**Interview tip noted:** Region selection is a real interview question; the expected answer is **latency-based selection**, with HA requiring geographically distributed regions. 33

---

## Cloud Deployment Models

Three deployment models were defined: 353637

**Public Cloud:**

- Resources hosted by a third-party provider, accessible over the public internet
- Cost-effective, easy to manage, highly scalable
- Example: hosting a website with a public IP accessible to anyone 38

**Private Cloud:**

- Resources restricted to a specific organization or team
- Highly secure; access typically requires **VPN connectivity**
- Rules and regulations are set by the organization itself 36
- Example: internal company tools like Outlook accessible only within the corporate network 37

**Hybrid Cloud:**

- Combination of public and private cloud environments working together
- Allows organizations to keep sensitive workloads private while leveraging public cloud scalability 38

---

## Cloud Service Models — IaaS, PaaS, SaaS

Vikas used a live GCP demo to illustrate each service model concretely. 394041

**IaaS — Infrastructure as a Service:**

- You receive the raw compute infrastructure: virtual machine, operating system, CPU, memory, disk
- Example demonstrated: creating a GCP VM instance (named "Sunil") with Ubuntu 24.04 LTS, 25 GB disk, networking configured — this is IaaS 39
- The user is responsible for everything above the OS layer
- Analogy: an **unfurnished flat** — bare walls, you decide what goes inside 4142

**PaaS — Platform as a Service:**

- A platform is built on top of the infrastructure; software and runtime environments are pre-installed
- Example demonstrated: installing Docker on the GCP VM converts it into a **Dockerized/containerized platform** — this is PaaS 4043
- Analogy: a **semi-furnished flat** — some furniture provided; you customize the rest
- Real-world PaaS examples: Kubernetes Engine, managed databases, Canva (where you customize within the platform's capabilities) 42

**SaaS — Software as a Service:**

- Ready-made software delivered directly to the end user; no customization of the underlying platform or infrastructure
- Analogy: a **PG (paying guest) accommodation** — fully ready, move in and use immediately 42
- Examples: Gmail (cannot customize the backend), Google Docs (use as-is), Windows 11 emulator running as a Docker container (end user just uses the software) 4042
- Clarification for participant Mohd: SaaS means the software is pre-built and handed to you; you consume it but cannot modify its architecture 4445

---

## Live GCP Demo — VM Creation and Docker Deployment

A complete hands-on walkthrough was conducted on a live Google Cloud account. 4647

**VM Creation — Multiple Methods Discussed:**

Vikas asked participants how many ways a VM can be created. Answers surfaced: 4849

- **UI/Console:** Navigate to GCP Console → Compute Engine → Create Instance, fill the template form
- **CLI/Terminal/CloudShell:** Execute `gcloud compute instances create` commands directly in the browser-based terminal
- **SDK (Python/scripting):** Use Google Cloud SDK to programmatically create instances
- **Terraform (IaC):** Infrastructure as Code approach — write declarative configuration files and execute
- **REST API:** Auto-generated API call from the GCP console's "Equivalent REST" button; can be handed to developers to trigger VM creation programmatically (e.g., when a resume is uploaded to a web server, a REST API call auto-creates an app server) 505152
- **CloudFormation** (AWS-specific, noted by participant Pravin) 53

**Step-by-step VM creation demonstrated:**

1. Selected project (named "March 22" as a project identifier) 54
2. Named the instance: **Sunil** 55
3. Selected operating system: **Ubuntu 24.04 LTS** — chosen because it is the most commonly used in real-world company environments; LTS (Long-Term Support) is mandatory for production 555657
4. Disk size: **25 GB** (for learning purposes; production typically uses 512 GB or more) 5558
5. Networking/firewall tags configured to allow internet access 55
6. Clicked **Create** — the equivalent CLI command was auto-generated, showing the full `gcloud` command 55

**CLI execution via CloudShell:**

- Copied the generated CLI command, pasted into GCP CloudShell (browser-based Linux terminal) 5960
- VM was created within **30 to 60 seconds** — contrasted with the 2–5 day procurement cycle for physical hardware 60
- Output showed: instance name (Sunil), zone (us-central), machine type, **internal IP** (private, VPC-only communication) and **external IP** (publicly accessible over internet) 61

**SSH connection to VM:**

- Connected via browser-based SSH from GCP Console — SSH keys (public/private) were automatically transferred and validated 6263
- Connected on **port 22** (standard SSH port — flagged as an interview question) 63
- Logged in as root user on the provisioned Ubuntu machine 63

**Docker installation and container run:**

- Best practice first step on any new VM: `sudo apt update` to update all packages 64
- Installed Docker using the Ubuntu-provided installation command (279 MB storage required; confirmed with `Y`) 4365
- Verified Docker version: `docker -v` → returned **Docker version 29.13** 6667
- General rule: `hyphen-V` or `--version` works for checking the version of almost any installed software 66
- Ran a Docker container (Windows 11 emulator image) on **port 3000** 68
- Checked running containers: `docker ps` — confirmed container running after \~4 seconds 69
- Accessed the running application via the VM's external IP on port 3000 — confirmed working by participants 70

**Port mapping explained:**

- Container port (3000) mapped to VM port (3000); multiple containers can run on different ports simultaneously 7172
- `docker run` flags: `-d` (detached/background mode), `--restart never` or `unless-stopped`, `--name` for container naming 71

---

## Linux OS Selection for Corporate Environments

An interview-relevant discussion on which Linux distribution to use: 737475

- **Ubuntu:** Open-source, freely available, Debian family — most commonly used in general corporate environments; answer for most interview contexts 7375
- **Red Hat Enterprise Linux (RHEL):** Preferred in **enterprise environments** specifically because of **paid enterprise support** (24/7 support contracts), compliance requirements, and security certifications 7475
- **CentOS:** Derived from RHEL; free but without the enterprise support — acceptable answer in many contexts 73
- **SUSE Enterprise:** Also valid for enterprise environments (noted by participant Saeed) 74
- Interview guidance: if the interviewer uses the word **"enterprise"**, answer Red Hat/RHEL; otherwise, Ubuntu is the safe default 73
- Mandhir added: enterprises also choose RHEL because not all packages/libraries are freely available, enforcing controlled, audited environments 75

**Why companies don't rush to upgrade OS versions:**

- Companies run POCs (Proof of Concepts) before migrating; automations and scripts can break on version upgrades 76
- Migration happens when Microsoft/vendor announces **end of support** for an OS version, or when a client mandates a specific version 76
- Example: some ATM networks still run Windows XP in isolated, secure environments 76

---

## REST API Role in Cloud Automation

A nuanced discussion clarified the DevOps/Cloud engineer's role with REST APIs: 527778

- DevOps/Cloud engineers do **not** write REST API code themselves — that is a developer responsibility
- The engineer's role: generate the REST API endpoint/command from the cloud console and **hand it to the developer**
- Developer integrates it into the application (e.g., web server triggers a POST request → new app server VM is automatically created)
- This is the practical meaning of REST API in a cloud engineering context: infrastructure provisioned on demand via API calls from application logic 5278

---

## Key Interview Questions Highlighted

Throughout the session, Vikas flagged several real interview questions: 335763

- **Why did you select a particular cloud region?** → Answer: latency-based selection; ping all regions, choose fastest response 33
- **How many ways can you create a VM?** → UI console, CLI/terminal, SDK/scripting, Terraform/IaC, REST API, CloudFormation (AWS) 4849
- **Which Linux OS is most commonly used in corporate/web environments?** → Ubuntu (general); Red Hat/RHEL (enterprise) 73
- **What is the SSH port?** → Port 22 63
- **Which Ubuntu version do you use in production?** → Ubuntu 24.04 LTS (Long-Term Support is mandatory for production) 5557
- **What is IaaS / PaaS / SaaS?** → Infrastructure/Platform/Software as a Service with concrete examples 41
- **How to check Docker version?** → `docker -v` or `docker --version`; the `-V` flag works for most software 66
- **How to check running containers?** → `docker ps` 69
- **What is the difference between internal IP and external IP?** → Internal: private VPC communication only; External: publicly accessible over internet 3961

**Interview confidence guidance:**

- When asked about your company's VM size or OS version, give a specific confident answer based on real or plausible experience — "In my current project, we use Ubuntu 24.04 LTS with 16 GB RAM and 512 GB storage" is an acceptable and strong answer 5758
- Freshers should not be intimidated by experienced participants answering questions — focus on building your own level progressively 679

---

## Session Logistics and Participant Guidance

- **Discipline during live sessions:** Participants asked to stay muted unless speaking; use spacebar to unmute; raise hand before speaking — to keep recordings clean for YouTube and future reference 3
- **Camera etiquette:** Participants encouraged to keep cameras on during streaming, as the session is live on YouTube and visible to a global audience 80
- **Recording availability:** All 55 sessions will be recorded and available for review; PPT and notes will be accessible 81
- **LMS (Learning Management System):** The platform where recordings and course materials are hosted; access issues were flagged by some participants and Vikas committed to having his team resolve them via a poll 828384
- **Practical accounts:** GCP account creation is scheduled for **Day 5** of the program — participants should not attempt to replicate the live demo independently before that session 85
- **System requirements:** A laptop with Chrome browser is sufficient; no high-end specs required — GCP/AWS/Azure all run in the browser; 8 GB RAM and SSD recommended minimum for smooth performance 8687
- **Time management:** Vikas requested all participants join by 8:00 AM sharp; late arrivals may be locked out after 8:15 AM as the session start is tied to referral and networking opportunities at the beginning 88
- **LinkedIn activity:** Participants were encouraged to post daily learnings on LinkedIn, take notes, and share screenshots — active LinkedIn presence increases visibility for job referrals 8990

---

## Action Items

- **All participants:** Post daily learning notes and session takeaways to LinkedIn; tag connections and stay active 90
- **All participants:** Join the WhatsApp community via the group link for session updates, notes, and access to 20 interview questions and 20 scenario questions per class 48191
- **All participants:** Do not attempt GCP practical setup independently — wait for Day 5 when GCP account creation is formally covered 85
- **Participants with LMS access issues (including Ankur):** Vikas to create a poll in the WhatsApp group; team will resolve access issues individually 84
- **Participant Anurag:** Vikas to verify batch enrollment (Batch 44 vs Batch 45) based on admission date (July 10 cutoff for Batch 45) 92
- **All participants:** Review Day 1 and Day 2 recordings available in the LMS/WhatsApp group before Day 3 93

---

## Open Questions

- Whether the session start time can be shifted to 7:30 AM to accommodate participants who need to leave for office by 9:00 AM — Vikas acknowledged the request but indicated it depends on overall batch consensus; no final decision made 9495
- Participants raised questions about auto-scaling (scaling VMs without shutdown) — Vikas acknowledged this is valid and will be covered in practical sessions with hands-on exercises 1896


---

# 20 Interview Questions & Answers

## 1. What is cloud computing?
**Answer:** Cloud computing means accessing compute resources such as servers, storage, networking, and software over the internet. The cloud provider manages the physical infrastructure, while customers consume the resources as needed.

## 2. Why did companies move from traditional data centers to cloud?
**Answer:** Traditional data centers require hardware procurement, manual setup, maintenance, 24/7 monitoring, and large upfront investment. Cloud provides faster provisioning, easier scalability, pay-per-use pricing, and managed infrastructure.

## 3. What is the difference between a physical server and a cloud VM?
**Answer:** A physical server is dedicated hardware that you own or lease, while a cloud VM is a virtual machine created on a provider's physical infrastructure. Cloud VMs can usually be created, resized, and removed much faster.

## 4. What is IaaS?
**Answer:** IaaS stands for Infrastructure as a Service. It provides infrastructure such as virtual machines, CPU, memory, storage, and networking. The customer manages the operating system and everything above it.

## 5. What is PaaS?
**Answer:** PaaS stands for Platform as a Service. The provider manages the infrastructure and platform/runtime, allowing the customer to focus more on deploying and running applications.

## 6. What is SaaS?
**Answer:** SaaS stands for Software as a Service. It provides ready-to-use software over the internet. The user consumes the application without managing the underlying infrastructure or platform.

## 7. What is a public cloud?
**Answer:** A public cloud is an environment where cloud resources are provided by a third-party provider and can be accessed over the internet. AWS, Azure, and GCP are common examples.

## 8. What is a private cloud?
**Answer:** A private cloud is dedicated to a specific organization. Access and infrastructure are controlled by that organization, often with additional network and security controls.

## 9. What is hybrid cloud?
**Answer:** Hybrid cloud combines private and public cloud environments. For example, sensitive systems can remain in a private environment while scalable application workloads run in a public cloud.

## 10. How do you select a cloud region?
**Answer:** A major factor is latency. I would test latency from the customer locations to available regions and select a suitable region with low latency. I would also consider compliance, availability, cost, and disaster recovery requirements.

## 11. Why should primary and DR infrastructure not be placed in the same region?
**Answer:** A regional disaster such as a major power failure, flood, or other outage could affect both environments. Geographic separation reduces the risk of losing both primary and DR infrastructure at the same time.

## 12. What is the difference between internal and external IP?
**Answer:** An internal IP is used for private communication inside the cloud network or VPC. An external IP is reachable from outside the private network, subject to firewall and security rules.

## 13. What is SSH and which port does it use?
**Answer:** SSH stands for Secure Shell and is commonly used to securely connect to Linux servers remotely. The standard SSH port is TCP 22.

## 14. Why is Ubuntu commonly used?
**Answer:** Ubuntu is open-source, widely adopted, has strong community support, and is commonly used for cloud and web workloads. For enterprise-specific requirements, organizations may choose distributions such as RHEL.

## 15. What is an LTS version?
**Answer:** LTS means Long-Term Support. An LTS release receives long-term maintenance and security updates, making it suitable for stable production environments.

## 16. How can you create a cloud VM?
**Answer:** A VM can be created through the cloud console, CLI, SDK/script, Infrastructure as Code such as Terraform, or APIs. In AWS, CloudFormation is another Infrastructure as Code option.

## 17. What is Infrastructure as Code?
**Answer:** Infrastructure as Code, or IaC, means defining infrastructure using code or configuration files instead of manually creating resources. Terraform is a common example.

## 18. How do you check the Docker version?
**Answer:** Use `docker -v` or `docker --version`.

## 19. How do you check running Docker containers?
**Answer:** Use `docker ps`. It displays currently running containers.

## 20. What is port mapping in Docker?
**Answer:** Port mapping connects a port on the host machine to a port inside the container. For example, mapping VM port 3000 to container port 3000 allows users to access the application through the VM's IP and port 3000.

---

# 20 Scenario-Based Questions & Answers

## 1. Your website suddenly receives traffic from 100 users to 10,000 users. What would you do?
**Answer:** I would first monitor CPU, memory, network, and application metrics. Then I would use load balancing and horizontal scaling/auto-scaling to add capacity. I would also check database and other dependent services because scaling only the web servers may not solve the complete bottleneck.

## 2. Your application is hosted in Mumbai, but most customers are in Singapore. What would you investigate?
**Answer:** I would measure latency from the customer locations to different cloud regions. If business and compliance requirements allow it, I would evaluate deploying the workload closer to the users, such as Singapore, to reduce network latency.

## 3. Your primary cloud region goes down. What should happen?
**Answer:** A properly designed DR architecture should allow traffic or workloads to be restored in a separate region. The exact recovery approach depends on the application's RTO, RPO, data replication design, and business requirements.

## 4. Your company says customer data must remain inside India. How would this affect region selection?
**Answer:** Data residency becomes a mandatory constraint. I would select an appropriate Indian region or regions that satisfy the requirement, and then evaluate latency, availability, cost, and DR options within the allowed geographic boundaries.

## 5. A new VM is created, but you cannot SSH into it. What would you check?
**Answer:** I would check whether the VM is running, whether it has the expected IP address, whether port 22 is allowed by firewall/security rules, whether routing is correct, and whether the SSH key or credentials are valid.

## 6. Your Docker container is running, but users cannot access the application.
**Answer:** I would check `docker ps`, confirm the application is listening on the expected container port, verify host-to-container port mapping, and then check cloud firewall/security rules and whether the VM's external IP is reachable.

## 7. A physical server takes several days to replace after hardware failure. How would cloud help?
**Answer:** Cloud allows a replacement VM or infrastructure to be provisioned through a console, CLI, API, or IaC. This can reduce provisioning time significantly, assuming the required images, data, networking, and automation are already prepared.

## 8. Your company has sensitive workloads but also needs public-cloud scalability. Which model could fit?
**Answer:** A hybrid-cloud model could fit. Sensitive workloads can remain in a controlled private environment while scalable workloads use public cloud resources, depending on security, networking, compliance, and application architecture.

## 9. Your cloud bill is high because servers are idle after a temporary sales event. What would you investigate?
**Answer:** I would review resource utilization and identify idle or oversized resources. Then I would consider auto-scaling, scheduled scaling, right-sizing, and shutting down non-production resources when they are not required.

## 10. You need to create 50 identical VMs. Would you create them manually?
**Answer:** I would avoid manual creation at that scale. I would use Terraform or another automation/IaC approach so the infrastructure is repeatable, version-controlled, and easier to manage.

## 11. A developer wants the application to create a VM automatically after a user submits a request. How can this be implemented?
**Answer:** The application can call a cloud API or service through an authenticated backend workflow. The developer handles application-side API integration, while the cloud/DevOps engineer provides the required infrastructure, permissions, networking, and automation design.

## 12. Your company wants production stability but a new OS version has been released. Would you upgrade immediately?
**Answer:** I would not upgrade production immediately without validation. I would test the new version in a POC or non-production environment, verify application compatibility and automation, and then plan a controlled migration.

## 13. Your application works inside the VM but not from the internet. What could be wrong?
**Answer:** The application may be listening only on localhost, the required host/container port may not be mapped, or cloud firewall/security rules may block the port. I would validate each layer from the application to the network.

## 14. You have a primary region and a DR region, but both are geographically close. What concern do you have?
**Answer:** A large regional or geographic event could potentially affect both environments. I would evaluate greater geographic separation while considering latency, data residency, replication capability, and recovery objectives.

## 15. A container exits immediately after starting. How would you troubleshoot?
**Answer:** I would first check `docker ps -a` to see the container status and then inspect logs using `docker logs <container-name-or-id>`. I would verify the image, startup command, environment variables, dependencies, and application errors.

## 16. Your VM has an external IP, but the application is still unreachable on port 3000. What would you check?
**Answer:** I would verify that the application is listening on port 3000, confirm Docker port mapping, check the VM's firewall/security rules, and verify that the cloud network allows inbound TCP traffic on port 3000.

## 17. Your company wants a ready-to-use email application without managing servers. Which cloud service model matches this requirement?
**Answer:** SaaS is the closest match because the user consumes a ready-made application without managing the underlying infrastructure or platform.

## 18. Your team wants a managed database instead of maintaining the database server OS and patches. Which service model is this closest to?
**Answer:** This is generally a PaaS-style managed service because the provider manages much of the underlying infrastructure and platform while the customer focuses on using the database service.

## 19. Your team wants complete control over the OS, installed packages, and server configuration. Which model would you choose?
**Answer:** IaaS would provide the required level of control because the team receives a VM and manages the operating system and software installed above the infrastructure layer.

## 20. During a production incident, someone suggests putting everything in one region because it has the lowest latency. What else should you consider?
**Answer:** Latency is important, but it should not be the only factor. I would also evaluate high availability, disaster recovery, compliance/data residency, service availability, cost, data replication, and the application's RTO/RPO before finalizing the architecture.
