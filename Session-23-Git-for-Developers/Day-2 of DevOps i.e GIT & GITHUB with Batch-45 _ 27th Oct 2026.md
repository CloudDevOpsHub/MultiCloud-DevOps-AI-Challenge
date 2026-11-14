## Key Outcomes

The session provided a comprehensive walkthrough of Git and GitHub concepts within a DevOps context, using a clock-building scenario to simulate real-world team development. Key topics covered included repository creation, committing and pushing code, branching strategies, pull requests, merge conflicts, and advanced Git commands. Participants also explored environment-based branching (dev, QA, production) and received guidance on interview-ready answers for common Git questions.

---

## Decisions Made

- **DevOps role in code**: DevOps engineers are responsible for **code management and automation**, not writing code; developers write code and push it via Git. 
- **Language choice**: **Python** selected for the clock scenario due to its ready-made libraries (`time`, `datetime`), platform independence, open-source nature, and ease of use. 
- **Branch naming**: Default branch should be called **`main`** for new projects; older projects (3+ years) may still use **`master`** — both are functionally identical, the name change was introduced when **Microsoft acquired GitHub**. 
- **Code ownership**: **Code owners** (technical leads/managers) are responsible for approving pull requests; DevOps engineers manage code infrastructure but do not own code review responsibility. 
- **Branch protection**: Branch protection rules in GitHub settings can restrict who can merge into `main`, enforcing review gates before any code lands in production. 
- **JDK installation**: Participants instructed to install **JDK 27** (not JRE) on local machines ahead of the next Jenkins-focused session. 

---

## Core Concepts Covered

### Why Git and GitHub in DevOps

- DevOps spans development **and** operations — the "Dev" in DevOps requires managing code, even if engineers don't write it. 
- Developers write code locally, push to GitHub via Git; **code management and CI/CD automation** fall to the DevOps team. 
- Every commit is tracked with a **commit ID** tied to the author, enabling accountability (e.g., identifying which developer introduced a bug). 

### Scenario: Building a Clock Application

- A client requirement to build a **clock** (hours, minutes, AM/PM) was used as the teaching scenario. 
- The requirement was divided into **three modules/tickets** assigned to three developers of varying experience:
    - **Nilesh** (9 years, Team Lead) → Hour module (~2 hours to complete) 
    - **Rita** (5 years, Mid-level) → Minute module (~1–2 days) 
    - **Mamta** (6 months, Junior) → AM/PM feature (~3–5 days) 
- Each developer works in isolation; upon completion, code is pushed to a **centralized GitHub repository** so it can be combined. 

### GitHub Copilot

- Shortcut to open Copilot in VS Code: **Ctrl + Shift + I**. 
- Copilot is an **AI code assistant** — useful for generating boilerplate code from prompts. 
- Demonstrated live: a prompt ("I'm a Python developer, write a code to create a clock using libraries") generated a working clock script. 
- Running Python code: `python <filename>.py` compiles and runs the script. 

---

## Git Workflow — Step-by-Step (First-Time Setup)

The following commands were demonstrated live for initializing and pushing a new repository: 

1. **`git init`** — Initializes an empty local Git repository (one-time activity)
2. **`git add *`** — Stages all files for commit
3. **`git commit -m "message"`** — Commits staged files with a descriptive message; creates a trackable commit ID
4. **`git branch -M main`** — Renames local branch from `master` to `main` to match GitHub's default
5. **`git remote add origin <URL>`** — Links local repo to the remote GitHub repository (`origin` is a conventional alias for the remote URL)
6. **`git push -u origin main`** — Pushes code to GitHub; `-u` sets **upstream**, meaning future pushes track this branch automatically 

### Everyday Commands (After Initial Setup)

Mnemonic shared by a participant: **ACP — Add, Commit, Push** 

- `git add <file or *>` — Stage changed files
- `git commit -m "second message"` — Commit with context on what changed
- `git push` — Push to the already-configured remote; no need to re-add origin or reset branch 

