# Lab 13: Automating Network Configuration with Ansible

This repository contains Ansible playbooks and Jinja2 templates developed to automate network configurations (static IPs, secondary interfaces, routing tables, and DNS) on a local CentOS target without disrupting primary EC2 cloud connectivity.

---

## 📂 Project Structure

```text
lab13-network-automation/
├── inventory
├── configure-static-ip.yml
├── configure-secondary-interface.yml
├── configure-routing.yml
├── configure-dns.yml
├── validate-network.yml
├── templates/
│   └── resolv.conf.j2
└── reports/
    └── localhost_network_report.txt
```

---

## 🚀 Features & Playbook Tasks

### 1. Static IP Configuration (`configure-static-ip.yml`)
* Configures persistent network interface settings using `community.general.nmcli`.
* Utilizes virtual dummy interfaces (`dummy0`) to prevent SSH disconnects on EC2.

### 2. Secondary Network Interfaces (`configure-secondary-interface.yml`)
* Provisions secondary network profiles (`secondary-net` on `dummy1`).
* Verifies state transitions and reports interface readiness.

### 3. Custom Routing Tables (`configure-routing.yml`)
* Automates static routes for local subnets (`172.16.0.0/16`, `10.10.0.0/16`).
* Inspects and outputs current kernel routing tables (`ip route show`).

### 4. DNS Management & Templating (`configure-dns.yml`)
* Dynamic `/etc/resolv.conf` rendering via `templates/resolv.conf.j2`.
* Configures custom search domains (`example.com`, `lab.local`, `internal.net`) and tertiary nameservers.
* Modifies host resolution priority in `/etc/nsswitch.conf`.

### 5. Validation & Auditing (`validate-network.yml`)
* Collects interface states, active links, and network maps.
* Fetches and compiles all data into an automated report (`reports/localhost_network_report.txt`).

---

## 📸 Execution Proofs & Screenshots

| Description | File Name / Proof |
| :--- | :--- |
| **Network Interfaces** <br> Execution of `configure-static-ip.yml` and `configure-secondary-interface.yml`, along with `nmcli connection show` output showing `lab-static-profile` and `secondary-net`. | `![Network Interfaces](01_ansible_network_interfaces.png)` |
| **Routing Tables** <br> Execution of `configure-routing.yml` showing updated static route tables (`192.168.1.0/24`, `10.0.0.0/24`, etc.). | `![Routing Table](02_ansible_routing_table.png)` |
| **DNS Configuration** <br> Execution of `configure-dns.yml` and output of `/etc/resolv.conf` showing rendered search domains and nameservers. | `![DNS Configuration](03_ansible_dns_configuration.png)` |
| **Validation Report** <br> Master audit output showing the complete content of `reports/localhost_network_report.txt`. | `![Validation Report](04_network_validation_report.png)` |
