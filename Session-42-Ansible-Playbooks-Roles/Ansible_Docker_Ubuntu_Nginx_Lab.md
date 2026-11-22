# Ansible Installation and Nginx Deployment Using Docker Containers

## Lab overview

In this hands-on lab, you will create three Ubuntu containers, configure one as the **Ansible control node**, configure the other two as **managed nodes**, establish password-based SSH access initially, exchange SSH keys, and use an Ansible playbook to install Nginx on both target containers.

### Lab architecture

| Container | Role | Purpose |
|---|---|---|
| `ansible_master` | Ansible control node | Runs Ansible commands and playbooks |
| `target1` | Managed node 1 | Receives configuration from Ansible |
| `target2` | Managed node 2 | Receives configuration from Ansible |

**Important:** Docker commands such as `docker run`, `docker ps`, and `docker inspect` are executed on the Docker Playground node/host. Commands such as `apt`, `ssh-keygen`, and `ansible-playbook` are executed inside the relevant container.

The examples below use Docker containers for a quick lab. For production-like practice, use three Ubuntu virtual machines with reachable IP addresses and suitable network/security rules.

---

## Part 1: Create three Ubuntu containers

### Step 1: Open GCP / AWS account create a  ubuntu VM 

You will use this node as the Docker host. All three Ubuntu containers will run on it.

### Step 2: Install Docker on the VM and craete a node

Run these commands on the Docker Playground node:

```bash
sudo apt-get update && apt-get install docker.io -y
```

Check that Docker is available:

```bash
docker --version
```

If Docker is already installed and running, you can skip the installation.

### Step 3: Create the three Ubuntu containers

Run the following commands on the Docker host:

```bash
docker run -itd --name ansible_master ubuntu /bin/bash
docker run -itd --name target1 ubuntu /bin/bash
docker run -itd --name target2 ubuntu /bin/bash
```

What these options mean:

- `docker run`: creates and starts a container.
- `-i`: keeps standard input open i.e interactive mode.
- `-t`: allocates a terminal.
- `-d`: runs the container in the background.
- `--name`: assigns a readable name to the container.
- `ubuntu`: uses the Ubuntu image.
- `/bin/bash`: starts a Bash shell as the container's main process.

### Step 4: Verify the containers

```bash
docker ps
```

You should see all three containers in the running state. The names should be:

- `ansible_master`
- `target1`
- `target2`

If a container is not listed, check all containers (including stopped ones):

```bash
docker ps -a
```

---

## Part 2: Install Ansible and required tools on the control node

### Step 5: Enter the Ansible master container

Run this command on the Docker host:

```bash
docker exec -it ansible_master bash
```

Your terminal is now inside `ansible_master`. The remaining commands in this part must be run inside this container.

### Step 6: Update Ubuntu and install basic utilities

```bash
apt update
apt install -y python-is-python3 vim iputils-ping openssh-client software-properties-common
```

Why these packages are needed:

| Package | Purpose |
|---|---|
| `python-is-python3` | Makes the `python` command point to Python 3 on Ubuntu versions that provide this package |
| `vim` | Text editor for editing configuration files |
| `iputils-ping` | Provides the `ping` command for network checks |
| `openssh-client` | Provides SSH client tools, including `ssh` and `ssh-keygen` |
| `software-properties-common` | Provides `add-apt-repository` |

### Step 7: Add the Ansible repository

For Ubuntu versions where you want to use the Ansible project PPA, run:

```bash
add-apt-repository --yes --update ppa:ansible/ansible
```

If the command is unavailable or the PPA does not support your Ubuntu release, check the official Ansible installation guide for the supported installation method for that release.

### Step 8: Install Ansible

```bash
apt update
apt install -y ansible
```

Verify the installation:

```bash
ansible --version
```

You should see the installed Ansible version and its configuration details.

---

## Part 3: Configure target1 as an Ansible managed node

Ansible normally connects to Linux managed nodes over SSH. The target containers therefore need an SSH server and Python 3.

### Step 9: Enter target1

Open another terminal on the Docker host, or exit the master temporarily, and run:

```bash
docker exec -it target1 bash
```

You are now inside `target1`.

### Step 10: Update the package index and install required packages

```bash
apt update
apt install -y vim python-is-python3 iputils-ping openssh-client openssh-server
```

