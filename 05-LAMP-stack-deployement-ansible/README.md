# Module 5: End-to-End LAMP Stack Deployment with Ansible

A modular, production-ready Ansible automation suite that provisions a fully functional LAMP (Linux, Apache, MariaDB, PHP) stack on Red Hat Enterprise Linux (RHEL) and CentOS Stream 9 environments.

---
https://github.com/SecuredByRup/ansible-rhel-automation/tree/main/05-LAMP-stack-deployement-ansible
## Architecture Overview

This project uses a multi-tier play architecture separating the Web Tier and Database Tier while maintaining a unified variable scope.

```text
               ┌──────────────────────────────┐
               │    Ansible Control Node      │
               └──────────────┬───────────────┘
                              │
                    ansible-playbook
                              │
               ┌──────────────┴───────────────┐
               │    Target Machine (Target)   │
               └──────────────┬───────────────┘
                              │
        ┌─────────────────────┴─────────────────────┐
        ▼                                           ▼
┌───────────────┐                           ┌───────────────┐
│   Web Tier    │                           │ Database Tier │
│ Apache + PHP  │ ── MySQL Socket / Port ──>│  MariaDB 10.x │
└───────────────┘                           └───────────────┘

ansible-lab8/
├── group_vars/
│   └── all.yml                # Global infrastructure variables
├── roles/
│   ├── common/                # Base system configuration & packages
│   │   ├── defaults/main.yml
│   │   └── tasks/main.yml
│   ├── database/              # MariaDB installation & database setup
│   │   ├── defaults/main.yml
│   │   ├── handlers/main.yml
│   │   └── tasks/main.yml
│   ├── webserver/             # Apache & PHP stack configuration
│   │   ├── defaults/main.yml
│   │   ├── handlers/main.yml
│   │   └── tasks/main.yml
│   └── webapp/                # Web application deployment & DB connectivity
│       ├── defaults/main.yml
│       ├── templates/
│       │   └── index.php.j2
│       └── tasks/main.yml
├── complete-deployment.yml    # Master orchestrator playbook
├── inventory.ini              # Environment target definitions
└── README.md                  # Project documentation
```

## Key Features

* **Role-Based Architecture:** Modular design isolating common tasks, web application setup, and database initialization.
* **Idempotent Execution:** Every playbook run guarantees the target environment state without causing unintended side effects.
* **Dynamic Content Delivery:** Jinja2 templated PHP dashboard displaying system facts, system load, and database connection state.
* **Granular Privilege Escalation:** Root operations isolated through `become: yes` at task levels.

## Prerequisites

* **OS:** RHEL 9 / CentOS Stream 9
* **Ansible Core:** 2.11 or higher
* **Python Dependencies:** PyMySQL (required for community.mysql module operations)

### Verify Inventory Configuration

Configure your host definitions inside `inventory.ini`:

```ini
[web_servers]
localhost ansible_connection=local

[db_servers]
localhost ansible_connection=local
```
## Syntax & Dry-Run Validation

Execute a syntax check across all orchestration files:
```ini
ansible-playbook --syntax-check complete-deployment.yml
```

## Execute Complete Stack Provisioning

Run the master playbook to deploy the web, database, and application layers:
```ini
ansible-playbook -i inventory.ini complete-deployment.yml
```

## Challenges & Troubleshooting (CentOS Stream 9 Edge Cases)

During the development of this playbook, several system-level and syntax issues were identified and resolved:

* **Package Naming & Module Resolution**
**Issue:** Default RHEL/CentOS packages mysql-server and php-mysql failed to install.
**Solution:** Updated tasks to target mariadb-server and php-mysqlnd to align with CentOS Stream 9 package repositories.

    MariaDB Local Authentication Errors

        Issue: mysql_user and mysql_db tasks threw Access denied for user 'root'@'localhost' errors.

        Solution: Explicitly defined login_unix_socket: /var/lib/mysql/mysql.sock across all MySQL tasks to authenticate directly via the local Unix domain socket.

    Duplicate Document Markers (---) in Roles

        Issue: Playbook execution failed with Syntax Error while loading YAML: but found another document in role files.

        Solution: Cleaned roles/*/tasks/main.yml and roles/*/defaults/main.yml files, ensuring only a single --- marker exists at the very top of each file.

    Global Variable Scope Across Web and DB Tiers

        Issue: The webapp role failed with AnsibleUndefinedVariable: 'mysql_database' is undefined.

        Solution: Moved common database credentials into group_vars/all.yml and explicitly passed them into the webapp play scope within complete-deployment.yml.

    Systemd Service Unit Registration

        Issue: Apache installation threw Could not find the requested service httpd.

        Solution: Added a systemd module task with daemon_reload: yes immediately following package installation to force systemd to index new unit files before managing the service state.

Verification & Testing

Verify that the LAMP stack is up and operational:
Bash

# Check HTTP service status
systemctl status httpd

# Verify Database status
systemctl status mariadb

# Test web endpoint execution
curl -I http://localhost/index.php
