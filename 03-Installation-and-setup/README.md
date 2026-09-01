# Ansible Installation and Environment Setup

## Understanding Ansible Architecture

Before installing Ansible, it is important to understand where Ansible runs and how it communicates with managed nodes.

An Ansible environment consists of:

- Control Node
- Managed Nodes
- Connection protocols
- Authentication mechanisms

The Control Node is responsible for executing Ansible commands and playbooks.

Managed Nodes are the target systems where Ansible performs tasks.

---

# Environment Scenarios

In a real-world infrastructure, the operating systems of the Control Node and Managed Nodes may differ.

This section covers the following scenarios:

1. Linux Control Node → Linux Managed Nodes
2. Linux Control Node → Linux + Windows Managed Nodes
3. Windows Machine → Linux + Windows Managed Nodes

---

# Scenario 1: Linux Control Node → Linux Managed Nodes

## Architecture

```text
                    Ansible Control Node
                         (Linux)
                            │
                            │ SSH
                            │
              ┌─────────────┼─────────────┐
              │             │             │
              ▼             ▼             ▼
          Linux VM 1    Linux VM 2    Linux VM 3
```

This is the simplest and most common Ansible architecture.

The Control Node runs Ansible and connects to Linux Managed Nodes using SSH.

## Requirements

### Control Node

- Linux operating system
- Python
- Ansible installed

### Managed Nodes

- SSH service running
- Python installed
- User account for Ansible connection
- Required privileges for administrative tasks

---

## Installing Ansible on Ubuntu

Update the package repository:

```bash
sudo apt update
```

Install Ansible:

```bash
sudo apt install ansible -y
```

Verify the installation:

```bash
ansible --version
```

---

## Configure SSH Authentication

Ansible uses SSH to connect to Linux Managed Nodes.

Test the connection manually:

```bash
ssh username@managed-node-ip
```

For key-based authentication:

```bash
ssh -i private-key.pem username@managed-node-ip
```

---

## Example Inventory

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

# Scenario 2: Linux Control Node → Linux + Windows Managed Nodes

## Architecture

```text
                       Ansible Control Node
                            (Linux)
                                │
                 ┌──────────────┴──────────────┐
                 │                             │
                SSH                        WinRM / PSRP
                 │                             │
        ┌────────┴────────┐              ┌─────┴─────┐
        │                 │              │           │
        ▼                 ▼              ▼           ▼
    Linux VM 1        Linux VM 2     Windows 1   Windows 2
```

This architecture is commonly used in enterprise environments where both Linux and Windows servers need to be managed.

The Linux Control Node can manage:

- Linux servers using SSH
- Windows servers using WinRM or PSRP

Windows can also be managed using SSH on supported configurations, but WinRM/PSRP are common native approaches for Windows automation.

---

## Linux Managed Nodes Configuration

Linux nodes require:

- SSH connectivity
- Python
- Appropriate user permissions

Example inventory:

```ini
[linux_servers]
linux1 ansible_host=192.168.1.10
linux2 ansible_host=192.168.1.11

[linux_servers:vars]
ansible_user=ansible
ansible_connection=ssh
```

---

## Windows Managed Nodes Configuration

Windows hosts are managed differently from Linux hosts.

Ansible typically connects to Windows machines using:

- PSRP
- WinRM

Modern Windows hosts can also be managed over SSH when OpenSSH is configured and supported.

Windows-specific Ansible modules are generally written in PowerShell.

---

## Example Inventory

```ini
[linux_servers]
linux1 ansible_host=192.168.1.10
linux2 ansible_host=192.168.1.11

[linux_servers:vars]
ansible_user=ansible
ansible_connection=ssh


[windows_servers]
windows1 ansible_host=192.168.1.20
windows2 ansible_host=192.168.1.21

[windows_servers:vars]
ansible_user=administrator
ansible_connection=winrm
ansible_winrm_transport=ntlm
ansible_winrm_server_cert_validation=ignore
```

> Note: Production environments should use an appropriate secure authentication and certificate configuration rather than blindly disabling certificate validation.

---

# Scenario 3: Windows Machine → Linux + Windows Managed Nodes

## Can Windows Be an Ansible Control Node?

Ansible does not natively support Windows as a Control Node.

However, Ansible can be run on a Windows machine using:

- Windows Subsystem for Linux (WSL)
- Containers

The architecture would look like:

```text
                Windows Machine
                      │
             ┌────────┴────────┐
             │ WSL / Container │
             │ Ansible Runs Here
             └────────┬────────┘
                      │
          ┌───────────┴───────────┐
          │                       │
         SSH                 WinRM / PSRP
          │                       │
          ▼                       ▼
      Linux Servers         Windows Servers
```

In this setup, Windows itself is not directly running Ansible natively.

Instead, Ansible runs inside a Linux environment hosted on the Windows machine.

---

# Which Architecture is Best for Production?

## Recommended Architecture

For a production environment, my preferred architecture would be:

```text
                     Dedicated Linux Server
                      Ansible Control Node
                              │
              ┌───────────────┴───────────────┐
              │                               │
             SSH                         WinRM / PSRP
              │                               │
       Linux Managed Nodes             Windows Managed Nodes
```

### Why?

A dedicated Linux Control Node provides:

- Native Ansible support
- Better compatibility
- Easier automation and scheduling
- Stable Python environment
- Better integration with CI/CD tools
- Easier SSH key management
- No dependency on WSL
- Suitable for centralized automation

The same Control Node can manage both Linux and Windows infrastructure.

---

# Production Considerations

In a production environment, the Ansible Control Node should ideally:

- Be a dedicated and secured system
- Not be used for unrelated daily activities
- Have restricted access
- Use SSH keys instead of passwords for Linux servers
- Use secure authentication for Windows servers
- Store secrets using Ansible Vault or an external secrets manager
- Use Git for version control
- Use separate inventories for Development, UAT, and Production
- Maintain audit logs of automation activities
- Be integrated with a CI/CD tool or automation platform when required

Example structure:

```text
ansible/
├── inventories/
│   ├── dev/
│   ├── uat/
│   └── production/
│
├── playbooks/
│
├── roles/
│
├── group_vars/
│
└── ansible.cfg
```

---

# Comparison

| Scenario | Possible | Recommended for Production |
|---|---|---|
| Linux Control → Linux Nodes | Yes | Yes |
| Linux Control → Linux + Windows Nodes | Yes | Yes |
| Native Windows Control → Managed Nodes | No | No |
| Windows + WSL → Managed Nodes | Yes | Not recommended for production |
| Windows + Container → Managed Nodes | Possible | Depends on implementation |

---

# My Understanding

Ansible works best when the Control Node runs on a Linux or other supported Unix-like operating system.

A Linux Control Node can centrally manage both Linux and Windows infrastructure using different connection methods.

For a production environment, I would prefer a dedicated Linux server as the Ansible Control Node because it provides native Ansible support and can manage heterogeneous environments without depending on WSL.

---

# Key Takeaways

- Ansible is installed on the Control Node.
- Linux Managed Nodes are commonly managed using SSH.
- Windows Managed Nodes can be managed using PSRP or WinRM.
- A Linux Control Node can manage both Linux and Windows servers.
- Native Windows is not supported as an Ansible Control Node.
- WSL can be useful for learning and development but is not recommended by Ansible for production control systems.
- A dedicated Linux Control Node is generally the preferred architecture for production environments.