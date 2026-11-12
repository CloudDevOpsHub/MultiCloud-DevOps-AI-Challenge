## Key Outcomes

The session provided a comprehensive introduction to GitLab as a **DevSecOps platform**, covering its architecture, comparison with GitHub and Jenkins, and hands-on pipeline creation. Students created GitLab accounts, set up self-hosted runners on local Windows machines, and deployed a static HTML portfolio website through an automated CI/CD pipeline triggered by code commits. The instructor emphasized that GitLab's primary advantage is its **integrated, ready-made CI/CD capability** requiring only a single `.gitlab-ci.yml` file, contrasting with Jenkins which requires extensive plugin configuration.

---

## Decisions Made

- **Self-hosted runners** are the recommended approach for students without credit card verification, avoiding GitLab's shared runner restriction on new unverified accounts. 
- The **`.gitlab-ci.yml`** file must always retain this exact filename — no alternative naming is supported. 
- **Shell executor** selected as the runner executor type for Windows-based local machines during the session. 
- GitLab terminology must be learned distinctly: **repo → project**, **organization → group**, **PR → MR (Merge Request)**, **GitHub Actions → GitLab CI/CD pipeline**. 
- For interview preparation, students should claim at least one GitLab use case even if self-learned, as interviewers increasingly ask about GitLab alongside Jenkins. 

---

## GitLab vs. GitHub vs. Jenkins: Core Comparisons

### What is GitLab?

- A **single DevSecOps platform** (UI-based) for planning, building, securing, and delivering software. 
- Approximately **10% market share** for CI/CD usage; GitHub dominates (~95%) for code management/collaboration. 
- Offers **source code management + integrated CI/CD + security scanning** in one platform — eliminating the need to separately configure Jenkins. 

### GitLab vs. GitHub

|      Dimension       |                GitHub                 |                  GitLab                  |
|----------------------|---------------------------------------|------------------------------------------|
| Primary purpose      | Code hosting & collaboration          | Integrated DevSecOps platform            |
| CI/CD                | Requires GitHub Actions (chargeable)  | Built-in, triggered via `.gitlab-ci.yml` |
| Pipeline config file | `.github/workflows/*.yml`             | `.gitlab-ci.yml`                         |
| Execution agents     | GitHub-hosted or self-hosted runners  | Self-managed runners                     |
| Community size       | Very large (~95% code management use) | Smaller community                        |
| Free tier            | Generous for public/private repos     | 5 users free; 400 compute minutes/month  |
| Terminology          | Repo, Org, PR                         | Project, Group, MR                       |

### GitLab vs. Jenkins

- **Jenkins**: 2,300+ plugins, fully customizable, open-source, standalone automation server — any integration is possible. 
- **GitLab**: 20–50 ready-made integrations; enterprise features require purchase; less customizable but faster to set up. 
- **Key insight from Naren (10 years GitLab experience)**: In many organizations, the stack becomes **GitLab + webhook + runner + Jenkins** together — Jenkins doesn't disappear even when GitLab is adopted. 
- Jenkins remains **completely free**; GitLab CI/CD is not free beyond limits. 

---

## GitLab Architecture & Key Concepts

### Structural Terminology

- **Group** = Organization (equivalent to GitHub org) — can contain subgroups. 
- **Project** = Repository — where code lives and CI/CD is configured. 
- **Subgroup** = A group nested within a group. 
- **Members & Roles** exist at both group and project levels. 
- **Issue Board** = GitHub Projects equivalent for task management. 
- **Branch protection rules**, **package registries**, and **security/compliance** features use the same keywords as GitHub. 

### Visibility Levels for Projects

- **Public**: Accessible to everyone.
- **Internal**: Accessible to all members of the team/group.
- **Private**: Explicitly granted access only.
- Note: Public and Internal options may be unavailable without account verification (credit card required to unlock). 

### The CI/CD Pipeline File

