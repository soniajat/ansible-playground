# Ansible Playbooks

## What is an Ansible Playbook?

An Ansible Playbook is a YAML file that defines a set of tasks to be executed on managed nodes.

Instead of running multiple Ad-Hoc commands manually, we can define the required automation in a Playbook and execute it whenever needed.

For example:

```text
Without Playbook:

Run Command 1
      ↓
Run Command 2
      ↓
Run Command 3


With Playbook:

Write Tasks
      ↓
Run Playbook
      ↓
Ansible Executes Tasks
```

Playbooks make automation:

- Repeatable
- Reusable
- Consistent
- Easier to maintain
- Suitable for version control

---

# Basic Playbook Structure

A basic Ansible Playbook contains:

```yaml
---
- name: Play Name
  hosts: target_hosts
  become: true

  tasks:
    - name: Task Name
      module_name:
        argument: value
```

---

# Understanding the Syntax

## 1. `---`

```yaml
---
```

This indicates the start of a YAML document.

It is optional in some cases but commonly used in Ansible Playbooks.

---

## 2. Play

A Play defines which hosts Ansible should target and what tasks should be performed.

```yaml
- name: Configure Web Servers
  hosts: webservers
```

The `-` indicates that this is an item in a YAML list.

A single Playbook can contain multiple Plays.

Example:

```yaml
---
- name: Configure Web Servers
  hosts: webservers

- name: Configure Database Servers
  hosts: databases
```

---

## 3. `name`

The `name` field provides a human-readable description.

```yaml
- name: Install Apache
```

Names help make Playbooks easier to understand when reading output.

---

## 4. `hosts`

The `hosts` field specifies which managed nodes should execute the Play.

```yaml
hosts: webservers
```

The target can be:

```yaml
hosts: all
```

A specific inventory group:

```yaml
hosts: webservers
```

Or a specific host:

```yaml
hosts: server1
```

---

## 5. `become`

The `become` keyword enables privilege escalation.

```yaml
become: true
```

This is commonly required for administrative tasks such as:

- Installing packages
- Managing services
- Creating system directories
- Modifying system configuration

It is similar to using:

```bash
sudo
```

on a Linux system.

---

## 6. `tasks`

Tasks define the actions that Ansible should perform.

```yaml
tasks:
  - name: Create application directory
    ansible.builtin.file:
      path: /opt/demo-app
      state: directory
```

Each task:

- Has a descriptive name
- Uses an Ansible module
- Provides arguments to the module

---

# Modules

Ansible modules are reusable units of code that perform specific tasks.

Examples:

| Module | Purpose |
|---|---|
| `ansible.builtin.ping` | Test Ansible connectivity |
| `ansible.builtin.command` | Execute commands |
| `ansible.builtin.file` | Manage files and directories |
| `ansible.builtin.copy` | Copy files |
| `ansible.builtin.user` | Manage users |
| `ansible.builtin.service` | Manage services |
| `ansible.builtin.debug` | Display messages |

Example:

```yaml
- name: Create directory
  ansible.builtin.file:
    path: /opt/demo-app
    state: directory
```

Here:

```text
Task
  │
  ├── name → Description of the task
  │
  ├── module → ansible.builtin.file
  │
  └── arguments
       ├── path
       └── state
```

---

# Playbook Execution

Playbooks are executed using the `ansible-playbook` command.

Basic syntax:

```bash
ansible-playbook <playbook-name>.yml
```

Example:

```bash
ansible-playbook setup-server.yml
```

Specify an inventory:

```bash
ansible-playbook -i inventory.ini setup-server.yml
```

---

# Example Playbook Structure

```yaml
---
- name: Basic Server Setup
  hosts: webservers
  become: true

  tasks:

    - name: Create application directory
      ansible.builtin.file:
        path: /opt/demo-app
        state: directory
        mode: '0755'

    - name: Create application information file
      ansible.builtin.copy:
        content: |
          Application: Demo App
          Managed by: Ansible
        dest: /opt/demo-app/info.txt

    - name: Display completion message
      ansible.builtin.debug:
        msg: "Server setup completed successfully"
```

This Playbook:

1. Targets the `webservers` group.
2. Uses privilege escalation.
3. Creates an application directory.
4. Creates an information file.
5. Displays a completion message.

---

# Playbook Execution Flow

```text
                Run Playbook
                     │
                     ▼
              Select Target Hosts
                     │
                     ▼
                Execute Play
                     │
                     ▼
                Execute Task 1
                     │
                     ▼
                Execute Task 2
                     │
                     ▼
                Execute Task 3
                     │
                     ▼
               Playbook Complete
```

---

# Playbooks vs Ad-Hoc Commands

| Ad-Hoc Commands | Playbooks |
|---|---|
| One-line commands | YAML files |
| Quick tasks | Multiple structured tasks |
| Usually not reusable | Reusable |
| Limited documentation | Self-documented through task names |
| Difficult to version control | Easy to version control |
| Best for quick operations | Best for repeatable automation |

---

# My Understanding

Ansible Playbooks allow us to define infrastructure automation in a structured and reusable format.

A Playbook contains one or more Plays, and each Play contains a set of Tasks.

Each task uses an Ansible module to perform a specific action on the target managed nodes.

Playbooks are more suitable than Ad-Hoc commands when the same automation needs to be executed repeatedly.

---

# Key Takeaways

- Playbooks are written in YAML.
- A Playbook can contain one or more Plays.
- A Play targets specific hosts.
- Tasks define the actions to be executed.
- Modules perform the actual operations.
- `become: true` enables privilege escalation.
- Playbooks are executed using `ansible-playbook`.
- Playbooks are suitable for repeatable and reusable automation.