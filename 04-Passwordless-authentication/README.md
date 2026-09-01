# Passwordless Authentication in Ansible

## Why Is Authentication Needed?

Ansible must authenticate with managed nodes before executing commands or playbooks.

Since Ansible is agentless, Linux servers are commonly accessed using **SSH**.

```text
Ansible Control Node
        │
       SSH
        │
        ▼
   Managed Node
```

For automation across multiple servers, repeatedly entering passwords is inconvenient. This is why **SSH key-based authentication** is commonly used.

---

## SSH Key-Based Authentication

SSH uses two keys:

* **Private Key** → Stored securely on the Control Node
* **Public Key** → Added to the Managed Node

```text
Control Node                      Managed Node

Private Key ───── SSH ───────► Public Key
                               authorized_keys
```

When the keys match, authentication happens without entering the remote user's password.

> Never share or commit private keys to GitHub.

---

## Method 1: Generate and Copy an SSH Key

### 1. Generate a Key Pair

On the Control Node:

```bash
ssh-keygen
```

This creates:

```text
~/.ssh/id_rsa       # Private key
~/.ssh/id_rsa.pub   # Public key
```

### 2. Copy the Public Key

```bash
ssh-copy-id username@managed-node-ip
```

The public key is added to:

```text
~/.ssh/authorized_keys
```

### 3. Test the Connection

```bash
ssh username@managed-node-ip
```

If successful, SSH should connect without asking for the remote password.

---

## Use SSH Authentication with Ansible

### Inventory

```ini
[linux_servers]
server1 ansible_host=192.168.1.10

[linux_servers:vars]
ansible_user=username
ansible_connection=ssh
```

Test connectivity:

```bash
ansible linux_servers -m ping
```

Expected output:

```text
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

## Method 2: Use an Existing Private Key

For example, AWS EC2 instances commonly use `.pem` keys.

Manual SSH connection:

```bash
ssh -i private-key.pem username@server-ip
```

Ansible inventory:

```ini
[linux_servers]
ec2-server ansible_host=192.168.1.10

[linux_servers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/path/to/private-key.pem
```

---

## Password Authentication

Password authentication is also possible:

```bash
ansible all -i inventory.ini -m ping -u username -k
```

* `-u` → Remote username
* `-k` → Ask for SSH password

This works, but SSH keys are generally better suited for automation.

---

## SSH Key vs Password

| Feature         | SSH Key   | Password      |
| --------------- | --------- | ------------- |
| Password prompt | No        | Yes           |
| Automation      | Excellent | Limited       |
| Scalability     | High      | Lower         |
| CI/CD friendly  | Yes       | Less suitable |

---

## Managing Multiple Servers

Once authentication is configured, one Control Node can manage multiple servers.

```text
              Ansible Control Node
                     │
                    SSH
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Linux-01   Linux-02   Linux-03
```

Example inventory:

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[databases]
db1 ansible_host=192.168.1.20
```

---

## Common Issues

### Permission Denied

```text
Permission denied (publickey)
```

Check:

* Correct private key
* Public key exists in `authorized_keys`
* Correct remote username
* Correct SSH file permissions

### Private Key Permissions

```bash
chmod 400 private-key.pem
```

Common usernames:

```text
Ubuntu       → ubuntu
Amazon Linux → ec2-user
CentOS       → centos
```

---

## Security Best Practices

* Never share private keys.
* Never commit `.pem` files to GitHub.
* Use appropriate file permissions.
* Remove unused keys.
* Rotate credentials when required.

Example `.gitignore`:

```gitignore
*.pem
id_rsa
```

---

## Key Takeaways

* Ansible needs authentication to access managed nodes.
* Linux servers are commonly managed using SSH.
* Passwordless authentication uses SSH public/private key pairs.
* Private key stays on the Control Node.
* Public key is stored in the Managed Node's `authorized_keys`.
* SSH keys are better suited for automation than passwords.
* Existing keys, such as AWS `.pem` files, can also be used.
