# Passwordless Authentication in Ansible

## Why Authentication is Required

Ansible needs to connect to managed nodes before it can execute commands or run playbooks.

Since Ansible is agentless, it uses existing connection protocols to communicate with managed nodes.

For Linux systems, Ansible commonly uses SSH.

```text
Ansible Control Node
        |
        | Authentication
        |
        ▼
Managed Node
```

If authentication is not configured properly, Ansible cannot access the managed node.

---

# Why Passwordless Authentication?

Imagine managing multiple servers with Ansible.

If a password is required every time Ansible connects to a server, automation becomes inconvenient and difficult to scale.

```text
Control Node
     |
     ├── Password → Server 1
     ├── Password → Server 2
     ├── Password → Server 3
     └── Password → Server 4
```

Passwordless authentication allows the Control Node to authenticate securely without manually entering a password every time.

This is commonly achieved using SSH key-based authentication.

---

# SSH Key-Based Authentication

SSH uses a pair of cryptographic keys:

- Private Key
- Public Key

```text
Control Node                         Managed Node

Private Key
     │
     │ SSH Authentication
     │
     ▼
Public Key ───────────────────────► authorized_keys
```

The private key remains on the Control Node.

The public key is placed on the Managed Node.

When a connection is initiated, SSH verifies that the private key matches the public key stored on the target server.

---

# Method 1: Using SSH Key Pair

## Step 1: Generate SSH Key Pair

On the Ansible Control Node:

```bash
ssh-keygen
```

This generates:

```text
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

Where:

- `id_rsa` → Private key
- `id_rsa.pub` → Public key

> Never share or commit the private key to GitHub.

---

## Step 2: Copy Public Key to Managed Node

```bash
ssh-copy-id username@managed-node-ip
```

This copies the public key to:

```text
~/.ssh/authorized_keys
```

on the Managed Node.

---

## Step 3: Test SSH Connection

```bash
ssh username@managed-node-ip
```

If configured successfully, SSH should connect without asking for the user's password.

---

## Step 4: Use the Connection with Ansible

Example inventory:

```ini
[linux_servers]
server1 ansible_host=192.168.1.10

[linux_servers:vars]
ansible_user=username
ansible_connection=ssh
```

Test the connection:

```bash
ansible linux_servers -m ping
```

Expected result:

```text
server1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

# Method 2: Using an Existing SSH Private Key

Sometimes an SSH key pair already exists.

For example, AWS EC2 instances use `.pem` private key files.

The private key can be specified while connecting:

```bash
ssh -i private-key.pem username@server-ip
```

The same key can be configured in the Ansible inventory:

```ini
[linux_servers]
ec2-server ansible_host=192.168.1.10

[linux_servers:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=/path/to/private-key.pem
```

Test connectivity:

```bash
ansible linux_servers -m ping
```

> Private key files should never be committed to a Git repository.

---

# Method 3: Username and Password Authentication

Ansible can also authenticate using a username and password.

Example:

```bash
ansible all -i inventory.ini -m ping -u username -k
```

Where:

- `-u` specifies the remote username
- `-k` asks for the SSH password

However, this approach is less convenient for automation because manual password input may be required.

Passwords can also be managed securely using tools such as:

- Ansible Vault
- Environment variables
- External secrets managers

---

# Passwordless Authentication vs Password Authentication

| Feature | SSH Key Authentication | Password Authentication |
|---|---|---|
| Password prompt | Not required | Required |
| Suitable for automation | Yes | Less suitable |
| Scalability | High | Lower |
| Security | Strong when managed properly | Depends on password policy |
| CI/CD compatibility | Good | Difficult |
| Manual intervention | Minimal | Often required |

---

# Using Passwordless Authentication with Multiple Servers

Once SSH authentication is configured, a single Ansible Control Node can connect to multiple managed nodes.

```text
                    Ansible Control Node
                           │
                 SSH Key Authentication
                           │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
        Linux-01       Linux-02       Linux-03
```

The servers can be defined in an inventory:

```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[databases]
db1 ansible_host=192.168.1.20
```

Ansible can then execute tasks across the defined hosts.

---

# Common Issues

## Permission Denied

Example:

```text
Permission denied (publickey)
```

Possible causes:

- Incorrect private key
- Public key not present in `authorized_keys`
- Incorrect permissions on SSH files
- Incorrect username

---

## Private Key Permissions

SSH may reject a private key if its permissions are too open.

Example:

```bash
chmod 400 private-key.pem
```

---

## Incorrect Remote User

Different Linux distributions may use different default usernames.

Examples:

```text
Ubuntu → ubuntu
Amazon Linux → ec2-user
CentOS → centos
```

Always verify the correct remote username.

---

# Security Best Practices

- Never share private keys.
- Never commit `.pem` files or private keys to GitHub.
- Use strong passphrases for SSH keys where appropriate.
- Restrict access to private keys.
- Rotate keys when required.
- Remove unused keys from managed nodes.
- Use separate credentials for different environments where required.
- Store sensitive information securely.

Example `.gitignore`:

```gitignore
*.pem
id_rsa
id_rsa.pub
```

---

# My Understanding

Passwordless authentication is important for infrastructure automation because Ansible needs to connect to managed nodes without requiring repeated manual intervention.

For Linux servers, SSH key-based authentication is a common and scalable approach.

The Control Node stores the private key, while the Managed Node stores the corresponding public key. Once the connection is configured, Ansible can authenticate and execute automation tasks across multiple servers.

Password authentication can still be used, but SSH key-based authentication is generally more suitable for repeated and automated operations.

---

# Key Takeaways

- Ansible needs authentication to communicate with managed nodes.
- Linux systems are commonly managed through SSH.
- SSH key pairs contain a private key and a public key.
- The private key stays on the Control Node.
- The public key is added to the Managed Node.
- `ssh-copy-id` can be used to copy a public key.
- Existing private keys, such as AWS `.pem` files, can be used with Ansible.
- Password authentication is possible but less convenient for automation.
- Private keys and passwords should never be committed to GitHub.