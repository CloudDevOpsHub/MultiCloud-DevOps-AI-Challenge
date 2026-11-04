## Key Outcomes

The session focused on teaching Jenkins Pipeline concepts, Groovy-based pipeline syntax, and multiple approaches to installing and running Jenkins locally. Students practiced running Jenkins via WAR file, MSI installer on Windows, and explored pipeline syntax generation tools. The key distinction between **declarative** and **scripted** pipelines was explained, and students learned to auto-generate Groovy pipeline code using Jenkins' built-in **Pipeline Syntax Generator** and ChatGPT rather than writing code from scratch. 

---

## Decisions Made

- **Primary approach for pipeline coding:** Use the **Jenkins Pipeline Syntax Generator** (and ChatGPT as a supplement) to auto-generate Groovy code — approximately 70–90% of pipeline code is available as ready-made reference; only minor tweaks are needed per project. 
- **Java version standardized at JDK 25** for Jenkins compatibility; Java 21 is acceptable but nearing end-of-support; Java 8 and 17 are too old and cause failures. 
- **Declarative pipeline preferred** over scripted in real-world company use because pipeline code stored in GitHub (as a `Jenkinsfile`) enables version control of the pipeline itself. 
- **Linux preferred over Windows** for Jenkins execution due to significantly better performance; Windows requires additional environment variable configuration (`PATH` settings) not needed on Linux. 
- **Port 8080** is the default Jenkins port; different ports (8082, 8083, 8087, etc.) are assigned to different projects to avoid conflicts when running multiple projects simultaneously. 

---

## Jenkins Installation Approaches Covered

Three distinct approaches were demonstrated, each with a specific use case:

### Approach 1 — WAR File (Temporary / Quick Turnaround)

- Download `jenkins.war` from the Jenkins releases page (version 2.587 used as example, ~50.1 MB). 
- Place the WAR file in a `bin` folder on the C drive. 
- Open CMD from that folder and run: `java -jar jenkins.war --httpPort=8082` 
- **Limitation:** Jenkins goes down as soon as the terminal is closed — not suitable for production. 
- **Use case:** Quick environment testing, verifying Jenkins compatibility, or temporary setups. 

### Approach 2 — MSI Installer on Windows (Permanent Local Installation)

- Download Jenkins LTS version (2.5x, ~57.4 MB) Windows MSI from `jenkins.io/download`. 
- Install to `Program Files\Jenkins`; select **"Run service as local system"** during setup. 
- Test port (default 8080); if occupied, switch to 8087. 
- Point installer to JDK 25 location if auto-detection fails (navigate to `Program Files\Java\JDK25\bin`). 
- Retrieve initial admin password from: `C:\ProgramData\Jenkins\secrets\initialAdminPassword` (open in Notepad). 
- Install suggested plugins, create admin user (`admin/admin` used for demo), then Jenkins is permanently available at `localhost:8080`. 
- **Key difference from Linux:** Windows pipelines use `bat` (batch) steps; Linux pipelines use `sh` (shell) steps. 

### Approach 3 — Running on Existing Linux VM (GCP/AWS)

- Jenkins already installed on a Linux VM runs identically to local Windows Jenkins at the UI level; version differences may cause minor UI changes. 
- Linux is significantly faster than Windows for Jenkins operations. 
- Firewall/security group rules must allow the relevant port (e.g., 8080, 8083) for external access. 

---

## Jenkins Pipeline — Core Concepts

### What Is a Pipeline?

- A **pipeline** is an ordered set of **stages**, each containing **steps** (commands), executed sequentially to achieve a goal (e.g., clone → build → test → deploy).
- is a set of automated processes and tools that lets developers build, test, and deploy code to production quickly and safely
- Written in **Groovy** language when coded directly in Jenkins. 
- Analogy used: water flowing through pipes from a tank to multiple destinations — the pipeline defines the path and sequence. 

### Pipeline Syntax Structure (15-line skeleton to memorize)

