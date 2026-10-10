# Module 12: Automated Web Server Deployment via Ansible

![Ansible](https://img.shields.io/badge/Ansible-E25939?style=for-the-badge&logo=ansible&logoColor=white)
![RHEL/CentOS](https://img.shields.io/badge/CentOS_Stream-262577?style=for-the-badge&logo=centos&logoColor=white)
![Apache HTTP Server](https://img.shields.io/badge/Apache-D22128?style=for-the-badge&logo=Apache&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

An enterprise-grade, modular Ansible automation solution designed to provision, configure, secure, and deploy a complete Apache (`httpd`) web infrastructure on RHEL/CentOS systems.

---

## Project Architecture & Directory Structure

```text
ansible-webserver-lab/
├── ansible.cfg             # Global Ansible configuration settings
├── inventory/
│   └── hosts.ini           # Inventory file defining target host groups
├── playbooks/
│   ├── install-apache.yml     # Installs and configures Apache HTTP Server
│   ├── configure-firewall.yml # Provisions firewalld rules for HTTP/HTTPS
│   └── deploy-website.yml     # Deploys dynamic templates and static assets
├── templates/
│   └── index.html.j2        # Dynamic Jinja2 template with system facts
├── files/
│   └── style.css            # Custom static stylesheet
├── assets/                  # Verification screenshots and documentation
└── site.yml                 # Master orchestration playbook
```

## Key Features

* **Modular Architecture**: Playbooks are divided by task scope (installation, firewall security, application deployment) for reusability.
* **Dynamic Content Rendering**: Leverages Jinja2 templating (`index.html.j2`) to automatically inject target host facts (hostname, IP address, OS version, deployment timestamp).
* **Network & Firewall Security**: Manages system `firewalld` configurations to open HTTP (`80/tcp`) and HTTPS (`443/tcp`) ports persistently.
* **Idempotent Execution**: Designed to ensure repeated play executions maintain system state without unintended side effects.
* **Automated Health Verification**: Integrates direct HTTP `uri` module checks to validate application availability post-deployment.

---

## Execution & Deployment Guide

### 1. Prerequisites
Ensure Ansible is installed on your control node and SSH access is configured for managed nodes:
```bash
# Verify Ansible installation
ansible --version
```

### 2. Verify Host Connectivity
Validate control node communication with inventory targets:
```bash
ansible -i inventory/hosts.ini all -m ping
```

### 3. Deploy via Master Playbook
Execute the entire provisioning pipeline using the unified master playbook:
```bash
ansible-playbook -i inventory/hosts.ini site.yml --ask-become-pass
```

---

## Component Playbooks

* **Web Server Installation (`playbooks/install-apache.yml`)**: Installs `httpd`, creates custom configuration directives, establishes necessary runtime directories, and ensures service startup.
* **Network Security (`playbooks/configure-firewall.yml`)**: Ensures `firewalld` is running and permanently permits HTTP and HTTPS traffic.
* **Web Content Deployment (`playbooks/deploy-website.yml`)**: Deploys dynamic Jinja2 web templates, static CSS assets, additional web pages, and runs automated HTTP health checks.

---

## Execution Proofs & Verification

### 1. Project Setup & Ping Test
![Project Setup and Ping](assets/01-project-setup-and-ping.png)  
*Proof: Shows project initialization, directory creation, inventory setup, and successful Ansible ping test.*

### 2. Apache Installation Playbook Execution
![Apache Installation Playbook](assets/02-apache-installation-playbook.png)  
*Proof: Successful execution of `install-apache.yml` with `failed=0`.*

### 3. Website Deployment Playbook Execution
![Website Deployment Playbook](assets/03-website-deployment-playbook.png)  
*Proof: Successful execution of `deploy-website.yml` deploying Jinja2 templates, static CSS, and verifying HTTP response.*

### 4. Firewall Configuration Playbook Execution
![Firewall Configuration Playbook](assets/04-firewall-configuration-playbook.png)  
*Proof: Execution of `configure-firewall.yml` configuring `firewalld` to allow HTTP/HTTPS traffic.*

### 5. Master Playbook Execution
![Master Playbook Execution](assets/05-master-playbook-execution.png)  
*Proof: End-to-end master playbook (`site.yml`) execution.*

### 6. HTTP Verification via cURL
![HTTP Verification cURL](assets/06-http-verification-curl.png)  
*Proof: Local HTTP validation using `curl -I http://localhost` returning `HTTP/1.1 200 OK` and rendered Jinja2 HTML output.*
