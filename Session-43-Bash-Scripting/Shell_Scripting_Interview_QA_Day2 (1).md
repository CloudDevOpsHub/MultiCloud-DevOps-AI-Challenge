**🚀 Day-2 of Shell Scripting Automation for Batch-44 | 21th Aug 2026 | 8:00 AM IST | Live Session**

## Key Outcomes

The session was a comprehensive introduction to **Shell Scripting** conducted on a cloud environment (GCP), covering foundational concepts through live demonstrations and real-world automation scenarios. Participants learned what a shell is, the types of shells available, how to write and execute basic shell scripts, and how to apply scripting to practical DevOps use cases including disk space monitoring, log cleanup, and service health checks. The instructor emphasized that **Bash** is the most popular and widely used shell for Linux automation, and that shell scripting is a critical skill for DevOps interviews and production environments. 

## Session Context & Setup

·       **Topic:** Shell Scripting — from basics to real-time automation use cases 

·       **Platform:** Live Zoom session; participants encouraged to turn on cameras and write full names for certificate generation 

·       **Cloud environment used:** GCP (Google Cloud Platform); participants were advised to have a GCP or AWS account to follow along practically 

·       **Resource reference:** Instructor's website/blog contains step-by-step guides for GCP account creation, budget alarm setup (GCP, AWS, Azure), and VM creation — participants directed to search these articles 

·       **GitHub repository:** A Day 43 Shell Scripting file was shared containing all commands and scripts used during the session 

·       **Certificate note:** Participants reminded to update their Zoom profile with their full, correctly spelled names so certificates are generated accurately 

## Shell & Shell Scripting — Core Concepts

### What is a Shell?

·       The **shell** is the outer layer of the operating system — it sits between the user/terminal and the kernel 

·       The **kernel** is the heart of the OS, closely connected to hardware (CPU, memory, disk); it is the lowest-level component 

·       User commands flow: **Terminal → Shell → Kernel → Hardware** 

·       Some commands (e.g., wget, cat, vi) are user-level/external commands and run slightly slower than kernel-level operations 

### Types of Shells

Participants identified the following shell types during discussion: 

·       **Bash (Bourne Again Shell)** — most popular, available across all Linux flavors, used heavily for automation; dominant since the 1990s 

·       **KSH (Korn Shell)** — Unix-based

·       **CSH / C Shell** — syntax similar to C programming language; suited for low-level scripting; harder to use, less popular 

·       **TCSH** — advanced version of Unix Shell Scripting 

·       **ZSH** — also mentioned by participants 

·       **SH (original shell script)** — origins traced to around 1977 

·       Interview tip: Know the names and key differences; TCSH full form is not commonly asked but awareness is expected 

### What is Shell Scripting?

·       **Definition:** A series of Linux commands written in one file and executed in order 

·       File extension: .sh (e.g., rita.sh, 1.sh, monitor.sh) 

·       The .sh extension signals that the file is a shell script 