```plaintext
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // command here
            }
        }
        stage('Deploy') {
            steps {
                // command here
            }
        }
    }
}
```

- **`pipeline {}`** — top-level block containing everything. 
- **`agent any`** — run on any available node/VM. 
- **`stages {}`** — container for all stages (Build, Deploy, Test, Scan, Email, etc.). 
- **`stage('name') {}`** — individual stage with a label. 
- **`steps {}`** — contains the actual commands to execute within a stage. 
- To add a new stage (e.g., email notification), copy an existing stage block and replace the step command. 
- Students were instructed to write these 15 lines on paper by hand for interview preparation. 

### Linux vs. Windows Pipeline Difference

- **Linux step:** `sh 'java HelloWorld.java'` — uses shell script execution. 
- **Windows step:** `bat 'java HelloWorld.java'` — uses Windows batch script; also requires setting `PATH` environment variable within the pipeline for the JDK location. 
- This is the **primary technical difference** between Linux and Windows pipelines; all other syntax remains identical. 

---

## Pipeline Syntax Generator — Key Tool

- Located inside Jenkins: **New Item → Pipeline → Configure → Pipeline Syntax**. 
- Allows generating any pipeline step command via a UI form — eliminates the need to memorize complex syntax. 
- **Demo — Git clone step:**
    - Select `git` from the dropdown, enter repo URL and branch (e.g., `main`), click **Generate Pipeline Syntax**. 
    - The full `git` command with correct quoting and parameters is generated automatically. 
- **Demo — Shell compile step:**
    - Select `sh`, enter `javac HelloWorld.java`, click Generate → outputs `sh 'javac HelloWorld.java'`. 
- **Demo — Windows batch step:**
    - Select `bat`, enter same command → outputs `bat 'javac HelloWorld.java'`. 
- **Demo — Email notification step:**
    - Select `mail`, fill in To, CC, BCC, Subject (can include job status/ID variables), click Generate → full email step syntax produced. 
- **Interview tip:** If asked to write pipeline code, state: *"Let me log into Jenkins and use the Pipeline Syntax Generator"* — this is the real-world approach. 

---

## Declarative vs. Scripted Pipeline

|        Aspect         |                  Scripted                  |             Declarative              |
|-----------------------|--------------------------------------------|--------------------------------------|
| Top-level keyword     | `node`                                     | `pipeline`                           |
| Where code lives      | Pasted directly into Jenkins UI script box | Stored in GitHub as a `Jenkinsfile`  |
| Version control       | Not inherently version-controlled          | Full Git history of pipeline changes |
| Real-world preference | Less common                                | Preferred in companies               |
| Code similarity       | ~99% same Groovy code                      | ~99% same Groovy code                |

- **Scripted pipeline:** Uses `node {}` as the top-level block; code written directly in the Jenkins job configuration. 
- **Declarative pipeline:** Uses `pipeline {}` as the top-level block; code stored in a file named exactly **`Jenkinsfile`** in the GitHub repository. 
- In Jenkins UI: **Pipeline Script** = scripted; **Pipeline Script from SCM** = declarative (Jenkins reads the `Jenkinsfile` from the provided Git URL). 
- Companies prefer declarative because the pipeline itself becomes versioned code — previous pipeline versions are recoverable via Git commits. 
- Code content is ~99.9% identical between the two approaches; only the delivery mechanism differs. 

---

## CI/CD Trigger Configuration in Pipelines

- **Approach 1 — Code-based trigger:** Add a `triggers {}` block below `agent any` in the pipeline, using `pollSCM` with a cron expression. 

```plaintext
triggers {
    pollSCM('H/5 * * * *')
}
```

- **Approach 2 — UI-based trigger:** In the Jenkins job configuration UI, under **Build Triggers**, select **Poll SCM** and set the schedule — equivalent to the code approach. 
- **Webhook trigger:** Also available; generates a webhook URL in Jenkins that GitHub can call on push events to trigger the pipeline automatically. 
- **Jira integration possibility:** Pipeline can be extended to close a Jira ticket automatically upon successful deployment by hitting the Jira REST API with a POST request. 

