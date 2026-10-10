# Module 17: Ansible Playbook Optimization & Performance Tuning

![Ansible](https://img.shields.io/badge/Ansible-EE0000?style=for-the-badge&logo=ansible&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)

## Overview

This repository demonstrates practical strategies for refactoring, tuning, and benchmarking Ansible playbooks in enterprise infrastructure environments. It documents the transition from a monolithic architecture to modular roles, performance improvements using asynchronous execution and task delegation, and automated performance benchmarking on AWS EC2.

---

## 🛠️ Key Architecture & Optimization Features

1. **Role Modularization**: Refactored monolithic playbooks into structured, single-responsibility Ansible roles (`webserver`, `database`, `application`, `monitoring`, `security`) [cite: 49].
2. **Asynchronous Execution**: Implemented non-blocking tasks via `async` and `poll: 0` alongside `async_status` polling for resource-heavy operations [cite: 49].
3. **Task Delegation**: Centralized local infrastructure setup (SSL generation, local HTTP verification) using `delegate_to: localhost` [cite: 49].
4. **Engine Tuning**: Configured `fact_caching = jsonfile`, `gathering = smart`, and optimized connection defaults in `ansible.cfg` [cite: 49].

---

## 📁 Directory Structure

```text
lab20/
├── ansible.cfg                    # Optimized Ansible configuration (fact caching, forks)
├── large-inventory                # Inventory file mapping groups to target hosts
├── large-playbook.yml             # Monolithic baseline playbook
├── optimized-playbook.yml         # Modular playbook referencing extracted roles
├── async-optimization.yml         # Asynchronous task execution demo
├── delegation-optimization.yml    # Task delegation demonstration
├── benchmark-playbooks.sh         # Shell script for measuring playbook execution times
├── analyze-performance.yml        # Playbook to collect results and generate summary data
└── roles/                         # Enterprise Role Architecture
    ├── application/               # Tasks, defaults, handlers, templates for application
    ├── database/                  # Database management role
    ├── monitoring/                # Service monitoring configuration
    ├── security/                  # System hardening role
    └── webserver/                 # Apache/Nginx web server deployment
```

### Project & Role Structure Verification
![Role Hierarchy Tree](assets/01_role_hierarchy_tree.png)  
*Proof: Output of `tree roles/ -L 2` showing all 5 created role directories and subdirectories [cite: 49].*

---

## 📊 Benchmark & Performance Results

Execution times were measured on a single-node AWS EC2 instance (`172.31.10.80`) using `benchmark-playbooks.sh` [cite: 49].

| Playbook Strategy | Strategy Description | Execution Time | Output Status |
| :--- | :--- | :--- | :--- |
| **Monolithic Baseline** | Monolithic structure (`--check`) | 10s [cite: 49] | SUCCESS [cite: 49] |
| **Role-Based Modular** | Structured roles with tags (`--check`) | 10s [cite: 49] | SUCCESS [cite: 49] |
| **Async Execution** | Background polling & non-blocking execution | 7s [cite: 49] | SUCCESS [cite: 49] |

> **Note on Async Benchmarking**: Standard `--check` mode cannot spawn active background processes. The benchmark script executes `async-optimization.yml` live without `--check` to properly evaluate job IDs (`ansible_job_id`) [cite: 49].

![Benchmark and Performance Results](assets/02_benchmark_and_performance.png)  
*Proof: Successful execution of `ansible-playbook analyze-performance.yml` and `./benchmark-playbooks.sh` [cite: 49].*

---

## 🚀 Execution & Verification

### 1. View Directory & Role Hierarchy
```bash
tree roles/ -L 2
```

### 2. Execute Automated Performance Benchmarks
```bash
./benchmark-playbooks.sh
```

### 3. Generate Performance Analysis & Summary
```bash
ansible-playbook analyze-performance.yml
```

---

## 📝 Performance Optimization Takeaways

- **Modularity**: Splitting monolithic playbooks into distinct roles enables targeted playbook execution using tags (e.g., `ansible-playbook -t webserver`) and simplifies code maintenance [cite: 49].
- **Asynchronous Tasks**: Offloading long-running tasks via `async` reduced overall execution times from 10s down to 7s in test runs [cite: 49].
- **Fact Caching**: Enabling `fact_caching = jsonfile` in `ansible.cfg` avoids re-querying node facts across repeat runs, conserving execution overhead [cite: 49].
