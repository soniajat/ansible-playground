# Ansible Collections

## What are Collections?

Ansible Collections are packages that can contain:

- Modules
- Roles
- Plugins
- Playbooks

Collections provide reusable Ansible content, often grouped by technology or vendor.

---

## Installing a Collection

Example:

```bash
ansible-galaxy collection install amazon.aws
```

For AWS-related automation, the `amazon.aws` Collection provides modules for interacting with AWS services.

---

## AWS Collection Requirements

Install the required Python AWS SDK:

```bash
pip install boto3
```

Some AWS modules may require additional Python dependencies depending on the module being used. :contentReference[oaicite:1]{index=1}

---

## Using a Collection

Example:

```yaml
---
- name: Create AWS Resources
  hosts: localhost
  gather_facts: false

  tasks:
    - name: Create AWS resource
      amazon.aws.<module_name>:
        # module parameters
```

Cloud provisioning tasks generally execute from the **Ansible Control Node**, using the AWS APIs, rather than on the AWS resource being created. :contentReference[oaicite:2]{index=2}

---

## Useful Commands

```bash
ansible-galaxy collection install <namespace>.<collection>
```

```bash
ansible-galaxy collection list
```

Example:

```bash
ansible-galaxy collection install amazon.aws
```

---

## Example Use Case

```text
Ansible Control Node
        |
        | AWS API
        ▼
      AWS
        |
   ┌────┼────┐
   ▼    ▼    ▼
  EC2   S3   VPC
```

Collections allow Ansible to interact with these AWS services through their APIs.