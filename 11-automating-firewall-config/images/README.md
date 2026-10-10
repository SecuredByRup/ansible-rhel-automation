# Module 11: Automating Firewall Configuration with Ansible

This repository contains Ansible playbooks designed to automate the management and configuration of system firewalls using `firewalld`. The playbooks configure standard network services, custom TCP/UDP ports, complex rich rules, firewall zones, and automated test backups.

## Repository Structure

```text
11-automating-firewall-config/
├── inventory                  # Host inventory configured for local execution
├── basic-firewall.yml         # Subtask: Ensures firewalld is active and enables standard services
├── advanced-firewall.yml      # Subtask: Opens custom ports (8080, 3306, 5432) and port ranges
├── rich-rules.yml             # Subtask: Implements complex rate-limiting and IP-based drops
├── zones-management.yml       # Subtask: Configures DMZ, trusted, and public zones
├── services-management.yml    # Subtask: Creates custom service XML definition (custom-app)
├── security-policy.yml        # Subtask: Applies role-specific security rules
├── firewall-testing.yml       # Subtask: Verifies active ports and exports backup state
└── images/                    # Execution proofs and validation screenshots
```

## Features & Playbook Tasks

* **Basic Firewall Configuration (`basic-firewall.yml`)**: Ensures `firewalld` is active and permits standard services (`ssh`, `http`, `https`).
* **Advanced Port Management (`advanced-firewall.yml`)**: Opens custom TCP ports (e.g., `8080`, `3306`, `5432`) and port ranges (`60000-61000`).
* **Rich Rules (`rich-rules.yml`)**: Implements precise rate-limiting, IP-based drops, and log prefixes.
* **Zones Management (`zones-management.yml`)**: Manages and configures network zones and interfaces.
* **Services Management (`services-management.yml`)**: Provisions custom service definitions (`custom-app`).
* **Security Policy & Testing (`security-policy.yml` & `firewall-testing.yml`)**: Compiles firewall configurations and exports a backup state to `/tmp/firewall-backup-localhost.txt`.

---

## Execution Proofs & Verification

### 1. Environment Verification & Basic Firewall Setup
![Task 1.1 & 1.2 Execution](images/01_ansible_basic_firewall.png)  
*Proof: Execution of `basic-firewall.yml` and verification of active `firewalld` state, standard services, and default zone setup.*

### 2. Basic Firewall Play Recap & Rule Outputs
![Task 1.2 Play Recap](images/02_ansible_basic_play_recap.png)  
*Proof: Detailed output showing active services, custom ports, and initial rich rules applied on localhost.*

### 3. Advanced Firewall Rules Playbook Execution
![Task 1.3 Execution](images/03_ansible_advanced_firewall.png)  
*Proof: Successful execution of `advanced-firewall.yml` incorporating custom port ranges and rules.*

### 4. Rich Rules & Firewall Zones Management
![Task 1.4 & 2.1 Execution](images/04_ansible_rich_rules_zones.png)  
*Proof: Output displaying active rich rules (source-based filtering/logging) and zone management executions.*

### 5. Custom Service Definition & Public Zone Services
![Task 2.2 Execution](images/05_ansible_custom_services.png)  
*Proof: Verification of custom service configuration (`custom-app`) alongside standard services in the public zone.*

### 6. Security Policy & Final Firewall Audit Backup
![Task 2.3 & 2.4 Execution](images/06_ansible_firewall_audit_backup.png)  
*Proof: Final security policy application and successful export of the configuration backup to `/tmp/firewall-backup-localhost.txt`.*

> *Note: Minor test timeouts during validation occurred because background daemons like `httpd` were not actively running on the target node during the test step, but all firewall rule configurations were successfully applied and verified via Ansible.*