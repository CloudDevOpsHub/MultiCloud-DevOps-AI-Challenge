## Key Outcomes

The session focused on introducing Jenkins as a CI tool, covering installation on Google Cloud Platform (GCP) Ubuntu VMs, initial configuration, user management with role-based access control, and building a working CI pipeline that automatically triggers on GitHub code commits. Students successfully integrated Jenkins with GitHub, compiled a Java program through the pipeline, and configured a cron-based poll SCM trigger to automate builds. The class also introduced Maven as a build automation tool and previewed upcoming Spring Boot project work.

---

## Decisions Made

- **Jenkins installation approach:** The DevOps/developer approach was chosen — running Jenkins via a WAR file on an Ubuntu VM in GCP — over the local/end-user approach, which was deemed unsuitable for interview scenarios. 
- **Operating system:** Ubuntu 24.04 LTS selected for the Jenkins server; Linux preferred over Windows for performance reasons. 
- **Java version:** OpenJDK 21 used; a real-time issue arose where JRE was installed instead of JDK, requiring correction before compilation could succeed. 
- **Job type for today:** Freestyle job selected over Pipeline (Groovy) for initial learning; real-time market split noted as approximately 40% freestyle, 40% pipeline, 5% multi-branch. 
- **Trigger method:** Poll SCM with a cron schedule (`* * * * *`) used to check GitHub for changes every minute and auto-trigger builds. 
- **Static IP:** Acknowledged that in real-time environments, a static/elastic IP must be used so the Jenkins URL does not change on VM reboot. 
- **Plugin installed:** **Role-Based Authorization Strategy** plugin installed to enable granular user permission management beyond the default admin-for-all behavior. 

---

## Concepts Covered

### Continuous Integration (CI)

- **CI definition:** A development practice where developers integrate code multiple times per day; the process — compile, build, test — runs automatically with no manual interaction. 
- **Scope:** CI covers the **dev environment** up to and including testing; deployment/ops activities fall outside CI. 
- **Testing in CI:** Only **unit testing** occurs in CI — code logic validation written by developers, executed automatically. 
- **Benefits:** Increased productivity, improved quality, reduced risk through automation. 
- **Market share:** Jenkins holds approximately **60% of the CI/CD market** due to 2,300+ plugin integrations. 

### Jenkins Architecture & Key Properties

- **Open source:** Freely available; install, set up, and run. 
- **Lightweight by default:** Becomes heavier only as plugins are added; unnecessary plugins should be avoided to maintain speed. 
- **Cross-platform UI:** Jenkins UI is identical across Mac, Linux, and Windows; Linux runs faster than Windows. 
- **Master-slave configuration:** Planned for Day 29 (approximately Friday) to distribute build load across slave nodes and improve speed. 
- **Plugin ecosystem:** ~2,300 plugins; each plugin community estimated at ~10 contributors = ~23,000 community members supporting integrations. 

### Jenkins Installation Steps (Ubuntu/GCP)

1. `sudo apt update` — update package lists 
2. `sudo apt install openjdk-21-jdk` — install Java (JDK, not just JRE) 
3. `java -version` — verify installation 
4. Download Jenkins GPG key via `wget` 
5. Add Jenkins repository to sources list 
6. `sudo apt update` again (best practice before any new install) 
7. `sudo apt install jenkins` — install Jenkins 
8. `systemctl status jenkins` — verify service is running 
9. Enable HTTP/HTTPS traffic in GCP firewall (VM networking settings) 
10. Retrieve initial admin password from `/var/lib/jenkins/secrets/initialAdminPassword` 

### Java: JRE vs. JDK — Key Distinction

- **JRE (Java Runtime Environment):** Sufficient to *run* Jenkins as an application. 
- **JDK (Java Development Kit):** Required to *compile* Java code (`javac` command); without it, the build step fails with a "Java compiler not found" error. 
- **Real-time scenario shared:** A team had JRE documented in their setup guide; the DevOps engineer had to raise a ticket with the security/production team to get JDK approved and installed on the Jenkins server. 

