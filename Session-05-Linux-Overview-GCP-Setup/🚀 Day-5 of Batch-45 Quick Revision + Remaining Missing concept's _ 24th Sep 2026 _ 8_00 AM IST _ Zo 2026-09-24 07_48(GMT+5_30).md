## Key Outcomes

This session was a structured revision day covering Days 1–4 of the program curriculum, encompassing AWS fundamentals, cloud computing models, DevOps principles, and the CI/CD pipeline flow. Participants engaged in live Q&A, real-world use case discussions, and mock interview questions. The instructor confirmed no special agenda beyond comprehensive revision, emphasizing that the program roadmap progresses from Linux → AWS → DevOps → Docker/Shell Scripting. Several participants introduced themselves, sharing backgrounds ranging from freshers to 10+ year professionals across development, QA, platform engineering, and full-stack domains.

---

## Session Structure & Ground Rules

- **Thumb rule enforced throughout:** All participants to remain on mute; press spacebar to unmute when speaking; raise hand to be called upon. 
- **Camera requirement:** All attendees requested to be on camera — rationale given that interviews are conducted on camera, so practice being visible builds confidence. 
- **Question discipline:** Participants instructed not to throw questions at the start of the session; type name in chat box to signal readiness; Q&A slots allocated every 10–15 minutes. 
- **Recording policy:** Session recording belongs to participants; may be revisited after 3, 6, or 12 months; background noise degrades recording quality, hence the mute rule. 
- **Recording access:** Same LMS link used for live class becomes the recording link approximately 90 minutes after session completion. 
- **Note-taking encouraged:** Re-watching recordings costs an additional hour; taking notes during the session is more efficient; all definitions already provided in written form on the platform. 

---

## Participant Introductions

Multiple participants introduced themselves; the instructor used this as a teaching moment on professional self-presentation.

- **Vikas (participant):** 5 years experience in Laravel and React; currently at Abid Industries working on a WhatsApp AI automation SaaS platform that also supports Google Ads, Meta Ads, and WhatsApp status ads — described as a digital marketing tech platform; goal is to migrate infrastructure to AWS. 
- **Roshan Kadkar:** 5+ years as a software test engineer; experience in IoT home automation and connected car domain. 
- **Asitav:** 10 years total experience, last 5 years as a platform engineer at Infosys; client is Charter Communication; works with CI/CD, Terraform, Ansible, and AWS at a high level. 
- **Krunal Mahale:** 10+ years as a full-stack developer; PHP, Node.js, Angular, React; currently at Cactus Communication (product-based); working on an AI product that extracts text from PDFs, translates to other languages, and converts to audio using 11 Labs.  Instructor invited Krunal to present his project in the last 15 minutes of a future Friday session. 
- **Subhranil Raha:** 1.5 years experience; associate software engineer at EUI GBS; built a cloud migration image solution for Microsoft client using Python/Pillow; also has blockchain instructor experience at IAM Labs and contributed to Linux Foundation's LFX organization with Spring Boot, Java, Kafka, Docker. 
- **Instructor coaching on introductions:** Freshers cautioned not to lead with college name — interviewers care about current role and work, not institution; introduction should not exceed 90 seconds. 

---

## AWS Fundamentals

### What AWS Is

- **AWS (Amazon Web Services)** is a subsidiary of Amazon (the parent organization); provides cloud computing resources, virtualization resources, and data center services accessible via `aws.amazon.com`. 
- Core value proposition: provides servers, services, and resources on the cloud — accessible **on demand**, scalable very easily based on need. 
- AWS is described as no longer merely a cloud platform — it is the **foundational infrastructure** behind streaming apps, banking systems, AI products, e-commerce applications, and startups; billions of people depend on it daily. 
- Example: Recordings on the instructor's LMS are served from **S3**; if AWS S3 goes down, recordings become inaccessible — illustrating AWS as a basic necessity. 

### AWS History & Evolution

