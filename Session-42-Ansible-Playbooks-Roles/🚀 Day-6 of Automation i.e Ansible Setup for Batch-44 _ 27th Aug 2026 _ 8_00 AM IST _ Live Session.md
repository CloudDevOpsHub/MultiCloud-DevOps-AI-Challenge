## Key Outcomes

A full hands-on Ansible session was conducted covering theory, architecture, and end-to-end practical setup using Docker containers on Google Cloud Platform. Students created three Ubuntu containers (one Ansible master, two targets), installed Ansible and its dependencies on the master, configured SSH passwordless authentication, built an inventory host file, wrote a YAML playbook to install NGINX, and successfully executed it against target machines. Real-time troubleshooting scenarios were also demonstrated, and the session concluded with students committing their Ansible master container as a reusable Docker image pushed to Docker Hub.

---

## Ansible Concepts Covered

- **Configuration Management** defined as the automated execution of repeated operational activities across multiple IT systems (Linux servers, databases, monitoring setups, backups) to maintain a consistent desired state. 
- **Ansible vs. Terraform** distinction emphasized: Terraform is an Infrastructure-as-Code (IaC) tool for *creating* infrastructure; Ansible is a *configuration management* tool for managing and operating existing infrastructure. 
- **Ansible vs. Puppet/Chef**: Puppet and Chef are considered outdated (~5 years ago); Ansible now dominates with approximately 75–80% market adoption. 
- **Agentless architecture**: No Ansible software needs to be installed on target/client machines; connectivity is established purely via SSH, making it lightweight and scalable to thousands of VMs. 
- **Python dependency**: Python must be present on all target machines as Ansible depends on it; Python is not optional. 
- **Language**: Ansible configurations and tasks are written in **YAML**, referred to as **playbooks**. 
- **Developed by Red Hat** as an open-source product; freely available with no licensing cost. 
- **Windows support**: Ansible can connect to Windows machines via **WinRM**, but the session focused on Linux (Ubuntu), which represents ~98% of real-world use. 
- **Ansible came to market in 2012**; 2–4 years of Ansible automation experience is considered a strong resume asset. 

---

## Ansible Architecture Components

- **Ansible Master (Control Node)**: The central machine where Ansible is installed and all configuration files are stored; SSH connections originate from here to all target machines. 
- **Target Machines (Managed Nodes)**: Remote servers on which tasks are executed; no Ansible installation required on these nodes. 
- **Inventory / Host File** (`/etc/ansible/hosts`): Contains IP addresses or hostnames of all target machines, organized into groups (e.g., `[DB]`, `[Ubuntu]`, `[CentOS]`); multiple host files are supported for different server categories. 
- **Playbook**: A YAML file containing one or more tasks (commands) to be executed on target machines; tasks describe the desired state (e.g., install NGINX latest version). 
- **CMDB (Configuration Management Database)**: Stores the current state and history of changes made by Ansible, analogous to Terraform's state file or Kubernetes' etcd. 
- **SSH Key-Based (Passwordless) Authentication**: Required for Ansible to connect to 100+ machines without manual password entry; public/private key pair generated on master and public key copied to each target. 
- **Grouping in Host File**: IP addresses can be organized under named groups (e.g., `[DB]`, `[CentOS]`, `[Ubuntu]`); the playbook's `hosts:` field references the group name to scope execution. 

---

## Practical Setup — Step-by-Step Walkthrough

### Environment Preparation

- Students logged into **Google Cloud Console** and created a VM instance (Ubuntu 24.04 LTS, 2 vCPU, 8 GB RAM, 40 GB disk) with all network traffic allowed. 
- **Docker** was installed on the GCP VM using `apt update` followed by `apt install docker.io`; students verified installation with `docker -v`. 
- Three Ubuntu containers were created via Docker: `ansible_master`, `target1`, and `target2`; confirmed running with `docker ps`. 
- Students on AWS were advised the commands are identical since both platforms use Ubuntu 24.04. 

### Ansible Master Setup (Part 2)

- Entered the master container: `docker exec -it ansible_master bash`, then switched to root with `sudo -i`. 
- Ran `apt update` to refresh package lists inside the container. 
- Installed four dependencies in one command: **Python**, **VI editor**, **ping utility**, and **SSH client** — all mandatory for Ansible to function. 
    - Python: required as Ansible's runtime dependency
    - VI editor: required to edit configuration files inside the container
    - Ping: required to verify connectivity between containers
    - SSH client: required to SSH from master to targets
