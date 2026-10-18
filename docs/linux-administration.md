# Linux System Administration

## Overview

This document summarizes the Linux administration activities performed in an Ubuntu Server 24.04 LTS virtual machine.

The lab focused on working with the Linux command line, managing files and users, monitoring processes, and investigating system services and network connectivity.

## 1. Linux Command-Line Fundamentals

Practiced filesystem navigation and inspection using:

```bash
pwd
ls -la
cd /var/log
cd ~
whoami
```

These commands were used to navigate the Linux filesystem, identify the current user, and inspect directory contents, including hidden files.

## 2. Files and Directories

Created directories and files, wrote and inspected text, and practiced file management.

```bash
mkdir projects
cd projects
touch notes.txt
echo "My first Linux project" > notes.txt
echo "I am learning cybersecurity" >> notes.txt
cat notes.txt
cp notes.txt backup.txt
mv backup.txt notes-backup.txt
rm -i notes-backup.txt
```

This exercise demonstrated the difference between overwriting and appending file contents, the importance of backups, and safe deletion practices.

## 3. File Permissions and Ownership

Inspected and modified Unix file permissions.

```bash
ls -l notes.txt
chmod 600 notes.txt
```

Studied owner, group, and other permissions using read (`r`), write (`w`), and execute (`x`) access.

The lab also covered group ownership and Access Control Lists (ACLs) to grant specific access without exposing unrelated files.

## 4. User and Group Administration

Created a test account and assigned it to a security group.

```bash
id
sudo whoami
sudo adduser employee1
sudo groupadd securityteam
sudo usermod -aG securityteam employee1
groups employee1
```

These activities demonstrated user identity management, administrative privileges, supplementary groups, and the principle of least privilege.

## 5. Process and Resource Monitoring

Examined running processes and system resource consumption using:

```bash
ps
ps aux
top
```

Investigated process identifiers, CPU usage, memory consumption, and the relationship between running services and Linux processes.

## 6. System Services

Inspected SSH service status using systemd:

```bash
systemctl status ssh
systemctl is-enabled ssh
systemctl is-active ssh
systemctl status ssh.socket
```

Observed that Ubuntu used an enabled, listening SSH socket even when `ssh.service` initially appeared inactive.

Also created a custom systemd service and investigated its failure during the troubleshooting phase.

## 7. System Logs

Practiced reading the system journal:

```bash
sudo journalctl -u ssh -n 10 --no-pager
sudo journalctl -n 10 --no-pager
```

Learned how service events, error messages, and system activity can support troubleshooting and security investigations.

## 8. Network Administration

Identified network interfaces and configured connectivity testing.

```bash
ip a
ip -br addr
ping -c 4 1.1.1.1
ping -c 4 google.com
resolvectl status
sudo ss -tulnp
```

The VM used the `enp0s8` interface with IPv4 address `10.0.2.15/24`.

The exercises covered IP addressing, DNS resolution, listening ports, and basic network connectivity.

## 9. Remote Administration

Configured VirtualBox NAT port forwarding to access Ubuntu through OpenSSH from macOS.

The connection was tested successfully using:

```bash
ssh -p 2222 alejandro@127.0.0.1
```

Later, SSH public-key authentication was implemented as part of the security-hardening module.

## Key Takeaways

The administration exercises established practical experience with Linux filesystem operations, account management, process monitoring, systemd, logging, and network diagnostics.

These foundations were applied to four simulated troubleshooting incidents, documented separately in the `incidents/` directory.