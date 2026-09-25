## Key Outcomes

The session marked Day 1 of a planned five-day Linux training program targeting non-technical and semi-technical professionals transitioning into cloud and DevOps roles. Core foundational concepts were covered — operating systems, kernel vs. shell, Linux history, open-source philosophy, distributions, and Linux vs. Windows comparison. Students installed Git and MobaXterm, cloned the Linux kernel repo from GitHub, connected to a live server via SSH using the Bandit game challenge, and began running basic Linux commands inside a VM. The session concluded with guided Google Cloud Platform (GCP) account creation, VM provisioning on Ubuntu 24.04 LTS, and budget alarm setup guidance.

---

## Session Plan and Structure

- **Five-day Linux curriculum** designed to take participants from non-technical to technically proficient in Linux administration for cloud and DevOps engineering. 
- **Day 1 (Friday):** Basics — OS concepts, kernel/shell, Linux history, distributions, environment setup, and introductory commands. 
- **Day 2 (Monday):** Intermediate Linux. 
- **Day 3 (Tuesday):** Advanced Linux. 
- **Day 4:** Advanced real-time troubleshooting. 
- **Day 5:** Interview-perspective activities and Q&A. 
- Friday start was intentional: students get a head start over the weekend; a **free Linux course** was promised to be shared at session end to support slow and fast learners alike — slow learners can re-watch recordings; fast learners can explore further. 
- Sessions are **live and interactive**: camera on, mute by default, raise hand to speak, and take notes. 

---

## Operating System Fundamentals

- **Operating system defined:** An interface between hardware and applications — system software that manages CPU, memory, disk, and other resources, and provides common services for programs. 
- A student correctly summarized it as "an interface between computer user and computer hardware," which the instructor confirmed as the perfect answer. 
- Applications consume hardware resources (CPU, memory) through the operating system; without the OS, applications cannot access hardware. 
- **Examples of operating systems** cited: macOS (Apple), Windows (Microsoft), Ubuntu, Linux, DOS. 

### Kernel vs. Shell

- **Trap interview question:** "Is Linux an operating system?" — officially yes, but at the interview level the technically precise answer is that Linux is a **kernel**, not just an OS. 
- **Kernel:** The innermost layer of the OS, closely and directly connected to hardware; responsible for performance and resource management. 
- **Shell:** The outer layer of the OS; users and applications connect through the shell, which in turn communicates with the kernel, which talks to hardware. 
- **Flow:** Application → Shell → Kernel → Hardware → CPU/Memory returned to application. 
- **Restaurant analogy** offered by a student: The restaurant = OS; the user = customer; the waiter = shell; the chef = kernel. Users interact with the waiter (shell), never seeing the chef (kernel) directly, yet the chef produces the output. 
- **Shell used in Linux:** Bash (Bourne Again Shell) — same in Linux and Unix environments. 

---

## Linux: Definition, History, and Philosophy

### What Linux Is

- Linux is an **open-source, community-developed kernel** (and operating system) for computers, servers, mainframes, mobile phones, embedded systems, and supercomputers. 
- **Open source** means the source code is publicly available on the internet (e.g., GitHub); anyone can view, fork, modify, and redistribute it. 
- The Linux kernel repository on GitHub is written **98% in C language**, with small percentages in Assembly (~6%), Rust (~4%), and Python (~0.03%). 
- **Why C?** C is a low-level language that connects closely to hardware, enabling better performance — the primary reason operating systems are written in C. 
- **Community developed:** The Linux kernel has **19,234 contributors** actively involved in writing and maintaining the code. 
- Code changes are tracked via GitHub; individual commits show exactly what lines were added or removed, enabling full traceability across thousands of contributors. 

### History: Unix → Linux

- **Unix came first;** Linux was developed from the idea and design principles of Unix. 
- From Unix's architecture, many operating systems were later created; Linux became one of the most popular. 
- Linux was developed by **Linus Torvalds in 1991**, approximately 30+ years ago. 
- Linux is designed to be very similar to Unix in structure and behavior. 

