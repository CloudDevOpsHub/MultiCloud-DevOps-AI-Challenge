# 🚀 Day-8 of DevOps i.e Day-6 of Jenkins Pipeline Batch-44 : Jenkins Pipeline with SonarQube Quality Gate & Deployment

[![Module: Jenkins CI/CD](https://img.shields.io/badge/Module-Jenkins_CI%2FCD-4C9BD4?style=for-the-badge&logo=sonarqube)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 29th July 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-29th%20July%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


## 📋 Key Outcomes

This session covered Jenkins from a backend/administrative perspective, moving beyond the UI to explore file system structure, configuration management, plugin lifecycle, and real-world troubleshooting. Participants discussed backup strategies using GitHub, common day-to-day Jenkins issues encountered in production environments, branching strategies for multi-environment deployments, and deployment approaches such as blue-green. The session concluded with a preview of upcoming topics including SonarQube, JFrog/Nexus, and Docker integration.

### 💡 Key Decisions Made

- Jenkins backend location confirmed as the authoritative source for all configuration, job, plugin, user, and workspace data — not to be manually edited under normal circumstances.
- GitHub private repository chosen as the best practice for storing Jenkins configuration backups, with collaborator access restricted to relevant team members only.
- Git commands (git init, git add, git commit, git remote, git push) identified as the standard manual backup workflow for Jenkins config files.
- Backup plugin acknowledged as the preferred automated alternative to manual Git-based backup, supporting migration and restore scenarios.
- XML confirmed as the language in which all Jenkins backend configurations are written.
- Console output / logs established as the first troubleshooting step ("thumb rule") for any failing Jenkins job.
- Friday, 31st July, 8 AM scheduled for a Reverse KT session where students present topics to the group.
- One-day break agreed for the next session day (30th) before resuming with SonarQube.

- Jenkins Backend Architecture
- File System Locations
- Windows: C:\ProgramData\Jenkins\.jenkins or C:\Program Files\Jenkins depending on installation method.
- Linux: /var/lib/jenkins (standard install) or ~/.jenkins (if run via WAR file).
- Slave/Agent nodes: Located in whatever directory was configured during node setup.
- Mac is not used in real-time production environments; Jenkins runs on Windows or Linux servers.
- Key Folders and Their Purpose
- config.xml (root): Master Jenkins configuration file; written by Jenkins from the UI — do not edit manually. Contains all Jenkins-level settings in XML format.
- jobs/: Contains one sub-folder per job; each has its own config.xml reflecting that job's pipeline/configuration. Deleting a job's folder removes it from Jenkins entirely.
- users/: Stores all user accounts and their individual configuration files.
- logs/: Maintains Jenkins application logs for all tasks and activities.
- nodes/: Contains agent/slave node configuration data; corresponds to nodes created under Manage Jenkins.
- plugins/: Stores all installed plugin files (.hpi/.jpi); currently 184 plugins installed in the demo environment, rising to 198+ after Docker plugin installation due to dependencies.
- secrets/: Stores passwords, tokens, and secret keys including the initial admin password.
- updates/: Holds Jenkins update metadata.
- userContent/: User-specific content storage.
- workspace/: Where Jenkins executes all job activity; workspace path visible in job configuration matches this folder.
- Config File Hierarchy
- Every entity (Jenkins master, each job, each user) has its own separate config.xml — not a single shared file.
- Names are always config.xml; Jenkins manages naming/location automatically.

- Plugin Management
- Installation and Dependencies
- Installing one plugin can trigger 10–20 additional dependency plugins to install automatically — this has caused client confusion in real-world scenarios.
- Unpublished/beta plugins can be manually placed in the plugins/ folder as a workaround; Jenkins will load them on restart — equivalent to sideloading an APK.
- Advanced settings UI also supports manual plugin upload via .hpi file.
- Lifecycle Management
- Deprecated plugins (e.g., Groovy Library Plugin shown as deprecated) should be uninstalled to avoid vulnerabilities.
- Plugins can be enabled, disabled, or removed via the UI without full uninstall.
- Disabling a plugin may cause dependent jobs (e.g., Gradle jobs) to fail — always track what you remove and why.
- Updates available are shown with age (e.g., "8 days old"); in dev environments, direct update is acceptable; for client-managed systems, approval and CAB/CAP process required.
- Plugin health indicator shows percentage; 100% healthy is good, but low health flags potential failure areas — not a blocking concern in most cases.

- Backup Strategy
- Manual Git-Based Backup
### 1.     Navigate to Jenkins home directory.

### 2.     git init to initialize repository.

3.     git add config.xml (specific file) or git add . (all files in current directory) or git add /* (including sub-folders).
### 4.     git commit -m "message" — use --amend for updating.

5.     git remote add origin <repo-url> then git push.
- Private GitHub repo used to secure backup; collaborators added via Settings → Collaborators.
- Only config.xml is strictly necessary for config backup; full folder backup covers everything.
Plugin-Based Backup
- Backup plugin automates this process; supports restore functionality for migration between Jenkins instances.

Common Day-to-Day Issues and Troubleshooting
Thumb Rule
- Always check console output logs first — the log will reveal the root cause in most cases.
- Follow up with Manage Jenkins → check for deprecated/vulnerable plugins.
Frequent Issues Reported (Real-World)
- Incomplete or wrong configuration — most common cause of job failures; often submitted by QA or developers without complete information.
- All jobs running on master node — causes slowness; jobs should be distributed to agents.
- Slave/agent offline — users report "job not running" without knowing the agent is down; DevOps must check node status.
- Plugin not supported or slow — may require upgrading or downgrading the plugin.
- Developer changed tooling without informing DevOps — e.g., switching from Maven to Gradle, or upgrading JDK version; pipeline fails silently.
- Saurabh's company established a process: developers must inform DevOps before making any pipeline-related changes, otherwise DevOps is not responsible for failures.
- QA/non-DevOps users modifying job configuration — changing Java version, Maven version, Node version without notice causes unexpected failures.
- Long job queues — caused by insufficient CPU/RAM on nodes; solution is to increase resources, optimize jobs into smaller parts, or distribute across agents.
- Jenkins responding slowly — caused by excessive logs filling memory, or insufficient RAM (e.g., only 1 GB allocated).
- Jenkins file overwritten on branch merge — developer/TL merges branches and accidentally overwrites Jenkinsfile; requires recheck and rewrite.
CAB/CAP Process (Change Management)
- In ITIL-following organizations, all Jenkins changes require a CAP (Change Advisory Board) call — typically held Wednesday/Thursday.
- CAP call covers: what is changing, which teams are impacted, rollback plan if the change fails.
- Supporting teams are added to the call so they are aware and available if collateral failures occur.

Multi-Environment Deployment and Branching Strategies
Common Branching Models
- Main/Master branch: Production-ready code.
- Develop branch: Active development; CI/CD can run here.
- Feature branches: New features isolated from main development flow.
- Hotfix branch: Emergency production fixes applied directly, then merged back.
Deploying to Multiple Environments
- Different branches per environment (dev, stage, prod) with separate pipelines per branch — most common approach.
- Tags can be used to deploy specific versions to specific environments.
- Parameterized pipelines (single job, environment passed as parameter) — used by Akshay's team; environment name determines target machine and variable set.
- One repo, one pipeline, different parameters for dev/QA/prod/UAT.
- Multiple jobs (job1, job2, job3) per environment — straightforward but verbose.
- Promoting code from dev → stage is done via Pull Requests (PRs) — code does not change, only the target branch differs.
- Kubernetes project on GitHub used as real-world example: 1.6, 1.35, 1.36 release branches visible; teams deploy by selecting the relevant release branch or tag.
Code Owners
- CODEOWNERS file in repository defines who must approve PRs for specific files/directories.
- Approvers receive review requests automatically when relevant code changes are submitted.

Jenkins Agents / Nodes
Homework Review
- Rohit Pawar: Created master VM in GCP with Jenkins + JDK; created CentOS slave in GCP (connected); attempted Windows VM (free trial issue), used local machine as Windows agent instead.
- Aditya: Created Ubuntu and CentOS machines in GCP, both connected as slaves; shared screenshot in group.
- Others encouraged to complete the same exercise — "no excuse, no choice."
Executor Configuration
- Executors = runners; determine how many parallel jobs a node can handle.
- Minimum/maximum executor count depends on application and project requirements — no universal standard; default of 1 is sufficient for basic use.
- If a job is queued for extended periods (e.g., 1 hour), the agent may be overloaded or shared — solution is to add more agents or increase resources.

Upcoming Topics / Roadmap
- SonarQube: Plugin install → server creation → scanner configuration → integration into pipeline for code quality checks.
- JFrog Artifactory (noted as outdated/chargeable; still used in many companies) → artifact storage.
- Docker: Most common; artifact deployment pipeline to be covered as part of the full project flow.
- Full project session: Expected to run ~1 hour 50 minutes covering the complete CI/CD flow end-to-end in a single session.
- Git/GitHub Advanced Session: Requested by Aditya; Vikas confirmed it will be covered separately in Batch 44.

Freelancing / Job Opportunities
- Kubernetes freelancing role: 40–50 hours of extra work; requires Kubernetes knowledge; interview with Vikas's team at 8 PM same day; direct referral if passed.
- i.techtsystem / Parakh company opening: 2–8 years experience; direct referral available through a known interviewer.
- Barclays opening (Pune): Shared by Abhinav in WhatsApp group; 5+ years DevOps experience preferred (4–5 years may also qualify).
- All opportunities: zero-degree/zero-formal-experience barrier for Kubernetes role; apply and let the interview determine eligibility.

Reverse KT Session — Friday, 31st July, 8 AM
- Each participant presents for 5 minutes on a topic they found interesting or learned.
- Topics can include: Linux, GCP, Git/GitHub, Jenkins, Docker — anything covered in the batch.
- Participants must comment their topic in the WhatsApp group thread that will be opened.
- Goal: Teach back to the group, not just revise — "Reverse KT, not revision."
- Avoid duplicate topics (e.g., multiple people explaining ls command).

Open Questions
- Git merge vs. Git rebase difference: Rajvaibhav attempted an explanation but audio was unclear; cherry-pick was clarified (picking a specific commit from one branch to apply to another) but merge vs. rebase distinction was not fully resolved in session.
- Groovy DSL depth: Dewashish asked whether more Groovy content is coming — Vikas confirmed it was already covered; pipeline syntax (agent, stages, steps) is the core Groovy knowledge needed; writing a Groovy pipeline for a Java project is the key interview deliverable.
- GitHub Copilot in Jenkins: Sumit asked about integrating Copilot for pipeline code generation — Vikas noted VS Code + Copilot can assist with pipeline file editing (Ctrl+Shift+I), but backend Jenkins files should not be directly modified.
- Stale/hung pipelines: CPU/memory check is the primary diagnosis; disk space exhaustion on the slave node is another common cause (Saurabh confirmed this from experience).

### 📋 Action Items

- All students: Complete the master + slave node setup in GCP (Ubuntu + CentOS connected) if not already done — no deadline given but flagged as mandatory.
- All students: Read the interview questionnaire shared in LMS (Jenkins section); be ready for Jenkins pipeline syntax questions including writing/sharing screen in mock interviews.
- Interested candidates: Give Kubernetes interview to Vikas's team by 8 PM today.
- Interested candidates (Barclays): Send email ID to Abhinav for referral.
- All students: Post your Reverse KT topic in the WhatsApp group thread before Friday, 31st July.
- Yogita / Ajay: Practice creating a Spring Boot project repo in GitHub with a Jenkinsfile; push code and raise a PR.

Below are 20 Jenkins Interview Questions & Answers and 20 Real-Time Scenario-Based Questions & Answers based on your uploaded session notes.

- Jenkins Interview Questions & Answers
### ❓ Q1: Where is Jenkins data stored?

**💡 Answer:**

Jenkins stores all its data in the Jenkins Home directory.
Linux
/var/lib/jenkins
Windows
C:\ProgramData\Jenkins\.jenkins
This directory contains jobs, plugins, users, workspace, logs, secrets, and configuration files.

### ❓ Q2: What is config.xml?

**💡 Answer:**

config.xml is the primary configuration file of Jenkins.
It stores:
Global settings
Security configuration
System configuration
Jenkins automatically manages this file, so manual editing is generally not recommended.

3. What is stored inside the jobs folder?
**💡 Answer:**

Each Jenkins job has its own folder.
Inside every job folder:
config.xml
Build history
Console logs
Job metadata
Deleting the folder permanently deletes the job.

4. Where are Jenkins plugins stored?
**💡 Answer:**

Plugins are stored inside:
plugins/
Every plugin is stored as a .jpi or .hpi file.

5. What is Workspace in Jenkins?
**💡 Answer:**

Workspace is the directory where Jenkins performs:
Git checkout
Build
Testing
Packaging
Deployment
Every job gets its own workspace.

6. Where are Jenkins secrets stored?
**💡 Answer:**

Passwords, API keys, tokens and credentials are stored inside:
secrets/
Never modify these files manually.

7. How do you back up Jenkins?
**💡 Answer:**

Two common methods:
Git backup
Backup Plugin
Git commands:
```bash
git init
git add .
git commit -m "Backup"
git remote add origin URL
git push
```

Automated backups are commonly done with the Backup Plugin.

8. Where should Jenkins backups be stored?
**💡 Answer:**

A private GitHub repository is a common best practice so only authorized collaborators can access the backup.

9. What is the first step when a Jenkins job fails?
**💡 Answer:**

Always check:
Console Output
It usually shows the root cause of the failure.

10. What causes Jenkins to become slow?
**💡 Answer:**

Common reasons:
Low RAM
Too many logs
Heavy jobs on master
Too many builds
Plugin issues

11. What are Jenkins Agents?
**💡 Answer:**

Agents execute build jobs.
Master schedules the jobs.
Agents perform the actual work.

12. What is an Executor?
**💡 Answer:**

Executor determines how many jobs a node can execute simultaneously.
More executors = more parallel builds.

13. Why should builds not run on the Master?
**💡 Answer:**

Running all builds on the master:
Slows Jenkins
Increases CPU usage
Makes the UI unresponsive
Production environments usually use agents for builds.

14. What happens if a plugin is disabled?
**💡 Answer:**

Dependent jobs may fail.
For example, disabling the Gradle plugin can break Gradle builds.

15. How do you deploy to Dev, QA and Production?
**💡 Answer:**

Common methods:
Separate branches
Separate jobs
Parameterized pipeline
Git tags

16. What is a parameterized pipeline?
**💡 Answer:**

One Jenkins job deploys to multiple environments.
Example parameter:
Environment=DEV
Environment=QA
Environment=PROD
Pipeline changes behavior based on the selected environment.

17. What is CODEOWNERS?
**💡 Answer:**

CODEOWNERS defines who must approve changes to specific files or folders before merging.

18. What is Blue-Green Deployment?
**💡 Answer:**

Blue-Green deployment maintains two production environments.
Blue = current version
Green = new version
Traffic switches to Green after successful validation, enabling quick rollback if needed. (Mentioned as one deployment approach in the session overview.)

19. What language is Jenkins configuration written in?
**💡 Answer:**

XML. All backend configuration files are stored as XML.

20. Which upcoming tools were planned for Jenkins integration?
**💡 Answer:**

SonarQube
JFrog Artifactory
Docker
These would be integrated into the CI/CD pipeline.

20 Real-Time Scenario Questions & Answers
### 📌 Scenario 1

> **❓ Question:**

A build suddenly fails. What will you check first?
**💡 Answer:**

First check:
Console Output
Then identify whether the issue is related to code, configuration, plugins, or infrastructure.

### 📌 Scenario 2

> **❓ Question:**

All Jenkins jobs are very slow.
**💡 Answer:**

Check:
CPU usage
RAM
Disk space
Running builds
Build queue
Number of jobs on master

### 📌 Scenario 3

> **❓ Question:**

Jobs remain in the queue and never start.
**💡 Answer:**

Possible causes:
Agent offline
No available executors
Insufficient CPU/RAM
Verify node status and executor availability.

### 📌 Scenario 4

> **❓ Question:**

A developer changes Maven to Gradle without informing DevOps.
**💡 Answer:**

The pipeline may fail because it still expects Maven.
Update the pipeline to use the correct build tool after coordinating with the development team.

### 📌 Scenario 5

> **❓ Question:**

Someone accidentally deletes a Jenkins job.
**💡 Answer:**

Restore it from:
Git backup
Backup Plugin
Jenkins backup directory

### 📌 Scenario 6

> **❓ Question:**

The workspace is full.
**💡 Answer:**

Clean the workspace, remove old builds, archive required artifacts, and configure workspace cleanup. (The workspace is where builds execute.)

### 📌 Scenario 7

> **❓ Question:**

A plugin update causes builds to fail.
**💡 Answer:**

Rollback or disable the problematic update after checking plugin compatibility and dependencies.

### 📌 Scenario 8

> **❓ Question:**

Your Jenkins server crashes.
**💡 Answer:**

Restore from the latest backup stored in Git or created by the Backup Plugin.

### 📌 Scenario 9

> **❓ Question:**

A user reports "Job is not running."
**💡 Answer:**

Verify whether the assigned agent is offline before troubleshooting the job itself.

### 📌 Scenario 10

> **❓ Question:**

There are 200 plugins installed. Is that okay?
**💡 Answer:**

Yes, but remove deprecated or unused plugins to reduce security risks and maintenance overhead.

### 📌 Scenario 11

> **❓ Question:**

You need to deploy the same application to Dev, QA, and Production.
**💡 Answer:**

Use a parameterized pipeline or different branches/tags for each environment.

### 📌 Scenario 12

> **❓ Question:**

A pull request cannot be merged.
**💡 Answer:**

Check whether approvals required by the CODEOWNERS file are still pending.

### 📌 Scenario 13

> **❓ Question:**

A Jenkinsfile is overwritten after a branch merge.
**💡 Answer:**

Review the merge, restore the correct Jenkinsfile from version control, and prevent accidental overwrites with proper review practices.

### 📌 Scenario 14

> **❓ Question:**

The build queue keeps increasing.
**💡 Answer:**

Add more agents, increase resources, or optimize jobs into smaller tasks.

### 📌 Scenario 15

> **❓ Question:**

A production change is requested in an ITIL-based company.
**💡 Answer:**

Follow the Change Advisory Board (CAB/CAP) process with impact analysis and rollback planning before implementation.

### 📌 Scenario 16

> **❓ Question:**

You need to migrate Jenkins to another server.
**💡 Answer:**

Restore the backed-up Jenkins configuration using the Backup Plugin or the Git-backed Jenkins home directory.

### 📌 Scenario 17

> **❓ Question:**

Developers ask why they cannot edit Jenkins backend files directly.
**💡 Answer:**

Backend files are managed by Jenkins and manual edits can corrupt configuration. Use the Jenkins UI whenever possible.

### 📌 Scenario 18

> **❓ Question:**

A build hangs indefinitely.
**💡 Answer:**

Check CPU, memory, and disk space on the agent, as these are common causes of hung pipelines.

### 📌 Scenario 19

> **❓ Question:**

A new environment (UAT) needs to be added.
**💡 Answer:**

Extend the parameterized pipeline or create a dedicated branch and corresponding deployment configuration for UAT.

### 📌 Scenario 20

> **❓ Question:**

How do you prepare Jenkins for production?
**💡 Answer:**

Use agents instead of the master for builds.
Back up Jenkins regularly.
Monitor plugin health.
Remove deprecated plugins.
Secure secrets and credentials.
Review console logs when troubleshooting.

