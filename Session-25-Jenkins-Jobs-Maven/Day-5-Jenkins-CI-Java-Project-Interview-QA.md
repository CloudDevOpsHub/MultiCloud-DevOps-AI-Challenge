## Key Outcomes

The session focused on setting up Jenkins on Google Cloud Ubuntu VMs, integrating it with a GitHub-hosted Java Spring Boot e-commerce project, and building/deploying the application using Maven. Students successfully installed Jenkins, configured freestyle jobs to pull code from GitHub, installed Maven, and ran `mvn install` and `mvn test` commands to compile code, execute unit tests, and produce a JAR artifact. The application was then manually deployed and run on both Ubuntu (Linux) and Windows environments on port 8083, and an experimental AI-assisted deployment to an "Embargo" platform was demonstrated using GitHub Copilot agent mode.

---

## Decisions Made

- **Jenkins** confirmed as the primary CI tool for the course due to its open-source nature and extensive plugin ecosystem. 
- **Ubuntu 24.04 LTS** selected as the operating system for all VM instances, as the LTS version includes pre-installed required tools (Git, etc.). 
- **JDK 21** chosen for Java installation on the Ubuntu VM. 
- **Maven (****`mvn install`** **+** **`mvn test`****)** used as the build commands in the Jenkins freestyle job's execute shell step, rather than a single `mvn package`, to demonstrate the process in smaller steps. 
- **Port 8083** designated for running the Spring Boot application to avoid conflict with Jenkins running on port 8080. 
- Manual deployment demonstrated first (freestyle job); **Jenkins pipeline** (declarative) deferred to the next session. 

---

## Topics Covered

### Jenkins Installation on Ubuntu (Google Cloud VM)

- Students created a new GCP VM instance with Ubuntu 24.04 LTS and 30 GB disk, with HTTP/HTTPS network access allowed. 
- Installation sequence: `sudo -i` → `sudo apt-get update -y` → `sudo apt-get upgrade -y` → install JDK 21 → run all Jenkins installation commands in one shot from the Jenkins Ubuntu documentation. 
- Jenkins accessed via `<external-IP>:8080`; initial admin password retrieved from `/var/lib/jenkins/secrets/initialAdminPassword`. 
- **Install Suggested Plugins** selected on first launch, installing approximately 90 plugins automatically. 
- Admin credentials set as `ADMIN` / `admin@gmail.com` to complete setup. 

### Java, Spring Boot, and Maven — Concepts Review

Students were given 3 minutes to research and then explain each technology:

- **Java:** Object-oriented programming language maintained by Oracle; used for enterprise-level backend (and frontend) applications; widely accepted across industries; follows OOP concepts and is highly reliable and secure. Notable: Oracle has made Java chargeable for enterprise use (no longer purely open source). 
- **Spring Boot:** A framework for Java-based web applications that provides ready-made features — embedded Tomcat/Jetty server (no separate Apache/Nginx needed), dependency injection, HTTP request handling, authentication/security, database connectivity, profiling across environments (dev/test/prod), and microservices support. Widely used in banking applications. 
- **Maven:** A build automation and project management tool for Java applications. Downloads external libraries/dependencies from **Maven Central** based on what is declared in `pom.xml`; compiles `.java` files to `.class` files; runs unit tests; packages the output as a **JAR or WAR artifact**. 

### Project Workflow: Developer Code → Jenkins Build → Artifact

The complete manual CI flow explained: 

1. Developer writes code in `.java` files under the `src/main` directory.
2. `mvn clean` — deletes the `target` folder (compiled output).
3. `mvn test` — executes unit test cases written under `src/test`.
4. `mvn install` / `mvn package` — reads `pom.xml`, downloads dependencies from Maven Central, compiles `.java` → `.class` files, runs tests, and packages everything into a **JAR/WAR file** in the `target/` folder.
5. JAR file run with: `java -jar <filename>.jar --server.port=8083`. 

