## Key Outcomes

The session focused on deploying and configuring Prometheus and Grafana on a Google Cloud Kubernetes cluster using Helm, then exploring the full monitoring stack: dashboards, data sources, user/team management, alerting, and PromQL queries. Participants were walked through importing ready-made JSON dashboards from the Grafana community, creating custom panels using PromQL, and configuring alert rules with contact points and silencers. The session also covered the architecture of the monitoring system, the role of kube-state-metrics and node exporter, and best practices around alert management using the **golden signals** framework (Latency, Error, Traffic, Saturation). 

---

## Architecture of the Monitoring Stack

- **Three core components** work together to deliver Kubernetes monitoring: 
    - **Kube-state-metrics**: Collects Kubernetes-level data — CPU usage, memory utilization, pod status, and all cluster resource states. 
    - **Node Exporter**: Fetches node-specific information (per-node metrics) and exports it to the Prometheus database. 
    - **Prometheus**: Pulls data from the cluster via these exporters, stores it in a **time-series database** using key-value pairs, enabling PromQL queries. 
- **Persistent Volume Claims (PVC)**: Data is stored in PVCs rather than a generic database; the term "claim" is used because the storage is bound to Prometheus. 
- **Grafana**: Acts as the visualization layer — runs PromQL queries against Prometheus, fetches the data, and renders graphs on the UI. 
- **Data flow**: Cluster metrics → kube-state-metrics / node exporter → Prometheus (time-series DB, key-value store) → Grafana (PromQL queries) → visual dashboards. 
- Prometheus's time-series database stores data as matrices; Grafana can visualize this data across configurable time zones (e.g., last 15 minutes, 1 hour, 1 month). 

---

## Kubernetes Cluster Setup on Google Cloud

- Instructor deleted the previous cluster and created a new one live to demonstrate the process. 
- **Cluster configuration steps:** 
    - Navigate to the Kubernetes list overview page in Google Cloud Console.
    - Click **Create** → select **Standard** mode (not Autopilot).
    - Name the cluster (e.g., `grafana-day-2`).
    - Select **one node per zone** — since the US-central region has three zones, this automatically creates **three nodes**. 
    - Set disk size to **30 GB per node** (reduced from the default 100 GB), resulting in 90 GB total across the cluster. 
    - Leave all other configuration at defaults; click **Create**.
- Cluster creation takes approximately 2–3 minutes. 
- Participants were instructed to navigate to the same page and follow along in real time. 

---

## Helm Installation of Prometheus & Grafana Stack

- **Helm** is described as an open-source stack of ready-made, pre-configured components — it installs and configures all monitoring components in one command rather than requiring manual setup. 
- **Step-by-step commands executed** (from a shared GitHub repo): 
    1. Connect to the Kubernetes cluster.
    2. Create a **namespace**: `monitor` (namespace can be renamed as needed). 
    3. Add the **Prometheus community Helm repo**.
    4. Run `helm repo update` to update all available repos in the environment. 
    5. Run `helm install kube-prometheus-stack` — installs all components (pods, services, deployments) into the `monitor` namespace. 
- The Helm chart name (`kube-prometheus-stack`) is from the Prometheus community; the release name and namespace can be customized. 
- Installation takes 2–3 minutes; Helm deploys all services automatically. 

---

## Exposing Grafana via Load Balancer

- After installation, all services run internally within the `monitor` namespace. 
- **To access Grafana over the internet**, a Load Balancer service must be created: 
    - Command: `kubectl expose deployment kube-prometheus-stack-grafana --port=3000 --target-port=3000 --type=LoadBalancer --name=grafana --namespace=monitor` 
    - `--port=3000` and `--target-port=3000`: Grafana runs internally on port 3000. 
    - `--type=LoadBalancer`: Exposes the service publicly over the internet. 
    - `--name=grafana`: Name of the newly created service. 
- Verify with: `kubectl get svc -n monitor` — wait ~30–40 seconds for the **external IP** to be generated and published. 
- Once the external IP is available, access Grafana at `<external-IP>:3000`. 
- **Service type options** discussed: ClusterIP (internal only), NodePort, LoadBalancer (internet-facing). 
- ClusterIP is used for internal cluster communication; Load Balancer is needed for internet access. 

