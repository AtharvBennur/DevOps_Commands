# 01. Ansible Fundamentals

## What is Ansible?
Ansible is an open-source automation tool from Red Hat that uses simple YAML playbooks to define desired system state. It is widely used for configuration management, application deployment, cloud provisioning, and orchestration.

### Example
```bash
ansible --version
```
This verifies that Ansible is installed and available on the control node.

## Why Ansible is used in DevOps
Ansible reduces manual work and makes environments reproducible. It helps DevOps teams manage multiple servers consistently, deploy faster, and standardize platform configuration.

### Example
```bash
ansible all -m ping
```
This checks that all managed hosts are reachable and responding.

## Configuration management and automation
Configuration management is the practice of defining the desired state of systems and ensuring the system stays in that state. Ansible does this through playbooks and modules.

### Example playbook
```yaml
---
- name: Ensure package is installed
  hosts: webservers
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
```
This ensures nginx exists on every target host in the `webservers` group.

## Agentless architecture
Ansible is agentless: there is no permanent software agent installed on managed nodes. It connects over SSH or WinRM and runs commands/modules remotely through Python or PowerShell.

### Commands
```bash
ansible all -m ping
ssh user@host
```
The first tests connectivity via Ansible; the second confirms the underlying SSH path works.

## Push-based architecture
Ansible follows a push model. The control node pushes configuration to managed hosts, rather than waiting for agents to fetch changes.

### Example
```bash
ansible-playbook deploy.yml -i inventory.ini
```
The control node pushes the playbook instructions to the selected hosts.

## SSH and WinRM
Ansible commonly uses SSH for Linux/Unix hosts and WinRM for Windows servers. The connection method is defined per host or group in inventory.

### Inventory example
```ini
[webservers]
app01 ansible_host=10.0.0.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
[windows]
win01 ansible_host=10.0.0.11 ansible_user=Administrator ansible_connection=winrm
```
This shows both Linux and Windows connection patterns.

## Idempotency
Idempotency means running the same task multiple times produces the same result without unintended drift. Ansible modules are designed to be idempotent.

### Example
```yaml
- name: Ensure directory exists
  ansible.builtin.file:
    path: /opt/myapp
    state: directory
    mode: '0755'
```
If the directory already exists, Ansible does not change it unnecessarily.

## Declarative vs imperative automation
Declarative automation declares the target state. Imperative automation tells the system exactly how to perform a step-by-step task.

### Example
```yaml
- name: Ensure package is installed
  ansible.builtin.apt:
    name: nginx
    state: present
```
This is declarative: the desired state is "nginx installed".

## Ansible vs Terraform vs Chef vs Puppet
| Tool | Best for | Notes |
|---|---|---|
| Ansible | Configuration and orchestration | Simple YAML, agentless, easy to start |
| Terraform | Infrastructure provisioning | Declarative IaC for cloud resources |
| Chef | Configuration management | Ruby-based, agent-driven |
| Puppet | Enterprise configuration management | Agent-based, policy-driven |

### Practical fit
Ansible is often used after Terraform provisions infrastructure and before application deployment automation begins.

## Where Ansible fits in a DevOps pipeline
Ansible fits well in CI/CD pipelines for provisioning, configuration, app deployment, and post-deploy checks.

### Example flow
```bash
terraform apply
ansible-playbook -i inventory.ini configure.yml
ansible-playbook -i inventory.ini deploy.yml
```
This sequence is common: provision infrastructure, configure hosts, deploy apps.

## Summary
Ansible is a simple, agentless, YAML-driven automation tool built for repeatable infrastructure management and deployments. It is especially effective for Linux environments and AWS automation.
