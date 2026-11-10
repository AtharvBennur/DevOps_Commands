# Ansible Reference Library

This directory is a compact, interview-oriented Ansible reference for daily DevOps work and AWS automation.

## What this covers
- Ansible fundamentals, architecture, and execution model
- Inventory, variables, playbooks, tasks, modules, roles, and collections
- Templates, handlers, Vault, security, and troubleshooting
- AWS automation using the `amazon.aws` collection
- Real-world playbooks and interview preparation

## Folder structure

```text
ansible/
├── README.md
├── 01_ansible_fundamentals.md
├── 02_ansible_architecture.md
├── 03_ansible_commands.md
├── 04_inventory_and_variables.md
├── 05_playbooks_and_tasks.md
├── 06_ansible_modules.md
├── 07_aws_ansible_collection.md
├── 08_ansible_roles_and_collections.md
├── 09_ansible_templates_and_handlers.md
├── 10_ansible_vault_and_security.md
├── 11_ansible_troubleshooting.md
├── 12_real_world_tasks.md
├── 13_ansible_interview_questions.md
└── examples/
    ├── install_packages.yml
    ├── configure_nginx.yml
    ├── create_users.yml
    ├── manage_services.yml
    ├── copy_files.yml
    ├── create_directories.yml
    ├── execute_shell_commands.yml
    ├── setup_docker.yml
    ├── configure_ec2.yml
    ├── aws_ec2_inventory.yml
    ├── aws_provisioning.yml
    └── deploy_application.yml
```

## Learning order
1. [01_ansible_fundamentals.md](01_ansible_fundamentals.md)
2. [02_ansible_architecture.md](02_ansible_architecture.md)
3. [03_ansible_commands.md](03_ansible_commands.md)
4. [04_inventory_and_variables.md](04_inventory_and_variables.md)
5. [05_playbooks_and_tasks.md](05_playbooks_and_tasks.md)
6. [06_ansible_modules.md](06_ansible_modules.md)
7. [07_aws_ansible_collection.md](07_aws_ansible_collection.md)
8. [08_ansible_roles_and_collections.md](08_ansible_roles_and_collections.md)
9. [09_ansible_templates_and_handlers.md](09_ansible_templates_and_handlers.md)
10. [10_ansible_vault_and_security.md](10_ansible_vault_and_security.md)
11. [11_ansible_troubleshooting.md](11_ansible_troubleshooting.md)
12. [12_real_world_tasks.md](12_real_world_tasks.md)
13. [13_ansible_interview_questions.md](13_ansible_interview_questions.md)

## Example YAML files
- [examples/install_packages.yml](examples/install_packages.yml)
- [examples/configure_nginx.yml](examples/configure_nginx.yml)
- [examples/create_users.yml](examples/create_users.yml)
- [examples/manage_services.yml](examples/manage_services.yml)
- [examples/copy_files.yml](examples/copy_files.yml)
- [examples/create_directories.yml](examples/create_directories.yml)
- [examples/execute_shell_commands.yml](examples/execute_shell_commands.yml)
- [examples/setup_docker.yml](examples/setup_docker.yml)
- [examples/configure_ec2.yml](examples/configure_ec2.yml)
- [examples/aws_ec2_inventory.yml](examples/aws_ec2_inventory.yml)
- [examples/aws_provisioning.yml](examples/aws_provisioning.yml)
- [examples/deploy_application.yml](examples/deploy_application.yml)

## Recommended practical learning sequence
1. Learn Ansible basics and the execution model.
2. Practice inventory and variables on a small Linux fleet.
3. Write simple playbooks for packages, services, users, and files.
4. Move to roles and collections.
5. Add templates, handlers, and Vault to production-quality workflows.
6. Explore AWS automation using `amazon.aws` and dynamic inventory.
7. Practice troubleshooting and interview-style scenario answers.

## Quick start
```bash
ansible --version
ansible-inventory --list -i inventory.ini
ansible-playbook site.yml --syntax-check
ansible all -m ping
```
