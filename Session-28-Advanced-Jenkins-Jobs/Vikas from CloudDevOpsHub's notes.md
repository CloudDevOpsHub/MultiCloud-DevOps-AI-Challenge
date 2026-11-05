## Key Outcomes

The session covered advanced Jenkins concepts including pipeline types (declarative vs. scripted), Jenkins file, master-slave architecture setup, top plugins, and day-to-day troubleshooting. A live demo walked through creating and connecting agent nodes, running jobs on specific nodes, and recovering offline nodes. Students were assigned a multi-VM master-slave setup on cloud as homework, and job referrals for DevOps roles at two companies were shared.

## Decisions Made

- **Declarative pipeline** is the preferred approach in most companies because it is human-friendly, medium-to-low complexity, and supports reuse across projects via **DSL (Domain-Specific Language)**. 
- **Jenkins file** (stored in GitHub/SCM) is the standard for managing pipeline code, as it enables version tracking, audits, and rollback — changes made directly in Jenkins UI cannot be tracked. 
- **Installation of build tools** (Maven, Gradle, Docker) is preferred over plugins for heavy operations, as plugins are less reliable for intensive builds. 
- **Master node** should never run build loads; all execution should be delegated to agent/slave nodes to prevent system crashes and slowness. 
- **Java is mandatory** on both master and slave nodes; JDK is always preferred over JRE for development and build purposes. 
- **Monday is declared a leave** for the festival; next class is scheduled for Friday morning. 

---

## Jenkins Pipeline Types

### Declarative vs. Scripted

- **Declarative pipeline**: Human-friendly, structured, medium-to-low complexity; uses **Groovy DSL**; recommended for most use cases. 
- **Scripted pipeline**: More flexible but higher complexity; behavior changes if Groovy changes. 
- Reality in companies: ~40% freestyle projects, ~40% scripted/declarative pipelines; multi-branch and shared library pipelines are used by fewer than 10% of teams. 

### Multi-Branch Pipeline

- Used when the same pipeline needs to run on **different branches** (e.g., dev branch vs. QA branch) for different teams or verticals. 
- Allows branch-specific testing and parallel branch execution without manual reconfiguration. 

### Shared Library Pipeline

- A **centralized, reusable pipeline codebase** — written once and consumed across multiple projects or teams. 
- Used in large organizations (e.g., TCS-scale) where multiple projects share common CI/CD prerequisites. 
- Variables can be parameterized so the same library serves different environments (Dev, QA, Prod). 
- Reduces duplication: avoids rewriting the same pipeline logic for every project with similar requirements. 

---

## Jenkins File

- A **text file** that stores the entire pipeline workflow — all stages, steps, and configurations — as code, committed to GitHub. 
- Primary benefit: changes are **version-controlled and auditable** via GitHub; any bad change can be rolled back to a previous version. 
- Written using **Groovy DSL**. 
- **File name must not be changed** without also updating the corresponding path in Jenkins configuration (Pipeline → Configure → Script Path); changing the name without updating Jenkins will break the pipeline. 
- Syntax errors (e.g., missing brackets) are detectable via IDE or code review before execution. 

---

## Master-Slave Architecture

### Concept

- **Master node**: The main Jenkins instance; handles UI, configuration, job scheduling, and coordination — no build load should run here. 
- **Slave/Agent/Worker/Node**: Additional machines that execute the actual build jobs; all four terms refer to the same entity. 
- Purpose: **distributed build environment** — spreads workload across multiple machines to enable parallel execution and environment isolation. 

### Benefits

- **Parallel execution**: Multiple pipelines run simultaneously on different agents instead of queuing serially on master (e.g., 10 pipelines × 30 min = 300 min serial vs. ~30 min parallel). 
- **Environment isolation**: Different teams get dedicated nodes matching their stack — Windows node for .NET teams, Linux node for backend teams, Dockerized node for microservices teams. 
- **Team isolation**: A performance team running load tests won't destabilize other teams' pipelines if they have a separate node. 
- **If master goes down**: Everything goes down — master should be kept load-free to minimize this risk. 

### Live Demo: Creating a Node

