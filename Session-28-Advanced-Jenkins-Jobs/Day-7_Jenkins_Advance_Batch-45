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

# Jenkins Interview Preparation -- 20 Questions and 20 Scenario-Based Questions

**Based on:** Jenkins advanced concepts, pipelines, master-agent
architecture, plugins, credentials, troubleshooting, filesystem, and
cloud-practice notes.

------------------------------------------------------------------------

# Part 1: 20 Jenkins Interview Questions and Answers

### 1. What is the difference between Declarative and Scripted Pipeline in Jenkins?

**Answer:** - **Declarative Pipeline** uses a structured, human-friendly
syntax and is suitable for medium-to-low complexity workflows. It uses
Groovy DSL and is the preferred approach in most companies. - **Scripted
Pipeline** provides more flexibility but is more complex to write and
maintain. Its behavior can also be affected by changes in Groovy.

For most standard CI/CD workflows, Declarative Pipeline is a good
starting point.

### 2. What is a Jenkinsfile, and why should it be stored in Git?

**Answer:** A Jenkinsfile is a text file that defines the complete
pipeline workflow, including stages, steps, and configuration. It is
written using Groovy DSL.

Storing it in Git provides: - Version history of pipeline changes. -
Auditability: who changed what and when. - Rollback to a previous
working version. - Collaboration and code review.

Changes made directly in the Jenkins UI do not provide the same
source-control history.

### 3. What is the difference between a Jenkins controller (master) and an agent (slave)?

**Answer:** The controller, traditionally called the master, manages the
Jenkins UI, configuration, job scheduling, and coordination. An agent,
historically called a slave, is a machine that executes build jobs.

The recommended architecture is to keep the controller free from build
workloads and delegate execution to agents.

### 4. Why should build jobs not run on the Jenkins controller?

**Answer:** Builds consume CPU, memory, disk, and other resources. If
all jobs run on the controller, Jenkins can become slow or unstable, and
the controller may crash.

Using agents distributes the workload and helps protect the controller.
It also allows different teams to use machines suited to their build
requirements.

### 5. What are the benefits of Jenkins master-agent architecture?

**Answer:** The main benefits are: 1. **Parallel execution:** Different
agents can run pipelines at the same time. 2. **Environment isolation:**
Teams can use Windows, Linux, or Docker-based agents as needed. 3.
**Team isolation:** Heavy testing on one agent is less likely to affect
jobs running on another agent. 4. **Workload distribution:** Build
execution is moved away from the controller.

For example, a .NET team can use a Windows agent while a backend team
uses a Linux agent.

### 6. What is a Multi-Branch Pipeline?

**Answer:** A Multi-Branch Pipeline allows Jenkins to discover and run
pipelines for different branches of a source-code repository.

For example, a project may have `dev`, `qa`, and `main` branches.
Jenkins can run branch-specific builds and tests without manually
creating a separate pipeline configuration for every branch.

### 7. What is a Shared Library in Jenkins?

**Answer:** A Shared Library is a centralized, reusable codebase for
Jenkins pipeline logic. Teams write common pipeline functionality once
and reuse it across multiple projects.

It reduces duplicate pipeline code. Parameters can be used to support
different environments such as Dev, QA, and Prod.

### 8. What happens if the Jenkinsfile is renamed?

**Answer:** Jenkins uses the configured Script Path to locate the
pipeline file. If the file is renamed but the Script Path is not
updated, Jenkins may fail to find the pipeline definition.

Update the path under the pipeline job's configuration to match the new
filename and location.

### 9. Which programming language or syntax is used to write a Jenkinsfile?

**Answer:** Jenkins pipelines use **Groovy DSL**. Declarative Pipeline
provides a structured syntax, while Scripted Pipeline gives more direct
Groovy flexibility.

### 10. How do you create a permanent agent node in Jenkins?

**Answer:** A typical setup is: 1. Open **Manage Jenkins → Nodes → Add
Node**. 2. Select **Permanent Agent**. 3. Provide the agent's workspace
directory. 4. Assign a label, such as `linux-agent` or `performance`. 5.
Select the launch method **Launch agent by connecting to the
controller**. 6. Save the node. 7. Run the generated agent command on
the target machine.

The node should become connected when the agent process starts
successfully.

### 11. What is the purpose of `agent { label 'node-name' }` in a Jenkins pipeline?

**Answer:** It tells Jenkins to execute the pipeline on an agent
matching the specified label.

For example:

``` groovy
pipeline {
    agent { label 'linux-agent' }

    stages {
        stage('Build') {
            steps {
                echo 'Running on the Linux agent'
            }
        }
    }
}
```

