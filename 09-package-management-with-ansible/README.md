# Module 9: Package Management Across Distributions

This repository contains Ansible playbooks and inventory configurations designed to automate enterprise package management, cross-distribution service deployments, block/rescue error handling, and automated metric reporting across Enterprise Linux (CentOS/RHEL) environments.

## Repository Structure

```text
ansible-lab11/
├── inventory/
│   └── hosts                         # Host inventory configured for single-node local execution
├── playbooks/
│   ├── install-basic-packages.yml       # Subtask 1.2: Basic package setup (git, vim, curl, htop)
│   ├── advanced-package-management.yml  # Subtask 1.3: Development runtime setup & insecure package removal
│   ├── rhel-package-management.yml      # Subtask 2.1: EPEL repository & Docker CE setup
│   ├── universal-package-management.yml # Subtask 2.2: Cross-distro web/db deployment & HTTP verification
│   ├── robust-package-management.yml   # Subtask 2.3: RPM list backups & block/rescue error handling
│   └── package-reporting.yml          # Subtask 2.4: Dynamic system & package metric dashboard
└── README.md                         # Lab documentation
```

## Setup & Environment Details

* Target OS: CentOS / RHEL (Single Node execution via ansible_connection=local)

* Control Node: Localhost (127.0.0.1)

* Ansible Connection: Connection plugin set to local with become privileges.

## How to Run the Playbooks

Verify Connectivity:
```bash
ansible all -i inventory/hosts -m ping
```
Execute Task 1 Playbooks:
```bash
ansible-playbook -i inventory/hosts playbooks/install-basic-packages.yml -K
ansible-playbook -i inventory/hosts playbooks/advanced-package-management.yml -K
```
Execute Task 2 Playbooks:
```bash
ansible-playbook -i inventory/hosts playbooks/rhel-package-management.yml -K
ansible-playbook -i inventory/hosts playbooks/universal-package-management.yml -K
ansible-playbook -i inventory/hosts playbooks/robust-package-management.yml -K
ansible-playbook -i inventory/hosts playbooks/package-reporting.yml -K
```
## Generated Artifacts & Verification

* Web Server Verification: `curl -s http://127.0.0.1`

* Package Backups & Summaries: `/tmp/package_backup/`

* System Report Dashboard: `/tmp/ansible_reports/localhost_package_report.txt`
    
