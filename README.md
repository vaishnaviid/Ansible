# Ansible Setup on Amazon Linux VMs

This guide covers how to set up Ansible on Amazon Linux EC2 instances to automate configuration management.

---

## 🧱 Step 0: Create Amazon Linux VMs

- Launch **3 EC2 Instances** (Amazon Linux 2, t2.micro, Free Tier eligible):
  - **1 Control Node** (Ansible will be installed here)
  - **2 Managed Nodes** (will be managed by the control node)
- Connect to all instances using **MobaXterm** or your preferred SSH client.

---

## ⚙️ Step 1: Setup User & Configure Access (Run on All 3 VMs)

```bash
# a) Create a new user
sudo useradd ansible
sudo passwd ansible

# b) Add user to sudoers
sudo visudo
# Add this line:
ansible ALL=(ALL) NOPASSWD: ALL

# c) Update SSHD config
sudo vi /etc/ssh/sshd_config
# Comment this line:
# PasswordAuthentication no
# And add:
PermitEmptyPasswords yes

# d) Restart SSH service
sudo service sshd restart
```

> 🔁 Perform the above steps on all 3 instances.

---

## 🛠️ Step 2: Install Ansible (On Control Node Only)

```bash
# a) Switch to ansible user
sudo su - ansible

# b) Install Python
sudo yum install python3 -y

# c) Verify Python version
python3 --version

# d) Install PIP
sudo yum install python3-pip -y

# e) Install Ansible using PIP
pip3 install ansible --user

# f) Check Ansible version
~/.local/bin/ansible --version

# g) Create Ansible directory
sudo mkdir -p /etc/ansible
```

---

## 🔐 Step 3: SSH Key Setup (Control Node ➝ Managed Nodes)

```bash
# a) Switch to ansible user
sudo su - ansible

# b) Generate SSH Key
ssh-keygen

# c) Copy public key to Managed Nodes
ssh-copy-id ansible@<ManagedNode-Private-IP>

# Example:
ssh-copy-id ansible@172.31.44.90
ssh-copy-id ansible@172.31.47.247
```

---

## 📋 Step 4: Configure Ansible Inventory File

```bash
sudo vi /etc/ansible/hosts
```

**Add the following content:**

```ini
[webservers]
172.31.47.247

[dbservers]
172.31.44.90
```

---

## 🧪 Step 5: Test Ansible Connectivity

```bash
ansible all -m ping
```

Expected output:

```
172.31.44.90 | SUCCESS => {...}
172.31.47.247 | SUCCESS => {...}
```

---

## 📌 Notes

- Make sure all servers can ping each other (security group rules must allow SSH access between them).
- Use private IPs in your SSH and inventory configurations.
- Ensure the `ansible` user is created and has correct permissions on all nodes.

---

## ✅ You're Now Ready!

You’ve successfully set up Ansible with Amazon Linux EC2 instances. You can now start writing **Playbooks** and managing configuration at scale.
