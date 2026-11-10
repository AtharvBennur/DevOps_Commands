# 11. Ansible Troubleshooting Guide

| Error / Problem | Likely Cause | Solution / Commands |
|---|---|---|
| SSH connection failure | Host unreachable or bad key | `ssh -i ~/.ssh/key.pem ubuntu@host` and verify `ansible_host`, `ansible_user`, and `ansible_ssh_private_key_file`. |
| Permission denied | Wrong SSH user or invalid key permissions | `chmod 600 ~/.ssh/id_rsa`; check `ansible_user` and `-i` key path. |
| Incorrect PEM permissions | Private key readable by group/other | `chmod 600 ~/.ssh/mykey.pem` |
| Incorrect SSH username | AWS Linux uses `ec2-user`; Ubuntu uses `ubuntu` | Update inventory to correct username. |
| Host key verification failure | Known hosts mismatch or new host | `ssh-keygen -R server.example.com` or `ssh-keyscan host >> ~/.ssh/known_hosts`. |
| Connection timeout | Firewall, security group, VPC routing issue | `nc -vz host 22`, check AWS security groups and route tables. |
| Python interpreter errors | Target host missing Python or wrong interpreter path | `ansible all -m setup` and configure `ansible_python_interpreter` if needed. |
| Missing Python dependencies | Module needs Python packages | `python3 -m pip install boto3 botocore` for AWS; install system packages as required. |
| YAML syntax errors | Bad indentation or unquoted special characters | `ansible-playbook playbook.yml --syntax-check` |
| Undefined variables | Missing vars or inventory values | `ansible-playbook playbook.yml -e @vars.yml` or debug with `ansible.builtin.debug`. |
| Module not found | Missing collection or wrong module name | `ansible-doc <module>` and `ansible-galaxy collection install <collection>`. |
| Collection not installed | Required collection absent | `ansible-galaxy collection list` and `ansible-galaxy collection install amazon.aws`. |
| AWS authentication failures | Invalid credentials or expired tokens | `aws sts get-caller-identity`; validate `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and region. |
| boto3 / botocore issues | Wrong Python environment or package version mismatch | `python3 -m pip show boto3 botocore`; reinstall as needed. |
| Inventory problems | Host not in inventory or bad group naming | `ansible-inventory --list -i inventory.ini` |
| Unreachable hosts | Host offline, DNS issue, or port blocked | `ansible all -m ping` and confirm SSH/WinRM connectivity. |
| Failed tasks | Incorrect module parameters or state mismatch | Run with `-vvv`, inspect `stderr`, and verify module docs. |

## Useful troubleshooting commands
```bash
ansible all -m ping -vvv
ansible-playbook site.yml --syntax-check
ansible-playbook site.yml -vvv
ansible-inventory --list -i inventory.ini
ansible-doc amazon.aws.ec2_instance
```
These commands help isolate common issues quickly.

## Check inventory and groups
```bash
ansible-inventory --graph -i inventory.ini
ansible-inventory --host web1 -i inventory.ini
```
This confirms the inventory is correct before running playbooks.

## Debugging failed tasks
Use `register`, `debug`, and `-vvv` to trace values and causes.

```yaml
- name: Show value
  ansible.builtin.debug:
    var: result
```
This helps see what a task is receiving and returning.

## Summary
Ansible issues are usually caused by connectivity, inventory, variable, or module mismatch problems. Clear commands such as `ansible-playbook -vvv` and `ansible-inventory --list` save time and reduce downtime.