This is useful when a job requires a particular operating system or
build environment. Avoid spaces in labels; use hyphens instead.

### 12. How do you restrict a Freestyle job to a particular agent?

**Answer:** Open the Freestyle job's **Configure** page, go to
**General**, select **Restrict where this project can run**, and enter
the required agent label.

Jenkins will schedule the job only on an eligible node with that label.

### 13. What is the role of Java on Jenkins controller and agent machines?

**Answer:** The session notes specify that Java is required on both the
controller and agent machines. The JDK is preferred over the JRE for
development and build work.

For a reliable connection, verify Java availability and compatible Java
versions on both sides.

### 14. Where is Jenkins data stored on Linux and Windows?

**Answer:** - **Linux:** The default Jenkins home directory is
`/var/lib/jenkins`. - **Windows installation directory:**
`C:\Program Files\Jenkins`. - **Windows Jenkins data directory:**
`C:\ProgramData\Jenkins`.

On Windows, job configurations, logs, plugins, and agent data are stored
under `C:\ProgramData\Jenkins`.

### 15. What information is stored in the Jenkins jobs folder and configuration files?

**Answer:** The jobs folder contains individual job configurations,
including XML files for Pipeline and Freestyle jobs. Jenkins
configuration files are stored in XML format.

The Jenkins home directory also contains logs, node or agent
configuration data, and plugin files.

### 16. Name important Jenkins plugins and explain their purpose.

**Answer:**

  -----------------------------------------------------------------------
  Plugin                              Purpose
  ----------------------------------- -----------------------------------
  Git                                 GitHub/GitLab source-control
                                      integration

  GitHub Branch Source                Branch discovery and integration

  Pipeline                            Pipeline execution

  Pipeline Stage View                 Visual stage-by-stage build view

  Credentials                         Secure storage of secrets

  Credential Binding                  Securely inject credentials into
                                      build steps

  SSH Agent                           SSH-based connectivity support

  Maven Integration                   Maven integration

  SonarQube Scanner                   Static code analysis

  Docker / Docker Pipeline            Docker image and container pipeline
                                      operations
  -----------------------------------------------------------------------

### 17. Should Maven, Gradle, and Docker be installed directly or managed only through Jenkins plugins?

**Answer:** The session recommendation is to install heavy build tools
such as Maven, Gradle, and Docker directly on the relevant VM when
intensive operations are required. This is preferred for reliability and
performance.

Plugins can provide Jenkins integration, but should not automatically
replace the actual tool installation.

### 18. How does Jenkins Credentials management work?

**Answer:** Secrets such as passwords, tokens, SSH private keys,
certificates, and cloud credentials can be stored under **Manage Jenkins
→ Credentials**.

A pipeline references a credential by its credential ID rather than
hardcoding the secret value. The Credential Binding plugin can inject
stored credentials securely into pipeline steps, commonly as environment
variables.

### 19. How do you troubleshoot a Jenkins pipeline failure?

**Answer:** Start with the console output and job logs. Then: 1. Check
the failing stage and command. 2. Review job and pipeline configuration.
3. Check Java versions and tool availability. 4. Review plugin
compatibility and security warnings in Manage Jenkins. 5. Verify
credentials and access permissions. 6. Use Pipeline Stage View to
identify slow stages.

The exact fix depends on the error shown in the logs.

### 20. What happens when an agent node goes offline, and how can it be recovered?

**Answer:** Jobs assigned to an offline agent normally remain waiting or
pending rather than failing immediately.

To recover it: 1. Check whether the VM is running. 2. Check CPU, memory,
and disk usage. 3. SSH or log in to the agent machine. 4. Navigate to
the workspace directory. 5. Run the agent JAR command again. 6. Confirm
that the node becomes connected in Jenkins.

Pending jobs can then resume when the agent is available and eligible to
execute them.

------------------------------------------------------------------------

# Part 2: 20 Scenario-Based Jenkins Interview Questions and Answers

### 1. Scenario: Your Jenkins controller is becoming slow because multiple builds are running on it. What will you do?

**Answer:** I would move build execution to separate agents and keep the
controller focused on UI, configuration, scheduling, and coordination.

I would: 1. Create suitable permanent agent nodes. 2. Install Java on
each agent. 3. Connect agents using the generated agent JAR command. 4.
Assign labels to the agents. 5. Restrict jobs or pipelines to those
labels. 6. Verify that builds execute on agents instead of the
controller.

### 2. Scenario: A pipeline was working yesterday, but today Jenkins says it cannot find the Jenkinsfile. What could be wrong?

**Answer:** The Jenkinsfile may have been renamed or moved, while the
job's Script Path still points to the old location.