- Platform launched in **2002** at `aws.amazon.com/console`. 
- Early services included **SQS, EC2, VPC**; initial adoption was slow due to security concerns. 
- Timeline of key milestones: 
    - **2006:** EC2 machine available; security questions raised by market
    - **2007:** Security validation period
    - **2009:** VPC rolled out — users could create private networks and place resources securely within them
    - **2012:** Major customer event
    - **2016:** $10 billion revenue generated
    - **2017:** ~100 services launched; yearly releases accelerating
- Currently AWS offers **200+ services**; learning all of them is neither practical nor necessary. 
- **Recommended focus:** ~10 core services covering compute, networking, storage, database, and application/administrator activities — sufficient for real-world project use across 2–4 years of experience. 

### Market Share & Cloud Landscape

- AWS holds approximately **40% market share**; Azure approximately **30%**; GCP approximately **30%** (instructor noted data may be from ~2022). 
- **Google Cloud** consumption rate is increasing rapidly (~26% growth rate); may challenge top position within 2–5 years, driven by Kubernetes and AI services. 
- **Key insight:** All major cloud platforms (AWS, Azure, GCP) offer equivalent services — only the names differ; knowing one cloud deeply enables working across all.  Confirmed by participant Kishor who migrated from AWS to Oracle Cloud (OCI) and found concepts identical. 
- Instructor committed to one dedicated session comparing **top 10 services across AWS, Azure, and GCP**. 

### Traditional Infrastructure vs. Cloud

- Historical evolution used as context: floppy disks (1.2 MB) → CDs/DVDs → pen drives → Google Drive (cloud storage) → **EBS/EFS** (AWS equivalents). 
- On-premises data center rooms (with SAN/storage area networks using FTP protocol) have been replaced by cloud services like EC2 and AMIs. 
- Physical networking (LAN cables, routers) is being replaced by air fiber and soon **satellite networks** (Starlink/SpaceX already operational on ships and cruise lines). 
- Physical access control (fingerprint systems) replaced by **IAM** (Identity and Access Management) in the cloud. 
- Conclusion: Traditional infrastructure components have cloud equivalents; the underlying logic and service categories remain the same, just delivered remotely. 

---

## Cloud Computing Models

### Service Models (IaaS / PaaS / SaaS)

Explained using a pizza/kitchen/flat analogy: 

- **SaaS (Software as a Service):** Ready-to-eat pizza — user manages nothing; application, data, runtime, middleware all managed by the cloud provider; examples: Gmail, Google Drive.  User just consumes the service.
- **PaaS (Platform as a Service):** Kitchen provided — platform (runtime, middleware, Docker) is provided; user brings their own application and data; example: Canva provides the platform, user brings their own presentation content. 
- **IaaS (Infrastructure as a Service):** Empty flat/room — network, storage, servers, virtualization, and OS provided; user installs everything else (middleware, runtime, application); example: taking an EC2 instance and building on top of it. 

### Cloud Deployment Types

- **Public Cloud, Private Cloud, Hybrid Cloud** — mentioned as the three deployment models. 

### Serverless Computing

- Defined as **"five-star service"** — user brings only code; no need to manage web servers, databases, networking, or security; billing is based on execution (pay-as-you-go). 
- AWS serverless service: **Lambda**. 
- **Lambda use cases discussed:**
    - Weekly scheduled activity (e.g., backups) — no need to run a 24/7 VM for a once-a-week task. 
    - Email shooting via **SQS queue trigger**: emails placed in SQS → Lambda triggered on each entry → processes SMTP and sends email to user (real example shared by participant Pravin). 
    - File format conversion: if resumes are submitted in CSV or Word format, Lambda converts them to PDF automatically, triggered once per week. 
- **Event-driven nature:** Lambda runs based on events or need — not continuously; this is the core distinction from always-on compute. 
- **Cost consideration:** Serverless is comparatively expensive (like a five-star hotel) — best suited for infrequent, event-based tasks rather than continuous workloads. 
- **Continuous vs. event-based guidance:** For regular, continuous database usage → use **RDS** (PaaS); for once-a-week or event-based processing → use **Lambda** (serverless). 

