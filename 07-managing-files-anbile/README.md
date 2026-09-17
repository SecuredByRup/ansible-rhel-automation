Lab 10: Managing Files with Ansible

A comprehensive guide and repository reference for deploying static assets, rendering dynamic Jinja2 configuration templates, and handling advanced file permissions using Ansible on Red Hat Enterprise Linux / CentOS systems.
📋 Overview

This lab demonstrates essential file management techniques in Ansible. It covers copying static files with explicit permissions and backups, using Jinja2 templates to generate configuration files with system facts and custom variables, and enforcing directory attributes across target web servers.
🛠️ Prerequisites & Setup

    Control / Target Node: CentOS 9 Stream / RHEL 9 (Executing via localhost)

    Ansible Core: v2.14+ installed

    Required Package: httpd (Apache Web Server)

Local Workspace Setup
Bash

mkdir -p ~/ansible-labs/lab10-file-management/static-files
cd ~/ansible-labs/lab10-file-management

📁 Repository Structure
Plaintext

lab10-file-management/
├── group_vars/
│   └── webservers.yml           # Global variables for webserver group
├── static-files/
│   ├── apache-security.conf     # Static Apache security directives
│   └── favicon.ico              # Static binary asset placeholder
├── templates/
│   ├── conditional-template.j2  # Jinja2 template with conditional logic
│   ├── index.html.j2            # Dynamic HTML page template
│   ├── system-info.conf.j2      # System report template consuming facts
│   └── vhost.conf.j2            # Dynamic Virtual Host config template
├── advanced-file-management.yml # Advanced file, loop, and permission operations
├── copy-static-files.yml        # Static file deployment playbook
├── deploy-templates.yml         # Dynamic template deployment playbook
├── inventory.ini                # Ansible inventory file
└── test-file-management.yml     # Verification test suite playbook

🚀 Playbook Implementations
Task 1: Copying Static Files (copy-static-files.yml)

Deploys static Apache security configurations and static web root assets. Automatically creates configuration backups when destination files are modified.
Bash

ansible-playbook -i inventory.ini copy-static-files.yml -K

Task 2: Jinja2 Configuration Templates (deploy-templates.yml)

Renders dynamic Virtual Host parameters and host-specific system reports (/etc/system-info.conf) using system facts (IP addresses, memory, CPU cores) and group variables.
Bash

ansible-playbook -i inventory.ini deploy-templates.yml -K

Task 3: Advanced File Management (advanced-file-management.yml)

Handles multi-directory creation loops, inline dynamic file content generation via the copy module, explicit file permissions management, and ownership mapping (root / apache).
Bash

ansible-playbook -i inventory.ini advanced-file-management.yml -K

📊 Verification Commands

Verify the generated configurations and directory attributes:
Bash

# 1. Inspect rendered Virtual Host configuration
cat /etc/httpd/conf.d/localhost.conf

# 2. Inspect rendered system metrics configuration
cat /etc/system-info.conf

# 3. Verify target directory permissions and ownerships
ls -la /opt/app/config/ /opt/app/static/

🎯 Key Concepts Mastered

    Ansible Idempotence: Understanding the difference between state modifications (changed) and pre-validated configuration states (ok).

    Jinja2 Templating Engine: Variable substitution, default filters (default()), conditional statements ({% if %}), and loop structures ({% for %}).

    File Attributes & Security: Defining explicit file modes (0644, 0755), ownership properties (owner, group), and creating system backups (backup: yes).