1. Go to **Manage Jenkins → Nodes → Add Node**; select "Permanent Agent." 
2. Provide a **workspace directory path** on the target machine (e.g., a `dev/devashish` folder). 
3. Assign a **label** (e.g., `performance`) and set launch method to "Launch agent by connecting to the controller." 
4. Save — node appears as **offline** until the agent JAR is run. 
5. From the node page, copy the provided command; it uses `curl` to download `agent.jar` from Jenkins, then runs it with the node's secret, URL, name, and workspace path. 
6. Run the command in the target machine's terminal → node shows **Connected** and comes online in Jenkins. 

### Assigning Jobs to Specific Nodes

- **Freestyle jobs**: Configure → General → check **"Restrict where this project can run"** → enter the node label. 
- **Pipeline jobs**: Use `agent { label 'node-name' }` in the pipeline script instead of `agent any`. 
- Space in label names causes parsing errors — use hyphens instead of spaces in label names. 

### Node Recovery (Troubleshooting Offline Node)

- When a node goes offline (VM restart, load, crash), jobs assigned to it enter a **waiting/pending state** — they do not fail immediately. 
- Resolution: SSH into the VM, navigate to the workspace folder, re-run the `agent.jar` command → node reconnects → pending jobs resume and succeed. 
- Troubleshooting checklist: CPU, memory, disk, Linux-level diagnostics on the agent VM. 

### Assignment (Homework)

- Create **3 VMs on cloud** (same cloud — AWS or GCP):
    - VM 1 (Ubuntu): Install Java + Jenkins → this becomes the **master**. 
    - VM 2 (CentOS): Install Java only → add as a **Linux slave node**. 
    - VM 3 (Windows): Install Java only → add as a **Windows slave node**. 
- Connect both slave nodes to master using the agent JAR command from Jenkins. 
- Note: Jenkins installation is **only required on master**; slave nodes need Java only. 

---

## Jenkins File System & Backup

### Windows Location

- Jenkins is **installed** under `C:\Program Files\Jenkins` but **operates from** `C:\ProgramData\Jenkins`. 
- All job configurations, logs, plugins, and agent data are stored under `ProgramData\Jenkins`. 

### Linux Location

- Default path: `/var/lib/jenkins`. 

### Key Subdirectories

- **Jobs folder**: Contains individual job configurations (XML files for each pipeline/freestyle job). 
- **Logs**: All Jenkins activity and event logs. 
- **Nodes/Agents**: Node configuration data. 
- **Config files**: Written in **XML** (not HTML — the browser renders XML as HTML, but the underlying format is XML). 
- **Plugins folder**: All installed plugin files; plugins can be manually updated by replacing files here. 

---

## Top Jenkins Plugins

|            Plugin            |                                     Purpose                                     |
|------------------------------|---------------------------------------------------------------------------------|
| **Git**                      | GitHub/GitLab integration — mandatory for SCM connectivity.                     |
| **GitHub Branch Source**     | Connect and verify specific branches.                                           |
| **Pipeline**                 | Enables Groovy-based pipeline execution.                                        |
| **Pipeline Stage View**      | Visual stage-by-stage build timeline with timing data.                          |
| **Credentials**              | Securely store passwords, tokens, SSH keys, username/password pairs.            |
| **Credential Binding**       | Inject stored credentials securely into pipeline steps.                         |
| **SSH Agent**                | Enables SSH-based connectivity between master and slave nodes.                  |
| **Maven Integration**        | Integrates Maven builds (installation preferred over plugin for heavy builds).  |
| **SonarQube Scanner**        | Static code analysis without compiling/running the code.                        |
| **Docker / Docker Pipeline** | Build Docker images, run containerized pipelines, push to ECR.                  |

- **Plugin vs. Installation decision**: For heavy tools (Maven, Gradle, Docker), direct **installation on the VM is preferred** over the plugin for reliability and performance. 
- Outdated plugins trigger **red vulnerability warnings** in Jenkins; regular maintenance activities should include updating all plugins. 
- Plugins can be **downgraded** if a new version is incompatible — download the previous version's file and replace it in the plugins folder. 

---

## Day-to-Day Jenkins Problems & Solutions

### Common Problems

- **Pipeline failures** due to wrong/incomplete configuration or developer changing their approach/codebase. 
- **All jobs running on master** causing slowness and potential system crash — solution is mandatory master-slave setup. 
- **Slave node going offline** due to VM restart, high load, or memory issues — jobs queue in waiting state until node recovers. 
- **Plugin not supported / outdated** — plugin version incompatible with Jenkins or application changes; requires update or downgrade. 
- **Long job queues** when jobs run serially on a single node — requires master-slave parallel execution. 
- **Log files consuming full disk space** on build nodes — common in high-frequency build environments. 
- **User access/authentication issues** — credentials expiring (e.g., 30/90-day password policies), restricted build access after security patching. 

