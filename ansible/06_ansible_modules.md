# 06. Ansible Modules Reference

This section gives the most commonly used Ansible modules with purpose, key parameters, and sample YAML.

| Module Name | Purpose | Important Parameters | YAML Example |
|---|---|---|---|
| `ping` | Verifies connectivity and Python execution on a managed host. | `data` (optional) | `- ansible.builtin.ping:` |
| `setup` | Gathers facts about the target host. | `filter`, `gather_subset` | `- ansible.builtin.setup:` |
| `debug` | Prints variable or message output. | `var`, `msg` | `- ansible.builtin.debug: msg="Hello"` |
| `command` | Runs a command without shell features. | `argv`, `creates`, `removes` | `- ansible.builtin.command: cmd="uname -a"` |
| `shell` | Runs commands through a shell. | `cmd`, `chdir`, `executable` | `- ansible.builtin.shell: cmd="echo hello > /tmp/file"` |
| `raw` | Runs a raw command before Python is installed. | `cmd` | `- ansible.builtin.raw: cmd="whoami"` |
| `script` | Copies a script to remote hosts and executes it. | `chdir`, `creates`, `removes` | `- ansible.builtin.script: script.sh` |
| `copy` | Copies files from control node to managed hosts. | `src`, `dest`, `mode`, `content` | `- ansible.builtin.copy: src=app.conf dest=/etc/app.conf` |
| `fetch` | Copies a file from managed host back to control node. | `src`, `dest`, `flat` | `- ansible.builtin.fetch: src=/tmp/log.txt dest=/tmp/` |
| `file` | Manages files and directories. | `path`, `state`, `mode`, `owner`, `group` | `- ansible.builtin.file: path=/tmp/demo state=directory` |
| `template` | Renders a Jinja2 template to a file. | `src`, `dest`, `mode`, `owner` | `- ansible.builtin.template: src=nginx.conf.j2 dest=/etc/nginx/nginx.conf` |
| `lineinfile` | Ensures a line exists in a file. | `path`, `line`, `state`, `regexp` | `- ansible.builtin.lineinfile: path=/etc/hosts line="10.0.0.10 app"` |
| `blockinfile` | Inserts or updates a block of text in a file. | `path`, `block`, `marker` | `- ansible.builtin.blockinfile: ...` |
| `replace` | Replaces text in a file based on regex. | `path`, `regexp`, `replace` | `- ansible.builtin.replace: ...` |
| `apt` | Installs or removes Debian packages. | `name`, `state`, `update_cache` | `- ansible.builtin.apt: name=nginx state=present` |
| `yum` | Manages RPM packages on RHEL/CentOS. | `name`, `state`, `disable_gpg_check` | `- ansible.builtin.yum: name=httpd state=present` |
| `dnf` | Manages RPM packages on newer RHEL/Fedora. | `name`, `state` | `- ansible.builtin.dnf: name=httpd state=present` |
| `package` | Generic package manager abstraction. | `name`, `state` | `- ansible.builtin.package: name=git state=present` |
| `service` | Manages services. | `name`, `state`, `enabled` | `- ansible.builtin.service: name=nginx state=restarted` |
| `systemd_service` | Manages systemd services with more explicit semantics. | `name`, `state`, `enabled` | `- ansible.builtin.systemd: name=nginx state=restarted` |
| `user` | Creates or updates users. | `name`, `state`, `groups`, `shell`, `password` | `- ansible.builtin.user: name=appuser state=present` |
| `group` | Creates or manages groups. | `name`, `state` | `- ansible.builtin.group: name=appgroup state=present` |
| `cron` | Creates and manages cron jobs. | `name`, `minute`, `hour`, `job` | `- ansible.builtin.cron: name=cleanup hour=2 job='/tmp/cleanup.sh'` |
| `git` | Clones or updates repositories. | `repo`, `dest`, `version` | `- ansible.builtin.git: repo=https://... dest=/opt/app` |
| `unarchive` | Extracts archives. | `src`, `dest`, `remote_src` | `- ansible.builtin.unarchive: src=app.tar.gz dest=/opt/` |
| `archive` | Creates archives. | `path`, `dest`, `format` | `- ansible.builtin.archive: path=/opt/app dest=/tmp/app.tar.gz` |
| `uri` | Performs HTTP requests. | `url`, `method`, `status_code`, `body` | `- ansible.builtin.uri: url=https://example.com` |
| `get_url` | Downloads files from URLs. | `url`, `dest`, `mode` | `- ansible.builtin.get_url: url=... dest=/tmp/file` |
| `stat` | Gathers file status details. | `path`, `get_checksum` | `- ansible.builtin.stat: path=/etc/nginx/nginx.conf` |
| `assert` | Validates an expression or condition. | `that` | `- ansible.builtin.assert: that: - ansible_os_family == 'Debian'` |
| `fail` | Fails a task intentionally. | `msg` | `- ansible.builtin.fail: msg="Required variable missing"` |
| `wait_for` | Waits for a port, file, or condition. | `host`, `port`, `timeout` | `- ansible.builtin.wait_for: host=127.0.0.1 port=80 timeout=30` |
| `mount` | Manages fstab and mounts. | `path`, `src`, `fstype`, `state` | `- ansible.builtin.mount: path=/mnt/data src=/dev/xvdf fstype=ext4 state=mounted` |
| `authorized_key` | Manages SSH authorized keys. | `user`, `key`, `state` | `- ansible.builtin.authorized_key: user=ubuntu key='ssh-rsa ...'` |
| `known_hosts` | Manages SSH known_hosts entries. | `name`, `key`, `path` | `- ansible.builtin.known_hosts: name=github.com key=...` |
| `hostname` | Sets the system hostname. | `name` | `- ansible.builtin.hostname: name=web01` |
| `reboot` | Reboots the host. | `reboot_timeout` | `- ansible.builtin.reboot: reboot_timeout=600` |
| `meta` | Runs special Ansible behavior such as `flush_handlers`. | `flush_handlers` | `- ansible.builtin.meta: flush_handlers` |

## Command vs shell vs raw
These modules are easy to confuse:

- `command`: no shell, safer, faster, but no shell features.
- `shell`: runs through a shell; supports pipes and redirection.
- `raw`: used for bootstrapping when Python is not yet present.

### Example
```yaml
- name: Show command behavior
  ansible.builtin.command: cmd="echo hello"

- name: Use shell features
  ansible.builtin.shell: cmd="echo hello | tee /tmp/hello.txt"

- name: Bootstrap with raw
  ansible.builtin.raw: cmd="python3 --version || python --version"
```

## Important usage notes
- Prefer `command` over `shell` when shell features are not needed.
- Use `file` and `template` for configuration management instead of inline echo commands.
- Keep tasks idempotent and declarative.
- Use `notify`/handlers rather than ad-hoc restarts in many cases.

## Summary
Ansible modules are the building blocks of automation. Choose the simplest safe module for the task, and prefer idempotent modules whenever possible.
