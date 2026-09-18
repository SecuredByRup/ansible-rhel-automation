# Module 6: Enterprise Web Infrastructure via Ansible Roles

A modular, production-ready Ansible automation suite that provisions and validates a secure Apache HTTP Server (`httpd`) and core system configurations on CentOS Stream 9 / RHEL environments using reusable Ansible Roles.

---

## Architecture Overview

This project transitions standard procedural playbooks into a modular, role-based infrastructure model. System baseline setup and web tier management are isolated into decoupled roles.

```text
                     ┌──────────────────────────────┐
                     │    Ansible Control Node      │
                     └──────────────┬───────────────┘
                                    │
                             site.yml (-K)
                                    │
                     ┌──────────────┴───────────────┐
                     │    Target Host (localhost)   │
                     └──────────────┬───────────────┘
                                    │
        ┌───────────────────────────┴───────────────────────────┐
        ▼                                                       ▼
┌───────────────┐                                       ┌───────────────┐
│  common Role  │                                       │apache-server  │
│ System Specs  │                                       │ Jinja2 VHost  │
└───────────────┘                                       └───────────────┘

```

## Directory Structure
```Plaintext

ansible-lab9/
├── group_vars/
│   └── web_servers.yml        # Web tier environment variables
├── roles/
│   ├── common/                # Base OS baseline configuration
│   │   ├── defaults/main.yml
│   │   ├── handlers/main.yml
│   │   └── tasks/main.yml
│   └── apache-webserver/      # Apache service management & templates
│       ├── defaults/main.yml
│       ├── handlers/main.yml
│       ├── meta/main.yml
│       ├── tasks/main.yml
│       ├── templates/
│       │   ├── index.html.j2
│       │   └── vhost.conf.j2
│       └── vars/main.yml
├── deploy-webserver.yml       # Role deployment entry point
├── deploy-webserver-adv.yml   # Advanced configuration playbook
├── inventory.ini              # Host definitions
├── site.yml                   # Multi-role orchestrator playbook
├── validate-deployment.yml    # Post-deployment assertion test suite
└── README.md                  # Project documentation
```
### Key Features

* **Modular Role Architecture:** Complete separation of tasks, default variables, handlers, and templates for optimal reuse.

* **Jinja2 Dynamic Rendering:** Custom virtual host files and dynamic index dashboards rendering real-time system facts.

* **Automated Quality Assurance:** Dedicated validation suite executing service state, HTTP 200, and file existence assertions.

* **Idempotent State Management:** Safe re-execution across environment instances with zero side-effects (changed=0).

### Deployment & Verification Commands

1. Execute Multi-Role Orchestrator
```Bash
ansible-playbook -i inventory.ini site.yml -K
```

2. Run Automated Quality Assurance
```Bash
ansible-playbook -i inventory.ini validate-deployment.yml
```

3. Verify HTTP Endpoint Response
```Bash
curl -s http://localhost
```

### Verification Proof & Evidence

**1. Multi-Role Playbook Execution**

Figure 1: site.yml executing the common and apache-webserver roles sequentially with zero failures (failed=0).
![Multi-Role Playbook Execution](images/01%20execution%20of%20site.yml.png)

**2. Automated Assertion Validation**

Figure 2: validate-deployment.yml passing all 6 system assertions, including HTTP status 200 and index creation.
![Automated Assertion Validation](images/02%20validate-deployment.yml.png)

**3. Dynamic HTTP Endpoint Response**

Figure 3: HTML payload rendered via Jinja2 displaying host information, IP address, and OS facts.
![Dynamic HTTP Endpoint Response](images/03%20index%20page.png)

**4. Role Structure Layout**

Figure 4: Full structural tree of the common and apache-webserver roles.
![Role Structure Layout](images/04%20ansible%20roles.png)