I would check the repository and the pipeline job's **Configure →
Pipeline → Script Path** setting, then update it to the correct path.

### 3. Scenario: Your team has `dev`, `qa`, and `main` branches. Each branch needs its own build and tests. How would you design this?

**Answer:** I would use a Multi-Branch Pipeline. Jenkins can discover
the repository branches and run the pipeline for each branch.

The Jenkinsfile can define the relevant build and test stages, while
branch-specific behavior can be handled in the pipeline logic or
configuration.

### 4. Scenario: Five projects use the same checkout, build, and test stages. Developers are copying the same pipeline code into every repository. How would you improve this?

**Answer:** I would create a Jenkins Shared Library containing the
common pipeline logic.

Each project would call the shared functionality and pass
project-specific parameters. This reduces duplication and makes common
changes easier to maintain.

### 5. Scenario: A Linux application build must run on Linux, but a .NET application build must run on Windows. How would you handle this?

**Answer:** I would configure separate Linux and Windows agents and
assign clear labels, such as `linux-agent` and `windows-agent`.

Each pipeline would request the appropriate label using
`agent { label '...' }`, so the job runs in the required environment.

### 6. Scenario: A Jenkins agent shows Offline after you created it. What steps would you follow?

**Answer:** An agent can remain offline until its agent process connects
to the controller.

I would: 1. Verify that the target VM is running. 2. Confirm Java is
installed. 3. Open the node page in Jenkins. 4. Copy the generated agent
JAR command. 5. Run the command on the target VM. 6. Check the node
status again.

If it still fails, I would inspect the connection output and verify Java
compatibility and connectivity.

### 7. Scenario: An agent was connected, but after a VM restart it became offline and jobs are waiting. How will you recover it?

**Answer:** I would log in to the VM, check CPU, memory, disk, and basic
system health, then navigate to the agent workspace and rerun the agent
JAR command.

Once the agent reconnects, Jenkins can schedule the waiting jobs on it.

### 8. Scenario: Your pipeline must run on a particular agent, but Jenkins keeps scheduling it on another machine. What would you check?

**Answer:** I would inspect the pipeline's `agent` directive and verify
that the requested label matches the label configured on the intended
node.

I would also check for spelling mistakes and spaces in labels. The
session recommends using hyphens instead of spaces.

### 9. Scenario: A Freestyle job is running on the wrong node. How would you correct it?

**Answer:** I would open the job's Configure page, select **Restrict
where this project can run** under General, and enter the label of the
intended node.

Then I would save the configuration and run the job again.

### 10. Scenario: A developer changes the Jenkins UI pipeline configuration and the new version breaks the build. How can the team avoid this problem in the future?

**Answer:** I would manage the pipeline as code in a Jenkinsfile stored
in Git.

This gives the team version history, reviews, auditability, and the
ability to roll back a bad change to a previous working version.

### 11. Scenario: A Jenkins pipeline needs an AWS access key. A developer suggests writing the key directly inside the Jenkinsfile. What would you recommend?

**Answer:** I would not hardcode the key. I would store it in **Manage
Jenkins → Credentials** and reference it by credential ID.

Credential Binding can inject the secret into the build environment when
needed. The pipeline should not print or expose the secret value.

### 12. Scenario: A Jenkins plugin update causes compatibility problems and jobs stop working. What would you do?

**Answer:** I would identify the affected plugin and review Jenkins
warnings and job logs.

If a compatible update is available, I would update it. If the new
version is incompatible, the session notes describe downgrading by
obtaining the previous plugin version and replacing the plugin file in
the plugins folder. I would validate the change before relying on it in
production.

### 13. Scenario: Jenkins shows red vulnerability warnings for outdated plugins. How should an administrator handle this?

**Answer:** I would treat plugin maintenance as a scheduled activity: 1.
Review the outdated or vulnerable plugins. 2. Check compatibility with
the Jenkins version and other dependencies. 3. Update the plugins in a
controlled maintenance window. 4. Validate important jobs after the
updates.

### 14. Scenario: A build takes 30 minutes, and you do not know which stage is consuming most of the time. How will you investigate?

**Answer:** I would use Pipeline Stage View to inspect stage timings.
This helps identify the slow stage.

After identifying it, I would investigate its commands and dependencies,
then optimize or parallelize that stage where appropriate.

### 15. Scenario: Ten pipelines are waiting in a queue because one agent is executing jobs serially. What architecture change could help?

**Answer:** I would distribute the jobs across multiple agents and
configure appropriate executor capacity.

Separate jobs can be assigned to separate nodes and run independently,
subject to each node's executor count and available resources.

### 16. Scenario: A Jenkins agent is running out of disk space because build logs are growing continuously. What would you investigate?

