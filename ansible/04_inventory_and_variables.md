# 04. Inventory and Variables

## What is inventory?
Inventory is the list of managed hosts and their connection details. It defines which servers Ansible manages and how they should be accessed.

## Static vs dynamic inventory
Static inventory is defined in files such as `inventory.ini` or `inventory.yml`. Dynamic inventory is generated at runtime from external systems like AWS EC2, Azure, or CMDBs.

### Static inventory example
```ini
[webservers]
app1 ansible_host=10.0.0.10 ansible_user=ubuntu
app2 ansible_host=10.0.0.11 ansible_user=ubuntu

[dbservers]
db1 ansible_host=10.0.0.20 ansible_user=ec2-user
```
This is easy to read and works well for small and medium environments.

## INI inventory
INI inventory is simple and often used in labs and smaller environments.

```ini
[all:vars]
ansible_python_interpreter=/usr/bin/python3

[webservers]
web1 ansible_host=192.168.1.11 ansible_user=ubuntu
web2 ansible_host=192.168.1.12 ansible_user=ubuntu
```
`all:vars` applies variables to all hosts in the inventory.

## YAML inventory
YAML inventory is cleaner for modern Ansible usage and works well with more complex structures.

```yaml
all:
  vars:
    ansible_python_interpreter: /usr/bin/python3
  children:
    webservers:
      hosts:
        web1:
          ansible_host: 192.168.1.11
          ansible_user: ubuntu
        web2:
          ansible_host: 192.168.1.12
          ansible_user: ubuntu
```
This syntax groups hosts and variables in a structured way.

## Parent and child groups
Groups can be nested. A parent group contains child groups or hosts, and values can inherit downward.

```ini
[all:children]
webservers
appservers

[webservers]
web1
web2
```
This allows playbooks to target large groups while keeping host-level grouping logical.

## Host variables and group variables
Variables can be attached to a host or to a group.

### Host variables
```ini
[webservers]
web1 ansible_host=10.0.0.10 http_port=80
```
### Group variables
```ini
[webservers:vars]
http_port=80
nginx_enabled=true
```
Host variables override group variables when there is a conflict.

## `all` and `ungrouped`
`all` is a special group that includes every host. `ungrouped` contains hosts that do not belong to a named group.

```bash
ansible all -m ping
ansible ungrouped -m ping
```
Use these patterns when testing inventory coverage.

## Variable precedence
Ansible resolves variables using precedence rules. The order is usually:
1. Extra vars (`-e`)
2. Task vars
3. Block vars
4. Role defaults
5. Inventory variables
6. Facts

This means command-line variables often override orchestration defaults.

## `group_vars` and `host_vars`
These directories are widely used to store variables by group or host.

```text
inventory/
├── group_vars/
│   └── webservers.yml
├── host_vars/
│   └── web1.yml
└── inventory.ini
```
Example `group_vars/webservers.yml`:
```yaml
nginx_enabled: true
web_root: /var/www/html
```
Example `host_vars/web1.yml`:
```yaml
ansible_user: ubuntu
```

## Facts
Facts are information gathered about a host, such as OS distribution, memory, architecture, and network interfaces.

```bash
ansible webservers -m setup
```
This prints the gathered facts for all web servers.

## Magic variables
Magic variables are built-in variables provided by Ansible, such as `hostvars`, `groups`, `inventory_hostname`, and `playbook_dir`.

```yaml
- name: Print host groups
  ansible.builtin.debug:
    msg: "{{ groups['webservers'] }}"
```
This helps create dynamic, inventory-aware logic.

## Extra variables
Extra variables are passed at runtime with `-e` or `--extra-vars`.

```bash
ansible-playbook deploy.yml -e "release_version=2.1.0 environment=prod"
```
This is helpful for environment-specific values in CI/CD.

## Ansible Vault variables
Vault stores secret values in encrypted form. Use it for passwords, API keys, and private tokens.

```bash
ansible-vault create group_vars/all/vault.yml
```
Then reference the variable in a playbook.

## AWS EC2 inventory example
Use a static inventory for simple AWS environments or dynamic inventory for large fleets.

```ini
[webservers]
web1 ansible_host=54.12.34.56 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/aws-key.pem
web2 ansible_host=54.12.34.57 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/aws-key.pem
```
When you manage multiple EC2 instances, use their public IPs for SSH from the internet and private IPs for internal communication within the VPC.

## Managing multiple EC2 instances
For EC2 machines, the SSH parameters often look like this:
```ini
[aws_ec2]
app1 ansible_host=3.123.45.67 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/app.pem
app2 ansible_host=3.123.45.68 ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/app.pem
```
This pattern is common when different Linux distributions use different SSH usernames.

## Important note on AWS inventory
For production environments, prefer dynamic inventory with `amazon.aws.aws_ec2`, especially when instance counts vary over time.

```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
filters:
  tag:Environment: dev
```
This automatically fills the inventory from AWS EC2 metadata.

## Summary
Inventory and variables are the backbone of Ansible. A good inventory structure keeps hosts organized, while variable layering makes environments configurable, secure, and reusable.
