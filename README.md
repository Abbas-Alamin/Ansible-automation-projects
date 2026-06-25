# Multi-Node Environment Setup & Automated Apache Deployment

## Project Description
This project showcases a complete end-to-end automation workflow using Ansible. It covers the initial environment prerequisites, automating local repository configurations (BaseOS & AppStream), and executing a fully automated Apache HTTP Web Server installation across multiple managed nodes using `ansible-navigator`.

## Environment Prerequisites & Manual Preparation
Before running the Ansible playbooks, the following system and networking steps were manually configured to ensure seamless connectivity and proper lab isolation:
1. **Local Name Resolution:** Configured the `/etc/hosts` file on all nodes to map IP addresses to specific hostnames (`node1`, `node2`, `node3`).
2. **SSH Key Generation:** Generated a secure SSH key pair on the Control Node using:
   ```bash
Passwordless Authentication: Distributed the public key to all target managed nodes to enable secure, passwordless SSH access:
    `ssh-copy-id devops@node1
    ssh-copy-id devops@node2
    ssh-copy-id devops@node3`
Repository Structure & Core Files
​inventory.ini: Lists the managed hosts (`node1, node2, node3`) utilizing local hostname resolution.
`​ansible.cfg`: Sets the default inventory paths, defines the non-root remote user (`devops`), and configures secure `privilege escalation` (`become=true`).
`​ansible-navigator.yml`: Defines the specific containerized Execution Environment (EE) images to maintain standard Red Hat compliance.
`​setup-local-repo.yml`: A playbook that automatically mounts the RHEL ISO media image to `/mnt` and configures local AppStream and BaseOS yum repositories using the native `ansible.builtin.mount `and `ansible.builtin.yum_repository` modules.
`​install-httpd.yml`: A playbook that utilizes the modern `ansible.builtin.dnf` module to install and verify the latest Apache HTTP package across all nodes, testing the health of our local repositories.
​How to Run the Portfolio
​To execute the automation workflow using the modern Ansible Navigator interface, run:
  `ansible-navigator run setup-local-repo.yml -m stdout
ansible-navigator run install-httpd.yml -m stdout`
