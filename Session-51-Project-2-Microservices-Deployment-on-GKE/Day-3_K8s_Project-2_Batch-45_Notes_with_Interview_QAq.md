# Day 3 of K8s — Project 2: Microservices on GKE (Batch 45)
**Date:** 24th Nov 2026, 8 AM IST  |  **Trainer:** Vikas Ratnawat (CloudDevOpsHub)

---

## Key Outcomes

The session focused on deploying a Google Cloud-based microservices demo application onto a GKE (Google Kubernetes Engine) cluster using a single Kubernetes manifest file (~980 lines). Students learned the architectural differences between monolithic and microservices systems, how services communicate via labels and selectors, and how to perform single-command deployments. A live demonstration showed deploying all microservices, deleting a payment service pod/deployment, and observing fault isolation and self-healing behavior via Kubernetes deployments.

---

## Decisions Made

- **Mono-repo approach adopted** for the project: all microservices code lives in a single repository under the `src/` folder, mirroring Google's own practice adopted ~3–4 years ago.
- **Single consolidated manifest file** (`kubernetes-manifests.yaml`, ~980 lines) used for one-click deployment of all microservices rather than separate per-service files.
- **`kubectl apply`** is the preferred command over `kubectl create` for re-deployments; `create` is used only on first-time setup.
- **Front-end exposed via Load Balancer**; all other internal services communicate via **ClusterIP** only.
- **GKE cluster created** with 3 nodes, each with 8 GB RAM (total 24 GB), using standard machine type.
- **Replicas should be increased** (minimum 3–5 for front-end) to achieve zero-downtime resilience; auto-scaling to be added in the next session.

---

## Architecture: Monolithic vs. Microservices

- **Monolithic**: Single environment serving front-end, middleware, and database together; if one component fails, the entire application goes down.
- **Microservices**: Application broken into smallest independent units, each delivering one specific business goal; if one part fails, only that part is affected.
- **Key benefits demonstrated:**
    - Fault isolation — only the affected service goes down
    - Independent scaling — high-traffic services (e.g., front-end, pizza analogy) scale independently
    - Resource optimization — allocate more hardware only to frequently used or critical services
    - Loose coupling — services are independently deployable and manageable
    - Faster startup — independent services start quickly without waiting on others
- **Real-world analogy**: A restaurant with one chef (monolithic) vs. separate food stalls (microservices) — if the sushi chef is sick, burger and pizza stalls keep running.
- **Paytm example**: Credit card sometimes down, debit card works — classic microservices fault isolation in production.

---

## Project Explanation (Detailed — Use This to Explain in Class / Interviews)

### 1. What is this project?
**Online Boutique** — Google Cloud's open-source e-commerce microservices demo.
Repo: `https://github.com/GoogleCloudPlatform/microservices-demo`

It is a web shop where users browse products, add them to a cart, and check out. The interesting part for DevOps is **not the shop** — it's that the app is split into **11 independent microservices written in 5 different languages**, talking to each other over **gRPC**, and deployed together on **Kubernetes with one manifest file**.

### 2. Services, Languages & Ports (Verified against the official repo)

| # | Service | Language | Port | What it does |
|---|---------|----------|------|--------------|
| 1 | `frontend` | Go | 8080 | HTTP web UI; the only service exposed to internet (LoadBalancer) |
| 2 | `cartservice` | C# (.NET) | 7070 | Stores/retrieves cart items in **Redis** |
| 3 | `productcatalogservice` | Go | 3550 | Product list from a JSON file, search, product details |
| 4 | `currencyservice` | Node.js | 7000 | Currency conversion (highest-QPS service in the app) |
| 5 | `paymentservice` | Node.js | 50051 | Charges the (mock) credit card, returns transaction ID |
| 6 | `shippingservice` | Go | 50051 | Shipping cost quote + mock shipping |
| 7 | `emailservice` | Python | 8080 | Sends (mock) order confirmation email |
| 8 | `checkoutservice` | Go | 5050 | **Orchestrator** — calls cart, payment, shipping, email, currency |
| 9 | `recommendationservice` | Python | 8080 | Recommends products based on cart |
| 10 | `adservice` | **Java** | 9555 | Text ads based on context words |
| 11 | `loadgenerator` | Python (Locust) | — | Simulates real user traffic continuously |
| + | `redis-cart` | Redis | 6379 | Cache/data store for the cart |

> 📝 **Correction note:** In class we mentioned Ad service as Python — in the official repo it is **Java**. Also there is no separate "Product Detail service" — product detail pages are served by **productcatalogservice**. Keep the table above for interviews.

### 3. Request Flow (Explain like a story)

```
User (Internet)
   │
   ▼
[LoadBalancer: frontend-external]  ← only public entry point
   │
   ▼
frontend (Go) ──gRPC──► productcatalogservice   (browse/search/product page)
   │          ──gRPC──► currencyservice         (prices in INR/USD/EUR)
   │          ──gRPC──► cartservice ──► redis-cart   (add to cart)
   │          ──gRPC──► recommendationservice   (“you may also like”)
   │          ──gRPC──► adservice               (ads)
   │          ──gRPC──► shippingservice         (shipping quote)
   │
   └──gRPC──► checkoutservice (Place Order)
                 ├──► cartservice      (get + empty cart)
                 ├──► productcatalog   (get prices)
                 ├──► currencyservice  (convert)
                 ├──► shippingservice  (ship order)
                 ├──► paymentservice   (charge card)
                 └──► emailservice     (confirmation mail)
```