### Extended Service Model Taxonomy

Beyond IaaS/PaaS/SaaS, additional categories discussed for senior-level understanding: 

- **FaaS (Function as a Service):** Lambda; event-driven, short-duration functions
- **CaaS (Container as a Service):** Docker and Kubernetes-related activities — recommended for those targeting higher salary packages
- **DaaS (Database as a Service):** Managed database services
- **STaaS (Storage as a Service):** Managed storage
- **CaaS (Compute as a Service):** Compute resources as a service
- Instructor note: IaaS/PaaS/SaaS sufficient for up to ~10 LPA packages; CaaS and FaaS knowledge needed for higher compensation targets. 

---

## DevOps Principles & Roles

### What DevOps Is

- DevOps automates the build, compilation, testing, and deployment pipeline so that software can be delivered continuously and with high quality. 
- Compared to **Waterfall model** (rigid sequential phases: design → code → test → deploy over 6 months, no ability to go back) — Waterfall has largely failed in modern software delivery. 
- **Agile model:** Iterative and incremental; deployments every 2 weeks; same 6-month duration produces ~9 versions vs. Waterfall's 1. 
- **DevOps with Continuous Deployment:** Designing, coding, and testing happen with automation; deployments occur daily; product quality significantly higher than other models. 

### The 6 Cs of DevOps

Explained as a continuous loop where **tools may change but the process remains constant**: 

1. **Continuous Business Planning** — ongoing feature and roadmap discussions between business stakeholders
2. **Continuous Integration (CI)** — developers write code locally → push to Git/Bitbucket via pull request → Jenkins picks up code via trigger → compiles and builds → feedback sent to developer automatically
3. **Continuous Testing** — test scripts (e.g., Selenium) scheduled in Jenkins to run multiple times per day; continuous execution of test cases
4. **Continuous Delivery** — code deployed to environments after **manual approval** (e.g., a button click or pipeline-level approval gate before production); used by ~95% of companies
5. **Continuous Deployment** — fully automated deployment with no human approval step; used by ~5% of companies (instructor advised not to claim this in interviews unless accurate) 
6. **Continuous Monitoring** — monitoring set up across all environments; alerts and logs fed back into the cycle
- **Delivery vs. Deployment distinction (critical for interviews):** Delivery requires a manual approval before production deployment; deployment is fully automated with no human gate. 
- **DevSecOps extension:** Security can be added as **Continuous Security** within the 6C framework, covering both cloud security and infrastructure (e.g., firewall configuration). 

### DevOps Pipeline Flow (Participant-Explained)

Participant Pankaj walked through a real pipeline: 

- Developer writes code in local IDE → raises **Pull Request** → approved and merged into Git/Bitbucket
- **Jenkins** picks up code via webhook or SCM trigger → builds → runs testing (Selenium)
- Jenkins triggers build/test cycle → **Docker image** created → **manifest file** generated
- Deployed to **Kubernetes**
- **Terraform** used to provision full infrastructure for Kubernetes environments
- Logging, errors, and feedback loop back to developers automatically

### DevOps Job Roles

Multiple profiles discussed as valid career targets within the DevOps ecosystem: 

- DevOps Engineer
- Site Reliability Engineer (SRE)
- Cloud Operations Engineer
- Cloud Engineer
- Infrastructure Engineer
- Platform Engineer
- DevSecOps Engineer (requires security knowledge)
- Release Engineer / Build Engineer
- AIOps
- System Administrator (foundational to all above roles)

### Skills Required for DevOps Engineers

- **System administration excellence** is mandatory — must be proficient in Linux and Windows server management, performance tuning, and environment handling. 
- **Networking and storage knowledge** required. 
- **Coding/scripting:** Full coding not required, but at least basic understanding and prompt engineering capability; automation via Python and Shell scripting. 
- **Effective communication:** Must be able to explain issues to operations teams, developers, and management; must be "explainable." 
- **Cloud management and orchestration:** Docker and Kubernetes knowledge opens senior profiles. 
- **Real-world system thinking example:** If a production server's CPU/memory hits 90% (like Zoom consuming resources in the session), you cannot simply kill the process — you must increase compute, optimize the application, or work with developers to reduce resource consumption; same logic applies to laptops and servers. 