- Installed **software-properties-common** to enable adding external repositories. 
- Added the **Ansible PPA repository** (`ppa:ansible/ansible`) so Ubuntu recognizes the Ansible package. 
- Ran `apt install ansible`; Ansible 2.21 (approximately 28.1 MB) was installed and verified with `ansible --version`. 
- **Ansible configuration file** location confirmed: `/etc/ansible/ansible.cfg`; the host/inventory file is at `/etc/ansible/hosts`. 

### Target Machine Setup (Part 3)

- Exited master container, then entered `target1`: `docker exec -it target1 bash`. 
- Ran `apt update` and installed **Python**, **ping**, and **SSH client** on target1 — **Ansible was intentionally NOT installed** on target machines. 
- Navigated to `/etc/ssh/` and opened `sshd_config` to make two critical changes: 
    - **PermitRootLogin yes** — uncommented and set to `yes` to allow root login via SSH
    - **PasswordAuthentication yes** — uncommented and set to `yes` to allow password-based login
- Started the SSH service: `service ssh start`; verified it was running. 
- Set the root password to `admin` using `passwd root`. 
- Repeated the same target setup steps for `target2`. 

### IP Address Discovery and Host File Configuration (Parts 4–5)

- Exited target containers and returned to the base GCP VM. 
- Found container IP addresses using `docker inspect target1` and `docker inspect target2`; target1 received `172.17.0.3`, target2 received `172.17.0.4`. 
- Re-entered the Ansible master container and navigated to `/etc/ansible/`. 
- Opened the **hosts file** with `vi hosts`; all default content is commented out — added the target IP addresses in empty space and saved. 
- Verified connectivity by pinging target IP addresses from the master; ping confirmed working. 

### SSH Passwordless Authentication Setup (Part 6)

- Generated SSH key pair on the Ansible master using `ssh-keygen`; keys stored in `/root/.ssh/` (private key: `id_rsa`, public key: `id_rsa.pub`). 
- Copied the **public key** to target1 using `ssh-copy-id root@172.17.0.3`; accepted the fingerprint prompt and provided the `admin` password. 
- Verified passwordless SSH: `ssh root@172.17.0.3` connected without a password prompt. 
- Confirmed NGINX was not yet installed on target1 (returned "unrecognized service"). 

### Playbook Creation and Execution (Parts 7–9)

- Navigated to `/etc/ansible/` on the master and created a new YAML playbook file: `vi install_nginx.yml`. 
- Playbook structure explained: 
    - `---` : Three dashes indicate the start of a YAML file
    - `hosts: all` : Targets all IP addresses in the inventory file; can be scoped to a group name (e.g., `hosts: DB`, `hosts: CentOS`)
    - **Indentation** is critical in YAML — spaces define scope; no brackets used
    - `name:` : Human-readable task description
    - `apt:` module with `name: nginx` and `state: latest` : Installs the latest version of NGINX using apt
- Executed the playbook from the master: `ansible-playbook install_nginx.yml` 
    - Result: **2 changes** on target1 — apt cache updated (task 1) and NGINX installed (task 2)
    - Output showed `changed=2` confirming successful installation
- Students verified NGINX was installed on target1 by entering the container and checking `service nginx status`; NGINX was installed but not yet running (service not started). 

---

## Real-Time Issues and Troubleshooting

- **Unreachable error**: Occurs when SSH keys are not properly copied to target or SSH service is not running on the target; resolution — verify with ping, check SSH service status. 
- **Permission denied**: Occurs when `PermitRootLogin` or `PasswordAuthentication` is not enabled in `sshd_config`; resolution — manually verify SSH config changes on target. 
- **Port mismatch**: Ansible defaults to port 22; if the target uses a different port, all settings must be reconfigured to port 22. 
- **No hosts matched**: Occurs when the playbook's `hosts:` value does not match any group or entry in the inventory file. 
- **Second target unreachable**: When target2's IP (172.17.0.4) was added to the hosts file and the playbook re-run, target1 showed `ok=2 changed=0` (idempotent — NGINX already installed, no changes needed), while target2 showed `unreachable=1` because SSH was not yet configured on it. 

---

## YAML Playbook Structure Deep Dive