---

## JAR vs. WAR vs. EAR vs. TAR

- **JAR (Java ARchive):** Single Java file or collection of Java class files. 
- **WAR (Web ARchive):** Web-based Java application package; contains JAR files plus web components (frontend assets); used for web applications. 
- **EAR (Enterprise ARchive):** Enterprise-level package; combination of JARs with additional enterprise dependencies. 
- **TAR:** Linux zip/archive format — completely different from Java archives; not a Java artifact. 

---

## Common Issues & Resolutions

|                      Issue                      |                                                                       Resolution                                                                       |
|-------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Java version incompatibility (Java 8/17/27)     | Uninstall old versions, install **JDK 25**; multiple Java versions cause path conflicts                                                                |
| Jenkins installer can't find Java               | Manually navigate to `Program Files\Java\JDK25\bin` in the installer path selector                                                                     |
| Port already in use                             | Choose a different port (8087 or any unused port up to 65,535)                                                                                         |
| Initial admin password not found                | Navigate to `C:\ProgramData\Jenkins\secrets\initialAdminPassword`, open in Notepad                                                                     |
| Jenkins on GCP/AWS not accessible via public IP | Enable the relevant port (e.g., 8080/8083) in the **firewall rules / security group**                                                                  |
| GCP unexpected billing / data transfer charges  | Disable **auto-pay** immediately; public IP exposure can lead to unauthorized data transfer charges (example cited: ₹8,000 bill from 779 GB transfer)  |
| JRE vs. JDK confusion causing build failure     | Must install **JDK** (not JRE); JRE cannot compile code — only JDK supports compilation                                                                |
| WAR file won't move to bin folder               | Use **copy-paste** instead of cut-paste if cut operation fails                                                                                         |
| Screen blank/black on VM                        | Double-click to exit full screen; ensure VM display settings are correct                                                                               |

---

## GCP Billing Warnings (Critical for Students)

- **Disable auto-pay immediately** on GCP accounts used for learning to prevent unexpected charges. 
- A public IP left exposed on a VM can result in unauthorized traffic and large data transfer bills — instructor cited a personal example of a ₹8,000 charge from 779 GB of transfer. 
- Students on free trial: monitor billing reports and set billing alerts. 
- If a GCP account has been compromised or has unexpected charges: **disable auto-pay → contact Google Cloud support** explaining student status → request waiver. 
- Students needing a fresh start: create a **new email ID + new GCP account + new credit/debit card**. 
- Estimated cost for daily practice (VM running): approximately ₹5–10/day, which is considered manageable. 

---

## Upcoming Session Agenda

- **Next class:** Jenkins plugin integrations and Jenkins UI/configuration features. 
- **Following sessions:** Jenkins advanced topics (Architecture), then transition to Docker and Kubernetes. 
- **Student action requested:** Anyone with a real company Jenkins Pipeline (Java-based project preferred) to share it — instructor will explain it live to bridge theory and real-world practice. 
- **GitLab Pipeline session:** Students using GitLab at their companies are requested to share their pipeline code; a dedicated 5–10 minute GitLab pipeline walkthrough will be included in an upcoming class. 

---

## Action Items

- **All students:** Install **JDK 25** and remove conflicting older Java versions (8, 17, 27 if causing issues). 
- **All students:** Write the 15-line pipeline skeleton on paper and paste a photo to the WhatsApp group. 
- **All students using GCP:** Disable auto-pay on GCP accounts immediately to prevent unexpected billing. 
- **Students with company pipelines:** Share real Jenkins Pipeline code (Java-based preferred) with the instructor for live review in the next session. 
- **GitLab users:** Prepare to present a 5–10 minute walkthrough of their company's GitLab CI/CD pipeline in an upcoming class. 
- **Students with Jenkins install issues:** Verify JDK installation, ensure firewall allows the Jenkins port, and retrieve the initial password from the correct file path. 
- **Ravi Gadi:** Run the `java -jar` command to start the deployed JAR service on the Linux VM before attempting to access it via the public IP. 
- **Gopal Patil:** Contact Google Cloud support regarding billing error; if unresolved, create a new GCP account with a new email and payment method. 