---

## Branching Strategy

### Why Branches Exist

- Branches isolate individual developer work so the **main branch is never directly disrupted** during active development. 
- Best practice: **one branch per feature/ticket**, named with the Jira ticket number and feature name. 
    - Example: `feature/JIRA-007-alarm` for Dinesh's alarm feature ticket

### Branch Lifecycle

1. Developer creates a **feature branch** from the main/dev branch
2. Writes and commits code within that branch
3. Raises a **Pull Request (PR)** to merge into the target branch
4. Reviewer approves → code is merged
5. Feature branch is **deleted** after merge (no ongoing utility) 

### Environment-Based Branching (Real-World)

For teams with 3+ years of experience, a three-tier branch model is standard: 

- **Dev branch**: All developer feature branches merge here first; developer self-tests in the dev environment
- **QA branch**: Stable, versioned code promoted here after dev validation; QA team tests in QA environment
- **Production branch**: Final, QA-approved code deployed to production via Jenkins pipeline
> 
> Interview answer guidance: If asked about branching strategy, describe this three-branch model and explain that developers can only push to the **dev branch** directly; promotion to QA and production is controlled and version-tagged. 

---

## Pull Requests (PRs)

### What Is a PR?

- A **Pull Request** is a formal request to merge code from a feature branch into a target branch (e.g., `main` or `dev`). 
- Abbreviated as **PR**; raising a PR is distinct from `git pull` (which fetches remote code to local). 

### PR Workflow

1. Developer completes work on feature branch and raises a PR 
2. **Reviewer** (code owner / TL / manager) is assigned 
3. Reviewer leaves **comments** — requesting changes, flagging missing documentation, or approving 
4. Developer addresses comments, updates code, re-pushes
5. Once all checks pass (green tick), the reviewer clicks **Merge Pull Request → Confirm Merge** 
6. Code lands in the target branch; feature branch can then be deleted 

### PR Options in GitHub UI

- **Reviewer**: Who reviews the code 
- **Assignee**: Who is responsible for the PR
- **Label**: Tag the PR type — `enhancement` for new features, `bug` for fixes, etc. 
- **Project / Milestone**: Organizational metadata
- **Branch protection rules** (in Settings): Enforce required reviews, status checks, and conversation resolution before merging 

### Real-World PR Volume

- Kubernetes' GitHub repository was cited as an example with **1,400+ open PRs** — demonstrating that large projects require strict prioritization and ordering. 
- **Hotfixes** take priority over regular PRs regardless of submission order. 

---

## Merge Conflicts — Causes and Resolution

### Scenario Analysis (Dinesh vs. Ankur)

Three conflict scenarios were walked through: 

|                        Scenario                        |                  Outcome                  |
|--------------------------------------------------------|-------------------------------------------|
| Two developers edit **different files**                | No conflict — both merge cleanly          |
| Two developers edit the **same file, different lines** | No conflict — Git resolves automatically  |
| Two developers edit the **same file, same line**       | **Conflict** — manual resolution required |

### Resolution Process

- Manager checks **which PR was raised first** and which requirement is independent 
- The independent/first PR is merged first
- The second developer must **pull the latest merged code**, apply their changes on top, and raise a new PR 
- Developers should **`git pull`** **daily** to keep their local branch current and minimize conflict surface area 

---

## Advanced Git Commands (Conceptual Overview)

These are used rarely by DevOps engineers but appear in interviews: 

|         Command         |                             Purpose                             |
|-------------------------|-----------------------------------------------------------------|
| `git clone`             | Copies an entire remote repository to local machine             |
| `git checkout`          | Switches between branches                                       |
| `git diff`              | Shows line-by-line differences between file versions            |
| `git reset <commit-id>` | Rolls back to a previous commit state                           |
| `git stash`             | Temporarily shelves uncommitted changes without committing      |
| `git rebase`            | Replays commits one-by-one on top of another branch's tip       |
| `git squash`            | Collapses multiple commits into a single commit before merging  |

