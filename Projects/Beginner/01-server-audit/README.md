# 01 - Server Audit

## Overview
An Ansible playbook that connects to a mixed Linux + Windows inventory (real AWS EC2 instances) and generates a per-host audit report — OS info, memory, uptime, and disk usage — using platform-appropriate modules for each OS.

## Skills Demonstrated
- Ad-hoc commands for connectivity testing before writing a full playbook
- Cross-platform playbook design (separate plays per `hosts:` group for Linux vs Windows)
- SSH key-based auth (Linux) and WinRM auth (Windows)
- Fact gathering (`ansible_distribution`, `ansible_memtotal_mb`, etc.)
- `register` + `copy` with `delegate_to: localhost` to write local report files
- `changed_when: false` for read-only commands (idempotency hygiene)

## Prerequisites
- Ansible installed on control node (WSL, in my case)
- `pywinrm` installed for Windows connectivity: `pip install "pywinrm>=0.3.0"`
- One Linux EC2 instance (SSH key-based access)
- One Windows EC2 instance (WinRM enabled via `ConfigureRemotingForAnsible.ps1`)

## Project Structure

```text
01-server-audit/
├── README.md
├── inventory.ini
├── playbook.yml
└── reports/ # generated at runtime, not committed
```

## How to Run
1. Update `inventory.ini` with your own host IPs and credentials
2. Test connectivity:
```bash
   ansible linux -i inventory.ini -m ping
   ansible windows -i inventory.ini -m win_ping
```
3. Run the playbook:
```bash
   ansible-playbook -i inventory.ini playbook.yml
```
4. Check `reports/<hostname>.txt` for output

## Sample Output
- Host: server1
- OS: Ubuntu 22.04
- Kernel: 5.15.0-1053-aws
- Memory: 957 MB
- CPUs: 1
- Uptime: 14:32:01 up 2:15, 1 user, load average: 0.00, 0.01, 0.05

- Disk Usage:
- Filesystem Size Used Avail Use% Mounted on
- /dev/root 7.6G 1.8G 5.8G 24% /

## Problems Faced & How I Solved Them

**1. RDP couldn't connect to the Windows instance**
Got the generic "Remote Desktop can't connect" error. Root cause: the EC2 security group didn't have an inbound rule for port 3389. Fixed by adding an RDP rule scoped to my IP.

**2. WinRM cert-generation script path was outdated**
Followed an old guide pointing to a `certs_generation` script inside the `pywinrm` GitHub repo — that folder no longer exists in the current repo. Switched to generating the client certificate directly with OpenSSL instead of relying on a helper script.

**3. Self-signed cert rejected during WinRM client-cert mapping**
Got: `"the Issuer used for the certificate mapping operation is not valid"`. Cause: my self-signed cert wasn't set up with `CA:true` and `keyCertSign` key usage, so Windows wouldn't accept it as its own issuer. This also required importing the cert into **both** `TrustedPeople` and `Root` stores, not just one.

**4. Decided to drop certificate-based WinRM auth for this project**
After resolving the cert issuer problem, I decided password-based WinRM auth (`ansible_winrm_transport=basic` + `ansible_password`) was sufficient for this stage — cert-based WinRM auth adds real complexity for a beginner project. Documented here as a "known path" to revisit later rather than a dead end.

## What I Learned
- Passwordless auth means something completely different on Windows (WinRM certs) vs Linux (SSH keys) — they're not the same mental model at all.
- Most "connection failed" errors are security-group/firewall issues, not Ansible issues — always rule that out first.
- Writing one playbook with two plays (one per OS group) is cleaner than trying to force one task list to work across platforms.