---

## Open Questions

- How to call one pipeline from another pipeline (pipeline chaining) — deferred to a future session. 
- Email notification with SMTP configuration: requires a valid SMTP server (company SMTP or `smtp.gmail.com` on port 465); instructor noted Gmail requires additional app-password configuration and did not demonstrate live to avoid credential compromise. 
- Architectural differences between **GKE** (Google Kubernetes Engine) and **EKS** (Amazon Elastic Kubernetes Service) — raised by Mandhir, deferred to the Kubernetes module. 
- Parameterized pipelines (passing parameters into a pipeline) — raised by Shashwat, deferred to a future class.


---

# Interview Preparation: 20 Questions and Answers

### 1. What is a Jenkins Pipeline?
A Jenkins Pipeline is an ordered set of stages and steps used to automate software delivery tasks such as cloning source code, building, testing, and deploying an application.

### 2. What language is used to write Jenkins Pipelines?
Jenkins Pipeline code is written using Groovy-based syntax. Declarative and Scripted Pipelines are the two approaches covered in this session.

### 3. What is the basic structure of a Declarative Pipeline?
A Declarative Pipeline uses the `pipeline {}` block at the top level. It commonly contains `agent`, `stages`, `stage`, and `steps` blocks.

### 4. What does `agent any` mean?
`agent any` tells Jenkins that the pipeline can run on any available agent or node.

### 5. What is the purpose of the `stages` block?
The `stages` block groups the stages of a pipeline, such as Build, Test, Scan, and Deploy.

### 6. What is the difference between a stage and a step?
A stage is a logical phase of the pipeline, such as Build or Deploy. A step is an individual command or action executed inside a stage.

### 7. What is a Jenkinsfile?
A Jenkinsfile is a text file containing the pipeline definition. In the workflow discussed in class, it is stored in the project's Git repository so that pipeline changes can be version-controlled.

### 8. What is the difference between Declarative and Scripted Pipeline?
Declarative Pipeline starts with `pipeline {}` and follows a more structured syntax. Scripted Pipeline commonly starts with `node {}` and provides a more code-driven approach. The session emphasized Declarative Pipeline for company use because it can be stored and versioned in Git.

### 9. What is the Pipeline Syntax Generator?
It is a Jenkins UI tool that generates pipeline step syntax from form inputs. It helps users generate commands for steps such as Git checkout, shell commands, batch commands, and email.

### 10. Where can you find the Pipeline Syntax Generator?
Open a Jenkins Pipeline job, go to **Configure**, and select **Pipeline Syntax**. The exact UI wording can vary slightly by Jenkins version.

### 11. How do you clone a Git repository in a Jenkins Pipeline?
Use the `git` pipeline step with the repository URL and branch. You can generate the exact syntax through **Pipeline Syntax → git**, then copy it into the pipeline.

### 12. What is the difference between `sh` and `bat` in Jenkins?
`sh` executes shell commands, commonly on Linux agents. `bat` executes Windows batch commands on Windows agents.

### 13. How can a pipeline be triggered when source code changes?
The session covered Poll SCM and webhooks. Poll SCM checks the repository on a configured schedule, while a GitHub webhook can notify Jenkins when a push event occurs.

### 14. What does `pollSCM('H/5 * * * *')` do?
It configures Jenkins to check the source control repository on a recurring schedule, approximately every five minutes. The `H` symbol lets Jenkins distribute polling times rather than making every job poll at the exact same minute.

### 15. What is the default Jenkins port?
The default Jenkins web port is `8080`. If it is already occupied, Jenkins can be configured to use another available port, such as `8082` or `8087`.