### Squash — When and Why

- Clients or managers may request squashing when a developer has made **10+ small commits** over several days, making history hard to audit 
- Squash combines all commits into one clean entry in the target branch's history 

### Rebase vs. Squash vs. Fast-Forward Merge

- **Rebase**: Commits are replayed individually on top of the latest base — preserves granular history 
- **Squash on merge**: All commits collapsed into one before merging — cleaner history 
- **Fast-forward merge**: No merge commit created; branch pointer simply advances to the latest commit — used when there are no diverging changes 

---

## Key Interview Questions Highlighted

|                      Question                       |                                                       Answer                                                       |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| How do you set your Git username?                   | `git config --global user.name "Name"`                                                                             |
| What is the difference between `main` and `master`? | No functional difference; `master` was the original default, `main` introduced after Microsoft acquired GitHub     |
| What is a PR?                                       | A request to merge a feature branch into the main branch; raised after development is complete                     |
| `git pull` vs. `git fetch`?                         | `git fetch` retrieves remote changes without merging; `git pull` fetches **and** merges into the current branch    |
| What is upstream (`-u`)?                            | Sets the default remote tracking branch so future `git push` commands don't need to specify the remote and branch  |
| What is `origin`?                                   | A conventional alias for the remote repository URL; set via `git remote add origin <URL>`                          |
| If your project is 3+ years old, which branch name? | Likely `master`; new projects use `main`                                                                           |
| Who approves PRs?                                   | Code owners — typically technical leads or managers with domain knowledge                                          |

---

## Git Staging Area — Visual Clarification

Three-zone model explained: 

1. **Workspace (Working Directory)**: Local files being edited — changes exist here first
2. **Staging Area (Index)**: Files added via `git add`; changes are staged but not yet committed
3. **Local Repository**: Changes finalized via `git commit`; tracked with commit ID
4. **Remote Repository (GitHub)**: Code pushed via `git push`; visible to the whole team
- `git reset` moves code **back** from local repository to staging or workspace 
- `git diff` shows differences **between** any two of these zones 

---

## Pending Confirmation / Action Items

- **All participants**: Install **JDK 21** on local machines before the next class (link shared in chat); verify with `java -version` in terminal 
- **All participants**: Practice the full Git push workflow (init → add → commit → push) at least once more; those who encountered errors should retry without creating a README file on GitHub first 
- **All participants**: Post a LinkedIn update about today's learning to build profile visibility 
- **Next session**: Jenkins installation, Java environment setup, and introduction to CI/CD pipeline concepts 

---

## Open Questions

- **API Gateway (AWS)**: Shubham asked about API Gateway coverage — confirmed it is **not in the current syllabus** but will be touched on in supplementary content 
- **`.NET`** **CI/CD**: A participant asked whether CI/CD concepts apply to .NET — confirmed: **CI/CD concepts are language-agnostic**; the pipeline process is identical regardless of whether the codebase is Java, Python, Go, or .NET 
- **Staging environment purpose**: Discussed as a **pre-production replica** used to validate code under production-like conditions before final release; alpha/beta releases map to this layer 
- **AWS billing concern**: One participant reported unexpected charges (~₹1,500); advised to disable **auto-pay** in PhonePe and set a budget alarm; encouraged to continue practicing and revert resources to avoid further charges


---

# Git & GitHub Interview Preparation

Based on the concepts covered in **Day-2 of DevOps with Batch-45 — 27 October 2026**. The questions focus on the session topics: Git workflow, GitHub, branches, pull requests, merge conflicts, staging, remote repositories, and commonly used Git commands.

## Part 1: 20 Git & GitHub Interview Questions and Answers

### 1. What is Git?
**Answer:** Git is a distributed version control system used to track changes in source code. It helps developers collaborate, review history, and return to earlier versions when needed. it is one of the popular SCM tool.. 

### 2. What is GitHub?
**Answer:** GitHub is a platform for hosting Git repositories online. Teams use it to share code, manage branches, raise pull requests, review changes, and collaborate.