---

## Grafana Login and Initial Configuration

- **Default credentials**: Username = `admin`; password is base64-encrypted and retrieved via a specific `kubectl` command from the Helm output. 
- The initial admin password is **static** (set at install time), but each deployment generates a different encrypted value. 
- After logging in with admin credentials, the Grafana homepage shows options: Home, Bookmarks, Shared Dashboards, Alerting, Connections, and Administration. 

---

## Data Sources and Connections

- Grafana can connect to **any data source**, not just Prometheus: 
    - Prometheus, MySQL, PostgreSQL, Azure Monitor, AWS CloudWatch, Graphite, InfluxDB, and others.
    - In a company context, data sources could include Salesforce, Jira, or any internal system.
- When Helm installs the stack, **two data sources are automatically configured**: Prometheus and AlertManager — no manual setup required. 
- To add a new data source: **Connections → Data Sources → Add new data source** → select the type → provide URL, username, and password. 
- Prometheus is accessible internally within the cluster on **port 9090**; Grafana connects to it using the internal cluster service URL with the `monitor` namespace. 
- For sensitive credentials, a **Vault** can be used to store secrets; the token from Vault can be referenced in the data source configuration instead of hardcoding credentials. 
- Whether Prometheus is visible over the internet depends on whether it is exposed as a Load Balancer; by default it is not exposed externally. 

---

## Dashboard Import and Customization

