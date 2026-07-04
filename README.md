# Multi-Node Environment Setup and work with it

## Project Description
This project showcases a complete end-to-end automation workflow using Ansible. It covers the initial environment prerequisites, automating local repository configurations (BaseOS & AppStream), and executing a fully automated Apache HTTP Web Server installation across multiple managed nodes using `ansible-navigator`.

## Environment Prerequisites & Manual Preparation
## 🗺️ Network & Inventory Architecture
The managed infrastructure mimics a real-world multi-tier application environment, structured as follows:
* **Control Node:** Orchestrates all tasks via secure execution environments.
* **`[web]` Group (`node1`, `node2`):** Dedicated web application servers.
* **`[db]` Group (`node3`):** Dedicated backend database infrastructure.

2. **SSH Key Generation:** Generated a secure SSH key pair on the Control Node using:
3.Passwordless Authentication: Distributed the public key to all target managed nodes to enable secure, passwordless SSH access:
    `ssh-copy-id devops@node1
    ssh-copy-id devops@node2
    ssh-copy-id devops@node3`

## Repository Structure & Core Files:
​`inventory.ini`: Lists the managed hosts (`node1, node2, node3`) utilizing local hostname resolution.
`​ansible.cfg`: Sets the default inventory paths, defines the non-root remote user (`devops`), and configures secure `privilege escalation` (`become=true`).
`​ansible-navigator.yml`: Defines the specific containerized Execution Environment (EE) images to maintain standard Red Hat compliance.
`​setup-local-repo.yml`: A playbook that automatically mounts the RHEL ISO media image to `/mnt` and configures local AppStream and BaseOS yum repositories using the native `ansible.builtin.mount `and `ansible.builtin.yum_repository` modules.
`​install-httpd.yml`: A playbook that utilizes the modern `ansible.builtin.dnf` module to install and verify the latest Apache HTTP package across all nodes, testing the health of our local repositories.
​
## How to Run the Portfolio
​To execute the automation workflow using Ansible interface, run:
  `ansible-playbook setup-local-repo.yml
   ansible-palybook install-httpd.yml`
To execute the automation workflow using the modern Ansible Navigator interface, run:
  `ansible-navigator run setup-local-repo.yml -m stdout
ansible-navigator run install-httpd.yml -m stdout`
---

## ⚡ Advanced Enterprise Automation & SecOps

### 1️⃣ Secured Service Deployment (Ansible Vault)
* **Main Playbook:** `package.yml`
* **Encrypted Variables:** `vars/input.yml`
* **What it does:** Implements **SecOps best practices** by completely isolating sensitive metadata from execution logic. Core cluster packages (defined via `lin_srv`) are protected using AES-256 encryption via Ansible Vault. The automation pipeline decrypts these values on-the-fly, deploys the core infrastructure packages (`httpd`, `mariadb-server`, `vsftpd`) utilizing Full Qualified Collection Names (FQCN), ensures system services are active, and dynamically configures permanent inbound rules on `firewalld`.
### 2️⃣ Automated Custom & Native Fact Pipelines
* **Target Directory:** `Ansible_local_facts/`
* **What it does:** Maximizes local machine intelligence and dynamic environment scanning.
  * `local_facts.yml`: Automates the deployment of static, site-specific configuration metadata (`custom.fact`) to remote server paths (`/etc/ansible/facts.d`).
  * `Fact.yml`: Handles runtime consumption. It triggers a mid-play execution refresh using the `setup` module to force-load the newly deployed facts, dynamically determining package tasks without hardcoded parameters.
  * `Test-clusters-facts.yml`: Acts as an infrastructure diagnostic playbook, logging core target variables (Default IPv4, Hostname, and OS Distribution) for system status validation.
### 3️⃣ System Auditing & Idempotency Management
* **Main Playbook:** `register.yml`
* **What it does:** Focuses on infrastructure state auditing. It utilizes the `ansible.builtin.stat` module to capture file system properties into a runtime variable (`register: sym`). By logging and outputting this variable via debugging, the playbook conditionally triggers file touch actions (`ansible.builtin.file`) using specific execution logic (`when: sym.stat.exists == false`). This ensures protection against unintended overwrites or configuration drifts.
  ### 4️⃣ Dynamic Conditioning (Magic Variables)
* **Main Playbook:** `Magic.yml`
* **What it does:** Demonstrates absolute architectural targeting precision. Using conditional logic based on Ansible magic variables (`when: inventory_hostname in groups["db"]`), the control engine automatically queries the inventory layout at runtime, ensuring that group-specific playbooks execute only on eligible nodes.

---

## 🚀 How to Execute Advanced SecOps Workflows

### Running Secured Vault Playbooks:
To execute the core automation pipeline while safely unlocking the encrypted Vault variables, use the `--ask-vault-pass` flag:
```bash
# Using standard Ansible core
ansible-playbook package.yml --ask-vault-pass

# Using Ansible Navigator interface
ansible-navigator run package.yml -m stdout --ask-vault-pass --enable-promet
