# 🚀 Day-6 of Kubernetes Batch-44 : Continuous Monitoring with Prometheus, Grafana & Helm

[![Module: Monitoring & Observability](https://img.shields.io/badge/Module-Prometheus_%26_Grafana-E6522C?style=for-the-badge&logo=prometheus)](README.md)
[![Batch: DevOps-44](https://img.shields.io/badge/Batch-DevOps--44-blueviolet?style=for-the-badge)](README.md)
[![Date: 17th August 2026 | 8:00 AM IST](https://img.shields.io/badge/Date-17th%20August%202026%20%7C%208:00%20AM%20IST-success?style=for-the-badge)](README.md)


## 📋 Session Summary: Continuous Monitoring with Prometheus, Grafana & Helm

### 1. Full Session Overview

This session focused on continuous monitoring of applications running in a Kubernetes environment, with the main emphasis on Prometheus, Grafana, Helm, Kubernetes metrics, centralized monitoring, and practical deployment of the monitoring stack.
The session began by reviewing the previous Kubernetes work. The instructor explained that the team had already:
Created a Kubernetes/GKE cluster
Deployed microservices
Created Pods
Created Services
Created ReplicaSets
Created Deployments
However, one major question remained:
How do we continuously know whether the deployed application is healthy and performing properly?
This led to the introduction of continuous monitoring.
The instructor explained why monitoring is necessary. In a production environment, manually checking hundreds or thousands of machines, Pods, CPU usage, memory usage, logs, network traffic, and application health is not practical. Monitoring allows teams to identify problems early, reduce downtime, understand resource utilization, and take corrective action before a small problem becomes a major outage.
A major concept introduced was centralized continuous monitoring. Instead of keeping logs and metrics only on individual servers, data should be collected and stored in a centralized monitoring system. This is particularly useful when a server goes down or an attacker deletes logs from the original machine.
The session then compared several monitoring tools, including:
Nagios
Zabbix
ELK
AppDynamics
Splunk
Datadog
Prometheus
Grafana
The primary focus was placed on Prometheus + Grafana, particularly because of their strong suitability for Kubernetes environments and their open-source nature.

### 2. Monitoring Concepts Discussed

The instructor explained several different types of monitoring.
Infrastructure Monitoring
Monitoring the underlying infrastructure such as:
CPU
Memory
Disk
VM health
Kubernetes nodes
Resource utilization
Application Monitoring
Monitoring the actual application and its components, including:
Application health
Pods
Services
Deployments
Application behavior
Application metrics
Network Monitoring
Monitoring network-related information such as:
Incoming traffic
Outgoing traffic
Network utilization
Read/write activity
Network performance
The session emphasized that monitoring is not limited to CPU and memory. A complete monitoring strategy considers the infrastructure, application, and network.

### 3. Why Continuous Monitoring Is Required

The session highlighted several reasons.
Early Problem Detection
Monitoring helps identify problems before they become major failures.
```text
Small Problem
      ↓
Monitoring detects it
      ↓
Alert
      ↓
Troubleshooting
      ↓
Problem fixed
Reduce Downtime
```

If an application becomes unhealthy during business hours, monitoring can alert the responsible team before the issue causes a prolonged outage.
Real-Time Visibility
Teams can monitor:
CPU
Memory
Disk
Network
Application health
Kubernetes resources
Historical Analysis
Metrics can be stored so teams can analyze how infrastructure behaved over time.
Large-Scale Monitoring
Checking thousands of machines manually is impractical.
Centralized monitoring solves this problem.

### 4. Centralized Monitoring

One of the most important concepts in the session was centralized monitoring.
Instead of storing monitoring data only on individual servers:
Server 1 → Logs
Server 2 → Logs
Server 3 → Logs
Server 4 → Logs
```text
the data can be collected centrally:
Server 1 ─┐
Server 2 ─┤
Server 3 ─┼──→ Central Monitoring System
Server 4 ─┘
```

This provides a central location for monitoring and historical analysis.
It also protects against situations where:
A server goes down
Logs are lost
A server is corrupted
Someone deletes local logs
There are too many machines to inspect manually

### 5. Monitoring Tools Discussed

The session mentioned several monitoring and observability tools.
Nagios
A traditional monitoring solution discussed in comparison with Prometheus.
Zabbix
The instructor highlighted its agent-based approach and the challenge of managing agents across a very large environment.
ELK
The ELK stack was discussed as a combination of multiple components:
Elasticsearch
Logstash
Kibana
The main challenge discussed was the need to manage multiple components.
AppDynamics
Described as a powerful monitoring solution, but with higher cost considerations.
Splunk
Discussed primarily in the context of logging and enterprise usage.
Datadog
Discussed as another enterprise monitoring solution.
Prometheus + Grafana
The primary focus of the session.

### 6. Prometheus and Grafana

The central concept of the session was the Prometheus + Grafana monitoring stack.
They are two separate open-source tools that work together.
```text
Kubernetes
    ↓
Metrics
    ↓
Prometheus
    ↓
Grafana
    ↓
Dashboards
The instructor described their roles as:
Prometheus
```

Prometheus collects and stores metrics in a time-series database.
Grafana
Grafana retrieves the data and provides visualization through dashboards and graphs.
Therefore:
Prometheus = metrics collection and storage
Grafana = visualization and dashboards

### 7. Prometheus as a Time-Series Database

A major concept discussed was the meaning of a time-series database.
Monitoring data is continuously generated.
For example:
10:00 → CPU = 30%
10:01 → CPU = 35%
10:02 → CPU = 42%
10:03 → CPU = 50%
Prometheus stores these metrics along with their time information.
This allows historical analysis.
For example:
CPU
100% |                 *
80% |              *
60% |          *
40% |     *
20% | *
+--------------------
Time
This is why Prometheus is suitable for continuously changing infrastructure metrics.

### 8. Prometheus Data Flow

The session explained the basic architecture:
```text
Kubernetes Cluster
       ↓
Nodes / Kubernetes Components
       ↓
Metrics Sources
       ↓
Prometheus
       ↓
Time-Series Storage
       ↓
Grafana
       ↓
Dashboard
Prometheus collects the metrics and stores them.
```

Grafana then queries Prometheus and converts the data into visual dashboards.

### 9. Node Exporter

The session introduced Node Exporter.
Node Exporter is used to expose node-level metrics so that Prometheus can collect them.
Conceptually:
```text
Kubernetes Node
      ↓
Node Exporter
      ↓
Node Metrics
      ↓
Prometheus
```

Node-level information can include infrastructure metrics such as CPU, memory and other system information.
The instructor distinguished this from Kubernetes-specific metrics.

### 10. Kube-State-Metrics

Another important component discussed was kube-state-metrics.
Its purpose is to expose metrics about Kubernetes resources and their states.
Conceptually:
```text
Kubernetes Cluster
       ↓
kube-state-metrics
       ↓
Kubernetes Resource Metrics
       ↓
Prometheus
```

It can provide information related to resources such as:
Pods
Deployments
Services
ReplicaSets
Other Kubernetes objects
The session made the distinction:
Node Exporter → node/system-level metrics
kube-state-metrics → Kubernetes object/state information
This is an important interview distinction.

### 11. Grafana

Grafana was introduced as the visualization layer.
The basic architecture is:
```text
Prometheus
    ↓
Metrics
    ↓
Grafana
    ↓
Dashboard
Grafana can display metrics through:
Graphs
Panels
Charts
Dashboards
```

The instructor explained that Grafana itself does not primarily act as the metrics database in this architecture. It queries a configured data source.

### 12. Grafana Data Source

A very important practical concept was the Grafana Data Source.
Grafana needs to know where the monitoring data is stored.
Therefore:
```text
Grafana
   ↓
Data Source
   ↓
Prometheus
```

After configuring Prometheus as the data source, Grafana can query Prometheus.
The instructor emphasized:
Data Source provides the connection between Grafana and the monitoring backend.
Once connected, queries can be executed and the resulting data can be visualized.

### 13. PromQL

The session introduced PromQL, Prometheus Query Language.
Grafana uses PromQL queries to retrieve metrics from Prometheus.
The conceptual flow is:
```text
Grafana
   ↓
PromQL Query
   ↓
Prometheus
   ↓
Metric Data
   ↓
Grafana Panel
```

This allows users to retrieve specific metrics and create meaningful dashboards.

### 14. Grafana Port

The session mentioned that Grafana commonly runs on:
Port 3000
Therefore:
```text
Grafana
   ↓
:3000
This is a useful practical and interview detail.
```


### 15. Helm

The session introduced Helm as a major Kubernetes concept.
The instructor gave the simple definition:
Helm is a package manager for Kubernetes.
The comparison was made with Linux package managers:
Ubuntu/Debian → APT
Red Hat family → YUM
Kubernetes → Helm
Helm makes it easier to install, upgrade, customize and remove Kubernetes applications.

### 16. Helm Charts

Helm packages are called Helm Charts.
A chart contains the Kubernetes configuration required for an application.
Instead of manually creating many Kubernetes YAML files:
Deployment YAML
Service YAML
Config YAML
Storage YAML
RBAC YAML
...
a Helm Chart can package the application's Kubernetes configuration.
Conceptually:
Helm Chart
|
+-- Kubernetes Resources
+-- Templates
+-- Configuration

### 17. Helm values.yaml

One of the most important files discussed was:
values.yaml
This file contains configurable values used by Helm templates.
The instructor emphasized that the values can be customized according to requirements.
Conceptually:
```text
values.yaml
     ↓
Helm Templates
     ↓
Kubernetes Resources
This makes Helm charts reusable and configurable.
```


### 18. Helm Chart Benefits

Helm can be used to:
Install applications
Upgrade applications
Configure applications
Customize deployments
Remove applications
Reuse predefined charts
For example, instead of manually building the Prometheus/Grafana Kubernetes configuration, a ready-made Helm Chart can be used.
This is especially useful for complex applications with many Kubernetes resources.

### 19. Helm Repository

The practical section demonstrated adding a Helm repository.
The basic workflow was:
```text
Helm Repository
      ↓
helm repo add
      ↓
Repository Available
      ↓
helm repo update
      ↓
Latest Chart Information
      ↓
helm install
```

The instructor explained that the local Helm repository information may become outdated, so:
```bash
helm repo update
```

can be used to retrieve the latest chart information.

### 20. Helm Installation Workflow

The practical workflow demonstrated in the session was approximately:
```text
1. Create GKE Cluster
        ↓
2. Connect to Cluster
        ↓
3. Create Monitoring Namespace
        ↓
4. Add Helm Repository
        ↓
5. Update Helm Repository
        ↓
6. Install Prometheus/Grafana Chart
        ↓
7. Verify Kubernetes Resources
        ↓
8. Configure Grafana
        ↓
9. Connect Prometheus as Data Source
        ↓
10. Build Dashboards
```

This is the core practical workflow of the session.

### 21. Monitoring Namespace

The instructor demonstrated creating a separate namespace for monitoring.
The command discussed was:
```bash
kubectl create namespace monitor
```

The namespace can be checked using:
```bash
kubectl get ns
```

The reason for creating a separate namespace was resource isolation and better organization.
Conceptually:
Kubernetes Cluster
|
+-- Application Namespace
|
+-- Application Namespace
|
+-- monitor
|
+-- Prometheus
+-- Grafana
+-- Monitoring Components
This is a good operational practice because monitoring components can be managed separately from application workloads.

### 22. GKE Cluster Practical

The practical session created a GKE Standard cluster.
The instructor selected:
Standard cluster
Default/basic configuration
Node pool
Nodes per zone
Appropriate machine sizing
The instructor also explained that if students wanted to deploy both their application and the monitoring stack, they should choose an appropriately sized cluster.
The cluster was then connected so Kubernetes commands could be executed.

### 23. Prometheus/Grafana Helm Stack

The session used a ready-made Helm chart for Prometheus and Grafana.
The instructor referred to the kube-prometheus-stack Helm chart.
The purpose is to provide a ready-made monitoring stack instead of manually developing every monitoring component.
Conceptually:
```text
Helm
  ↓
kube-prometheus-stack
  ↓
Prometheus
Grafana
Node Exporter
kube-state-metrics
and related components
```

This demonstrates one of the major benefits of Helm.

### 24. Why Helm Is Useful Here

Without Helm, installing the monitoring stack manually would require managing many Kubernetes resources.
With Helm:
```text
Helm Chart
     ↓
Multiple Kubernetes Resources
     ↓
Installed Together
```

This reduces repetitive configuration and makes upgrades and configuration management easier.

### 25. Helm and Kubernetes Relationship

An important clarification from the session was that Helm is not a cloud service.
Helm is a Kubernetes package manager.
The conceptual relationship is:
```text
Kubernetes
     ↑
Helm
     ↓
Manages Kubernetes Applications
```

Helm communicates with the Kubernetes environment to deploy and manage resources.

### 26. Monitoring Architecture Explained

The most important architecture from the session can be represented as:
Kubernetes Cluster
|
+-------------+-------------+
|                           |
Nodes                  Kubernetes Objects
|                           |
Node Exporter              kube-state-metrics
|                           |
+-------------+-------------+
```text
                        |
                        ↓
                   Prometheus
                        |
                 Time-Series DB
                        |
                        ↓
                     Grafana
                        |
                  Data Source
                        |
                        ↓
                    PromQL
                        |
                        ↓
                  Dashboards
```

This architecture is the main concept you should remember from the session.

### 27. Monitoring and Alerting Workflow

The session also explained the general enterprise monitoring workflow.
```text
Metric Collection
       ↓
Data Storage
       ↓
Visualization
       ↓
Threshold
       ↓
Alert
       ↓
L1/L2/NOC Team
       ↓
Troubleshooting
       ↓
DevOps / L3 Team
       ↓
Resolution
```

Modern monitoring systems can also use machine learning/predictive capabilities to identify patterns and potentially predict future problems.

### 28. Manual Linux Monitoring Commands

Before automation, the instructor reviewed some basic Linux monitoring commands.
CPU / Processes
top
or:
htop
These can provide information about:
CPU usage
Memory
Processes
Process IDs
System activity
Memory
free
Disk
df -h
The session emphasized that these manual commands are useful for troubleshooting, even though production monitoring is normally automated.

### 29. Common Infrastructure Problems

Several common monitoring scenarios were discussed:
High CPU
High memory
Low memory
Out-of-memory conditions
Disk becoming full
Network outage
Slow network
Application issues
Software bugs
High system temperature
Poor resource utilization
Oversized infrastructure
Monitoring helps identify these conditions before they become major incidents.

### 30. Capacity Planning

The session also discussed the importance of monitoring for capacity planning.
For example, suppose an application is very small but the infrastructure is unnecessarily large:
```text
Small Application
       ↓
Large VM
       ↓
Low Resource Utilization
       ↓
Wasted Cost
```

Monitoring allows teams to identify underutilized resources and make better infrastructure-sizing decisions.

### 31. Centralized Monitoring vs Manual Monitoring

Manual Monitoring
Server 1 → Check
Server 2 → Check
Server 3 → Check
...
Server 1000 → Check
This doesn't scale.
Centralized Monitoring
```text
Servers / Kubernetes
       ↓
Metrics Collection
       ↓
Central Monitoring
       ↓
Dashboard
       ↓
Alerts
```

This is much more practical for large environments.

### 32. Key Topics Covered

Monitoring Fundamentals
Continuous monitoring
Proactive monitoring
Centralized monitoring
Real-time monitoring
Infrastructure monitoring
Application monitoring
Network monitoring
Capacity planning
Historical metrics
Alerting
Monitoring Tools
Prometheus
Grafana
Nagios
Zabbix
ELK
AppDynamics
Splunk
Datadog
Prometheus
Prometheus architecture
Time-series database
Metric collection
Metric storage
Historical data
PromQL
Prometheus data source
Grafana
Visualization
Dashboards
Panels
Data sources
Prometheus integration
Grafana port 3000
PromQL queries
Kubernetes Monitoring
Node monitoring
Kubernetes object monitoring
Node Exporter
kube-state-metrics
Pod metrics
Deployment metrics
Service metrics
Helm
Helm package manager
Helm Charts
values.yaml
Helm repository
```bash
helm repo add
helm repo update
```

Helm installation
Application upgrades
Application removal
Chart customization
Practical Work
GKE Standard cluster
Kubernetes cluster connection
Monitoring namespace
Helm repository configuration
Prometheus/Grafana stack
kube-prometheus-stack
Grafana data source configuration
Linux Monitoring
top
htop
free
df -h
Final Session Takeaway
The core learning of this session was:
After deploying applications to Kubernetes, we need continuous visibility into their health, performance, resource usage and behavior. Prometheus provides the metrics collection and time-series storage layer, while Grafana provides visualization and dashboards. Helm makes deploying this monitoring stack into Kubernetes much easier.
The complete flow is:
```text
Application
     ↓
Kubernetes
     ↓
Nodes / Pods / Services
     ↓
Node Exporter + kube-state-metrics
     ↓
Prometheus
     ↓
Time-Series Metrics
     ↓
Grafana Data Source
     ↓
PromQL
     ↓
Dashboards
     ↓
Monitoring / Alerts
     ↓
Troubleshooting
```

The session therefore connected several concepts learned throughout the Kubernetes course into a real monitoring architecture.

10 Interview Questions & Answers
### ❓ Q1: Why is monitoring required in a production environment?

**💡 Answer:**

Monitoring is required to continuously check the health and performance of applications and infrastructure. It helps identify problems such as high CPU usage, memory issues, disk problems, network failures, application failures, and downtime before they become major outages.
Monitoring also helps:
Detect issues proactively.
Reduce downtime.
Understand real-time application health.
Perform capacity planning.
Troubleshoot failures.
Identify abnormal behavior or trends.

### ❓ Q2: What is centralized monitoring?

**💡 Answer:**

Centralized monitoring means collecting monitoring data and logs from multiple servers, nodes, pods, or applications into a centralized monitoring system.
For example, instead of manually checking logs from 1,000 machines, all the required monitoring information can be collected centrally.
Benefits include:
Easier troubleshooting.
Centralized visibility.
Better security.
Reduced manual effort.
Monitoring large environments efficiently.
Logs remain available even if an individual server goes down.

3. What is Prometheus?
**💡 Answer:**

Prometheus is an open-source monitoring and metrics collection system that is widely used with Kubernetes.
It collects and stores metrics such as:
CPU usage
Memory usage
Pod information
Node metrics
Application metrics
Kubernetes object metrics
Prometheus stores metrics and allows users to query them using PromQL.

4. What is Grafana?
**💡 Answer:**

Grafana is a visualization and dashboarding tool. It connects to data sources such as Prometheus and displays metrics using graphs, charts, dashboards, and panels.
In the session, Prometheus was used for collecting/storing metrics, while Grafana was used for visualization.
Grafana commonly runs on port 3000.

5. What is the difference between Prometheus and Grafana?
**💡 Answer:**

Prometheus
Grafana
Monitoring and metrics system
Visualization and dashboard system
Collects metrics
Displays metrics
Stores/query metrics
Queries data sources
Uses PromQL
Uses queries from configured data sources
Acts as a metrics backend
Acts as a visualization layer

A common architecture is:
Kubernetes → Exporters/Metrics → Prometheus → Grafana

6. Why is Prometheus commonly used with Kubernetes?
**💡 Answer:**

Prometheus is well suited for Kubernetes because it can monitor Kubernetes components and workloads and integrates with Kubernetes monitoring components.
The session highlighted it as an open-source monitoring solution particularly suitable for Kubernetes environments.
It can monitor:
Nodes
Pods
Deployments
Services
Kubernetes resources
Application metrics

7. What is Helm?
**💡 Answer:**

Helm is a package manager for Kubernetes.
Similar to how Linux uses package managers such as apt or yum, Helm helps manage Kubernetes applications.
Helm can be used to:
Install applications.
Upgrade applications.
Remove applications.
Manage Kubernetes application configurations.
Helm packages are called Helm Charts.
For example, instead of manually creating many Kubernetes manifest files for Prometheus and Grafana, a Helm Chart can provide the required Kubernetes resources.

8. What is a Helm Chart?
**💡 Answer:**

A Helm Chart is a collection of Kubernetes configuration templates and files used to package and deploy an application.
A chart can contain:
Kubernetes deployment definitions.
Services.
Configurations.
Templates.
values.yaml.
Other required resources.
The session specifically emphasized the importance of values.yaml, which can be customized according to deployment requirements.

9. What is values.yaml in Helm?
**💡 Answer:**

values.yaml contains configurable values used by Helm templates.
Instead of modifying Kubernetes manifests directly, we can customize values through values.yaml.
For example, we can configure:
Application versions.
Resource limits.
Replica counts.
Service configuration.
Other deployment-specific settings.
This makes Helm deployments reusable and customizable.

10. What are Node Exporter and Kube-State-Metrics?
**💡 Answer:**

Node Exporter collects metrics related to a particular infrastructure node, such as CPU, memory, disk, and other system-level information.
Kube-State-Metrics provides metrics about the state of Kubernetes resources, such as:
Pods
Deployments
Services
ReplicaSets
Other Kubernetes objects
In simple terms:
Node Exporter → Node/system-level metrics
Kube-State-Metrics → Kubernetes object/state metrics
Prometheus can collect these metrics and Grafana can visualize them.

10 Scenario-Based Interview Questions & Answers
### ❓ Q1: Scenario: Your production application is suddenly down. How would monitoring help you troubleshoot it?

**💡 Answer:**

I would first check the monitoring dashboard to determine whether the problem is related to infrastructure or the application.
I would check:
Node health.
CPU and memory usage.
Pod status.
Deployment status.
Network-related metrics.
Application metrics.
Recent alerts.
Relevant logs.
If a node has high CPU or memory usage, I would investigate the resource-consuming processes or pods. If the pods are failing, I would inspect the pod status and events.
The goal is to identify the issue quickly instead of manually checking every server.

### ❓ Q2: Scenario: You have 1,000 Kubernetes pods. Can you manually check the logs and resource usage of every pod?

**💡 Answer:**

No. Manually checking 1,000 pods would be inefficient and error-prone.
I would implement centralized continuous monitoring using Prometheus and Grafana. Metrics from Kubernetes components and nodes can be collected centrally, and Grafana can provide dashboards for monitoring the entire environment.
This provides a centralized view instead of requiring manual inspection of every pod.

3. Scenario: A server goes down and its local logs are no longer available. How would you investigate the incident?
**💡 Answer:**

I would use centralized monitoring and centralized log collection.
If monitoring or logging data has already been collected in a separate centralized system, the failure of the original server does not necessarily destroy the collected information.
I would use the centralized monitoring system to determine:
When the server failed.
What resource usage looked like before failure.
Whether any alerts were triggered.
Whether there was an abnormal CPU, memory, disk, or network condition.

4. Scenario: An attacker deletes logs from a compromised server. How can centralized monitoring help?
**💡 Answer:**

If logs and monitoring information are continuously sent to a centralized location, deleting logs from the compromised server does not necessarily remove the already-collected centralized data.
I would investigate the centralized monitoring/logging system to determine:
When abnormal activity started.
What happened before the compromise.
Which systems were affected.
Whether unusual resource or network behavior occurred.
Centralized monitoring therefore provides an additional layer of visibility.

5. Scenario: Your Kubernetes cluster has very high CPU usage. How would you investigate it?
**💡 Answer:**

I would start with the monitoring dashboard and identify which node or workload is consuming excessive CPU.
My investigation would be:
Check node CPU utilization.
Identify high-consuming pods.
Check whether the application workload has increased.
Check pod resource requests and limits.
Check whether any application process is consuming abnormal CPU.
Review relevant logs and metrics.
Determine whether scaling is required.
The session also discussed HPA, or Horizontal Pod Autoscaling, as a mechanism for automatically scaling pods based on workload.

6. Scenario: You need to deploy Prometheus and Grafana into a Kubernetes cluster. Would you manually create every manifest?
**💡 Answer:**

Not necessarily. I would use Helm because Prometheus and Grafana Helm Charts are available.
The general process would be:
Create or access the Kubernetes cluster.
Create a dedicated monitoring namespace.
Add the required Helm repository.
Update the Helm repository.
Install the Prometheus/Grafana Helm Chart.
Verify the deployed Kubernetes resources.
Configure Grafana with Prometheus as its data source.
Create or import dashboards.
This reduces manual configuration and makes the deployment easier to manage.

7. Scenario: Your company wants monitoring components isolated from application workloads. What would you do?
**💡 Answer:**

I would create a separate Kubernetes namespace for monitoring components.
For example:
```bash
kubectl create namespace monitor
```

Then I would deploy Prometheus and Grafana into that namespace.
The advantage is resource and administrative isolation. Monitoring components can be managed separately from business applications, and access can be assigned to the appropriate support or operations team.

8. Scenario: Grafana is running, but no Prometheus metrics are appearing in Grafana. What would you check?
**💡 Answer:**

I would first verify that Prometheus is running and accessible.
Then I would check the Grafana Data Source configuration.
The troubleshooting process would be:
Verify the Prometheus pod is running.
Verify the Prometheus service.
Check Prometheus accessibility.
Open Grafana configuration.
Verify Prometheus is configured as a data source.
Verify the Prometheus endpoint/URL.
Test the data source connection.
Run a PromQL query in Grafana.
Check whether Prometheus itself contains the expected metrics.
Grafana needs a correctly configured data source to fetch and visualize Prometheus metrics.

9. Scenario: Your Helm repository was added several months ago. Before installing a monitoring application, what should you do?
**💡 Answer:**

I would update the Helm repositories before installing the application.
The session covered:
```bash
helm repo update
```

This ensures that the local Helm repository information is updated with the latest available chart information.
The typical flow is:
```bash
helm repo add <repo-name> <repo-url>
helm repo update
```

Then the required chart can be installed.

### 📌 10. Scenario: You need to monitor both Kubernetes resources and the underlying nodes. Which components would you consider?

**💡 Answer:**

I would use different metric sources depending on what I need to monitor.
For Kubernetes resource/state information, I would use Kube-State-Metrics.
For node-level infrastructure metrics, I would use Node Exporter.
A simplified architecture would be:
Kubernetes Nodes → Node Exporter → Prometheus
Kubernetes Objects → Kube-State-Metrics → Prometheus
Prometheus → Grafana → Dashboards
This allows infrastructure-level and Kubernetes-level metrics to be monitored from a centralized system.

