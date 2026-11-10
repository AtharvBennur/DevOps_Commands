# 07. AWS Ansible Collection (`amazon.aws`)

## What is an Ansible collection?
A collection is a package of modules, plugins, and roles distributed under a namespace. The `amazon.aws` collection contains modules for EC2, IAM, S3, Route53, VPC, and more.

## Why do we need `amazon.aws`?
Ansible includes basic control operations but not all cloud-specific functionality. AWS automation needs modules such as `ec2_instance`, `ec2_security_group`, `s3_bucket`, and `route53`.

## Built-in modules vs AWS collection modules
Built-in modules handle generic system tasks such as file, user, service, and package management. AWS modules are cloud-specific and are packaged under `amazon.aws`.

## How collections work internally
Collections are installed under the Ansible collections path and expose module names like `amazon.aws.ec2_instance`.

## Installing the AWS collection
```bash
ansible-galaxy collection install amazon.aws
ansible-galaxy collection list
```
This installs the collection and lists any modules already present.

## AWS CLI and Python dependencies
The AWS collection relies on the AWS SDKs for Python:
- `boto3`
- `botocore`

```bash
python3 -m pip install boto3 botocore
```
This provides the Python libraries needed for AWS API calls.

## AWS authentication
AWS credentials may come from:
- IAM roles on EC2 / ECS / EKS
- access keys in environment variables
- AWS shared config/profile files

### Environment variables
```bash
export AWS_ACCESS_KEY_ID="AKIA..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_DEFAULT_REGION="us-east-1"
```
Use these only in secure sessions and CI/CD secret stores.

## IAM roles vs access keys
IAM roles are safer because credentials are managed by AWS and rotated automatically. Access keys are simpler for local testing but require proper handling and least-privilege scope.

## Region configuration
```bash
export AWS_REGION=us-east-1
```
Set a default region or specify `region` directly in each task.

## AWS module examples
### 1. Create an EC2 instance
```yaml
- name: Launch EC2 instance
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Create EC2 instance
      amazon.aws.ec2_instance:
        key_name: my-key
        instance_type: t3.micro
        image_id: ami-12345678
        vpc_subnet_id: subnet-12345678
        region: us-east-1
        security_group: sg-12345678
        tags:
          Name: demo-web
        wait: true
```
This launches a new EC2 host with basic networking and tags.

### 2. List EC2 instances
```yaml
- name: List EC2 instances
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Gather EC2 info
      amazon.aws.ec2_instance_info:
        region: us-east-1
      register: ec2_info

    - name: Show instance IDs
      ansible.builtin.debug:
        msg: "{{ ec2_info.instances | map(attribute='instance_id') | list }}"
```
This is useful for inventory or reporting tasks.

### 3. Create a security group
```yaml
- name: Create security group
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Manage security group
      amazon.aws.ec2_security_group:
        name: demo-sg
        description: Demo group
        region: us-east-1
        vpc_id: vpc-12345678
        rules:
          - proto: tcp
            ports:
              - 22
            cidr_ip: 0.0.0.0/0
          - proto: tcp
            ports:
              - 80
            cidr_ip: 0.0.0.0/0
```
This grants HTTP and SSH access from the internet.

### 4. Create a VPC
```yaml
- name: Create VPC
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Create VPC
      amazon.aws.ec2_vpc_net:
        name: demo-vpc
        cidr_block: 10.0.0.0/16
        region: us-east-1
        tenancy: default
        state: present
      register: vpc
```
This creates the network foundation for EC2 and related resources.

### 5. Create a subnet
```yaml
- name: Create subnet
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Create subnet in VPC
      amazon.aws.ec2_subnet:
        vpc_id: vpc-12345678
        cidr: 10.0.0.0/24
        region: us-east-1
        state: present
        map_public: true
```
This creates a subnet and makes it public if required.

### 6. Create an S3 bucket
```yaml
- name: Create S3 bucket
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Create S3 bucket
      amazon.aws.s3_bucket:
        name: my-demo-bucket-2027
        region: us-east-1
        state: present
```
This creates an S3 bucket for application artifacts or backups.

### 7. Fetch AWS account information
```yaml
- name: Get AWS caller info
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Get account details
      amazon.aws.aws_caller_info:
      register: caller

    - name: Show account info
      ansible.builtin.debug:
        var: caller
```
This prints account and identity information for the active AWS session.

## Key AWS modules
- `amazon.aws.ec2_instance`
- `amazon.aws.ec2_vpc_net`
- `amazon.aws.ec2_security_group`
- `amazon.aws.ec2_subnet`
- `amazon.aws.ec2_key_pair`
- `amazon.aws.ec2_instance_info`
- `amazon.aws.ec2_security_group_info`
- `amazon.aws.aws_caller_info`
- `amazon.aws.aws_ssm`
- `amazon.aws.ec2_ami_info`
- `amazon.aws.s3_bucket`
- `amazon.aws.s3_object`
- `amazon.aws.route53`
- `amazon.aws.rds_instance`

## Documentation commands
```bash
ansible-doc amazon.aws.ec2_instance
ansible-doc amazon.aws.ec2_security_group
ansible-doc amazon.aws.ec2_vpc_net
```
Use these to verify current parameter names and supported options before writing automation.

## AWS dynamic inventory with `aws_ec2`
Dynamic inventory can discover EC2 instances automatically by tags and regions.

### Example `aws_ec2.yml`
```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
filters:
  tag:Environment: dev
hostvars_extras:
  - instance_id
  - public_ip_address
```
### Execution
```bash
ansible-inventory -i aws_ec2.yml --list
ansible all -i aws_ec2.yml -m ping
```
This automatically discovers EC2 hosts in AWS and uses them as Ansible inventory.

## Best practices
- Use IAM roles whenever possible.
- Prefer least-privilege policies.
- Store AWS secrets in CI/CD secret stores or environment variables.
- Specify `region` in each job or in `~/.aws/config`.
- Validate collection documentation before using a module in production.

## Summary
The `amazon.aws` collection is essential for AWS automation with Ansible. It allows infrastructure provisioning, configuration, and inventory discovery using standard AWS APIs and secure authentication patterns.