- Grafana dashboards are written in **JSON** (Go language, ~97% Go). 
- Writing a dashboard from scratch requires ~940 lines of JSON code — not practical for most users. 
- **Recommended approach**: Import ready-made dashboards from the [Grafana Dashboards community site](https://grafana.com/grafana/dashboards/). 

### Importing via Dashboard ID

- Search for the desired dashboard (e.g., "Kubernetes Nodes," "Kubernetes Pod Metrics"). 
- Copy the **dashboard ID** (e.g., `747` for Kubernetes Pod Metrics, `1860` for Node Exporter). 
- In Grafana: **Dashboards → New → Import** → paste the ID → click **Load** → select the Prometheus data source → click **Import**. 

### Importing via JSON File

- Download the JSON file from the community site, open in VS Code to inspect/modify, then upload via **Dashboards → New → Import → Upload JSON file**. 
- If a dashboard with the same UID already exists, the UID must be changed to avoid conflicts. 

### Dashboard Variables

- Dashboards use **variables** (prefixed with `$`) to allow dynamic filtering — e.g., selecting namespace, pod, cluster, or container from a dropdown without editing the dashboard. 
- This enables a **single dashboard for all namespaces and pods**, selectable at runtime. 

### Panel Customization

- Each panel can be edited: change the visualization type (graph, gauge, speedometer), rename the panel, modify the PromQL query, adjust thresholds. 
- **Duplicate a panel** as a shortcut: duplicate an existing panel (e.g., CPU) and change the query and name (e.g., to "Network") rather than building from scratch. 
- Visualization types include graphs, gauges, and other chart types; style can be customized as needed. 
- If data is not appearing in a panel, the metric name may not exist in the cluster — use the **Explore** tab to verify the metric before adding it to a dashboard. 

---

## PromQL Queries and the Explore Tab

- **PromQL** is the query language used by Grafana to fetch data from Prometheus. 
- To find and test a query before adding it to a dashboard: 
    - Go to **Connections → Data Sources → Prometheus → Explore** (or use the **Explore** button directly).
    - Select the metric from the dropdown (e.g., CPU, memory, network bytes, I/O).
    - Run the query to verify data is available.
- Example metric explored: **container network receive/transmit bytes total** — used to monitor network traffic per pod/container/namespace. 
- Data in Prometheus is available for every pod, cluster, namespace, and container — the query filters it by the variables selected. 
- Workflow for adding a new panel: find the metric in Explore → confirm data → copy the PromQL query → paste into a panel's query editor → save. 

---

## User and Team Management

- **Creating users**: Administration → Users and Access → Users → New User → provide name, email, username, password. 
- **Default role for new users is Viewer** — limited access (Home, Bookmarks, Shared Dashboards, Alerting only; no Connections or Administration tabs). 
- To grant more access: change role to **Admin** or **Editor** via the user's profile. 
- **Force logout**: Admins can force-logout any user from their session (useful for security or access revocation). 
- **Teams**: Create a team, add users to it; all members of a team share the same access level — useful for managing access at a group level (e.g., "Deepak Team"). 
- **Service Accounts**: Non-human, robotic accounts used for automation — unlike human users, they do not leave the organization, making them reliable for automated pipelines and integrations. 
- **Plugins**: Extend Grafana's capabilities for domain-specific use cases (e.g., temperature units for healthcare, petroleum sensor data for oil & gas). 
- **Enterprise License**: Grafana is open-source, but an Enterprise edition is available with advanced features; the license can be activated via the Administration → License tab. 

---

## Alerting Configuration

### Creating Alert Rules

- Alerts can be created **per panel** (from the panel's alert tab) or globally from the **Alerting** menu. 
- Steps to create an alert rule: 
    1. Name the alert rule.
    2. Define the condition/behavior — e.g., "if network usage exceeds threshold for more than 5 minutes."
    3. Set the **evaluation period** — how frequently to check the condition.
    4. Set **Keep Firing** duration — how long to keep firing the alert after the condition is met, until the issue is resolved. 

### Contact Points and Notifications

- **Contact points** define where alerts are sent: 
    - Email (requires SMTP configuration), Slack, Microsoft Teams (via Webhook URL), Telegram, Discord, Google Chat, Cisco Webex, SNS, and others.
- **SMTP configuration**: Requires the company's own SMTP server; using an external mail server (e.g., Gmail) risks emails being blocked or flagged as spam. 
- **Microsoft Teams integration**: Create a channel, generate a **Webhook URL**, paste it into the Teams contact point configuration in Grafana. 
- Notification routing: alerts can be routed to different teams/contact points based on severity or team assignment. 

### Alert Silencing (Maintenance Windows)

- During planned maintenance, alerts should be **suppressed** to avoid false positives (false alerts). 
- Create a **silencer** in Grafana — define the duration of the maintenance window; alerts will not fire during that period. 
- After the maintenance window ends, alerting resumes automatically. 

### Active Alerts View

- The **Active Notifications** section shows all currently firing alerts — useful for DevOps engineers to review overnight incidents without sorting through email. 
- Alerts can be grouped by team, contact person, or severity for easier triage. 

### Alert Manager

- **Alert Manager** is a separate service running on **port 9093**, automatically set up by the Helm stack. 
- It handles routing, grouping, and firing of alert notifications.

---

## Golden Signals Framework for Monitoring

Introduced by Chander as a best-practice framework for defining what to monitor and how to answer interview questions about alerting strategy: 

- **L — Latency**: Is the service responding slowly? Are there slowness issues? 
- **E — Error**: Are there errors occurring in the system? 
- **T — Traffic**: Has traffic increased or decreased unexpectedly? 
- **S — Saturation**: Are resources (CPU, memory) at or beyond capacity? 
- These four signals are **interconnected** — high traffic can cause latency, which can cause errors, which indicates saturation. 
- In interviews, when asked "what parameters do you alert on?", the recommended answer is: "We follow the golden signals — Latency, Error, Traffic, Saturation — and set thresholds accordingly." 
- **Observability** is described as a higher-level concept than monitoring — the preferred job title/skill term for this domain is "Observability Engineer" or "Continuous Monitoring SME." 
- Tools like Prometheus/Grafana, Splunk, Dynatrace, and AppDynamics all follow the same 90% logic; the concepts transfer across tools. 

---

## DevOps Roles and Responsibilities in Monitoring

- The DevOps engineer's role is to **build, deploy, and configure** the monitoring infrastructure (Prometheus, Grafana, alerts, dashboards) and hand it over to L1/L2/L3 teams. 
- **L1/L2/L3 responsibilities post-handover**: 
    - L1/L2/L3: Respond to and resolve alerts.
    - If unresolvable, escalate via ticket to DevOps.
    - DevOps logs into the server to investigate infrastructure-level issues.
- Panel-level issues (data not appearing, alert not firing) are DevOps problems; alert-triggered but unresolved issues are L1/L2/L3 problems. 
- DevOps sets up the system once; ongoing monitoring and response is the operations team's responsibility. 

---

## Q&A Highlights

- **Q: Can Prometheus be exposed externally?**
A: Yes, it can be exposed via Load Balancer, but it is not recommended as it would expose all cluster logs and metrics publicly without authentication. 
- **Q: What are the service types available in Kubernetes?**
A: ClusterIP (internal only), NodePort, and LoadBalancer (internet-facing). 
- **Q: Is the initial Grafana admin password static or dynamic?**
A: Static — set at install time; each deployment generates a different encrypted value, but it does not change dynamically. 
- **Q: Can Grafana connect to a VM-hosted service (not Kubernetes)?**
A: Yes — install a **node exporter** on the VM, configure a pull or push mechanism to send metrics to Prometheus, then connect Grafana to Prometheus as the data source. For logging specifically, **Loki** or **Splunk** (enterprise) are recommended. 
- **Q: What is a service account in Grafana?**
A: A non-human, robotic account used for automation. Unlike human users, it does not leave the organization and is always available for automated pipelines. 
- **Q: What is the difference between Git and GitHub?**
A: Git is a version control software installed locally on a machine — it tracks changes for an individual. GitHub is a code collaboration platform — code must be **pushed** to GitHub to be visible to other developers. 
- **Q: What is a staging area in Git?**
A: The staging area is the intermediate state before committing — changes are added to the staging area (`git add`) and then committed locally before being pushed to GitHub. 
- **Q: How can we configure monitoring for Docker containers on a separate VM (not Kubernetes)?**
A: Install a node exporter on the VM, set up a pull/push mechanism to send container metrics to Prometheus, and configure Grafana to use Prometheus as the data source. For logs, use Loki or Splunk forwarders. 
- **Q: GitHub outage observed — what happened?**
A: Six major incidents were identified (API request failures, pull request failures, GitHub Actions failures, Copilot failures, etc.) — all interconnected through API endpoints. GitHub published a status update confirming the issues were resolved; a detailed Root Cause Analysis (RCA) was promised. 

---

## Action Items and Next Steps

- **All participants**: Keep the deployed cluster running; spend ~1 hour exploring every button and option in Grafana to build familiarity before the next session. 
- **Next session**: Cover **HPA (Horizontal Pod Autoscaling)** — not completed in this session due to time constraints. 
- **Day 4**: Kubernetes troubleshooting scenarios (CrashLoopBackOff, ImagePullBackOff, etc.). 
- **Day 45 (extra session)**: Additional topics including Splunk forwarders and extended tool coverage. 
- **Azure coverage**: Vikas confirmed he will cover Azure DevOps and provide a short comparison of main Azure vs. AWS services; participants recommended to start with the **AZ-900** fundamentals video (~9 hours) for Azure basics. 
- **Resume tip for SRE roles**: Add the word **"observability"** to the resume; include HPA as a Kubernetes skill line item. 
- **Interview prep**: Review the golden signals framework (LETS) and be able to explain the Prometheus-Grafana architecture, data flow, and alerting setup. 


# 🎤 Interview Questions & Answers

> **Interview tip:** Don't memorize the answer word-for-word. Understand the **flow, purpose and troubleshooting approach** behind each concept.

### 1. What is Prometheus?

**Answer:**
Prometheus is an open-source monitoring and metrics collection system. It collects time-series metrics from different targets and stores them so they can be queried later. In a Kubernetes environment, Prometheus can collect metrics related to nodes, pods, containers, and Kubernetes objects.

---

### 2. What is Grafana and why is it used with Prometheus?

**Answer:**
Grafana is a visualization and monitoring platform. It connects to Prometheus as a data source, uses **PromQL** to query metrics, and displays the results through dashboards and panels.
The typical flow is:
**Kubernetes → Prometheus → Grafana → Dashboard**

---

### 3. What is PromQL?

**Answer:**
PromQL stands for **Prometheus Query Language**. It is used to query and analyze metrics stored in Prometheus.
For example, PromQL can be used to retrieve CPU, memory, network, pod, or node-related metrics and display them in Grafana.

---

### 4. What is kube-state-metrics?

**Answer:**
`kube-state-metrics` generates metrics about the state of Kubernetes objects.
It can provide information about resources such as:

- Pods
- Deployments
- ReplicaSets
- Nodes
- Namespaces

It is useful when we need to understand the state of Kubernetes resources rather than just operating-system-level metrics.

---

### 5. What is Node Exporter?

**Answer:**
Node Exporter exposes infrastructure-level metrics from machines or nodes.
It can provide metrics related to:

- CPU
- Memory
- Disk
- Network
- System resources

Prometheus collects these metrics and Grafana can visualize them.

---

### 6. Why is Helm used for installing Prometheus and Grafana?

**Answer:**
Helm is a package manager for Kubernetes. Instead of manually creating many Kubernetes YAML files, we can use a Helm chart to deploy and manage applications.
For Prometheus and Grafana, Helm simplifies:

- Installation
- Configuration
- Upgrades
- Version management
- Resource deployment

---

### 7. What are Grafana dashboards and panels?

**Answer:**
A **dashboard** is a collection of monitoring visualizations, while a **panel** is an individual visualization inside that dashboard.
For example, a Kubernetes dashboard might contain separate panels for:

- CPU utilization
- Memory utilization
- Network traffic
- Pod status
- Node status

Each panel can use a PromQL query to retrieve its data.

---

### 8. What is Grafana alerting?

**Answer:**
Grafana alerting allows us to define conditions under which Grafana should generate an alert.
For example, we could configure an alert when CPU utilization remains above a defined threshold.
The general process is:
**Metric → Query → Condition → Alert Rule → Contact Point → Notification**

---

### 9. What are contact points in Grafana?

**Answer:**
Contact points define where Grafana sends alert notifications.
Depending on the configuration, notifications can be sent through mechanisms such as:

- Email
- Slack
- Webhooks

For example, a production alert can be configured to notify the operations team through email or another supported notification channel.

---

### 10. What are the four golden signals of monitoring?

**Answer:**
The four golden signals are:

1. **Latency**: How long a request takes.
2. **Traffic**: The amount of demand on the system.
3. **Errors**: The number or rate of failed requests.
4. **Saturation**: How much a resource is being utilized.

They provide a useful framework for deciding what application-level metrics should be monitored.

---

---

# 🔥 Scenario-Based Interview Questions & Answers

> **How to answer scenarios:** Start with what you would check first, explain your troubleshooting sequence, and finish with the likely action or resolution.

### 1. Scenario: CPU usage of a Kubernetes node suddenly becomes very high. How would you investigate it?

**Answer:**
I would first check the node and pod metrics in Grafana. I would identify whether the CPU consumption is coming from a particular pod, container, or the node itself.
I would then:

1. Check CPU metrics in Grafana.
2. Identify the affected node/pod.
3. Verify the pod resource consumption using Kubernetes commands.
4. Check whether there was a recent deployment or traffic increase.
5. Review application logs if necessary.
6. Determine whether resource limits/requests need adjustment or whether scaling is required.

---

### 2. Scenario: Grafana is running, but you cannot see Prometheus metrics. What would you check?

**Answer:**
I would first verify that Prometheus is running correctly.
I would check:

1. Prometheus pod status.
2. Prometheus service.
3. Grafana's configured data source.
4. Whether Grafana can communicate with Prometheus.
5. Whether the Prometheus data source is showing as healthy.
6. Whether the PromQL query actually returns data.

The problem could be related to the data source configuration, connectivity, Prometheus itself, or the query.

---

### 3. Scenario: Your Grafana dashboard is showing "No Data." What would you do?

**Answer:**
I would troubleshoot it systematically.
First, I would check whether Prometheus contains the required metric. Then I would test the PromQL query directly in Grafana's query interface.
I would verify:

- Metric name
- PromQL syntax
- Time range
- Prometheus data source
- Labels and filters
- Prometheus target status

If the metric doesn't exist in Prometheus, I would investigate the relevant exporter or metric collection component.

---

### 4. Scenario: You need to monitor CPU and memory for all Kubernetes nodes. How would you design the Grafana dashboard?

**Answer:**
I would create a Kubernetes infrastructure dashboard with separate panels for CPU and memory.
For example:

- Node CPU utilization
- Node memory utilization
- Node availability
- Network usage
- Disk usage

I would use PromQL queries to retrieve the required metrics and configure Grafana panels to make comparison between nodes easy.

---

### 5. Scenario: Your team is receiving hundreds of alerts every day, and engineers are ignoring them. What would you do?

**Answer:**
This is an example of **alert fatigue**.
I would review the existing alert rules and determine which alerts are genuinely actionable.
I would:

- Remove unnecessary alerts.
- Adjust thresholds.
- Increase evaluation duration where appropriate.
- Separate warning and critical alerts.
- Configure alerts according to business impact.
- Use maintenance silences when required.

The objective is to ensure that when an engineer receives an alert, it actually deserves attention.

---

### 6. Scenario: You are performing planned maintenance and Grafana keeps sending alerts. How would you handle it?

**Answer:**
I would use **Grafana alert silencing** for the planned maintenance period.
Before starting maintenance, I would configure a silence for the relevant alerts and duration. After maintenance is completed, the silence would expire or be removed.
This prevents unnecessary notifications while still keeping the alert rules configured.

---

### 7. Scenario: A company wants only the monitoring team to manage Grafana dashboards. Developers should only be able to view them. How would you configure Grafana?

**Answer:**
I would use Grafana's **users, teams, and roles**.
I would:

- Create a monitoring team with appropriate editing/administrative permissions.
- Add monitoring engineers to that team.
- Give developers viewer-level access.
- Avoid giving administrative permissions to users who don't need them.

This follows the principle of giving users only the access required for their responsibilities.

---

### 8. Scenario: An employee who originally configured automated monitoring leaves the company. The automation stops because it used that employee's account. How would you prevent this?

**Answer:**
I would use a **service account** for automation instead of depending on an individual employee account.
The service account provides a stable identity for automated processes and can be assigned only the permissions required for the automation.
This reduces dependency on individual users and makes the system easier to maintain.

---

### 9. Scenario: You want Grafana to send an email whenever a critical monitoring condition occurs. What would you configure?

**Answer:**
I would configure Grafana alerting with an email-based contact point.
The process would be:

1. Create the required alert rule.
2. Define the alert condition.
3. Configure SMTP settings.
4. Create an email contact point.
5. Associate the contact point with the appropriate alert/notification policy.
6. Test the alert.

The final flow would be:
**Metric → Alert Rule → Contact Point → SMTP → Email**

---

### 10. Scenario: Your application is experiencing slow responses, but CPU and memory usage appear normal. What metrics would you investigate?

**Answer:**
I would not assume that normal CPU and memory mean the application is healthy. I would investigate the **four golden signals**.
I would check:

- **Latency**: Are requests taking longer than normal?
- **Traffic**: Has request volume increased?
- **Errors**: Are requests failing?
- **Saturation**: Is another resource becoming a bottleneck?

I would then correlate these metrics with application logs and infrastructure metrics to identify the actual bottleneck.

---

---

# 🧠 Quick Revision Sheet

## Prometheus vs Grafana

| Prometheus | Grafana |
|---|---|
| Collects metrics | Visualizes metrics |
| Stores time-series data | Queries data sources |
| Uses PromQL | Builds dashboards and panels |
| Focuses on monitoring data | Focuses on visualization and alerting |

### Monitoring Flow

**Kubernetes → Metrics Components → Prometheus → PromQL → Grafana → Dashboard → Alert → Notification**

---
`
## 🔑 Core Concepts to Remember

| Concept | Simple Explanation |
|---|---|
| **Prometheus** | Collects and stores monitoring metrics |
| **Grafana** | Visualizes metrics through dashboards |
| **PromQL** | Query language used with Prometheus |
| **kube-state-metrics** | Provides Kubernetes object/state metrics |
| **Node Exporter** | Provides node-level infrastructure metrics |
| **Helm** | Packages and simplifies Kubernetes application deployment |
| **Dashboard** | Collection of monitoring visualizations |
| **Panel** | Individual visualization inside a dashboard |
| **Contact Point** | Destination for alert notifications |
| **SMTP** | Used for email notification delivery |
| **Service Account** | Stable identity for automated processes |
| **Alert Silencing** | Temporarily prevents notifications during expected events |
| **Alert Fatigue** | Engineers becoming desensitized because of excessive alerts |

---

## 🚦 Four Golden Signals

These four signals provide a useful framework for application monitoring:

| Signal | Meaning |
|---|---|
| 🕐 **Latency** | How long requests take |
| ❌ **Errors** | Number or rate of failed requests |
| 📈 **Traffic** | Amount of demand or requests |
| 🔋 **Saturation** | How heavily a resource is being utilized |

### Easy Memory Trick

> **Latency + Traffic + Errors + Saturation = Golden Signals**

---

## 🛠️ Production Monitoring Mindset

A useful way to think about a DevOps monitoring workflow is:

```text
BUILD
  ↓
CONFIGURE
  ↓
MONITOR
  ↓
ALERT
  ↓
TROUBLESHOOT
  ↓
OPERATE / HANDOVER
```

Monitoring is not just about making attractive graphs. The real goal is to make system behavior understandable and turn abnormal conditions into an operational response.

---

# ⚠️ Important Note About HPA

The session introduced **Horizontal Pod Autoscaling (HPA)** as an objective and the cluster was created with scaling practice in mind.

However, the source transcript explicitly indicates that the detailed HPA practical was **not fully covered in this session** and was planned for further discussion.

> **For interview preparation:** Treat HPA implementation as a topic planned for the next discussion rather than claiming that the complete HPA practical was demonstrated here.

---

# ✅ Student Revision Checklist

- [ ] Understand Prometheus and Grafana roles
- [ ] Understand the Kubernetes monitoring architecture
- [ ] Understand Helm-based monitoring stack deployment
- [ ] Understand Kubernetes namespaces
- [ ] Understand Grafana Service exposure
- [ ] Understand Grafana users, teams and roles
- [ ] Understand service accounts
- [ ] Understand dashboards and panels
- [ ] Understand PromQL
- [ ] Understand CPU, memory and network metrics
- [ ] Understand Grafana alert rules
- [ ] Understand contact points
- [ ] Understand SMTP notifications
- [ ] Understand alert silencing
- [ ] Understand alert fatigue
- [ ] Memorize the four golden signals
- [ ] Practice all 10 interview questions
- [ ] Practice all 10 scenario-based questions
- [ ] Review the HPA scope carefully

---

# 💼 Interview Preparation Strategy

For this session, focus on being able to explain these **five flows** without looking at notes:

### 1. Monitoring Flow

```text
Kubernetes → Prometheus → Grafana
```

### 2. Query Flow

```text
Grafana Panel → PromQL → Prometheus → Metric Data
```

### 3. Alert Flow

```text
Metric → Query → Condition → Alert Rule
```

### 4. Notification Flow

```text
Alert Rule → Contact Point → SMTP / Notification Channel → Team
```

### 5. Production Operations Flow

```text
Monitor → Detect → Alert → Investigate → Troubleshoot → Resolve
```

If you can explain these five flows clearly, you understand the core of the session rather than merely remembering a collection of tool names. Humans do occasionally reward understanding over button-clicking.

---

## 📚 Session Source

This GitHub study material was prepared from the **Session 37 / Grafana & Kubernetes monitoring session material provided for Batch 44**. The HPA scope has been kept explicit because the source material states that its detailed practical was not fully covered.

---

## 🌟 CloudDevOpsHub

**Multi Cloud + DevOps with AI**

> Learn → Practice → Build → Monitor → Troubleshoot → Prepare for Interviews

