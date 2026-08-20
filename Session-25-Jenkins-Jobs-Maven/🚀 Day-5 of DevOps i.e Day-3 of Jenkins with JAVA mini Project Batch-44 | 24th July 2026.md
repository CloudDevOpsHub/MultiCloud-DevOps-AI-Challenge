# 🚀 DevOps Batch-44 | Day 05: Jenkins CI Pipeline with Java Spring Boot Project

[![Module: Jenkins CI/CD](https://img.shields.io/badge/Module-Jenkins_CI%2FCD-D24939?style=for-the-badge&logo=jenkins)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 24th July 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-24th%20July%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)

---
> [⬅️ Day 04](Day-04-Jenkins-CI-Advanced.md) | [🏠 Master Learning Index](README.md) | [Day 06 ➡️](Day-06-Jenkins-Pipeline-Stage-View.md)
---

## 📋 Key Outcomes

The session covered end-to-end setup of a Jenkins CI pipeline to build and run a Java Spring Boot application using Maven. Participants provisioned a GCP VM, installed Jenkins, configured jobs, and successfully compiled, packaged, and deployed a Spring Boot app via a single Maven command. Key DevOps concepts including RBAC, plugins, credentials management, and artifact handling were demonstrated live.
### 💡 Key Decisions Made

- VM specs: Minimum 4GB RAM, 1 core; Ubuntu 24 LTS selected for long-term support
- Storage: 25GB disk allocated to accommodate multiple build attempts and troubleshooting
- Maven installation: Install Maven directly on the Linux machine (not via plugin) for full capabilities on large projects
- Build command: mvn install chosen as the single command to compile, test, package, and install dependencies
- App port: 3000, defined in application.properties by the developer — DevOps should not modify
- RBAC plugin: Role-Based Authorization Strategy plugin installed to restrict access per team (Dev, QA, L1, DevOps)
### ✅ Completed Milestones

- Jenkins installed on GCP VM; version 2.5x confirmed
- Jenkins admin user created; additional user (Shalini) created to demonstrate user management
- RBAC plugin installed via Manage Jenkins → Plugins; safe restart performed
- Bitbucket and Docker plugins installed to demonstrate extensibility
- Freestyle job created with GitHub repo (public) as source; branch set to main
- mvn install added as Execute Shell build step; build succeeded in ~40 seconds
- Spring Boot app deployed manually using java -jar command on port 3000; confirmed running on both Linux and Windows (platform independence demonstrated)
## 🔑 Key Concepts Covered

- Maven lifecycle: mvn compile → mvn test → mvn package → mvn install (does all); mvn clean recommended for repeated runs
- pom.xml: Project Object Model — written by developers; defines dependencies, artifact name, version, build config
- MVNW (Maven Wrapper): Allows running Maven without installation but is slower and not recommended for real-time/large projects
- Workspace: Temporary Jenkins working directory where code is cloned and builds execute
- Target folder: Created after build; contains the JAR artifact
- Artifact storage: JFrog and Nexus identified as tools for maintaining artifacts in real-time environments
- Safe restart: Jenkins should be restarted only when no jobs are running after plugin installation
- Master/Slave Jenkins: Recommended for scaling — isolate heavy or non-critical jobs on slave nodes
### ⚠️ Blockers & Resolutions

- Region availability error on GCP (E2 standard unavailable in US Central B) → resolved by switching to a different region
- File lock error during package update (process conflict) → resolved by waiting and re-running
- mvn install failed initially because Maven was not installed on the server → resolved with apt install maven
- Space in Jenkins workspace path caused directory navigation errors → resolved using backslash escape or TAB autocomplete
- Multiple participants unable to log in to Jenkins after plugin install → required waiting for restart to complete
### ⏳ Pending Confirmation

- Automated deployment pipeline (CD step) not yet implemented — manual java -jar used today; full pipeline to be built in a future session
- Jenkins Master/Slave configuration scheduled for next Wednesday
### 📋 Action Items

- All participants: Watch previous 5 session recordings over the weekend to reinforce concepts
- All participants: Practice creating a freestyle job, running mvn install, and locating the JAR in the target folder independently
- Participants with access issues: Fork the shared GitHub repo into personal accounts before running builds
- Participants with GCP billing issues: Verify account with government ID and upgrade to full account

## 🎯 20 Jenkins + Maven + Java Spring Boot Interview Questions & Answers

### ❓ Q1: What is Jenkins?

**💡 Answer:**

Jenkins is an open-source automation server used to automate Continuous Integration (CI) and Continuous Delivery (CD). It automatically builds, tests, and deploys applications whenever developers push code to a Git repository.

### ❓ Q2: What is Continuous Integration (CI)?

**💡 Answer:**

Continuous Integration is the practice of frequently merging code into a shared repository where every commit is automatically built and tested.
Benefits
Detects bugs early
Reduces integration issues
Improves code quality
Saves developer time

### ❓ Q3: Why is Jenkins widely used in DevOps?

**💡 Answer:**

Jenkins supports:
2000+ plugins
GitHub, GitLab, Bitbucket integration
Docker and Kubernetes integration
Pipeline as Code
Distributed builds
Easy automation

### ❓ Q4: Explain the Maven lifecycle.

**💡 Answer:**

Main Maven phases:
```bash
mvn validate
mvn compile
mvn test
mvn package
mvn verify
mvn install
mvn deploy
```

Most companies commonly use:
```bash
mvn clean install
```

because it cleans old files and builds everything from scratch.

### ❓ Q5: What is the difference between mvn compile and mvn install?

Answer
Command
Purpose
```bash
mvn compile
```

Compiles Java code only
```bash
mvn package
```

Creates JAR/WAR
```bash
mvn install
```

Compiles, tests, packages and stores artifact in local Maven repository


### ❓ Q6: What is pom.xml?

**💡 Answer:**

POM stands for Project Object Model.
It contains
Project information
Dependencies
Plugins
Java version
Artifact name
Packaging type
Build configuration
Developers usually maintain this file.

### ❓ Q7: What is the target folder?

Answer
After Maven builds a project, it creates a target directory.
It contains:
Compiled classes
JAR/WAR file
Reports
Temporary build files
Example:
```text
target/
 ├── classes
 ├── generated-sources
 ├── springboot-demo.jar
```


### ❓ Q8: What is Maven Wrapper (MVNW)?

Answer
MVNW allows developers to run Maven without installing Maven manually.
Example
./mvnw clean install
Advantages
Same Maven version for everyone
Disadvantages
Slightly slower
Not preferred for very large production builds

### ❓ Q9: Why do companies install Maven on Jenkins servers instead of using MVNW?

Answer
Because
Faster
Centralized version management
Better performance
Easier maintenance
Standard enterprise practice

### ❓ Q10: What happens when Jenkins runs a Freestyle Job?

Answer
Sequence:
Clone code from Git
Create Workspace
Execute build commands
Generate artifact
Show logs
Archive build

### ❓ Q11: What is Jenkins Workspace?

Answer
Workspace is the directory where Jenkins downloads project source code and executes builds.
Example
/var/lib/jenkins/workspace/MyProject

### ❓ Q12: What is an Artifact?

Answer
Artifact is the output generated after a successful build.
Examples
JAR
WAR
ZIP
Docker Image

### ❓ Q13: Where are artifacts stored in enterprises?

Answer
Mostly in
Nexus Repository
JFrog Artifactory
Benefits
Versioning
Backup
Security
Easy deployment

### ❓ Q14: Why should Jenkins Safe Restart be used?

Answer
Safe Restart waits until all running jobs finish before restarting Jenkins.
It prevents:
Build failures
Data corruption
Interrupted deployments

### ❓ Q15: What is RBAC in Jenkins?

Answer
RBAC means Role Based Access Control.
Different permissions are assigned to:
Developer
QA
DevOps
Admin
L1 Support
This improves security.

16. Difference between Jenkins Admin and Normal User?
Answer
Admin
Install plugins
Manage credentials
Restart Jenkins
Configure jobs
Normal User
Build projects
View logs
Access assigned jobs only

### ❓ Q17: What is Jenkins Master-Agent (Master-Slave) Architecture?

Answer
Master
Controls Jenkins
Schedules builds
Agent
Executes build jobs
Advantages
Faster builds
Better scalability
Workload distribution

### ❓ Q18: Why do we use java -jar?

Answer
To start a Spring Boot application.
Example
```bash
java -jar springboot-demo.jar
```

This launches the embedded Tomcat server.

### ❓ Q19: Why shouldn't DevOps change the application port?

Answer
The application port is defined by developers inside
application.properties
Changing it without approval may cause:
Application failures
Environment mismatch
Unexpected production issues

### ❓ Q20: Explain the complete CI flow using Jenkins.

Answer
```text
Developer
  	↓
Push Code to GitHub
  	↓
Jenkins Pulls Code
  	↓
Workspace Created
  	↓
mvn clean install
  	↓
Target Folder
  	↓
JAR Generated
  	↓
Artifact Stored
  	↓
Deployment
```


20 Real-Time Scenario Based Interview Questions & Answers
### 📌 Scenario 1

Interviewer:
Your Jenkins build fails saying:
mvn: command not found
What will you do?
**💡 Answer:**

Verify Maven installation
mvn -version
Install Maven
```bash
sudo apt update
sudo apt install maven
```

Verify PATH
Rebuild

### 📌 Scenario 2

Your Jenkins workspace contains spaces in its path and shell commands fail.
Answer
Use
cd My\ Folder
or
cd "My Folder"
or press TAB for autocomplete.

### 📌 Scenario 3

Plugin installation completed but users cannot log in.
Answer
Wait for Jenkins restart
Use Safe Restart
Check Jenkins logs
Ensure service is running

### 📌 Scenario 4

Build succeeds but target folder is missing.
Answer
Possible reasons
Build actually failed
Wrong workspace
Package phase not executed
pom.xml issue

### 📌 Scenario 5

GitHub repository is not downloading in Jenkins.
Answer
Check
Repository URL
Branch name
Internet connectivity
Credentials (if private repository)

### 📌 Scenario 6

Developer asks why Jenkins is creating multiple workspaces.
Answer
Because each job has its own isolated workspace to prevent conflicts between builds.

### 📌 Scenario 7

The application starts locally but fails on Jenkins.
Answer
Check
Java version
Maven version
Missing dependencies
Environment variables
Build logs

### 📌 Scenario 8

Jenkins server becomes slow after many builds.
Answer
Clean
Old workspaces
Old build history
Unused plugins
Large logs
Increase
CPU
RAM

### 📌 Scenario 9

Multiple developers want different permissions.
Answer
Implement RBAC.
Example
Developers → Build only
QA → View builds
DevOps → Configure jobs
Admin → Full access

### 📌 Scenario 10

Your build works locally but fails in Jenkins.
Answer
Compare
Java version
Maven version
Environment variables
File permissions
OS differences

### 📌 Scenario 11

The generated JAR file needs to be reused by another team.
Answer
Upload it to
Nexus
JFrog Artifactory
instead of rebuilding every time.

### 📌 Scenario 12

GCP VM creation fails because the selected zone has no available resources.
Answer
Choose another region or availability zone with available capacity.

### 📌 Scenario 13

A package installation fails with a file lock error on Ubuntu.
Answer
Wait for the current package process to finish.
Check running package processes:
ps -ef | grep apt
Retry the installation after the lock is released.

### 📌 Scenario 14

Your Jenkins job suddenly starts failing after a plugin upgrade.
Answer
Check compatibility between Jenkins and the plugin.
Review Jenkins logs.
Roll back the plugin if needed.
Test plugin updates in a non-production environment first.

### 📌 Scenario 15

A developer accidentally changes the pom.xml, and all builds fail.
Answer
Review the Git commit history.
Compare the changes in pom.xml.
Revert the incorrect commit or fix the configuration.
Trigger a new build.

### 📌 Scenario 16

Your Spring Boot application builds successfully but doesn't start.
Answer
Check:
Correct JAR file
Application logs
Port availability
Java version
Missing configuration properties

### 📌 Scenario 17

The build succeeds, but Jenkins cannot find the artifact.
Answer
Verify:
Artifact is created in the target folder.
Correct artifact path is configured.
Build step completed successfully.
Archive Artifact configuration points to the correct file.

### 📌 Scenario 18

Your team has 50 build jobs, and Jenkins becomes overloaded.
Answer
Implement a Master-Agent architecture:
Master manages jobs.
Multiple agents execute builds in parallel.
Distribute workloads across build nodes.

### 📌 Scenario 19

Developers manually run java -jar after every build. How would you improve this?
Answer
Automate deployment by:
Creating a Jenkins Pipeline.
Adding deployment stages after the build.
Using SSH, Docker, Kubernetes, or system services to start the application automatically.

### 📌 Scenario 20

Your organization wants every code commit to trigger an automatic build.
Answer
Configure:
GitHub Webhooks (or Bitbucket Webhooks).
Jenkins job with SCM polling or webhook triggers.
On every commit:
Jenkins pulls the latest code.
Executes mvn clean install.
Generates the artifact.
Reports build status automatically.
This enables a complete Continuous Integration (CI) workflow with immediate feedback to developers.


---
> [⬅️ Day 04](Day-04-Jenkins-CI-Advanced.md) | [🏠 Master Learning Index](README.md) | [Day 06 ➡️](Day-06-Jenkins-Pipeline-Stage-View.md)
