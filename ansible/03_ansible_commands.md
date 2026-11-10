# 03. Ansible Command Reference

This section focuses on the most useful Ansible commands for day-to-day DevOps work. Every command below includes the syntax, a brief explanation, and a realistic example.

## Installation and verification
Ansible must be installed on the control node before it can manage remote hosts.

| Command (Key) | Explanation (Value) |
|---|---|
| `sudo apt update && sudo apt install ansible` | Installs Ansible on Ubuntu/Debian. This is the standard package-based installation for Linux hosts. Example: `sudo apt install ansible`. |
| `ansible --version` | Shows installed Ansible version and Python version. Use it to confirm the environment is ready. |
| `python3 --version` | Verifies Python is available because Ansible depends on Python for module execution. |
| `ansible-config --version` | Confirms the Ansible configuration tool is installed and accessible. |
| `ansible-config list` | Lists all available configuration entries. Useful for checking supported settings and default keys. |

## Configuration and settings
Ansible reads settings from `ansible.cfg`, environment variables, and CLI options.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-config list` | Lists config options. Example: `ansible-config list | grep inventory`. |
| `ansible-config dump` | Prints the effective configuration in use, including defaults and overrides. Example: `ansible-config dump --only-changed`. |
| `ansible-config view` | Displays the active configuration file content. Example: `ansible-config view`. |

## Inventory commands
Inventory defines the managed hosts and groups.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-inventory --list -i inventory.ini` | Lists all inventory entries as JSON. Use it to verify host/group definitions. |
| `ansible-inventory --graph -i inventory.ini` | Displays a host/group tree. Good for explaining inventory relationships. |
| `ansible-inventory --host app1 -i inventory.ini` | Shows variables for a specific host. Example: `ansible-inventory --host app1 -i inventory.ini`. |

## Connectivity and facts
Connectivity checks ensure managed nodes are reachable and usable.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible all -m ping` | Tests SSH connectivity and Python execution on all hosts. Example: `ansible webservers -m ping`. |
| `ansible all -m setup` | Gathers Ansible facts from target hosts. Use it for variable debugging. |
| `ssh -i ~/.ssh/id_rsa ubuntu@10.0.0.10` | Verifies the underlying SSH connection manually. Use this if Ansible ping fails. |
| `ssh -o StrictHostKeyChecking=no -i key.pem ubuntu@host` | Allows testing without host-key prompts. Usually for labs or ephemeral test hosts. |

## Ad-hoc commands
Ad-hoc commands are useful for quick actions without writing a full playbook.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible all -m command -a "uname -a"` | Executes a shell command without using a shell. Use for simple commands that do not need shell expansion. |
| `ansible all -m shell -a "echo hello > /tmp/test.txt"` | Runs shell commands with shell features like pipes and redirection. Use when shell syntax is needed. |
| `ansible all -m copy -a "src=/tmp/file.txt dest=/tmp/file.txt"` | Copies files to managed hosts. Example: `ansible webservers -m copy -a "src=./app.conf dest=/etc/app.conf"`. |
| `ansible all -m apt -a "name=nginx state=present"` | Installs packages on Debian/Ubuntu hosts. Equivalent to `apt-get`. |
| `ansible all -m yum -a "name=httpd state=present"` | Installs or upgrades packages on RHEL/CentOS/AMI-managed hosts. |
| `ansible all -m service -a "name=nginx state=restarted"` | Starts, stops, or restarts services. Useful for service management. |
| `ansible all -m file -a "path=/opt/demo state=directory mode=0755"` | Ensures a file or directory exists with the desired permissions. |

