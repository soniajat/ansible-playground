# Ansible Variables

## What are Variables?

Variables allow values to be stored once and reused throughout Ansible Playbooks.

Example:

```yaml
app_name: demo-app
app_port: 8080
environment: production
```

Use a variable with Jinja2 syntax:

```yaml
name: "{{ app_name }}"
```

---

## Where Can Variables Be Defined?

Variables can be defined in several places:

### 1. Playbook

```yaml
vars:
  app_port: 8080
```

### 2. Inventory

```ini
[webservers:vars]
app_port=8080
```

### 3. `group_vars`

```text
group_vars/
└── webservers.yml
```

### 4. `host_vars`

```text
host_vars/
└── server1.yml
```

### 5. Role Defaults

```text
roles/
└── webserver/
    └── defaults/
        └── main.yml
```

### 6. Command Line

```bash
ansible-playbook site.yml -e "app_port=9090"
```

---

## Variable Precedence

If the same variable is defined in multiple places, Ansible uses **variable precedence** to determine which value wins.

A useful simplified view is:

```text
Role defaults
      ↓
Inventory variables
      ↓
Play variables
      ↓
Task variables
      ↓
Extra variables (-e)
```

**Higher-precedence values override lower-precedence values.**

Extra variables passed with `-e` have very high precedence. :contentReference[oaicite:4]{index=4}

---

## Example

Variable:

```yaml
app_port: 8080
```

Override it at runtime:

```bash
ansible-playbook site.yml -e "app_port=9090"
```

The Playbook will use:

```text
app_port = 9090
```

---

## Best Practice

Keep variables in appropriate locations rather than defining the same variable in multiple places.

Use:

```text
group_vars/
host_vars/
roles/*/defaults/
```

for organized and maintainable variable management.

---

## Key Takeaways

- Variables make Playbooks reusable and flexible.
- Variables can be defined at multiple levels.
- Jinja2 syntax `{{ variable_name }}` is used to reference variables.
- Variable precedence determines which value is used.
- `-e` / `--extra-vars` has very high precedence.