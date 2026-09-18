# Module 08: Enterprise User & Group Management with Ansible

An enterprise-grade automation solution built with Ansible to manage the full lifecycle of Linux user accounts, Role-Based Access Control (RBAC) group assignments, home directory migrations, and account security policies across RHEL/CentOS environments.

---

## Technical Overview & Key Accomplishments

- **Declarative Group & User Provisioning:** Automated system group creation with explicit GIDs and user account provisioning (UIDs, shell types, primary/secondary groups) using standard Ansible modules (`ansible.builtin.user`, `ansible.builtin.group`).
- **Directory Migration & Permission Enforcements:** Migrated user home directories to non-standard paths (e.g., `/opt/users/`) while maintaining explicit file permissions and non-root ownership using `ansible.builtin.file`.
- **Account Security & Lifecycle Operations:** Enforced password aging policies via `chage` integrations and implemented account locking (`password_lock`) for compliance.
- **Scalable Playbook Design:** Utilized Ansible loop structures and complex list filters (`product`) to handle batch operations efficiently across multi-user environments.

---

## Repository Architecture

```text
lab12-user-management/
├── inventory.ini                  # Local host inventory definition
├── create-groups.yml              # Group creation tasks
├── create-users.yml               # Base user provisioning
├── modify-shells.yml              # Shell assignment updates
├── modify-groups.yml              # Membership & role adjustments
├── modify-home-dirs.yml           # Home directory migrations & permissions
├── advanced-user-management.yml   # Multi-user looping & security operations
└── verify-user-management.yml     # Automated verification suite
```

## Deployment & Verification Workflow
The project was executed and validated sequentially using local privilege escalation (become):

```bash
# Navigate to the project directory
cd ~/lab12-user-management

# 1. Base Infrastructure Provisioning
ansible-playbook -i inventory.ini create-groups.yml -K
ansible-playbook -i inventory.ini create-users.yml -K

# 2. Attribute Modifications & Migrations
ansible-playbook -i inventory.ini modify-shells.yml -K
ansible-playbook -i inventory.ini modify-groups.yml -K
ansible-playbook -i inventory.ini modify-home-dirs.yml -K

# 3. Security Hardening & Automated Validation
ansible-playbook -i inventory.ini advanced-user-management.yml -K
ansible-playbook -i inventory.ini verify-user-management.yml -K
```

## Implementation Results & Verification

1. System Verification Output
All user accounts and group memberships passed automated assertion checks with zero failures (failed=0).

2. Idempotency Confirmation
Re-running the entire playbook suite resulted in changed=0, confirming that the system state is fully declarative and idempotent.

3. Generated User & Group Audit Report
A dynamic system report was generated at /tmp/user_report.txt containing full details of created UIDs, GIDs, and shell paths.

4. Custom Home Directory & Permission Setup
Verified successful relocation of home directories (e.g., contractor account /opt/users/eve) with correct non-root ownership and strict file modes.

## Manual Inspection Commands
To manually check system states on the target node:

```bash
# Check created users
getent passwd | grep -E "(alice|bob|carol|david|eve|frank|grace)"

# Check created groups
getent group | grep -E "(developers|testers|managers|contractors|project-alpha|sysadmins)"

# Verify home directories and ownership
ls -la /home /opt/users
```
