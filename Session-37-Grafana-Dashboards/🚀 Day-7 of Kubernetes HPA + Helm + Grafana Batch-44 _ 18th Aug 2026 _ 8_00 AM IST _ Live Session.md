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