### Jenkins Freestyle Job — GitHub Integration

- **Job 1 (Integration test):** Created a freestyle job ("My First Java Project"), pasted the GitHub repo URL (`github.com/CloudDevOpsHub` → Java Spring Boot e-commerce repo), set branch to `main`, and built — result: Jenkins successfully cloned the repo (GitHub ↔ Jenkins integration confirmed). 
- **Job 2 (Build):** Added a **Build Step → Execute Shell** with commands `mvn install` and `mvn test`. 
    - First run **failed** because Maven was not installed on the VM. 
    - Fix: `sudo apt install maven` (or `apt install mvn`) run on the VM, then pipeline re-triggered. 
    - Second run **succeeded**: Maven downloaded all dependencies from `repo.maven.apache.org` (Maven Central), created a `SNAPSHOT` artifact as declared in `pom.xml`, executed unit tests, and produced the JAR file. 

### Artifact Location and Manual Deployment

- Artifact stored at: `/var/lib/jenkins/workspace/<job-name>/target/` 
- File name example: `shopping-cart-0.0.1-SNAPSHOT.jar` (~53.7 MB). 
- Run command: `java -jar shopping-cart-0.0.1-SNAPSHOT.jar --server.port=8083` 
- Application successfully started and verified via browser at `<IP>:8083`. 
- **Cross-platform confirmation:** The same JAR file (built on Linux) ran successfully on **Windows** as well — demonstrating Java's platform independence. 
    - Windows steps: download JAR → create a `bin` folder → open CMD → run `java -jar <filename>.jar --server.port=8083`. 
- Spring Boot's default port is **8080**; explicit `--server.port` flag used to override. 

### AI-Assisted Deployment (Embargo Platform — Experimental Demo)

- Vikas demonstrated deploying the same Spring Boot application to an open-source hosting platform ("Embargo") using **GitHub Copilot agent mode** with a single prompt. 
- The agent autonomously: read the codebase, identified configuration issues (port not assigned), modified source code, ran Maven build, and deployed the application. 
- Students were advised this approach cannot be fully explained in interviews without understanding the underlying technology, but it demonstrates the direction of **Agentic AI in DevOps**. 
- Vikas indicated he would explore making the Embargo platform free/accessible for all students to deploy portfolios. 

---

## Blockers Requiring Action

- **Maven not found error** during first pipeline run → resolved by running `sudo apt install maven` on the VM before re-triggering the build. 
- **MVN spelling typo** in Jenkins execute shell (`mvn` mistyped) caused pipeline failure for at least one student (Rajesh) → resolved by correcting spelling in the configure step. 
- **JAR file path errors** when running manually — students used incorrect paths (e.g., `target.jar` instead of `target/shopping-cart-0.0.1-SNAPSHOT.jar`) → resolved by navigating to `/var/lib/jenkins/workspace/<job>/target/`, running `ls` to find the exact filename, then executing the `java -jar` command. 
- **Jenkins not accessible at** **`<IP>:8080`** for some students (Shrikant, Anuj) → root causes: firewall port 8080 not enabled in GCP, wrong IP used (internal vs. external), or Jenkins service not running. Resolution steps: verify external IP via `curl ifconfig.me`, check Jenkins service status (`systemctl status jenkins`), enable port in GCP firewall. 
- **GCP billing/account issues:**
    - Dharmendra: UPI payment failing for GCP verification → advised to use credit/debit card (Visa preferred); 2-rupee charge is only for verification. 
    - Pankaj: Trial period expired, VM deleted, billing project locked → advised to raise a GCP support request to delete the VM from backend, then re-enable the API and billing. 
    - Ravi Gadi: Gmail MFA loop preventing GCP login → not a GCP issue; advised to raise a Gmail support ticket or use a different Gmail account. 

---

## Pending Confirmation