### 4. How do services actually find each other? (Important — 2 layers)

1. **Service → Pod (labels & selectors):** Each Deployment's pods carry a label like `app: paymentservice`. The Service object has `selector: app: paymentservice`. Kubernetes keeps an **Endpoints/EndpointSlice** list of matching *ready* pod IPs, and kube-proxy routes the Service's ClusterIP to those pods.
2. **Service → Service (DNS):** The calling app doesn't know pod IPs. It's configured with an env var, e.g. in checkoutservice:
   ```yaml
   env:
   - name: PAYMENT_SERVICE_ADDR
     value: "paymentservice:50051"
   ```
   CoreDNS resolves `paymentservice` → `paymentservice.default.svc.cluster.local` → ClusterIP.

**Best interview line:** *"Services discover each other through Kubernetes DNS using Service names passed as environment variables, and each Service routes to its pods using labels and selectors. The actual calls are gRPC."*

### 5. Anatomy of one microservice in the manifest (~70 lines each)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: paymentservice
  labels:
    app: paymentservice
spec:
  selector:
    matchLabels:
      app: paymentservice        # Deployment manages pods with this label
  template:
    metadata:
      labels:
        app: paymentservice      # Pod label (must match selector)
    spec:
      serviceAccountName: paymentservice
      containers:
      - name: server
        image: us-central1-docker.pkg.dev/google-samples/microservices-demo/paymentservice:<version>
        ports:
        - containerPort: 50051
        readinessProbe:
          grpc:
            port: 50051
        livenessProbe:
          grpc:
            port: 50051
        resources:
          requests: { cpu: 100m, memory: 64Mi }
          limits:   { cpu: 200m, memory: 128Mi }
---
apiVersion: v1
kind: Service
metadata:
  name: paymentservice
spec:
  type: ClusterIP
  selector:
    app: paymentservice          # routes to pods with this label
  ports:
  - name: grpc
    port: 50051
    targetPort: 50051
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: paymentservice
```

Three objects per service:
- **Deployment** → how many pods, which image, probes, resources
- **Service** → stable name + IP + load balancing to pods
- **ServiceAccount** → identity of the pod (used later for Workload Identity / RBAC)

### 6. End-to-end hands-on steps

```bash
# 1. Create GKE cluster (3 nodes, e2-standard-2 = 2 vCPU / 8 GB each)
gcloud container clusters create online-boutique \
  --zone us-central1-a --num-nodes 3 --machine-type e2-standard-2

# 2. Connect kubectl to cluster
gcloud container clusters get-credentials online-boutique --zone us-central1-a

# 3. Clone repo
git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
cd microservices-demo/release/

# 4. Deploy everything
kubectl apply -f kubernetes-manifests.yaml

# 5. Verify
kubectl get pods -o wide
kubectl get deploy
kubectl get svc
kubectl get all

# 6. Get public IP (wait for EXTERNAL-IP)
kubectl get svc frontend-external -w

# 7. Scale critical service
kubectl scale deployment frontend --replicas=3

# 8. Cleanup (avoid billing!)
kubectl delete -f kubernetes-manifests.yaml
gcloud container clusters delete online-boutique --zone us-central1-a
```

### 7. Trainer tips — better approaches used in real companies
- **Don't keep one 980-line file in production.** Use **Helm** (repo has `helm-chart/`) or **Kustomize** (repo has `kustomize/`) for per-environment configs (dev/stage/prod).
- **GitOps with Argo CD / Flux** instead of manual `kubectl apply` — Git becomes the single source of truth, drift is auto-corrected.
- **HPA** for frontend & currencyservice; **PodDisruptionBudget** for zero-downtime during node upgrades.
- **Managed Redis (Memorystore / ElastiCache)** instead of in-cluster Redis for production.
- **Observability:** Prometheus + Grafana, or Cloud Operations; tracing with OpenTelemetry (the demo supports it).
- **Service Mesh (Istio / Cloud Service Mesh)** for mTLS, retries, canary traffic splitting.
- **GKE Autopilot** if you don't want to manage nodes.

---

## Project Application: E-Commerce Microservices Demo (Session Notes)

**Source**: Official Google Cloud microservices demo (open-source GitHub repo).

### User Flow (Flipkart/E-Commerce Example)

A full purchase journey maps directly to discrete microservices:

- Internet → **Front-end** (DNS/IP, load balancer exposed)
- Product search & product page → **Product Catalog service**
- Add to cart → **Cart service** (data saved in Redis)
- Checkout → **Checkout service** (orchestrates the order)
- Payment → **Payment service** (Razorpay-style independent integration)
- Confirmation → **Email service**
- Post-purchase → **Recommendation service** (suggests related items, e.g., iPhone → charger)
- Ad targeting → **Ad service**

---

## Kubernetes Manifest File and Deployment

### Structure of the Manifest

- The single manifest contains all resources for all microservices in sequence.
- Per microservice, approximately **70 lines** covering **Deployment**, **Service**, and **ServiceAccount**.
- `kubectl apply` submits resources in file order; Kubernetes controllers then reconcile them independently.

### Deployment Commands

```bash
git clone https://github.com/GoogleCloudPlatform/microservices-demo.git
cd microservices-demo/release/