### 3. What is the difference between Git and GitHub?
**Answer:** Git is the version control tool that tracks changes. GitHub is an online platform that hosts Git repositories and adds collaboration features such as pull requests and code reviews.

### 4. Why do DevOps engineers need to understand Git?
**Answer:** DevOps engineers work with code repositories and automate build, test, and deployment workflows. Understanding Git helps them manage source code and connect repositories to CI/CD tools such as Jenkins.

### 5. What does `git init` do?
**Answer:** `git init` initializes a new local Git repository in the current directory. It is generally used once when starting version control for a new project.

```bash
git init
```

### 6. Explain `git add`, `git commit`, and `git push`.
**Answer:**
- `git add` stages changes for the next commit.
- `git commit` records the staged changes in the local repository.
- `git push` sends local commits to a remote repository such as GitHub.

Example:

```bash
git add app.py
git commit -m "Add clock application"
git push
```

### 7. What are the workspace, staging area, and local repository?
**Answer:** The workspace is where you edit files. The staging area contains changes selected with `git add`. The local repository stores committed snapshots created with `git commit`. After `git push`, the commits are sent to the remote repository.

### 8. What is a commit ID?
**Answer:** A commit ID is a unique identifier for a commit. It lets a team inspect a specific change and trace it through the repository history.

### 9. What is the difference between `main` and `master`?
**Answer:** They are branch names; neither has a special technical advantage simply because of its name. New repositories commonly use `main`, while older repositories may still use `master`. Teams should use the branch name configured for their repository.

### 10. What is a remote repository, and what is `origin`?
**Answer:** A remote repository is a version of the repository hosted elsewhere, for example on GitHub. `origin` is the conventional name Git assigns to the primary remote, although it can be changed.

```bash
git remote add origin <repository-url>
```

### 11. What does `git push -u origin main` do?
**Answer:** It pushes the local `main` branch to the remote named `origin` and sets the upstream tracking relationship. Later, Git can often determine the correct remote branch when you run `git push` or `git pull`.

### 12. What is a Git branch, and why do teams use feature branches?
**Answer:** A branch is a separate line of development. Feature branches let developers work on individual tickets without directly disturbing the shared integration branch.

```bash
git switch -c feature/JIRA-007-alarm
```

### 13. What is a pull request (PR)?
**Answer:** A pull request is a request to review and merge changes from one branch into another. Reviewers can comment, request changes, and approve the proposed update before it is merged.

### 14. What is the difference between a pull request and `git pull`?
**Answer:** A pull request is a collaboration and review workflow on a hosting platform such as GitHub. The `git pull` command fetches remote changes and integrates them into the current local branch.

### 15. What is the difference between `git fetch` and `git pull`?
**Answer:** `git fetch` downloads remote updates but does not integrate them into the current branch. `git pull` fetches updates and then integrates them, usually by merging or rebasing depending on configuration and options.

### 16. What causes a merge conflict?
**Answer:** A merge conflict occurs when Git cannot automatically combine changes, commonly when two branches change overlapping parts of the same file. The developer must inspect the conflicting changes, decide on the correct result, and complete the merge.

### 17. How can you reduce merge conflicts?
**Answer:** Keep your branch updated with the target branch, communicate about shared files, make focused commits, and integrate changes regularly. The session recommends pulling the latest changes daily.

### 18. What is `git stash` used for?
**Answer:** `git stash` temporarily stores uncommitted changes so you can switch tasks or branches without committing unfinished work.

```bash
git stash
git stash pop
```

### 19. Explain `git diff` and `git clone`.
**Answer:** `git diff` displays differences between versions or repository states. `git clone` creates a local copy of an existing remote repository, including its history.

```bash
git diff
git clone <repository-url>
```