### 16. What happens when Jenkins is started using a WAR file from a terminal?
Jenkins runs in that terminal session. If the terminal process is closed, Jenkins stops. This approach is useful for temporary testing, not as a permanent production setup.

### 17. How do you start Jenkins from a WAR file on a specific port?
Run a command such as `java -jar jenkins.war --httpPort=8082` from the directory containing the WAR file.

### 18. Where is the initial Jenkins administrator password stored on Windows?
The session demonstrated this path: `C:\ProgramData\Jenkins\secrets\initialAdminPassword`.

### 19. What is the difference between JAR, WAR, and EAR files?
A JAR packages Java classes and resources. A WAR packages a Java web application. An EAR packages an enterprise application and its modules. TAR is a general archive format and is not a Java application archive format.

### 20. Why might a Jenkins pipeline build fail if only a JRE is installed?
A JRE is intended to run Java applications. Java compilation requires a JDK, which includes development tools such as the Java compiler. Therefore, install and configure a suitable JDK on the build agent.

---

# Scenario-Based Interview Questions and Answers (20)

### 1. Jenkins is already running on port 8080, and you need to start another Jenkins instance. What will you do?
**Answer:** Check which process is using port 8080 and choose another unused port, for example 8082 or 8087. Start the second instance with that port, such as `java -jar jenkins.war --httpPort=8082`.

### 2. You start Jenkins using a WAR file, close CMD, and Jenkins becomes unavailable. Why?
**Answer:** The WAR-based instance was running in the terminal process. Closing the terminal stopped that process. For a persistent local setup, use a service-based installation or run Jenkins on a properly managed server.

### 3. Your Jenkins Windows installer cannot detect Java. How would you troubleshoot it?
**Answer:** Confirm that a JDK is installed, locate its installation directory, and point the installer to the JDK location. Also check that the Java path is correct and that the required environment variables are configured.

### 4. A Java build works on your laptop but fails on the Jenkins agent with a compiler-not-found error. What would you check?
**Answer:** Check whether the agent has a JDK rather than only a JRE. Verify the configured Java installation and `PATH`, then confirm that the `javac` command is available to the build process.

### 5. A pipeline uses `sh` commands but runs on a Windows agent. What is the likely issue?
**Answer:** The pipeline is using a shell step that does not match the Windows environment. Use `bat` for Windows batch commands, or run the pipeline on a suitable Linux agent where `sh` is available.

### 6. A pipeline uses `bat` but is assigned to a Linux agent. How would you fix it?
**Answer:** Replace the Windows batch step with an appropriate `sh` step, or assign the job to a Windows agent. The command syntax must match the operating system where it runs.

### 7. Your Jenkins pipeline is difficult to maintain because its code is stored only in the Jenkins UI. What would you recommend?
**Answer:** Move the pipeline definition into a `Jenkinsfile` in the application's Git repository and configure the job to load the pipeline from SCM. This gives the pipeline Git history and makes changes reviewable.

### 8. A developer pushes code to GitHub, but the Jenkins pipeline does not start automatically. What would you investigate?
**Answer:** Check the configured trigger. If using a webhook, verify the GitHub webhook URL, event selection, Jenkins reachability, and job configuration. If using Poll SCM, verify the repository settings and polling schedule.

### 9. A Jenkins job is checking Git for changes too frequently or not frequently enough. What can you adjust?
**Answer:** Review the Poll SCM schedule in the job configuration or the `pollSCM` entry in the pipeline. Adjust the cron expression to the required interval and confirm that the repository is configured correctly.

### 10. You need to add an email notification step, but you do not remember its syntax. What is a practical approach?
**Answer:** Open **Pipeline Syntax** in Jenkins, select the mail step, enter the required fields, and generate the syntax. Before using it, configure a valid SMTP server and keep credentials secure.

### 11. An email notification step is present in the pipeline, but emails are not being delivered. What would you check?
**Answer:** Check whether the Jenkins email-related configuration and SMTP server details are valid. Verify the recipient and sender settings, network access, and any required authentication or app-password setup. Do not put SMTP passwords directly in the Jenkinsfile.

