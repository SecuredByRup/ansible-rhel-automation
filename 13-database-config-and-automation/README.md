# Module 13: Database Configuration and Automation with Ansible

![Ansible](https://img.shields.io/badge/Ansible-E25939?style=for-the-badge&logo=ansible&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

An enterprise-grade, modular Ansible automation solution designed to automate the installation, service enablement, database creation, user administration, and privilege management for **MySQL** and **PostgreSQL** database servers.

---

## Architecture & Project Structure

```text
ansible-database-lab/
├── ansible.cfg             # Global Ansible configuration settings
├── inventory.ini           # Target host inventory (local connection setup)
├── playbooks/
│   ├── mysql-setup.yml     # Direct MySQL deployment playbook
│   ├── postgresql-setup.yml# Direct PostgreSQL deployment playbook
│   ├── role-mysql.yml      # Modular MySQL playbook utilizing roles
│   └── role-postgresql.yml # Modular PostgreSQL playbook utilizing roles
└── roles/
    └── database-management/
        ├── defaults/
        │   └── main.yml    # Default role variables
        ├── tasks/
        │   └── main.yml    # Conditional task execution logic
        └── vars/
            └── main.yml    # Engine-specific variable mappings
```

---

## Key Technical Solutions & Highlights

* **Local Connection Override**: Configured `ansible_connection=local` within `inventory.ini` to seamlessly execute configurations on control node targets without SSH key restrictions.
* **Dynamic Python Driver Management**: Automatically installs `python3-pip` and provisions database connectors (`pymysql` for MySQL and `psycopg2-binary` for PostgreSQL) via the Ansible `pip` module.
* **Modular Role Architecture**: Leverages conditional task blocks (`when: db_type == 'mysql'` / `when: db_type == 'postgresql'`) within a single unified role for multi-engine support.
* **PostgreSQL Collection Syntax Compatibility**: Utilizes modern parameter structures (`login_db`, `objs`, `roles`, and `privs`) inside the `postgresql_privs` module.

---

## Execution & Validation Guide

### 1. MySQL Direct Playbook Deployment
Installs `mysql-server`, installs the `pymysql` Python driver via pip, starts/enables `mysqld`, creates target database `app_db`, and provisions `app_user`.
```bash
ansible-playbook -i inventory.ini playbooks/mysql-setup.yml --ask-become-pass
```
![MySQL Setup Execution](assets/01-mysql-setup-execution.png)  
*Proof: Successful execution of `mysql-setup.yml` with `failed=0`.*

---

### 2. PostgreSQL Direct Playbook Deployment
Installs `postgresql-server`, initializes the database cluster, starts/enables `postgresql`, installs `psycopg2-binary`, creates database `app_pg_db`, creates `app_pg_user`, and assigns permissions using `postgresql_privs`.
```bash
ansible-playbook -i inventory.ini playbooks/postgresql-setup.yml --ask-become-pass
```
![PostgreSQL Setup Execution](assets/02-postgresql-setup-execution.png)  
*Proof: Successful execution of `postgresql-setup.yml` with `failed=0`.*

---

### 3. Reusable Database Role Deployments
Executes modular role-based deployments for each database engine dynamically.

**MySQL Role Execution:**
```bash
ansible-playbook -i inventory.ini playbooks/role-mysql.yml --ask-become-pass
```
![MySQL Role Execution](assets/03-role-mysql-execution.png)  
*Proof: Executing `role-mysql.yml` using the reusable `database-management` role.*

**PostgreSQL Role Execution:**
```bash
ansible-playbook -i inventory.ini playbooks/role-postgresql.yml --ask-become-pass
```
![PostgreSQL Role Execution](assets/04-role-postgresql-execution.png)  
*Proof: Executing `role-postgresql.yml` using the reusable `database-management` role.*

---

### 4. Final Service Status & File System Verification
Verifies active system state for both database services and inspects directory layout.
```bash
systemctl is-active mysqld postgresql
ls -la playbooks/
```
![Service and Filesystem Audit](assets/05-service-and-filesystem-audit.png)  
*Proof: Active systemd service status check (`mysqld` and `postgresql`) and directory listing.*
