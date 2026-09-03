# Ansible Roles

## What is an Ansible Role?

An Ansible Role is a structured way of organizing and reusing Ansible automation.

Instead of keeping all tasks, variables, handlers, templates, and files in a single Playbook, Roles separate them into predefined directories.

Roles are especially useful when Playbooks become large or when the same automation needs to be reused across multiple environments or projects.

---

## Why Use Roles?

Roles help with:

- Organizing complex automation
- Reusing automation
- Maintaining clean Playbooks
- Separating tasks, variables, handlers, and templates
- Making projects easier to maintain and scale

---

## Role Directory Structure

A typical Role looks like:

```text
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    ├── files/
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
    └── README.md
```

### Important Directories

| Directory | Purpose |
|---|---|
| `tasks/` | Main tasks executed by the role |
| `handlers/` | Handlers triggered by tasks |
| `templates/` | Jinja2 template files |
| `files/` | Static files copied to managed nodes |
| `vars/` | Role variables |
| `defaults/` | Default variables with lower precedence |
| `meta/` | Role metadata and dependencies |

The most commonly used directories when starting with Roles are:

```text
tasks/
templates/
files/
defaults/
handlers/
```

---

## Creating a Role

A Role can be created using:

```bash
ansible-galaxy role init webserver
```

This generates the standard Role directory structure.

---

## Using a Role in a Playbook

Example:

```yaml
---
- name: Configure Web Servers
  hosts: webservers
  become: true

  roles:
    - webserver
```

Ansible will automatically look for the Role in the configured Roles path.

---

## Role vs Playbook

| Playbook | Role |
|---|---|
| Defines automation workflow | Organizes reusable automation |
| Can contain tasks directly | Separates tasks into structured directories |
| Suitable for smaller automation | Better for larger/reusable automation |
| Can call Roles | Can be called from Playbooks |

A Playbook can use multiple Roles:

```yaml
roles:
  - webserver
  - monitoring
  - security
```

---

## Example

Without Roles:

```text
site.yml
   ├── Install Apache
   ├── Configure Apache
   ├── Copy configuration
   ├── Restart Apache
   └── Create website
```

With Roles:

```text
site.yml
   │
   └── webserver role
          ├── tasks
          ├── handlers
          ├── templates
          └── files
```

This makes the automation easier to understand and reuse.

---

## My Understanding

Roles provide a standard structure for organizing Ansible automation.

They become particularly useful when automation grows beyond a simple Playbook and needs to be reused across multiple servers or projects.

A Playbook defines **what should be executed**, while Roles provide a structured and reusable way to organize the automation itself.

---

## Key Takeaways

- Roles organize Ansible automation into a standard structure.
- `tasks/main.yml` contains the main tasks.
- `handlers/main.yml` contains handlers.
- `templates/` contains Jinja2 templates.
- `files/` contains static files.
- `defaults/` contains default variables.
- `ansible-galaxy role init` can be used to create a Role structure.
- Roles are useful for reusable and maintainable automation.