# Introduction to Ansible

## What is Ansible?

Ansible is an open-source automation tool used to automate repetitive tasks such as:

- Server configuration
- Application deployment
- Software installation
- Configuration management
- Infrastructure provisioning
- Orchestration

Ansible allows us to manage multiple servers from a single machine called the **Control Node**.

---

## Why Ansible?

Managing servers manually becomes difficult when the number of servers increases.

For example, imagine installing Apache on 50 servers manually:

```text
Connect to Server 1  → Install Apache
Connect to Server 2  → Install Apache
Connect to Server 3  → Install Apache
...
Connect to Server 50 → Install Apache
```

This approach is:

- Time-consuming
- Error-prone
- Difficult to maintain

With Ansible, we can define the required configuration once and apply it to multiple servers.

```text
                Ansible Control Node
                        │
          ┌─────────────┼─────────────┐
          │             │             │
          ▼             ▼             ▼
       Server 1      Server 2      Server 3
```

---

## Key Features of Ansible

### Agentless

Ansible does not require an agent to be installed on managed servers.

For Linux servers, Ansible typically connects using SSH.

### Simple

Ansible uses YAML syntax, which is easy to read and understand.

### Idempotent

Ansible ensures that the desired state is maintained.

For example, if Apache is already installed, Ansible will not install it again unnecessarily.

### Push-Based

Commands and configurations are pushed from the Control Node to Managed Nodes.

### Supports Multiple Servers

A single Ansible Control Node can manage multiple servers simultaneously.

---

## Basic Architecture

Ansible mainly consists of:

### Control Node

The machine where Ansible is installed and commands/playbooks are executed.

### Managed Nodes

The target servers managed by Ansible.

### Inventory

A file containing information about the managed servers.

### Playbook

A YAML file containing automation tasks that Ansible executes.

---

## How Ansible Works

The basic workflow is:

```text
Write Playbook
      │
      ▼
Run Ansible from Control Node
      │
      ▼
Connect to Managed Nodes
      │
      ▼
Execute Tasks
      │
      ▼
Desired State Achieved
```

For Linux servers, Ansible generally communicates using SSH.

---

## Important Terms

| Term | Description |
|------|-------------|
| Control Node | Machine where Ansible is installed |
| Managed Node | Server managed by Ansible |
| Inventory | List of servers managed by Ansible |
| Module | Pre-built unit used to perform a specific task |
| Task | A single action executed by Ansible |
| Playbook | YAML file containing automation tasks |
| Role | Structured way of organizing playbooks and tasks |

---

## Summary

Ansible is useful when the same task needs to be performed consistently across multiple servers.

Instead of manually connecting to every server and executing commands, we can define the desired configuration in a playbook and let Ansible perform the tasks automatically.

This makes infrastructure management more consistent, repeatable, and scalable.

---

## Key points

- What Ansible is
- Why infrastructure automation is needed
- Basic Ansible architecture
- Control Node and Managed Nodes
- Inventory
- Playbooks
- Agentless architecture
- Idempotency