### Troubleshooting Thumb Rules

1. **Check console output / job logs first** for every failure. 
2. Go to **Manage Jenkins page** to identify dependency and configuration issues (Java version, plugin conflicts, security warnings). 
3. **Always keep Jenkins and plugins updated** to resolve vulnerability issues; treat updates as a scheduled maintenance activity. 
4. For credential management: store secrets in **Jenkins Credentials** (not hardcoded); reference by credential ID in pipelines — never expose raw passwords. 
5. For performance optimization: use **Pipeline Stage View** to identify which stage consumes the most time, then optimize or parallelize that stage. 

---

## Credentials Management (Interview-Ready Answer)

- All secrets (passwords, tokens, SSH keys, AWS credentials) are stored in **Jenkins → Manage Jenkins → Credentials**. 
- Supported types: username/password, secret text, SSH private key, certificates. 
- In pipelines, credentials are referenced by their **credential ID** — the actual secret value is never exposed in the pipeline code. 
- **Credential Binding plugin** enables secure injection of credentials as environment variables during build execution. 

---

## Cloud Billing Warnings (Student Issues Addressed)

- **GCP unexpected charges**: Caused by data transfer out via public IPs on running VMs; deleting VMs and projects may not immediately stop charges if billing is still active. 
- **Recommended actions**:
    - Raise a GCP support ticket via chatbot; state "I am a student" and request bill reversal. 
    - Disable the billing account or remove the linked payment method immediately. 
    - Restrict VM firewall rules to **your own public IP only** (not `0.0.0.0/0`) to prevent unauthorized access and unexpected data transfer charges. 
- **Best practice**: Always **create and delete** cloud resources within the same session during practice; never leave VMs running unattended. 
- For students with exhausted free credits: create a new GCP account with a different email and payment method, or run Jenkins locally for practice. 

---

## Job Referrals Shared

### Company 1 (via Praveen Kumar — Batches 41 & 45)

- **Role**: DevOps Engineer / Cloud Engineer reporting to VP. 
- **Skills required**: DevOps activities, security, Linux admin troubleshooting, Docker, Kubernetes, Ansible, Terraform. 
- **Not required**: Claude/MCP server knowledge. 
- **Apply**: Send resume directly; referral link shared in classroom announcements. 

### Company 2 (LNT / Direct Referral Links)

- **Experience bands**: 0–3 years, 5–7 years (candidates with 8+ years can still apply). 
- **Multiple positions** available; direct referral token links provided — apply to all relevant roles. 
- HR referral and employee referral links both shared. 

---

## Action Items

- **All students**: Complete the 3-VM master-slave cloud assignment (Ubuntu master + CentOS slave + Windows slave); submit screenshots. 
- **All students**: Review and memorize the **top 10 Jenkins plugins** covered; be able to explain use cases in interviews. 
- **All students**: Take screenshots of the problems/troubleshooting slide for interview preparation. 
- **All students**: Write questions in the provided form for Q&A follow-up. 
- **Students with GCP billing issues (Ameen, Gopal, Mamta)**: Disable billing/remove payment method immediately; raise GCP support ticket requesting student bill reversal. 
- **Ravi G**: Redo the node connection exercise using **cloud-to-cloud** (not cloud-to-local); run Jenkins locally if needed for practice. 
- **Bhagyajeet**: Verify Java version parity between master and slave nodes to resolve the "fail to connect" handshake error. 
- **Vikas**: Share assignment details and referral links formally via email/LMS. 

## Open Questions

- **Parallel execution across two nodes simultaneously**: Confirmed possible — create separate jobs assigned to separate nodes; each node runs independently with its own executor count. 
- **Converting slave to master**: Not possible in Jenkins — no built-in functionality exists; a new Jenkins installation must be set up as master. 
- **Running jobs in background (detached mode) on Windows**: Append `&` (ampersand) at the end of the agent run command. 
- **Multi-cloud node connectivity**: Nodes on different clouds (AWS + GCP + Azure) can connect to a single master via **public IP** — confirmed to work. 
