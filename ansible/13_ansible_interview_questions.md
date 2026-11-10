# 13. Ansible Interview Questions

## Beginner
### Q1. What is Ansible?
Ansible is a simple, agentless automation tool for configuration management, deployment, and infrastructure automation using YAML playbooks.

### Q2. Why is Ansible widely used in DevOps?
Because it is easy to learn, agentless, idempotent, and suitable for cloud, Linux, and application workflows.

### Q3. What is idempotency?
Idempotency means repeated execution does not create harmful drift or duplicate changes. Ansible modules are designed to be idempotent.

### Q4. What is a playbook?
A playbook is a YAML file that defines one or more plays, each targeting a set of hosts and a list of tasks.

## Intermediate
### Q5. What is the difference between `command` and `shell`?
`command` executes without a shell and is safer for simple commands. `shell` supports shell features such as pipes and redirection.

### Q6. What is inventory?
Inventory is the list of hosts and variables that Ansible manages.

### Q7. How are variables prioritized in Ansible?
Extra vars have highest priority, followed by task/block vars, role defaults, inventory, and facts.

### Q8. What are handlers used for?
Handlers run when a task notifies them, usually for service restarts or reloads.

## Advanced
### Q9. What is a collection?
A collection is a packaged namespace of modules, plugins, and roles. Example: `amazon.aws`.

### Q10. What is a role?
A role is a reusable directory structure for organizing tasks, handlers, templates, files, defaults, and vars.

### Q11. What is dynamic inventory?
Dynamic inventory is generated at runtime from an external source such as AWS EC2 or Azure VM metadata.

### Q12. What is the role of `ansible.cfg`?
`ansible.cfg` stores default settings for inventory, SSH behavior, callback output, and plugin configuration.

## Real-world scenario-based
### Q13. How would you install and configure NGINX on 50 Linux servers?
Use an inventory group, a playbook with `apt`, `template`, and `service` tasks, and a handler to reload or restart NGINX if config changes.

### Q14. How do you handle secrets in Ansible?
Use Ansible Vault or a secret management system like AWS Secrets Manager. Never commit credentials to Git.

### Q15. How do you validate a playbook before running it?
Use `ansible-playbook --syntax-check` and optionally `--check --diff`.

## AWS Ansible
### Q16. What is the AWS Ansible collection?
It is the `amazon.aws` collection, used for AWS provisioning and automation such as EC2, VPC, S3, and IAM-related tasks.

### Q17. Which AWS modules are commonly used?
`amazon.aws.ec2_instance`, `ec2_vpc_net`, `ec2_security_group`, `s3_bucket`, `route53`, and `ec2_instance_info` are common.

### Q18. What is the best AWS auth method for Ansible?
IAM roles are preferred since they avoid storing long-lived credentials in code or local config.

## Troubleshooting
### Q19. What command helps debug failed Ansible tasks?
`ansible-playbook playbook.yml -vvv` is the standard first step.

### Q20. How do you troubleshoot inventory issues?
Use `ansible-inventory --list` and `ansible-inventory --graph` to confirm host names, groups, and variables.

## Summary
Strong Ansible interview responses focus on automation principles, YAML playbooks, idempotency, inventory design, tasks and handlers, role/collection structure, AWS automation, and troubleshooting discipline.