Package purpose:

- `openssh-server`: runs the SSH service so the control node can connect to this container.
- `openssh-client`: provides SSH client utilities if you need to test outbound SSH.
- `python-is-python3`: makes Python available through the `python` command on supported Ubuntu releases.
- `iputils-ping`: provides the `ping` utility.
- `vim`: allows you to edit configuration files.

### Step 11: Configure SSH to allow root and password login

For this training lab, we will allow root login and password authentication so that `ssh-copy-id` can install the control node's public key.

Open the SSH server configuration:

```bash
vi /etc/ssh/sshd_config
```

Find the following settings. They may be commented out or may not be present in the file:

```text
PermitRootLogin yes
PasswordAuthentication yes
```

Set both values to `yes`. If the settings are commented out with `#`, remove the `#`. Save and exit the editor.

**Security note:** Allowing root login and password authentication is only for this isolated practice lab. Do not use this configuration as-is on production servers. In production, prefer a non-root account with sudo privileges and SSH key authentication, and disable password-based SSH login where appropriate.

### Step 12: Set a root password

Run:

```bash
passwd root
```

When prompted, enter a lab password such as:

```text
Admin
```

Enter it again to confirm. The terminal does not display characters while you type the password; this is normal.

Use a stronger, unique password outside this temporary lab.

### Step 13: Start the SSH service

Check the service status:

```bash
service ssh status
```

Start it:

```bash
service ssh start
```

Check again:

```bash
service ssh status
```

If SSH does not start, inspect the configuration and logs. You can also validate the SSH server configuration with:

```bash
sshd -t
```

A successful validation normally produces no output.

---

## Part 4: Configure target2

Repeat the same target configuration for `target2`.

### Step 14: Enter target2 from the Docker host

```bash
docker exec -it target2 bash
```

### Step 15: Install dependencies

```bash
apt update
apt install -y vim python-is-python3 iputils-ping openssh-client openssh-server
```

### Step 16: Configure SSH

```bash
vi /etc/ssh/sshd_config
```

Set these values:

```text
PermitRootLogin yes
PasswordAuthentication yes
```

Save the file.

### Step 17: Set the root password and start SSH

```bash
passwd root
service ssh start
service ssh status
```

Set the same temporary lab password (`Admin`) if you want to follow the examples exactly. Repeat the SSH configuration and password steps on every additional managed container you create.

---

## Part 5: Find the IP addresses of the target containers

Ansible needs the target addresses in its inventory file.

### Step 18: Return to the Docker host

Exit the target container:

```bash
exit
```

Run these commands on the Docker host:

```bash
docker inspect target1
docker inspect target2
```

