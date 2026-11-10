# 10. Ansible Vault and Security

## Why secrets must be protected
Infrastructure automation frequently manages passwords, API tokens, SSH keys, and credentials. Failing to protect them creates major security risk and compliance issues.

## Ansible Vault
Ansible Vault encrypts files and values so secret data is not stored in plain text.

### Create an encrypted file
```bash
ansible-vault create secrets.yml
```
This opens an editor and stores the file in encrypted form.

### Edit a vault file
```bash
ansible-vault edit secrets.yml
```
Use this to update values without exposing the contents in the repository.

### Encrypt an existing file
```bash
ansible-vault encrypt vars.yml
```
This converts a plain-text file into an encrypted vault file.

### Decrypt a file
```bash
ansible-vault decrypt vars.yml
```
This converts it back to plain text, often used for temporary inspection.

### View a vault file
```bash
ansible-vault view secrets.yml
```
This displays the decrypted content while still requiring access to the vault password.

### Rekey a vault file
```bash
ansible-vault rekey secrets.yml
```
This rotates the encryption key used for the file.

### Encrypt a single string
```bash
ansible-vault encrypt_string 'supersecret' --name 'db_password'
```
This is useful when adding a single secret to a variable file or CI/CD secret store.

## Vault passwords
Vault passwords should never be committed to source control. Store them in a secure secret manager or a local password file protected with strict permission controls.

```bash
ansible-playbook deploy.yml --ask-vault-pass
```
or
```bash
ansible-playbook deploy.yml --vault-password-file .vault_pass
```
Use one of these methods in automation pipelines.

## SSH key management
Use SSH key pairs instead of static passwords where possible. Protect private keys with restricted permissions.

```bash
chmod 600 ~/.ssh/id_rsa
```
This prevents unauthorized access to the private key.

## IAM roles
For AWS automation, prefer IAM roles over long-lived access keys.

```bash
aws sts get-caller-identity
```
Use this to validate the active identity and confirm the right permission scope.

## Least privilege
Grant only the minimum required permissions to a service account or IAM role. For example, a deployment role should not have full administrative privileges.

## Best practices for secrets
- Store secrets in Vault, AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault.
- Never commit raw credentials or private keys to Git.
- Rotate secrets periodically.
- Restrict password and key file permissions.
- Use CI/CD environment injection for runtime secrets.

## Example vault variable file
```yaml
---
db_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          1234567890abcdef
```
This is a valid encrypted vault structure; actual values should come from a secure secret source.

## Summary
Ansible Vault and secure secret management are essential to protect automation from exposing credentials. Secure SSH and IAM patterns reduce risk while keeping environments manageable and auditable.
