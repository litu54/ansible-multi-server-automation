# Multi-Server Automation Project using Ansible

## Project Overview

This project demonstrates infrastructure automation using Ansible across multiple Ubuntu servers hosted on AWS EC2.

The setup includes:

* A Control Node running Ansible
* ServerA hosting dynamic system information through Apache
* ServerB mirroring the content from ServerA using cron and serving it through a Dockerized NGINX container

---

# Architecture

## Control Node

* Ubuntu 24.04 EC2
* Runs Ansible playbooks

## ServerA

* Apache Web Server
* Hosts `/srv/website/sysinfo.html`
* Generates dynamic system information

## ServerB

* Mirrors `sysinfo.html` every 5 minutes using cron
* Serves mirrored content through Dockerized NGINX

---

# Technologies Used

* Ansible
* Apache2
* Docker
* NGINX
* Cron
* AWS EC2
* Ubuntu 24.04

---

# Project Structure

```bash
ansible-lab/
├── inventory/
│   └── hosts.ini
├── roles/
│   ├── serverA/
│   └── serverB/
├── site.yml
└── README.md
```

---

# Inventory Configuration

File: `inventory/hosts.ini`

```ini
[serverA]
serverA ansible_host=<SERVERA_PRIVATE_IP>

[serverB]
serverB ansible_host=<SERVERB_PRIVATE_IP>

[all:vars]
ansible_user=ansible
```

---

# Features Implemented

## ServerA

* Creates `webteam` group
* Creates `webadmin` user
* Creates custom website directory
* Generates dynamic system information page
* Installs and configures Apache
* Configures custom Apache DocumentRoot
* Configures MTU size

## ServerB

* Creates mirror directory
* Installs curl
* Configures cron job for automatic mirroring
* Installs Docker
* Deploys Dockerized NGINX container
* Serves mirrored content on port 8080

---

# Prerequisites

* Ubuntu 24.04 EC2 instances
* Ansible installed on Control Node
* Passwordless SSH configured
* Port 80 and 8080 opened in Security Groups

---

# Install Required Collection

```bash
ansible-galaxy collection install community.docker
```

---

# Run Playbook

```bash
ansible-playbook -i inventory/hosts.ini site.yml
```

---

# Validation

## ServerA

```text
http://<ServerA-Public-IP>/sysinfo.html
```

## ServerB

```text
http://<ServerB-Public-IP>:8080/sysinfo.html
```

---

# Troubleshooting Performed

During implementation, the following issues were identified and resolved:

* SSH authentication issues
* Sudo password prompts
* Apache 404 errors
* MTU interface mismatch (`eth0` vs `ens5`)
* Hostname resolution issues
* Docker dependency issues
* NGINX 404 errors
* AWS Security Group connectivity issues

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

