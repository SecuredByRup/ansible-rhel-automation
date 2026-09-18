Module 10: Automating Firewall Configuration (Lab 14)
Overview

This directory contains Ansible playbooks designed to automate the management and security configuration of system firewalls using firewalld. The playbooks configure standard network services, custom TCP/UDP ports, complex rich rules, firewall zones, and automated audit backups.
Prerequisites

    Operating System: CentOS 8 / RHEL 8 (or compatible Linux distribution)

    Ansible: Version 2.9+ installed on the Control Node

    Firewall Daemon: firewalld service running on target hosts

    Privileges: Sudo / root access (become: yes)

Lab Structure & Playbooks
Playbook	Objective
basic-firewall.yml	

Ensures firewalld is enabled and permits standard services (ssh, http, https).
PNG+ 1
advanced-firewall.yml	

Configures custom application ports (8080, 3306, 5432) and port ranges (60000-61000/tcp).
PNG+ 1
rich-rules.yml	

Implements complex granular rules including SSH rate limiting and explicit network drops.
PNG+ 1
zones-management.yml	

Sets up custom zones (dmz-custom, trusted) across network interfaces.
PNG+ 1
services-management.yml	

Generates custom XML service definitions (custom-app) and updates active rules.
PNG
security-policy.yml	

Enforces role-based firewall security policies for web and database servers.
PNG
firewall-testing.yml	

Validates active ports and outputs an audit backup to /tmp/firewall-backup-localhost.txt.
PNG
Usage Instructions

    Navigate to the module directory:
    Bash

    cd ~/firewall-lab

    Configure your inventory file (inventory):
    Ini, TOML

    [webservers]
    localhost ansible_connection=local

    [all:vars]
    ansible_python_interpreter=/usr/bin/python3

    Execute the playbooks in sequence:
    Bash

    ansible-playbook -i inventory basic-firewall.yml -K
    ansible-playbook -i inventory advanced-firewall.yml -K
    ansible-playbook -i inventory rich-rules.yml -K
    ansible-playbook -i inventory zones-management.yml -K
    ansible-playbook -i inventory services-management.yml -K
    ansible-playbook -i inventory security-policy.yml -K
    ansible-playbook -i inventory firewall-testing.yml -K

Verification & Auditing

To manually verify the active firewall configuration:

    List Active Zones and Allowed Services:
    Bash

    sudo firewall-cmd --list-all

    View Configured Rich Rules:
    Bash

    sudo firewall-cmd --list-rich-rules

    Inspect Automated Backup Report:
    Bash

    cat /tmp/firewall-backup-localhost.txt
