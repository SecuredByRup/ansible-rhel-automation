# Module 14: Multi-Tier Application Orchestration with Ansible

![Ansible](https://img.shields.io/badge/Ansible-E25939?style=for-the-badge&logo=ansible&logoColor=white)
![MariaDB](https://img.shields.io/badge/MariaDB-003545?style=for-the-badge&logo=mariadb&logoColor=white)
![Apache](https://img.shields.io/badge/Apache-D22128?style=for-the-badge&logo=Apache&logoColor=white)
![HAProxy](https://img.shields.io/badge/HAProxy-6366F1?style=for-the-badge&logo=haproxy&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

An automated Ansible orchestration solution that deploys, configures, and validates a multi-tier web application stack on Enterprise Linux (RHEL/CentOS). The architecture includes a **MariaDB database**, an **Apache/PHP web application layer**, an **HAProxy load balancer**, and automated **health-monitoring scripts**.

---

## Architecture Overview

The single-node setup maps all tier components locally using `ansible_connection=local` with strict port separation and SELinux compliance.

```text
┌────────────────────────┐
│ HTTP Client /          │
│ Monitoring Agent       │
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│ HAProxy (0.0.0.0:80)   │ ◄── Stats Dashboard: :8404
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│ Apache HTTPD           │
│ (127.0.0.1:8080)       │
└───────────┬────────────┘
            │
            ▼
┌────────────────────────┐
│ MariaDB Database       │
│ (127.0.0.1:3306)       │
└────────────────────────┘
```

---

## Repository Structure

```text
ansible-orchestration/
├── group_vars/
│   └── all.yml               # Global variables (ports, application metadata)
├── inventory/
│   └── hosts                 # Target inventory definitions mapping hosts to local execution
├── playbooks/
│   ├── database.yml          # Phase 1: MariaDB installation and schema setup
│   ├── webservers.yml        # Phase 2: Apache, PHP runtime, and web app configuration
│   ├── loadbalancer.yml      # Phase 3: HAProxy installation and frontend/backend binding
│   ├── monitoring.yml        # Phase 4 & 5: System status cron job and stack verification
│   ├── site.yml              # Master orchestration entry point importing all phase playbooks
│   └── rollback.yml          # Rollback playbook for service teardown
└── templates/
    ├── app_config.php.j2     # PHP database connectivity configuration template
    ├── webapp.conf.j2        # Apache VirtualHost template binding to port 8080
    └── haproxy.cfg.j2        # HAProxy load balancer configuration template
```

---

## Key Engineering Decisions & Problem Solving

During local single-node adaptation, several environment conflicts were resolved:

* **Port Separation Strategy**: Apache default `Listen 80` was re-configured to bind exclusively to port `8080` via `templates/webapp.conf.j2`. HAProxy binds to port `80` as the public ingress load balancer and port `8404` for administrative telemetry metrics.
* **SELinux & UNIX Socket Handling**: Addressed cannot bind UNIX socket (`Permission denied`) issues on CentOS under SELinux by transitioning HAProxy management to TCP stats on port `8404` and enabling the `haproxy_connect_any` boolean.
* **Variable Scope & Privilege Management**: Explicitly scoped database connectivity parameters (`db_name`, `db_user`) across imported playbooks. Enabled privilege escalation (`become: yes`) on stack validation tasks to allow local socket connectivity tests without permission failures.

---

## Execution Guide

### Prerequisites
* RHEL / CentOS Linux host
* Ansible 2.9+ with `community.mysql` collection installed
* Root/sudo permissions on target node

### 1. Clone Repository & Setup
```bash
git clone https://github.com/YOUR_USERNAME/ansible-orchestration.git
cd ansible-orchestration
```

### 2. Run Main Deployment Pipeline
Execute the master orchestration playbook using the inventory configuration:
```bash
ansible-playbook -i inventory/hosts playbooks/site.yml --ask-become-pass
```

### 3. Verify Active Services
Confirm all services are running:
```bash
systemctl is-active mariadb httpd haproxy
```

---

## Deployment & Validation Proof

### 1. Master Playbook Completion (`site.yml`)
The orchestration playbook completes all phases cleanly with `failed=0`.
![Master Playbook Recap](assets/01-master-playbook-recap.png)  
*Proof: Ansible Playbook Recap completed cleanly with `ok=34`, `changed=0`, and `failed=0`.*

### 2. End-to-End HTTP Request Verification
Querying the load balancer endpoint on port 80 successfully returns the rendered PHP application response sourced from Apache and connected to MariaDB.
![Services and Routing](assets/02-services-and-routing.png)  
*Proof: All three services (`mariadb`, `httpd`, `haproxy`) are active, and the `curl http://127.0.0.1/webapp/` test successfully routed through HAProxy on port 80.*

### 3. HAProxy Administrative Dashboard
HAProxy metrics dashboard accessible on port 8404 showing active backend health.
![HAProxy Dashboard](assets/03-haproxy-dashboard.png)  
*Proof: The stats dashboard at `http://127.0.0.1:8404/stats` shows green `UP` status across the `webapp_frontend` and `webapp_servers` backends.*

---

## Teardown / Rollback
To perform a stack teardown or rollback changes:
```bash
ansible-playbook -i inventory/hosts playbooks/rollback.yml --ask-become-pass
```