·       **Shebang line** (#!/bin/bash) placed at the top of the file tells the OS which shell to use to execute the script 

·       The # (pound/hash sign) is a comment character; the shebang is a special comment that informs the machine which shell interpreter to invoke 

·       Best practice: always include the shebang; it is not mandatory but strongly recommended 

### Benefits of Shell Scripting

·       **Automation:** Eliminates need to manually execute repetitive commands; scripts can run at scheduled times (e.g., 2 AM, 3 AM) without human intervention 

·       **Reusability:** Write once, reuse across multiple executions and environments 

·       **Reduced manual errors:** Automation reduces human mistakes in repetitive tasks 

·       **Speed and efficiency:** Faster execution than manual steps; reduces team dependency 

·       **Always available:** Automation runs even when the engineer is offline or unavailable 

·       **Real-world examples cited:** Bank transaction reprocessing, IRCTC nightly maintenance, log file cleanup, backup automation 

·       **Scope:** Shell scripting is for **Linux automation**; Python/Boto3 is for cloud (AWS) automation — these are separate use cases 

·       **Windows equivalent:** PowerShell scripts (.bat files) serve the same purpose on Windows 

## Writing and Executing Shell Scripts — Step-by-Step

### Creating a Script

1.      Open a file with VI editor: vi filename.sh 

2.      Add shebang on the first line: #!/bin/bash 

3.      Add optional comment block for documentation/beautification (e.g., # Monitoring Shell Script) 

4.      Write Linux commands line by line 

5.      Save and exit: Escape → \:wq 

### Running a Script

·       Command: ./filename.sh (dot-slash notation) 

·       **Permission error** is expected on first run — by default, newly created files do **not** have executable permission, even for the owner 

·       **Interview question:** Why doesn't the owner have execute permission by default? — Because default Linux policy does not grant executable permission automatically 

### Granting Executable Permission

·       Command: chmod +x filename.sh 

·       Verify with: ls -l — color of filename changes when executable permission is added 

·       **Best practice warning:** Do NOT use chmod 777 in production — this grants full read/write/execute to all users (owner, group, others), which is a security risk 

·       Correct approach: grant only the minimum required permissions 

### Key Commands Demonstrated

|     |
| --- |

Command

|     |
| --- |

Purpose

|     |
| --- |
|     |

df -h

|     |
| --- |

Check disk space   usage

|     |
| --- |
|     |

free

|     |
| --- |

Check memory usage

|     |
| --- |
|     |

ls

|     |
| --- |

List   files/directories

|     |
| --- |
|     |

mkdir

|     |
| --- |

Create directory

|     |
| --- |
|     |

touch

|     |
| --- |

Create a file

|     |
| --- |
|     |

cat

|     |
| --- |

View file contents

|     |
| --- |
|     |

echo

|     |
| --- |

Print output   (equivalent to print in Python/Java) 

|     |
| --- |
|     |

chmod +x

|     |
| --- |

Grant executable   permission

|     |
| --- |
|     |

./filename.sh

|     |
| --- |

Execute shell script

|     |
| --- |
|     |

clear

|     |
| --- |

Clear terminal   screen

|     |
| --- |
|     |

hostname

|     |
| --- |

Display machine   hostname

|     |
| --- |
|     |

ps

|     |
| --- |

Show running   processes

|     |
| --- |
|     |

who / w

|     |
| --- |

Show logged-in users   

|     |
| --- |
|     |

date

|     |
| --- |

Show current date   and time 

|   |
| - |

## Variables in Shell Scripting

·       **Declaring a variable:** varname="value" — no spaces around = 

·       Running the declaration alone produces no output; the value is stored silently 

·       **Calling a variable:** Use $varname (dollar sign prefix) 

·       **Printing a variable:** echo $varname 

·       **Interview question:** How do you call/reference a variable in a shell script? → Use the $ sign 

·       **Single quote vs double quote difference** (interview topic): Double quotes allow variable expansion ($ is interpreted); single quotes treat everything as a literal string 

·       **whoami command** can be used as a variable value to dynamically capture the current user 

### Read Command (User Input)

·       read varname — takes input from the user at runtime 

·       **Interview question from PayPal:** Which command is used to take user input from the command line? → read 

·       Demonstrated with an email input script: script prompts "Enter your email ID", user types value, script prints "Welcome [input] to the Linux Learning Session" 

## Real-Time Scripts Demonstrated

### Script 1 — Basic Hello World / Learning Script

·       Shebang + echo "This is a shell script" + echo "I am learning Linux Automation" 

·       Purpose: Validate basic script creation, permission granting, and execution flow

### Script 2 — Variable Usage

·       Declares a variable (somewhere="value") and prints it using echo $varname 

·       Demonstrates how whoami output can be embedded: echo "My name is $(whoami)" or echo "My name is $USER" 

### Script 3 — System Monitoring Script

·       Commands included: hostname, internal IP display, ps, free, df 

·       Output: hostname, IP address, running processes, memory info, disk usage 

·       Described as a **monitoring script** — foundational for real-world health checks

### AI-Generated Script (ChatGPT Demo)

·       Instructor took the basic monitoring script and prompted ChatGPT: *"Script written by junior engineer. I am 5-year experience person. Update and Beautify this script."* 

·       ChatGPT output included: color-coded output, labeled sections (hostname, IP address, OS, kernel version), structured formatting 

·       Key technique used: cat > filename.sh << EOF ... EOF — heredoc syntax to create a file from command line when VI editor is unavailable 

·       **Important line highlighted:** The cat with redirect and EOF allows creating a file and writing content in one command — useful when AI-generated scripts can't be pasted directly into VI 

·       Script execution produced color-coded terminal output with hostname, IP, memory, disk size 

### Script 4 — System Info Report (Student Exercise)

·       Task: Write a script that shows date/time, lists logged-in users, shows running processes, and redirects all output to a file 

·       Solution approach demonstrated: 

·       date >> info.txt — append current date/time to file

·       w >> info.txt — append logged-in users

·       ps >> info.txt — append running processes

·       Use >> (append redirect) not > (overwrite) to accumulate all output in one file 

·       **Interview note:** Use >> for appending multiple outputs to the same file; > would overwrite 

## Real-Time Automation Scenarios

### Scenario 1 — Disk Space Monitoring Script

**Situation:** Server has 100 GB disk; if usage reaches 80%, alert must be triggered before space runs out. 

**Script logic explained:** 

·       Set threshold variable: LIMIT=80

·       Check disk usage of root / with: df /

·       Extract the percentage value using awk '{print $5}' (5th column of df output) 

·       Strip the % symbol using tr -d '%' 

·       **Condition:** if [ $CURRENT\_USAGE -gt $LIMIT ] → print warning 

·       Tested by changing limit to 10% to trigger the warning message in a live demo 

·       **tail -1** used to get only the last line of df output (the root filesystem line) 

·       **pipe (|)** used to chain df output into awk and tr 

### Scenario 2 — Log File Cleanup Script

**Situation:** Application generates log files daily; after several months, old files consume disk space; requirement is to delete files older than 7 days. 

**Script logic explained:** 

·       Define variable: logDirectory=/var/logs

·       Use find command: find $logDirectory -name "\*.log" -mtime +7 -exec rm {} \\;

·       -name "\*.log" — targets only .log extension files 

·       -mtime +7 — files with modification time older than 7 days 

·       -exec rm — deletes matched files 

·       Output confirmation: "Old files have been deleted successfully" 

·       **Production caution:** Never run delete scripts directly in production without manager approval; confirm the retention period (commonly 30, 60, or 90 days in real environments) 

·       **Force delete option:** rm -f can be used to forcefully delete without prompts, but requires explicit approval 

·       **Dry run concept** (raised by participant): Before running destructive scripts in production, use a dry-run or test environment (UAT) to simulate behavior without actual deletion 

### Scenario 3 — Service Health Check Script

**Situation:** Check if a service (e.g., Nginx, Docker, Jenkins) is running; alert if it is not; can be scheduled via cron job every 1 hour. 

**Script logic explained:** 

·       Define variable: SERVICE=nginx

·       Check status: systemctl is-active --quiet $SERVICE

·       --quiet flag suppresses terminal output, running the check silently in the background 

·       **Condition:** If active → print "Service is running"; if not → print "Service is not running" and optionally restart 

·       **Cron job integration:** Script can be scheduled to run every hour, every 6 hours, etc., for continuous monitoring 

## Colorized Output in Shell Scripts

·       Shell scripts can display output in different colors using **ANSI escape codes** 

·       Color codes embedded in echo -e statements allow green (success), red (error/warning), orange (caution) output 

·       Demonstrated in a colorized script where borders and section labels were rendered in different colors 

·       **Interview relevance:** Interviewer may ask about single vs double quotes in echo with color codes — double quotes allow $ variable expansion; single quotes do not 

·       Practical use: Makes monitoring script output more readable and visually scannable in terminals

## Interview Questions Highlighted

Throughout the session, the instructor flagged the following as commonly asked interview topics: 

·       **What is a shell script?** → Series of Linux commands in one file, executed in order 

·       **Types of shells?** → Bash, KSH, CSH, TCSH, ZSH, SH 

·       **Which shell is most popular?** → Bash 

·       **How to give executable permission?** → chmod +x filename 

·       **Why does a file owner not have execute permission by default?** → Default Linux policy does not include it 

·       **How to take user input in a shell script?** → read command (asked at PayPal) 

·       **How to call a variable?** → $variablename 

·       **Difference between single quote and double quote in echo?** → Double quotes expand variables; single quotes treat everything as literal 

·       **What is && vs ||?** → Logical AND (both conditions must be true) vs Logical OR (either condition satisfies) 

·       **Can you run interactive commands (like vi, top) inside a shell script?** → No; interactive commands cannot be executed within shell scripts 

·       **What is a dry run in shell scripting?** → Simulating script behavior without executing actual changes; important for production safety 

·       **How to append output to a file?** → Use >> (double redirect) 

·       **Difference between > and >>?** → > overwrites; >> appends 

## Tools, Best Practices & DevOps Integration

·       **Shell scripting vs Python/Boto3:** Shell scripting is for Linux/OS-level automation; Python with Boto3 is for cloud (AWS) API automation — different use cases, not interchangeable 

·       **ChatGPT for scripting:** Useful for beautifying or upgrading scripts; however, over-reliance risks being caught in interviews — understand the code, not just copy-paste 

·       **Centralized script storage:** Best practice is to store all automation scripts in a centralized Git repository (GitHub, Azure Repos, etc.) so team members can access, modify, and version-control them 

·       **Cron jobs:** Used to schedule shell scripts at regular intervals (hourly, daily, weekly) for automated execution without manual intervention 

·       **Real-world monitoring tools:** Tools like Prometheus, Grafana, and CloudWatch handle real-time monitoring in production; shell scripting knowledge is still required for interviews and foundational understanding 

·       **GCP Cloud Shell:** Demonstrated creating a VM creation script stored in Cloud Shell for reuse — avoids reconfiguring VM settings manually each time 

·       **Vim installation note:** If vi is not found, install with apt install vim 

## Student Exercise & Assignment

**Assignment given at end of session:** 

·       Write a shell script that:

a.      Shows current **date and time**

b.      Lists all currently **logged-in users**

c.       Shows all **running processes**

d.      Redirects all output into a single file (e.g., info.txt)

·       Commands to use: date, w or who, ps, with >> redirect operator 

·       Participants were asked to submit scripts via Zoom chat for review 

**Additional practice recommended:**

·       Execute the colorized monitoring script and share output screenshot 

·       Rewrite the AI-generated script manually without copy-pasting to internalize concepts 

·       Complete the **100 Tasks Challenge** available in the course GitHub repository — covers Day 1 through advanced topics including Python, Terraform, Ansible 

## Announcements & Administrative Notes

·       **Batch 45** is expected to start around **9th–10th September** 

·       **TCS Walk-in opportunity** shared: positions include Database Engineer, Windows, Ansible, Container Admin (Kubernetes); participants advised to create an iPortal account and attend the walk-in on Saturday 

·       **Wipro openings** also shared via WhatsApp group 

·       **Job referrals:** Participants with TCS employee contacts encouraged to seek referrals 

·       **LinkedIn profiles:** Participants encouraged to optimize LinkedIn profiles using the README file templates in the course repository; posting GitHub repo links on LinkedIn increases visibility 

·       **GCP account creation:** Creating multiple free-tier accounts is flagged by Google and will not work; participants advised to use a single genuine account 

·       **Course website:** Contains articles on GCP account creation, VM setup, budget alarms, assignment submission guides — accessible via the instructor's blog/website


---

# 🎯 20 Shell Scripting Interview Questions & Answers

These questions are based on the topics covered in this session and are suitable for DevOps/Linux interviews.

## 1. What is Shell Scripting?

**Answer:**  
Shell scripting is the process of writing a series of Linux commands in a file and executing them together. It is mainly used to automate repetitive Linux and system administration tasks.

**Example:**
```bash
#!/bin/bash
echo "Hello DevOps"
hostname
date
```

---

## 2. What is a Shell?

**Answer:**  
A shell is an interface between the user and the operating system kernel. When we execute a command, the shell interprets the command and passes the required operation to the operating system.

A simple flow is:

```text
User → Terminal → Shell → Kernel → Hardware
```

---

## 3. What is the difference between Shell and Shell Script?

**Answer:**  
A **Shell** is the command interpreter, such as Bash.

A **Shell Script** is a file containing multiple shell/Linux commands that can be executed together.

For example:

```bash
bash
```

is a shell, while:

```bash
monitor.sh
```

can be a shell script.

---

## 4. What is the purpose of `#!/bin/bash`?

**Answer:**  
`#!/bin/bash` is called a **shebang**. It tells the operating system that the script should be executed using the Bash interpreter.

Example:

```bash
#!/bin/bash
echo "Running with Bash"
```

It is strongly recommended to include it at the beginning of a script.

---

## 5. How do you give execute permission to a shell script?

**Answer:**

```bash
chmod +x script.sh
```

Then execute it using:

```bash
./script.sh
```

We can verify the permission using:

```bash
ls -l script.sh
```

---

## 6. Why can't we directly execute a newly created script using `./script.sh`?

**Answer:**  
Because a newly created file normally does not have the executable permission.

For example:

```bash
-rw-r--r-- script.sh
```

We need to add execute permission:

```bash
chmod +x script.sh
```

After that:

```bash
./script.sh
```

will work.

---

## 7. What is the difference between `>` and `>>`?

**Answer:**

`>` overwrites the existing file.

```bash
date > info.txt
```

`>>` appends output to the existing file.

```bash
date >> info.txt
```

For example, when multiple commands need to write into the same report file, `>>` is useful.

---

## 8. How do you take user input in Shell Scripting?

**Answer:**  
We use the `read` command.

Example:

```bash
#!/bin/bash

echo "Enter your name:"
read name

echo "Welcome $name"
```

The `read` command waits for the user to enter a value.

---

## 9. What is a variable in Shell Scripting?

**Answer:**  
A variable stores a value that can be reused in the script.

Example:

```bash
name="Vikas"
echo $name
```

Important: there should be **no spaces around `=`**.

Correct:

```bash
name="Vikas"
```

Incorrect:

```bash
name = "Vikas"
```

---

## 10. What is the difference between single quotes and double quotes?

**Answer:**  
Double quotes allow variable expansion, while single quotes treat the content as a literal string.

Example:

```bash
name="Vikas"

echo "Hello $name"
```

Output:

```text
Hello Vikas
```

With single quotes:

```bash
echo 'Hello $name'
```

Output:

```text
Hello $name
```

---

## 11. What is a pipe `|` in Linux?

**Answer:**  
A pipe sends the output of one command as input to another command.

Example:

```bash
df -h | grep /dev
```

Another example:

```bash
ps -ef | grep nginx
```

This is very useful when building monitoring and troubleshooting scripts.

---

## 12. What is `&&` and `||`?

**Answer:**

`&&` means the second command runs only when the first command succeeds.

```bash
mkdir test && echo "Directory created"
```

`||` means the second command runs when the first command fails.

```bash
mkdir test || echo "Directory creation failed"
```

These operators are commonly used for simple command-level error handling.

---

## 13. What is the difference between `chmod +x` and `chmod 777`?

**Answer:**  

```bash
chmod +x script.sh
```

adds executable permission while preserving the existing read/write permissions.

But:

```bash
chmod 777 script.sh
```

grants read, write, and execute permissions to owner, group, and others.

Using `777` unnecessarily can create a security risk. In production, follow the principle of least privilege.

---

## 14. How do you check disk usage from a shell script?

**Answer:**  
The commonly used command is:

```bash
df -h
```

For checking a specific filesystem:

```bash
df -h /
```

We can combine it with tools such as `awk` and `tr` to extract the percentage and use it in an alert condition.

Example:

```bash
USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')
echo "Disk Usage: $USAGE%"
```

---

## 15. How do you check memory usage in Linux?

**Answer:**  
We can use:

```bash
free -h
```

For example:

```bash
free -h
```

can be included in a system monitoring script along with `df`, `ps`, and `hostname`.

---

## 16. How do you check whether a Linux service is running?

**Answer:**  
We can use:

```bash
systemctl is-active --quiet nginx
```

Example:

```bash
if systemctl is-active --quiet nginx
then
    echo "Nginx is running"
else
    echo "Nginx is not running"
fi
```

This can also be scheduled using a cron job for regular health checks.

---

## 17. How do you delete log files older than 7 days?

**Answer:**  
The `find` command can be used.

Example:

```bash
find /var/logs -name "*.log" -mtime +7 -exec rm {} \;
```

This searches for `.log` files older than 7 days and deletes them.

In production, always confirm the retention requirement and test the command before performing destructive actions.

---

## 18. What is a dry run in Shell Scripting?

**Answer:**  
A dry run means checking what a script is going to do without actually making the destructive change.

For example, before deleting files:

```bash
find /var/logs -name "*.log" -mtime +7
```

We can first verify the files returned by `find`.

Only after validation should we add the delete operation.

This is especially important in production environments.

---

## 19. How do you schedule a shell script?

**Answer:**  
We can use **cron** to schedule shell scripts.

Example:

```bash
crontab -e
```

To run a script every hour:

```bash
0 * * * * /home/devops/monitor.sh
```

The script can then perform tasks such as disk checks, service checks, backups, or cleanup.

---

## 20. Shell Scripting vs Python/Boto3 — when would you use each?

**Answer:**  

**Shell scripting** is very useful for Linux and OS-level automation:

```text
Files
Processes
Services
Disk
Memory
Logs
Linux administration
```

**Python/Boto3** is better when interacting with AWS APIs:

```text
EC2
S3
IAM
Lambda
CloudWatch
RDS
```

A practical DevOps engineer may use both together depending on the requirement.

---

# 🔥 Scenario-Based Shell Scripting Interview Questions & Answers

These scenarios are closer to what an interviewer may ask a real DevOps engineer.

## Scenario 1: Disk is 85% full. What will you do?

**Answer:**

First, identify the filesystem consuming space:

```bash
df -h
```

Then identify large directories/files:

```bash
du -sh /* 2>/dev/null
```

For a specific directory:

```bash
du -sh /var/*
```

If logs are consuming the space, identify old logs:

```bash
find /var/log -name "*.log" -mtime +7
```

I would not immediately delete files in production. I would first verify retention requirements and take the required approval.

---

## Scenario 2: You need to alert when `/` disk usage crosses 80%.

**Answer:**

I would write a script like:

```bash
#!/bin/bash

LIMIT=80
USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')

if [ "$USAGE" -gt "$LIMIT" ]; then
    echo "WARNING: Disk usage is ${USAGE}%"
else
    echo "Disk usage is normal: ${USAGE}%"
fi
```

This script can later be connected to email, Slack, monitoring tools, or cron.

---

## Scenario 3: Jenkins is down. How will your script detect it?

**Answer:**

Use `systemctl`:

```bash
#!/bin/bash

SERVICE=jenkins

if systemctl is-active --quiet "$SERVICE"
then
    echo "Jenkins is running"
else
    echo "Jenkins is DOWN"
fi
```

For production, I would usually integrate this with monitoring/alerting rather than depending only on a shell script.

---

## Scenario 4: Your application generates thousands of `.log` files. How will you clean old logs safely?

**Answer:**

First perform a dry run:

```bash
find /var/log/myapp -name "*.log" -mtime +30
```

Review the files.

After confirming the retention requirement:

```bash
find /var/log/myapp -name "*.log" -mtime +30 -exec rm {} \;
```

I would also make the retention period a variable:

```bash
RETENTION=30

find /var/log/myapp -name "*.log" -mtime +$RETENTION -exec rm {} \;
```

---

## Scenario 5: A script works manually but fails from cron. What will you check?

**Answer:**

I would check:

1. Absolute paths are being used.
2. Environment variables are available in cron.
3. The script has executable permission.
4. The correct user is running the cron job.
5. Output/error is redirected to a log file.
6. File permissions are correct.

For example:

```bash
0 * * * * /home/devops/monitor.sh >> /home/devops/monitor.log 2>&1
```

This captures both standard output and errors.

---

## Scenario 6: You need to create a shell script but `vi` is not installed. What will you do?

**Answer:**

I can create the file directly from the terminal using a heredoc:

```bash
cat > monitor.sh <<'EOF'
#!/bin/bash

echo "System Monitoring"
hostname
date
free -h
df -h
EOF
```

Then:

```bash
chmod +x monitor.sh
./monitor.sh
```

This is also useful in cloud terminals and automation environments.

---

## Scenario 7: Your script deletes the wrong files in production. How could you prevent this?

**Answer:**

I would add safety controls:

- Validate the directory before running.
- Use variables instead of hard-coded paths.
- Print matched files before deletion.
- Test in UAT first.
- Add a dry-run option.
- Use the correct retention period.
- Avoid dangerous commands such as unrestricted `rm -rf`.
- Require approval for destructive production changes.

Example dry-run approach:

```bash
find "$LOG_DIR" -name "*.log" -mtime +"$RETENTION"
```

Then perform deletion only after validation.

---

## Scenario 8: You have to generate a daily server report.

**Answer:**

I would combine multiple Linux commands into one script:

```bash
#!/bin/bash

REPORT="server_report.txt"

echo "===== SERVER REPORT =====" > "$REPORT"
date >> "$REPORT"

echo "===== HOSTNAME =====" >> "$REPORT"
hostname >> "$REPORT"

echo "===== MEMORY =====" >> "$REPORT"
free -h >> "$REPORT"

echo "===== DISK =====" >> "$REPORT"
df -h >> "$REPORT"

echo "===== PROCESSES =====" >> "$REPORT"
ps -ef >> "$REPORT"
```

Then schedule it using cron.

---

## Scenario 9: The interviewer asks you to explain why `>>` is used in a monitoring report.

**Answer:**

I use `>>` because I want to append the output of multiple commands into the same file.

For example:

```bash
date >> report.txt
hostname >> report.txt
df -h >> report.txt
```

If I use `>` every time, the previous output will be overwritten.

---

## Scenario 10: A server has high CPU. How will you investigate using shell commands?

**Answer:**

First, I would inspect running processes:

```bash
ps -ef
```

For a more focused check:

```bash
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head
```

I would identify the process consuming the highest CPU and then investigate the application/service logs and recent changes.

For production systems, I would also use tools such as CloudWatch, Prometheus, Grafana, or another monitoring platform.

---

## Scenario 11: You need to find whether Nginx is running.

**Answer:**

One approach is:

```bash
systemctl is-active nginx
```

Or:

```bash
ps -ef | grep nginx
```

For scripting, `systemctl is-active --quiet` is cleaner when Nginx is managed as a systemd service.

---

## Scenario 12: Your script should print the current username dynamically. How?

**Answer:**

Use:

```bash
whoami
```

or:

```bash
echo "$USER"
```

Another useful pattern is command substitution:

```bash
echo "Current user: $(whoami)"
```

---

## Scenario 13: A script accepts an email ID from a user. How would you implement it?

**Answer:**

```bash
#!/bin/bash

echo "Enter your email ID:"
read email

echo "Welcome $email to the Linux Learning Session"
```

For production use, I would also validate the input before using it.

---

## Scenario 14: Your disk monitoring script always shows `85%`, even when the disk is not actually 85%. What would you check?

**Answer:**

I would first inspect the raw command output:

```bash
df /
```

Then verify the pipeline:

```bash
df / | tail -1
df / | tail -1 | awk '{print $5}'
df / | tail -1 | awk '{print $5}' | tr -d '%'
```

This helps identify whether the problem is in parsing, whitespace, filesystem selection, or the command logic.

---

## Scenario 15: An interviewer asks you to create a basic Linux health-check script in 5 minutes.

**Answer:**

I would create something simple and readable:

```bash
#!/bin/bash

echo "===== SERVER HEALTH CHECK ====="

echo "Hostname:"
hostname

echo "Date:"
date

echo "Memory:"
free -h

echo "Disk:"
df -h

echo "Running Processes:"
ps -ef | head

echo "Current User:"
whoami
```

Then:

```bash
chmod +x healthcheck.sh
./healthcheck.sh
```

The important thing is to keep the script readable and explain every command.

---

## Scenario 16: Your script should print a warning only when disk usage is greater than 80%.

**Answer:**

```bash
#!/bin/bash

LIMIT=80
USAGE=$(df / | tail -1 | awk '{print $5}' | tr -d '%')

if [ "$USAGE" -gt "$LIMIT" ]; then
    echo "WARNING: Disk usage is ${USAGE}%"
fi
```

The key concept here is using a variable, extracting the percentage, and comparing it using an `if` condition.

---

## Scenario 17: How would you run a health-check script every 6 hours?

**Answer:**

Add a cron entry:

```bash
0 */6 * * * /home/devops/healthcheck.sh
```

This runs the script at minute `0` every 6 hours.

---

## Scenario 18: Your service is down and you want the script to restart it automatically. What would you do?

**Answer:**

Example:

```bash
#!/bin/bash

SERVICE=nginx

if systemctl is-active --quiet "$SERVICE"
then
    echo "$SERVICE is running"
else
    echo "$SERVICE is down"
    systemctl restart "$SERVICE"
fi
```

In production, I would add proper logging, alerting, permissions, and failure handling. I would also confirm whether automatic restart is approved for that service.

---

## Scenario 19: You are reviewing a junior engineer's script. It uses `chmod 777`. What will you suggest?

**Answer:**

I would explain that `chmod 777` gives read, write, and execute permission to everyone.

Instead, I would grant only the permission required by the use case.

For an executable script:

```bash
chmod +x script.sh
```

If specific owner/group permissions are needed, use a more restrictive mode such as:

```bash
chmod 750 script.sh
```

The exact permission should depend on the application's requirements.

---

## Scenario 20: Your manager asks, "Why do we still need Shell Scripting when we already have Prometheus, Grafana and CloudWatch?"

**Answer:**

I would explain that these tools serve different purposes.

Monitoring platforms provide centralized metrics, dashboards, alerting, and observability.

Shell scripting is still useful for:

```text
Linux automation
File operations
Log cleanup
Service checks
Server health checks
Deployment helpers
Cron-based tasks
Troubleshooting
```

For example, Prometheus may tell us that CPU is high, while a shell script can quickly collect local process, disk, memory, and service information from the server.

---

# 🧠 Interview Tip: How to Answer Scenario Questions

When an interviewer gives you a production scenario, don't jump directly into a command.

A strong answer usually follows this flow:

```text
1. Understand the problem
2. Check current system state
3. Collect required information
4. Identify the root cause
5. Take the safest corrective action
6. Validate the result
7. Add monitoring/automation to prevent recurrence
```

For destructive operations such as deleting files, restarting services, or modifying production systems, always mention validation, testing, permissions, and rollback/safety considerations.

---

# 🚀 Quick Revision Commands

```bash
hostname
whoami
date
df -h
free -h
ps -ef
who
w
ls
mkdir
touch
cat
echo
clear
chmod +x script.sh
./script.sh
```

Useful scripting commands:

```bash
read
if
for
while
awk
grep
tr
find
tail
```

Useful operators:

```text
|    Pipe output
>    Overwrite file
>>   Append to file
&&   Run next command when previous succeeds
||   Run next command when previous fails
```

---

# ✅ Final Interview Preparation Checklist

- Understand what a shell and kernel are.
- Know Bash and other common shell types.
- Understand the shebang.
- Know how to create and execute a script.
- Understand Linux permissions.
- Be comfortable with variables and `read`.
- Know single vs double quotes.
- Understand pipes and redirection.
- Practice `df`, `free`, `ps`, `systemctl`, `find`, `awk`, and `grep`.
- Understand cron jobs.
- Practice disk monitoring.
- Practice log cleanup.
- Practice service health checks.
- Always discuss production safety before destructive operations.
- Be able to explain every line of the script you write.

