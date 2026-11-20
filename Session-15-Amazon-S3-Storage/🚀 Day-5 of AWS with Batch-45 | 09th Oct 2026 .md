# AWS Day 5 - Session Summary: Amazon S3 (Simple Storage Service), Storage Classes & Static Website Hosting

A comprehensive reference guide based on the Day 5 AWS Cloud & DevOps session by Vikas from CloudDevOpsHub.

---

## 1. The Storage Problem & The Why Behind Amazon S3

### 1.1 The Challenge with Traditional On-Premise Storage
Before managed cloud object storage, organizations handling rapidly accumulating digital data (such as application backups, database dumps, media files, and server logs) faced significant operational hurdles:
- **Massive Capital Expenditure (CapEx)**: Buying expensive storage arrays, server racks, SAN/NAS hardware, and maintaining redundant power and cooling systems.
- **Heavy Operational Overhead (OpEx)**: Organizations required dedicated specialized teams to keep storage alive:
  - **Storage Engineering Team**: Managing disks, RAID arrays, volume provisioning, and hardware replacements.
  - **Network Engineering Team**: Ensuring throughput, low-latency SAN fabric routing, and switch connectivity.
  - **Access & Identity Management Team**: Managing access lists and storage quotas.
  - **Infrastructure & DBA Teams**: Managing physical backup routines.
  - **Security & Compliance Teams**: Protecting stored data from physical theft and intrusion.
- **Cost**: A small company or training hub storing several terabytes of data on-premise could easily incur **₹5,00,000 to ₹10,00,000+ per month** in hardware maintenance and engineering salaries alone.

### 1.2 Real-World Case Study: Content & Course Recordings
- **Data Accumulation Reality**:
  - A single live batch session produces approximately **2 to 5 GB** of raw video recordings daily.
  - In a single month, a single batch produces **~150 to 250 GB** of video content.
  - Over a complete batch lifecycle, this accumulates to **1 TB+**.
  - Combined with high-definition 4K video shoots and marketing media, data generation rapidly reaches **2 to 5 TB per month**.
- **Why Consumer Cloud Drives (e.g., Google Drive) Fail for DevOps**:
  - Unprofessional for enterprise architectures.
  - Lacks infrastructure automation, programmable REST APIs, custom domain mapping, access policies, lifecycle transitions, and scalable multi-user authentication.

### 1.3 Amazon S3 as the Managed Solution
Amazon Web Services introduced **Amazon S3 (Simple Storage Service)** to eliminate these pain points:
- **Zero Upfront Investment**: No data centers to build or disks to pre-purchase; pay purely for what you consume.
- **Serverless & Managed**: No operating systems to patch, no capacity planning, and no hardware maintenance.
- **Web-Accessible**: Data is accessible globally over HTTP/HTTPS through secure REST APIs, web consoles, SDKs, or public web endpoints within seconds.
- **Elastic & Scalable**: Scales transparently from a few kilobytes to petabytes without performance degradation.

---

## 2. Amazon S3 Core Architecture & Key Concepts

### 2.1 Block Storage vs. Object Storage
Understanding the architectural difference between Amazon EBS and Amazon S3 is a fundamental cloud concept:

| Feature | Amazon EBS (Elastic Block Store) | Amazon S3 (Simple Storage Service) |
| :--- | :--- | :--- |
| **Storage Architecture** | Block Storage | Object Storage |
| **Data Organization** | Raw disk blocks formatted with a filesystem (ext4, xfs, NTFS) | Flat namespace; key-value object store (Buckets and Keys) |
| **Attachment** | Bound to a single EC2 instance in the same Availability Zone | Standalone web service accessible globally over the internet via HTTP/HTTPS |
| **Use Case** | OS boot drives, transaction databases (MySQL, PostgreSQL), active file systems | Media files, videos, documents, backups, big data datasets, static web assets |
| **Scalability** | Fixed provisioned volume sizes (scaled manually or via volume modification) | Virtually unlimited; grows and shrinks dynamically per object |

### 2.2 S3 Capacity Limits
- **Virtual Unlimited Capacity**: A single bucket can store an infinite number of objects and total bytes.
- **Single Object Maximum Size**: A single object stored in Amazon S3 can range from **0 bytes up to 5 TB**.
- **Upload Constraints**:
  - A single `PUT` request can upload objects up to **5 GB**.
  - For objects exceeding 5 GB (and strongly recommended for files larger than 100 MB), the **Multipart Upload API** must be used, which uploads objects in parts concurrently and reassembles them up to the **5 TB** maximum.

### 2.3 Durability vs. Availability (The SLA Difference)
Candidates frequently confuse Durability with Availability during technical interviews:

