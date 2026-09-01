# Ansible Ad-Hoc Commands

## What are Ansible Ad-Hoc Commands?

Ansible Ad-Hoc commands are one-line commands used to perform simple tasks on one or more managed nodes without writing a playbook.

They are useful for quick operations such as:

- Testing connectivity
- Checking server uptime
- Checking disk usage
- Managing services
- Installing packages
- Copying files
- Gathering system information
- Rebooting servers

Unlike playbooks, Ad-Hoc commands are generally used for quick tasks that do not need to be saved as reusable automation.

---

## Basic Syntax

```bash
ansible <host-pattern> -m <module> -a "<module-arguments>"
```

### Components

```text
ansible
   │
   ├── <host-pattern> → Target hosts from inventory
   │
   ├── -m → Module to execute
   │
   └── -a → Arguments passed to the module
```

Example:

```bash
ansible all -m ping
```

This command:

- Targets all hosts in the inventory
- Uses the `ping` module
- Tests whether Ansible can successfully communicate with the managed nodes

---

# Frequently Used Ad-Hoc Commands

## 1. Ping Module

The `ping` module is commonly used to test Ansible connectivity.

```bash
ansible all -m ping
```

Example output:

```text
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

> Note: This is not the same as the traditional ICMP network ping command. It verifies that Ansible can connect to the host and execute Python.

Target a specific group:

```bash
ansible webservers -m ping
```

Target a specific host:

```bash
ansible server1 -m ping
```

---

## 2. Command Module

The `command` module executes commands on managed nodes.

```bash
ansible all -m command -a "uptime"
```

Other examples:

```bash
ansible all -m command -a "hostname"
```

```bash
ansible all -m command -a "df -h"
```

```bash
ansible all -m command -a "free -h"
```

### Important

The `command` module does not process shell operators such as:

```text
|
>
>>
&&
;
```

For such operations, the `shell` module may be required.

---

## 3. Shell Module

The `shell` module executes commands through a shell.

Example:

```bash
ansible all -m shell -a "ps aux | grep nginx"
```

Another example:

```bash
ansible all -m shell -a "echo 'Hello' > /tmp/test.txt"
```

### Command vs Shell

| Command | Shell |
|---|---|
| Does not use a shell | Runs through a shell |
| More secure | Can introduce shell-related risks |
| Does not support pipes | Supports pipes |
| Does not support redirects | Supports redirects |
| Preferred when possible | Use when shell features are required |

> Prefer the `command` module unless shell functionality is specifically required.

---

## 4. Setup Module

The `setup` module gathers information (facts) about managed nodes.

```bash
ansible all -m setup
```

This can return information such as:

- Operating system
- IP addresses
- CPU details
- Memory details
- Hostname
- Network interfaces
- Python version

To filter specific information:

```bash
ansible all -m setup -a "filter=ansible_distribution"
```

Example:

```bash
ansible all -m setup -a "filter=ansible_memory_mb"
```

---

## 5. Package Management

### Install a Package

Ubuntu/Debian:

```bash
ansible all -b -m apt -a "name=nginx state=present update_cache=yes"
```

`-b` means `become`, which allows privilege escalation.

### Remove a Package

```bash
ansible all -b -m apt -a "name=nginx state=absent"
```

### Check Package State

Common states include:

```text
present
absent
latest
```

---

## 6. Service Module

The `service` module manages system services.

### Start a Service

```bash
ansible all -b -m service -a "name=nginx state=started"
```

### Stop a Service

```bash
ansible all -b -m service -a "name=nginx state=stopped"
```

### Restart a Service

```bash
ansible all -b -m service -a "name=nginx state=restarted"
```

### Enable a Service

```bash
ansible all -b -m service -a "name=nginx enabled=yes"
```

---

## 7. Copy Module

The `copy` module copies files from the Control Node to Managed Nodes.

```bash
ansible all -b -m copy -a "src=/path/to/file.txt dest=/tmp/file.txt"
```

Example:

```bash
ansible webservers -b -m copy -a "src=index.html dest=/var/www/html/index.html"
```

---

## 8. File Module

The `file` module is used to manage files and directories.

### Create a Directory

```bash
ansible all -b -m file -a "path=/tmp/test-dir state=directory"
```

### Create an Empty File

```bash
ansible all -b -m file -a "path=/tmp/test.txt state=touch"
```

### Remove a File

```bash
ansible all -b -m file -a "path=/tmp/test.txt state=absent"
```

---

## 9. User Module

The `user` module manages users on managed nodes.

### Create a User

```bash
ansible all -b -m user -a "name=testuser state=present"
```

### Remove a User

```bash
ansible all -b -m user -a "name=testuser state=absent"
```

---

## 10. Reboot Module

The `reboot` module reboots managed nodes.

```bash
ansible all -b -m reboot
```

Ansible waits for the server to reboot and attempts to reconnect.

> Use this carefully in production environments.

---

# Useful Options

## Specify Inventory

```bash
ansible all -i inventory.ini -m ping
```

## Specify Remote User

```bash
ansible all -m ping -u ubuntu
```

## Ask for SSH Password

```bash
ansible all -m ping -u ubuntu -k
```

## Become Root

```bash
ansible all -b -m command -a "whoami"
```

## Ask for Become Password

```bash
ansible all -b -K -m command -a "whoami"
```

## Run Against Multiple Hosts

```bash
ansible webservers -m ping
```

## Limit Execution

```bash
ansible all -m ping --limit server1
```

---

# Commonly Used Modules Summary

| Module | Purpose | Example Use |
|---|---|---|
| `ping` | Test Ansible connectivity | Verify managed nodes |
| `command` | Execute commands | Check uptime |
| `shell` | Execute shell commands | Use pipes/redirection |
| `setup` | Gather system facts | Check OS information |
| `apt` | Manage Debian packages | Install Apache |
| `service` | Manage services | Start/stop Nginx |
| `copy` | Copy files | Deploy configuration files |
| `file` | Manage files/directories | Create directories |
| `user` | Manage users | Create users |
| `reboot` | Reboot systems | Restart server |

---

# Ad-Hoc Commands vs Playbooks

| Ad-Hoc Commands | Playbooks |
|---|---|
| One-line commands | YAML files |
| Quick execution | Structured automation |
| Not usually reusable | Reusable |
| Good for simple tasks | Good for complex workflows |
| Best for testing and quick operations | Best for repeatable automation |

### Use Ad-Hoc Commands When

- Testing connectivity
- Performing quick checks
- Running simple one-time tasks
- Troubleshooting servers
- Quickly managing services

### Use Playbooks When

- Multiple tasks are required
- Automation needs to be repeatable
- The configuration needs to be version-controlled
- Managing complex infrastructure
- Performing deployments

---

# My Understanding

Ad-Hoc commands are useful for quickly interacting with managed nodes without creating a complete playbook.

They are particularly useful for server administration, troubleshooting, and testing Ansible connectivity.

For repeated or complex automation, Playbooks are generally a better approach because they provide a structured, reusable, and version-controlled way of defining automation.

---

# Key Takeaways

- Ad-Hoc commands allow quick execution of tasks without writing playbooks.
- Modules perform specific actions on managed nodes.
- `ping`, `command`, `shell`, and `setup` are commonly used for testing and troubleshooting.
- `apt`, `service`, `file`, and `user` are commonly used for system administration.
- Use `command` instead of `shell` whenever possible.
- Use Ad-Hoc commands for quick tasks and Playbooks for repeatable automation.