## Playbook execution
Playbooks are the primary Ansible workflow for automation.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-playbook playbook.yml` | Runs a playbook on the selected inventory and hosts. Example: `ansible-playbook site.yml -i inventory.ini`. |
| `ansible-playbook playbook.yml --check` | Performs a dry run and reports changes without applying them. Use for validation. |
| `ansible-playbook playbook.yml --diff` | Displays differences for files changed by tasks. Very useful for config drift review. |
| `ansible-playbook playbook.yml --syntax-check` | Validates the YAML syntax and structure before execution. |
| `ansible-playbook playbook.yml --list-hosts` | Shows which hosts will be targeted. Good for sanity checks. |
| `ansible-playbook playbook.yml --list-tasks` | Lists tasks in the playbook without running them. |
| `ansible-playbook playbook.yml --start-at-task "Install nginx"` | Starts execution at a specific task, useful for debugging. |
| `ansible-playbook playbook.yml --tags app,web` | Runs only tasks tagged with the given labels. |
| `ansible-playbook playbook.yml --skip-tags debug` | Skips tasks with the provided tags. |
| `ansible-playbook playbook.yml --limit webservers` | Runs only on a subset of hosts. |
| `ansible-playbook playbook.yml -e "app_version=1.2.3"` | Passes extra variables at runtime. |
| `ansible-playbook -v playbook.yml` | Verbose output. Use `-vv` or `-vvv` for deeper debugging. |

## Galaxy commands
Ansible Galaxy is the community hub for roles and collections.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-galaxy collection install amazon.aws` | Installs the AWS collection. This is required for AWS automation modules. |
| `ansible-galaxy collection list` | Lists installed collections and versions. |
| `ansible-galaxy role install geerlingguy.nginx` | Installs a community role from Galaxy. |
| `ansible-galaxy role list` | Lists installed roles. |
| `ansible-galaxy init myrole` | Creates a new role skeleton using the standard layout. |

## Vault commands
Vault protects secrets stored in YAML or files.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-vault create secrets.yml` | Creates an encrypted YAML file. Useful for sensitive variables. |
| `ansible-vault edit secrets.yml` | Opens an encrypted file for editing with vault password prompt. |
| `ansible-vault encrypt vars.yml` | Encrypts an existing plain file. |
| `ansible-vault decrypt vars.yml` | Decrypts a vault file to plain text. |
| `ansible-vault view secrets.yml` | Shows the decrypted content without editing. |
| `ansible-vault rekey secrets.yml` | Changes the vault password for an existing file. |
| `ansible-vault encrypt_string 'password123' --name 'db_password'` | Encrypts a single value for use in a playbook or variable file. |

## Debugging and documentation
These commands help troubleshoot and understand module interfaces.

| Command (Key) | Explanation (Value) |
|---|---|
| `ansible-doc apt` | Shows documentation for a built-in module. Useful when you need parameter syntax. |
| `ansible-doc amazon.aws.ec2_instance` | Shows docs for an AWS collection module. |
| `ansible-playbook playbook.yml -vvv` | Provides maximum detail for troubleshooting failed tasks or connection issues. |
| `ansible all -m setup -a "filter=ansible_distribution"` | Gathers only targeted fact data. Use it for quick environment inspection. |

## Other useful commands
| Command (Key) | Explanation (Value) |
|---|---|
| `ansible all -m raw -a "whoami"` | Executes a raw command without relying on Python. Useful for early bootstrap. |
| `ansible all -m user -a "name=appuser state=present"` | Creates a user on all target hosts. |
| `ansible all -m cron -a "name='job' minute='*/15' job='/usr/bin/uptime'"` | Creates a cron deployment schedule. |
| `ansible-playbook playbook.yml --ask-vault-pass` | Prompts for vault password at runtime when secrets are encrypted. |
| `ansible-playbook playbook.yml --become-password-file /path/to/password` | Supplies a sudo password file for become operations. |

## Quick command examples
```bash
ansible all -m ping
ansible webservers -m apt -a "name=nginx state=present"
ansible-playbook site.yml --check --diff
ansible-galaxy collection install amazon.aws
ansible-vault encrypt vars/secrets.yml
```
These commands cover basic connectivity, package management, validation, AWS automation, and secret protection.
