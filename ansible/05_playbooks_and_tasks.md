# 05. Playbooks and Tasks

## YAML fundamentals for Ansible
Ansible playbooks are written in YAML. Indentation matters, and each play is a dictionary or list item.

```yaml
---
- name: Example play
  hosts: webservers
  become: true
  tasks:
    - name: Install package
      ansible.builtin.apt:
        name: nginx
        state: present
```
This example shows the basic shape of a valid Ansible playbook.

## Playbook structure
A playbook is usually composed of one or more plays. A play defines target hosts, variables, and tasks.

```yaml
---
- name: Base server config
  hosts: all
  become: true
  vars:
    package_name: nginx
  tasks:
    - name: Install package
      ansible.builtin.apt:
        name: "{{ package_name }}"
        state: present
```
This shows variables inside a play.

## Hosts
`hosts` defines the target machines or groups. It can be a group name, a literal host, or a pattern like `webservers:!dbservers`.

```yaml
- hosts: webservers
```
This limits the play to the `webservers` group.

## `become` and `become_user`
`become: true` allows tasks to run as root or a privileged account. `become_user` changes the user context for the task.

```yaml
- name: Install package as root
  hosts: all
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
```
This is common for package installation and system changes.

## `vars`
Variables can be declared inline in a play.

```yaml
vars:
  app_dir: /var/www/app
  app_user: appuser
```
This keeps values centralized and easy to modify.

## `vars_files`
You can separate secrets or configuration into variable files.

```yaml
vars_files:
  - vars/common.yml
  - vars/secrets.yml
```
This is useful for environments, credentials, and reusable defaults.

## Loops
Loops help you apply the same task to multiple items.

```yaml
- name: Install multiple packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
    - curl
```
This is shorter and more maintainable than writing repetitive tasks.

## Conditions
Conditions let tasks run only when a condition is true.

```yaml
- name: Install nginx only on Ubuntu
  ansible.builtin.apt:
    name: nginx
    state: present
  when: ansible_os_family == 'Debian'
```
This keeps tasks environment-aware.

## `register`
`register` stores the result of a task for later use.

```yaml
- name: Check if package is installed
  ansible.builtin.command: dpkg -s nginx
  register: nginx_check
  failed_when: nginx_check.rc not in [0, 1]
```
The result can be used in later conditions or debug output.

## `when`
`when` is the primary conditional mechanism in Ansible.

```yaml
- name: Create user if not exists
  ansible.builtin.user:
    name: appuser
    state: present
  when: inventory_hostname in groups['webservers']
```
This runs only on selected host types.

## `failed_when` and `changed_when`
These override default result interpretation.

```yaml
- name: Check service health
  ansible.builtin.command: systemctl is-active nginx
  register: nginx_status
  failed_when: nginx_status.rc not in [0, 3]
  changed_when: false
```
This is useful when the command is meant as a check, not a mutating action.

## `block`, `rescue`, and `always`
Blocks group tasks and allow error-handling flow.

```yaml
- name: Install application
  block:
    - name: Create directory
      ansible.builtin.file:
        path: /opt/myapp
        state: directory
  rescue:
    - name: Log failure
      ansible.builtin.debug:
        msg: 'Application installation failed'
  always:
    - name: Print final status
      ansible.builtin.debug:
        msg: 'Execution finished'
```
This pattern is very useful for robust automation.

## Tags
Tags allow selective task or play execution.

```yaml
- name: Install app dependencies
  ansible.builtin.apt:
    name: npm
    state: present
  tags:
    - install
    - app
```
This enables `--tags` and `--skip-tags` during playbook runs.

## Handlers
Handlers run when notified by a task.

```yaml
- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
  notify: reload nginx
```
The handler is triggered only when a relevant change occurs.

## Roles usage
Roles are a clean way to organize playbook logic.

```yaml
---
- hosts: webservers
  roles:
    - common
    - nginx
    - app
```
This keeps tasks, files, templates, and vars structured.

## Example full playbook
```yaml
---
- name: Configure web server
  hosts: webservers
  become: true
  vars:
    app_user: appuser
  tasks:
    - name: Ensure group exists
      ansible.builtin.group:
        name: "{{ app_user }}"
        state: present

    - name: Ensure user exists
      ansible.builtin.user:
        name: "{{ app_user }}"
        state: present
        group: "{{ app_user }}"

    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
      notify: restart nginx

  handlers:
    - name: restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```
This example uses variables, user creation, package installation, and a handler.

## Summary
Playbooks combine hosts, tasks, variables, loops, conditionals, handlers, and roles into reproducible automation. Writing clean, idempotent playbooks is the core of practical Ansible work.