# First-time deployment
kubectl create -f kubernetes-manifests.yaml

# Re-deployment / updates (preferred, idempotent)
kubectl apply -f kubernetes-manifests.yaml
```

- `kubectl apply` is safe to re-run: unchanged resources show `unchanged`, changed ones show `configured`, missing ones show `created`.

---

## Live Demonstration: Key Observations

### Successful Deployment
- Single command deployed all Deployments, Services, and ServiceAccounts.
- Verified via `kubectl get pods`, `kubectl get deploy`, `kubectl get svc`, `kubectl get all`.
- Full purchase flow (browse → cart → payment → order confirmation) worked end-to-end via frontend LoadBalancer IP.

### Pod Deletion (Payment Service)
- Deleted the payment pod → payment page broke for ~30 seconds.
- Pod **automatically recreated** by the Deployment's ReplicaSet → self-healing.
- **Root cause of downtime**: only 1 replica. **Fix**: 2–3+ replicas.

### Deployment Deletion (Payment Service)
- Deleting the **Deployment** removes its ReplicaSet and pods — no auto-recreation.
- **Service object is NOT deleted** — it's independent (but has zero endpoints now).
- Frontend, catalog, cart still worked; only checkout/payment failed → fault isolation.
- Restore: `kubectl apply -f kubernetes-manifests.yaml` → only payment Deployment gets `created`.

### Why Services Rarely Go Down
- A Service is a virtual entity (iptables/IPVS rules + DNS record), no CPU/memory workload.
- With multiple pods behind it, one pod crash doesn't matter — traffic goes to remaining **ready** pods.
- ⚠️ But if **zero pods are ready**, the Service exists yet requests fail (connection refused / no endpoints).

---

## DevOps Engineer's Role in This Project

- Set up and manage the **Git repository** for the dev team
- Create and manage the **GKE cluster** (ideally via Terraform)
- Write and maintain **Kubernetes manifests** with developers
- **Containerize** apps using Dockerfiles; push images to **Artifact Registry**
- Manage Deployments, Services, ServiceAccounts
- **Troubleshoot**: logs, events, identify failing microservice, raise tickets or fix infra
- **On-call rotation** for production incidents
- Document runbooks for L1 support team

**Resume framing:** Internal e-commerce / corporate gifting portal at your company, 7–10 microservices (user mgmt, product catalog, cart, order mgmt, payment, recommendation, notification), running on Kubernetes, managed as DevOps engineer.

**Sample resume bullets:**
- Deployed and managed 10+ polyglot microservices (Go, Java, Python, Node.js, C#) on GKE using declarative Kubernetes manifests with gRPC-based inter-service communication.
- Improved availability by configuring multi-replica Deployments, readiness/liveness probes and resource requests/limits, reducing single-pod-failure downtime to near zero.
- Automated application deployment via Jenkins pipeline (`kubectl apply`) with full audit trail; provisioned GKE infrastructure using Terraform.
- Troubleshot production issues (CrashLoopBackOff, ImagePullBackOff, Pending pods, service endpoint failures) using kubectl logs/describe/events and Cloud Logging.

---

## CI/CD Integration

- All manual `kubectl` commands can go into a **Jenkins pipeline** stage.
- Jenkins gives an **audit trail**: who, when, why, what changed.
- **Terraform** provisions the GKE cluster; **Jenkins** deploys the application.
- Project 3 (monolithic CI/CD) will cover this in detail.

```groovy
pipeline {
  agent any
  stages {
    stage('Checkout') { steps { git 'https://github.com/<your-org>/microservices-demo.git' } }
    stage('Auth GKE') {
      steps {
        sh 'gcloud container clusters get-credentials online-boutique --zone us-central1-a'
      }
    }
    stage('Deploy') { steps { sh 'kubectl apply -f release/kubernetes-manifests.yaml' } }
    stage('Verify') { steps { sh 'kubectl rollout status deploy/frontend --timeout=120s' } }
  }
}
```

---

## Key Concepts Clarified via Q&A

| Topic | Clarification |
|-------|---------------|
| **`create` vs `apply`** | `create` = first time only (fails if exists); `apply` = idempotent, safe for re-runs and updates |
| **Service vs Deployment** | Deployment manages pods/replicas; Service handles networking/exposure and load balancing |
| **Service downtime** | Services rarely go down — virtual entity; pod crashes don't take down the service |
| **Deleting deployment** | Pods deleted; Service remains; re-apply manifest to restore |
| **New microservice addition** | Add to existing manifest and re-apply, or create a standalone YAML and apply |
| **Multi-developer changes** | Full manifest re-apply is safe; Kubernetes only patches what changed |
| **Database in Kubernetes** | Best practice: managed DB (Cloud SQL, RDS) rather than DB as a pod |
| **Active-passive deployments** | Rollback strategy — tag image versions, `kubectl rollout undo` on failure |
| **Port-based vs path-based routing** | Path-based routes by URL path (Ingress), port-based by port number |
| **gRPC** | High-performance RPC framework (HTTP/2 + Protocol Buffers) for inter-service calls; originally from Google |

---

## Pending Confirmation

- **Auto-scaling (HPA)** for individual microservices — **next session**.
- **CI/CD pipeline** with Jenkins — separate dedicated project session.
- **Artifact Registry** usage — upcoming session.
- **AWS EKS equivalent** — bonus session in syllabus.
- **Mock interviews** — next weekend; Nikhil to send reminder.

---

## Action Items

- **All students**: Practice full flow — clone, `cd release/`, `kubectl apply -f kubernetes-manifests.yaml`, verify pods & services.
- **All students**: Add project to resume with company framing; prepare talking points.
- **All students**: Increase replicas for frontend & observe zero-downtime behavior.
- **Vikas**: Share practical example of label/selector integration visually.
- **Vikas**: Add special notes doc for this project in course notes / GitHub challenge repo.
- **Nikhil**: Post mock interview scheduling message for next weekend.
- **Students with Jenkins**: Add `kubectl apply` to an existing Jenkins job's deploy step.
- **All students**: Delete GKE cluster after practice to avoid billing.

---

# 🎯 20 Interview Questions & Answers

**Q1. Explain your microservices project in 2 minutes.**
**A:** It's an e-commerce application with ~11 microservices — frontend, product catalog, cart, checkout, payment, shipping, currency, email, recommendation, ads — written in Go, Java, Python, Node.js and C#. All services run on a 3-node GKE cluster. Services talk over gRPC; only the frontend is exposed through a LoadBalancer, everything else is ClusterIP. Cart data is in Redis. I managed the cluster, manifests (Deployment, Service, ServiceAccount per service), containerization, deployments through Jenkins, scaling, and troubleshooting in production.

**Q2. Monolithic vs microservices — when would you NOT choose microservices?**
**A:** Monolith = one deployable unit; one failure or one bad release can take down everything. Microservices give fault isolation, independent scaling and deployments. But for a small team, early-stage product or simple app, microservices add network latency, distributed debugging, more infra cost and operational overhead. Start with a well-structured monolith and split when scale or team size demands it.

**Q3. How do microservices communicate with each other in Kubernetes?**
**A:** Two layers. Service discovery: the caller uses a Service DNS name (e.g. `paymentservice:50051`) passed as an env var; CoreDNS resolves it to a ClusterIP. Routing: the Service uses a label selector (`app: paymentservice`) to find ready pods and kube-proxy load-balances across them. The protocol in this project is gRPC.

**Q4. What are labels and selectors? What happens if they don't match?**
**A:** Labels are key-value tags on objects; selectors query them. A Deployment's `spec.selector.matchLabels` must match the pod template labels (else the API rejects it). A Service's `selector` must match pod labels — if it doesn't, the Service has **no endpoints** and requests fail even though pods are running. Check with `kubectl get endpoints <svc>`.

**Q5. Difference between `kubectl create` and `kubectl apply`?**
**A:** `create` is imperative — creates resources and errors with "AlreadyExists" if present. `apply` is declarative — creates if missing, patches if changed, leaves unchanged otherwise. It stores last-applied config to compute diffs. In CI/CD we always use `apply` (or Helm/Argo CD).

**Q6. What is the difference between Deployment, ReplicaSet, and Pod?**
**A:** Pod = smallest unit running containers. ReplicaSet = ensures N pod replicas exist. Deployment = manages ReplicaSets and adds rolling updates, rollback and revision history. We create Deployments; Kubernetes creates the ReplicaSet and pods.

**Q7. Explain Service types — ClusterIP, NodePort, LoadBalancer. Why is only frontend a LoadBalancer?**
**A:** ClusterIP = internal-only virtual IP (default). NodePort = opens a port (30000–32767) on every node. LoadBalancer = provisions a cloud load balancer with a public IP. Only frontend needs internet exposure; exposing payment or cart publicly would be a security risk and extra cost. Internal services stay ClusterIP.

**Q8. What is a ServiceAccount and why does each microservice have its own?**
**A:** A ServiceAccount is the identity of a pod inside the cluster. Separate SAs follow least privilege — each service can be given only the RBAC permissions or cloud IAM roles it needs (via GKE Workload Identity / EKS IRSA). If one service is compromised, the blast radius is limited.

**Q9. What is gRPC and why is it used here instead of REST?**
**A:** gRPC is an RPC framework using HTTP/2 and Protocol Buffers. Benefits: binary payloads (smaller, faster), strongly typed contracts via `.proto` files, multiplexing, streaming, and code generation across languages — perfect for polyglot microservices. REST/JSON is still preferred for public browser-facing APIs.

**Q10. How does Kubernetes self-healing work?**
**A:** Controllers run a reconciliation loop: desired state (e.g. replicas: 3) vs actual state. If a pod dies, the ReplicaSet controller creates a new one. Kubelet restarts failed containers per restartPolicy and liveness probes. If a node dies, pods are rescheduled to healthy nodes.

**Q11. Liveness vs readiness vs startup probes?**
**A:** Readiness — is the pod ready to receive traffic? If it fails, the pod is removed from Service endpoints (not restarted). Liveness — is the container alive? If it fails, kubelet restarts it. Startup — gives slow-starting apps time before liveness kicks in. This project uses gRPC probes for backend services.

**Q12. What are resource requests and limits? What happens if you don't set them?**
**A:** Requests = guaranteed amount used by the scheduler for placement. Limits = maximum allowed; exceeding memory limit → OOMKilled, exceeding CPU → throttled. Without them, pods can starve neighbours, scheduling becomes unpredictable, and HPA (CPU-based) can't work properly.

**Q13. How do you achieve zero-downtime deployments?**
**A:** Multiple replicas, RollingUpdate strategy (`maxUnavailable: 0`, `maxSurge: 1`), proper readiness probes, PodDisruptionBudget, graceful shutdown (`preStopHook` / `terminationGracePeriodSeconds`), and anti-affinity/topology spread so replicas sit on different nodes.

**Q14. How do you roll back a bad deployment?**
**A:** `kubectl rollout undo deployment/<name>` (or `--to-revision=N`), check with `kubectl rollout history`. Better practice: immutable image tags (git SHA, not `latest`), and with GitOps you revert the Git commit and Argo CD syncs back. Helm: `helm rollback <release> <rev>`.

**Q15. Why should databases run as managed services instead of pods?**
**A:** Managed DBs (Cloud SQL, RDS) give automated backups, point-in-time recovery, HA/failover, patching and scaling. Running DBs in pods needs StatefulSets, PVs, backup tooling and deep expertise. It's possible (with operators) but most companies prefer managed for production.

**Q16. What is a mono-repo vs multi-repo? Pros/cons?**
**A:** Mono-repo = all services in one repo (like this project's `src/`). Pros: easy code sharing, atomic cross-service changes, single place for tooling. Cons: large repo, need path-based CI triggers so one change doesn't build everything. Multi-repo = one repo per service: clear ownership, independent pipelines, but harder to coordinate shared changes.

**Q17. How would you deploy this in a real company instead of one big YAML?**
**A:** Package with Helm or Kustomize with overlays per environment (dev/stage/prod), store in Git, deploy via GitOps (Argo CD). CI (Jenkins/GitHub Actions) builds images, scans them (Trivy), pushes to Artifact Registry, and updates image tag in Git. Infra (GKE, VPC, IAM) via Terraform.

**Q18. What is HPA and which services would you autoscale in this project?**
**A:** HorizontalPodAutoscaler scales replicas based on CPU/memory or custom metrics. Needs metrics-server and resource requests. I'd autoscale frontend (user-facing), currencyservice (highest QPS), and productcatalog/recommendation. Cluster Autoscaler adds nodes when pods are Pending.

**Q19. What is the role of Redis in this project?**
**A:** Redis is an in-memory key-value store used by cartservice to store cart items — fast reads/writes. In the demo it's a pod (`redis-cart`) with no persistence guarantee; in production I'd use Memorystore/ElastiCache with replication so cart data survives pod restarts.

**Q20. How would you migrate this project from GKE to EKS or AKS?**
**A:** Manifests are mostly portable. Changes: provision the cluster with Terraform (EKS/AKS module), push images to ECR/ACR, update image references, replace GKE-specific bits (Workload Identity → IRSA/Pod Identity on EKS, Azure Workload Identity on AKS), LoadBalancer annotations, storage classes and Ingress controller (AWS LB Controller / AGIC). Then test, cut over DNS gradually.

---

# 🛠️ 20 Scenario-Based Questions & Answers

**S1. You run `kubectl apply` and frontend's EXTERNAL-IP stays `<pending>` for 10+ minutes. What do you check?**
**A:** `kubectl describe svc frontend-external` for events. Check: cloud LB quota (forwarding rules / external IPs) in the project, that the Compute Engine API is enabled, the node service account's permissions, and org policies blocking external LBs. If on a local cluster (Minikube/kind) there's no cloud LB — use `minikube tunnel` or NodePort.

**S2. Payment pod is in `CrashLoopBackOff`. How do you troubleshoot?**
**A:** `kubectl describe pod <pod>` (events, exit code, last state), `kubectl logs <pod> --previous` (logs of the crashed container). Common causes: missing env var/config, app error, liveness probe failing too early, OOMKilled (exit code 137 → raise memory limit). Fix the root cause, re-apply, watch `kubectl get pods -w`.

**S3. A pod shows `ImagePullBackOff`. What's wrong?**
**A:** Wrong image name/tag, image doesn't exist in registry, or no pull permission. `kubectl describe pod` shows exact error. For private Artifact Registry, grant the node SA `Artifact Registry Reader`, or use `imagePullSecrets`. Also check rate limits on Docker Hub.

**S4. Pods are stuck in `Pending`. Why?**
**A:** `kubectl describe pod` → "Insufficient cpu/memory" (cluster full → add nodes / enable cluster autoscaler / reduce requests), unbound PVC, node taints without tolerations, or nodeSelector/affinity that no node satisfies. `kubectl top nodes` to see utilisation.

**S5. All pods are Running but the site says "connection refused" when adding to cart. How do you find the issue?**
**A:** Frontend logs → see which call fails (cartservice). `kubectl get endpoints cartservice` — if empty, selector/label mismatch or pods not ready. Check cartservice logs → likely can't reach `redis-cart`. Verify redis pod, its Service, and `REDIS_ADDR` env var. Test with `kubectl exec` + `nc -zv redis-cart 6379`.

**S6. During a sale, frontend latency jumps and pods hit CPU limits. What do you do now and long term?**
**A:** Immediate: `kubectl scale deploy frontend --replicas=6`. Long term: HPA on CPU (e.g. target 60%), Cluster Autoscaler for nodes, review limits (CPU throttling), add CDN for static assets, load test with the loadgenerator/Locust before sale events.

**S7. A developer deletes the payment Deployment by mistake in production. How do you recover and prevent it?**
**A:** Recover: `kubectl apply -f kubernetes-manifests.yaml` (or Argo CD auto-sync restores it within minutes). Prevent: RBAC — developers get read-only in prod, GitOps as the only deployment path, audit logs, namespace separation, admission policies, and change approval in CI/CD.

**S8. A new release of checkoutservice broke orders. Customers are impacted. What's your action plan?**
**A:** First restore service: `kubectl rollout undo deploy/checkoutservice`, verify with `rollout status` and a test order. Then communicate status to stakeholders, collect logs/metrics for RCA, raise a ticket to dev with evidence. Long term: canary/blue-green deployments, automated smoke tests in pipeline, alerting on order error rate.

**S9. A node goes down. What happens to the app?**
**A:** Node becomes NotReady; after the eviction timeout (~5 min by default) pods are rescheduled to other nodes. Services with 1 replica on that node are down meanwhile. Fix: ≥2 replicas, topology spread / pod anti-affinity across nodes and zones, PDBs, regional GKE cluster for zone failure.

**S10. You need to add a new "loyalty-points" microservice. How do you do it?**
**A:** Dev writes code + Dockerfile; CI builds, scans and pushes image. I create Deployment + Service (ClusterIP) + ServiceAccount, either in the main manifest or a new YAML / Helm chart. Calling service (checkout) gets env var `LOYALTY_SERVICE_ADDR=loyaltyservice:<port>`. `kubectl apply` → only new resources `created`. Add probes, resources, HPA, monitoring.

**S11. Two developers changed different services in the same manifest. Will re-applying break running services?**
**A:** No. `apply` computes a diff per object; unchanged objects remain `unchanged`, only changed Deployments roll out. Risk is merge conflicts in Git — handle via PR reviews. Better: split manifests per service or use Helm/Kustomize.

**S12. Pods get `OOMKilled` repeatedly for recommendationservice.**
**A:** `kubectl describe pod` shows `Reason: OOMKilled`, exit code 137. Check usage with `kubectl top pod` / Grafana. Either memory limit too low → increase it, or memory leak → raise ticket with dev, add VPA in recommendation mode to find right sizing.

**S13. Cart items disappear when the redis-cart pod restarts. Why and fix?**
**A:** Redis in the demo uses `emptyDir` — data lives only for the pod's lifetime. Fix: use managed Redis (Memorystore/ElastiCache) with HA, or a StatefulSet with PersistentVolumeClaim and AOF persistence. Point `REDIS_ADDR` to the new endpoint.

**S14. Security team says internal services must not talk to each other freely. How do you restrict?**
**A:** Kubernetes **NetworkPolicies** (default deny, then allow only required paths, e.g. only checkoutservice → paymentservice on 50051). GKE needs Dataplane V2 / network policy enabled. For encryption + identity, add a service mesh (Istio) with mTLS and AuthorizationPolicies.

**S15. You get alerted that `kubectl get svc paymentservice` exists but payments fail. Pods were deleted. Explain.**
**A:** Service is independent of Deployment. If Deployment/pods are gone, the Service exists with **zero endpoints**, so calls fail. Verify with `kubectl get endpoints paymentservice`. Restore the Deployment via `kubectl apply`.

**S16. Your GCP bill is very high after practice sessions. What happened and how do you optimize?**
**A:** GKE nodes + LoadBalancer + external IP kept running. Delete the cluster when not in use, use Spot VMs for dev node pools, right-size requests, use Autopilot (pay per pod), set budget alerts in billing. Always run `gcloud container clusters delete` after labs.

**S17. Interviewer: "Your frontend has 3 replicas but users still see errors during deployment." Why?**
**A:** Likely missing/weak readiness probe — new pods get traffic before ready — or no graceful shutdown, so old pods are killed mid-request. Fix: proper readiness probe, `maxUnavailable: 0`, `preStop` sleep + `terminationGracePeriodSeconds`, and PDB.

**S18. You need different replica counts and resources in dev vs prod using the same manifest.**
**A:** Use Kustomize overlays (`base/` + `overlays/dev`, `overlays/prod` with patches) or Helm with `values-dev.yaml` / `values-prod.yaml`. Deploy to separate namespaces or clusters. Keep the base identical so environments don't drift.

**S19. How would you automate this deployment so nobody runs kubectl manually?**
**A:** Pipeline: commit → Jenkins/GitHub Actions builds image → Trivy scan → push to Artifact Registry tagged with git SHA → update manifest/Helm values in a config repo → Argo CD syncs to GKE → smoke test → Slack notification. Cluster creation in Terraform with remote state in a GCS bucket.

**S20. Product team reports checkout is slow, but no pod is crashing. How do you find the slow service?**
**A:** Checkout calls 6 services, so use distributed tracing (OpenTelemetry → Cloud Trace / Jaeger) to see which span is slow. Check Prometheus/Grafana latency (p95/p99) per service, CPU throttling, `kubectl top`, and logs for timeouts. Common culprits: currencyservice under load, CPU-throttled pods, or cross-zone latency. Fix by scaling, tuning limits, or caching.

---

## 📌 Quick Revision Commands

```bash
kubectl get pods -o wide                 # pods with node & IP
kubectl get endpoints <svc>              # check selector actually matched pods
kubectl describe pod <pod>               # events, probe failures, OOM
kubectl logs <pod> --previous            # logs of crashed container
kubectl rollout status deploy/<name>     # watch rollout
kubectl rollout undo deploy/<name>       # rollback
kubectl scale deploy/<name> --replicas=3 # scale
kubectl top pods / kubectl top nodes     # resource usage
kubectl get events --sort-by=.lastTimestamp
```

---

# 🎤 How to Speak About This Project in an Interview

This project works well in a DevOps/Kubernetes interview because it covers the full application delivery lifecycle:

**Microservices → Git → Jenkins → Docker → Kubernetes → Deployment → Production Support**

> 💡 **Golden rule:** Don't just read out the responsibilities from your resume. Explain the project as a **real-world problem that you solved**.

Below is an interview-ready explanation that a DevOps Engineer with 3–5+ years of experience can use.

---

## 1. How to Introduce the Project

**Interviewer:** *"Explain your JioMart project and your roles and responsibilities."*

### ✅ Interview-ready answer

> "In my previous project, I worked on an E-Commerce online shopping application for JioMart. Our main job was to build and deploy a microservices-based application on Kubernetes.
>
> The application supported online grocery shopping. Functions like user management, product catalog, shopping cart, order management and payment processing were built as independent microservices.
>
> My role was mainly DevOps Engineer and Kubernetes Administrator.
>
> I set up the CI/CD pipeline using Jenkins, containerized the application services using Docker, and deployed those services on Kubernetes.
>
> We used Git for source code management, and developers pushed their code changes there. Jenkins was integrated with the Git repository to automate the build and deployment process.
>
> During CI, Jenkins pulled the latest source code, built the application, created Docker images using Dockerfiles and pushed those images to a container registry.
>
> For CD, we used Kubernetes Deployment and Service manifests to deploy the microservices into the Kubernetes cluster.
>
> I also configured Jenkins jobs, managed Kubernetes deployments, troubleshot deployment issues, maintained documentation and gave knowledge transfer to the QA team.
>
> Apart from that, I was part of production support and on-call rotations, troubleshooting production incidents to keep the application available."

---

## 2. Explain the Project Architecture

This is probably the **most important part**. If you explain the architecture properly, the interviewer can see that you have practical knowledge.

| Component | Responsibility |
|-----------|----------------|
| Git | Source code management |
| Jenkins | CI/CD automation |
| Maven / Gradle | Application build (if Java-based) |
| Docker | Containerization |
| Docker Registry | Store Docker images |
| Kubernetes | Container orchestration |
| Deployment | Manage application Pods |
| Service | Internal communication between microservices |
| Ingress | External application access |
| Monitoring | Application and cluster monitoring |

### Complete flow (say it in this order)

1. Developers write code and push it to Git.
2. A webhook or polling triggers Jenkins.
3. Jenkins checks out the latest code.
4. Jenkins builds and validates the application.
5. Docker builds the container image.
6. The image is pushed to a container registry.
7. Jenkins deploys the latest image to Kubernetes.
8. Kubernetes creates or updates Pods through Deployments.
9. Kubernetes Services handle internal communication between microservices.
10. Users reach the application through an external Load Balancer or Ingress.

---

## 3. Explain Your CI/CD Pipeline Practically

**Interviewer:** *"Can you explain how you implemented CI/CD using Jenkins?"*

Explain it in **two stages**:

### Continuous Integration (CI)
1. Developer pushes code to GitHub/GitLab.
2. The Jenkins pipeline is triggered.
3. Code checkout and dependency installation.
4. Application compilation and testing.
5. Docker image creation.
6. Push the image to the Docker Registry.

### Continuous Deployment (CD)
1. Jenkins connects to the Kubernetes cluster.
2. Updates the application image in the deployment manifests.
3. Applies the Kubernetes manifests using `kubectl`.
4. Kubernetes performs rolling updates.
5. Checks the deployment rollout status.
6. The application becomes available to users.

---

## 4. Important Kubernetes Questions for This Project

Expect the interviewer to go deeper into Kubernetes, because your resume specifically mentions Kubernetes Cluster, Deployment and Services. Prepare these 15:

| # | Interview question | What you should explain |
|---|--------------------|-------------------------|
| 1 | How did you create your Kubernetes cluster? | Managed or self-managed cluster, nodes and networking |
| 2 | What is a Pod? | Smallest deployable Kubernetes unit |
| 3 | What is a Deployment? | Manages ReplicaSets and Pods |
| 4 | What is a Kubernetes Service? | Stable network endpoint |
| 5 | ClusterIP vs NodePort vs LoadBalancer? | Different service exposure methods |
| 6 | How do microservices communicate? | Kubernetes DNS and Services |
| 7 | How do you update an application? | Rolling update |
| 8 | How do you roll back a deployment? | `kubectl rollout undo` |
| 9 | How do you troubleshoot CrashLoopBackOff? | Logs, describe, events and configuration |
| 10 | How do you troubleshoot ImagePullBackOff? | Registry access, image name and credentials |
| 11 | How do you manage application configuration? | ConfigMaps and Secrets |
| 12 | How do you scale an application? | Replicas and HPA |
| 13 | How do you check Pod logs? | `kubectl logs` |
| 14 | How do you expose the application externally? | Ingress or LoadBalancer |
| 15 | How do you ensure application availability? | Replicas, probes and resource management |

---

## 5. Practical Commands You Should Know

When the interviewer asks how you troubleshoot your application in Kubernetes, walk through these.

**Check cluster and nodes**
```bash
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
```

**Check application deployments**
```bash
kubectl get deployments -n production
kubectl get pods -n production
kubectl get services -n production
```

**Troubleshoot a failing Pod**
```bash
kubectl describe pod <pod-name> -n production
kubectl logs <pod-name> -n production
kubectl logs <pod-name> --previous -n production
kubectl get events -n production
```

**Deployment management**
```bash
kubectl apply -f deployment.yaml
kubectl rollout status deployment/product-service
kubectl rollout history deployment/product-service
kubectl rollout undo deployment/product-service
```

**Scaling**
```bash
kubectl scale deployment product-service --replicas=5
```

---

## 6. Real-World Production Scenarios (How to Answer)

Interviewers use these to check whether you have actually handled production problems.

### Scenario 1: Application is not accessible after deployment
**Interviewer:** *"Your application is deployed successfully, but users can't access it. What will you check?"*

> "I will troubleshoot layer by layer:
> - First, check whether the Pods are running.
> - Verify the Kubernetes Service and its selectors.
> - Check whether the Service endpoints point to the correct Pods.
> - Verify the Ingress configuration and DNS.
> - Check LoadBalancer health and security group/firewall rules.
> - Finally, check the application logs."

```bash
kubectl get pods
kubectl get svc
kubectl get endpoints
kubectl get ingress
kubectl describe ingress
```

### Scenario 2: New deployment is failing
> "I will first check the deployment rollout status. Then I will inspect the Pods, events and application logs. If the new release is causing a production issue, I will coordinate with the team and roll back to the previous stable version."

```bash
kubectl rollout status deployment/product-service
kubectl rollout undo deployment/product-service
```

### Scenario 3: Pod is continuously restarting
> "I will check the Pod status, the describe output and the previous container logs. I will look for application exceptions, missing environment variables, resource limits, failed health checks and dependency connectivity."

### Scenario 4: Jenkins pipeline succeeds but the application isn't updated
> "I will check whether Jenkins pushed the correct Docker image tag, whether the Kubernetes Deployment references that tag, and whether the rollout completed. I will also verify the image pull policy and the deployment events."

### Scenario 5: One microservice can't talk to another
> "I will verify that both services are running, check the Service selectors and endpoints, validate Kubernetes DNS resolution and test network connectivity. I will also check NetworkPolicies if any are configured."

---

## 7. Jenkins Pipeline Example You Should Understand

Use this simplified example to explain your CI/CD implementation:

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "registry.example.com/product-service"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/example/app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/product-service \
                  product-service=$IMAGE_NAME:$IMAGE_TAG

                kubectl rollout status deployment/product-service
                '''
            }
        }
    }
}
```