---

## Tools & Technology Discussed

- **Jenkins:** Primary CI tool; used for integration, build automation, scheduling test scripts, and deployment pipelines; can also handle deployment but may be paired with other tools like Octopus for specific deployment features. 
- **Octopus Deploy:** Used alongside Jenkins in some organizations for deployment; instructor explained that each tool has limitations and teams may prefer specialized tools for specific stages. 
- **Terraform:** Used for full infrastructure provisioning, especially in Kubernetes environments. 
- **Docker & Kubernetes:** Core to CaaS; Kubernetes handles automatic rollback; GKE is Google's managed Kubernetes service. 
- **SonarQube:** Mentioned in context of integrating code quality checks into Jenkins pipelines (DevSecOps project reference). 
- **GitHub / Bitbucket / GitLab:** Source control platforms; Jenkins integrates via webhook or SCM trigger. 
- **AWS Lambda:** Serverless compute; configured by cloud engineers; has a dedicated full session planned. 
- **AWS SQS:** Queue service; used as event trigger for Lambda (email processing use case). 
- **AWS EC2:** Virtual machine service; front-end and middleware hosting for web applications. 
- **AWS RDS:** Managed relational database service; recommended for continuous database needs. 
- **AWS S3:** Object storage; used for resume/file storage and media delivery. 
- **AWS VPC:** Private network for securing EC2 and other resources. 
- **AWS IAM:** Identity and Access Management; replaced physical access control systems. 
- **Multiple monitoring tools:** Organizations often run more than one monitoring tool simultaneously because no single tool satisfies all requirements. 

---

## Mock Interview Questions & Answers

### Q: What is AWS?

- Best answer framework: AWS is a subsidiary of Amazon providing cloud computing resources (servers, storage, networking, databases) accessible on demand via `aws.amazon.com`; it is the foundation behind streaming, banking, AI, and e-commerce at global scale. 

### Q: If a developer pushes code to GitHub but Jenkins does not start the build, what do you check?

Answers provided by participants Mandhir and Pawan: 

- **Authentication issue** between Jenkins and GitHub (most common; private repos require credentials)
- **Plugin configuration** — required Jenkins plugins may be missing or misconfigured
- **Webhook/trigger configuration** — webhook may not be set up correctly
- **Timezone mismatch** — if Jenkins is scheduled and running on UK time zone while team monitors in India time zone, builds may appear to not trigger at expected times

### Q: How do you roll back a deployment?

- **Versioning approach:** Tag each build with a version (V1, V2, V3, V4); if V4 fails, re-trigger the Jenkins pipeline pointing to the stable V3 build. 
- **Kubernetes:** Handles rollback automatically.
- Simple answer: change the version reference in the pipeline and re-run.

### Q: Linux server is running out of disk space — what do you do?

- First step: Check disk usage with `df -h` (disk free) and `du -sh` (disk usage by directory). 
- Check swap partition utilization
- If application partition is full, inspect logs under `/var/log`
- Options: clean up unnecessary logs/files, add/extend a partition, increase storage size 

### Q: What AWS service best fits building a web application (e.g., a job portal with messaging, resume upload, certification storage)?

- **Front-end:** EC2
- **Middleware/business logic:** EC2
- **Database:** RDS
- **File/resume storage:** S3
- **Networking:** VPC 

### Q: PaaS vs. Serverless — what is the difference?

- **PaaS:** Underlying infrastructure (compute, OS, runtime) is provided and visible; you deploy Docker/Kubernetes or other services on top; CPU/memory allocation is known and managed. 
- **Serverless:** No underlying infrastructure management; service is ready to use; bring only code or data; scales automatically on event trigger; billed per execution. 
- **Practical rule:** Continuous/regular workloads → PaaS (RDS for databases); infrequent/event-based → Serverless (Lambda). 

