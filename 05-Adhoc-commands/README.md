# Ansible Ad-Hoc Commands

## What are Ad-Hoc Commands?

Ansible Ad-Hoc commands are **one-line commands used to perform quick tasks on managed nodes without creating a playbook**.

### Basic Syntax

```bash
ansible <host-pattern> -m <module> -a "<arguments>"
```

Example:

```bash
ansible all -m ping
```

---

## Commonly Used Ad-Hoc Commands

| Module    | Purpose                           | Example                                          |
| --------- | --------------------------------- | ------------------------------------------------ |
| `ping`    | Test Ansible connectivity         | `ansible all -m ping`                            |
| `command` | Run a command on managed nodes    | `ansible all -m command -a "uptime"`             |
| `shell`   | Run commands using shell features | `ansible all -m shell -a "ps aux \| grep nginx"` |
| `setup`   | Gather system information         | `ansible all -m setup`                           |
| `apt`     | Manage packages on Debian/Ubuntu  | Install or remove packages                       |
| `service` | Start, stop, restart services     | Manage nginx                                     |
| `copy`    | Copy files to managed nodes       | Deploy configuration files                       |
| `file`    | Manage files and directories      | Create/remove directories                        |
| `user`    | Manage system users               | Create/remove users                              |
| `reboot`  | Reboot managed nodes              | Restart servers                                  |

---

## Important: `command` vs `shell`

| `command`                           | `shell`                                 |
| ----------------------------------- | --------------------------------------- |
| Does not use a shell                | Runs through a shell                    |
| Does not support pipes or redirects | Supports pipes and redirects            |
| Preferred when possible             | Use only when shell features are needed |

Examples:

```bash
# command
ansible all -m command -a "uptime"

# shell
ansible all -m shell -a "ps aux | grep nginx"
```

> Prefer `command` unless you specifically need shell features.

---

## Frequently Used Options

```bash
# Specify inventory
ansible all -i inventory.ini -m ping

# Specify remote user
ansible all -u ubuntu -m ping

# Become root
ansible all -b -m command -a "whoami"

# Ask for SSH password
ansible all -k -m ping

# Limit execution to a host
ansible all -m ping --limit server1
```

---

## Ad-Hoc Commands vs Playbooks

| Ad-Hoc Commands          | Playbooks           |
| ------------------------ | ------------------- |
| One-line commands        | YAML files          |
| Quick tasks              | Multiple tasks      |
| Usually one-time         | Repeatable          |
| Good for troubleshooting | Good for automation |

### When to Use What?

**Use Ad-Hoc Commands for:**

* Quick checks
* Troubleshooting
* Testing connectivity
* Simple one-time tasks

**Use Playbooks for:**

* Repeatable automation
* Multiple tasks
* Deployments
* Infrastructure configuration

---

## Key Takeaways

* Ad-Hoc commands perform quick tasks without creating a playbook.
* Modules define the action Ansible performs.
* `ping`, `command`, `shell`, and `setup` are useful for checks and troubleshooting.
* `command` is preferred over `shell` when possible.
* Use Ad-Hoc commands for quick tasks and Playbooks for repeatable automation.
