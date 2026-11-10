# 09. Ansible Templates and Handlers

## Jinja2 templating
Ansible uses Jinja2 templates for dynamic file generation. Templates allow variables, conditions, and loops to render configuration files.

### Example template
```jinja2
server {
    listen {{ nginx_port }};
    server_name {{ inventory_hostname }};
    root {{ web_root }};
}
```
This file will render with real values when the template module executes.

## Variables inside templates
Templates can use facts, inventory variables, and play variables.

```yaml
- name: Deploy nginx config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    mode: '0644'
```
This results in a rendered configuration file on the target host.

## Conditions and loops in templates
Templates can include logic such as loops and conditional blocks.

```jinja2
{% for server in upstream_servers %}
server {{ server }};
{% endfor %}
```
This is common in NGINX or HAProxy configuration templates.

## Template module
`ansible.builtin.template` is the standard module for rendering Jinja2 files.

```yaml
- name: Template app config
  ansible.builtin.template:
    src: app.conf.j2
    dest: /etc/myapp/app.conf
```
This uses the Jinja2 template engine to produce the final file.

## Handlers
Handlers are tasks triggered only when notified by another task. They are ideal for service restarts and reloads.

```yaml
- name: Copy nginx config
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: reload nginx
```
This notifies the handler only if the file changed.

## `notify`
`notify` calls a handler when a task changes the managed system state.

```yaml
handlers:
  - name: reload nginx
    ansible.builtin.service:
      name: nginx
      state: reloaded
```
Only changed files trigger the service reload.

## `listen`
`listen` provides a named handler group, useful when multiple tasks notify the same handler.

```yaml
handlers:
  - name: restart web services
    listen: "restart web"
    ansible.builtin.service:
      name: nginx
      state: restarted
```
This allows easy notification by a logical name instead of a single handler name.

## When handlers execute
Handlers run at the end of a play after all tasks are completed, unless `meta: flush_handlers` is used earlier.

```yaml
- ansible.builtin.meta: flush_handlers
```
This is useful when the next task depends on the service being restarted immediately.

## Practical NGINX example
### Template file `templates/nginx.conf.j2`
```jinja2
server {
    listen {{ nginx_port }};
    server_name {{ inventory_hostname }};
    root {{ web_root }};
    index index.html;
}
```
### Playbook
```yaml
---
- name: Configure nginx
  hosts: webservers
  become: true
  vars:
    nginx_port: 80
    web_root: /var/www/html
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Deploy config template
      ansible.builtin.template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: reload nginx

  handlers:
    - name: reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded
```
This pattern is used often in production for configuration management.

## Summary
Templates generate dynamic configuration files, and handlers ensure services restart or reload only when needed. Together they form a clean, idempotent configuration workflow.