### Q: How can a non-AWS user pitch AWS experience in an interview?

- Instructor guidance: Even without direct AWS tool usage, understanding the concepts and being able to map them to your project context is valid; Linux and system admin knowledge is universally applicable. 

---

## Job Market & Career Guidance

- **Job openings:** Approximately **20,000–25,000 DevOps/Cloud openings** available on job platforms at any given time; Naukri is one source but many other platforms have more listings. 
- **Platform resource:** Instructor's website (`clouddevops.website`) aggregates **35+ job platforms** with pre-configured search filters for DevOps roles; includes remote job boards ([Remote.co](https://remote.co), WeWork Remote, OKFlex, Himalayas, Tuning, Ark) and startup/tech-company-specific boards. 
- **Bookmark recommended:** The aggregated job links page is private (not on public website homepage) but shared directly via chat; participants advised to bookmark it. 
- **Filters available:** Can be adjusted for QA, cloud, DevOps, remote, startup, or premium platforms based on individual preference. 
- **Resume advice:**
    - Participants instructed to revise and update resumes progressively — add 5 lines every Friday/Saturday as new topics are completed. 
    - Introductions in interviews should not exceed 90 seconds and must focus on current role/company, not college. 
    - Developer learning DevOps is a strong profile — can handle both development and operations activities, justifying a higher salary ask. 
- **Interview coaching note:** Participant Ravi attended a first interview; instructor advised to observe how top candidates introduce themselves and model that approach; confidence is built through repetition. 
- **On-site/abroad jobs:** No commitment made, but instructor indicated international job portal links will be added to the platform soon. 

---

## LinkedIn & Community Engagement

- Participants instructed to start posting on LinkedIn after completing each module (Linux complete → post; AWS complete → post; etc.). 
- Posts should be written personally (with AI assistance for drafting, but customized in own words — not fully AI-generated). 
- Tag instructor and community in posts; instructor will review posts in upcoming sessions. 
- **AI Companion** for LinkedIn post generation is currently unavailable due to high cost; instructor will generate and provide posts separately. 

---

## Program Roadmap & Next Steps

- **Current position:** Days 1–4 revision complete; all marked green on roadmap. 
- **Upcoming curriculum sequence:** 
    - Next full week: **Linux** (complete revision)
    - Following two weeks: **AWS** (complete coverage)
    - After AWS: **DevOps** tools and practices
    - After DevOps: **Docker**
    - Throughout: **Shell Scripting**
    - Later modules (7–8): **Platform Engineering / SRE**
    - Module 9+: **Senior Architect** level content
    - Advanced (75 days out): **AI Agents on Kubernetes, MCP Server**
- **AWS account setup:** Participants instructed to create AWS accounts; watch the previously shared setup video; create budget alarms immediately to avoid unexpected charges; free tier available for 30 days. 
- **Budget alarm setup:** Navigate to billing → budget alarms → set threshold (e.g., ₹10–₹20); prevents accidental spend. 
- **Virtualization:** All participants must enable virtualization in BIOS on their laptops; if disabled, search YouTube/ChatGPT with laptop model name for specific BIOS steps. 
- **Krunal's project presentation:** Scheduled for a future Friday session — last 15 minutes allocated for Krunal to explain his AI PDF-to-audio product built with 11 Labs. 
- **Questions submission:** Participants to fill the question form (same LMS link); can be filled anytime including late at night; technical questions only — not HR/rangoli-related queries. 

---

## Action Items

- **All participants:** Enable virtualization on personal laptops before next session. 
- **All participants:** Create AWS account and watch the account setup video shared previously; set up budget alarms immediately after account creation. 
- **All participants:** Begin updating resumes progressively — add 5 lines per week as modules are completed. 
- **All participants:** Start posting on LinkedIn after each module completion; tag instructor and community; write posts personally (AI-assisted but self-customized). 
- **Krunal:** Prepare a 15-minute project walkthrough of the AI PDF/audio platform for a future Friday session. 
- **All participants:** Bookmark the job aggregator page shared in chat (clouddevops.website job portal section). 
- **All participants:** Fill the question form via LMS link for technical questions to be addressed in upcoming sessions.

