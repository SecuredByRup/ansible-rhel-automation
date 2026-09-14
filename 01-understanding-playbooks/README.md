**Module 01: Playbook Architecture & Error Handling**

**Overview**
This module demonstrates the design, deployment, and verification of Ansible Playbooks on CentOS/RHEL nodes. It covers structured playbook organization, Jinja2 templating, handler management, variable scoping, and advanced error handling (`block`, `rescue`, `always`).

**Directory Structure**
├── enhanced-playbook.yml
├── error-handling-playbook.yml
├── files/
├── group_vars/
├── host_vars/
├── install-package.yml
├── inventory.ini
├── roles/
├── templates/
│   └── index.html.j2
├── variable-playbook.yml
├── vars.yml
└── verify-setup.yml

**Host Inventory (inventory.ini)**
[managed_nodes]
node1 ansible_host=192.168.1.10 ansible_user=ansible
node2 ansible_host=192.168.1.11 ansible_user=ansible

[web_servers]
node1

[database_servers]
node2

[all:vars]
ansible_ssh_private_key_file=/home/ansible/.ssh/id_rsa
ansible_ssh_common_args='-o StrictHostKeyChecking=no'

**Connectivity Verification**
ansible managed_nodes -m ping -i inventory.ini

**Playbook Executions & Outcomes**
Web Infrastructure Deployment (enhanced-playbook.yml): Deploys httpd and firewalld, provisions dynamic index content using Jinja2 templates (index.html.j2), and reloads services using triggered handlers.

Error Handling & Resilience (error-handling-playbook.yml): Tests playbook recovery against non-existent packages and services using ignore_errors alongside block/rescue/always logic.

Externalized Variables (variable-playbook.yml): Demonstrates variable separation by loading external definitions (vars.yml) and conditionally assigning software setups based on host groups.

**Deployment Verification**
Validating web server execution and Jinja2 dynamic template rendering via local HTTP requests:

Bash
curl [http://192.168.1.10](http://192.168.1.10)