### 12. Your Jenkins instance on an AWS or GCP VM works locally but cannot be opened from your browser. What would you check?
**Answer:** Confirm that Jenkins is running and listening on the expected port. Then review the VM firewall or cloud security group rules, the VM's network access, and whether the correct public IP and port are being used. Allow only the access needed; avoid exposing Jenkins broadly.

### 13. Two projects need separate Jenkins instances on the same machine. How can you avoid a port conflict?
**Answer:** Assign each instance a different available HTTP port, for example 8082 for one and 8087 for another. Ensure that each process is configured to use its assigned port.

### 14. A Jenkins pipeline successfully builds a JAR, but the application is not accessible after deployment. What should you verify?
**Answer:** Confirm that the JAR was copied to the intended VM and that the Java application process was actually started, for example with `java -jar application.jar`. Then check application logs, the listening application port, and firewall rules. A successful build alone does not prove that the application is running.

### 15. Your team wants to recover an older version of its pipeline after a change breaks the build. What helps?
**Answer:** When the pipeline is stored as a Jenkinsfile in Git, use Git history to inspect the change and restore or revert a known-good version. This is one of the practical benefits of keeping pipeline code under version control.

### 16. A student installs several Java versions and Jenkins starts using an unexpected one. How would you troubleshoot it?
**Answer:** Inspect the Java executable resolved by the Jenkins service or agent, check the configured Java home and `PATH`, and remove or correct conflicting configuration. Configure Jenkins and the build tools to use the intended supported JDK consistently.

### 17. A build command works in an interactive terminal but fails inside Jenkins. What would you investigate?
**Answer:** Compare the environment used by Jenkins with the interactive terminal. Check the service account, working directory, `PATH`, Java configuration, file permissions, and whether the command is written for the agent's operating system.

### 18. Your team wants a successful deployment to close a related Jira ticket automatically. How could the pipeline be extended?
**Answer:** After a successful deployment, add a step that calls the Jira REST API using an HTTP POST request to perform the required ticket transition. Use securely stored credentials, validate the API response, and do not assume the transition succeeded just because the request was sent. This integration was discussed as a possible extension, not demonstrated in the session.

### 19. A team asks you to call one Jenkins pipeline from another, but you have not implemented pipeline chaining yet. How would you respond?
**Answer:** Explain that pipeline chaining was identified as a topic for a future session. In a real implementation, review Jenkins' supported job-triggering or pipeline-build mechanisms, then test permissions, parameters, and failure handling before using it in a production workflow.

### 20. Your GCP learning VM unexpectedly generates a large bill. What immediate checks and actions would you take?
**Answer:** Review the billing report to identify the resource and type of charge, inspect VM exposure and network traffic, and stop or secure resources that are not needed. Review billing alerts and account payment settings, and contact Google Cloud support if the charge appears unauthorized or unexpected. Do not assume that disabling auto-pay alone resolves the underlying resource or billing issue.

---

## Quick Revision Checklist

- [ ] Explain the pipeline → stages → steps relationship.
- [ ] Write the basic Declarative Pipeline skeleton from memory.
- [ ] Explain Declarative vs. Scripted Pipeline.
- [ ] Explain why teams store a `Jenkinsfile` in Git.
- [ ] Demonstrate `sh` on Linux and `bat` on Windows.
- [ ] Generate a Git checkout and command step using Pipeline Syntax.
- [ ] Explain Poll SCM and webhook triggers.
- [ ] Troubleshoot Java/JDK, port, and firewall issues.
- [ ] Explain the difference between JAR, WAR, EAR, and TAR.
- [ ] Describe safe handling of SMTP credentials and cloud VM exposure.

*Note: These questions are based on the topics and examples covered in the supplied Day-6 session notes. Topics marked as future or not demonstrated in the notes are identified as such in the relevant answers.*
