**## Key Outcomes**

The session covered advanced Kubernetes topics including all five deployment models (Rolling Update, Recreate, Blue-Green, Canary, and A/B Testing), a hands-on practical demonstration of Horizontal Pod Autoscaling (HPA) on a GKE cluster, and a discussion of cost optimization strategies in Kubernetes. Students gained both theoretical understanding and practical exposure to configuring HPA with NGINX, metrics server installation, and load testing using BusyBox. The session concluded with interview preparation guidance, cluster creation methods (EKS, kubeadm), and troubleshooting scenarios such as node hangs and node draining.

**---**

**## Deployment Models Covered**

**### Rolling Update (Default)**

\- **\*\*Definition:\*\*** Gradual, one-by-one replacement of pods from old version (V1/blue) to new version (V2/green) with **\*\*zero downtime\*\***. 

\- Kubernetes terminates one old pod and creates one new pod at a time; traffic continues flowing throughout the process. 

\- **\*\*Rolling Rollback:\*\*** The reverse process — gradually replacing V2 pods with V1 pods, one by one; also called a rolling downgrade. 

\- Practically triggered by editing the image version in the YAML deployment file (e.g., changing \`nginx:1.25\` to a newer version) and applying the change via \`kubectl apply -f\`. 

\- Two key YAML parameters control the behavior: \`maxSurge\` and \`maxUnavailable\` — setting both to 1 enforces strict one-at-a-time replacement. 

\- **\*\*80% of companies use this model by default\*\***; if no deployment model is explicitly configured, Kubernetes defaults to rolling update. 

\- Supports easy rollback to either a previous or a newer version. 

\- In interview contexts: answer "rolling update" when asked about zero-downtime deployment; it is the safest and most universally applicable answer. 

**### Recreate**

\- All existing pods are terminated first, then all new pods are created — results in **\*\*downtime\*\***. 

\- Not recommended for production use; mentioned only for completeness. 

**### Canary Deployment**

\- Releases a new version to a **\*\*small percentage of users\*\*** (e.g., 10%, 15%, 20%, 30%) before full rollout. 

\- Allows controlled exposure: if issues arise, only a small user segment is affected. 

\- Can be implemented manually by adjusting replica counts — e.g., with 4 servers, replacing 1 with a new version automatically routes 25% of traffic to the new version. 

\- Canary can be achieved indirectly within a rolling update by manually controlling which pods are replaced and monitoring traffic split (e.g., 50/50 between old and new pods simultaneously). 

\- Used widely in companies for **\*\*beta feature releases\*\*** and **\*\*country-wise or traffic-segment-based rollouts\*\*** (e.g., Facebook, Netflix, WhatsApp releasing features region by region due to regulatory requirements). 

\- Automation of canary percentage increments (5% → 10% → 20% → 100%) requires scripting or pipeline tooling; cannot be done natively with a single kubectl command. 

\- Requires at least **\*\*two separate deployment files\*\*** (one for stable, one for canary) with distinct configurations. 

**### Blue-Green Deployment**

\- Maintains **\*\*two identical environments\*\*** side by side: Blue (current/live version) and Green (new version being prepared). 

\- Traffic is switched from Blue to Green only after the Green environment has been fully tested internally. 

\- **\*\*High availability\*\*** benefit: if the live environment goes down, the standby is immediately available. 

\- **\*\*Cost concern:\*\*** Running two full environments doubles infrastructure cost; approximately **\*\*70–90% of companies avoid this model\*\*** for this reason. 

\- Cost mitigation strategy: Green environment does not need to run permanently — it can be spun up only when a new release is being prepared, reducing cost by 30–50%. 

\- Traffic switching is handled via a **\*\*load balancer\*\*** (e.g., AWS Application Load Balancer) at the front-end service level. 

\- After Green becomes live, the old Blue environment becomes the next staging environment for the subsequent release cycle (V3 would be deployed to Blue next time). 

\- In interview answers: mention Blue-Green as a valid zero-downtime option but note the cost trade-off; Canary is generally preferred for gradual rollouts. 

**### A/B Testing**

\- Deploys different versions to different user groups simultaneously to compare behavior or feature performance. 

\- Considered unnecessary overhead for DevOps engineers in most cases — QA teams handle this in their own environments. 

\- Depends on architecture, traffic volume, and platform maturity. 

**### Combined / Real-World Approach**

\- In practice, companies often **\*\*combine Rolling + Canary + Blue-Green\*\*** based on client requirements, which change frequently (country-wise, traffic-wise, premium customer segments, etc.). 

\- Vikas emphasized that in interviews, candidates should present this combined approach as their real-world experience rather than citing a single model. 

**---**

**## HPA (Horizontal Pod Autoscaler) — Theory**

\- **\*\*HPA = Horizontal Pod Autoscaling\*\***: automatically scales the number of pods up or down based on observed resource metrics (CPU, memory, or custom traffic metrics). 

\- Core use case: if traffic increases and CPU load exceeds a defined threshold, Kubernetes automatically adds more pods; when load drops, pods are scaled back down. 

\- Key configuration parameters: \`minReplicas\`, \`maxReplicas\`, and a utilization threshold (e.g., CPU percentage). 

\- HPA requires a **\*\*Metrics Server\*\*** to collect resource utilization data from pods and nodes; on managed cloud platforms (GKE, EKS, AKS) it may be pre-installed, but on self-managed clusters it must be installed manually. 

\- HPA works with **\*\*Deployments\*\*** (not standalone pods) because deployments manage replica sets and enable multi-pod scaling. 

\- HPA applies to **\*\*stateless\*\*** applications; stateful workloads require different handling (StatefulSets with persistent volume claims). 

**---**

**## HPA — Hands-On Practical (GKE)**

**### Cluster Setup**

\- A GKE cluster named "HPE" was created with **\*\*1 node per zone, 3 zones total = 3 nodes\*\***, with 10 GB storage per node. 

\- Students connected to the cluster and followed along with each step. 

**### Step 1 — NGINX Deployment with Resource Limits**

\- Created a folder \`batch44/HPA\` and a file \`nginx-deployment.yml\` by copying lines 9–32 from the provided reference document. 

\- Deployed using \`kubectl apply -f nginx-deployment.yml\`; confirmed **\*\*2/2 replicas running\*\*** (replicas set to 2). 

**### Step 2 — Expose as LoadBalancer Service**

\- Exposed the NGINX deployment as a **\*\*LoadBalancer service on port 80\*\*** using \`kubectl expose\`. 

\- Retrieved the **\*\*external IP\*\*** via \`kubectl get svc\`; waited \~30 seconds for the external IP to become available. 

\- Verified NGINX was accessible via the external IP in a browser. 

**### Step 3 — Install Metrics Server**

\- Checked if Metrics Server was pre-installed; installed it by applying the component YAML directly from the official URL using \`kubectl apply\` (line 56 of the reference document). 

\- Verified Metrics Server registration with \`kubectl get apiservices\` (line 60); confirmed it was running. 

**### Step 4 — Create HPA**

\- Command used: \`kubectl autoscale deployment nginx-deployment --cpu-percent=2 --min=2 --max=5\` 

    - \`--cpu-percent=2\`: threshold set to 2% for demo purposes (real-world value would be \~70%) 

    - \`--min=2\`: minimum 2 pods always running

    - \`--max=5\`: scale up to maximum 5 pods

\- Verified HPA creation with \`kubectl get hpa\`; confirmed autoscaler was active. 

**### Step 5 — Load Testing with BusyBox**

\- Launched a **\*\*BusyBox pod\*\*** in interactive mode (\`kubectl run -it --image=busybox\`) to act as a load generator. 

\- Inside the BusyBox pod, ran a \`while true\` loop using \`wget -q -O-\` to continuously hit the NGINX service endpoint, simulating high traffic. 

\- Opened a second terminal tab and ran \`kubectl get hpa -w\` (watch mode) to observe CPU utilization rising in real time. 

\- Observed CPU load climbing (e.g., 11%), triggering pod scale-up from 2 to 5 pods. 

**### Step 6 — Scale-Down Observation**

\- Stopped the load generator with \`Ctrl+C\`; monitored CPU utilization dropping: 11% → 8% → 6% → 5% → 3% → 0%. 

\- Observed pods scaling back down to the minimum (2) after load dropped to 0%. 

\- Demonstrated that the \`-w\` (watch) flag on \`kubectl get hpa\` provides real-time monitoring of the autoscaler state. 

**### Rolling Update Demo (within HPA session)**

\- Used \`kubectl edit deployment nginx-deployment\` to change the NGINX image version (e.g., to \`1.17\`) directly in the running deployment. 

\- Saved the edit; Kubernetes automatically triggered a rolling update — new pods created with updated image, old pods terminated. 

\- Demonstrated manual canary behavior: deleted one pod explicitly (\`kubectl delete pod \<pod-name>\`), resulting in 50/50 traffic split between old and new version pods temporarily. 

**---**

**## Cluster Creation Methods**

**### Managed Cloud (EKS/GKE/AKS)**

\- **\*\*AWS:\*\*** Use **\*\*EKS\*\*** (Elastic Kubernetes Service) — create EKS cluster, configure IAM roles, attach EC2 worker nodes. 

\- GKE and AKS follow similar managed patterns; metrics server and networking components are often pre-installed. 

**### Manual / Self-Managed (kubeadm)**

\- Use **\*\*kubeadm\*\*** to set up a Kubernetes cluster on bare VMs. 

\- Steps: provision multiple VMs → install \`kubeadm\`, \`kubelet\`, \`kubectl\` on all nodes → run \`kubeadm init\` on the master node to initialize the control plane → join worker nodes using the generated join token → configure pod network (e.g., Calico). 

\- Acknowledged as complex; not recommended to practice unless VMs are readily available. 

**---**

**## Kubernetes Aliases (CKA Exam Tip)**

\- **\*\*Alias:\*\*** A short custom name mapped to a longer command, useful for speed during CKA/CKAD exams. 

\- Example: \`alias k=kubectl\` → typing \`k get pod\` executes \`kubectl get pod\`. 

\- Can be further customized: \`alias kgp='kubectl get pod'\`. 

\- **\*\*Caveat:\*\*** Aliases must be set up in each new session unless saved to \`.bashrc\` (permanent alias); if a previous engineer set aliases you're unaware of, the main command always works as a fallback. 

\- Recommendation: Learn the full commands first; use aliases only after you're comfortable with the underlying syntax to avoid confusion. 

**---**

**## Cost Optimization in Kubernetes**

Discussion prompted by a student question; key strategies identified:

\- **\*\*Namespace separation:\*\*** Use separate namespaces (dev, QA, staging) within a single cluster rather than running separate clusters, reducing infrastructure overhead. 

\- **\*\*Node Autoscaling (Cluster Autoscaler):\*\*** Automatically add nodes when demand is high and **\*\*release nodes when not needed\*\***, directly reducing cloud compute costs. 

\- **\*\*HPA (Pod Autoscaling):\*\*** Scale pods down when traffic is low, reducing resource consumption. 

\- **\*\*Spot Instances:\*\*** Use cloud provider spot/preemptible instances for non-critical workloads — these are unused cloud capacity offered at significantly discounted prices via a bidding mechanism. 

\- **\*\*Reserved Instances / Saving Plans:\*\*** Pre-purchase compute capacity (1-year or 3-year terms) for predictable workloads to get discounted rates. 

\- **\*\*Right-sizing resources:\*\*** Avoid over-provisioning CPU/memory requests and limits; size containers appropriately. 

\- **\*\*Blue-Green cost note:\*\*** Always running two full environments is expensive; spin up the Green environment only when a release is being prepared. 

\- **\*\*Important caveat:\*\*** Spot instances are **\*\*not appropriate for mission-critical production workloads\*\*** due to the risk of preemption. 

**---**

**## Troubleshooting: Node Hang Scenario**

Interview question discussed: *\*"If a node is stuck and not allowing pods to run, what do you do?"\**

\- **\*\*Root cause identification:\*\*** Check monitoring/logs first to understand why the node stopped responding (hardware failure, CPU at 100%, OOM, etc.). 

\- **\*\*Best practice (graceful):\*\*** Use \`kubectl drain \<node>\` to **\*\*empty the node\*\*** — evict all pods gracefully so they reschedule on other nodes — before performing maintenance. 

\- **\*\*If node is unresponsive:\*\*** Drain may not be possible; a hard restart of the underlying VM/hardware may be required. 

\- **\*\*Kubernetes self-healing:\*\*** If node health monitoring is configured, Kubernetes can automatically reschedule pods to healthy nodes; if not configured, manual intervention is needed. 

\- **\*\*Multi-zone advantage:\*\*** In a multi-zone cluster, traffic automatically shifts to pods in healthy zones while the problematic node is being fixed. 

\- **\*\*After recovery:\*\*** Release the node cordon, allow pods to reschedule, and verify cluster health via monitoring. 

\- **\*\*Stateful vs. Stateless consideration:\*\*** Stateless pods reschedule cleanly; stateful pods (with persistent volumes) require more careful handling during node failures. 

**---**

**## Networking: Calico**

\- **\*\*Calico\*\*** is a **\*\*CNI (Container Network Interface)\*\*** plugin used to manage pod networking and network security policies in Kubernetes. 

\- Used when manually configuring cluster networking (e.g., with kubeadm). 

\- **\*\*Disadvantage:\*\*** Open-source, requires manual management and configuration; security policies must be defined manually. 

\- On managed cloud platforms (GKE, EKS, AKS), networking is handled automatically with built-in CNI solutions, eliminating the need for manual Calico setup. 

**---**

**## Interview Preparation Highlights**

\- **\*\*Zero downtime deployment question:\*\*** Any of Rolling Update, Canary, or Blue-Green are valid answers; Rolling Update is the safest default answer, but mentioning a combination demonstrates real-world maturity. 

\- **\*\*Deployment model used in your company:\*\*** Default answer is Rolling Update (80% of companies); supplement with Canary or Blue-Green based on actual experience. 

\- **\*\*HPA interview question:\*\*** Explain that HPA monitors CPU/memory via the Metrics Server and automatically scales pods between a defined min and max based on a utilization threshold. 

\- **\*\*Node troubleshooting question:\*\*** Lead with log analysis → drain → reschedule; mention monitoring tools and graceful pod eviction. 

\- **\*\*Cost optimization question:\*\*** Mention namespace separation, node autoscaling, HPA, spot instances, and reserved instances — avoid vague answers like "use containers" which are incorrect in this context. 

\- **\*\*Stateful vs. Stateless:\*\*** Stateless = no persistent background data maintained (web apps); Stateful = requires persistent data storage (databases); HPA works best with stateless workloads. 

\- **\*\*Wipro openings mentioned:\*\*** 10+ years experience, pan-India location, package range 20–40 LPA; students can connect via Pranav's email for referrals. 

**---**

**## Next Steps & Announcements**

\- **\*\*Next topic:\*\*** Terraform — starting from Monday to maintain curriculum flow. 

\- **\*\*This week:\*\*** Python (a brief module) + resume building exercise. 

\- Students instructed to **\*\*add HPA, Grafana, deployment models, and project deployments to their resumes\*\***. 

\- Kubernetes interview questions document shared in the WhatsApp group for self-study; Vikas noted he would not provide all answers directly — students should research and prepare. 

\- Practical exercises completed this session: HPA setup, rolling update demo, canary simulation, load testing — all to be reflected in resume as hands-on experience. 

---

## 20 Kubernetes Interview Questions & Answers

### 1. What is a Rolling Update in Kubernetes?
**Answer:** Rolling Update gradually replaces old pods with new pods without stopping the application completely. It is the default Deployment strategy and helps achieve near zero-downtime releases.

### 2. What is the difference between Rolling Update and Recreate?
**Answer:** Rolling Update replaces pods gradually, while Recreate terminates all old pods first and then creates new pods. Recreate causes downtime and is generally avoided for production applications.

### 3. What is a Canary Deployment?
**Answer:** Canary deployment releases a new version to a small percentage of users first. If the new version works correctly, traffic can be increased gradually until the new version becomes fully live.

### 4. What is Blue-Green Deployment?
**Answer:** Blue-Green maintains two environments. Blue is the current production version and Green contains the new version. After testing Green, traffic is switched to Green using a load balancer or routing layer.

### 5. What is the main disadvantage of Blue-Green deployment?
**Answer:** It can be expensive because two environments may need to run at the same time. A common cost-saving approach is to create or scale up the Green environment only during release preparation.

### 6. What is HPA in Kubernetes?
**Answer:** HPA stands for Horizontal Pod Autoscaler. It automatically increases or decreases the number of pod replicas based on metrics such as CPU, memory, or custom metrics.

### 7. What are minReplicas and maxReplicas in HPA?
**Answer:** `minReplicas` defines the minimum number of pods that should run, while `maxReplicas` defines the maximum number of pods HPA can create.

### 8. Why does HPA need Metrics Server?
**Answer:** HPA needs resource utilization data to make scaling decisions. Metrics Server provides CPU and memory metrics that HPA can consume.

### 9. Can HPA scale a standalone Pod?
**Answer:** HPA is normally used with scalable workload controllers such as Deployments. A Deployment manages ReplicaSets and provides the replica management required for reliable horizontal scaling.

### 10. What is the difference between HPA and Cluster Autoscaler?
**Answer:** HPA scales pods. Cluster Autoscaler scales cluster nodes. For example, HPA may increase pods from 2 to 8, and if the existing nodes do not have enough capacity, Cluster Autoscaler can add nodes.

### 11. What is a stateless application?
**Answer:** A stateless application does not depend on local pod storage for persistent application state. If a pod is removed, another pod can normally serve the request without losing application state.

### 12. What is a Stateful application?
**Answer:** A stateful application needs persistent identity or data, such as a database. Kubernetes StatefulSets and persistent volumes are commonly used for these workloads.

### 13. How do you update the image of a Deployment?
**Answer:** You can update the image in the Deployment YAML and run `kubectl apply -f deployment.yaml`. Kubernetes then starts the configured rollout strategy.

### 14. How do you check the status of an HPA?
**Answer:** Use `kubectl get hpa`. For continuous monitoring, `kubectl get hpa -w` can be used.

### 15. What does `kubectl drain` do?
**Answer:** `kubectl drain <node>` safely evicts pods from a node so that workloads can be rescheduled onto other available nodes. It is commonly used before node maintenance.

### 16. What is Calico?
**Answer:** Calico is a Kubernetes CNI solution used for pod networking and network security policies. It is commonly seen in self-managed Kubernetes clusters.

### 17. What is the difference between EKS and kubeadm?
**Answer:** EKS is a managed Kubernetes service on AWS, while kubeadm is a tool used to bootstrap a self-managed Kubernetes cluster on your own infrastructure.

### 18. How can Kubernetes infrastructure cost be reduced?
**Answer:** Use right-sized resources, HPA, Cluster Autoscaler, namespaces where appropriate, Spot/Preemptible capacity for suitable workloads, and Reserved Instances or Savings Plans for predictable workloads.

### 19. What are maxSurge and maxUnavailable?
**Answer:** `maxSurge` controls how many extra pods can be created above the desired replica count during a rollout. `maxUnavailable` controls how many pods can be unavailable during the rollout.

### 20. How would you answer "Which deployment strategy do you use?"
**Answer:** A practical answer is: "We generally use Rolling Updates as the default. For higher-risk releases, we can combine rolling deployment with Canary or Blue-Green techniques depending on traffic, business requirements, and rollback needs."

---

## 20 Kubernetes Scenario-Based Questions & Answers

### 1. Scenario: CPU suddenly reaches 90% and users report slow responses. What will you do?
**Answer:** Check pod CPU usage and HPA status first. Verify whether HPA is scaling pods, check application logs and traffic, and confirm that resource requests/limits and HPA thresholds are configured correctly. If required, increase capacity and investigate the application bottleneck.

### 2. Scenario: HPA shows `<unknown>` CPU utilization. What will you check?
**Answer:** Check whether Metrics Server is running and healthy. Verify that resource requests are configured for the containers and inspect HPA events using `kubectl describe hpa`. Also verify that the metrics API is available.

### 3. Scenario: HPA is created but pods are not scaling.
**Answer:** Check `kubectl get hpa`, `kubectl describe hpa`, Metrics Server health, CPU/memory requests, current utilization, and the configured min/max replicas. Also confirm that the target workload is a supported scalable controller.

### 4. Scenario: A new application version is deployed and users see errors. How do you rollback?
**Answer:** First confirm the failed rollout using Deployment status and application logs. Then use Kubernetes rollout history and rollback commands, such as `kubectl rollout undo deployment/<deployment-name>`, to return to a known-good version.

### 5. Scenario: One Kubernetes node is stuck and pods are not running correctly.
**Answer:** Check node status, events, CPU/memory pressure, kubelet status, and infrastructure logs. If the node is responsive, cordon and drain it before maintenance. If it is completely unresponsive, recover or restart the underlying VM and verify that workloads are running on healthy nodes.

### 6. Scenario: You need to deploy a risky release to only 10% of users.
**Answer:** Use a Canary strategy. Keep the stable version serving most traffic and route a small percentage to the new version. Monitor errors, latency, CPU, and business metrics before increasing the percentage.

### 7. Scenario: Your company wants zero-downtime deployment but cannot afford two complete environments.
**Answer:** Rolling Update is usually a better fit because it does not require two full environments. Configure readiness probes and rollout parameters carefully and monitor the deployment during the release.

### 8. Scenario: Traffic increases from 1,000 to 100,000 requests and existing nodes have no capacity.
**Answer:** HPA can increase the number of pods, but if nodes do not have enough capacity, Cluster Autoscaler should add nodes. This is a good example of HPA and Cluster Autoscaler working together.

### 9. Scenario: A node needs maintenance but is hosting production pods.
**Answer:** Cordon the node to prevent new scheduling, then drain it so pods are gracefully evicted and rescheduled on other nodes. Perform maintenance and verify cluster health before returning the node to service.

### 10. Scenario: After stopping a load test, HPA does not immediately reduce replicas.
**Answer:** Check the current metrics and HPA behavior. Scale-down is intentionally less aggressive than scale-up in many situations to avoid constant scaling up and down. Wait for the configured stabilization/scale-down behavior and monitor with `kubectl get hpa -w`.

### 11. Scenario: You are running dev, QA, and staging workloads and the cloud bill is too high.
**Answer:** Evaluate whether separate clusters are actually required. Where isolation and security requirements allow, namespaces can separate environments inside a shared cluster. Also review right-sizing, autoscaling, and idle workloads.

### 12. Scenario: A production workload is running on expensive instances but traffic is predictable.
**Answer:** First right-size the workload and then evaluate Reserved Instances or Savings Plans for the predictable baseline. Autoscaling can handle variable demand.

### 13. Scenario: A non-critical batch workload is very expensive to run.
**Answer:** Consider Spot or Preemptible instances if the workload can tolerate interruption. Make the workload retryable and avoid using this approach for workloads where interruption would cause unacceptable business impact.

### 14. Scenario: A deployment is stuck during rollout.
**Answer:** Check `kubectl rollout status`, Deployment events, ReplicaSet status, pod states, image pull errors, readiness probes, resource constraints, and application logs. Fix the underlying issue before continuing or rollback if the release is unsafe.

### 15. Scenario: New pods are running but traffic is still going to the old version.
**Answer:** Check the Service selector and pod labels. Verify that the new pods match the intended Service selector and inspect endpoints. In a controlled rollout, also verify the load balancer or ingress routing configuration.

### 16. Scenario: The new version starts successfully but becomes unhealthy after receiving traffic.
**Answer:** Check readiness and liveness probes, application logs, dependency connectivity, resource usage, and actual traffic behavior. If production impact is occurring, stop or rollback the rollout and investigate the new version.

### 17. Scenario: A node failure happens in a multi-zone cluster.
**Answer:** Verify that healthy nodes and pods are available in other zones. Kubernetes can reschedule eligible workloads onto healthy nodes. Multi-zone design improves availability, but stateful workloads require additional care around persistent storage and recovery.

### 18. Scenario: A Stateful application is on a failed node. Can you treat it exactly like a stateless application?
**Answer:** No. Stateful workloads depend on persistent data and identity. Before moving or recovering them, verify persistent volume attachment, storage behavior, replication, and application consistency.

### 19. Scenario: Your team wants to test a new feature with selected users only.
**Answer:** A/B testing or controlled traffic routing can be used when the goal is to compare different versions for different user groups. The routing decision should be based on the application and business requirements.

### 20. Scenario: An interviewer asks, "Tell me about a Kubernetes project you worked on."
**Answer:** Explain the project using a practical flow: application deployment → Service/load balancing → HPA → Metrics Server → load testing → monitoring → rolling update → troubleshooting → cost optimization. Mention the actual commands and problems you solved, rather than only listing Kubernetes concepts.

---

## Interview Quick Revision

For interviews, remember these practical flows:

**Zero downtime:** Rolling Update → readiness → monitoring → rollback if required.

**High traffic:** HPA scales pods → Cluster Autoscaler adds nodes if capacity is insufficient.

**Node issue:** Check logs/events → cordon → drain → recover/repair → verify workloads.

**Cost optimization:** Right-size → HPA → Cluster Autoscaler → Spot for suitable workloads → Savings Plans/Reserved capacity for predictable workloads.

**Risky release:** Canary → small traffic → monitor → increase gradually → rollback if required.