- Embargo platform free-tier access for all students — Vikas to negotiate/confirm. 
- Jenkins **declarative pipeline** session scheduled for the next class (Day 29 / Friday). 
- Students with unresolved Jenkins/GCP issues to self-troubleshoot and follow up. 

---

## Key Concepts Reinforced

- **CI vs. Traditional approach:** Everything automated with CI — code push triggers compilation, testing, and artifact creation without manual intervention. 
- **pom.xml** is the configuration file Maven reads to determine version, dependencies, plugins, artifact name, and packaging type. 
- **`target/`** **folder** is created by Maven after a successful build and contains the compiled `.class` files and final JAR/WAR artifact. 
- **Java is platform-independent** because `.java` source is compiled to `.class` bytecode (machine language via JVM interpreter), allowing the same JAR to run on Linux, Windows, and Mac. 
- **Artifacts** are the packaged/deployable version of an application — analogous to a `.exe` file on Windows. 
- **Log4j** dependency downloaded during build = logging framework for application logs. 
- **SSH** = Secure Shell; used to remotely connect to Linux servers on **port 22**. 
- As a **DevOps engineer**, the role is to compile, build, and run code via Jenkins pipelines — not to write application code (that is the developer's responsibility). 

---

## Action Items

- **All students:** Delete and recreate VMs after class for practice; repeat Jenkins installation multiple times to build confidence for interviews. 
- **All students:** Post a LinkedIn update describing today's work — building and deploying a Java Spring Boot application from scratch via Maven, manually on Linux/Windows, and via AI on Embargo. 
- **All students:** Explore the GitHub repo (`github.com/CloudDevOpsHub` — Java Spring Boot e-commerce) and run the pipeline independently. 
- **Students with GCP issues (Dharmendra, Pankaj, Ravi Gadi):** Follow specific resolution paths outlined above (card payment, support ticket, Gmail support) before next session.
- **Vikas:** Investigate making Embargo platform accessible/free for student portfolio deployments. 
- **Vikas:** Conduct Jenkins declarative **pipeline** session next class (Friday, Day 29). 

---

## Open Questions

- How to design a **reusable Jenkins pipeline for 40 microservices**? (Raised by Shashwat — deferred to pipeline session.) 
- How to run **multiple Jenkins stages in parallel**? (Raised by Shashwat — deferred.) 
- How to **recover deleted files in Linux** if `temp`/trash was not explicitly enabled? Answer: permanent deletion unless explicitly backed up. 
- Can the OS configuration of a GCP VM be changed after creation? (Raised by Anuj — not fully resolved.)


---

# Interview Preparation: 20 Questions and Answers

These questions are based on the Day-5 topics: Jenkins installation, GitHub integration, Java, Spring Boot, Maven, CI, build artifacts, and manual deployment.

### 1. What is Jenkins, and why is it used in DevOps?
**Answer:** Jenkins is an open-source automation server used to automate tasks such as code integration, building, testing, and deployment. In this session, Jenkins pulled Java code from GitHub and executed Maven commands to build and test the application.

### 2. What is Continuous Integration (CI)?
**Answer:** Continuous Integration is the practice of frequently integrating code changes into a shared repository and automatically validating them through builds and tests. It helps detect issues early instead of waiting for a manual release process.

### 3. What is a Jenkins Freestyle project?
**Answer:** A Freestyle project is a basic Jenkins job that can be configured through the Jenkins UI. We can configure source-code management, build triggers, and build steps such as Execute Shell. In this session, a Freestyle job cloned the GitHub repository and ran Maven commands.

### 4. How do you connect Jenkins to a GitHub repository?
**Answer:** Create a Jenkins job, select Git under Source Code Management, provide the repository URL, and specify the branch, such as `main`. Configure credentials if the repository requires authentication, then save and run the job to verify that Jenkins can clone the code.

### 5. What is Maven?
**Answer:** Maven is a Java build automation and project management tool. It reads project configuration from `pom.xml`, resolves dependencies, compiles source code, runs tests, and packages the application as a JAR or WAR file.

### 6. What is the purpose of `pom.xml`?
**Answer:** `pom.xml` is Maven's Project Object Model file. It contains project details, dependencies, versions, build plugins, and packaging configuration. Maven uses it to understand how the project should be built.

### 7. What is the difference between `mvn clean`, `mvn test`, and `mvn install`?
**Answer:**
- `mvn clean` removes the previous build output, usually the `target/` directory.
- `mvn test` compiles the test code and executes the unit tests.
- `mvn install` runs the build lifecycle through packaging and installs the resulting artifact into the local Maven repository. It also runs earlier lifecycle phases, including tests, unless configured otherwise.

### 8. What is the difference between `mvn package` and `mvn install`?
**Answer:** Both run the build lifecycle through the package phase. `mvn package` creates the packaged artifact, such as a JAR, in `target/`. `mvn install` goes one phase further and also places the artifact in the local Maven repository so other local Maven projects can use it.

### 9. What is Maven Central?
**Answer:** Maven Central is a public repository that hosts Java libraries and dependencies. When a dependency is declared in `pom.xml`, Maven can download it from Maven Central or another configured repository.

### 10. What is the `target/` directory?
**Answer:** It is the standard Maven build-output directory. It commonly contains compiled classes, test reports, and the packaged JAR or WAR generated by the build.

### 11. What is a JAR file?
**Answer:** JAR stands for Java Archive. It packages compiled Java classes and related resources into a single file. A Spring Boot executable JAR can include the application and its required dependencies and can be started with the Java runtime.

### 12. What is Spring Boot?
**Answer:** Spring Boot is a framework built on the Spring ecosystem that simplifies creating Java applications. It provides auto-configuration, dependency injection, and embedded web-server support, making it easier to build and run web applications.

### 13. What is the role of an embedded Tomcat server in Spring Boot?
**Answer:** An embedded Tomcat server allows a Spring Boot web application to run with its web server packaged/configured as part of the application. For a typical executable JAR, we do not need to install and configure a separate Tomcat server just to start the application.

### 14. How do you run a Spring Boot JAR file?
**Answer:** Use the command `java -jar application.jar`. To run it on a specific port, use `java -jar application.jar --server.port=8083`.

### 15. Why was port 8083 used in this session?
**Answer:** Jenkins was already using port 8080 on the VM. The Spring Boot application was configured to use port 8083 to avoid a port conflict.

### 16. How can you check whether Jenkins is running on Ubuntu?
**Answer:** Run `sudo systemctl status jenkins`. If it is not running, inspect the service logs and try `sudo systemctl start jenkins`. You can also use `sudo systemctl enable jenkins` to configure it to start automatically at boot.

### 17. Where can you find the initial Jenkins administrator password?
**Answer:** On the Ubuntu VM, the initial password is stored at `/var/lib/jenkins/secrets/initialAdminPassword`. An administrator with suitable permissions can read it using `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

### 18. Where was the JAR generated by the Jenkins job stored?
**Answer:** In the job's workspace, under the Maven output directory. In this session, the example path was `/var/lib/jenkins/workspace/<job-name>/target/`.

### 19. Why can the same Java JAR run on Linux and Windows?
**Answer:** Java source code is compiled into platform-independent bytecode. A compatible Java Virtual Machine (JVM) on each operating system executes that bytecode. The target machine still needs a compatible Java runtime and any required external configuration.

### 20. What is the difference between CI and deployment?
**Answer:** CI focuses on integrating code and validating it through automated build and test steps. Deployment is the process of making the built application available in a target environment. In this session, Jenkins performed the build and tests, while the JAR was then run manually on Linux and Windows.

---

# Scenario-Based Interview Questions and Answers

### 1. Your Jenkins job fails with `mvn: command not found`. How would you fix it?
**Answer:** First, confirm that Maven is installed on the machine where the Jenkins job runs. Run `mvn -version` on that VM. If it is missing, install it with `sudo apt update && sudo apt install maven -y`, then verify the command again. Re-run the Jenkins job. Also check the Jenkins service user's `PATH` if Maven is installed but is still not found by the job.

### 2. Jenkins cannot clone your GitHub repository. What would you check?
**Answer:** Check the repository URL, branch name, network connectivity, and repository visibility. If the repository is private, configure valid GitHub credentials in Jenkins and select those credentials in the job's Git configuration. Review the Jenkins console output for the exact authentication or network error.

### 3. Your Jenkins job reports a successful Git checkout, but the Maven build fails. What is your troubleshooting approach?
**Answer:** Open the Jenkins console output and identify the first meaningful Maven error. Verify Java and Maven versions, inspect `pom.xml`, confirm dependency availability, and check whether the source code compiles locally. Fix the root cause and run the job again.

### 4. The Jenkins build fails because of a typo in the command. How do you prevent this?
**Answer:** Read the console output and compare the command in the Jenkins job configuration with the intended command. Correct the spelling, save the job, and run it again. For repeatable builds, move commands into a version-controlled build script or Jenkinsfile and review changes before committing.

### 5. Maven build succeeds, but you cannot find the JAR. What would you do?
**Answer:** Check the Jenkins job's workspace and navigate to `target/`. Run `ls -lh` to list the generated files. Confirm the packaging type and artifact name in `pom.xml`, and inspect the Maven console output for the build result. Do not assume the JAR has a fixed filename.

### 6. A student runs `java -jar target.jar` and gets a file-not-found error. How would you guide them?
**Answer:** Explain that `target/` is a directory, not usually the JAR filename. Ask them to enter the project directory, run `ls -lh target/`, identify the actual JAR name, and run `java -jar target/<actual-file-name>.jar`.

### 7. The application starts, but users cannot open `<external-IP>:8083`. What would you check?
**Answer:** Confirm that the application started successfully and is listening on port 8083. Check the application logs and verify the VM's firewall or cloud firewall rule allows inbound TCP traffic on port 8083. Also confirm that users are using the VM's external IP and the correct port.

### 8. Jenkins is not accessible at `<external-IP>:8080`. How would you troubleshoot it?
**Answer:** Check `sudo systemctl status jenkins` and inspect Jenkins logs if needed. Confirm that the VM has an external IP, that the correct external rather than internal IP is being used, and that the cloud firewall allows inbound TCP traffic on port 8080. Verify that Jenkins is listening on the expected port.

### 9. Jenkins and the Spring Boot application both need to run on the same VM. They are conflicting on port 8080. What would you do?
**Answer:** Keep Jenkins on its configured port 8080 and start the Spring Boot application on another available port, such as 8083, using `--server.port=8083`. Ensure the cloud firewall allows the application port if it needs to be reached externally.

### 10. Your Maven build works on your laptop but fails on the Jenkins VM. What could be different?
**Answer:** Compare Java and Maven versions, environment variables, operating-system dependencies, repository access, credentials, and project configuration. Run `java -version` and `mvn -version` in both environments. Make the build reproducible by documenting tool versions and configuring the required tools consistently.

### 11. A dependency download fails during the Maven build. What would you investigate?
**Answer:** Check the dependency coordinates and version in `pom.xml`, network connectivity from the VM, DNS/proxy settings, and Maven repository availability. Review the exact Maven error. If a repository is temporarily unavailable, retry after confirming connectivity; do not randomly change dependency versions.

### 12. The build is successful, but unit tests are failing. What should happen next?
**Answer:** Treat the test failure as a quality gate. Review the test reports and console output, identify the failing test, and share the failure with the developer responsible for the code. Do not mark the change as ready for release just because compilation or packaging completed. Fix the issue and rerun the tests.

### 13. A developer pushes new code to the `main` branch. How can Jenkins build the latest version?
**Answer:** Configure a suitable build trigger, such as a GitHub webhook or SCM polling, and ensure the job tracks the correct repository and branch. When the trigger runs, Jenkins checks out the latest commit and executes the configured build and test steps. Verify the commit shown in the console output.

### 14. You need to deploy the same JAR to both Linux and Windows. What should you verify?
**Answer:** Verify that both machines have a compatible Java runtime, that the JAR was built correctly, and that required environment variables, configuration, and external services are available. Use the appropriate command-line syntax on each OS and ensure the selected application port is free.

### 15. The application runs locally but exits immediately on the VM. How would you investigate?
**Answer:** Run the JAR from a terminal and read the startup error. Check the Java version, application configuration, environment variables, port availability, file permissions, and connectivity to external services such as databases. Use the application logs to identify the actual cause before changing settings.

### 16. Your Jenkins job works when you run commands manually as your own user, but fails in Jenkins. Why?
**Answer:** Jenkins usually runs jobs under the Jenkins service account, which may have a different `PATH`, home directory, permissions, environment variables, and access to files. Check the job's environment and permissions, and configure required tools and credentials for the Jenkins service account rather than relying on a personal shell environment.

### 17. The `target/` folder was removed, and you are worried that the source code is lost. What would you explain?
**Answer:** `target/` is generated build output. Removing it with `mvn clean` does not normally delete source code stored in `src/`. The project source should remain in the workspace or Git repository. If source files themselves were deleted, restore them from version control or a backup.

### 18. A Jenkins build succeeds, but the team cannot identify which source revision produced the JAR. How would you improve traceability?
**Answer:** Record the Git commit ID in the build output, use Jenkins build numbers, and archive the generated artifact with a clear version or build identifier. This makes it easier to connect a deployed artifact to the exact source revision and build.

### 19. You are asked to use GitHub Copilot agent mode to deploy the application. What should you explain in an interview?
**Answer:** Explain that an AI agent can help inspect a codebase, suggest or modify configuration, run build commands, and assist with deployment. However, the engineer must understand the application, build process, permissions, deployment target, logs, and security implications. Review and validate the agent's changes rather than trusting an automated action blindly.

### 20. Your team now has 40 Java microservices. Should you create 40 unrelated Jenkins jobs with duplicated configuration?
**Answer:** Avoid unnecessary duplication. Use a reusable pipeline approach with shared pipeline logic or a common Jenkins library, while keeping service-specific configuration separate. Standardize build, test, artifact, and deployment stages. Decide whether services can build in parallel based on dependencies and available Jenkins agents. Validate the design with the team before applying it across all services.

---

## Practical Revision Checklist

Use this checklist to practise the Day-5 workflow without depending on the instructor:

- [ ] Create an Ubuntu 24.04 LTS VM and verify network access.
- [ ] Install JDK 21 and verify it with `java -version`.
- [ ] Install Jenkins and verify the service with `sudo systemctl status jenkins`.
- [ ] Open Jenkins on port 8080 and complete the initial setup.
- [ ] Create a Freestyle job and connect it to the Java project on GitHub.
- [ ] Install Maven and verify it with `mvn -version`.
- [ ] Configure the job to run `mvn install` and `mvn test`.
- [ ] Read the Jenkins console output and confirm the build result.
- [ ] Locate the generated JAR under the job workspace's `target/` directory.
- [ ] Run the JAR on port 8083 using `java -jar`.
- [ ] Verify the application through a browser and check firewall rules if needed.
- [ ] Practise running the same JAR on a Windows machine with a compatible Java runtime.
- [ ] Explain each step clearly, including one failure you encountered and how you fixed it.

## Important Interview Note

The questions above are practice material based on the session notes. Some answers add standard troubleshooting and CI/CD context to make the scenarios useful in interviews. Be ready to explain the commands you actually ran, the errors you saw, and how you verified each fix.
