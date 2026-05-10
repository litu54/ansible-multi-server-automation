# Multi-Server Automation Project using Ansible

# Project Overview

This project demonstrates infrastructure automation using Ansible across multiple Ubuntu servers hosted on AWS EC2.

The setup includes:

* A Control Node running Ansible
* ServerA hosting dynamic system information through Apache
* ServerB mirroring the content from ServerA using cron and serving it through a Dockerized NGINX container

---

# Initial Environment Setup

Three Ubuntu 24.04 AWS EC2 instances were created for this project:

* Control Node
* ServerA
* ServerB

A common `ansible` user was created across all servers to standardize automation and SSH access.

Passwordless SSH authentication was configured by generating SSH keys on the Control Node and exchanging public keys with the managed nodes.

This enabled secure Ansible-based communication between the Control Node and managed servers.

---

# Architecture

## Control Node

* Ubuntu 24.04 EC2
* Ansible installed
* Executes playbooks against managed nodes

## ServerA

* Apache Web Server
* Hosts `/srv/website/sysinfo.html`
* Generates dynamic system information

## ServerB

* Mirrors `sysinfo.html` every 5 minutes using cron
* Hosts mirrored content using Dockerized NGINX

---

# Control Node Execution

All Ansible playbooks were executed from the Ansible Control Node.

The Control Node manages:

* ServerA
* ServerB

using SSH-based communication and inventory-driven automation.

---

# AWS EC2 Setup

Created 3 Ubuntu 24.04 EC2 Instances:

| Server       | Purpose               |
| ------------ | --------------------- |
| Control Node | Runs Ansible          |
| ServerA      | Apache Server         |
| ServerB      | Docker + NGINX Mirror |

---

# Security Group Configuration

## Opened Ports

| Port | Purpose                |
| ---- | ---------------------- |
| 22   | SSH                    |
| 80   | Apache Access          |
| 8080 | NGINX Container Access |

---

# Step 1 - Install Ansible on Control Node

```bash id="s1"
sudo apt update
sudo apt install ansible -y
```

Verify:

```bash id="s2"
ansible --version
```

---

# Step 2 - Create Common Ansible User on All Servers

Performed on:

* Control Node
* ServerA
* ServerB

## Create User

```bash id="s3"
sudo useradd -m ansible
sudo passwd ansible
sudo usermod -aG sudo ansible
```

---

# Step 3 - Configure Passwordless Sudo

Edit sudoers:

```bash id="s4"
sudo visudo
```

Add:

```bash id="s5"
ansible ALL=(ALL) NOPASSWD: ALL
```

---

# Step 4 - Generate SSH Key on Control Node

Switch to ansible user:

```bash id="s6"
su - ansible
```

Generate key:

```bash id="s7"
ssh-keygen
```

---

# Step 5 - Copy SSH Key to Managed Nodes

From Control Node:

```bash id="s8"
ssh-copy-id ansible@<ServerA-Private-IP>
ssh-copy-id ansible@<ServerB-Private-IP>
```

---

# Step 6 - Validate SSH Connectivity

```bash id="s9"
ssh ansible@<ServerA-Private-IP>
ssh ansible@<ServerB-Private-IP>
```

---

# Step 7 - Create Ansible Project Structure

```bash id="s10"
mkdir -p ~/ansible-lab
cd ~/ansible-lab
```

Create roles:

```bash id="s11"
ansible-galaxy init roles/serverA
ansible-galaxy init roles/serverB
```

---

# Step 8 - Create Inventory File

File:

```bash id="s12"
inventory/hosts.ini
```

Content:

```ini id="s13"
[serverA]
serverA ansible_host=<SERVERA_PRIVATE_IP>

[serverB]
serverB ansible_host=<SERVERB_PRIVATE_IP>

[all:vars]
ansible_user=ansible
```

---

# Step 9 - Create Main Playbook

File:

```bash id="s14"
site.yml
```

Content:

```yaml id="s15"
---
- name: Configure ServerA
  hosts: serverA
  become: yes
  roles:
    - serverA

- name: Configure ServerB
  hosts: serverB
  become: yes
  roles:
    - serverB
```

---

# Step 10 - Install Required Collection

On Control Node:

```bash id="s16"
ansible-galaxy collection install community.docker
```

---

# Step 11 - Run Playbook

```bash id="s17"
cd ~/ansible-lab

ansible-playbook -i inventory/hosts.ini site.yml
```

---

# Validation

## ServerA

```text id="s18"
http://<ServerA-Public-IP>/sysinfo.html
```

## ServerB

```text id="s19"
http://<ServerB-Public-IP>:8080/sysinfo.html
```

---

# Troubleshooting Performed

## SSH Issues

* Configured passwordless authentication
* Added SSH public keys

## Sudo Password Prompt

* Added NOPASSWD entry in sudoers

## Apache 404 Error

* Fixed Apache DocumentRoot configuration
* Reloaded Apache service

## MTU Failure

* Corrected network interface name from eth0 to ens5

## NGINX 404 Error

* Mirrored file was missing initially
* Fixed cron job and hostname resolution

## Docker Issues

* Installed community.docker collection
* Installed python3-docker package

## Connectivity Issue

* Opened port 80 in AWS Security Group

---

# Final Outcome

Successfully automated:

* Apache-based system information hosting on ServerA
* Scheduled mirroring of content on ServerB
* Dockerized NGINX serving mirrored content
* End-to-end automation using Ansible roles, templates, handlers, and inventory-based management

---

# GitHub Repository

https://github.com/litu54/ansible-multi-server-automation

