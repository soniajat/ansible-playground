# Ansible Galaxy

## What is Ansible Galaxy?

Ansible Galaxy is a community platform and command-line tool used to **find, install, share, and manage Ansible Roles and Collections**.

It allows us to reuse automation created by the Ansible community instead of building everything from scratch.

---

## Install a Role

Search for a role:

```bash
ansible-galaxy search <role-name>
```

Install a role:

```bash
ansible-galaxy role install <role-name>
```

Example:

```bash
ansible-galaxy role install geerlingguy.nginx
```

The role is downloaded to the configured Roles directory.

---

## List Installed Roles

```bash
ansible-galaxy role list
```

---

## Create a Role

```bash
ansible-galaxy role init <role-name>
```

Example:

```bash
ansible-galaxy role init webserver
```

---

## Share / Publish a Role

Ansible Galaxy can be used to share Roles with others.

A Role can be maintained in a Git repository and published/imported into Galaxy depending on the Galaxy/Automation Hub workflow being used.

---

## Requirements File

Multiple Roles can be defined in a `requirements.yml` file:

```yaml
roles:
  - name: geerlingguy.nginx
  - name: geerlingguy.mysql
```

Install them together:

```bash
ansible-galaxy install -r requirements.yml
```

---

## Collections

Collections package Ansible content such as:

- Modules
- Roles
- Plugins
- Playbooks

Install a Collection:

```bash
ansible-galaxy collection install <namespace>.<collection>
```

Example:

```bash
ansible-galaxy collection install ansible.windows
```

---

## Key Takeaways

- **Galaxy → Find and share Ansible content**
- `ansible-galaxy role install` → Install a Role
- `ansible-galaxy role init` → Create a Role structure
- `requirements.yml` → Define dependencies
- `ansible-galaxy collection install` → Install Collections