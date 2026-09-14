# Module 4: Ansible Variables and Facts (Lab 5)

This repository demonstrates the practical application of Ansible variables, external variable files (`group_vars`), native system facts, local custom facts (`/etc/ansible/facts.d/`), fact-based conditional execution, and variable precedence hierarchy.

---

## 📁 Repository Structure

```text
lab5-variables-facts/
├── group_vars/
│   └── all.yml
├── external-vars-demo.yml
├── variables-demo.yml
├── facts-exploration.yml
├── setup-custom-facts.yml
├── display-custom-facts.yml
├── os-specific-tasks.yml
├── version-specific-tasks.yml
├── hardware-based-tasks.yml
├── variable-precedence.yml
├── dynamic-variables.yml
├── lab5-verification.yml

🚀 Key Tasks Summary
Task 1: Basic & External Variables
Defined and rendered play- and task-level variables (variables-demo.yml).

Configured global external variables in group_vars/all.yml and verified interpolation using external-vars-demo.yml.

Task 2: Ansible Facts & Custom Facts
Extracted host system details, hardware specs, network interfaces, and disk mounts using native facts (facts-exploration.yml).

Configured local custom facts (.fact files) under /etc/ansible/facts.d/ (setup-custom-facts.yml) and retrieved them using ansible_local (display-custom-facts.yml).

Task 3: Fact-Based Conditional Execution
Evaluated conditional expressions (when) using system facts to handle OS distributions and version variations dynamically (os-specific-tasks.yml, version-specific-tasks.yml).

Dynamically adjusted system parameters and created /tmp/app_config.conf based on CPU cores and RAM specifications (hardware-based-tasks.yml).

Task 4: Variable Precedence & Verification
Demonstrated variable scope hierarchy using set_fact and registered runtime variables (variable-precedence.yml).

Executed assertion checks in lab5-verification.yml to confirm total environment validity.

✅ Verification & Execution
Execute the full verification suite:
ansible-playbook lab5-verification.yml


TASK [Final verification message] *************************************************
ok: [localhost] => {
    "msg": "🎉 All Lab 5 tests completed successfully!"
}
