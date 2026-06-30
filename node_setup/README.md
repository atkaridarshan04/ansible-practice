# 🧪 Ansible Lab Nodes Setup

---

This guide provides a **containerized Ansible lab environment** using Docker containers as managed Linux hosts. It offers a lightweight, fast alternative to virtual machines.

The setup is **security-hardened**:

- ❌ Root login disabled
- ❌ Password authentication disabled
- ✅ SSH key-based login only
- ✅ Non-root user for automation

Each container gets a **static IP** via a custom bridge network, ensuring a stable Ansible inventory.

---

## 📋 1. Prerequisites

- Docker & Docker Compose installed
- Ansible installed on your host machine

---

## ⚙️ 2. Setup & Installation

### 🔑 Step 1: Prepare Your SSH Key

Docker cannot access files outside the project directory, so copy your public key:

```bash
# Navigate to project folder
cd test/ansible_learn/node

# Copy your public SSH key
cp ~/.ssh/id_rsa.pub ./id_rsa.pub
```

---

### 📁 Step 2: Configuration Files

### 🐳 `Dockerfile`

Builds the managed nodes with:

- `ansible_user`
- Passwordless sudo
- SSH hardening

```docker
FROM ubuntu:22.04

# Install SSH, Python, and Sudo
RUN apt-get update && apt-get install -y openssh-server python3 sudo

# 1. Create 'ansible_user' and grant passwordless sudo
RUN useradd -m -s /bin/bash ansible_user && \\
    echo "ansible_user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# 2. Hardening: Disable Root Login and Password Auth
RUN mkdir -p /run/sshd && \\
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin no/' /etc/ssh/sshd_config && \\
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# 3. Set up SSH directory and copy the key
RUN mkdir -p /home/ansible_user/.ssh && \\
    chmod 700 /home/ansible_user/.ssh

# Copy SSH public key
COPY id_rsa.pub /home/ansible_user/.ssh/authorized_keys

# 4. Fix ownership and permissions
RUN chown -R ansible_user:ansible_user /home/ansible_user/.ssh && \\
    chmod 600 /home/ansible_user/.ssh/authorized_keys

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

---

### 🧩 `docker-compose.yml`

Defines two nodes with static IPs:

```yaml
services:
  node1:
    build: .
    container_name: ansible_node_1
    networks:
      ansible_net:
        ipv4_address: 172.20.0.11

  node2:
    build: .
    container_name: ansible_node_2
    networks:
      ansible_net:
        ipv4_address: 172.20.0.12

networks:
  ansible_net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

---

## 🚀 3. Running the Lab

### ▶️ Build and Start Containers

```bash
docker-compose up -d --build
```

---

### ⚠️ Fix Host Key Warnings (if containers are rebuilt)

```bash
ssh-keygen -R 172.20.0.11
ssh-keygen -R 172.20.0.12
```

### ⚠️ The "Host Identification" Panic

**Error:** `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`

**Why it happens:**
Each SSH server has a unique fingerprint stored in `~/.ssh/known_hosts`.
When containers are rebuilt, new SSH keys are generated.

Your system detects:

- Same IP (`172.20.0.12`)
- Different fingerprint

➡️ Interprets it as a possible MITM (Man in the middle) attack.

**Fix:**
Remove old keys using:

```bash
ssh-keygen -R <IP>
```

**Note:**
In labs, we disable checking:

```
host_key_checking = False
```

⚠️ Never disable this in production.

---

### 🔍 Verify SSH Connection

```bash
ssh ansible_user@172.20.0.11
```

---

## 🛠️ 4. Ansible Configuration

### 📄 `ansible.cfg`

```
[defaults]
inventory = inventory.ini
host_key_checking = False
```

---

### 📄 `inventory.ini`

```
[nodes]
172.20.0.11
172.20.0.12

[nodes:vars]
ansible_user=ansible_user
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
```

---

### ✅ Test with Ansible

```bash
ansible nodes -m ping
```

---
