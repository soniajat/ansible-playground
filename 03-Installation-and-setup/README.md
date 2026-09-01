# Ansible Installation and Environment Setup

## Understanding Ansible Architecture

An Ansible environment consists of:

* **Control Node** — Runs Ansible commands and playbooks
* **Managed Nodes** — Target servers managed by Ansible
* **Connection Protocols** — SSH, WinRM, or PSRP
* **Authentication** — SSH keys, passwords, Kerberos, etc.

---

## Common Environment Scenarios

### 1. Linux Control Node → Linux Managed Nodes

```text
          Ansible Control Node
               (Linux)
                  │
                 SSH
          ┌───────┼───────┐
          ▼       ▼       ▼
       Linux1   Linux2   Linux3
```

This is the simplest and most common setup.

#### Requirements

**Control Node**

* Linux
* Python
* Ansible

**Managed Nodes**

* SSH service
* Python
* User with required permissions

#### Install Ansible on Ubuntu

```bash
sudo apt update
sudo apt install ansible -y

ansible --version
```

#### Example Inventory

```ini
[linux_servers]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.11

[linux_servers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/ansible-key.pem
```

Test connectivity:

```bash
ansible linux_servers -m ping
```

---

### 2. Linux Control Node → Linux + Windows Managed Nodes

```text
              Ansible Control Node
                   (Linux)
                 /         \
               SSH       WinRM/PSRP
               /             \
          Linux Servers   Windows Servers
```

A Linux Control Node can manage multiple operating systems.

| Managed Node | Common Connection |
| ------------ | ----------------- |
| Linux        | SSH               |
| Windows      | WinRM / PSRP      |

#### Example Inventory

```ini
[linux_servers]
linux1 ansible_host=192.168.1.10

[linux_servers:vars]
ansible_user=ansible
ansible_connection=ssh


[windows_servers]
windows1 ansible_host=192.168.1.20

[windows_servers:vars]
ansible_user=administrator
ansible_connection=winrm
```

---

### 3. Windows Machine as a Control Node

Windows is **not natively supported** as an Ansible Control Node.

However, you can run Ansible on Windows using:

* WSL
* Containers

```text
Windows Machine
      │
 WSL / Container
      │
   Ansible
   /      \
 SSH    WinRM
  │        │
Linux   Windows
```

This is useful for learning and development, but a dedicated Linux Control Node is generally preferred for production.

---

## Recommended Production Architecture

```text
            Dedicated Linux Server
             Ansible Control Node
                 /          \
               SSH        WinRM/PSRP
               /              \
         Linux Servers    Windows Servers
```

### Why Linux?

* Native Ansible support
* Can manage Linux and Windows
* Better for centralized automation
* Easy SSH key management
* Works well with CI/CD tools
* Stable and suitable for production

---

## Production Best Practices

* Use a dedicated and secured Control Node
* Use SSH keys instead of passwords
* Store secrets using Ansible Vault
* Maintain separate inventories for Dev, UAT, and Production
* Store playbooks in Git
* Restrict access to the Control Node

Example structure:

```text
ansible/
├── inventories/
│   ├── dev/
│   ├── uat/
│   └── production/
├── playbooks/
├── roles/
├── group_vars/
└── ansible.cfg
```

---

## Key Takeaways

* Ansible runs from a **Control Node**.
* Linux servers are commonly managed using **SSH**.
* Windows servers are commonly managed using **WinRM or PSRP**.
* A Linux Control Node can manage both Linux and Windows servers.
* Windows can run Ansible through **WSL or containers**, but not natively as a standard Control Node.
* For production, a **dedicated Linux Control Node** is generally the preferred architecture.