- Students were asked to write the playbook from memory (without looking) on paper to reinforce recall for interviews. 
- **`hosts: all`** executes on every IP in the inventory; replacing `all` with a group name (e.g., `DB`, `Ubuntu`, `CentOS`) scopes execution to that group only. 
- **Specific version installation**: Use `state: present` for default, or `state: 3.17` to pin a specific version. 
- **Service management**: To start/restart a service after installation, use the `service` module with `state: started` or `state: restarted`. 
- **Multiple playbooks** are supported; each can reference different host groups; all playbooks should be stored in `/etc/ansible/` and can also be maintained in GitHub. 
- Real-world use cases mentioned: Apache/HTTPD installation, Tomcat, Jenkins, PostgreSQL, user creation/deletion, backup scripts, patching. 

---

## Docker Image Commit and Push

- After completing the Ansible master setup, the container was committed as a reusable Docker image: `docker commit ansible_master vikas_cloud/ansible_master`. 
- Tagged and pushed to Docker Hub: `docker tag [image_id] vikas_cloud/ansible_master` → `docker push vikas_cloud/ansible_master`. 
- Same process performed for target1: `docker commit target1 vikas_cloud/target1` → pushed to Docker Hub. 
- **Purpose**: Students who could not complete the practical can `docker pull` the pre-configured image and resume from a known-good state without repeating all setup steps. 
- Images are public on Docker Hub, accessible to all batch members. 

---

## Terraform Drift — Concept and Resolution

- **Drift definition**: The gap between what is defined in Terraform code/state file and the actual state of infrastructure in the cloud console. 
- **Common causes**: 
    - Manual changes made directly via AWS/GCP console or CLI (e.g., upgrading T2 micro to T2 large during a 2AM incident)
    - External scripts or automation tools modifying resources outside Terraform
    - Cloud provider updates that rename or deprecate resource types (e.g., T2 micro decommissioned)
    - Multiple IaC tools in use simultaneously (e.g., Terraform + CloudFormation + Lambda)
    - Broad console/CLI access given to L1/L2 engineers
- **Detection**: Run `terraform plan`; it will highlight the difference between desired state (code) and actual state. 
- **Resolution options**: 
    - Update the Terraform code to match the manual change, then re-apply
    - Run `terraform refresh` to update the state file to reflect current real-world state
    - Implement weekly/monthly pipeline checks to detect drift proactively
- **Real-world scenario shared by student (Rajvaibhav)**: During high-priority incidents, teams sometimes bypass pipelines and make direct manual changes to avoid approval delays; after the incident, the code must be updated to match, and the pipeline plan will detect and flag the discrepancy. 

---

## Action Items and Recommendations for Students

- **Complete the full Ansible practical** independently — create 3 containers, install Ansible on master, configure SSH, build host file, write and execute a playbook; treat this as interview preparation. 
- **Write the YAML playbook from memory** on paper at least once; this is a common interview task. 
- **Be active on LinkedIn**: Post about today's practical, tag classmates and the instructor; certificate value is tied to visible LinkedIn activity — HR screens profiles, not just certificates. 
- **Module Expert task**: Write a new playbook (e.g., install Apache/Jenkins/PostgreSQL), post it on LinkedIn, and tag the instructor to receive a module expert certificate worth 500 rupees credit. 
- **Review Linux Day 3 session** for SSH key generation concepts used today; students unfamiliar with Docker basics were advised to review Docker class recordings before the next session. 
- **New students** (e.g., first-day attendees) were advised to start with Linux and Docker recordings before attempting Ansible practicals. 
- **GitHub repository** (Batch 44 folder) contains all commands used today; students should open the file, read before copy-pasting, and not blindly execute commands. 

---

## Open Questions and Clarifications

- **Can Ansible install Jenkins via playbook?** Yes — any software installable via apt/yum can be managed through Ansible playbooks; this is the core value of automation. 
- **Multiple host files**: Supported; pass additional host files at runtime via command flag; the default `/etc/ansible/hosts` is used when no file is specified. 
- **FQDN in host file**: Hostnames and fully qualified domain names can be used in the inventory file instead of raw IP addresses. 
- **Idempotency**: If a package is already installed, Ansible will not reinstall it; it checks current state against desired state and only acts on differences — this is the core idempotency principle. 
- **GCP vs. AWS for Ansible**: No difference in commands; both use Ubuntu 24.04, so all steps are identical regardless of cloud provider. 
- **GCP CLI authentication**: Uses service accounts; Google Cloud SDK (`gcloud`) must be installed locally and authenticated; differs from AWS access key/secret key model. 
- **Dockerfile vs. manual setup**: It is possible to encode setup steps in a Dockerfile to automate container creation; however, the manual approach was used today for learning purposes. 