**Answer:** I would inspect disk usage on the build node and identify
which log files or build-related data are consuming the space.

The session identifies log files filling agent disks as a common issue.
I would establish regular disk monitoring and a cleanup or retention
approach appropriate to the environment, then verify that the agent has
enough free space to run builds.

### 17. Scenario: A Jenkins job suddenly cannot access a repository or deployment target after a security change. What would you check?

**Answer:** I would check the console output, credentials, user access,
and authentication configuration.

Credentials may have expired under an organization's password policy, or
access may have changed after security patching. I would verify the
relevant credential and permissions without exposing secret values.

### 18. Scenario: You need to create a Jenkins lab with one Ubuntu controller, one CentOS agent, and one Windows agent. What is the setup plan?

**Answer:** I would create three VMs: 1. **Ubuntu VM:** Install Java and
Jenkins; use it as the controller. 2. **CentOS VM:** Install Java only;
use it as a Linux agent. 3. **Windows VM:** Install Java only; use it as
a Windows agent.

Then I would add both agents in Jenkins and run their generated agent
JAR commands on the respective machines. Jenkins installation is
required only on the controller for this lab.

### 19. Scenario: Your Jenkins controller is on AWS, but an agent is on GCP. Can they work together?

**Answer:** Yes. The session notes confirm that nodes on different
clouds, such as AWS and GCP or Azure, can connect to one Jenkins
controller through public IP connectivity.

I would verify network reachability and the agent connection details,
then run the generated agent command on the remote machine. Network and
firewall rules should be restricted to the required sources and ports.

### 20. Scenario: A student is receiving unexpected GCP charges while practicing Jenkins on cloud VMs. What immediate precautions would you recommend?

**Answer:** I would recommend: 1. Stop and delete unused VMs and other
practice resources. 2. Check whether billing is still active; deleting
VMs or projects may not immediately stop charges. 3. Contact GCP support
through the chatbot and request a student bill review or reversal where
applicable. 4. Disable the billing account or remove the linked payment
method if appropriate. 5. Restrict VM firewall access to the student's
own public IP instead of allowing `0.0.0.0/0`. 6. Create and delete
practice resources within the same session, rather than leaving VMs
running unattended.

------------------------------------------------------------------------

## Quick Revision: Important Jenkins Commands and Syntax

### Pipeline agent label

``` groovy
pipeline {
    agent { label 'linux-agent' }

    stages {
        stage('Build') {
            steps {
                echo 'Build running on Linux agent'
            }
        }
    }
}
```

### Common Jenkins locations

  Operating System       Jenkins location
  ---------------------- ----------------------------
  Linux                  `/var/lib/jenkins`
  Windows installation   `C:\Program Files\Jenkins`
  Windows Jenkins data   `C:\ProgramData\Jenkins`

### Important Jenkins navigation paths

  -----------------------------------------------------------------------
  Task                                Jenkins navigation
  ----------------------------------- -----------------------------------
  Add an agent                        Manage Jenkins → Nodes → Add Node

  Store credentials                   Manage Jenkins → Credentials

  Restrict a Freestyle job to an      Job → Configure → General
  agent                               

  Configure Jenkinsfile path          Job → Configure → Pipeline → Script
                                      Path
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Interview Preparation Tips

1.  Be ready to explain the difference between Declarative and Scripted
    Pipeline with a small Jenkinsfile example.
2.  Practice creating one controller and connecting both Linux and
    Windows agents.
3.  Demonstrate how to assign a pipeline to a specific agent label.
4.  Explain why pipeline code should be stored in Git rather than
    maintained only through the Jenkins UI.
5.  Practice troubleshooting an offline agent and explain why jobs may
    remain pending.
6.  Memorize the purpose of the ten plugins covered in the session.
7.  Explain how Jenkins Credentials and Credential Binding help protect
    secrets.
8.  Use real examples when explaining parallel execution, environment
    isolation, and shared libraries.

*Note: These questions and answers are prepared from the supplied
session notes. Where the notes do not specify an exact command, port,
configuration value, or recovery procedure, this document avoids
inventing one.*



## Open Questions

- **Parallel execution across two nodes simultaneously**: Confirmed possible — create separate jobs assigned to separate nodes; each node runs independently with its own executor count. 
- **Converting slave to master**: Not possible in Jenkins — no built-in functionality exists; a new Jenkins installation must be set up as master. 
- **Running jobs in background (detached mode) on Windows**: Append `&` (ampersand) at the end of the agent run command. 
- **Multi-cloud node connectivity**: Nodes on different clouds (AWS + GCP + Azure) can connect to a single master via **public IP** — confirmed to work. 
