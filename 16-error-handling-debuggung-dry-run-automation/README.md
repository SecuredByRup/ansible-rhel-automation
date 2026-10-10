# Module 16: Ansible Error Handling, Debugging & Dry Run Automation

![Ansible](https://img.shields.io/badge/Ansible-EE0000?style=for-the-badge&logo=ansible&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Status](https://img.shields.io/badge/Reliability-Error_Handling-success?style=for-the-badge)

## Overview

This repository demonstrates enterprise automation reliability practices using **Ansible**. It covers advanced data structure debugging, dry-run simulation via `--check` mode, task failure interception with `ignore_errors`, and structural resilience patterns using `block`, `rescue`, and `always`.

---

## Key Technical Concepts Covered

- **Variable & Data Inspection:** Leveraging the `debug` module, Jinja2 filters (`type_debug`, `length`), and verbosity flags (`-v`) to inspect facts and complex loops.
- **Dry-Run Validation:** Utilizing `--check` mode alongside `ansible_check_mode` conditionals to simulate changes prior to execution.
- **Fault Tolerance:** Employing `ignore_errors: yes` to register non-zero return codes and prevent playbook crashes.
- **Exception Handling Blocks:** Implementing `block / rescue / always` workflows to catch task failures, trigger automated recovery procedures, and ensure cleanup tasks execute.

---

## Directory Structure

```text
lab19-error-handling/
├── inventory                    # Target host inventory file
├── debug-variables.yml          # Basic debug module examples
├── advanced-debug.yml           # Data filtering and verbosity testing
├── check-mode-aware.yml         # Dry-run execution and conditional checks
├── basic-error-handling.yml     # Non-zero exit code tolerance via ignore_errors
└── block-rescue-demo.yml        # Structural block/rescue/always exception handling
```

---

## Execution Results & Verification

### 1. Advanced Debugging & Verbosity Output
Verified variable types, loop iteration items, and verbosity logging using `ansible-playbook -i inventory advanced-debug.yml -v`.
![Advanced Debug Verbosity](assets/01_advanced_debug_verbosity.png)  
*Proof: Detailed output verifying variable structures and loop execution.*

### 2. Check Mode Simulation (`--check`)
Evaluated system state impact safely without applying changes using `ansible-playbook -i inventory check-mode-aware.yml --check`.
![Check Mode Dry Run](assets/02_check_mode_dry_run.png)  
*Proof: Successful evaluation in check mode without altering actual target state.*

### 3. Non-Zero Exit Code Interception (`ignore_errors`)
Intercepted command failures (`rc=1`, `rc=2`) and continued playbook execution (`ignored=2`).
![Ignore Errors Handling](assets/03_ignore_errors_handling.png)  
*Proof: Graceful handling of task failure using `ignore_errors: yes`.*

### 4. Structural Exception Recovery (`block` / `rescue` / `always`)
Caught task execution failures in the primary block, triggering the rescue task and mandatory cleanup step (`rescued=1`).
![Block Rescue Always](assets/04_block_rescue_always.png)  
*Proof: Resilient exception recovery and guaranteed cleanup execution.*

---

## Best Practices
- **Always Register Failed Output:** When applying `ignore_errors: yes`, capture the task output into a variable using `register` to evaluate return codes (`rc`) downstream.
- **Safe Recovery Actions:** Place fallback tasks inside `rescue` blocks to restore services or revert changes when primary tasks fail.
- **Mandatory Cleanup:** Use the `always` block for tasks like temporary file cleanup or log generation so they run regardless of task outcomes.