# Batch-45 Day-5 Revision
## 20 Basic Interview Q&A + 20 Scenario-Based Q&A

Based on the Day-5 revision topics: AWS fundamentals, cloud computing models, DevOps principles, CI/CD, AWS services, and basic troubleshooting.

---

# Part 1: 20 Basic Interview Questions & Answers

## 1. What is AWS?
**Answer:** AWS is Amazon's cloud platform that provides on-demand services like compute, storage, networking, databases, and serverless services.

## 2. What are the main benefits of cloud computing?
**Answer:** On-demand resources, scalability, pay-as-you-go pricing, reduced infrastructure management, and faster provisioning.

## 3. What is IaaS?
**Answer:** Infrastructure as a Service provides infrastructure such as servers, networking, storage, and virtualization. Example: **AWS EC2**.

## 4. What is PaaS?
**Answer:** Platform as a Service provides the underlying infrastructure and platform so developers can focus mainly on their application.

## 5. What is SaaS?
**Answer:** Software as a Service is a ready-to-use application managed by the provider. Examples include Gmail and Google Drive.

## 6. What is serverless computing?
**Answer:** Serverless allows us to run code without managing servers. AWS Lambda is a common example.

## 7. What is AWS EC2?
**Answer:** EC2 is a virtual machine service used to run applications and workloads in AWS.

## 8. What is AWS S3?
**Answer:** S3 is an object storage service used to store files, images, backups, resumes, videos, and other objects.

## 9. What is AWS RDS?
**Answer:** RDS is a managed relational database service. AWS manages many database administration tasks for us.

## 10. What is AWS VPC?
**Answer:** VPC is a logically isolated network in AWS where we deploy and secure resources such as EC2 instances.

## 11. What is AWS IAM?
**Answer:** IAM is used to manage users, roles, permissions, and access to AWS resources.

## 12. What is AWS Lambda?
**Answer:** Lambda is a serverless compute service that executes code in response to events without requiring us to manage servers.

## 13. What is DevOps?
**Answer:** DevOps is a combination of practices, culture, and automation that helps development and operations teams deliver software faster and more reliably.

## 14. What is Continuous Integration?
**Answer:** CI automatically builds and tests code whenever developers integrate changes into the source-code repository.

## 15. What is Continuous Delivery?
**Answer:** Continuous Delivery automatically prepares code for deployment, but production deployment normally requires manual approval.

## 16. What is Continuous Deployment?
**Answer:** Continuous Deployment automatically deploys validated code to production without a manual approval step.

## 17. What is Jenkins?
**Answer:** Jenkins is an automation server commonly used to build, test, and automate CI/CD pipelines.

## 18. What is Terraform?
**Answer:** Terraform is an Infrastructure as Code tool used to provision and manage infrastructure using configuration files.

## 19. What is Docker?
**Answer:** Docker packages an application and its dependencies into a container so it can run consistently across environments.

## 20. What is Kubernetes?
**Answer:** Kubernetes is a container orchestration platform used to deploy, manage, scale, and recover containerized applications.

---

# Part 2: 20 Scenario-Based Interview Questions & Answers

## 1. Developer pushed code to GitHub, but Jenkins didn't start. What will you check?
**Answer:**
- GitHub-Jenkins authentication
- Webhook configuration
- Jenkins job trigger configuration
- Required Jenkins plugins
- Jenkins logs
- Repository permissions

## 2. Your production deployment failed. How will you roll back?
**Answer:** Use versioned builds. If V4 failed, redeploy the last stable V3 build through the pipeline. In Kubernetes, use the deployment rollback mechanism.

## 3. Linux server is running out of disk space. What will you do?
**Answer:** First check disk usage:
```bash
df -h
du -sh *
```
Then identify large files/directories, check `/var/log`, clean unnecessary data, or increase storage/partition if required.

