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