### 20. What are rebase, squash, and fast-forward merge?
**Answer:**
- **Rebase:** Replays commits on top of another base commit, changing their commit IDs when rewritten.
- **Squash merge:** Combines a branch's changes into a single commit on the target branch.
- **Fast-forward merge:** Advances the target branch pointer when it can move forward without creating a merge commit.

Teams choose a strategy based on the desired history and repository policy.

---

## Part 2: 20 Scenario-Based Git & GitHub Interview Questions and Answers

### 1. You created a Python clock application locally. How would you push it to a new GitHub repository for the first time?
**Answer:** Initialize Git, stage the files, commit them, configure the branch and remote, then push.

```bash
git init
git add .
git commit -m "Initial clock application"
git branch -M main
git remote add origin <repository-url>
git push -u origin main
```

If the repository already has a remote or commits, inspect its state first instead of blindly repeating setup commands.

### 2. You changed `clock.py`, but the changes are not included in your commit. What would you check?
**Answer:** The changes may not have been staged. Check the status, stage the file, and commit again.

```bash
git status
git add clock.py
git commit -m "Update clock logic"
```

### 3. Your teammate asks you to work on an alarm feature without affecting the shared branch. What would you do?
**Answer:** Create a feature branch, make and commit the change there, push it, and open a PR.

```bash
git switch -c feature/JIRA-007-alarm
git add .
git commit -m "Add alarm feature"
git push -u origin feature/JIRA-007-alarm
```

Then create a pull request against the team's agreed target branch.

### 4. You ran `git push`, but Git says there is no upstream branch. How do you fix it?
**Answer:** Set the upstream while pushing the current branch. Replace `feature-name` with the actual branch name.

```bash
git push -u origin feature-name
```

### 5. A developer accidentally committed to their local `master` branch, but the team uses `main`. What should they do?
**Answer:** First inspect the branch and commit state. If the commit is local and has not been shared, the developer can create or move the work to the correct branch carefully. If it has already been pushed, coordinate with the team before rewriting history. Do not rename or reset shared branches without checking the repository's workflow.

### 6. Two developers change different files in the same feature release. Will Git always report a conflict?
**Answer:** No. Changes to different files can usually be merged automatically, though conflicts can still occur in related metadata or other overlapping changes. Review the merge result and run tests.

### 7. Two developers edit different lines in the same file. What is likely to happen?
**Answer:** Git can often combine the changes automatically if the edits do not overlap. The team should still review the resulting file and test it.

### 8. Two developers change the same line in `clock.py`. What should the team do?
**Answer:** Git may report a conflict. The developers should inspect both versions, agree on the correct logic, edit the file to remove conflict markers, stage the resolution, and complete the merge or rebase.

```bash
git status
# Edit the conflicted file and remove conflict markers.
git add clock.py
git commit
```

The final command depends on whether the conflict happened during a merge or another operation; during a rebase, use `git rebase --continue` instead.

### 9. Your feature branch is several days behind `dev`. How should you update it?
**Answer:** Check for uncommitted work, save or commit it, fetch the latest remote state, then integrate the latest `dev` changes using the team's preferred merge or rebase workflow.

```bash
git status
git fetch origin
git switch feature/JIRA-007-alarm
git merge origin/dev
```

Resolve any conflicts, test, and push the updated branch.

### 10. You need to inspect what changed before committing. Which command would you use?
**Answer:** Use `git diff` for unstaged changes. Use `git diff --staged` to inspect staged changes.

```bash
git diff
git diff --staged
```

### 11. You started working on a new urgent ticket but have unfinished changes in your current branch. What can you do?
**Answer:** If the changes are not ready to commit, stash them, switch to the other branch, and restore them when ready.

```bash
git stash
git switch -c hotfix/urgent-issue
# Work on the urgent issue.
git switch feature/JIRA-007-alarm
git stash pop
```

Check for conflicts after restoring the stash.

### 12. A pull request has review comments requesting a fix. What is the normal workflow?
**Answer:** Make the requested changes on the same feature branch, commit them, and push the branch. The existing PR will update with the new commits. Respond to review comments and wait for required approvals and checks before merging.

