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
