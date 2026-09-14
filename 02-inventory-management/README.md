# Module 02: Ansible Inventory Management (Static & Dynamic)

## Overview
This repository contains the configuration files, dynamic inventory scripts, and verification playbooks for Ansible Inventory Management. It demonstrates static INI/YAML layouts, dynamic host discovery using custom Python scripts, group variable hierarchy, and local CLI parsing techniques.

---

## Directory Structure
```text
02-inventory-management/
├── advanced-inventory.ini
├── cloud-inventory.py
├── dynamic-inventory.py
├── inventory.ini
├── inventory.yml
└── test-inventory.yml

## Configuration Files
1. Basic Static Inventory (`inventory.ini`)
Configures host grouping, child relationships, and group-level variables in traditional INI format.

[webservers]
web1 ansible_host=127.0.0.1 ansible_user=student
web2 ansible_host=127.0.0.1 ansible_user=student

[databases]
db1 ansible_host=127.0.0.1 ansible_user=student

[production:children]
webservers
databases

[webservers:vars]
http_port=80
max_clients=200

[databases:vars]
mysql_port=3306
max_connections=100

2. Multi-Tier Static Inventory (`advanced-inventory.ini`)
Demonstrates complex regional tagging, environment grouping (staging vs. production), and multi-layer variable assignment.

Ini, TOML
[webservers]
web1 ansible_host=127.0.0.1 ansible_user=student server_role=frontend
web2 ansible_host=127.0.0.1 ansible_user=student server_role=frontend

[databases]
db1 ansible_host=127.0.0.1 ansible_user=student server_role=backend

[loadbalancers]
lb1 ansible_host=127.0.0.1 ansible_user=student server_role=loadbalancer

[production:children]
webservers
databases
loadbalancers

[staging]
staging-web ansible_host=127.0.0.1 ansible_user=student
staging-db ansible_host=127.0.0.1 ansible_user=student

[east-coast]
web1
db1

[west-coast]
web2
lb1

[all:vars]
ansible_connection=local

[webservers:vars]
http_port=80
https_port=443
document_root=/var/www/html
max_clients=200

[databases:vars]
mysql_port=3306
mysql_datadir=/var/lib/mysql
max_connections=100
innodb_buffer_pool_size=256M

[loadbalancers:vars]
balance_method=roundrobin
health_check_interval=30

## 3. Structured YAML Inventory (`inventory.yml`)
Replicates host and group definitions using structured YAML hierarchies.

YAML
all:
  children:
    webservers:
      hosts:
        web1:
          ansible_host: 127.0.0.1
          ansible_user: student
          server_role: frontend
          http_port: 80
        web2:
          ansible_host: 127.0.0.1
          ansible_user: student
          server_role: frontend
          http_port: 80
      vars:
        max_clients: 200
        document_root: /var/www/html
    databases:
      hosts:
        db1:
          ansible_host: 127.0.0.1
          ansible_user: student
          server_role: backend
          mysql_port: 3306
      vars:
        max_connections: 100
        mysql_datadir: /var/lib/mysql
    production:
      children:
        webservers:
        databases:
      vars:
        environment: production
        backup_schedule: "0 2 * * *"

## Dynamic Inventory Automation
1. JSON Data Provider Script (`dynamic-inventory.py`)
Generates host metrics and metadata programmatically in JSON format compatible with Ansible's --list and --host requirements.

Python
#!/usr/bin/env python3
import json
import sys

def get_inventory():
    return {
        'webservers': {
            'hosts': ['web1', 'web2'],
            'vars': {'http_port': 80, 'max_clients': 200}
        },
        'databases': {
            'hosts': ['db1'],
            'vars': {'mysql_port': 3306, 'max_connections': 100}
        },
        'production': {
            'children': ['webservers', 'databases'],
            'vars': {'environment': 'production'}
        },
        '_meta': {
            'hostvars': {
                'web1': {'ansible_host': '127.0.0.1', 'ansible_user': 'student', 'server_role': 'frontend'},
                'web2': {'ansible_host': '127.0.0.1', 'ansible_user': 'student', 'server_role': 'frontend'},
                'db1': {'ansible_host': '127.0.0.1', 'ansible_user': 'student', 'server_role': 'backend'}
            }
        }
    }

def main():
    if len(sys.argv) == 2 and sys.argv[1] == '--list':
        print(json.dumps(get_inventory(), indent=2))
    elif len(sys.argv) == 3 and sys.argv[1] == '--host':
        print(json.dumps(get_inventory()['_meta']['hostvars'].get(sys.argv[2], {}), indent=2))
    else:
        sys.exit(1)

if __name__ == '__main__':
    main()

2. Cloud Environment Inventory Script (`cloud-inventory.py`)
Simulates dynamic cloud provider discovery using environment variables (CLOUD_PROVIDER, CLOUD_REGION, ENVIRONMENT).

Python
#!/usr/bin/env python3
import json
import sys
import os
from datetime import datetime

class CloudInventory:
    def __init__(self):
        self.cloud_provider = os.environ.get('CLOUD_PROVIDER', 'aws')
        self.region = os.environ.get('CLOUD_REGION', 'us-east-1')
        self.environment = os.environ.get('ENVIRONMENT', 'production')

    def build_inventory(self):
        instances = [
            {'name': 'web1', 'ip': '127.0.0.1', 'type': 't2.micro', 'tags': {'Role': 'webserver', 'Environment': 'production'}},
            {'name': 'web2', 'ip': '127.0.0.1', 'type': 't2.micro', 'tags': {'Role': 'webserver', 'Environment': 'production'}},
            {'name': 'db1', 'ip': '127.0.0.1', 'type': 't2.small', 'tags': {'Role': 'database', 'Environment': 'production'}}
        ]
        
        inventory = {
            'all': {'vars': {'cloud_provider': self.cloud_provider, 'region': self.region, 'discovered_at': datetime.now().isoformat()}},
            '_meta': {'hostvars': {}}
        }

        for inst in instances:
            role_group = f"{inst['tags']['Role']}s"
            if role_group not in inventory:
                inventory[role_group] = {'hosts': [], 'vars': {}}
            inventory[role_group]['hosts'].append(inst['name'])
            inventory['_meta']['hostvars'][inst['name']] = {
                'ansible_host': inst['ip'],
                'ansible_user': 'student',
                'instance_type': inst['type'],
                'cloud_provider': self.cloud_provider,
                'region': self.region,
                'ansible_connection': 'local'
            }
        return inventory

def main():
    cloud_inv = CloudInventory()
    if len(sys.argv) == 2 and sys.argv[1] == '--list':
        print(json.dumps(cloud_inv.build_inventory(), indent=2))
    else:
        sys.exit(1)

if __name__ == '__main__':
    main()

## Validation & Verification Playbook (`test-inventory.yml`)
YAML
---
- name: Test Inventory Configuration
  hosts: all
  gather_facts: no
  tasks:
    - name: Display host information
      debug:
        msg: "Host: {{ inventory_hostname }} | IP: {{ ansible_host | default('N/A') }}"

Terminal Execution Verification
1. Static Inventory CLI Parsing Output
Bash
$ ansible-inventory -i inventory.ini --list
{
    "_meta": {
        "hostvars": {}
    },
    "databases": {
        "hosts": [
            "db1"
        ]
    },
    "production": {
        "children": [
            "databases",
            "webservers"
        ]
    },
    "webservers": {
        "hosts": [
            "web1",
            "web2"
        ]
    }
}
2. Dynamic Inventory Script Verification
Bash
$ ./dynamic-inventory.py --list
{
  "webservers": {
    "hosts": [
      "web1",
      "web2"
    ],
    "vars": {
      "http_port": 80,
      "max_clients": 200
    }
  },
  "databases": {
    "hosts": [
      "db1"
    ]
  }
}
3. Playbook Execution Output Across Inventories
Bash
$ ansible-playbook -i ./dynamic-inventory.py test-inventory.yml --connection=local

PLAY [Test Inventory Configuration] ********************************************************************

TASK [Display host information] ************************************************************************
ok: [web1] => {
    "msg": "Host: web1 | IP: 127.0.0.1"
}
ok: [web2] => {
    "msg": "Host: web2 | IP: 127.0.0.1"
}
ok: [db1] => {
    "msg": "Host: db1 | IP: 127.0.0.1"
}

PLAY RECAP *********************************************************************************************
db1                        : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web1                       : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2                       : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
