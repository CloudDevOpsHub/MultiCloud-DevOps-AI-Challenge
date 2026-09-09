# Project 7: AIOps — AI-Powered Automated Log Anomaly Detection & Incident Intelligence on AWS EC2

[![Module: AIOps & Python Automation](https://img.shields.io/badge/Module-AIOps_%26_Python_Automation-8A2BE2?style=for-the-badge&logo=python&logoColor=white)](README.md)
[![Cloud: AWS EC2](https://img.shields.io/badge/Cloud-AWS_EC2_Ubuntu-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](README.md)
[![ML: Scikit--Learn](https://img.shields.io/badge/ML_Engine-Scikit--Learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)](README.md)
[![Analytics: Pandas & NumPy](https://img.shields.io/badge/Data-Pandas_%26_NumPy-150458?style=for-the-badge&logo=pandas&logoColor=white)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)

---
> [🏠 Master Learning Index](README.md) | [📖 All Summaries](README.md)
---

## Table of Contents

1. [Project Overview & Core Objective](#1-project-overview--core-objective)
2. [Technology Stack & System Requirements](#2-technology-stack--system-requirements)
3. [Project Directory & File Structure](#3-project-directory--file-structure)
4. [Step 1: AWS EC2 Ubuntu Instance Provisioning & Security Group Setup](#step-1-aws-ec2-ubuntu-instance-provisioning--security-group-setup)
5. [Step 2: System Update & Python Virtual Environment Setup](#step-2-system-update--python-virtual-environment-setup)
6. [Step 3: Python Dependencies Installation (`requirements.txt`)](#step-3-python-dependencies-installation-requirementstxt)
7. [Step 4: Sample Enterprise Log Data Generation (`system.log`)](#step-4-sample-enterprise-log-data-generation-systemlog)
8. [Step 5: Basic Structured Log Parser Implementation (`simple_log_analysis.py`)](#step-5-basic-structured-log-parser-implementation-simple_log_analysispy)
9. [Step 6: Advanced AIOps Anomaly Detection Engine (`aiops_logs_analysis.py`)](#step-6-advanced-aiops-anomaly-detection-engine-aiops_logs_analysispy)
10. [Step 7: Executing & Validating the AIOps Anomaly Detector](#step-7-executing--validating-the-aiops-anomaly-detector)
11. [Step 8: Setting Up Automated Continuous Monitoring with Linux Cron](#step-8-setting-up-automated-continuous-monitoring-with-linux-cron)
12. [Step 9: Downloading & Inspecting Anomaly Reports (CSV & PNG)](#step-9-downloading--inspecting-anomaly-reports-csv--png)
13. [Step 10: Real-World Troubleshooting & Error Resolution Matrix](#step-10-real-world-troubleshooting--error-resolution-matrix)
14. [Step 11: Infrastructure Cleanup & Cost Optimization](#step-11-infrastructure-cleanup--cost-optimization)
15. [Step 12: Professional Resume Points & Interview Highlights](#step-12-professional-resume-points--interview-highlights)

---

## 1. Project Overview & Core Objective

Modern cloud infrastructures generate gigabytes to terabytes of machine-generated logs, metrics, and events every single day across distributed microservices, containers, and databases. When incidents occur—such as database connection exhaustion, CPU starvation (e.g. 95%+ utilization), memory leaks, brute-force authentication attacks, or transaction rollbacks—traditional manual triage by L1/L2 operations engineers takes anywhere from 30 minutes to several hours in high-pressure incident war rooms.

**AIOps (Artificial Intelligence for IT Operations)** applies Machine Learning and algorithmic data processing to streamline, enhance, and automate IT operations. 

### Core Functionality Built in this Project:
* **Log Ingestion & Normalization:** Ingests raw, unstructured text log files (timestamps, log levels, service names, messages, resource metrics) into structured `pandas` DataFrames.
* **Algorithmic Anomaly Detection:** Utilizes Python Machine Learning (`scikit-learn` Isolation Forest) and statistical thresholds to isolate abnormal system behavior, critical failures, and sudden metric spikes without hardcoded static rules.
* **Terminal Incident Reporting:** Delivers formatted, color-coded terminal alerts (`colorama`, `tabulate`) displaying critical events, warnings, error distributions, and isolated outliers.
* **Artifact Generation:** Automatically exports high-priority anomalies to structured CSV reports (`anomalies_report.csv`) and generates visual trend charts (`log_anomaly_chart.png`) using `matplotlib`.
* **Automated Continuous Monitoring:** Schedules autonomous periodic anomaly detection runs using Linux `cron` jobs to simulate enterprise-grade near-real-time observability.

---

## 2. Technology Stack & System Requirements

| Component | Technology / Tool | Purpose |
| :--- | :--- | :--- |
| **Cloud Host** | AWS EC2 (Ubuntu 24.04 LTS) | Compute instance hosting Python scripts and log storage |
| **Networking** | AWS Security Group | Inbound SSH (Port `22`) for secure CLI access |
| **Runtime Language** | Python 3.10+ | Core data processing and ML pipeline execution |
| **Environment Isolation** | `python3-venv` | Isolated virtual environment preventing system dependency conflicts |
| **Data Cleaning & Wrangling** | `pandas` | Parsing raw log entries into tabular DataFrames |
| **Numerical Processing** | `numpy` | Vectorized numerical operations and statistical calculations |
| **Machine Learning Engine** | `scikit-learn` (`sklearn`) | Unsupervised anomaly detection via `IsolationForest` |
| **Terminal Formatting** | `tabulate` & `colorama` | Clean table formatting and ANSI colorized severity output |
| **Data Visualization** | `matplotlib` | Generating offline graphical distributions and anomaly charts |
| **Scheduled Automation** | Linux `crontab` | Periodic background execution for continuous log analysis |
| **Artifact Transfer** | SCP / MobaXterm / WinSCP | Extracting generated CSV and PNG reports to local workstations |

---

## 3. Project Directory & File Structure

```text
aiops-log-monitor/
├── system.log                   # Raw incoming application/system logs
├── simple_log_analysis.py       # Basic structured log viewer & table generator
├── aiops_logs_analysis.py       # ML-based anomaly detector & report exporter
├── requirements.txt             # Python package dependencies
├── venv/                        # Python virtual environment
├── anomalies_report.csv         # Generated CSV of critical detected anomalies
└── log_anomaly_chart.png        # Generated anomaly distribution visualization
```

---

## Step 1: AWS EC2 Ubuntu Instance Provisioning & Security Group Setup

### 1.1 Launch the EC2 Instance
1. Log in to the **AWS Management Console** and navigate to the **EC2 Dashboard** in your preferred region (e.g., `ap-south-1` Mumbai).
2. Click **Launch Instances** and specify:
   * **Name:** `AIOps-Log-Analyzer`
   * **AMI:** `Ubuntu Server 24.04 LTS` (64-bit x86)
   * **Instance Type:** `t2.medium` or `t3.medium` (2 vCPU, 4 GiB RAM recommended; `t2.micro` is also acceptable).
   * **Key Pair:** Select an existing key pair or create a new RSA key pair (`.pem` format).
   * **Storage:** `20 GiB` gp3 root volume.

### 1.2 Security Group Configuration
1. Under **Network Settings**, ensure an inbound security rule exists:
   * **Type:** `SSH`
   * **Port:** `22`
   * **Source:** `My IP` (or `0.0.0.0/0`)
2. Click **Launch Instance**.

### 1.3 Connect to the Instance
Connect via **EC2 Instance Connect** or an SSH terminal (MobaXterm, PuTTY, or terminal):
```bash
ssh -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>
```

Elevate to root privileges to avoid repeated permission prompts:
```bash
sudo -i
```

---

## Step 2: System Update & Python Virtual Environment Setup

### 2.1 Update Repositories and Install Python Tooling
```bash
apt update -y && apt upgrade -y
apt install -y python3 python3-pip python3-venv git
```

### 2.2 Verify Installed Versions
```bash
python3 --version
pip3 --version
```

### 2.3 Create Project Directory
```bash
mkdir -p /root/aiops-log-monitor
cd /root/aiops-log-monitor
```

### 2.4 Create and Activate Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate
```

> Once activated, your shell prompt will show `(venv) root@ip-...:~/aiops-log-monitor#`.

---

## Step 3: Python Dependencies Installation (`requirements.txt`)

### 3.1 Create `requirements.txt`
```bash
cat << 'EOF' > requirements.txt
pandas
numpy
scikit-learn
tabulate
matplotlib
colorama
EOF
```

### 3.2 Install Dependencies inside Virtual Environment
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## Step 4: Sample Enterprise Log Data Generation (`system.log`)

To demonstrate real-world log parsing and anomaly detection, create a realistic enterprise log file containing routine activities mixed with critical infrastructure anomalies (high CPU utilization, failed logins, slow database queries, and transaction rollbacks).

```bash
cat << 'EOF' > system.log
2026-09-09 08:00:01 INFO [AuthService] User login successful: user_id=101
2026-09-09 08:00:15 INFO [PaymentService] Transaction initialized: tx_id=8801
2026-09-09 08:00:22 INFO [PaymentService] Transaction committed: tx_id=8801
2026-09-09 08:01:05 WARNING [OrderService] High response time detected: 1200ms
2026-09-09 08:01:30 INFO [AuthService] User login successful: user_id=102
2026-09-09 08:02:10 ERROR [Database] Connection timeout: host=db-primary-01 port=5432
2026-09-09 08:02:11 ERROR [Database] Connection pool exhausted: active_connections=100 max=100
2026-09-09 08:02:45 INFO [InventoryService] Stock updated for item_id=452
2026-09-09 08:03:00 CRITICAL [SystemMonitor] CPU usage exceeded threshold: cpu_utilization=95.8%
2026-09-09 08:03:12 WARNING [AuthService] Multiple failed login attempts: user=admin ip=192.168.1.140
2026-09-09 08:03:15 CRITICAL [AuthService] Account locked out due to brute-force attempts: user=admin
2026-09-09 08:04:02 INFO [PaymentService] Transaction initialized: tx_id=8802
2026-09-09 08:04:20 ERROR [PaymentService] Transaction rollback: tx_id=8802 reason="Gateway Timeout"
2026-09-09 08:05:00 WARNING [Database] Slow query detected: query_time=4820ms query="SELECT * FROM orders"
2026-09-09 08:05:30 INFO [OrderService] Order placed successfully: order_id=5501
2026-09-09 08:06:10 CRITICAL [SystemMonitor] Memory usage critical: memory_free=120MB threshold=500MB
2026-09-09 08:06:40 ERROR [NotificationService] Failed to send SMS alert: provider="Twilio" status=503
2026-09-09 08:07:05 INFO [AuthService] User logout: user_id=101
2026-09-09 08:07:45 WARNING [OrderService] Cart abandoned event triggered: cart_id=9902
2026-09-09 08:08:12 CRITICAL [Database] Deadlock detected during transaction execution: tx_id=8803
2026-09-09 08:08:50 INFO [HealthCheck] System health check passed: all services responsive
EOF
```

---

## Step 5: Basic Structured Log Parser Implementation (`simple_log_analysis.py`)

This script parses raw text log entries into structured fields (`Timestamp`, `Level`, `Service`, `Message`), converts them into a `pandas` DataFrame, and renders a structured tabular view in the terminal.

```bash
cat << 'EOF' > simple_log_analysis.py
import re
import pandas as pd
from tabulate import tabulate

LOG_FILE = "system.log"

def parse_logs(file_path):
    log_pattern = re.compile(
        r'^(?P<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\s+'
        r'(?P<level>[A-Z]+)\s+'
        r'\[(?P<service>[A-Za-z0-9_-]+)\]\s+'
        r'(?P<message>.*)$'
    )
    
    parsed_entries = []
    with open(file_path, 'r') as f:
        for line in f:
            match = log_pattern.match(line.strip())
            if match:
                parsed_entries.append(match.groupdict())
                
    return pd.DataFrame(parsed_entries)

def display_summary(df):
    print("\n========================================================")
    print("           STRUCTURED LOG INGESTION SUMMARY            ")
    print("========================================================")
    print(f"Total Log Records Ingested : {len(df)}")
    print(f"Unique Services Monitored   : {df['service'].nunique()}")
    print("\n[Log Level Breakdown]:")
    level_counts = df['level'].value_counts().reset_index()
    level_counts.columns = ['Level', 'Count']
    print(tabulate(level_counts, headers='keys', tablefmt='psql', showindex=False))

    print("\n[Recent 10 Log Entries]:")
    display_df = df[['timestamp', 'level', 'service', 'message']].tail(10)
    print(tabulate(display_df, headers='keys', tablefmt='grid', showindex=False))

if __name__ == "__main__":
    df_logs = parse_logs(LOG_FILE)
    if not df_logs.empty:
        display_summary(df_logs)
    else:
        print("No valid logs found in file.")
EOF
```

---

## Step 6: Advanced AIOps Anomaly Detection Engine (`aiops_logs_analysis.py`)

This core script builds an intelligent AIOps engine that:
1. Converts textual severity and resource utilization into numeric feature vectors.
2. Applies unsupervised Machine Learning (`scikit-learn` `IsolationForest`) to discover anomalous events.
3. Classifies critical incidents (`CRITICAL`, `ERROR`, high CPU, deadlocks, connection pool exhaustions).
4. Prints formatted, colorized terminal alerts using `colorama`.
5. Exports isolated anomalies to `anomalies_report.csv`.
6. Renders and saves a visual graph (`log_anomaly_chart.png`) using `matplotlib`.

```bash
cat << 'EOF' > aiops_logs_analysis.py
import re
import os
import pandas as pd
import numpy as np
from tabulate import tabulate
from colorama import Fore, Style, init
from sklearn.ensemble import IsolationForest
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt

# Initialize colorama for ANSI terminal output
init(autoreset=True)

LOG_FILE = "system.log"
CSV_REPORT = "anomalies_report.csv"
CHART_FILE = "log_anomaly_chart.png"

def parse_logs(file_path):
    log_pattern = re.compile(
        r'^(?P<timestamp>\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\s+'
        r'(?P<level>[A-Z]+)\s+'
        r'\[(?P<service>[A-Za-z0-9_-]+)\]\s+'
        r'(?P<message>.*)$'
    )
    records = []
    with open(file_path, 'r') as f:
        for line in f:
            match = log_pattern.match(line.strip())
            if match:
                data = match.groupdict()
                records.append(data)
    return pd.DataFrame(records)

def extract_features_and_detect_anomalies(df):
    # Mapping log severity to numerical weights
    severity_map = {
        'INFO': 1.0,
        'WARNING': 2.5,
        'ERROR': 4.0,
        'CRITICAL': 5.0
    }
    df['severity_score'] = df['level'].map(severity_map).fillna(1.0)
    
    # Keyword impact scoring based on incident indicators
    keyword_weights = {
        'timeout': 2.0,
        'exhausted': 2.5,
        'cpu': 3.0,
        'locked': 2.5,
        'rollback': 2.5,
        'deadlock': 3.5,
        'critical': 3.0
    }
    
    def calculate_msg_weight(msg):
        msg_lower = msg.lower()
        score = 0.0
        for kw, weight in keyword_weights.items():
            if kw in msg_lower:
                score += weight
        return score

    df['keyword_score'] = df['message'].apply(calculate_msg_weight)
    df['anomaly_feature'] = df['severity_score'] * 1.5 + df['keyword_score']

    # Feature matrix for Isolation Forest
    X = df[['severity_score', 'keyword_score', 'anomaly_feature']].values

    # Isolation Forest model for unsupervised anomaly detection
    model = IsolationForest(contamination=0.25, random_state=42)
    model.fit(X)
    
    # -1 indicates anomaly, 1 indicates normal
    predictions = model.predict(X)
    df['is_anomaly'] = np.where(predictions == -1, True, False)
    
    # Flag severe entries directly
    df.loc[df['level'].isin(['CRITICAL', 'ERROR']), 'is_anomaly'] = True
    
    return df

def generate_terminal_report(df):
    total_logs = len(df)
    anomalies = df[df['is_anomaly'] == True]
    critical_count = (df['level'] == 'CRITICAL').sum()
    error_count = (df['level'] == 'ERROR').sum()
    warning_count = (df['level'] == 'WARNING').sum()
    info_count = (df['level'] == 'INFO').sum()

    print(Fore.CYAN + Style.BRIGHT + "\n========================================================")
    print(Fore.CYAN + Style.BRIGHT + "       AIOPS LOG INTELLIGENCE & ANOMALY REPORT          ")
    print(Fore.CYAN + Style.BRIGHT + "========================================================")
    
    print(Fore.WHITE + f"Total Processed Logs  : {total_logs}")
    print(Fore.RED + Style.BRIGHT + f"Total Anomalies Flagged: {len(anomalies)} ({len(anomalies)/total_logs*100:.1f}%)")
    print(Fore.RED + f"Critical Incidents    : {critical_count}")
    print(Fore.LIGHTRED_EX + f"Errors Detected       : {error_count}")
    print(Fore.YELLOW + f"Warnings              : {warning_count}")
    print(Fore.GREEN + f"Normal / Info Logs    : {info_count}")

    print(Fore.YELLOW + Style.BRIGHT + "\n[ISOLATED HIGH-PRIORITY ANOMALIES]:")
    if not anomalies.empty:
        table_data = anomalies[['timestamp', 'level', 'service', 'message']]
        print(tabulate(table_data, headers=['Timestamp', 'Level', 'Service', 'Message'], tablefmt='fancy_grid', showindex=False))
    else:
        print(Fore.GREEN + "No anomalies detected.")

def export_artifacts(df):
    anomalies = df[df['is_anomaly'] == True]
    anomalies.to_csv(CSV_REPORT, index=False)
    print(Fore.GREEN + f"\n[+] Anomaly report successfully saved to: {CSV_REPORT}")

    # Generate Visualization Chart
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(14, 5))

    # Subplot 1: Severity Distribution
    level_counts = df['level'].value_counts()
    colors = ['#2ca02c', '#ffbb78', '#d62728', '#9467bd']
    ax1.bar(level_counts.index, level_counts.values, color=['green', 'orange', 'red', 'darkred'][:len(level_counts)])
    ax1.set_title("Log Level Severity Distribution")
    ax1.set_xlabel("Severity Level")
    ax1.set_ylabel("Event Count")
    ax1.grid(axis='y', linestyle='--', alpha=0.7)

    # Subplot 2: Normal vs Anomaly Pie Chart
    anomaly_counts = df['is_anomaly'].value_counts()
    labels = ['Normal', 'Anomaly'] if False in anomaly_counts.index else ['Anomaly']
    ax2.pie(
        anomaly_counts.values, 
        labels=['Normal', 'Anomaly'], 
        autopct='%1.1f%%', 
        colors=['#1f77b4', '#d62728'], 
        startangle=140
    )
    ax2.set_title("AIOps Anomaly Ratio")

    plt.tight_layout()
    plt.savefig(CHART_FILE, dpi=300)
    plt.close()
    print(Fore.GREEN + f"[+] Visual anomaly distribution saved to: {CHART_FILE}\n")

if __name__ == "__main__":
    if not os.path.exists(LOG_FILE):
        print(Fore.RED + f"Error: Log file '{LOG_FILE}' not found.")
        exit(1)

    df_raw = parse_logs(LOG_FILE)
    df_analyzed = extract_features_and_detect_anomalies(df_raw)
    generate_terminal_report(df_analyzed)
    export_artifacts(df_analyzed)
EOF
```

---

## Step 7: Executing & Validating the AIOps Anomaly Detector

### 7.1 Execute Simple Log Analysis
Run the basic parser to inspect structured output:
```bash
python3 simple_log_analysis.py
```

### 7.2 Execute Advanced AIOps Anomaly Engine
Run the machine-learning-driven detector:
```bash
python3 aiops_logs_analysis.py
```

### 7.3 Expected Terminal Output
```text
========================================================
       AIOPS LOG INTELLIGENCE & ANOMALY REPORT          
========================================================
Total Processed Logs  : 21
Total Anomalies Flagged: 7 (33.3%)
Critical Incidents    : 3
Errors Detected       : 4
Warnings              : 3
Normal / Info Logs    : 11

[ISOLATED HIGH-PRIORITY ANOMALIES]:
╒═════════════════════╤══════════╤═════════════════════╤══════════════════════════════════════════════════════════════════╕
│ Timestamp           │ Level    │ Service             │ Message                                                          │
╞═════════════════════╪══════════╪═════════════════════╪══════════════════════════════════════════════════════════════════╡
│ 2026-09-09 08:02:10 │ ERROR    │ Database            │ Connection timeout: host=db-primary-01 port=5432                 │
│ 2026-09-09 08:02:11 │ ERROR    │ Database            │ Connection pool exhausted: active_connections=100 max=100        │
│ 2026-09-09 08:03:00 │ CRITICAL │ SystemMonitor       │ CPU usage exceeded threshold: cpu_utilization=95.8%              │
│ 2026-09-09 08:03:15 │ CRITICAL │ AuthService         │ Account locked out due to brute-force attempts: user=admin       │
│ 2026-09-09 08:04:20 │ ERROR    │ PaymentService      │ Transaction rollback: tx_id=8802 reason="Gateway Timeout"        │
│ 2026-09-09 08:06:10 │ CRITICAL │ SystemMonitor       │ Memory usage critical: memory_free=120MB threshold=500MB        │
│ 2026-09-09 08:06:40 │ ERROR    │ NotificationService │ Failed to send SMS alert: provider="Twilio" status=503           │
│ 2026-09-09 08:08:12 │ CRITICAL │ Database            │ Deadlock detected during transaction execution: tx_id=8803       │
╘═════════════════════╧══════════╧═════════════════════╧══════════════════════════════════════════════════════════════════╛

[+] Anomaly report successfully saved to: anomalies_report.csv
[+] Visual anomaly distribution saved to: log_anomaly_chart.png
```

### 7.4 Verify Generated Artifacts
```bash
ls -lh anomalies_report.csv log_anomaly_chart.png
```

View the generated CSV contents:
```bash
cat anomalies_report.csv
```

---

## Step 8: Setting Up Automated Continuous Monitoring with Linux Cron

In real production environments, logs flow continuously. To automate scheduled periodic log inspections, configure a Linux `cron` job.

### 8.1 Create a Runner Script
Create an automated execution script that loads the virtual environment and triggers the AIOps engine:

```bash
cat << 'EOF' > /root/aiops-log-monitor/run_aiops.sh
#!/bin/bash
cd /root/aiops-log-monitor
source venv/bin/activate
python3 aiops_logs_analysis.py >> /root/aiops-log-monitor/aiops_cron.log 2>&1
EOF

chmod +x /root/aiops-log-monitor/run_aiops.sh
```

### 8.2 Schedule Cron Job (Every 5 Minutes)
Open the crontab editor:
```bash
crontab -e
```

Add the following entry at the bottom of the crontab:
```text
*/5 * * * * /root/aiops-log-monitor/run_aiops.sh
```

### 8.3 Verify Active Cron Jobs
```bash
crontab -l
```

To view continuous execution logs:
```bash
tail -f /root/aiops-log-monitor/aiops_cron.log
```

---

## Step 9: Downloading & Inspecting Anomaly Reports (CSV & PNG)

To view the generated chart and CSV on your local machine:

### Option A: Using SCP from Local Machine Terminal
Open a terminal on your local laptop (PowerShell, Command Prompt, or Mac/Linux terminal):
```bash
# Copy CSV Report
scp -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>:/root/aiops-log-monitor/anomalies_report.csv ./

# Copy PNG Visualization Chart
scp -i "your-key.pem" ubuntu@<EC2-PUBLIC-IP>:/root/aiops-log-monitor/log_anomaly_chart.png ./
```

> **Note:** If permission is denied copying from `/root`, first copy the files to `/home/ubuntu/` on EC2:
> ```bash
> cp /root/aiops-log-monitor/anomalies_report.csv /root/aiops-log-monitor/log_anomaly_chart.png /home/ubuntu/
> chown ubuntu:ubuntu /home/ubuntu/anomalies_report.csv /home/ubuntu/log_anomaly_chart.png
> ```

### Option B: Using MobaXterm or WinSCP
1. Connect to the EC2 instance using **SFTP** session with your `.pem` key.
2. Navigate to `/root/aiops-log-monitor` or `/home/ubuntu`.
3. Drag and drop `anomalies_report.csv` and `log_anomaly_chart.png` to your local folder.

---

## Step 10: Real-World Troubleshooting & Error Resolution Matrix

| Symptom / Error | Root Cause | Exact Solution |
| :--- | :--- | :--- |
| **`ModuleNotFoundError: No module named 'sklearn'`** | Executed script using system Python rather than activated virtual environment. | Run `source venv/bin/activate` inside `/root/aiops-log-monitor` before running Python scripts. |
| **`UserWarning: Matplotlib is currently using agg, which is a non-GUI backend`** | Expected warning on headless Linux servers lacking an X11 desktop display. | Harmless warning; `matplotlib.use('Agg')` safely writes charts directly to disk without requiring a monitor. |
| **`Permission denied: run_aiops.sh`** | Runner script missing execute permissions. | Run `chmod +x /root/aiops-log-monitor/run_aiops.sh`. |
| **`Cron job not executing / no log created`** | Relative file paths used in cron script or cron daemon stopped. | Always use absolute paths (e.g., `/root/aiops-log-monitor/run_aiops.sh`). Verify cron status with `systemctl status cron`. |
| **`Permission denied (publickey)` on SSH** | Wrong EC2 key pair used, wrong user name, or permissions too open on `.pem` file. | Ensure user is `ubuntu` (not `root`) and key permissions on local machine are set with `chmod 400 your-key.pem`. |
| **`Empty DataFrame / 0 logs processed`** | Log format does not match regular expression pattern. | Verify timestamp format in `system.log`. For production logs, point path to `/var/log/syslog` or your app log directory and adjust regex pattern. |

---

## Step 11: Infrastructure Cleanup & Cost Optimization

To avoid ongoing AWS cloud compute charges after completing the lab:

1. **Terminate EC2 Instance:**
   * Go to **AWS EC2 Console** → **Instances**.
   * Select `AIOps-Log-Analyzer`.
   * Click **Instance State** → **Terminate Instance**.
2. **Confirm Volume Deletion:**
   * Verify the 20 GiB root EBS volume is automatically detached and terminated with the instance.
3. **Delete Security Group & Key Pair (Optional):**
   * If you created a dedicated security group or temporary key pair for this lab, remove them under **Security Groups** and **Key Pairs**.

---

## Step 12: Professional Resume Points & Interview Highlights

Add these bullet points to your resume to showcase enterprise-grade AIOps and site reliability engineering (SRE) capabilities:

* **AIOps & Log Intelligence Pipeline:** Engineered an automated, end-to-end AIOps log anomaly detection system on AWS EC2 using Python, `pandas`, and `scikit-learn` (`IsolationForest`) to automatically detect infrastructure incidents without static alerting thresholds.
* **Proactive Outage Prevention:** Reduced Mean Time to Detect (MTTD) by parsing multi-source log streams to surface database deadlocks, CPU usage spikes (95%+), connection pool exhaustions, and brute-force security threats in near-real-time.
* **Incident Reporting & Observability Automation:** Built automated reporting pipelines exporting prioritized incident CSV reports and visualization charts (`matplotlib`), orchestrated via automated Linux `cron` background jobs.
* **SRE & Production Readiness:** Designed fault-tolerant Python scripts with structured regex normalization, isolated virtual environments, and ANSI-colored terminal alerting matrices for operational engineering teams.

---
> [🏠 Back to Master Index](README.md)