### Java Compilation vs. Build — Interview Question

- **Compile (****`javac HelloWorld.java`****):** Converts source code to a `.class` (machine language/bytecode) file. 
- **Build (****`mvn package`** **/** **`mvn clean package`****):** Compiles + packages + runs tests + produces an **artifact** (JAR/WAR file) ready for deployment. 
- **Key rule:** In Java, the **filename and class name must match exactly** (case-sensitive); mismatch causes a compilation error. 

### Jenkins Job Configuration Walkthrough

- **Source Code Management (SCM):** GitHub repo URL entered; branch must match (e.g., `main` not `master`) — branch mismatch was the first failure students encountered. 
- **Build Steps → Execute Shell:** Two commands added for Linux environments: 
    - `javac HelloWorld.java`
    - `java HelloWorld`
- **For Windows:** Use **Execute Windows Batch Command** instead of Execute Shell; Windows-specific commands go here. 
- **Console Output:** Clicking the red/green icon on a build shows full logs — essential for debugging. 
- **Poll SCM trigger:** Cron syntax `* * * * *` (five stars) checks GitHub every minute; if changes are detected, the job runs automatically. 

### Cron Syntax Explained

- Format: `minute hour day-of-month month day-of-week` 
- Tool recommended: **crontab.guru** for generating and validating cron expressions. 
- Example given: Vikas's class schedule — 8 AM, Monday–Friday = `0 8 * * 1-5` 
- `*` in all five positions = run every minute (used for demo purposes) 

### Maven as a Build Tool

- **Maven** is a **build automation tool** for Java (and Java-based frameworks). 
- Reads **`pom.xml`** (Project Object Model) — defines project version, dependencies, and build lifecycle. 
- Downloads dependencies from **Maven Central** repository. 
- Key commands: 
    - `mvn install` — downloads dependencies from Maven Central as defined in `pom.xml`
    - `mvn clean package` — compiles, tests, and packages into a JAR/WAR artifact
    - `mvn test` — runs unit tests only
    - `mvn validate` — validates project structure (mentioned as a pre-step)

### User Management & Role-Based Access Control

- **Default behavior:** All new Jenkins users get full admin access — a security problem in real teams. 
- **Solution:** Install the **Role-Based Authorization Strategy** plugin. 
- **Configuration path:** Manage Jenkins → Security → Authorization → Project-based Matrix Authorization 
- **Workflow:** Create roles (e.g., `developers`) → assign permissions (read, create job, etc.) → add users to roles. 
- **Adding users:** Manage Jenkins → Users → Create User (username, password, email). 
- **Credential management:** Manage Jenkins → Credentials → Add credentials (username/password type for global scope). 

### Real-Time Warnings Encountered

- **Java 21 support expiry:** Jenkins displayed a warning that Java 21 support ends September 30, 2027; teams should plan migration to Java 27. 
- **Building on master node:** Warning that running all builds on the master/controller node poses security risks and performance bottlenecks; slave nodes should handle build workloads. 

---

## CI Pipeline Demo — End-to-End Flow

1. Developer writes Java `HelloWorld.java` locally in VS Code 
2. Code compiled and run locally (`javac`, `java`) to verify 
3. Code pushed to GitHub repository (`git add`, `git commit`, `git push`) 
4. Jenkins freestyle job created; GitHub repo URL configured in SCM 
5. Build Steps added: `javac` + `java` commands in Execute Shell 
6. Initial build failed — branch mismatch (`master` vs `main`) → fixed 
7. Second failure — JDK not installed on server → JDK installed → build succeeded 
8. Poll SCM configured with `* * * * *` cron; developer makes new commit 
9. Jenkins automatically detects change within one minute and triggers build — **CI achieved** 
10. Output updated with new developer name (Nikhil → Uttam + Apoorvi) confirmed in build log 

---

## Common Issues & Resolutions

|                 Issue                  |                   Root Cause                   |                           Resolution                           |
|----------------------------------------|------------------------------------------------|----------------------------------------------------------------|
| Jenkins URL not accessible             | HTTP/HTTPS firewall rule not enabled on GCP VM | Enable HTTP traffic in VM networking settings                  |
| Build failed: branch error             | Branch set to `master`; code was on `main`     | Change branch in job configuration to `main`                   |
| Build failed: `javac` not found        | JRE installed instead of JDK                   | Install `openjdk-21-jdk` on the server                         |
| Jenkins page not loading after install | Firewall rule not applied                      | Save VM settings after enabling HTTP; wait for propagation     |
| Job stuck on "pending"                 | Insufficient `/tmp` partition size (< 1 GB)    | Increase VM disk/temp space; use minimum T2-medium on AWS      |
| GitHub URL not accepted by Jenkins     | Git not installed on the Ubuntu server         | Run `sudo apt install git` on the Jenkins server               |
| Jenkins slow/non-functional            | Minimal Ubuntu version taken instead of LTS    | Reinstall using Ubuntu LTS; plugins may not load on minimal    |
| IP changes after VM reboot             | Dynamic IP assigned                            | Use static/elastic IP in production; recreate VM for learning  |

---

## Upcoming Topics (Next Class)

- **Maven deep dive:** Spring Boot Java project with Maven build pipeline 
- **Maven Central:** Dependency management and `pom.xml` structure 
- **Spring Boot framework:** Java web/backend development framework; will be used as the project codebase 
- **Node.js/NPM equivalent:** Ankush and Anuj asked about Node.js builds; Vikas confirmed NPM replaces Maven for Node projects — a slide/example will be prepared 
- **Jenkins Pipeline (Groovy):** Covered in subsequent sessions; code-based pipeline alternative to freestyle UI 
- **Jenkins on Docker/Kubernetes:** Deferred to Docker and Kubernetes-specific classes 

---

## Pending Confirmation

- **Node.js/React build pipeline example:** Requested by students; Vikas to prepare a slide comparing Maven (Java) vs. NPM (Node) vs. other build tools for different languages. 
- **Webhook vs. Poll SCM:** Mandhir raised that webhooks provide real-time push-based triggers vs. Poll SCM's polling approach; Vikas acknowledged both are valid trigger methods but deferred detailed webhook setup. 
- **Robot Framework / pytest integration with Jenkins:** Dewashish asked about integrating Python test frameworks; Vikas directed to manually run the framework first, then add shell execution steps in Jenkins — no dedicated class example confirmed yet. 
- **GCP billing/payment issues:** Several students (Dharmendra, Pankaj, Nirmal, Vinay) reported payment/account issues; advised to enable international transactions on debit cards, or upgrade free trial accounts. 

---

## Action Items

- **All students:** Practice the full Jenkins setup independently — install Jenkins on a fresh GCP Ubuntu LTS VM, configure a freestyle job, connect to GitHub, add build steps, and configure Poll SCM trigger. 
- **All students (Homework):** Experiment with cron expressions on **crontab.guru**; write a cron schedule for a specific use case (e.g., nightly backup, weekday-only builds). 
- **All students:** Experiment with user creation, role-based plugin configuration, and permission assignment after class. 
- **Students with Windows machines:** Recreate the same job using **Execute Windows Batch Command** instead of Execute Shell; set Java environment variables if `java` is not recognized. 
- **Gopal:** Set up Java environment variables on Windows laptop; verify `java` command works locally before attempting Jenkins integration. 
- **Ankush / Anuj:** Review a Node.js project example to understand NPM commands as the Maven equivalent; Vikas to prepare a comparative slide. 
- **Shrikant:** Delete current minimal-version VM, create a new one using Ubuntu LTS, install Git on the server, and redo Jenkins setup. 
- **Students on AWS:** Use minimum **T2-medium** instance type for Jenkins to avoid `/tmp` space issues causing pending builds. 
- **GCP users:** Enable the **auto-delete** option when creating VMs (set to 8–24 hours) to avoid unnecessary charges and forgotten running instances. 

---

## Open Questions

- **Database pipelines:** Shankar asked if Jenkins can be used for database configuration/installation pipelines; Vikas clarified Jenkins is **not** used for database pipelines — only for backups, monitoring tasks, and scheduling activities. 
- **Parallel vs. sequential jobs:** Pravin asked whether CI jobs run in parallel or sequentially; Vikas confirmed it **depends on settings** — both modes are configurable. 
- **Suppressing Jenkins logs:** Mohit raised a use case of bypassing security audit logs; Vikas did not confirm this as a valid or supported requirement. 
- **Multiple developers committing simultaneously:** Neminath asked how Jenkins handles concurrent commits; Vikas explained that commits rarely happen at the exact same second, and each commit triggers its own build run. 
- **Git merge vs. Git rebase:** Tarun asked; Vikas confirmed — **merge** moves code from one branch to another preserving history; **rebase** rewrites commits linearly on top of the target branch.


---

# Interview Preparation: Jenkins Setup and Continuous Integration

The following questions and answers are based on the topics covered in this session: Jenkins installation on Ubuntu/GCP, Java and JDK, Freestyle jobs, GitHub integration, Poll SCM, Maven basics, cron syntax, user permissions, and CI troubleshooting.

## Part 1: 20 Technical Interview Questions and Answers

### 1. What is Jenkins, and why is it used in DevOps?

**Answer:** Jenkins is an open-source automation server commonly used to automate software build, test, and delivery workflows. In this session, we used Jenkins for Continuous Integration: it connected to a GitHub repository, compiled Java code, ran it, and triggered builds when new changes were detected.

### 2. What is Continuous Integration (CI)?

**Answer:** Continuous Integration is a development practice in which developers integrate code changes frequently. An automated process checks the changes by performing activities such as compilation, building, and testing. The goal is to find integration problems early and reduce manual effort.

### 3. What is the difference between CI and deployment?

**Answer:** CI focuses on integrating code and automatically validating it through build and test activities. Deployment is the process of releasing the resulting application or artifact into an environment. The session focused on CI in the development and testing flow, not deployment.

### 4. What is a Jenkins Freestyle project?

**Answer:** A Freestyle project is a basic Jenkins job that can be configured through the Jenkins UI. We used it to connect a GitHub repository, select a branch, add shell build steps, and configure Poll SCM. It is useful for learning and for straightforward jobs.

### 5. What is the difference between a Freestyle job and a Pipeline job?

**Answer:** A Freestyle job is mainly configured through the Jenkins UI. A Pipeline job defines the workflow as code, commonly using a Jenkinsfile and Groovy-based pipeline syntax. The session used Freestyle for the initial hands-on exercise and introduced Pipeline as a topic for a later class.

### 6. What is the difference between JRE and JDK?

**Answer:** JRE (Java Runtime Environment) provides what is needed to run Java applications. JDK (Java Development Kit) includes development tools such as the Java compiler (`javac`). Jenkins may run with a Java runtime, but compiling Java source code on the Jenkins server requires a JDK.

### 7. Why did the Jenkins build fail when only JRE was installed?

**Answer:** The job used `javac HelloWorld.java` to compile the source file. The `javac` compiler is provided by the JDK, not by a JRE-only installation. Installing the JDK resolved the `javac`-not-found error.

### 8. What is the purpose of the Jenkins workspace?

**Answer:** A Jenkins job uses a workspace directory to check out source code and run build steps. In the session's Freestyle job, Jenkins retrieved the GitHub repository and executed the Java commands against the checked-out source. The workspace is where the job performs its work.

### 9. What is Source Code Management (SCM) in Jenkins?

**Answer:** SCM configuration tells Jenkins where the source code is stored and which version-control system and branch to use. In the demo, the GitHub repository URL and branch were configured in the job so Jenkins could retrieve the Java source code.

### 10. What is Poll SCM in Jenkins?

**Answer:** Poll SCM makes Jenkins periodically check the configured source repository for changes. If it detects a new change, Jenkins triggers the job. In the session, `* * * * *` was used to check every minute for demonstration purposes.

### 11. What is the difference between Poll SCM and a webhook?

**Answer:** Poll SCM checks the repository at scheduled intervals. A webhook is a push-based mechanism in which GitHub notifies Jenkins when an event such as a push occurs. Poll SCM can introduce a delay until the next check; a webhook can trigger the job soon after the event. Webhook setup was mentioned but not demonstrated in this session.

### 12. Explain the five fields in a cron expression.

**Answer:** The five fields are:

`minute hour day-of-month month day-of-week`

For example, `0 8 * * 1-5` means 8:00 AM, Monday through Friday. The expression `* * * * *` means every minute.

### 13. What does `javac HelloWorld.java` do?

**Answer:** It compiles the Java source file and generates a `.class` bytecode file. It does not package the application into a JAR or WAR artifact. The class name and filename must match exactly, including case.

### 14. What is Maven, and what is `pom.xml`?

**Answer:** Maven is a build automation and dependency-management tool for Java projects. It reads `pom.xml` (Project Object Model), which defines project information, dependencies, and build configuration. Maven can download required dependencies from repositories such as Maven Central.

### 15. What is the difference between `mvn test` and `mvn clean package`?

**Answer:** `mvn test` runs the project's test phase. `mvn clean package` cleans previous build output, compiles the project, runs the configured tests, and packages the application into an artifact such as a JAR or WAR, subject to the project's configuration.

### 16. What is the purpose of the Jenkins Console Output?

**Answer:** Console Output contains the log messages generated during a job run. It helps identify which stage or command failed and provides clues about the root cause. In the session, it was used to diagnose the branch mismatch and missing Java compiler.

### 17. Why should a Jenkins server use a static IP in a real environment?

**Answer:** A dynamic IP can change when a VM is stopped or restarted, which can make the Jenkins URL inaccessible at its previous address. A static or reserved IP keeps the endpoint stable. The session highlighted this as a production consideration for a GCP VM.

### 18. Why use the Role-Based Authorization Strategy plugin?

**Answer:** It allows administrators to assign permissions based on roles instead of giving every user full administrator access. For example, a developer role can receive only the permissions needed to read or create jobs. This supports more controlled access to Jenkins.

### 19. What is the purpose of Jenkins agents (formerly commonly called slaves)?

**Answer:** Agents are machines that execute build workloads assigned by the Jenkins controller. Distributing builds to agents can reduce load on the controller and help scale build execution. The session mentioned this architecture as an upcoming topic and warned against running all builds on the controller.

### 20. What is a build artifact?

**Answer:** A build artifact is an output produced by the build process, such as a JAR or WAR file. Compilation creates Java bytecode, while a build tool such as Maven can compile, test, and package the application into a distributable artifact.

---

## Part 2: 20 Scenario-Based Interview Questions and Answers

### 1. Your Jenkins job fails with `javac: command not found`. How will you troubleshoot it?

**Answer:**
1. Open the job's Console Output and confirm the exact error.
2. Connect to the Jenkins Ubuntu server.
3. Run `java -version` and `javac -version`.
4. If the compiler is missing, install the JDK, for example: `sudo apt install openjdk-21-jdk`.
5. Verify `javac -version` and rerun the job.

The likely cause in this session was that only the JRE had been installed instead of the JDK.

### 2. Jenkins cannot find the source branch, and the build fails. What will you check?

**Answer:**
1. Open the Jenkins job configuration.
2. Check the repository URL and branch setting under SCM.
3. Confirm the actual branch name in GitHub.
4. Update the Jenkins configuration to use the correct branch, such as `main` instead of `master`.
5. Save and run the job again.

The session encountered this exact issue because the job was configured for `master` while the code was on `main`.

### 3. You installed Jenkins on a GCP Ubuntu VM, but the Jenkins page does not open. What will you do?

**Answer:**
1. Check the VM's status and confirm Jenkins is running with `systemctl status jenkins`.
2. Check that the required HTTP/HTTPS firewall traffic is allowed in GCP.
3. Save the VM networking/firewall settings and allow time for the change to apply.
4. Confirm that you are using the correct VM IP and Jenkins port.
5. Review service logs if the page still does not load.

The session identified a missing GCP firewall rule as a common cause.

### 4. Jenkins was working, but its URL stopped working after the VM rebooted. What could be wrong?

**Answer:** The VM may have been assigned a dynamic IP address that changed after reboot. In a real environment, reserve or assign a static external IP and update DNS or the Jenkins URL accordingly.

### 5. A Jenkins job stays pending and does not start. What would you investigate?

**Answer:**
1. Check the Jenkins queue and job status.
2. Review available disk and temporary space on the VM.
3. Check whether the node is online and able to accept builds.
4. Review Jenkins logs and the job's Console Output.
5. In the environment discussed in class, a `/tmp` partition smaller than 1 GB was identified as one possible cause. Increase available space if that is the confirmed issue.

### 6. Jenkins reports that it cannot retrieve code from GitHub. What will you check first?

**Answer:**
1. Verify that the repository URL is correct.
2. Confirm Git is installed on the Jenkins server using `git --version`.
3. If Git is missing, install it with `sudo apt install git`.
4. Check repository access and credentials if the repository is private.
5. Run the job again and inspect Console Output.

The session specifically identified missing Git on the Ubuntu server as one possible cause.

### 7. A developer pushes new code to GitHub, but Jenkins does not build it immediately. Why?

**Answer:** If the job uses Poll SCM, Jenkins checks the repository only at the configured intervals. With `* * * * *`, it checks every minute, so the build may wait until the next poll. A webhook can be configured for push-based notification, but that setup was not covered in the session.

### 8. Your team wants Jenkins to run a job at 8 AM from Monday to Friday. What cron expression will you use?

**Answer:** Use:

`0 8 * * 1-5`

The fields represent minute, hour, day of month, month, and day of week. This schedules the job for 8:00 AM on weekdays.

### 9. You configured Poll SCM with `* * * * *`. What does it mean, and would you use it permanently?

**Answer:** It means Jenkins checks the repository every minute. It is useful for a quick demonstration, as in the session. For a real environment, choose a schedule that fits the team's needs or use a webhook to avoid unnecessary repeated polling.

### 10. A Java file is named `helloworld.java`, but the public class is `HelloWorld`. Compilation fails. What will you check?

**Answer:** Check the Java filename and public class name. Java requires the public class name and filename to match exactly, including capitalization. Rename the file or class so they match, then run the compile command again.

### 11. Your Java program compiles successfully, but the Jenkins job fails when executing it. How will you investigate?

**Answer:**
1. Read the Console Output to identify the failing command.
2. Confirm that compilation generated the expected `.class` file.
3. Check that the execution command uses the correct class name, such as `java HelloWorld`.
4. Confirm the job is running in the expected workspace and directory.
5. Reproduce the commands on the server to isolate environment-related issues.

### 12. A Jenkins job works on Linux but fails on a Windows machine. What could be the reason?

**Answer:** The job may be using Linux shell commands in an environment that expects Windows commands. On Windows, use **Execute Windows Batch Command** rather than **Execute Shell**, and verify that Java is installed and its environment variables are configured.

### 13. Every Jenkins user can access administrator settings. How would you improve security?

**Answer:**
1. Install the Role-Based Authorization Strategy plugin.
2. Configure the authorization strategy under Manage Jenkins → Security.
3. Create roles such as `developers`.
4. Assign only the required permissions to each role.
5. Create users and assign them to the appropriate roles.
6. Test access using a non-admin account.

This follows the role-based access approach demonstrated in the session.

### 14. A developer needs permission to view jobs and create a job, but should not administer Jenkins. What would you do?

**Answer:** Create or update a developer role and grant only the required permissions, such as read and job creation. Assign the developer's user account to that role and verify that administrative permissions are not granted.

### 15. Your Jenkins server becomes slow after installing many plugins. What will you investigate?

**Answer:** Review the installed plugins and identify those that are not required. Disable or remove unnecessary plugins after checking their dependencies and impact. The session highlighted that Jenkins is relatively lightweight by default and can become heavier as plugins are added.

### 16. Your team wants to build a Java Spring Boot project instead of a single Java file. What changes are needed?

**Answer:**
1. Store the Spring Boot project in GitHub.
2. Ensure the Jenkins build environment has the required JDK and Maven.
3. Configure the Jenkins job to retrieve the correct repository and branch.
4. Use the project's `pom.xml` to manage dependencies and build configuration.
5. Run an appropriate Maven command, such as `mvn clean package`.
6. Review the Console Output and verify that the expected JAR/WAR artifact is generated.

The Spring Boot Maven pipeline was introduced as an upcoming topic in the session.

### 17. Your Node.js project is being built using Maven commands. What would you change?

**Answer:** Maven is used for Java projects. For a Node.js project, use the Node.js package ecosystem and NPM commands instead. The session specifically noted NPM as the equivalent build-tool direction for Node.js; a detailed Node.js pipeline was not demonstrated.

### 18. Two developers push changes close together. How should you understand the Jenkins build behavior?

**Answer:** Jenkins can create separate build runs for detected changes, and concurrent execution depends on job and executor configuration. If the job is configured to allow concurrent builds and resources are available, runs may execute in parallel; otherwise, they may wait and execute sequentially. Review the job settings and build queue to confirm the behavior.

### 19. Your organization wants to distribute builds instead of running everything on the Jenkins controller. What approach would you take?

**Answer:** Configure Jenkins agents and assign build workloads to them. This distributes execution and helps avoid overloading the controller. The session identified controller-only builds as a potential security and performance concern; detailed agent setup was deferred to a later class.

### 20. A team asks you to use Jenkins to install and configure its production database. How would you respond?

**Answer:** First clarify the exact requirement. The session's guidance was that Jenkins was not being used for database installation/configuration pipelines in the demonstrated scope. It mentioned possible database-related automation such as backups, monitoring tasks, and scheduling. Any production database automation would need a separately designed and reviewed workflow.

---

## Quick Practical Revision Checklist

Use this checklist to prepare for a hands-on Jenkins interview or lab:

- [ ] Create an Ubuntu LTS VM on GCP.
- [ ] Install OpenJDK 21 JDK and verify `java -version` and `javac -version`.
- [ ] Install Jenkins and verify it with `systemctl status jenkins`.
- [ ] Configure the GCP firewall so the Jenkins UI is reachable.
- [ ] Retrieve the initial admin password from `/var/lib/jenkins/secrets/initialAdminPassword`.
- [ ] Install the required plugins, including Role-Based Authorization Strategy for the RBAC exercise.
- [ ] Create a Freestyle job and connect it to a GitHub repository.
- [ ] Configure the correct branch, such as `main`.
- [ ] Add build steps using `javac HelloWorld.java` and `java HelloWorld`.
- [ ] Read Console Output and troubleshoot a failed build.
- [ ] Configure Poll SCM using `* * * * *` for the demo.
- [ ] Push a new commit and verify that Jenkins detects the change.
- [ ] Practice cron expressions such as `0 8 * * 1-5`.
- [ ] Create a user and assign permissions through a role.
- [ ] Review the basic Maven commands: `mvn validate`, `mvn test`, `mvn install`, and `mvn clean package`.

**Interview tip:** While answering scenario questions, explain your troubleshooting sequence: identify the error in Console Output, check the relevant configuration or dependency, fix the root cause, and rerun the job to verify the result.
