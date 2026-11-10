# 08. Ansible Roles and Collections

## What is a role?
A role is a reusable directory structure for tasks, handlers, templates, files, defaults, metadata, and vars. Roles keep playbooks maintainable and modular.

## Why roles are used
Roles solve complexity by organizing related automation into reusable building blocks. Instead of one big playbook, teams split tasks into role directories.

## Typical role structure
```text
roles/
├── nginx/
│   ├── defaults/
│   │   └── main.yml
│   ├── files/
│   ├── handlers/
│   │   └── main.yml
│   ├── tasks/
│   │   └── main.yml
│   ├── templates/
│   │   └── nginx.conf.j2
│   ├── vars/
│   │   └── main.yml
│   └── meta/
│       └── main.yml
```
This structure encourages clean separation of logic and configuration.

## Role execution
A role runs as part of a play through the `roles` list.

```yaml
---
- hosts: webservers
  become: true
  roles:
    - common
    - nginx
    - app
```
Each role may provide tasks, handlers, templates, and variables.

## Creating a role
```bash
ansible-galaxy init nginx
```
This creates the standard role skeleton.

## Installing a role from Galaxy
```bash
ansible-galaxy role install geerlingguy.nginx
```
Community roles can save effort and improve consistency.

## Listing installed roles
```bash
ansible-galaxy role list
```
This shows the currently installed roles in the Ansible role path.

## Collections vs roles
| Item | Scope | Typical use |
|---|---|---|
| Role | Single service or reusable playbook component | App stack pieces, service setup |
| Collection | Multi-module package | AWS modules, vendor integrations, plugins |

A collection can include roles, but a role does not inherently include remote modules or plugin integration.

## Example role tasks
```yaml
# roles/nginx/tasks/main.yml
---
- name: Install nginx
  ansible.builtin.apt:
    name: nginx
    state: present

- name: Deploy nginx config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: restart nginx
```
This task file is the logic executed when the role runs.

## Defaults and vars
Defaults are low-priority values, while vars can override defaults when needed.

```yaml
# roles/nginx/defaults/main.yml
nginx_port: 80
```
```yaml
# roles/nginx/vars/main.yml
nginx_port: 8080
```
The `vars` file is usually higher priority than defaults.

## Handlers and templates
Handlers and templates belong inside the role and are invoked by tasks when configuration changes.

```yaml
# roles/nginx/handlers/main.yml
---
- name: restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```
This is used when the configuration changes and the service needs a restart.

## Dependencies
Roles can depend on other roles via `meta/main.yml`.

```yaml
# roles/myapp/meta/main.yml
---
dependencies:
  - role: common
    vars:
      app_user: myapp
```
This allows one role to reuse another role's setup.

## Collections
Collections are distributed packages that include modules and plugins in namespaces such as `amazon.aws` and `community.general`. They are the modern way to package reusable automation logic.

## Installing a collection
```bash
ansible-galaxy collection install amazon.aws
```
This repository often uses `amazon.aws` for EC2, IAM, S3, and Route53 tasks.

## Listing installed collections
```bash
ansible-galaxy collection list
```
This helps confirm the environment is ready for AWS automation.

## Summary
Roles organize reusable automation logic. Collections package modules and plugins into namespaces. Together they provide a scalable structure for enterprise Ansible automation.
