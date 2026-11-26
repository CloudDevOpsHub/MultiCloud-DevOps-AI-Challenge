## Key Outcomes

Day 6 of the Docker & Kubernetes series (conducted as Day 11 of Kubernetes & Docker) focused on hands-on production Kubernetes workflows on a Google Kubernetes Engine (GKE) cluster. Rather than using slides, the session walked directly through a live GitHub repository (`k8s-demo`) to deploy and troubleshoot a containerized NGINX application end-to-end. Core topics included creating isolated Namespaces, managing sensitive and non-sensitive configuration with Secrets and ConfigMaps, exposing workloads via ClusterIP Services and Ingress, scaling and rolling out updates, debugging pod failures (`ImagePullBackOff`, `CrashLoopBackOff`, probes), understanding deployment strategies (Rolling Update, Blue-Green, Canary), cluster upgrades, and introducing continuous monitoring with Prometheus and Grafana via Helm.

---

## Decisions Made

- **Use GitHub `k8s-demo` repository directly** (bookmark CloudDevOpsHub GitHub) for step-by-step commands instead of relying on PPT slides
- **Use a 1-node GKE cluster with 30 GB disk** (standard smaller machine type, avoiding Europe region) for practice labs
- **Always switch to a dedicated Namespace** (e.g., `k8s-workshop` or `dev`) via `kubectl config set-context` rather than deploying into the `default` namespace
- **Keep sensitive credentials in Kubernetes Secrets (Base64-encoded)** and never hardcode secrets or PAT tokens in open code or plain YAML manifest files
- **Use `ClusterIP` Services + Ingress** instead of creating a separate `LoadBalancer` external IP for every microservice, as external IPs are chargeable
- **Use `kubectl create` for initial resource creation** and `kubectl apply` for subsequent updates (both work, but this is the recommended best practice)
- **Modify replica counts in the YAML manifest file** rather than only via `kubectl scale`, because re-applying/rolling out the manifest enforces the YAML's desired state
- **Install Prometheus and Grafana in a dedicated `monitoring` namespace using Helm** instead of relying solely on chargeable cloud logging services (CloudWatch / Google Cloud Logging)
- **Delete GKE practice clusters after use** to avoid unnecessary cloud billing

---

## GKE Cluster Setup & Namespace Isolation

### Cluster & Repository Workflow

- Created a GKE cluster (`day 11`) with 1 node and 30 GB disk, then connected via Google Cloud Shell (`Connect` → `Run in Cloud Shell`)
- Cloned the `k8s-demo` repository from CloudDevOpsHub GitHub (`git clone <repo-url>`) and navigated into the directory

### Namespaces & Current Context

- **Namespace:** A logical, isolated environment within a Kubernetes cluster used to separate workloads such as `dev`, `pre-prod`, `prod`, `experimental`, `workshop`, or `demo`
- **Default behavior:** If no namespace is selected, Kubernetes deploys resources into the `default` namespace
- Created the workshop namespace and verified it with `kubectl get ns`
- Set the active context namespace using `kubectl config set-context --current --namespace=<namespace-name>` so all subsequent `kubectl` commands target that namespace automatically

---

## Kubernetes Secrets & ConfigMaps

### Secrets (`secrets.yml`)

- Created a Secret named `db-secret` (following standard company naming conventions such as `db-secrets`, often prefixed with `prod` or `pre-prod`)
- **Type `Opaque`:** Indicates sensitive, non-transparent key-value data
- **Base64 encoding:** Kubernetes Secrets store values in Base64-encoded format (`echo -n '<value>' | base64`)
- **Inspection commands:**
    - `kubectl get secrets` — list secrets in the current namespace
    - `kubectl describe secret <name>` — inspect secret metadata and keys
    - `kubectl get secret <name> -o yaml` — view the full YAML definition of a running resource directly from the CLI
- **Security lesson (PAT Token & Account Freeze Q&A):** Never commit Personal Access Tokens (PAT) or credentials into application code or manifest files. If a PAT token is exposed, 2FA will not stop API abuse — immediately revoke/expire the token (e.g., GitLab → Edit Profile → Access Tokens) and disable/rotate the affected IAM user/resources

### ConfigMaps (`configmap.yml` & Custom `index.html`)