The output is lengthy. To print only the IP address, use:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target1
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' target2
```

Example output might look like:

```text
172.17.0.3
172.17.0.4
```

**Your IP addresses may be different.** Always use the addresses returned by your own Docker environment.

You can also inspect the network details with:

```bash
docker inspect target1
docker inspect target2
```

Look for `NetworkSettings` and then `IPAddress`.

---

## Part 6: Configure the Ansible inventory and test network reachability

### Step 19: Enter the Ansible master container

On the Docker host, run:

```bash
docker exec -it ansible_master bash
```

### Step 20: Open the Ansible inventory file

The default inventory file on many Ubuntu Ansible packages is:

```text
/etc/ansible/hosts
```

Check the directory:

```bash
ls -la /etc/ansible/
```

Edit the inventory:

```bash
vi /etc/ansible/hosts
```

Add the IP addresses you collected. Replace the example addresses with your actual container IPs:

```ini
[targets]
172.17.0.3
172.17.0.4
```

You can also use host aliases, which make playbooks and command output easier to read:

```ini
[targets]
target1 ansible_host=172.17.0.3
target2 ansible_host=172.17.0.4
```

Save the file.

**Note:** If `/etc/ansible/hosts` does not exist, create the directory and file:

```bash
mkdir -p /etc/ansible
vi /etc/ansible/hosts
```

### Step 21: Test basic IP connectivity

From inside `ansible_master`, ping each target:

```bash
ping -c 4 172.17.0.3
ping -c 4 172.17.0.4
```

Replace the example IPs with your actual target IP addresses.

If ping fails, check that all containers are running and connected to a network that allows communication. Ping is only a basic network test; successful ping does not by itself prove that SSH is working.

---

## Part 7: Configure SSH key-based connectivity

Ansible uses SSH to connect to Linux managed nodes. We will generate a key pair on the control node and copy its public key to both targets.

### Step 22: Generate an SSH key pair on ansible_master

Run this inside `ansible_master`:

```bash
ssh-keygen
```

When prompted:

1. Press **Enter** to accept the default key file location.
2. Press **Enter** to leave the passphrase empty for this disposable lab.
3. Press **Enter** again to confirm the empty passphrase.

This creates a private key and a public key, usually:

```text
/root/.ssh/id_rsa
/root/.ssh/id_rsa.pub
```

The private key must remain on the control node. Do not share it.

### Step 23: Copy the public key to target1

```bash
ssh-copy-id root@172.17.0.3
```

Replace the example IP with the actual IP of `target1`.

On the first connection, SSH may ask whether you trust the host. Type:

```text
yes
```

When prompted for the root password, enter the password you configured on `target1`.

### Step 24: Copy the public key to target2

```bash
ssh-copy-id root@172.17.0.4
```

Replace the example IP with the actual IP of `target2`. Accept the host key prompt with `yes` if shown, then enter the root password for `target2`.

### Step 25: Test SSH access to both targets

From `ansible_master`:

```bash
ssh root@172.17.0.3
```

If the connection works without asking for the account password, key-based authentication is configured. Exit the target session:

```bash
exit
```

Repeat for the second target:

```bash
ssh root@172.17.0.4
exit
```

You may be asked to confirm a host key the first time you connect. That is expected in a new lab.

### Step 26: Check whether Nginx is already installed

On each target, you can check:

```bash
service nginx status
```

If Nginx is not installed, the command may report that the service cannot be found. That is expected; the playbook in the next part will install it.

---

## Part 8: Create and run an Ansible playbook to install Nginx

### Step 27: Create the playbook

Make sure you are back inside `ansible_master`.

```bash
cd /etc/ansible
vi installnginx.yaml
```

Add the following playbook:

```yaml
---
- name: Install and start Nginx on all managed nodes
  hosts: targets
  become: true

  tasks:
    - name: Update the apt package cache
      ansible.builtin.apt:
        update_cache: true

    - name: Ensure Nginx is installed
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Ensure Nginx service is running
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

Save and exit.

What the playbook does:

- `---`: marks the beginning of a YAML document.
- `name`: gives the play or task a readable description.
- `hosts: targets`: runs the play against every host in the `targets` inventory group.
- `become: true`: uses privilege escalation. In this lab, the SSH user is root, so no additional privilege escalation is normally required; it is included to make the playbook suitable for a common non-root Ansible setup too.
- `ansible.builtin.apt`: uses Ansible's built-in apt module to manage Ubuntu packages.
- `update_cache: true`: refreshes the apt package index.
- `name: nginx`: identifies the package to install.
- `state: present`: ensures Nginx is installed. Use `latest` instead if you specifically want Ansible to upgrade it to the newest repository version.
- `ansible.builtin.service`: manages the Nginx service.
- `state: started`: ensures the service is running.
- `enabled: true`: enables the service to start automatically when the container/host boots.

### Step 28: Check the playbook syntax

```bash
ansible-playbook installnginx.yaml --syntax-check
```

If the YAML is valid, Ansible reports that the syntax is OK.

### Step 29: Test Ansible connectivity to all targets

Run:

```bash
ansible targets -m ping
```

Expected result: both targets should return `SUCCESS` and `pong`.

The Ansible `ping` module is not the same as the Linux `ping` command. It checks whether Ansible can connect over SSH and run its Python-based module on the managed node.

If you see an SSH authentication error, check the inventory IPs, SSH service status, root login settings, and whether `ssh-copy-id` completed successfully.

### Step 30: Execute the playbook

```bash
ansible-playbook installnginx.yaml
```

Ansible should connect to both targets, refresh the apt cache, install Nginx, and start and enable the service.

At the end, review the `PLAY RECAP`. Ideally, both hosts should show successful tasks and no failures.

### Step 31: Verify Nginx on target1 and target2

You can verify the service remotely from `ansible_master`:

```bash
ansible targets -m command -a "service nginx status"
```

You can also log in to each target and check manually:

