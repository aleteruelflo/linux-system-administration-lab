# Ubuntu Server Security Hardening

## 1. Overview

This document describes the security-hardening measures implemented on an Ubuntu Server 24.04.5 LTS virtual machine.

The objective was to reduce unnecessary access, strengthen remote authentication, configure network security controls, and verify that the changes worked without disrupting legitimate administration.

**Environment:** Ubuntu Server 24.04.5 LTS (ARM64), Oracle VirtualBox, Apple Silicon Mac.

## 2. Initial Security Assessment

Before modifying the server, I inspected the existing SSH configuration, administrator group memberships, firewall status, and available package updates.

Commands used:

```bash
getent group sudo
sudo sshd -T | grep -E '^(passwordauthentication|pubkeyauthentication|permitrootlogin) '
sudo ufw status verbose
apt list --upgradable
```

### Initial findings

| Security control | Initial state |
|---|---|
| SSH password authentication | Enabled |
| SSH public-key authentication | Enabled |
| Direct root SSH access | Restricted to non-password authentication |
| UFW firewall | Inactive |
| Package updates | Available |

These findings established the baseline for the hardening process.

## 3. SSH Public-Key Authentication

Generated a dedicated Ed25519 SSH key pair on the macOS host using:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/cybersecurity_lab_ed25519 -C "cybersecurity-lab"
```

The private key remained on the Mac, while the public key was installed in the Ubuntu user's `~/.ssh/authorized_keys` file.

Key-based authentication was verified using:

```bash
ssh -i ~/.ssh/cybersecurity_lab_ed25519 -p 2222 -o PreferredAuthentications=publickey -o PasswordAuthentication=no alejandro@127.0.0.1
```

The connection succeeded without requiring the Ubuntu account password.

## 4. SSH Server Hardening

Created a dedicated OpenSSH configuration file:

`/etc/ssh/sshd_config.d/01-hardening.conf`

Configuration:

```text
PasswordAuthentication no
KbdInteractiveAuthentication no
PubkeyAuthentication yes
PermitRootLogin no
```

These settings disable password-based SSH authentication, enable public-key authentication, and prevent direct root SSH login.

The configuration was validated using:

```bash
sudo sshd -t
sudo sshd -T | grep -E '^(passwordauthentication|kbdinteractiveauthentication|pubkeyauthentication|permitrootlogin) '
```

After validation, the SSH service configuration was reloaded:

```bash
sudo systemctl reload ssh
```

A second SSH session confirmed that public-key authentication continued to work.

A separate connection attempt with public-key authentication disabled was rejected.

This verification helped reduce the risk of accidentally locking the administrator out of the server.

## 5. Firewall Configuration with UFW

Configured UFW with default policies:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Allowed incoming SSH traffic before activating the firewall:

```bash
sudo ufw allow 22/tcp comment 'SSH lab access'
```

Enabled UFW:

```bash
sudo ufw enable
```

Verified the configuration:

```bash
sudo ufw status verbose
```

### Firewall policy

| Traffic | Rule |
|---|---|
| Incoming by default | Deny |
| Outgoing by default | Allow |
| SSH (TCP 22) | Allow |

The Ubuntu virtual machine uses port 22 for SSH. VirtualBox forwards connections from the Mac's loopback address on port 2222 to the guest's SSH port.

After activating UFW, a second SSH connection was tested successfully.

## 6. System Updates

Updated Ubuntu's package metadata and installed available package upgrades:

```bash
sudo apt update
sudo apt upgrade
```

Checked for further available upgrades:

```bash
apt list --upgradable
```

The update process completed, and no reboot was required at the time of verification.

Keeping installed packages updated helps reduce exposure to known vulnerabilities and software defects.

## 7. Network Service Audit

Inspected listening network sockets using:

```bash
sudo ss -tulnp
```

The observed services included:

| Service | Port or protocol | Purpose |
|---|---|---|
| OpenSSH | TCP 22 | Remote administration |
| systemd-resolved | TCP/UDP 53 | Local DNS resolution |
| systemd-networkd | UDP 68 | DHCP client |

The observed listening services were consistent with the expected Ubuntu lab configuration.

A listening socket alone does not prove that a service is accessible from external networks.

## 8. Least-Privilege Access Controls

Practiced controlling access through Unix permissions, Linux groups, and Access Control Lists (ACLs).

For example, a report was assigned to the `securityteam` group with permissions allowing the owner to read and write, while group members could read only.

A targeted ACL was used to permit a specific user to traverse the administrator's home directory without granting general directory-listing access.

This demonstrated how targeted permissions can support legitimate access while limiting unnecessary exposure.

## 9. Verification and Limitations

The following controls were successfully verified during the lab:

- SSH public-key login succeeded.
- Password-only SSH login was rejected.
- Direct root SSH login was configured as disabled.
- UFW was active with an explicit SSH allow rule.
- SSH remained accessible after firewall activation.
- Package upgrades were completed.
- Listening network services were inspected.

This was a controlled educational environment. The work demonstrates foundational Linux hardening practices but does not constitute a complete production security assessment.

## 10. Key Lessons Learned

- Verify access before disabling authentication methods.
- Back up configuration files before changing them.
- Validate SSH configuration before reloading it.
- Configure firewall exceptions before activating restrictive rules.
- Use least-privilege permissions wherever possible.
- Confirm that security changes work through practical testing.