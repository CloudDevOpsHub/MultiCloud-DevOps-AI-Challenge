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
