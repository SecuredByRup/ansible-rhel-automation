# Module 3: Introduction to Ansible Modules

This repository contains Ansible playbooks demonstrating fundamental automation tasks, including cross-platform package management, system service administration, conditional logic, dynamic web deployment, and error handling.

---

## 🛠️ Environment Setup

- **Control Node / Target**: CentOS Linux
- **Inventory File**: `/etc/ansible/hosts` configured to route tasks locally:

```ini
[centos_nodes]
localhost ansible_connection=local

[ubuntu_nodes]
localhost ansible_connection=local

📁 Repository Structure
lab4/
├── package-management.yml   # YUM package management tasks
├── universal-packages.yml   # OS-agnostic conditionals (YUM vs APT)
├── service-management.yml   # Web service configuration (httpd/apache2)
├── advanced-services.yml    # Service management for SSH & Firewalld
├── infrastructure-setup.yml # Complete infrastructure setup with handlers
└── error-handling.yml       # Resiliency testing (ignore_errors, retries)

🚀 Playbook Execution & Key Features
1. Verification & Ping Test
Ensured local control node connectivity:
ansible all -m ping

2. YUM Package Management (`package-management.yml`)
Managed system utilities (git, wget, curl, vim) and package removal using the yum module.
ansible-playbook package-management.yml -K

3. Universal Cross-Platform Management (`universal-packages.yml`)
Used Ansible conditionals (when: ansible_os_family == "RedHat") to dynamically execute OS-specific tasks without crashing on mixed host types.

4. Service Management (`service-management.yml` & `advanced-services.yml`)
Automated web server (`httpd`) and firewall (`firewalld`) setup, state validation, and runtime variable registration.
ansible-playbook service-management.yml -K

5. Master Infrastructure Deployment (`infrastructure-setup.yml`)
Deployed an end-to-end web server stack, created a custom HTML landing page using gathered facts (ansible_os_family, ansible_distribution), and triggered service handlers upon file modifications.
ansible-playbook infrastructure-setup.yml -K

Verify deployment:
curl http://localhost

HTML
<html>
<head><title>Ansible Managed Server</title></head>
<body>
<h1>Welcome to localhost</h1>
<p>This server is managed by Ansible</p>
<p>OS Family: RedHat</p>
<p>Distribution: CentOS</p>
</body>
</html>


