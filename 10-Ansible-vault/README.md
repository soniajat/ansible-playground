# Ansible Vault

## What is Ansible Vault?

Ansible Vault is used to **encrypt sensitive data** used by Ansible.

Examples:

- Passwords
- API tokens
- AWS credentials
- SSH-related secrets
- Other sensitive variables

---

## Create a Vault Password File

Generate a random password:

```bash
openssl rand -base64 2048 > vault.pass
```

> Keep `vault.pass` secure and never commit it to Git.

---

## Create an Encrypted File

```bash
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```

Add sensitive variables:

```yaml
aws_access_key: <ACCESS_KEY>
aws_secret_key: <SECRET_KEY>
```

The file is stored encrypted.

---

## View an Encrypted File

```bash
ansible-vault view group_vars/all/pass.yml --vault-password-file vault.pass
```

---

## Edit an Encrypted File

```bash
ansible-vault edit group_vars/all/pass.yml --vault-password-file vault.pass
```

---

## Run a Playbook Using Vault

```bash
ansible-playbook -i inventory.ini create_ec2.yml \
  --vault-password-file vault.pass
```

---

## Important Security Rules

Never commit:

```text
vault.pass
```

or unencrypted credentials to GitHub.

Add them to `.gitignore`:

```gitignore
vault.pass
```

Ansible supports storing vault passwords in files or using external secret-management mechanisms. Vault password files should not be placed under source control. :contentReference[oaicite:3]{index=3}

---

## Key Commands

```bash
ansible-vault create <file>
ansible-vault view <file>
ansible-vault edit <file>
ansible-vault encrypt <file>
ansible-vault decrypt <file>
```