- Filename: **`.gitlab-ci.yml`** (exact spelling mandatory; spelling errors prevent template suggestions from appearing). 
- Located in the **root directory** of the project/repository. 
- Contains **stages**, **jobs**, **scripts**, **artifacts**, **environment setup**, **deployment rules**, and **conditions**. 
- Ready-made templates are available within the GitLab UI when creating this file — covering different technology stacks and use cases. 

---

## Runners: Architecture & Setup

### What Are Runners?

- Runners are the **CPU/memory resources** that execute pipeline jobs. 
- Equivalent to **agents/slaves in Jenkins**. 
- Two types: 
    - **Shared runners**: Provided by GitLab (free tier); require credit card for new accounts due to past exploitation abuse. 
    - **Self-hosted runners**: Installed on your own machine (laptop, EC2, bare metal, EKS) — no credit card required. 

### Why GitLab Now Requires Credit Card for Shared Runners

- Previously, GitLab offered free shared runners without verification. 
- Users exploited this by creating multiple accounts to bypass limits. 
- GitLab now requires credit card for identity verification — **not for billing** — before granting shared runner access. 
- **Alternative**: Set up a self-hosted runner to bypass this entirely. 

### Three-Step Runner Setup Process (Windows)

Manoj summarized the process clearly during the session: 

1. **Create the runner** in GitLab UI:
    - Navigate to: Project → Settings → CI/CD → Runners → Create Project Runner. 
    - Select OS (Windows/Mac/Linux), generate a **PAT token** (Personal Access Token) for authentication. 