- **Durability (Data Integrity & Retention)**:
  - **SLA**: **99.999999999% (11 9's)** for standard storage classes.
  - **What it means**: S3 protects your objects from loss, bit rot, corruption, and hardware destruction. AWS automatically replicates your data across a minimum of **three geographically separated Availability Zones** (multiple physical facilities).
  - **Real-World Analogy**: If you upload **10,000 objects** to S3, you can statistically expect to lose a single file once every **10 million years**.
- **Availability (System Uptime & Accessibility)**:
  - **SLA**: Typically **99.9%** annual uptime.
  - **What it means**: The probability that your data is retrievable over the network when requested at any given moment.

### 2.4 Buckets & The Global Unique Namespace
- **What is a Bucket?**: A logical container for objects in Amazon S3 ("Balti" / bucket, where the stored data files are the "Paani" / water).
- **The Global Namespace Rule**:
  - While an S3 bucket is created in a specific AWS Region (e.g., `ap-south-1` Mumbai or `us-east-1` N. Virginia), **the bucket name must be globally unique across all existing AWS accounts worldwide**.
- **Why Global Uniqueness is Mandatory**:
  - Every S3 bucket is assigned an internet-routable DNS hostname (e.g., `http://<bucket-name>.s3.<region>.amazonaws.com` or `http://<bucket-name>.s3-website.<region>.amazonaws.com`).
  - Because public DNS names cannot duplicate globally, duplicate bucket names would create routing collisions on the public internet.
- **Naming Conventions**:
  - Must be between **3 and 63 characters long**.
  - Must consist solely of **lowercase letters, numbers, and hyphens (`-`)**.
  - Must begin and end with a letter or number.
  - Uppercase letters and underscores are strictly prohibited.

---

## 3. S3 Storage Classes Deep Dive & Cost Optimization

Amazon S3 provides distinct storage tiers optimized for data access patterns, retrieval latency, and cost:

| Storage Class | Designed For | Availability Zones | Min Duration | Retrieval Fee | Retrieval Latency | Relative Storage Cost |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **S3 Standard** | Active, frequently accessed data (web assets, active uploads) | >= 3 AZs | None | None | Milliseconds | Baseline (~$0.023/GB) |
| **S3 Intelligent-Tiering** | Dynamic, unknown, or fluctuating access patterns | >= 3 AZs | None | None | Milliseconds | Standard rate + small monthly monitoring fee |
| **S3 Standard-IA (Infrequent Access)** | Long-lived data accessed infrequently (monthly audits, recent backups) | >= 3 AZs | 30 days | Per GB retrieved | Milliseconds | ~50% cheaper storage |
| **S3 One Zone-IA** | Non-critical, easily reproducible data accessed infrequently | **1 AZ only** | 30 days | Per GB retrieved | Milliseconds | ~80% of Standard (~1/3 discount) |
| **S3 Glacier Flexible Retrieval** | Archive data accessed 1–2 times per year (historical logs) | >= 3 AZs | 90 days | Per GB retrieved | Minutes to hours (Expedited, Standard, Bulk) | ~1/6th to 1/10th of Standard |
| **S3 Glacier Deep Archive** | Long-term digital preservation and regulatory compliance (2–10+ years) | >= 3 AZs | 180 days | Per GB retrieved | 12 to 48 hours | Lowest cost in cloud (~1/100th of Standard) |

### 3.1 Detailed Storage Class Breakdown

#### S3 Standard
- Designed for active, "hot" data requiring immediate, continuous access.
- Objects are distributed across multiple Availability Zones with sub-second retrieval latency.
- Best suited for active web applications, current batch course recordings, media streaming platforms, and dynamic content.

#### S3 Intelligent-Tiering
- Automatically optimizes storage costs by moving data between access tiers based on runtime activity without operational intervention.
- Evaluates access patterns over a **30-day window**:
  - Objects unaccessed for 30 consecutive days shift to an Infrequent Access tier.
  - Objects unaccessed for 90 days shift to an Archive Instant Access tier.
  - If an object is accessed, it immediately shifts back to the Frequent Access tier with zero retrieval penalties.
- Incurs a tiny per-object monitoring fee, which is offset by massive storage savings for large datasets with unpredictable access patterns.

#### S3 Standard-IA (Infrequent Access)
- Ideal for data that is critical and requires millisecond access when requested, but is queried rarely (e.g., once or twice a month).
- Example: Disaster recovery copies, secondary backups, or last month's financial spreadsheets.
- Cheaper storage cost per GB than Standard, but AWS charges a data retrieval fee per GB accessed.

#### S3 One Zone-IA
- Unlike Standard-IA, data is stored inside a **single Availability Zone**.
- Cost is significantly lower, but **data is not resilient to physical datacenter destruction**. If that specific datacenter encounters a catastrophic event (e.g., flooding or fire), data loss can occur.
- Best for non-critical, reproducible backups (e.g., secondary thumbnails or raw source files that can be regenerated from an original master).

#### S3 Glacier Flexible Retrieval
- Designed for cold archival storage where immediate retrieval is not required.
- Real-world example: A bank customer requesting their 5-year-old bank statement. The bank acknowledges the request and processes it within a few hours because historical data sits in cold archive storage.
- Offers three retrieval tiers:
  - **Expedited**: 1 to 5 minutes.
  - **Standard**: 3 to 5 hours.
  - **Bulk**: 5 to 12 hours.

#### S3 Glacier Deep Archive
- The lowest-cost storage class across all cloud providers.
- Engineered for strict compliance, legal discovery, and healthcare retention where data must be preserved for 7 to 10+ years but is almost never queried.
- Retrieval times range from **12 to 48 hours**.

### 3.2 S3 Lifecycle Management Rules
Instead of manually shifting files across classes as they age:
- **Lifecycle Configuration Rules** allow you to define automated transition policies directly on a bucket or prefix:
  - Day 0: Object created in **S3 Standard**.
  - Day 30: Transition to **S3 Standard-IA** or **S3 Intelligent-Tiering**.
  - Day 90 / Day 365: Transition to **S3 Glacier Flexible Retrieval**.
  - Day 730 (2 Years): Transition to **S3 Glacier Deep Archive**.
  - Day 2555 (7 Years): Permanent automated deletion / expiration.

---

## 4. Hands-On Practical: Static Website Hosting & Resolving HTTP 403 Forbidden

In Day 5, students built and deployed an online personal portfolio website hosted on an S3 bucket and systematically troubleshot access permissions.

### 4.1 Step 1: Bucket Creation
1. Navigate to the AWS Management Console > Search for **S3**.
2. Click **Create bucket**.
3. Configure settings:
   - **Bucket type**: General purpose.
   - **Bucket name**: Must be globally unique, lowercase, and DNS-compliant (e.g., `studentname-portfolio-2026`).
   - **AWS Region**: Select the target region (e.g., `ap-south-1` Mumbai).
4. Leave other default settings for now and click **Create bucket**.

### 4.2 Step 2: Uploading Static Website Files
1. Open the created bucket.
2. Prepare your portfolio website package (unzipped HTML, CSS, JavaScript, and media assets):
   - Individual files: `index.html`, `about.html`, etc.
   - Folders: `assets/` (images, CSS styles, JavaScript bundles) and `forms/`.
3. Click **Upload** > Click **Add files** (upload the root HTML files).
4. Click **Add folder** > Select and upload the `assets/` and `forms/` folders.
5. Verify total uploaded objects (in session: 111 objects total).
6. Click **Upload** and wait for 100% completion.

### 4.3 Step 3: Enabling Static Website Hosting
1. Go to the bucket's **Properties** tab.
2. Scroll to the bottom to find **Static website hosting**.
3. Click **Edit**.
4. Select **Enable**.
5. Set **Hosting type** to *Host a static website*.
6. In **Index document**, enter the exact entry point file name: `index.html`.
7. Click **Save changes**.
8. AWS generates a public website endpoint URL formatted as:
   `http://<bucket-name>.s3-website.<region>.amazonaws.com`

### 4.4 Step 4: The Problem Encountered — HTTP 403 Forbidden (Access Denied)
When opening the generated S3 website endpoint in a browser, the page fails with:
```text
403 Forbidden
Code: AccessDenied
Message: Access Denied
```

#### Why Does 403 Forbidden Occur?
By default, Amazon S3 enforces two layers of protection:
1. **Block Public Access (BPA)**: An overarching account- and bucket-level safety switch enabled by default to prevent accidental data leaks.
2. **Implicit Deny / Private Objects**: Every object uploaded into Amazon S3 is private by default and accessible only by the IAM identity that uploaded it.

### 4.5 Step 5: Step-by-Step Resolution of the 403 Forbidden Error

To make the static website accessible to global internet users, three security settings must be adjusted:

#### Adjustment 1: Disable "Block All Public Access"
1. Navigate to the bucket's **Permissions** tab.
2. Under **Block public access (bucket settings)**, click **Edit**.
3. Uncheck **Block *all* public access**.
4. Click **Save changes**.
5. Type `confirm` in the confirmation dialog and click **Confirm**.

#### Adjustment 2: Enable Object Ownership (ACLs Enabled)
By default, modern S3 buckets disable Access Control Lists (ACLs) and enforce "Bucket owner enforced". To allow public ACL operations:
1. In the **Permissions** tab, scroll to **Object Ownership**.
2. Click **Edit**.
3. Select **ACLs enabled**.
4. Choose **Bucket owner preferred**.
5. Check the acknowledgement box: *"I acknowledge that ACLs will be restored"*.
6. Click **Save changes**.

#### Adjustment 3: Make Uploaded Objects Public Using ACLs
Even though public access is unblocked and ACLs are enabled, existing objects remain marked as private:
1. Navigate to the **Objects** tab.
2. Select all files and folders (or check the top checkbox to select all objects).
3. Click the **Actions** dropdown menu.
4. Select **Make public using ACL**.
5. Review the object list and click **Make public**.
6. Click **Close**.

#### Verification
Return to your browser and refresh the S3 website endpoint URL (`http://<bucket-name>.s3-website.<region>.amazonaws.com`). The static portfolio website renders successfully worldwide.

### 4.6 Updating Website Content & The "Re-Upload Trap"
During the session, students edited `index.html` locally to replace placeholder text and profile images with their own details:
- **Developer Thumb Rule**: Never attempt manual code hacks in production cloud consoles. Always edit website code and assets locally on your workstation, verify locally, and re-upload.
- **The "Re-Upload Trap"**:
  - When a student deleted `index.html` or replaced an image with a new upload, visiting the website immediately returned **403 Forbidden** again.
  - **Root Cause**: Any *new* object uploaded to an S3 bucket is uploaded with default **private** permissions.
  - **Resolution**: Every newly uploaded object must be explicitly selected and made public via **Actions > Make public using ACL** (or governed permanently by an automated S3 Bucket Policy).

---

## 5. Production Considerations, Hosting Costs & Enterprise Architecture

### 5.1 Static Website Hosting vs. Dynamic Web Applications
Understanding what can and cannot run inside Amazon S3 is a core architectural boundary:
- **Supported on S3 Static Website Hosting**:
  - Pure client-side static files: HTML, CSS, client-side JavaScript, WebAssembly.
  - Client-Side Rendered (CSR) Single Page Applications (SPAs) built with React, Vue, or Angular, where JavaScript executes directly in the user's browser.
  - Static media: images, PDFs, videos, stylesheets, web fonts.
- **Unsupported on S3**:
  - Server-side scripting: PHP, Python/Django/Flask, Node.js Express, Java Spring Boot, Ruby on Rails.
  - Direct database queries or embedded relational database engines.
  - Dynamic server-side session authentication or server-rendered pages (SSR).

### 5.2 Real-World Hosting Cost Analysis
- One of the biggest advantages of S3 static hosting is near-zero cost:
  - Storing a standard static portfolio (~6 MB to 50 MB) consumes less than $0.002 per month.
  - An entire year of hosting a static portfolio website typically costs **between ₹3 and ₹10 (approx. $0.05 to $0.15)** under moderate personal traffic.
  - **Cost Caution**: Avoid uploading unnecessarily massive files (e.g., uncompressed 1 GB images or raw 4K videos) into public web buckets without compression, as high egress bandwidth and storage will increase charges. Keep images optimized (under 1–2 MB).

### 5.3 Production Architecture Enhancements (Discussed in Session)
While hosting directly from the S3 website endpoint works well for internal testing and student portfolios, enterprise architectures enhance this setup:
1. **Custom Domain Mapping (Amazon Route 53)**:
   - Instead of long AWS URLs, Route 53 maps friendly corporate domains (e.g., `portfolio.example.com` or `clouddevopshub.com`) directly to S3 endpoints using DNS Alias (A) records.
2. **Amazon CloudFront (CDN) & SSL/TLS Encryption**:
   - S3 static website endpoints only support unencrypted HTTP (Port 80) over public endpoints.
   - Placing **Amazon CloudFront** in front of S3 provides:
     - Free SSL/TLS HTTPS encryption (Port 443) via AWS Certificate Manager (ACM).
     - Global Edge Location caching, reducing latency for international users.
     - Origin Access Control (OAC), keeping the underlying S3 bucket completely private from the public internet.
3. **Security, DDoS Protection & Firewalls**:
   - Public S3 URLs can become targets for malicious traffic, HTTP request floods, and DDoS scraping.
   - Integrating **AWS WAF (Web Application Firewall)** and **AWS Shield** (comparable to GCP Cloud Armor or Cloudflare) protects frontend web endpoints from Layer 7 attacks, rate-limiting malicious IPs and filtering malicious requests before they hit storage.
4. **Cross-Account Access & Delegated IAM Roles**:
   - In corporate multi-account setups (e.g., Account A needing to read/write data in Account B's S3 bucket), access is managed using **IAM Policies**, **S3 Bucket Policies**, and **Cross-Account IAM Roles (with Trust Policies)** rather than hardcoding static access keys.

---

## 6. Top 10 Core Interview Questions & Answers

### Q1: What is Amazon S3, and how does it fundamentally differ from Amazon EBS?
**Answer:**
Amazon S3 (Simple Storage Service) is a fully managed, serverless **object storage service** designed to store and retrieve any amount of unstructured data (files, images, videos, backups, logs) from anywhere over the internet via HTTP/HTTPS.

In contrast, Amazon EBS (Elastic Block Store) provides persistent **block-level storage volumes** designed to attach directly to a single EC2 instance within the same Availability Zone, acting like a physical hard drive formatted with an operating system filesystem (e.g., ext4, xfs). While EBS is bound to an instance and an Availability Zone for fast, transactional I/O, S3 operates with a flat namespace globally accessible via web endpoints and scales virtually without limits.
and difference between bucket and object is -: A bucket is the storage container that holds your data, while an object is an actual file (like an image, video, or document) along with its metadata 

---

### Q2: What is the difference between S3 Durability and S3 Availability?
**Answer:**
- **Durability (99.999999999% / 11 9's)** refers to the protection against permanent data loss or corruption. AWS achieves this by automatically replicating data synchronously across a minimum of three geographically separated Availability Zones. If you store 10,000 objects in S3, on average you might lose one file every 10 million years.
- **Availability (typically 99.9%)** refers to system uptime and network accessibility—the percentage of time your objects are readily retrievable over the network when requested.

---

### Q3: Why must an Amazon S3 bucket name be globally unique across all AWS accounts?
**Answer:**
Every Amazon S3 bucket is assigned a publicly resolvable DNS domain name (e.g., `http://<bucket-name>.s3.<region>.amazonaws.com`). Because public DNS records on the internet must be unique to route traffic to the correct destination without collisions, no two AWS accounts anywhere in the world can share the exact same bucket name.

---

### Q4: What is the maximum size of a single object in Amazon S3, and how must large files be uploaded?
**Answer:**
The maximum size of a single object in Amazon S3 is **5 TB**.
- A single standard `PUT` upload operation can upload files up to **5 GB**.
- For files larger than 5 GB (and recommended for any file over 100 MB), the **S3 Multipart Upload API** must be used. It uploads file parts in parallel, improves throughput, supports pause-and-resume on network failure, and reassembles parts into the final object up to 5 TB.

---

### Q5: Explain the purpose and operation of S3 Intelligent-Tiering.
**Answer:**
S3 Intelligent-Tiering is a storage class engineered for datasets with unknown, changing, or unpredictable access patterns. It automatically analyzes object access at a granular level:
- Objects start in a **Frequent Access Tier**.
- If unaccessed for **30 consecutive days**, they automatically transition to an **Infrequent Access Tier** (~40% cheaper).
- If unaccessed for **90 consecutive days**, they shift to an **Archive Instant Access Tier** (~68% cheaper).
- If an object is accessed at any point, it instantly returns to the Frequent Access tier with **millisecond retrieval latency and zero retrieval fees**. A small monthly monitoring fee per object applies.

---

### Q6: When would you choose S3 One Zone-IA over S3 Standard-IA?
**Answer:**
You choose **S3 One Zone-IA** when your primary objective is reducing storage costs for infrequently accessed data (it costs ~20% less than Standard-IA), and the stored data is **non-critical or easily reproducible**.

Because One Zone-IA stores data within a single Availability Zone rather than three, it lacks geographic redundancy. If that specific physical datacenter experiences a disaster, data could be lost. Standard-IA should always be used for critical backups, while One Zone-IA is appropriate for secondary backup copies or regenerable thumbnails.

---

### Q7: What are the exact steps required to host a static website on Amazon S3 and avoid the HTTP 403 Forbidden error?
**Answer:**
1. **Enable Static Website Hosting**: In Bucket Properties > Static website hosting > Select *Enable* and define the index document (e.g., `index.html`).
2. **Disable Block Public Access**: In Bucket Permissions > Edit *Block public access (bucket settings)* > Uncheck *Block all public access* and confirm.
3. **Enable ACLs**: In Bucket Permissions > Object Ownership > Select *ACLs enabled* (Bucket owner preferred) and acknowledge.
4. **Make Objects Public**: In Bucket Objects > Select all uploaded files and folders > Actions > Select *Make public using ACL* (or alternatively, apply a public bucket policy granting `s3:GetObject` to `*`).

---

### Q8: What are the architectural limitations of Amazon S3 Static Website Hosting?
**Answer:**
Amazon S3 only acts as a storage server delivering static files to web clients.
- **Supported**: HTML, CSS, client-side JavaScript, images, fonts, and client-side Single Page Applications (React, Angular, Vue).
- **Unsupported**: Server-side runtime execution (PHP, Python, Node.js server scripts, Ruby), dynamic server-side page rendering (SSR), server session state management, and direct database querying. Any dynamic processing requires backend compute (EC2, ECS, or AWS Lambda) fronted by an API Gateway or Load Balancer.

---

### Q9: What happens when you upload a new version of `index.html` to a public static S3 website without an automated bucket policy?
**Answer:**
By default, newly uploaded objects in S3 do not inherit the public ACL permissions of previous files; they are uploaded with **private** permissions by default. As a result, visiting the website will immediately return an **HTTP 403 Access Denied** error for that new file until an administrator manually selects the object and clicks **Make public using ACL**, or unless an overarching S3 **Bucket Policy** is applied to automatically grant public `s3:GetObject` permissions to all objects under the bucket prefix.

---

### Q10: How does Amazon S3 Lifecycle Management optimize enterprise cloud spending?
**Answer:**
S3 Lifecycle Management allows administrators to define automated declarative rules based on object age or prefixes to transition data down cheaper storage tiers without manual intervention:
- E.g., Move objects from **Standard** to **Standard-IA** after 30 days.
- Move from **Standard-IA** to **Glacier Flexible Retrieval** after 90 days.
- Move from **Glacier** to **Glacier Deep Archive** after 365 days.
- Permanently expire/delete objects after 7 years.
This automates data hygiene, meets regulatory compliance, and slashes long-term storage expenses by up to 90–95%.

---

## 7. Top 20 Real-World Scenario-Based Interview Questions & Answers

### Scenario 1: Troubleshooting HTTP 403 Forbidden on a Newly Deployed S3 Static Website
- **Scenario**: You have uploaded all HTML and CSS files for your company's marketing landing page into an S3 bucket and enabled Static Website Hosting with `index.html` configured as the index document. When you click the generated bucket website endpoint URL, your browser displays `403 Forbidden - AccessDenied`. How do you resolve this?
- **Answer**:
  1. Navigate to the S3 bucket's **Permissions** tab.
  2. Under **Block public access (bucket settings)**, click **Edit**, uncheck **Block *all* public access**, save changes, and type `confirm`.
  3. Ensure object-level public access is enabled:
     - Under **Object Ownership**, ensure ACLs are enabled (or leave ACLs disabled and write a Bucket Policy).
     - Under **Bucket Policy**, apply a JSON policy granting public read access:
       ```json
       {
         "Version": "2012-10-17",
         "Statement": [
           {
             "Sid": "PublicReadGetObject",
             "Effect": "Allow",
             "Principal": "*",
             "Action": "s3:GetObject",
             "Resource": "arn:aws:s3:::your-bucket-name/*"
           }
         ]
       }
       ```
  4. Refresh the endpoint; the website will load immediately.

---

### Scenario 2: Preventing 403 Errors on Continuous Website Updates
- **Scenario**: A web developer updates `index.html` on their laptop and re-uploads it to an S3-hosted static website bucket using the AWS Console. Immediately, users report that the homepage shows `403 Forbidden`, while all images in `/assets/` continue to load fine. Why did this happen, and how do you permanently fix it so developers don't have to manually click "Make Public" on every upload?
- **Answer**:
  - **Root Cause**: When a file is uploaded manually via the console, S3 creates a new object version with default private ACL permissions. Because the developer did not manually apply a public ACL during the upload step, the new `index.html` is private.
  - **Permanent Enterprise Solution**: Implement a bucket-level **Bucket Policy** (as shown in Scenario 1) granting `s3:GetObject` to `*` across `arn:aws:s3:::bucket-name/*`. With a bucket policy in place, *any* newly uploaded or overwritten object automatically inherits public read permissions at the bucket boundary, completely eliminating the need for object-level ACL maintenance.

---

### Scenario 3: Global Bucket Name Collision During Automated Pipeline Run
- **Scenario**: Your team is writing an automation script to deploy an S3 bucket for a client named `acme-devops`. The script fails with `BucketAlreadyExists: The requested bucket name is not available`. The AWS account has no buckets with this name. Why is this error occurring, and how do you design your deployment naming strategy to prevent it?
- **Answer**:
  - **Root Cause**: S3 bucket names share a single global DNS namespace across every AWS account worldwide. Another AWS customer in a different account has already registered the bucket name `acme-devops`.
  - **Remediation & Best Practice**:
    - Incorporate unique identifiers into bucket names, such as the company name, project environment, AWS region, and account ID or random hash.
    - Example: `acme-devops-prod-ap-south-1-123456789012` or `acme-portfolio-${random_uuid}`.
    - Update deployment templates (Terraform/CloudFormation) to dynamically construct DNS-compliant unique names.

---

### Scenario 4: Cost Optimization for High-Volume, Short-Lived Daily Video Processing
- **Scenario**: A media platform ingests 5 TB of raw video recordings daily. Processing nodes transcode the raw videos into 1080p and 720p streams within 6 hours. Once transcoded, the raw source videos are never needed again. A junior engineer suggests configuring a lifecycle rule to immediately move the raw videos to S3 Glacier Flexible Retrieval to save costs. Why is this a costly mistake, and what should be done instead?
- **Answer**:
  - **The Mistake**: S3 Glacier Flexible Retrieval enforces a **minimum billable storage duration of 90 days** (and Standard-IA enforces 30 days). If you delete or overwrite objects after 6 hours, AWS still bills you for the full 90-day storage duration, plus transition fees.
  - **Correct Architecture**: Keep the raw videos in **S3 Standard** (which has no minimum storage duration). Configure an S3 Lifecycle Rule to automatically **Expire / Delete** the raw video objects after **1 day**. This ensures you only pay for a few hours of standard storage with zero early deletion penalties.

---

### Scenario 5: Multi-Year Regulatory Compliance Storage for Banking Logs
- **Scenario**: A fintech startup is legally mandated by banking regulators to retain all transaction log files for 7 years. These logs are audited at most once every three years. The engineering team currently stores 50 TB of logs in S3 Standard, resulting in high monthly storage bills. How do you re-architect this storage to maximize cost savings while ensuring compliance?
- **Answer**:
  1. **Configure S3 Lifecycle Rule**:
     - Keep active logs in **S3 Standard** for the first 30 days.
     - At Day 30, transition the log objects directly to **S3 Glacier Deep Archive**.
     - At Day 2555 (7 years), configure the lifecycle rule to permanently expire/delete the objects.
  2. **Cost Impact**: S3 Glacier Deep Archive costs ~$0.00099 per GB/month compared to ~$0.023 for S3 Standard—a **~95% reduction** in storage costs.
  3. **Security Enhancement**: Apply **S3 Object Lock** in Compliance Mode with a 7-year retention period to prevent accidental deletion or tampering by any user (including the AWS root account).

---

### Scenario 6: Securing an S3 Static Website with HTTPS and Custom Domain
- **Scenario**: Your management approves your S3-hosted portfolio website but requires that it must be accessible via `https://portfolio.mycompany.com`. S3 static website hosting only exposes an unencrypted `http://` endpoint. What AWS services and steps are required to achieve secure HTTPS delivery on a custom domain?
- **Answer**:
  1. **Request an SSL/TLS Certificate**: Use **AWS Certificate Manager (ACM)** in the `us-east-1` (N. Virginia) region to generate a free public SSL certificate for `portfolio.mycompany.com`. Validate it via DNS.
  2. **Deploy an Amazon CloudFront Distribution**:
     - Set the CloudFront Origin to the S3 bucket's website endpoint (or direct S3 REST endpoint with Origin Access Control).
     - Attach the ACM SSL certificate to the CloudFront distribution.
     - Configure *Viewer Protocol Policy* to **Redirect HTTP to HTTPS**.
     - Set Alternate Domain Names (CNAME) to `portfolio.mycompany.com`.
  3. **Configure DNS in Amazon Route 53**:
     - Open the hosted zone for `mycompany.com`.
     - Create an **A record** (Alias) pointing `portfolio.mycompany.com` directly to the CloudFront distribution domain name.

---

### Scenario 7: Protecting a Public Static S3 Website from DDoS and HTTP Flooding
- **Scenario**: Your public S3 website URL was shared on social media and experienced an HTTP flood attack from a botnet sending 20,000 requests per second, resulting in unexpected S3 data transfer out (DTO) charges. How do you protect the architecture from Layer 7 attacks and rate-limit abusive clients?
- **Answer**:
  1. **Place Amazon CloudFront in Front of S3**: Never expose the raw public S3 bucket directly to the internet. Restrict S3 bucket access using an **Origin Access Control (OAC)** policy so only CloudFront can read files.
  2. **Enable AWS WAF (Web Application Firewall)**:
     - Attach an AWS WAF Web ACL to the CloudFront distribution.
     - Configure a **Rate-Based Rule** (e.g., limit individual IP addresses to a maximum of 2,000 requests per 5-minute window; block or CAPTCHA exceeding IPs).
     - Enable AWS Managed Rules for Known Bad Inputs and IP Reputation lists.
  3. **Leverage AWS Shield**: AWS Shield Standard automatically mitigates Layer 3 and Layer 4 DDoS attacks at the CloudFront edge for free.

---

### Scenario 8: Accidental Overwrite Protection on Critical S3 Objects
- **Scenario**: A developer accidentally uploaded an empty `index.html` file to an S3 bucket with the same key name, overwriting the production file and taking down the website. How do you protect your S3 buckets against accidental overwrites and human-error deletions?
- **Answer**:
  1. **Enable S3 Bucket Versioning**:
     - In Bucket Properties, enable **Bucket Versioning**.
     - When versioning is active, uploading an object with the same name does not overwrite the old data; S3 assigns a unique Version ID to the new file while preserving the prior version intact.
  2. **Recovery**: To restore the website, simply delete the latest version marker or copy the older Version ID to become the current version.
  3. **Extra Protection**: Enable **MFA Delete** via AWS CLI, requiring multi-factor authentication codes to permanently delete any object version or alter versioning states.

---

### Scenario 9: Cross-Account Access Between Account A (Compute) and Account B (S3 Storage)
- **Scenario**: An application running on an EC2 instance in AWS Account A needs to read and write reporting files to an S3 bucket located in AWS Account B. How do you securely establish this cross-account connectivity without using hardcoded access keys?
- **Answer**:
  1. **Account B (S3 Bucket Policy)**:
     - Attach a Bucket Policy on the target bucket in Account B allowing Account A's IAM Role to perform `s3:GetObject`, `s3:PutObject`, and `s3:ListBucket`.
  2. **Account A (IAM Role & Policy)**:
     - Create an IAM Role attached to the EC2 instance in Account A with an IAM policy granting permissions to access Account B's bucket ARN (`arn:aws:s3:::account-b-bucket/*`).
  3. **Object Ownership**: Ensure Account B configures **Object Ownership** as *Bucket owner enforced* so Account B automatically owns all objects uploaded by Account A, avoiding cross-account ownership conflicts.

---

### Scenario 10: Handling Large 20 GB Database Backup Upload Failures
- **Scenario**: A DevOps engineer attempts to upload a 20 GB database dump directly to an S3 bucket using the AWS Management Console over a slow office internet connection. At 85% completion, the network flickers, the upload aborts, and the engineer must restart from 0%. How do you resolve this?
- **Answer**:
  1. **Adopt S3 Multipart Upload via AWS CLI**:
     - Use the AWS CLI command:
       ```bash
       aws s3 cp backup.sql s3://my-backup-bucket/backup.sql
       ```
     - The AWS CLI automatically segments files larger than 100 MB into smaller parts (e.g., 8 MB or 16 MB chunks) and uploads them concurrently using multiple threads.
  2. **Fault Tolerance**: If a network interruption occurs, only the specific failed parts are retried rather than restarting the entire 20 GB file.
  3. **Clean Up Incomplete Parts**: Configure an S3 Lifecycle Rule with the action **Abort incomplete multipart uploads** after 7 days to delete orphaned part data and prevent hidden storage charges.

---

### Scenario 11: Blocking Public Access Across All Accounts in an Organization
- **Scenario**: The Chief Information Security Officer (CISO) discovers that developers are frequently disabling "Block Public Access" on buckets to test static websites, introducing data leak risks. How do you enforce at an enterprise level that NO S3 bucket can ever be made public, regardless of console actions?
- **Answer**:
  1. **AWS Organizations Service Control Policy (SCP)**:
     - Apply an SCP at the Root or Organizational Unit (OU) level that explicitly denies permissions to disable S3 Block Public Access:
       ```json
       {
         "Version": "2012-10-17",
         "Effect": "Deny",
         "Action": [
           "s3:PutAccountPublicAccessBlock",
           "s3:PutBucketPublicAccessBlock",
           "s3:PutBucketPolicy",
           "s3:PutBucketAcl"
         ],
         "Resource": "*",
         "Condition": {
           "StringEquals": {
             "s3:PublicAccessBlock": "false"
           }
         }
       }
       ```
  2. Even account administrators and root account users in member accounts cannot override this organizational guardrail.

---

### Scenario 12: Providing Temporary Secure Download Access to a Confidential PDF
- **Scenario**: A healthcare customer requires download access to a private medical report PDF stored in a strictly private S3 bucket. The customer does not have an AWS account or IAM credentials. You cannot make the bucket or object public. How do you securely share this file?
- **Answer**:
  - **Generate an S3 Pre-Signed URL**:
    - An authorized IAM user or backend application generates a **Pre-Signed URL** using the AWS CLI or SDK (e.g., Python Boto3):
      ```bash
      aws s3 presign s3://patient-reports-bucket/report-123.pdf --expires-in 3600
      ```
    - The URL embeds cryptographic query parameters authenticated by the generator's IAM credentials.
    - The customer uses the link to download the file directly via their browser. After 3,600 seconds (1 hour), the URL expires automatically, and subsequent access attempts return `Access Denied`.

---

### Scenario 13: Choosing Between S3 Standard-IA and S3 One Zone-IA for Machine Learning Datasets
- **Scenario**: A data science team generates 10 TB of intermediate feature training files every weekend. They query these files once a month. The original raw data is permanently stored on an on-premise cluster and can be re-extracted if needed. The team wants to reduce cloud costs. Which storage class should you recommend and why?
- **Answer**:
  - **Recommendation**: **S3 One Zone-IA**.
  - **Rationale**:
    - The data is accessed infrequently (once a month), satisfying the infrequent access pattern.
    - Storage cost is ~20% lower than Standard-IA and ~50% lower than S3 Standard.
    - Because the original dataset is securely stored on-premise, the intermediate data is **reproducible**. The risk of losing data in the event of a rare single-AZ failure is acceptable given the significant ongoing cost savings.

---

### Scenario 14: Diagnosing Broken CSS and Images on an S3-Hosted Website
- **Scenario**: A student deploys their static portfolio to an S3 bucket and verifies `index.html` loads. However, the webpage has no styling (raw unformatted text) and images are broken. Inspecting browser developer tools shows HTTP 404 errors for `/assets/css/style.css` and `/assets/img/hero.jpg`. How do you troubleshoot this?
- **Answer**:
  1. **Verify Folder Upload Structure**:
     - Check the bucket objects in the console. Often, users upload files from inside the `assets` folder directly to the bucket root instead of preserving the `assets/` directory structure.
  2. **Verify Case Sensitivity**:
     - S3 object keys are strictly case-sensitive. If `index.html` references `assets/img/Hero.JPG` but the S3 object is named `assets/img/hero.jpg`, S3 will return a 404 Not Found error.
  3. **Verify Relative vs. Absolute Paths**:
     - Ensure HTML file paths use relative references (`assets/css/style.css` or `./assets/...`) rather than local workstation absolute paths (e.g., `C:/Users/name/...`).

---

### Scenario 15: Optimizing High Request Rate Performance for an S3 Bucket
- **Scenario**: A high-throughput logging application uploads 10,000 files per second to an S3 bucket under a single key prefix `/logs/2026-10-09/`. The application begins receiving HTTP 503 Slow Down errors from S3. What is causing this, and how do you resolve it?
- **Answer**:
  - **Root Cause**: Amazon S3 automatically partitions buckets by prefix. A single partition supports up to **3,500 PUT/POST/DELETE requests per second** and **5,500 GET/HEAD requests per second**. Concentrating 10,000 writes/sec onto a single static prefix exceeds partition limits.
  - **Resolution**:
    - Introduce randomized or hashed prefixes to distribute writes across multiple internal S3 partitions:
      - Instead of `/logs/2026-10-09/app.log`, use a prefix strategy like `/logs/{hash}/2026-10-09/app.log` or partition by customer/sensor ID: `/logs/sensor-A1/2026/...`.
    - Implement exponential backoff and retry logic in the uploading application client.

---

### Scenario 16: Automated Data Deletion for Ephemeral Application Logs
- **Scenario**: An application fleet writes 500 GB of debug logs every day to an S3 bucket named `app-debug-logs`. These logs are only useful for real-time troubleshooting during active incidents and hold zero business value after 7 days. Storage costs are growing rapidly. How do you implement zero-maintenance automated cleanup?
- **Answer**:
  1. Open the bucket > **Management** tab.
  2. Click **Create lifecycle rule**.
  3. Rule Configuration:
     - **Rule name**: `DeleteLogsAfter7Days`.
     - **Filter**: Apply to all objects in the bucket (or prefix `debug-logs/`).
     - **Lifecycle rule actions**: Check **Expire current versions of objects**.
     - **Expiration**: Set *Days after object creation* to **7 days**.
  4. Save the rule. S3 will automatically queue and permanently delete all log objects older than 7 days daily with zero server overhead.

---

### Scenario 17: Enforcing Server-Side Encryption on All S3 Uploads
- **Scenario**: Compliance standards dictate that all healthcare records uploaded to an S3 bucket must be encrypted at rest using customer-managed AWS KMS keys (SSE-KMS), and any unencrypted upload attempt must be automatically rejected. How do you configure this?
- **Answer**:
  1. **Bucket Default Encryption**: Under Bucket Properties > *Default encryption*, select **Server-side encryption with AWS Key Management Service keys (SSE-KMS)** and select the custom KMS Customer Managed Key (CMK). Enable S3 Bucket Keys to reduce KMS request costs.
  2. **Enforce via Bucket Policy**: Add a bucket policy statement with an explicit `Deny` effect for `s3:PutObject` if the request does not include the KMS encryption header:
     ```json
     {
       "Sid": "DenyUnencryptedObjectUploads",
       "Effect": "Deny",
       "Principal": "*",
       "Action": "s3:PutObject",
       "Resource": "arn:aws:s3:::health-records/*",
       "Condition": {
         "StringNotEquals": {
           "s3:x-amz-server-side-encryption": "aws:kms"
         }
       }
     }
     ```

---

### Scenario 18: Restoring an Accidentally Deleted Object in a Versioned Bucket
- **Scenario**: A developer runs a cleanup script that executes an unintended delete command against an S3 bucket where Versioning is enabled. The critical file `customer_database.csv` has disappeared from the console file list. How do you recover this object?
- **Answer**:
  1. In the AWS S3 Console, open the bucket and navigate to the folder location.
  2. Toggle the **Show versions** slider to **ON**.
  3. Notice that `customer_database.csv` has a new top version with the type **Delete marker**.
  4. Select the specific row containing the **Delete marker** and click **Delete**.
  5. Confirm deletion of the delete marker.
  6. The previous version of `customer_database.csv` immediately restores as the active object and reappears in standard console views.

---

### Scenario 19: Preventing High Bill Shock on an S3-Hosted Static Portfolio
- **Scenario**: A student deploys an online portfolio containing a 4K introductory video file (3 GB) and uncompressed raw photography portfolio shots (50 MB each). The portfolio link goes viral on LinkedIn, attracting 50,000 visitors in three days. At the end of the week, the student receives an AWS bill of over $200. What caused this bill, and how should static portfolios be designed to avoid bill shock?
- **Answer**:
  - **Root Cause**: While S3 *storage* costs are negligible (~$0.023/GB), **Data Transfer Out (DTO)** to the public internet costs ~$0.09 per GB after the first free 100 GB. Delivering 3.5 GB of assets to 50,000 visitors generated ~175,000 GB of bandwidth, resulting in substantial egress bandwidth charges.
  - **Architectural Fixes**:
    1. **Optimize Media**: Compress all portfolio photography into modern WebP/JPEG formats under 200 KB each.
    2. **External Video Streaming**: Never host raw video files directly on S3 for static site streaming. Upload the video to YouTube or Vimeo and embed the player iframe into `index.html`.
    3. **Deploy Amazon CloudFront**: CloudFront includes a generous Always-Free tier (1 TB of data transfer out every month) and edge caches static files, dramatically reducing origin requests to S3.

---

### Scenario 20: Cross-Region Disaster Recovery for Critical Enterprise S3 Data
- **Scenario**: Your company's primary infrastructure operates in AWS Region `ap-south-1` (Mumbai). Management mandates an automated Disaster Recovery (DR) posture ensuring that if the entire Mumbai region suffers an extended multi-day outage, all uploaded business documents are available in `ap-southeast-1` (Singapore) within 15 minutes. How do you implement this?
- **Answer**:
  1. **Enable S3 Versioning**: Versioning must be enabled on both the source bucket (Mumbai) and target destination bucket (Singapore).
  2. **Configure S3 Cross-Region Replication (CRR)**:
     - Open the source bucket in Mumbai > **Management** tab > **Replication rules**.
     - Create a replication rule targeting the Singapore bucket.
     - Select an IAM Role granting S3 replication permissions.
  3. **Enable S3 Replication Time Control (S3 RTC)**:
     - Enable S3 RTC on the replication rule. S3 RTC provides an SLA guaranteeing that **99.99% of new objects are replicated across regions within 15 minutes**, backed by Amazon CloudWatch replication latency metrics and alarms.

---
*End of AWS Day 5 Session Summary. Master these core storage paradigms, security troubleshooting workflows, and scenario questions to excel in Cloud & DevOps engineering interviews.*