- **ConfigMap:** Stores non-sensitive configuration data such as environment mode (`production`), database URLs, and service endpoints so applications can connect to each other
- Executed the ConfigMap manifest and verified entries with `kubectl describe configmap <name>`
- Note: Running `kubectl describe configmap` without specifying a name displays all ConfigMaps in the namespace, including Kubernetes's built-in `kube-root-ca.crt` CA certificate
- **Mounting `index.html` via ConfigMap:**
    - Created a `web-content` ConfigMap from a local `index.html` file
    - Mounted it into the NGINX Deployment container (equivalent to Docker's `-v` volume mount)
    - Triggered a rollout so pods restarted with the mounted `index.html`, then refreshed the browser to confirm the custom page loaded

---

## Deployments, Scaling, Services & Ingress

### Deployment & Pod Lifecycle

- Deployed `nginx` with `replicas: 2` (referencing `db-secret` and ConfigMap values in the pod template)
- Observed pod startup progression via `kubectl get pods`: `Pending` → `ContainerCreating` → `Running`

### Scaling & Desired State Catch

- Scaled the deployment manually from 2 to 5 replicas using `kubectl scale deployment <name> --replicas=5`
- **Important catch demonstrated live:** When a rollout/re-apply was later executed from the deployment YAML file (which still specified `replicas: 2`), Kubernetes terminated 3 pods and reverted the count back to 2
- **Reason:** Kubernetes continuously reconciles the actual state against the **desired state** in the YAML manifest. Manual `kubectl scale` changes are temporary unless the YAML file is updated
- **Minimum vs. Desired vs. Maximum:** Minimum is the hard lower limit of pods required to run the application; Desired is the target replica count Kubernetes tries to maintain; Maximum is the upper bound during autoscaling
- Also briefly noted **Pod Disruption Budget (PDB)** (policy limiting how many pods can be unavailable during disruptions) and **StatefulSets** (used when pods require distinct state/storage/identities)

### Services (`ClusterIP`), Ingress & Web Preview

- **Three Service types:** `ClusterIP` (internal cluster communication via `kube-proxy`), `NodePort`, and `LoadBalancer` (provisions a chargeable external IP)
- Deployed the NGINX Service as `ClusterIP` (no external IP)
- **Ingress:** Manages external HTTP/HTTPS traffic and path-based routing rules to internal services without provisioning a separate LoadBalancer IP per microservice
    - Demonstrated using GCP's native Ingress; third-party controllers (such as NGINX Ingress Controller) are also common
- **Port Forwarding & Web Preview:**
    - Forwarded local Cloud Shell port `8080` to service port `80` (`kubectl port-forward svc/<service-name> 8080:80`)
    - Used Google Cloud Shell's **Web Preview** (configured on port `8080`) to view the running application in the browser
    - For production custom domains, map the external IP in a DNS provider (AWS Route 53, GoDaddy, Hostinger)

---

## Rollouts, Rollbacks & Deployment Strategies

- **Rollout:** Deploying a new application version or configuration change across pods
- **Rollback:** Reverting back to the previous stable version if a rollout fails (`kubectl rollout undo`)
- **Three Deployment Strategies Covered:**

|     Strategy     |                                      How It Works                                      |                             Notes                              |
|------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------|
| **Rolling Update** | Replaces pods one by one (terminates 1 old pod, starts 1 new pod, repeats)             | Default Kubernetes strategy; avoids full downtime              |
| **Blue-Green**     | Maintains two identical environments (Blue = current, Green = new); switches traffic   | Fast rollback; doubles infrastructure resource usage           |
| **Canary**         | Routes a small percentage of traffic (e.g., 10%, 20%, 30%) to the new version first    | Validates features with a subset of users before full rollout  |

---

## Kubernetes Troubleshooting & Cluster Upgrades

### Step-by-Step Debugging Workflow

- `kubectl get pods` — check current pod status (`Running`, `Pending`, `ImagePullBackOff`, `CrashLoopBackOff`)
- `kubectl get pods -o wide` — view wider details including which **node** each pod is running on (helps isolate node-level issues)
- `kubectl describe pod <pod-name>` — inspect the **Events** section (provides ~90% of troubleshooting clues)
- `kubectl logs <pod-name>` — check container logs
- `kubectl logs -l app=<app-name>` — view logs across all pods belonging to an application
- `kubectl logs <pod-name> --previous` — inspect logs from the previous crashed container instance
- `kubectl exec -it <pod-name> -- /bin/sh` (or `bash`) — open an interactive shell inside a running container

### Common Errors & Root Causes

- **Quota / Resource `Pending`:** Insufficient CPU/memory or hit resource limits → increase resource limits or expand cluster capacity
- **`ImagePullBackOff`:** Incorrect image name or tag, or pulling from a private registry (Docker Hub, GCR, AWS ECR) without valid credentials or network access
- **`CrashLoopBackOff`:** Container starts and repeatedly crashes (e.g., memory limit set to 300 MB when the app needs 500 MB) and Kubernetes keeps restarting it
- **Readiness & Liveness Probes:**
    - **Readiness Probe:** Checks whether the pod is ready to accept incoming traffic
    - **Liveness Probe:** Checks whether the container is still alive
    - If an application takes ~5 minutes to initialize and probe timeouts are set too short (e.g., 2 minutes), Kubernetes kills and restarts the pod before startup finishes → increase probe delay/threshold (up to 500 seconds for slow-starting apps)
- **Mount / Kubelet / Scheduler issues:** Invalid volume mount paths or node-level `kubelet` rejections

### Cluster Upgrades & Maintenance

- Minor Kubernetes upgrades can be automated in GKE via **rolling node updates** (upgrading one node at a time)
- **Taints & Tolerations / Draining:** Before patching or upgrading a node, prevent new pods from scheduling on it, move existing workloads to healthy nodes, patch the node, and then bring workloads back
- **Major version upgrades (Real-time flow):** Test first in Dev (e.g., 1.36) → validate in QA → deploy to a new Production cluster (e.g., moving from 1.35 to 1.36; 1.37 is latest, 1.38 planned)

---

## Continuous Monitoring — Prometheus & Grafana

### Why Prometheus & Grafana Are Used

- Manually checking thousands of pods/containers is impossible, and cloud-native logging services (AWS CloudWatch, Google Cloud Logging) become very expensive at high log volumes
- **Prometheus:** Scrapes/pulls time-series metrics (CPU, memory, traffic every few seconds) using exporters (`node-exporter`, `kube-state-metrics`) and stores them in its local time-series database
- **Grafana:** Visualization layer that runs **PromQL** queries against Prometheus to render dashboards/graphs and trigger alerts (Email, PagerDuty, SMS, WhatsApp — e.g., CPU > 70% or Memory > 80% for 5 minutes)
- **Installation:** Deployed into a separate `monitoring` namespace using **Helm** (`prometheus-grafana` stack)

### Team Ownership Model (L1 / L2 / L3 / DevOps)

- **DevOps Engineer:** Sets up and configures the Prometheus + Grafana monitoring stack on Kubernetes
- **L2 / L3 Team:** Maintains the setup, builds custom Grafana dashboards, and configures alert rules and email triggers
- **L1 Team:** Monitors dashboards, logs, and alerts daily and escalates issues (both freshers and experienced engineers may work in L1 depending on the company)

---

## Interview Preparation Highlights

- **Namespace context question:** Be ready to explain how you isolate `dev`, `pre-prod`, and `prod` using Namespaces and set the current namespace context via CLI
- **YAML retrieval question:** Know `kubectl get <resource> <name> -o yaml` to inspect running Kubernetes object definitions
- **Desired state trap:** Explain why manual `kubectl scale` changes get overwritten when a deployment YAML with an older replica count is re-applied
- **Troubleshooting flow:** Walk interviewers cleanly through `get pods` → `get pods -o wide` → `describe pod` (Events) → `logs` / `logs --previous` → `exec`
- **Deployment strategies:** Clearly contrast Rolling Update (default), Blue-Green, and Canary with real-world examples
- **Monitoring pitch for L1/L2 engineers:** Highlight setting up Prometheus and Grafana with Helm in a `monitoring` namespace and configuring PromQL-based alerts

---

## Action Items

- **All students:** Bookmark the CloudDevOpsHub GitHub organization and clone the `k8s-demo` repository
- **All students:** Practice the full `k8s-demo` lab end-to-end: Namespace setup, Secrets, ConfigMap (`index.html` mount), Deployment, `ClusterIP` Service, Ingress, `port-forward`, `scale`, `rollout`, and troubleshooting commands
- **All students:** Write down the troubleshooting flow and read it aloud to build interview speaking fluency
- **All students:** Update and submit resumes for review (especially adding practical DevOps/Jenkins/Kubernetes hands-on work); keep track of previous resume versions
- **All students:** Delete the GKE practice cluster immediately after completing the lab to avoid billing charges
- **Vikas:** Conduct the upcoming Prometheus & Grafana hands-on setup and PromQL session in the next class
- **Vikas:** Schedule another resume review session for the remaining ~20–25 students

---

## Open Questions

- **Headless Services:** Shubhendu asked about headless services in Kubernetes; Vikas asked Mandhir and students to research and test it practically before concluding in the next session
- **GitOps / ArgoCD & GitHub Actions:** Students requested sessions on GitOps/ArgoCD and GitHub Actions; Vikas noted GitLab CI/CD and upcoming DevSecOps CI/CD projects (Project 1 and Project 4) are already planned, and will consider a poll for extra topics
- **GKE Billing / Credit Expiry:** Several students raised questions about 90-day GCP trial expiry and negative balance refunds; Vikas advised checking the billing section for refund eligibility on unused/negative balances

---

# Interview Preparation — 20 Questions and Answers

These questions are based on the Day 6 topics: Namespaces, Secrets, ConfigMaps, Deployments, Scaling, Services, Ingress, Rollouts, Troubleshooting, Cluster Upgrades, and Prometheus/Grafana.

### 1. What is a Kubernetes Namespace, and why is it used?
**Answer:** A Namespace is a logical, isolated environment inside a Kubernetes cluster. Companies use namespaces to separate environments such as `dev`, `pre-prod`, `prod`, or `workshop` on the same cluster so resources do not clash.

### 2. Which namespace does Kubernetes use by default if you do not specify one, and how do you switch your active namespace?
**Answer:** Kubernetes uses the `default` namespace. You can set your current working namespace so all commands run inside it using:
```bash
kubectl config set-context --current --namespace=<namespace-name>
```

### 3. What is the difference between a Kubernetes Secret and a ConfigMap?
**Answer:** A **Secret** stores sensitive data (like database usernames and passwords) in Base64-encoded format, whereas a **ConfigMap** stores non-sensitive configuration data (like application mode `production`, URLs, or mounted config files like `index.html`).

### 4. What does `type: Opaque` mean in a Kubernetes Secret?
**Answer:** `Opaque` is the default Secret type for arbitrary user-defined sensitive key-value pairs (such as DB credentials), indicating the data is non-transparent and meant to be treated as sensitive.

### 5. How can you view the YAML configuration of an already running Kubernetes resource from the CLI?
**Answer:** Append `-o yaml` to the `kubectl get` command, for example:
```bash
kubectl get secret db-secret -o yaml
```
You can also check it from the Kubernetes UI.

### 6. What is the difference between `kubectl create -f` and `kubectl apply -f`?
**Answer:** Both can create resources from a YAML file, but the recommended best practice is to use `kubectl create -f` when creating a resource for the first time and `kubectl apply -f` when applying updates to an existing configuration.

### 7. How do you pass a custom `index.html` file into an NGINX pod in Kubernetes without rebuilding the image?
**Answer:** Create a ConfigMap from the `index.html` file and mount it as a volume inside the Deployment's pod template (similar to `-v` volume mounting in Docker), then trigger a rollout so the pods pick up the mounted file.

### 8. If you manually scale a Deployment to 5 replicas using `kubectl scale`, why might it revert back to 2 replicas after a YAML rollout?
**Answer:** Kubernetes continuously enforces the **desired state** defined in the Deployment YAML manifest. If the YAML file still specifies `replicas: 2`, re-applying or rolling out from that file overwrites the manual CLI change and terminates the extra 3 pods.

### 9. What is the difference between `ClusterIP`, `NodePort`, and `LoadBalancer` Services?
**Answer:**
- **ClusterIP:** Exposes the service only inside the cluster (default, no external IP).
- **NodePort:** Exposes the service on a port across cluster nodes.
- **LoadBalancer:** Exposes the service externally using a cloud provider's load balancer and assigns a chargeable external IP.

### 10. Why do companies use Ingress instead of creating a LoadBalancer Service for every microservice?
**Answer:** Every `LoadBalancer` Service provisions a separate external IP, which is costly and exposes too many public endpoints. **Ingress** acts as a single entry point that routes external HTTP/HTTPS traffic using path-based or host-based rules to internal `ClusterIP` Services.

### 11. What is `kubectl port-forward` used for?
**Answer:** `kubectl port-forward` maps a local port (for example `8080`) to a port on a Service or Pod (for example `80`) so you can test an internal `ClusterIP` application privately in your browser (such as via Cloud Shell Web Preview) without exposing an external IP.

### 12. What is the difference between a rollout and a rollback in Kubernetes?
**Answer:** A **rollout** deploys a new version or configuration change of an application, while a **rollback** reverts the deployment back to the previous working version if the new rollout fails.

### 13. Explain the three Kubernetes deployment strategies discussed in the session.
**Answer:**
- **Rolling Update:** The default strategy; replaces pods one by one (kills one old pod, creates one new pod) to avoid downtime.
- **Blue-Green:** Runs two identical environments (Blue = current, Green = new) and switches traffic to Green after verification.
- **Canary:** Exposes the new version to a small percentage of users first (e.g., 10%–30%) before rolling it out to everyone.

### 14. What does `kubectl get pods -o wide` show that `kubectl get pods` does not?
**Answer:** `-o wide` provides wider operational details, most importantly showing **which Node** each pod is running on, along with its pod IP. This helps determine whether an issue is tied to a specific node.

### 15. How do you check logs for a container that already crashed and restarted, or across all pods of an app?
**Answer:** Use `--previous` to see logs from the previous crashed container instance, and `-l` (label selector) to view logs across all pods of an application:
```bash
kubectl logs <pod-name> --previous
kubectl logs -l app=<app-name>
```

### 16. What causes `ImagePullBackOff` in Kubernetes?
**Answer:** Kubernetes cannot pull the container image. Common reasons include a typo in the image name, an invalid image tag, or pulling from a private registry (Docker Hub, GCR, AWS ECR) without proper credentials or internet/registry access.

### 17. What is `CrashLoopBackOff`, and how can Liveness/Readiness probes trigger it?
**Answer:** `CrashLoopBackOff` means a container starts, crashes repeatedly, and Kubernetes keeps restarting it (for example, due to insufficient memory like 300 MB assigned to a 500 MB app). If a slow-starting app takes 5 minutes to come up but the Liveness probe checks and fails after 2 minutes, Kubernetes will keep killing and restarting the pod in a loop.

### 18. How are Kubernetes nodes upgraded safely in production without breaking running applications?
**Answer:** Nodes are upgraded one by one using rolling node updates. Using taints/draining, new scheduling is blocked on the target node, existing workloads are moved to other healthy nodes, the node is patched/upgraded, and then workloads can run on it again.

### 19. What are Prometheus and Grafana, and how do they work together?
**Answer:** **Prometheus** is a monitoring tool that pulls time-series metrics (CPU, memory, traffic) from the Kubernetes cluster using exporters and stores them in its local time-series database. **Grafana** connects to Prometheus, runs **PromQL** queries, visualizes the metrics in dashboards, and triggers alerts.

### 20. How is the Prometheus-Grafana stack typically installed in Kubernetes, and how do teams divide the work?
**Answer:** It is installed into a dedicated `monitoring` namespace using **Helm**. Typically, the DevOps engineer sets up the stack, L2/L3 engineers maintain dashboards and alert rules (e.g., CPU > 70% or Memory > 80% for 5 minutes), and L1 engineers monitor the logs and alerts.

---

# Scenario-Based Interview Questions — 20 Questions and Answers

### 1. You deploy a new application to your GKE cluster, and it lands in the `default` namespace instead of `dev`. How do you fix your CLI so all future commands run in `dev`?
**Answer:** Create the `dev` namespace if it does not exist, then update your current context to point to `dev`:
```bash
kubectl create namespace dev
kubectl config set-context --current --namespace=dev
```
Now every `kubectl` command runs in `dev` without needing `-n dev`.

### 2. A teammate manually scaled a deployment from 2 to 5 replicas using `kubectl scale` during a traffic spike. Later, you ran a rollout from the GitHub YAML file, and the pods dropped back to 2. Why did this happen, and how do you prevent it?
**Answer:** The YAML manifest still had `replicas: 2`, so Kubernetes enforced that desired state during the rollout and terminated the 3 extra pods. To make scaling permanent, update `replicas: 5` directly inside the Deployment YAML file before applying or rolling out.

### 3. You ran `kubectl describe configmap` without giving a name and saw a certificate/key in the output. Is that a security leak in your ConfigMap?
**Answer:** No. Running `kubectl describe configmap` without a resource name describes **all** ConfigMaps in that namespace, including Kubernetes's automatically created `kube-root-ca.crt` ConfigMap which holds the cluster's public CA certificate. Always pass the specific ConfigMap name: `kubectl describe configmap <name>`.

### 4. You updated a ConfigMap containing `index.html` for an NGINX Deployment, but the browser still shows the old NGINX welcome page. What should you do?
**Answer:** Pods do not automatically restart when a ConfigMap volume/reference is updated in this flow. Trigger a rollout restart on the Deployment so new pods spin up with the updated ConfigMap mounted, and then hard-refresh the browser:
```bash
kubectl rollout restart deployment <deployment-name>
```

### 5. Your NGINX Deployment is exposed via a `ClusterIP` Service (no external IP), and you need to quickly verify the UI in Google Cloud Shell. How will you access it?
**Answer:** Use `kubectl port-forward` to map Cloud Shell port `8080` to the Service port `80`:
```bash
kubectl port-forward svc/<service-name> 8080:80
```
Then click **Web Preview** in Google Cloud Shell and select port `8080` to view the application in the browser.

### 6. Your team has 15 microservices and wants to expose them externally without paying for 15 separate cloud LoadBalancer external IPs. What architecture will you use?
**Answer:** Keep each microservice behind a `ClusterIP` Service (internal only) and configure a Kubernetes **Ingress** with path-based routing rules so a single external entry point routes traffic to the appropriate internal service.

### 7. A pod is stuck in `Pending` state right after you scaled the Deployment from 2 to 5 replicas. What is the first thing you check?
**Answer:** Run `kubectl describe pod <pod-name>` and read the **Events** section. `Pending` during scaling typically means the cluster or node has insufficient CPU/memory or hit resource quota limits, requiring either adjusted resource limits or a larger/additional node.

### 8. A pod shows `ImagePullBackOff`. Walk through the exact causes you will investigate.
**Answer:** Run `kubectl describe pod <pod-name>` and check Events, then verify:
1. Whether the image name or tag in the YAML manifest is misspelled or does not exist.
2. If it is a private registry (Docker Hub, GCR, AWS ECR), whether the cluster/node has valid pull credentials (`imagePullSecrets`) and network access to the registry.

### 9. A newly deployed application takes 5 minutes to boot up, but Kubernetes keeps killing and restarting it at the 2-minute mark with `CrashLoopBackOff`. What is wrong and how do you fix it?
**Answer:** The Liveness/Readiness probe timeout or `initialDelaySeconds` is configured shorter than the application's startup time (e.g., 2 minutes vs. 5 minutes). Increase the probe initial delay/threshold (for example, up to 500 seconds for slow-initializing apps) so Kubernetes waits for startup before checking health and doesn't kill the pod before logs can even be inspected.

### 10. A pod crashed and restarted, so `kubectl logs <pod-name>` only shows fresh empty startup logs. How do you find out why it crashed?
**Answer:** Check the logs of the previous terminated container instance using `--previous`, and check pod Events:
```bash
kubectl logs <pod-name> --previous
kubectl describe pod <pod-name>
```

### 11. Only 2 out of 5 pods for your application are failing, and you suspect a specific worker node has a problem. How do you verify which node those 2 pods are running on?
**Answer:** Run:
```bash
kubectl get pods -o wide
```
Check the `NODE` column. If both failing pods are scheduled on the same node, investigate that node's health, `kubelet` status, or disk/memory pressure.

### 12. An application pod has a memory limit of 300 MB in its YAML, but the app requires 500 MB under load and keeps crashing. What happens in Kubernetes, and how do you fix it?
**Answer:** Unlike a laptop that might simply hang, Kubernetes kills the container when it exceeds its memory limit and repeatedly restarts it, resulting in `CrashLoopBackOff`. Increase the memory limit in the Deployment YAML to accommodate the 500 MB requirement and apply the update.

### 13. You need to inspect all logs across 5 replicas of an application at once rather than checking pod by pod. How do you do it?
**Answer:** Use a label selector with `kubectl logs`:
```bash
kubectl logs -l app=<app-label>
```
This streams or prints logs from all pods matching that application label together.

### 14. Your manager asks you to deploy a new feature to production with zero downtime and without spinning up double infrastructure. Which deployment strategy applies?
**Answer:** **Rolling Update** (Kubernetes's default strategy). It replaces pods one by one — bringing up a new version pod and terminating an old one sequentially — so traffic continues to be served without doubling cluster capacity.

### 15. Your product team wants to release a new feature only to 10% of users first to verify stability before rolling it out globally (similar to WhatsApp feature rollouts). Which strategy is this?
**Answer:** **Canary Deployment**. A small percentage of traffic (e.g., 10%, then 20%–30%) is routed to the new version while the majority stays on the stable version. Once validated, the rollout expands to 100%.

### 16. A new rollout broke production immediately after deployment. How do you restore the previous working version?
**Answer:** Trigger a **rollback** to the previous revision of the Deployment:
```bash
kubectl rollout undo deployment <deployment-name>
```

### 17. Someone on the team accidentally changed the production replica count from 3 to 1 using CLI access. How do you prevent unauthorized manual changes in production?
**Answer:** Do not grant direct production `kubectl` write access to developers or general users. Enforce **RBAC** (Role-Based Access Control) restrictions and route all production deployments through automated CI/CD pipelines (Jenkins/GitLab) or GitOps tools (ArgoCD) synced with Git.

### 18. A developer accidentally pushed a Personal Access Token (PAT) to code, leading to abuse and an AWS account freeze. The team suggests enabling 2FA to fix it. Is 2FA enough?
**Answer:** No. 2FA only protects interactive console logins, not API/PAT token access. You must immediately revoke/delete the exposed PAT token (e.g., in GitLab under Edit Profile → Access Tokens), disable/delete the compromised IAM user or access keys, and store secrets properly in Kubernetes Secrets or a secret manager — never in code or open manifests.

### 19. You need to upgrade your Kubernetes cluster from v1.35 to v1.36 without breaking production workloads. What process should you follow?
**Answer:** First test the upgrade in `Dev` (v1.36), then validate in `QA`, and finally migrate `Production` (either via a new v1.36 cluster or rolling node updates). During node patching/upgrades, perform one node at a time: block new pods (taint/cordon), drain existing pods to healthy nodes, patch the node, and restore scheduling.

### 20. Your company runs thousands of containers, and cloud logging (CloudWatch / Google Cloud Logging) is becoming too expensive. How do you set up cost-effective monitoring and alerting?
**Answer:** Deploy **Prometheus and Grafana** inside a dedicated `monitoring` namespace using **Helm**. Prometheus scrapes time-series metrics via exporters (`node-exporter`, `kube-state-metrics`) into its local storage, and Grafana runs **PromQL** queries to visualize dashboards and send alerts (Email, PagerDuty, SMS, WhatsApp) when thresholds like CPU > 70% or Memory > 80% for 5 minutes are breached.

---

## Quick Practical Revision Commands

Run these commands in your `k8s-demo` lab and be ready to explain the output.

```bash
# Clone the demo repository
git clone <k8s-demo-repo-url>
cd k8s-demo

# Create and switch to a dedicated namespace
kubectl create namespace k8s-workshop
kubectl get ns
kubectl config set-context --current --namespace=k8s-workshop

# Create and inspect Secrets
kubectl create -f secrets.yml
kubectl get secrets
kubectl describe secret db-secret
kubectl get secret db-secret -o yaml

# Create and inspect ConfigMaps
kubectl create -f configmap.yml
kubectl describe configmap <configmap-name>

# Create ConfigMap from index.html
kubectl create configmap web-content --from-file=index.html

# Deploy application, Service, and Ingress
kubectl apply -f deployment.yml
kubectl apply -f service.yml
kubectl apply -f ingress.yml

# Check pods and node placement
kubectl get pods
kubectl get pods -o wide

# Scale deployment and check rollout
kubectl scale deployment <deployment-name> --replicas=5
kubectl rollout restart deployment <deployment-name>
kubectl rollout undo deployment <deployment-name>

# Port-forward internal ClusterIP service for Web Preview
kubectl port-forward svc/<service-name> 8080:80

# Troubleshooting commands
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl logs -l app=<app-label>
kubectl exec -it <pod-name> -- /bin/sh
```

## Interview Preparation Tip

Do not just memorize commands — practice the entire troubleshooting flow out loud (`get pods` → `get pods -o wide` → `describe pod` Events → `logs` / `--previous`). Be ready to explain **why** `ClusterIP` + Ingress saves cost over multiple LoadBalancers, **why** YAML desired state overrides manual `kubectl scale`, and **how** Prometheus + Grafana with Helm handle monitoring and alerting in real-time teams.