### Why Linux Is Popular (Student Discussion)

- **Free and open source:** No license cost unlike Windows; this lowered the barrier to adoption and drove widespread use. 
- **Lightweight:** Requires significantly less memory and disk space than Windows. 
- **Highly secure:** Does not support `.exe` or `.msi` files (common virus delivery formats); viruses cannot execute natively; no antivirus installation required. 
- **No GUI overhead:** Linux is used without a graphical user interface (GUI) in server environments, freeing up significant memory (the instructor's Windows machine was consuming ~18 GB RAM, with ~10 GB attributed to Windows GUI processes alone). 
- **Performance and scalability:** More efficient than Windows for running servers and cloud workloads; GUI-less operation reduces CPU and memory consumption dramatically. 
- **Stability:** Can run for years without a single reboot. 
- **Cloud-native:** Runs on cloud infrastructure, containers, Kubernetes nodes, and databases. 
- **Developer-friendly ecosystem.** 
- **Permissions and user management:** Granular control over users, groups, and file permissions. 

---

## Linux vs. Windows Comparison

|   Dimension    |                   Linux                   |                            Windows                            |
|----------------|-------------------------------------------|---------------------------------------------------------------|
| Cost           | Free, open source                         | Requires a license (pirated copies are common but unofficial) |
| Source         | Open source                               | Closed source                                                 |
| Security       | Highly secure, no antivirus needed        | Less secure, antivirus typically required                     |
| Performance    | More efficient, lightweight               | Less efficient due to GUI overhead                            |
| Path separator | Forward slash `/`                         | Backslash `\`                                                 |
| GUI            | Available (GNOME) but not used in servers | GUI is default and resource-intensive                         |
| Use case       | Servers, cloud, DevOps, containers        | Developer workstations, end-user desktops                     |
| Ease of use    | Requires training; CLI-based              | Usable by anyone with basic English skills                    |

- Windows runs on DOS (Disk Operating System) at its base; DOS is pre-installed on hard disks/SSDs to make storage readable during OS installation. 
- Linux GUI (GNOME) exists but is intentionally avoided in server/cloud contexts to preserve performance and memory. 

---

## Linux Distributions

- Linux has **four main distribution families:** 
    1. **Red Hat** — parent organization providing enterprise-grade, supported, commercial Linux; used when enterprise support is required. 
    2. **Fedora** — Red Hat-sponsored community version. 
    3. **Debian** — family of open-source operating systems; Ubuntu belongs to this family. 
    4. **Others** — OpenSUSE, Mint Linux, Arch Linux, etc. 
- **When to use Ubuntu vs. Red Hat:**
    - Ubuntu: free, open-source use cases, learning, personal projects. 
    - Red Hat: enterprise environments requiring vendor support and SLAs. 
- **Most commonly used in real-time company environments** (per student responses): **Ubuntu**, particularly **Ubuntu 24.04 LTS (64-bit)**. 
- Other distributions mentioned in real-time use: Amazon Linux (AWS), CentOS. 

---

## Unix vs. Linux

- **Unix:** Licensed, proprietary operating system developed at AT&T Labs; used in enterprise workstations and servers where support contracts are needed. 
- **Linux:** Open source, free, developed from Unix's ideas; used across desktops, mobiles, mainframes, and cloud. 
- Both use **Bash shell** and **GNOME** as a GUI option. 
- Key difference: Linux is open source; Unix is not — Unix is tied to a company/organization providing licensed support. 
- Examples of Unix: Sun Solaris, HP-UX. 
- Examples of Linux: Ubuntu, Debian, GNU, Arch Linux. 
- Linux initially developed for **32-bit architecture**; all modern systems are now **64-bit**. 

---

## Practical Environment Setup

### Git Installation and Linux Kernel Clone

- Students instructed to download and install **Git for Windows (64-bit, ~62 MB)**. 
- Mac users: no Git download needed — run `brew install git` from the terminal. 
- After installation, right-click in any working directory → **Open Git Bash** → black terminal opens. 
- Command demonstrated to download the Linux kernel source code: 

```plaintext
git clone <Linux kernel GitHub repo URL>
```

- **`git clone`** = downloads code from a remote repository to the local machine. 
- Once downloaded, the code can be opened in VS Code, customized, made more secure using AI, or even redistributed under a license. 
- Instructor noted the full kernel repo is very large; students were advised not to run the clone unless they have sufficient internet and storage. 

### MobaXterm Installation (Windows SSH Terminal)

- **MobaXterm** is a terminal application used to connect to remote servers via SSH; widely used in real-time corporate environments. 
- Download: search "MobaXterm Download for Windows" → download the portable edition (~free). 
- **Mac users do not need MobaXterm** — Mac already has a Unix-based terminal built in; Linux users similarly need nothing extra. 
- Installation: extract the downloaded zip → double-click the executable → select dark/black theme → click **Start Local Terminal**. 
- Students who cannot install software on office laptops: MobaXterm is portable (no installation required — just extract and run). 
- Alternative for restricted office laptops: use Windows CMD, PowerShell, or WSL (Windows Subsystem for Linux) for practice — though the instructor noted that interview environments typically reflect real Linux terminals. 

### Bandit Game (SSH Practice Challenge)

- **Bandit** (OverTheWire) introduced as a weekend challenge — a game with **34 stages** that teaches Linux command-line skills progressively. 
- SSH connection command demonstrated: 

```plaintext
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

- **Command breakdown:**
    - `ssh` = Secure Shell — used to securely connect to remote servers. 
    - `bandit0` = username. 
    - `@` = separator between username and hostname/endpoint. 
    - `bandit.labs.overthewire.org` = hostname (endpoint/domain URL — preferred term over "IP address" in interview contexts). 
    - `-p 2220` = port number. 
    - Password for Level 0: `bandit0`. 
- Students successfully connected to the server; Linux terminal environment confirmed working. 
- Instructor advised: complete the 34 stages over the week alongside training; completing the game signals strong Linux proficiency. 
- Note: office laptops may block outbound SSH connections on port 2220; personal laptops recommended for this exercise. 

### Google Cloud Platform (GCP) Account Creation

- Students guided to create GCP accounts at `cloud.google.com/free`. 
- **Free tier:** **$300 credit for 90 days** provided upon account creation. 
- **Key steps:**
    1. Use a **genuine personal email** (not a numbered or incomplete-name email); Google performs background verification. 
    2. Select **Individual** account type; uncheck the "Organization" checkbox. 
    3. Enter accurate contact details (mobile number and state must be correct). 
    4. Add a payment method with **international transactions enabled** (credit card, debit card, or UPI). 
    5. If using UPI: a **₹15,000 autopay mandate** will appear — this must be **allowed/enabled** but **no money is actually deducted**; the autopay can be disabled afterward. 
    6. A **₹2 verification charge** may be deducted from credit cards — this is **fully refundable**. 
    7. Some accounts may be asked for a **₹1,000 prepayment** due to Google's trust verification process — this is **100% refundable** and gets added to the account as credit. 
    8. Upon successful setup, students received credit confirmations of approximately **₹28,000–₹28,664** in their accounts. 
- **Post-setup actions:**
    - Enable **Compute Engine API** (takes ~30–60 seconds; required before creating any VM). 
    - Create a VM: Compute Engine → VM Instances → Create Instance → name it → select **Ubuntu 24.04 LTS, 64-bit, 10 GB disk** → allow network → click Create. 
    - Connect to VM via SSH: click the SSH button in the console — Google Cloud auto-handles key exchange, username, external IP, and port 22. 
    - **Delete the VM after use** — it consumes credits even when idle; three-dot menu → Delete. 
    - **Do not upgrade the account** to a paid tier. 
- **Budget Alarm Setup** (strongly recommended):
    - Navigate to Billing → Budgets & Alerts → Create Budget. 
    - Set budget name (e.g., "Batch 45 Budget"), scope to all projects, amount = **₹100** as a safe threshold. 
    - Configure alerts at **50%, 90%, and 100%** of budget; add email alerts to project owner. 
    - A blog post on the instructor's website covers this step-by-step for GCP (and Azure). 
    - If account shows "billing disabled" on a secondary project, use only the primary billing-enabled project. 

---

## Linux File System and Basic Commands

### Root Directory and Home Directory

- **Root directory (****`/`****):** The top-level directory of the Linux file system — equivalent to the C: drive in Windows; contains all OS files and folders. 
- **Root user:** A separate concept — root is also a superuser account with full system permissions; distinct from the root directory location. 
- **Home directory (****`~`****):** The tilde `~` symbol represents the current user's home directory (e.g., `/home/vikas`). 
- Different users have separate home directories, desktops, and downloads — same concept as Windows multi-user setups. 
- Command `sudo -i` switches to the root user's home location. 

### Key Directories Explored

- **`/usr`** — User-related data; contains user management information. 
- **`/dev`** — Hardware-related device information. 
- **`/bin`** — Binary (executable) files; applications that can be run directly without installation (e.g., MobaXterm portable edition is a binary). 

### Commands Demonstrated

|     Command      |                  Purpose                   |
|------------------|--------------------------------------------|
| `ls`             | List contents of the current directory     |
| `cd <directory>` | Change directory                           |
| `cd ..`          | Go back one directory level                |
| `pwd`            | Print working directory (current location) |
| `whoami`         | Display the current logged-in user         |
| `uname`          | Display the machine/OS name                |
| `mkdir <name>`   | Create a new directory                     |

- Commands are **case-sensitive** in Linux — `LS` will not work; must be lowercase `ls`. 
- Students ran five commands in sequence during the session: `whoami`, `uname`, `pwd`, `mkdir`, `ls`, and `cd`. 

---

## Action Items

- **All students:** Install Git for Windows (64-bit) if not already installed; Mac users run `brew install git`. 
- **All students (Windows):** Download, extract, and open MobaXterm portable edition; set dark theme; open local terminal. 
- **All students:** Create a GCP account at `cloud.google.com/free` using a genuine personal email and payment method. 
- **All students:** Set up a GCP budget alarm for ₹100 using the instructor's website guide. 
- **All students:** Delete any VMs created during the session to avoid credit consumption. 
- **All students (weekend task):** Access the free Linux course shared by the instructor via the LMS/website. 
- **All students (weekend challenge):** Begin the **Bandit game** (OverTheWire, 34 stages) to practice Linux CLI skills; aim to complete as many levels as possible before Monday. 
- **Vijay (18 years experience, interview pending):** Prepare Kubernetes topics one by one — HPA and platform engineering architecture specifically; review each point systematically before the next interview round. 
- **Instructor:** Share the free Linux course link with all students; post budget alarm blog link in the group. 
- **Instructor:** Create a budget alarm walkthrough in Monday's session. 
- **Instructor:** Continue with intermediate Linux on Monday. 

---

## Open Questions / Pending Items

- Students with **office laptop restrictions** (Deloitte security policy cited as an example) cannot install software or open external links; they need to use personal laptops for GCP and MobaXterm setup. 
- Some students encountered **GCP account creation errors** (payment failures, mobile numbers already used across multiple Gmail IDs, "account activation up to 24 hours" messages); instructor advised: re-attempt, use a different payment method, or wait 24 hours for activation to reflect. 
- Students who received a **"make a payment" prompt** (₹500–₹3,000 range) were advised this is a Google trust-verification prepayment, fully refundable, and must be paid to proceed. 
- **Autopay disable:** Students who enabled ₹15,000 UPI autopay for account creation were advised to disable it afterward; no charges occur as long as free credit remains. 
- **LMS chat scroll issue:** Latest messages require manual scrolling; noted as a known limitation of the current LMS/AI backend integration with Zoom. 
- **Interview questions per session:** Student requested 20 questions per day; instructor confirmed 1,000 questions are already available in the LMS under the interview questions section; days 3 and 4 question sets to be verified/expanded. 




# Batch-45 Day-1 — Linux Interview Q&A + GCP/Linux Scenarios

> Based on the Day-1 Linux session reference. The session covered Linux fundamentals, kernel vs shell, distributions, SSH, basic Linux commands, GCP account setup, Compute Engine VM creation, SSH access, and budget alerts. fileciteturn0file0L3-L3

## Part 1: 20 Basic Linux Questions & Answers

### 1. What is Linux?
**Answer:** Linux is an open-source, community-developed kernel and operating system used widely on servers, cloud platforms, containers, desktops, and other systems.

### 2. Is Linux an operating system or a kernel?
**Answer:** In interview terms, Linux is technically a **kernel**. Linux-based operating systems combine the Linux kernel with other components such as utilities and shells.

### 3. What is an operating system?
**Answer:** An operating system is system software that acts as an interface between applications/users and hardware. It manages CPU, memory, disk, and other resources.

### 4. What is a kernel?
**Answer:** The kernel is the core part of the operating system. It manages system resources and communicates closely with hardware.

### 5. What is a shell?
**Answer:** A shell provides an interface through which users interact with the operating system. In Linux, **Bash** is a commonly used shell.

### 6. What is Bash?
**Answer:** Bash stands for **Bourne Again Shell**. It is a command-line shell commonly used in Linux and Unix environments.

### 7. What is open source?
**Answer:** Open source means the source code is publicly available so people can inspect, modify, fork, and redistribute it according to its license.

### 8. Who developed Linux?
**Answer:** Linux was developed by **Linus Torvalds in 1991**.

### 9. What is a Linux distribution?
**Answer:** A Linux distribution is a complete operating system built around the Linux kernel along with packages, tools, libraries, and a package-management system.

### 10. What is Ubuntu?
**Answer:** Ubuntu is a Linux distribution that belongs to the Debian family. It is widely used for learning, servers, cloud workloads, and DevOps.

### 11. What is the root directory in Linux?
**Answer:** The root directory is represented by `/`. It is the top-level directory of the Linux filesystem.

### 12. What is the difference between `/` and `~`?
**Answer:** `/` is the root of the filesystem, while `~` represents the current user's home directory.

### 13. What does `pwd` do?
**Answer:** `pwd` means **Print Working Directory**. It displays the current directory path.

### 14. What does `ls` do?
**Answer:** `ls` lists the files and directories in the current directory.

### 15. What does `cd` do?
**Answer:** `cd` means **Change Directory**. It is used to move from one directory to another.

### 16. What does `cd ..` do?
**Answer:** It moves one level up from the current directory.

### 17. What does `whoami` do?
**Answer:** `whoami` displays the username of the currently logged-in user.

### 18. What does `uname` do?
**Answer:** `uname` displays system or kernel-related information about the machine.

### 19. What does `mkdir` do?
**Answer:** `mkdir` creates a new directory. Example: `mkdir project`.

### 20. Is Linux case-sensitive?
**Answer:** Yes. Linux commands, file names, and paths are case-sensitive. For example, `ls` and `LS` are different.

---

# Part 2: 20 Basic GCP + Linux Scenario-Based Questions & Answers

### Scenario 1: You created a GCP VM but cannot find a Linux terminal. What can you do?
**Answer:** Open **Compute Engine → VM Instances** and use the **SSH** button for the VM. GCP can handle the SSH connection from the console.

### Scenario 2: You need a Linux VM for the Batch-45 practice labs. Which OS was used in the session?
**Answer:** **Ubuntu 24.04 LTS, 64-bit** was used for the GCP VM setup.

### Scenario 3: Your GCP VM is running but you want to know your current Linux user.
**Answer:** Run:
```bash
whoami
```

### Scenario 4: You logged into the VM and want to know where you are.
**Answer:** Run:
```bash
pwd
```
This shows the current working directory.

### Scenario 5: You want to see files in your current GCP Linux VM directory.
**Answer:** Run:
```bash
ls
```

### Scenario 6: You want to move into a directory called `project`.
**Answer:** Run:
```bash
cd project
```

### Scenario 7: You want to move one directory level back.
**Answer:** Run:
```bash
cd ..
```

### Scenario 8: You want to create a directory called `devops`.
**Answer:** Run:
```bash
mkdir devops
```

### Scenario 9: You are confused between the Linux root directory and the root user. What is the difference?
**Answer:** `/` is the **root directory** of the filesystem. The **root user** is a superuser account with extensive system permissions. They are different concepts.

### Scenario 10: You want to go to your current user's home directory.
**Answer:** Run:
```bash
cd ~
```
The `~` symbol represents the current user's home directory.

### Scenario 11: You are connected to a GCP Ubuntu VM and want to check the machine/kernel information.
**Answer:** Run:
```bash
uname
```
You can use `uname` to display system/kernel information.

### Scenario 12: Your application needs a remote Linux server. Which protocol can you use for secure command-line access?
**Answer:** Use **SSH (Secure Shell)**. The session demonstrated SSH using:
```bash
ssh username@hostname -p 2220
```
For a normal Linux SSH server, port **22** is commonly used. GCP's browser-based SSH connection handles the connection details for you.

### Scenario 13: You need to practice Linux commands without creating your own server. What challenge was recommended?
**Answer:** The session recommended the **Bandit game from OverTheWire**, which progressively teaches Linux command-line skills through SSH.

### Scenario 14: Your GCP VM is no longer needed. What should you do?
**Answer:** **Delete the VM** after completing the lab. The session specifically advised deleting unused VMs because they can consume GCP credits even while idle.

### Scenario 15: You created a GCP VM but Compute Engine is not allowing you to create one. What should you check first?
**Answer:** Check whether the **Compute Engine API** is enabled for the project. The session instructed students to enable it before creating the VM.

### Scenario 16: You want to protect your GCP credits from unexpected usage. What should you configure?
**Answer:** Create a **Budget & Alert** under **Billing → Budgets & Alerts** and configure alert thresholds such as **50%, 90%, and 100%**.

### Scenario 17: You want to create a low-cost practice Linux VM in GCP. What configuration was demonstrated?
**Answer:** The session demonstrated an **Ubuntu 24.04 LTS 64-bit** VM with a **10 GB disk** and network access enabled.

### Scenario 18: You are using Windows and need a terminal application to connect to Linux servers. What tool was introduced?
**Answer:** **MobaXterm** was introduced as a Windows terminal application commonly used for SSH connections to remote servers.

### Scenario 19: Your office laptop does not allow MobaXterm installation. What alternatives were discussed?
**Answer:** The session mentioned **Windows CMD, PowerShell, or WSL** as alternatives for practice. A personal laptop may also be needed where corporate restrictions block SSH or external access.

### Scenario 20: You want to download a Linux project/repository to your local machine before working with it.
**Answer:** Use Git's `clone` command:
```bash
git clone <repository-url>
```
The session used cloning the Linux kernel repository as an example.

---

# Quick Revision: Commands

| Command | Purpose |
|---|---|
| `pwd` | Show current working directory |
| `ls` | List files and directories |
| `cd <dir>` | Change directory |
| `cd ..` | Move one level up |
| `cd ~` | Go to current user's home directory |
| `whoami` | Show current logged-in user |
| `uname` | Show system/kernel information |
| `mkdir <name>` | Create a directory |
| `ssh user@host` | Connect to a remote server using SSH |
| `git clone <url>` | Clone a remote Git repository |

## Interview Tip

For Day-1 interviews, don't just memorize commands. Be ready to explain **what the command does, why you use it, and where you would use it on a cloud Linux VM**.

The source session specifically introduced these commands: `ls`, `cd`, `cd ..`, `pwd`, `whoami`, `uname`, and `mkdir`, along with SSH and Git clone practice. fileciteturn0file0L189-L217