## 4. You need to build a job portal on AWS. Which services would you use?
**Answer:**
- EC2 → application/frontend
- RDS → relational database
- S3 → resumes/files
- VPC → networking and security

## 5. You need to process an email whenever a message enters a queue. What would you use?
**Answer:** Use **SQS + Lambda**. SQS receives the message and Lambda is triggered to process it.

## 6. You have a task that runs only once every week. Would you use EC2 24/7?
**Answer:** Not necessarily. Consider **Lambda with a scheduled trigger** because an always-running EC2 instance may not be required for an occasional task.

## 7. Users upload files in different formats and you need to convert them to PDF automatically. What would you use?
**Answer:** Store the uploaded files in **S3** and trigger **Lambda** to perform the conversion when the required event occurs.

## 8. Your application needs a database that is continuously used by users. Lambda or RDS?
**Answer:** For a continuously used relational database, choose **RDS**. Lambda is better suited to event-driven or short-duration processing.

## 9. Your application CPU reaches 90% in production. What will you do?
**Answer:** Identify the reason using monitoring and system-level investigation. Depending on the root cause, scale infrastructure, optimize the application, or work with developers to reduce resource consumption.

## 10. Your Jenkins pipeline builds successfully but the application is not deployed. What will you check?
**Answer:**
- Deployment stage logs
- Target server/Kubernetes connectivity
- Credentials
- Deployment configuration
- Environment variables
- Docker image availability
- Deployment permissions

## 11. Your Docker image works locally but fails in another environment. What will you investigate?
**Answer:** Check whether the image contains all required dependencies, environment variables, configuration files, exposed ports, and correct application versions. Compare the runtime environments.

## 12. Your Kubernetes application is running but users cannot access it. What would you check?
**Answer:**
1. Pod status
2. Service
3. Service endpoints
4. Ingress configuration
5. Application logs
6. Network/security rules
7. Container port vs service port

## 13. Terraform creates the infrastructure but the application cannot connect to the database. What will you check?
**Answer:** Check VPC/network configuration, security rules, database endpoint, ports, credentials, subnet placement, and whether the application is using the correct database endpoint.

## 14. Your AWS application needs to store resumes uploaded by users. Which service would you choose?
**Answer:** Use **S3** because it is designed for object/file storage. Store the object reference in the database if required.

## 15. You need to control which users can access an S3 bucket. What would you use?
**Answer:** Use **IAM permissions and roles**, along with appropriate S3 bucket policies, to control access.

## 16. Your company wants developers to receive automatic feedback whenever they push code. How would you implement it?
**Answer:** Configure a CI pipeline where GitHub triggers Jenkins. Jenkins builds the application, runs tests, and sends the build/test result back to the team.

## 17. Your production deployment should require manager approval before deployment. Which approach would you use?
**Answer:** Implement **Continuous Delivery** with a manual approval gate before production deployment.

## 18. Management wants every successful code change deployed automatically to production. What approach is this?
**Answer:** This is **Continuous Deployment**, where deployment happens automatically after the required build and testing stages without a manual approval step.

## 19. Your AWS bill suddenly increases. What will you check first?
**Answer:** Check active resources and their usage, identify unexpected resources or workloads, review billing details, and configure a **budget alarm** to receive alerts when spending crosses the defined threshold.

## 20. Explain a complete DevOps pipeline for a real project.
**Answer:**
A typical flow is:

**Developer → Git/PR → Jenkins → Build → Testing → Docker Image → Kubernetes → Monitoring → Feedback**

Terraform can be used to provision the infrastructure required for the Kubernetes environment.

---

# Quick Interview Tip

For scenario questions, don't just name a tool. Explain:

**Problem → Investigation → Tool → Action → Expected Result**

Example:

> "First I will identify the issue from logs and monitoring. Then I will check the relevant configuration and connectivity. After finding the root cause, I will fix it and validate the application again."

This approach makes the answer more practical and interview-friendly.

