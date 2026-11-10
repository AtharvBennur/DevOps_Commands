# 12. Real-World DevOps Tasks

## 1. Installing packages on multiple servers
### Requirement
Install packages on several Linux hosts consistently.

### Approach
Use a package module and target a group such as `webservers`.

```yaml
---
- name: Install packages on webservers
  hosts: webservers
  become: true
  tasks:
    - name: Install common packages
      ansible.builtin.apt:
        name:
          - nginx
          - git
          - curl
        state: present
        update_cache: true
```
### Execution
```bash
ansible-playbook install_packages.yml -i inventory.ini
```

## 2. Installing and configuring Nginx
### Requirement
Deploy and configure NGINX on Ubuntu servers.

```yaml
---
- name: Configure nginx
  hosts: webservers
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Deploy config
      ansible.builtin.template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: reload nginx

  handlers:
    - name: reload nginx
      ansible.builtin.service:
        name: nginx
        state: reloaded
```

## 3. Starting, stopping, and restarting services
```yaml
---
- name: Manage service state
  hosts: all
  become: true
  tasks:
    - name: Ensure nginx is started
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

## 4. Creating users and groups
```yaml
---
- name: Manage users
  hosts: all
  become: true
  tasks:
    - name: Ensure group exists
      ansible.builtin.group:
        name: apps
        state: present

    - name: Ensure user exists
      ansible.builtin.user:
        name: appuser
        state: present
        group: apps
        shell: /bin/bash
```

## 5. Copying files
```yaml
---
- name: Copy app files
  hosts: webservers
  become: true
  tasks:
    - name: Copy config file
      ansible.builtin.copy:
        src: ./files/app.conf
        dest: /etc/myapp/app.conf
        mode: '0644'
```

## 6. Creating directories
```yaml
---
- name: Create directories
  hosts: all
  become: true
  tasks:
    - name: Ensure app directory exists
      ansible.builtin.file:
        path: /opt/myapp/releases
        state: directory
        mode: '0755'
```

## 7. Changing file permissions
```yaml
---
- name: Fix permissions
  hosts: all
  become: true
  tasks:
    - name: Set script executable
      ansible.builtin.file:
        path: /opt/myapp/deploy.sh
        mode: '0755'
```

## 8. Updating configuration files
```yaml
---
- name: Update config
  hosts: webservers
  become: true
  tasks:
    - name: Replace default config
      ansible.builtin.template:
        src: app.conf.j2
        dest: /etc/myapp/app.conf
```

## 9. Running shell commands
```yaml
---
- name: Run shell commands
  hosts: all
  become: true
  tasks:
    - name: Print system info
      ansible.builtin.shell: cmd="uname -a"
```

## 10. Installing Docker
```yaml
---
- name: Install Docker
  hosts: all
  become: true
  tasks:
    - name: Install prerequisites
      ansible.builtin.apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - gnupg
        state: present
```

## 11. Deploying an application
```yaml
---
- name: Deploy application
  hosts: webservers
  become: true
  tasks:
    - name: Clone application repo
      ansible.builtin.git:
        repo: https://github.com/example/app.git
        dest: /opt/myapp
        version: main
```

## 12. Configuring EC2 instances
```yaml
- name: Configure EC2 instance
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Launch EC2
      amazon.aws.ec2_instance:
        region: us-east-1
        image_id: ami-12345678
        instance_type: t3.micro
        key_name: my-key
        vpc_subnet_id: subnet-12345678
        security_group: sg-12345678
        wait: true
```

## 13. Gathering EC2 public IPs
```yaml
- name: Gather EC2 public IPs
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Get EC2 instance info
      amazon.aws.ec2_instance_info:
        region: us-east-1
      register: ec2_info

    - name: Show public IPs
      ansible.builtin.debug:
        msg: "{{ ec2_info.instances | map(attribute='public_ip_address') | list }}"
```

## 14. Generating an EC2 IP report in a text file
```yaml
- name: Create EC2 report
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Get EC2 instances
      amazon.aws.ec2_instance_info:
        region: us-east-1
      register: ec2_info

    - name: Write report
      ansible.builtin.copy:
        content: "{{ ec2_info.instances | map(attribute='public_ip_address') | list | join('\n') }}\n"
        dest: /tmp/ec2-public-ips.txt
```

## 15. Configuring multiple servers with different usernames
```ini
[webservers]
web1 ansible_host=3.122.44.12 ansible_user=ubuntu
web2 ansible_host=3.122.44.13 ansible_user=ec2-user
```
This pattern is common when you manage Ubuntu and Amazon Linux instances together.

## 16. Running playbooks against selected hosts
```bash
ansible-playbook site.yml --limit webservers
ansible-playbook site.yml --limit web1
```
This targets only selected hosts instead of the full fleet.

## 17. Updating packages
```yaml
---
- name: Update packages
  hosts: all
  become: true
  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: true
```

## 18. Rebooting servers
```yaml
---
- name: Reboot servers
  hosts: all
  become: true
  tasks:
    - name: Reboot host
      ansible.builtin.reboot:
        reboot_timeout: 600
```

## 19. Creating cron jobs
```yaml
---
- name: Create cron job
  hosts: all
  become: true
  tasks:
    - name: Add log cleanup cron
      ansible.builtin.cron:
        name: cleanup-logs
        minute: '0'
        hour: '2'
        job: '/usr/bin/find /var/log -type f -mtime +7 -delete'
```

## Summary
These real-world tasks are the building blocks of production automation. The key is to combine modules, variables, and templates into repeatable, idempotent playbooks.