> ⚠️ This pipeline is only an illustration. A real implementation depends on your project's configuration, credentials, build tools and deployment strategy.

---

## 8. Difficult Questions the Interviewer May Ask

Prepare these especially well:

1. Why did you choose a microservices architecture instead of a monolith?
2. How do you handle communication between multiple microservices?
3. What happens when one microservice goes down?
4. How do you manage Docker image versioning?
5. How do you manage secrets in Jenkins and Kubernetes?
6. How do you implement zero-downtime deployments?
7. How do you troubleshoot a failed Jenkins pipeline?
8. What is the difference between readiness and liveness probes?
9. How do you manage different environments like Dev, QA, UAT and Production?
10. How do you handle database connectivity from Kubernetes Pods?
11. How do you perform rolling updates?
12. How do you monitor Kubernetes cluster health?
13. How do you secure the Kubernetes cluster?
14. How do you handle production incidents?
15. What was the biggest technical challenge in this project?

---

## 9. Be Ready to Defend Your Project

An interviewer may challenge some of the statements in your project description:

| Your resume statement | What you must be able to explain |
|-----------------------|----------------------------------|
| Created Kubernetes cluster | Was it EKS, AKS, GKE or kubeadm? |
| Created Docker images | Which application runtime and Dockerfile did you use? |
| Configured Jenkins | Freestyle jobs or Jenkinsfile pipelines? |
| Created Git repository | GitHub, GitLab or Bitbucket? |
| Microservices architecture | What were the actual microservices? |
| Production support | Give one real incident and how you resolved it |
| CI/CD automation | Walk through one complete pipeline run |

> 🚫 **Important:** Don't claim AWS EKS, Java, Maven, Helm, Prometheus and so on unless you actually used them in this project. Interviewers can go very deep into each technology.
>
> 🚫 Also, **don't invent** performance improvements, traffic numbers or business metrics.

---

*Prepared for CloudDevOpsHub — Multi-Cloud + DevOps with AI Bootcamp, Batch 45.*