### 13. A developer tries to merge directly into `main`, but GitHub blocks the merge. Why?
**Answer:** Branch protection rules may require reviews, successful status checks, resolved conversations, or restricted permissions. Check the PR's reported requirements and follow the repository policy rather than bypassing protection.

### 14. Your branch contains 12 small “fix typo” commits, and the team wants a cleaner target-branch history. What can you do?
**Answer:** Use the repository's approved squash-merge option to combine the PR's changes into one commit. Confirm that the team wants a squash merge and preserve any important commit details in the final message.

### 15. A teammate asks you to review the exact changes in a PR. How can you inspect them locally?
**Answer:** Use the GitHub PR diff, or fetch the branch and compare it with the target branch using `git diff`. Review the logic, test coverage, security concerns, and possible side effects.

```bash
git fetch origin
git diff origin/dev...origin/feature/JIRA-007-alarm
```

Adjust branch names to match the repository.

### 16. You need a local copy of an existing GitHub project on a new machine. Which command do you use?
**Answer:** Clone the repository.

```bash
git clone <repository-url>
cd <repository-folder>
```

Then check the current branch and remote configuration with `git status` and `git remote -v`.

### 17. You committed a change locally and now realize it is wrong. What should you do?
**Answer:** First determine whether the commit has been pushed and whether others depend on it. For a shared commit, `git revert <commit-id>` is often safer because it creates a new commit that reverses the change. For a private, unpushed commit, reset may be appropriate, but understand whether it will keep or discard the changes before using it.

### 18. A bug appeared after a recent deployment. How can Git help identify the change?
**Answer:** Inspect recent commits and their authors, compare the deployed version with the previous version, and review relevant PRs.

```bash
git log --oneline
git show <commit-id>
git diff <known-good-commit>..<suspected-commit>
```

Use this evidence to identify a likely change, then validate the fix with tests.

### 19. Your team uses `dev`, `qa`, and `production` branches. How should code move through these environments?
**Answer:** Developers work in feature branches and merge into `dev` for initial validation. Approved, stable changes are promoted to `qa` for testing, then QA-approved and versioned changes are promoted to the production branch according to release policy. CI/CD pipelines can deploy the appropriate branch or tag to each environment. Protect release branches and avoid uncontrolled direct pushes.

### 20. Jenkins needs to build the latest application code from GitHub. What repository details and workflow are important?
**Answer:** Configure the repository URL, credentials or token where required, and the correct branch or tag in Jenkins. The pipeline checks out the code, builds and tests it, and only deploys when the required checks pass. Keep secrets out of the repository and use protected credentials in Jenkins.

---

## Quick Revision: Common Commands

| Command | Purpose |
|---|---|
| `git init` | Initialize a local repository |
| `git clone <url>` | Copy a remote repository locally |
| `git status` | Show working-tree and staging status |
| `git add <file>` | Stage changes |
| `git commit -m "message"` | Record staged changes |
| `git push` | Send local commits to a remote |
| `git pull` | Fetch and integrate remote changes |
| `git fetch` | Download remote updates without integrating them |
| `git branch` | List local branches |
| `git switch <branch>` | Switch branches |
| `git switch -c <branch>` | Create and switch to a branch |
| `git diff` | Show unstaged differences |
| `git diff --staged` | Show staged differences |
| `git log --oneline` | Show compact commit history |
| `git show <commit-id>` | Inspect a commit |
| `git stash` | Temporarily save uncommitted changes |
| `git stash pop` | Reapply the most recent stash |
| `git merge <branch>` | Merge a branch into the current branch |
| `git rebase <branch>` | Replay current-branch commits on a new base |
| `git revert <commit-id>` | Create a commit that reverses another commit |

## Interview Tip

When answering scenario-based questions, explain **the problem, the command or GitHub workflow you would use, and how you would verify the result**. For shared branches, avoid destructive history changes unless the team has agreed on them. Always check `git status` before and after a recovery or merge operation.
