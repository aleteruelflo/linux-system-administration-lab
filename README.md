# Linux System Administration & Troubleshooting Lab

A hands-on Linux administration and security lab built using Ubuntu Server 24.04 LTS and Oracle VirtualBox on an Apple Silicon Mac.

## Project Overview

This project documents the deployment, administration, security hardening, and troubleshooting of a virtualized Linux server.

The objective was to develop practical Linux administration skills through real command-line operations, security configurations, and simulated incident investigations.

## Lab Environment

| Component | Configuration |
|---|---|
| Host machine | Apple Silicon Mac |
| Virtualization | Oracle VirtualBox |
| Operating system | Ubuntu Server 24.04 LTS (ARM64) |
| Memory | 2 GB |
| Processors | 2 vCPUs |
| Storage | 25 GB virtual disk |
| Networking | VirtualBox NAT with SSH port forwarding |
| Remote access | OpenSSH with Ed25519 key authentication |
| Firewall | UFW |

## Technical Skills Demonstrated

- Linux command-line navigation and filesystem management
- File ownership, permissions, and access control lists (ACLs)
- User and group administration
- Process monitoring and systemd service management
- Network diagnostics using ping, curl, ip, and ss
- SSH key authentication and server hardening
- Firewall configuration using UFW
- Linux service troubleshooting and incident investigation

## Security Hardening

The Ubuntu server was configured with:

- SSH public-key authentication
- Password-based SSH login disabled
- Direct root SSH login disabled
- UFW enabled with a default-deny incoming policy
- SSH traffic explicitly permitted
- Available package updates applied

## Troubleshooting Incidents

Four simulated incidents were investigated:

1. **Service Failure:** Diagnosed and repaired a systemd service with an invalid executable path.
2. **File Permission Denial:** Restored authorized access using Unix permissions and targeted ACLs.
3. **Network Connectivity Failure:** Identified a service port mismatch using curl and socket inspection.
4. **Access Control Investigation:** Diagnosed and resolved read-access restrictions for a monitoring status file.

Detailed reports are maintained in the `incidents/` directory.

## Documentation

- `docs/lab-architecture.md` — Virtual machine and networking architecture
- `docs/linux-administration.md` — Linux administration activities and commands
- `docs/security-hardening.md` — SSH and firewall security configuration
- `incidents/` — Simulated incident investigations and resolutions
- `evidence/screenshots/` — Selected supporting evidence

## Project Outcome

Completed a Linux administration lab demonstrating fundamental system administration, security configuration, and troubleshooting practices.

The project emphasizes understanding system behavior, investigating problems using evidence, applying least-privilege principles, and verifying technical solutions.
