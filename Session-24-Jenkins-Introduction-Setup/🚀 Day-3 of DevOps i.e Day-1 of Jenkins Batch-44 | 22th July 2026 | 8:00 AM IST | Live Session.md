# 🚀 DevOps Batch-44 | Day 03: Jenkins & Continuous Integration (CI) Fundamentals

[![Module: Jenkins CI/CD](https://img.shields.io/badge/Module-Jenkins_CI%2FCD-D24939?style=for-the-badge&logo=jenkins)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 22nd July 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-22nd%20July%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


Multi-Cloud + DevOps with AI Bootcamp
Module: Jenkins (Continuous Integration)
Instructor: Vikas
Duration: ~3 Hours

Introduction
Day-3 was dedicated to one of the most important DevOps tools—Jenkins. Before learning Jenkins, the session focused on understanding Continuous Integration (CI) and why companies automate software development instead of performing builds manually.
In a real software company, developers work on different features simultaneously. If everyone waits until the end of the sprint to merge their code, integration becomes difficult, bugs increase, and deployments become risky. Continuous Integration solves this problem by encouraging developers to merge their code frequently. Jenkins then automatically builds the application, runs tests, and provides immediate feedback if anything goes wrong.
The session was designed to help students understand not only how to install Jenkins but also why almost every enterprise uses CI/CD pipelines today.

What is Continuous Integration (CI)?
Continuous Integration (CI) is a software development practice where developers merge their code changes into a shared Git repository multiple times a day. Every time code is committed, an automation server like Jenkins detects the change and performs several automated tasks such as compiling the application, executing unit tests, checking code quality, and notifying developers about the results.
The primary objective of CI is to identify bugs and integration issues as early as possible, reducing manual effort and improving software quality.

Traditional Development Process (Without CI)
In older software development approaches:
Developer A works on Feature A.
Developer B works on Feature B.
Developer C works on Feature C.
Each developer keeps their code locally for several days or weeks.
At the end of development:
All code is merged together.
Hundreds of merge conflicts occur.
Build failures happen.
Testing becomes difficult.
Deployment gets delayed.
Finding the source of a bug becomes extremely time-consuming because many changes are merged at once.

Modern Development Process (With CI)
With Continuous Integration:
Developer writes code.
Code is committed to Git multiple times a day.
Jenkins automatically detects the new commit.
Jenkins downloads the latest code.
Application is compiled.
Automated tests are executed.
Static code analysis can be performed.
Results are shared with developers.
Developers fix issues immediately.
Instead of fixing 100 bugs at the end of the project, teams fix one or two bugs immediately after every commit.

Why Continuous Integration is Important
Continuous Integration provides several advantages in modern software development:
Faster bug detection
Early identification of integration issues
Better collaboration among developers
Improved software quality
Faster release cycles
Reduced manual effort
Automated testing
Higher customer satisfaction
Reduced deployment risks
Large organizations like Amazon, Google, Netflix, Microsoft, and Facebook rely heavily on CI/CD pipelines because they release software many times a day.

What is Jenkins?
Jenkins is an open-source automation server used for implementing Continuous Integration and Continuous Delivery (CI/CD). It automates repetitive software development tasks such as:
Downloading source code
Compiling applications
Running automated tests
Performing code quality analysis
Building Docker images
Deploying applications
Sending notifications
Instead of developers manually performing these tasks, Jenkins automates the entire workflow.

Why Jenkins is So Popular
Jenkins remains one of the most widely used automation servers because it is:
Completely Open Source
Free to use
Easy to install
Cross-platform (Windows, Linux, macOS)
Highly customizable
Supports distributed builds
Integrates with almost every DevOps tool
Backed by a large community
Supports over 2000 plugins
Its plugin ecosystem makes Jenkins suitable for projects of any size, from small startups to large enterprises.

Features of Jenkins
During the session, the following features were discussed:
### 1. Open Source

No licensing cost and a strong global community.
### 2. Platform Independent

Runs on Windows, Linux, macOS, Docker containers, Kubernetes, AWS, Azure, and GCP.
### 3. Plugin Support

More than 2000 plugins allow Jenkins to integrate with tools such as:
Git
GitHub
GitLab
Bitbucket
Maven
Gradle
Docker
Kubernetes
Terraform
Ansible
SonarQube
Slack
Jira
AWS
Azure
GCP
### 4. Pipeline as Code

Pipelines can be written using a Jenkinsfile, making automation version-controlled and reusable.
### 5. Distributed Builds

Multiple Jenkins agents can execute builds simultaneously, improving performance.

Real-Time CI Workflow
```text
Developer
      │
      ▼
Git Repository
      │
      ▼
Jenkins Detects Commit
      │
      ▼
Download Source Code
      │
      ▼
Compile Application
      │
      ▼
Run Unit Tests
      │
      ▼
Static Code Analysis
      │
      ▼
Package Application
      │
      ▼
Generate Reports
      │
      ▼
Ready for Deployment
```


This automated workflow reduces manual intervention and ensures that every code change is validated before moving to the next stage.

Jenkins Installation on Windows
The session included a complete hands-on installation of Jenkins on Windows.
Step 1: Install Java
Jenkins requires Java to run because it is built using Java.
Students installed Java 21 and verified the installation using:
java -version

If Java was not detected, the following checks were performed:
Verify Java installation.
Configure the JAVA_HOME environment variable.
Add Java to the system PATH.
Step 2: Download Jenkins
The Windows installer (.msi) was downloaded from the official Jenkins website.
Step 3: Install Jenkins
During installation:
Jenkins service was installed.
Default port 8080 was selected.
Windows service configuration was completed.
Step 4: Start Jenkins
After installation, Jenkins started automatically as a Windows service.
Step 5: Unlock Jenkins
Students accessed:
http://localhost:8080

Jenkins requested the initial admin password.
Windows location:
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword

Step 6: Install Recommended Plugins
Jenkins downloaded and installed the recommended plugin set, including Git, Pipeline, Credentials, and other essential plugins.
Step 7: Create Admin User
Students created their administrator account and logged in to the Jenkins dashboard.

Jenkins Dashboard
After successful installation, the dashboard provides:
New Item (Create Jobs)
Build History
Manage Jenkins
Nodes
Plugins
Credentials
Build Queue
System Logs
This becomes the central location for managing all CI/CD activities.

Introduction to Google Cloud Platform (GCP)
The second part of the session introduced Google Cloud Platform (GCP) for cloud-based Jenkins installation.
Students learned how to:
Create a free GCP account.
Use free trial credits.
Navigate the Google Cloud Console.
Create a Compute Engine virtual machine.

Creating an Ubuntu VM on GCP
Recommended VM configuration:
Operating System: Ubuntu 24.04 LTS
RAM: 4 GB
Machine Type: e2-medium (or similar)
External Public IP: Enabled
Boot Disk: Standard Persistent Disk
This VM will be used in upcoming sessions to install Jenkins on Linux.

Jenkins Installation on Ubuntu (High-Level Steps)
The Linux installation process includes:
Update package repositories.
Install Java 21.
Add the Jenkins repository.
Install Jenkins using apt.
Start the Jenkins service.
Enable Jenkins to start automatically on boot.
Verify service status.
Access Jenkins using the VM's external IP and port 8080.

Firewall Configuration
One of the most common issues encountered during the session was the inability to access Jenkins from the browser.
Root cause:
The firewall blocked incoming traffic on port 8080.
Resolution:
Create a firewall rule in GCP.
Allow TCP traffic on port 8080.
Associate the rule with the VM.
Verify that the VM has an external IP address.
After applying these changes, Jenkins became accessible from the browser.

Common Troubleshooting
Java Not Found
Cause: Java not installed or JAVA_HOME not configured.
**💡 Solution:**

Install Java 21.
Verify with java -version.
Set JAVA_HOME and update PATH.

Jenkins Not Opening on localhost:8080
Possible Causes:
Jenkins service stopped.
Port conflict.
Firewall restrictions.
Java issues.
Checks:
Confirm Jenkins service is running.
Ensure port 8080 is free.
Restart the Jenkins service if required.

Plugin Installation Failed
Possible Causes:
Internet connectivity issues.
Corporate proxy.
Temporary update center issue.
**💡 Solution:**

Retry installation.
Verify internet access.
Install plugins later if required.

Jenkins Not Accessible on GCP
Possible Causes:
Firewall not configured.
Missing external IP.
Incorrect network tag.
**💡 Solution:**

Allow TCP 8080.
Verify VM networking settings.
Restart Jenkins if needed.

AWS vs GCP Discussion
The session briefly compared AWS and GCP:
AWS
GCP
Most widely adopted cloud provider
Simple and beginner-friendly interface
Largest service portfolio
Generous free trial credits
Strong enterprise presence
Excellent Kubernetes integration
Larger job market
Easy learning experience

Students were encouraged to gain hands-on experience with both platforms to become multi-cloud engineers.

LinkedIn Career Guidance
Vikas emphasized the importance of building a professional online presence. Students were advised to:
Optimize their LinkedIn profiles.
Post learning updates regularly.
Share screenshots of completed labs.
Tag mentors and relevant communities.
Engage with other professionals' posts.
Consistent activity on LinkedIn can improve visibility, expand professional networks, and increase opportunities for interviews and referrals.

Next Steps
Students were assigned the following tasks before the next session:
Install Jenkins on Windows or Linux.
Verify Java 21 installation.
Access Jenkins on http://localhost:8080.
Create a free AWS and/or GCP account.
Launch an Ubuntu 24.04 VM with 4 GB RAM.
Prepare the VM for Jenkins installation.
Configure firewall rules to allow port 8080.
Complete the Module Expert form.
Watch the recorded sessions if any part was missed.
Share a LinkedIn post summarizing what they learned.

## 🎓 Key Takeaways

Continuous Integration enables developers to merge code frequently and receive rapid feedback.
Jenkins automates repetitive tasks such as building, testing, and packaging applications.
Its extensive plugin ecosystem allows seamless integration with virtually every major DevOps tool.
Installing Jenkins requires Java, proper service configuration, and access to port 8080.
Linux is generally preferred for production Jenkins deployments due to better performance and stability.
Cloud platforms such as AWS and GCP provide an ideal environment for learning and running Jenkins in real-world scenarios.
Regular hands-on practice and documenting your learning on LinkedIn are essential habits for building a successful DevOps career.

