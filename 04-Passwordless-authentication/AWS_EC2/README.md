# Passwordless Authentication in Ansible

## Passwordless Authentication on AWS EC2

When working with EC2 instances, we initially connect using the `.pem` private key created during instance launch.

This existing access can then be used to configure authentication between the Ansible Control Node and the managed EC2 instance.

---

## Method 1: Using Public Key Authentication

Suppose:

- The Ansible Control Node has its own SSH key pair.
- The EC2 instance was launched with an existing `.pem` key.

We can use the `.pem` file to access the instance and copy the Control Node's public key to it.

```bash
ssh-copy-id -f -o "IdentityFile=<PATH_TO_PEM_FILE>" ubuntu@<INSTANCE_PUBLIC_IP>
```

### Command Breakdown

- `ssh-copy-id` — Copies the public SSH key to the remote server.
- `-f` — Forces the key to be copied.
- `-o "IdentityFile=..."` — Specifies the existing EC2 `.pem` private key used for authentication.
- `ubuntu@<INSTANCE_PUBLIC_IP>` — Remote username and EC2 public IP address.

After successfully copying the key, test the connection:

```bash
ssh ubuntu@<INSTANCE_PUBLIC_IP>
```

If configured correctly, the connection should work without specifying the `.pem` file.

### How It Works

#### Before

```text
Control Node
     |
     | Uses EC2 .pem key
     ▼
EC2 Instance
```

#### After

```text
Control Node
     |
     | Uses Control Node SSH Key
     ▼
EC2 Instance
```

The public key from the Ansible Control Node is added to:

```text
~/.ssh/authorized_keys
```

on the EC2 instance.

---

# Method 2: Using Username and Password

EC2 instances are usually configured for SSH key authentication by default. To enable password authentication, the SSH configuration needs to be updated.

On the EC2 instance:

```bash
sudo nano /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
```

Update:

```text
PasswordAuthentication yes
```

Then restart the SSH service:

```bash
sudo systemctl restart ssh
```

> Depending on the Linux distribution, the SSH service name can differ.

After enabling password authentication, ensure that the target user has a password set:

```bash
sudo passwd ubuntu
```

Then test the connection:

```bash
ssh ubuntu@<INSTANCE_PUBLIC_IP>
```

---

## Important Difference

| Method | What Happens |
|---|---|
| Public Key Authentication | SSH authenticates using cryptographic keys |
| Password Authentication | SSH authenticates using username and password |
| EC2 `.pem` | Existing private key used to access the EC2 instance |
| `ssh-copy-id` | Copies the Control Node's public key to the managed node |

---

## My Understanding

The `.pem` file and the newly configured SSH key do not necessarily serve the same long-term purpose.

Initially:

> `.pem` → Used to gain access to the EC2 instance.

Then:

> Control Node SSH public key → Added to the EC2 instance for passwordless authentication.

This allows the Ansible Control Node to connect to the managed EC2 instance without manually specifying the `.pem` file or entering a password each time.

---

## Key Takeaways

- AWS EC2 instances commonly use `.pem` files for initial SSH access.
- `ssh-copy-id` can be used to copy the Control Node's public key to a managed node.
- The public key is stored in `~/.ssh/authorized_keys` on the managed node.
- After configuration, SSH key authentication can be used without manually specifying the `.pem` file.
- Password authentication can be enabled by modifying the SSH configuration.
- A password must be set for the user before password authentication can work.
- SSH key-based authentication is generally more suitable for automation than password authentication.