```bash
ssh root@172.17.0.3
service nginx status
exit

ssh root@172.17.0.4
service nginx status
exit
```

Replace the sample IPs with your actual target IP addresses.

If the service is running, the Nginx installation and service-management tasks have completed successfully.

---

## Troubleshooting guide

### 1. `docker: command not found`

Docker is not installed on the Playground node, or the Docker command is not available in that terminal. Install Docker on the host and verify with:

```bash
docker --version
```

### 2. A container is not running

Check:

```bash
docker ps -a
```

Start a stopped container:

```bash
docker start ansible_master
docker start target1
docker start target2
```

### 3. SSH connection is refused

Check inside the target container:

```bash
service ssh status
service ssh start
```

Also confirm that `openssh-server` is installed:

```bash
dpkg -l openssh-server
```

### 4. SSH asks for a password every time

Run `ssh-copy-id` again from `ansible_master` and check that the public key is present on the target:

```bash
cat /root/.ssh/authorized_keys
```

Also confirm that the target's SSH configuration allows public-key authentication. The default `PubkeyAuthentication yes` setting is normally suitable.

### 5. Ansible reports `UNREACHABLE`

Check the inventory:

```bash
cat /etc/ansible/hosts
```

Test SSH manually:

```bash
ssh root@172.17.0.3
```

Then test Ansible again:

```bash
ansible targets -m ping -vvv
```

Use your actual target IP addresses. The `-vvv` option provides more connection details.

### 6. Ansible reports that Python is missing

Install Python 3 on the target:

```bash
apt update
apt install -y python3
```

Ansible needs Python on the managed node for most Linux modules.

### 7. YAML syntax or indentation error

YAML is indentation-sensitive. Use spaces, not tabs, and check the file with:

```bash
ansible-playbook installnginx.yaml --syntax-check
```

### 8. Nginx is installed but not running

Check the service:

```bash
service nginx status
```

Try starting it:

```bash
service nginx start
```

For more details, inspect:

```bash
journalctl -u nginx --no-pager
```

---

## Useful commands recap

| Task | Command |
|---|---|
| List running containers | `docker ps` |
| Enter a container | `docker exec -it <container_name> bash` |
| Get a container IP | `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name>` |
| Check Ansible version | `ansible --version` |
| View inventory | `cat /etc/ansible/hosts` |
| Test network reachability | `ping -c 4 <target_ip>` |
| Generate SSH key | `ssh-keygen` |
| Copy public key | `ssh-copy-id root@<target_ip>` |
| Test SSH | `ssh root@<target_ip>` |
| Test Ansible connectivity | `ansible targets -m ping` |
| Check playbook syntax | `ansible-playbook installnginx.yaml --syntax-check` |
| Run playbook | `ansible-playbook installnginx.yaml` |
| Verify Nginx remotely | `ansible targets -m command -a "service nginx status"` |

---

## Lab completion checklist

- [ ] Created three Ubuntu containers.
- [ ] Installed Ansible on `ansible_master`.
- [ ] Installed Python 3 and SSH server on `target1` and `target2`.
- [ ] Started the SSH service on both targets.
- [ ] Collected both target IP addresses.
- [ ] Added both targets to `/etc/ansible/hosts`.
- [ ] Generated an SSH key pair on `ansible_master`.
- [ ] Copied the public key to both targets.
- [ ] Confirmed passwordless SSH connectivity.
- [ ] Confirmed `ansible targets -m ping` works.
- [ ] Created `installnginx.yaml`.
- [ ] Passed the playbook syntax check.
- [ ] Ran the playbook successfully.
- [ ] Verified that Nginx is installed and running on both targets.

## What you learned

By completing this lab, you practised:

1. Creating and managing Ubuntu Docker containers.
2. Understanding the Ansible control node and managed nodes.
3. Installing Ansible and its required tools.
4. Configuring SSH access on Linux systems.
5. Setting up SSH key-based authentication.
6. Creating an Ansible inventory.
7. Testing managed-node connectivity using the Ansible ping module.
8. Writing and executing an Ansible YAML playbook.
9. Automating Nginx installation and service management across multiple machines.

**Production reminder:** This lab deliberately uses root SSH login and a simple password to make the first-time setup easier. For real infrastructure, use a dedicated automation account, SSH keys, least-privilege sudo access, secure secret management, and appropriate network restrictions.