2. **Register the runner** on the local machine:
    - Open **PowerShell as Administrator** (critical — access denied errors occur without admin rights). 
    - Copy the download command from GitLab ("How do I install GitLab Runner" tab). 
    - Run the command → downloads `gitlab-runner.exe` to a local folder (e.g., `C:\gitlab-runner\`). 
    - Run the registration command → prompted for: GitLab instance URL, token, runner name, executor type (enter `shell`). 
    - The token authenticates the local machine to GitLab — analogous to `aws configure` for Terraform. 
3. **Start the runner**:
    - Run the start command; runner appears as **online (green)** in GitLab UI under Settings → CI/CD → Runners. 
    - Runner must be **online before pipeline execution** — pipelines fail if no runner is available. 

### Common Runner Errors & Resolutions

- **"Access denied"**: PowerShell not run as Administrator → right-click → Run as Administrator. 
- **"Fatal: failed to install"**: Same cause — admin rights missing. 
- **Token registration step skipped**: Pipeline cannot authenticate → must complete all 3 steps in sequence. 
- **"Configuration pooling issues / bottleneck"**: Warning only, not an error — can be ignored. 
- **Pipeline fails even with runner online**: Delete the `.gitlab-ci.yml` file and recreate from scratch; ensure runner is online *before* the pipeline file is committed. 
- **Runner already exists folder error**: Folder from previous install exists — delete and reinstall. 

---

## Hands-On: Portfolio Website Deployment via GitLab CI/CD

### What Was Built

- A **static HTML portfolio website** generated via ChatGPT, committed to a GitLab project, and automatically deployed using GitLab Pages through a CI/CD pipeline. 

### Step-by-Step Process Demonstrated

1. Create a GitLab account at `gitlab.com` → verify email → set role as **DevOps Engineer** → select "Explore GitLab". 
2. Create a new **blank project** (named "portfolio") → set visibility. 
3. Add `index.html` via the GitLab UI (+ button → New File) → paste ChatGPT-generated HTML → commit to `main` branch. 
4. Create `.gitlab-ci.yml` file → paste pipeline code → commit. 
5. As soon as commit happens → **pipeline triggers automatically** — no manual trigger needed. 
6. Pipeline runs the job → console output visible (equivalent to Jenkins console output). 
7. Deployed site accessible via GitLab Pages URL under Deploy → Pages. 

### Demonstrating CI/CD in Action

- Instructor changed a name in `index.html` (replaced "Vikas" with "Jishan") using Ctrl+F → re-committed. 
- Pipeline re-triggered automatically → new version deployed → URL updated. 
- This demonstrated the core CI/CD loop: **code change → auto pipeline → new deployment**. 

---

## Pipeline Code Explained

The `.gitlab-ci.yml` used in the session contained two stages, explained by student Mandhir: 

```yaml
stages:
  - build
  - deploy

deploy_website:
  stage: deploy
  script:
    - mkdir public
    - cp index.html public/
  artifacts:
    paths:
      - public
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

- **`mkdir public`**: Creates a `public/` directory — GitLab Pages serves content from this folder. 
- **`cp index.html public/`**: Copies the HTML file into the public folder for serving. 
- **`rules`** **condition**: Pipeline only executes when a commit is made to the `main` branch — branch name is configurable per company convention. 
- If the branch differs (e.g., `master`, `develop`), update the condition accordingly. 

---

## DevSecOps Features: SAST & Secret Detection

GitLab positions itself as a DevSecOps platform through two built-in security options available during project creation: 

- **SAST (Static Application Security Testing)**: Scans source code statically for vulnerabilities — equivalent to SonarQube for code-level analysis. 
- **Secret Detection**: Scans for accidentally committed secrets — passwords, SSH keys, tokens, API keys. 
- Enabling either option **auto-generates additional stages** in the `.gitlab-ci.yml` file, calling GitLab's built-in security templates. 
- **DAST (Dynamic Application Security Testing)**: Also available — scans running applications dynamically, distinct from SAST which is static/code-level. 
- **Trivy**: Used for **container image scanning** (not application code) — a separate concept from SAST/DAST. 

---

## GitLab Advantages & Disadvantages

### Advantages

- **Single platform** for code management, CI/CD, security, and collaboration — no tool switching. 
- **Automated by default**: Pipeline triggers on commit without additional setup. 
- **Unlimited private repositories** on free tier. 
- **Self-hosted option**: Full control when installed on own infrastructure (Helm install available for Kubernetes). 
- **GitOps support**: Read-only repositories can be hosted for GitOps workflows in production EKS/bare metal clusters. 
- **Ready-made Docker images** with pre-installed dependencies available as runner environments. 
- **Ready-made CI/CD templates** for different technology stacks built into the UI. 

### Disadvantages

- **Smaller community** → slower issue resolution, fewer community answers. 
- **Limited integrations** (~20–50 ready-made vs. Jenkins' 2,300+); custom use cases may not be fulfillable. 
- **Complicated UI**: Became significantly more complex in recent versions. 
- **Heavy hardware** required to run runners effectively. 
- **New feature instability**: Upgrading features can break production environments; fixes depend on GitLab's timeline. 
- **Enterprise features**: Advanced functionality (compliance, governance) requires paid plans. 
- **Shared runners now require credit card** for new accounts. 
- **Managed bugs**: Customer-reported bugs take a long time to resolve due to external dependency. 

---

## GitLab vs. Azure DevOps

Briefly addressed in response to a student question: 

- Azure DevOps and GitLab offer **similar integrated experiences** — both provide source code management + CI/CD in one platform.
- Azure DevOps pipeline config file: **`azure-pipeline.yml`**; GitLab: **`.gitlab-ci.yml`**. 
- Azure DevOps uses **agents**; GitLab uses **runners** — conceptually equivalent. 
- Azure DevOps is tightly integrated with the Microsoft/Azure ecosystem; GitLab is cloud-agnostic. 
- The instructor noted that mastering one platform's pipeline concepts makes the other easier to learn. 

---

## When to Use GitLab vs. GitHub

- **Choose GitHub** if: focus is open-source collaboration, developer-friendly workflows, or the team is already deeply integrated with GitHub. 
- **Choose GitLab** if: need a self-managed SCM with integrated CI/CD, want to avoid tool switching, require DevSecOps workflows, or need GitOps for production deployments. 
- **Interview strategy**: Always mention at least one GitLab use case — even self-learning projects count. Interviewers increasingly ask about GitLab alongside Jenkins and Bitbucket. 

---

## Q&A Highlights

- **Q: Can a self-hosted runner be used without credit card?**
A: Yes — install the runner on any local machine or EC2 instance; no verification needed. 
- **Q: How does rollback work in GitLab?**
A: Use commit IDs or tags to reference previous builds; a dedicated demo was deferred to a future session. 
- **Q: What is the difference between CI and CD?**
A: CI = automated build + test (application creation). CD = automated deployment/delivery to an environment. Both can be separate or combined in one pipeline. 
- **Q: Runner is online but pipeline still fails — why?**
A: Likely cause is that the pipeline file was committed *before* the runner was online, or the runner was not assigned to the project. Delete the pipeline file and recommit after confirming runner is green. 
- **Q: IP address changes — does data remain?**
A: IP and data are independent. If IP changes, the application runs on the new IP; data remains unchanged. 
- **Q: How does load balancing work in EKS?**
A: Use an **Ingress controller** (e.g., NGINX) — operates at Layer 4 or Layer 7. Direct public IP load balancing in Kubernetes is costly; Ingress acts as the load balancer for microservices. 
- **Q: Can the same GitHub account be used to sign into GitLab?**
A: Yes, GitLab supports sign-in via GitHub OAuth; avoid using official company email IDs. 

---

## Pending Confirmation

- Students with unverified accounts need to either: (a) provide credit card for shared runners, or (b) complete the 3-step self-hosted runner setup to run pipelines. 
- Students who received pipeline errors should delete the `.gitlab-ci.yml` file, ensure runner is online, and recommit. 
- Monday session: **holiday/leave** — instructor to send group message confirming. 
- 30-day free trial available on [GitLab.com](https://gitlab.com) for students wanting to explore premium features. 

---

## Action Items

- **All students**: Create GitLab account, create a portfolio project, commit an HTML page, set up a self-hosted runner, and trigger the CI/CD pipeline. 
- **All students**: Sync and optimize LinkedIn profiles — post regularly to increase recruiter visibility. 
- **Instructor (Vikas)**: Plan a monthly hackathon with a problem statement, one-week build time, presentation, and peer voting for winners. 
- **Instructor**: Deliver a future session with Python/Flask or Java-based application through GitLab CI/CD pipeline (same process, different code). 
- **Instructor**: Demo rollback using commit IDs/tags in a future session. 
- **Students with errors**: Screenshot errors and share via the designated channel (not WhatsApp) for troubleshooting. 
- **Instructor**: Announce top 5 leaderboard winners next session; move from 20 submissions to target of more participants.

# GitLab CI/CD, Runners & Jenkins — Interview Preparation
## Batch-45 | Day-8 of DevOps | 6 November 2026

This document is based on the Day-8 session notes. It covers GitLab fundamentals, architecture, runners, CI/CD pipelines, GitLab Pages, DevSecOps features, troubleshooting, and comparisons with GitHub, GitHub Actions, and Jenkins.

---

# Part 1: 20 Basic to Advanced Interview Questions & Answers

### 1. What is GitLab?
**Answer:** GitLab is a DevSecOps platform used to plan, build, secure, and deliver software. It provides source code management, collaboration, integrated CI/CD pipelines, and security-scanning capabilities in one platform.

### 2. What is the main difference between GitLab and GitHub?
**Answer:** GitHub is widely used for source-code hosting and collaboration. GitLab provides source-code management along with built-in CI/CD and security capabilities. GitHub can also provide CI/CD through GitHub Actions.

### 3. What is a GitLab project?
**Answer:** A project is the place where code, repository files, issues, and CI/CD configuration are managed. It is broadly equivalent to a repository in GitHub.

### 4. What is a GitLab group?
**Answer:** A group is a way to organize multiple projects and manage members and permissions centrally. It is similar to an organization in GitHub. A group can also contain subgroups.

### 5. What is a subgroup in GitLab?
**Answer:** A subgroup is a group nested inside another group. It helps organize projects and teams in a hierarchy.

### 6. What is a Merge Request (MR)?
**Answer:** A Merge Request is GitLab's workflow for proposing and reviewing code changes before merging them into another branch. It is similar to a Pull Request (PR) in GitHub.

### 7. What is the `.gitlab-ci.yml` file?
**Answer:** It is the YAML configuration file used to define GitLab CI/CD pipelines. It can contain stages, jobs, scripts, artifacts, environment setup, rules, and deployment configuration. The exact filename is `.gitlab-ci.yml`.

### 8. Where should `.gitlab-ci.yml` be placed?
**Answer:** It should be placed in the root directory of the project repository so GitLab can detect and use it for pipeline configuration.

### 9. What is a GitLab CI/CD pipeline?
**Answer:** A pipeline is an automated workflow that runs jobs in a defined sequence. It can build and test an application and then deliver or deploy it. In the session demo, a commit triggered a pipeline that deployed a static HTML portfolio through GitLab Pages.

### 10. What is the difference between a stage and a job?
**Answer:** A **stage** represents a phase of the pipeline, such as build or deploy. A **job** is a specific unit of work assigned to a stage and contains instructions such as commands under `script`. Multiple jobs can belong to the same stage.

### 11. What is a GitLab Runner?
**Answer:** A GitLab Runner is the agent or execution resource that picks up and runs pipeline jobs. It provides the CPU and memory resources needed to execute the job. It is conceptually similar to an agent or node in Jenkins.

### 12. What is the difference between shared and self-hosted runners?
**Answer:** Shared runners are provided and managed by GitLab. Self-hosted runners are installed and managed on infrastructure controlled by the team, such as a laptop, EC2 instance, or server. The session recommended self-hosted runners for students whose new accounts could not use shared runners without verification.

### 13. What is an executor in GitLab Runner?
**Answer:** An executor defines the environment in which a runner executes a job. In the Windows runner setup demonstrated in the session, the **Shell executor** was selected. It runs job commands through the machine's shell.

### 14. Why must a runner be online before a pipeline job executes?
**Answer:** A runner must be available and assigned to the project to pick up the job. If no eligible runner is available, the job may remain pending or fail to execute. The session emphasized confirming that the runner appears online (green) before committing the pipeline configuration.

### 15. What does the `rules` keyword do in `.gitlab-ci.yml`?
**Answer:** `rules` controls whether a job is added to a pipeline based on conditions. For example, the session pipeline used `$CI_COMMIT_BRANCH == "main"` so the deployment job ran for commits to the `main` branch.

### 16. What are artifacts in GitLab CI/CD?
**Answer:** Artifacts are files or directories produced by a job and retained by GitLab so they can be used by later jobs or made available after the pipeline. In the Pages example, the `public` directory was declared as an artifact.

### 17. How did GitLab Pages deployment work in the session?
**Answer:** The pipeline created a `public` directory, copied `index.html` into it, and published that directory as an artifact. GitLab Pages then served the static website. A new commit to the HTML file triggered the pipeline and deployed the updated version.

### 18. What is the difference between SAST, DAST, and secret detection?
**Answer:**
- **SAST (Static Application Security Testing):** Scans source code without running the application.
- **DAST (Dynamic Application Security Testing):** Tests a running application for security issues.
- **Secret detection:** Looks for accidentally committed sensitive values such as passwords, API keys, tokens, or SSH keys.

### 19. Is Trivy the same as SAST?
**Answer:** No. In the session, Trivy was discussed as a tool for container-image scanning. SAST focuses on static application source-code analysis. They address different scanning targets.

### 20. Can GitLab and Jenkins be used together?
**Answer:** Yes. The session highlighted that organizations may use GitLab for source-code management and collaboration while using webhooks, runners, and Jenkins together in the delivery workflow. Adopting GitLab does not automatically mean Jenkins disappears. The choice depends on existing integrations and requirements.

---

# Part 2: 20 Scenario-Based Interview Questions & Answers

### 1. Your GitLab pipeline is stuck in a pending state. What will you check?
**Answer:**
1. Open the project's **Settings → CI/CD → Runners**.
2. Confirm that a runner is online.
3. Confirm that the runner is assigned and eligible to run jobs for the project.
4. Check whether the runner is available for the job's tags and configuration.
5. Review the job status again after correcting the runner configuration.

**Why:** A pipeline job needs an eligible runner to execute.

### 2. You installed GitLab Runner on Windows, but registration returns “Access denied.” How will you fix it?
**Answer:** Open PowerShell using **Run as Administrator** and repeat the installation or registration command. The session identified missing administrator permissions as a common reason for access-denied and installation failures.

### 3. Your Windows runner is installed, but it does not appear online in GitLab. What will you do?
**Answer:**
1. Confirm that runner registration completed successfully.
2. Check that the GitLab instance URL and runner token were entered correctly.
3. Start the runner using the start command.
4. Refresh **Settings → CI/CD → Runners** and confirm that it is online.

### 4. A student cannot use GitLab shared runners because the account is not verified. What is the alternative?
**Answer:** Set up a self-hosted runner on a local machine or an EC2 instance. The session recommended this approach because it does not depend on shared-runner access verification.

### 5. Your pipeline does not detect the CI/CD configuration file. What could be wrong?
**Answer:** Verify that the file is named exactly `.gitlab-ci.yml` and is located in the project's root directory. A spelling mistake or a different filename can prevent GitLab from recognizing the pipeline configuration.

### 6. Your pipeline runs on `main`, but not on `develop`. What will you inspect?
**Answer:** Inspect the `rules` condition in `.gitlab-ci.yml`. If it checks `$CI_COMMIT_BRANCH == "main"`, the job is restricted to the `main` branch. Update the condition to match the intended branch or define rules for both branches.

### 7. Your deployment job succeeds, but the GitLab Pages website does not show the HTML page. What will you check?
**Answer:**
1. Confirm that `index.html` exists in the repository.
2. Confirm that the job creates the `public` directory.
3. Confirm that the file is copied to `public/index.html`.
4. Confirm that `public` is declared under `artifacts.paths`.
5. Check the GitLab Pages deployment status and URL.

### 8. You changed the portfolio page, committed the change, but the website still shows the old content. How will you troubleshoot?
**Answer:** Check whether the new commit triggered a pipeline. Inspect the pipeline and deployment job logs, verify that the latest `index.html` was copied into `public`, and confirm that the Pages deployment completed successfully.

### 9. A pipeline worked yesterday, but today it cannot find a runner. What might have changed?
**Answer:** The self-hosted runner may have stopped, gone offline, become unassigned, or become ineligible for the job. Check runner status and project assignment, then start or correct the runner as required.

### 10. You see “Fatal: failed to install” while setting up the runner on Windows. What is your first troubleshooting step?
**Answer:** Reopen PowerShell as Administrator and retry. The session associated this error with missing administrator permissions.

### 11. Runner registration was skipped, but the `.gitlab-ci.yml` file was committed. Why can the pipeline fail?
**Answer:** The runner has not completed authentication with the GitLab instance. Complete the runner registration using the correct URL and token, start it, confirm that it is online, and then retry the pipeline.

### 12. You get an error saying the runner installation folder already exists. What should you do?
**Answer:** Check whether a previous installation folder is present. If it is a leftover from an incomplete or old setup, remove it carefully and repeat the installation and registration steps.

### 13. Your organization wants to deploy only when code is committed to the production branch. How can you implement this?
**Answer:** Add a `rules` condition to the deployment job that checks the production branch. For example, if the production branch is `main`:

```yaml
deploy_website:
  stage: deploy
  script:
    - echo "Deploying application"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

### 14. A developer accidentally commits an API key to the repository. Which GitLab security capability is relevant?
**Answer:** Secret Detection is relevant because it scans for sensitive values such as passwords, API keys, tokens, and SSH keys that may have been committed accidentally. The exposed credential should also be revoked or rotated; scanning alone does not make a leaked secret safe.

### 15. Your team wants to find vulnerabilities in source code before deployment. Which security testing approach should you consider?
**Answer:** SAST is relevant because it performs static analysis of source code. It can be included in the GitLab CI/CD workflow so code is scanned as part of the pipeline.

### 16. Your team wants to test a running web application for security issues. Should it use SAST or DAST?
**Answer:** DAST is the relevant approach because it tests the running application dynamically. SAST analyzes source code without requiring the application to run.

### 17. Your team wants to scan Docker images for vulnerabilities. Is SAST the correct tool?
**Answer:** Not necessarily. The session discussed Trivy for container-image scanning. SAST is intended for static source-code analysis, while container-image scanning checks the image and its contents.

### 18. Your company uses GitLab for repositories but already has Jenkins pipelines. Should the company immediately remove Jenkins?
**Answer:** No. GitLab and Jenkins can coexist. The team should review its existing pipeline logic, plugins, integrations, and operational requirements before deciding whether to migrate. The session noted that some organizations use GitLab, webhooks, runners, and Jenkins together.

### 19. A deployment introduced a bug. How can you identify and return to a previous version?
**Answer:** Identify the known-good commit ID or tag and use the team's deployment/release process to redeploy that version. The session covered using commit IDs or tags as references to previous builds; a detailed rollback demonstration was deferred to a future session.

### 20. Your team is moving from GitHub to GitLab. Which terminology should developers learn?
**Answer:**
- GitHub repository → GitLab project
- GitHub organization → GitLab group
- GitHub Pull Request (PR) → GitLab Merge Request (MR)
- GitHub Actions workflow → GitLab CI/CD pipeline

Learning these terms helps developers navigate GitLab's interface and collaboration workflow.

---

# Part 3: 10 Comparison-Based Questions & Answers
## GitLab vs GitHub vs GitHub Actions vs Jenkins

### 1. What is the primary purpose of GitHub, GitLab, GitHub Actions, and Jenkins?
**Answer:**

| Tool | Primary purpose |
|---|---|
| GitHub | Source-code hosting and collaboration |
| GitLab | Integrated DevSecOps platform: source code, CI/CD, and security capabilities |
| GitHub Actions | CI/CD and workflow automation integrated with GitHub |
| Jenkins | Standalone, open-source automation server for CI/CD and other automation |

### 2. Which tool provides built-in CI/CD?
**Answer:** GitLab includes integrated CI/CD configured through `.gitlab-ci.yml`. GitHub provides CI/CD through GitHub Actions, configured using workflow YAML files under `.github/workflows/`. Jenkins provides automation through its own pipeline configuration and plugin ecosystem.

### 3. What configuration file is used by each tool?
**Answer:**

| Tool | Configuration |
|---|---|
| GitLab CI/CD | `.gitlab-ci.yml` |
| GitHub Actions | `.github/workflows/<workflow>.yml` |
| Jenkins | Commonly a `Jenkinsfile` for Pipeline-as-Code |
| GitHub | No separate CI/CD file unless using a workflow tool such as GitHub Actions |

### 4. What is the equivalent of a runner or agent in each platform?
**Answer:**

| Tool | Execution component |
|---|---|
| GitLab CI/CD | GitLab Runner |
| GitHub Actions | GitHub-hosted or self-hosted runner |
| Jenkins | Agent/node |
| GitHub repository alone | No job executor; GitHub Actions supplies workflow execution |

### 5. Which tool is easier to set up for an integrated source-code and CI/CD workflow?
**Answer:** GitLab can be quicker to set up for an integrated workflow because source-code management and CI/CD are available in the same platform and a pipeline can be defined in one `.gitlab-ci.yml` file. GitHub users can use GitHub Actions, while Jenkins may require server setup and plugin configuration.

### 6. Which platform offers a larger plugin ecosystem?
**Answer:** Jenkins is known for its large plugin ecosystem and customization options. GitLab provides a more integrated set of platform features and integrations, but the session described Jenkins as having substantially more plugins.

### 7. Can GitHub Actions replace Jenkins?
**Answer:** It can replace Jenkins for some use cases, especially when a team wants workflow automation closely integrated with GitHub. However, the decision depends on existing pipelines, plugins, integrations, infrastructure, and operational needs. Some organizations may continue using Jenkins alongside GitHub.

### 8. Can GitLab replace both GitHub and Jenkins?
**Answer:** GitLab can cover source-code management and CI/CD in one platform, so it may reduce the need for separate tools. However, it does not automatically replace every Jenkins integration or custom workflow. Evaluate the organization's requirements before migrating.

### 9. Which tool should a team choose for an open-source project, and which for an integrated DevSecOps workflow?
**Answer:** GitHub is a strong choice when open-source collaboration and its developer ecosystem are the main priorities. GitLab is a strong choice when the team wants source-code management, CI/CD, and security workflows integrated into one platform.

### 10. Can GitLab and Jenkins work together in a real production environment?
**Answer:** Yes. GitLab can manage source code and trigger workflows, while Jenkins can continue executing established automation jobs. Webhooks and the relevant runner/agent setup can connect the workflow. Tool selection should be based on the team's architecture rather than assuming one platform must eliminate the other.

---

# Quick Revision: Important GitLab Terms

| Term | Meaning |
|---|---|
| Group | Organization-level container for projects |
| Subgroup | Group nested inside another group |
| Project | Repository and project workspace |
| Merge Request (MR) | Proposal to merge code changes |
| Pipeline | Automated CI/CD workflow |
| Stage | Phase in a pipeline, such as build or deploy |
| Job | Individual task executed in a stage |
| Runner | Executes GitLab CI/CD jobs |
| Executor | Defines the environment used by a runner |
| Artifact | Output file or directory retained from a job |
| SAST | Static Application Security Testing |
| DAST | Dynamic Application Security Testing |
| Secret Detection | Detects accidentally committed secrets |
| GitLab Pages | Hosting for static websites |
| `.gitlab-ci.yml` | GitLab pipeline configuration file |

# Practical Pipeline from the Session

The following example builds a `public` directory and copies the static HTML page into it. The deployment job runs only for commits to the `main` branch.

```yaml
stages:
  - build
  - deploy

deploy_website:
  stage: deploy
  script:
    - mkdir public
    - cp index.html public/
  artifacts:
    paths:
      - public
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

**Explain it in an interview:**
1. `stages` defines the pipeline phases.
2. `deploy_website` is the job name.
3. `stage: deploy` assigns the job to the deploy stage.
4. `script` contains the commands executed by the runner.
5. `mkdir public` creates the folder used for the Pages output.
6. `cp index.html public/` copies the website file into that folder.
7. `artifacts` retains the `public` directory.
8. `rules` limits the job to the `main` branch.

# Important Interview Notes

- Remember the exact `.gitlab-ci.yml` filename and its root-directory location.
- A runner must be online and eligible to execute the pipeline job.
- For the Windows setup demonstrated in the session, use PowerShell as Administrator and the Shell executor.
- Know the difference between GitLab project/group/MR and GitHub repository/organization/PR.
- Do not confuse SAST, DAST, secret detection, and container-image scanning.
- Be ready to explain the complete demo: commit HTML → pipeline trigger → runner executes job → artifact is created → GitLab Pages publishes the website.
- When comparing tools, explain the use case and trade-offs instead of saying one tool is always better.

---

*Prepared from the Batch-45 Day-8 session notes. Product plans, usage limits, pricing, and platform features can change; verify current vendor documentation before using plan-specific details in a production decision.*

