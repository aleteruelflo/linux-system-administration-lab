# Incident 004 — Linux Access Control Investigation

## Incident Summary

| Field | Details |
|---|---|
| Incident ID | LAB-004 |
| Category | Linux Access Control & Troubleshooting |
| Environment | Ubuntu Server 24.04.5 LTS |
| Affected user | `employee1` |
| Affected resource | `/home/alejandro/final-lab/status.txt` |
| Severity | Low — Controlled Lab |
| Status | Resolved |
| Incident type | Simulated file-access failure |

## 1. Problem Description

During the final Linux troubleshooting assessment, a simulated monitoring process was unable to read a system status file.

The monitoring process was represented by the Linux user `employee1`.

The objective was to independently investigate the failure, identify the relevant access-control restrictions, restore authorized read access, and verify the result.

## 2. Lab Setup

A test directory and status file were created:

```bash
mkdir -p ~/final-lab
echo "SYSTEM STATUS: HEALTHY" > ~/final-lab/status.txt
```

The status file was initially restricted using:

```bash
chmod 600 ~/final-lab/status.txt
```

This allowed the owner to read and write the file while denying access through the ordinary group and other permission bits.

## 3. Observed Failure

The monitoring account attempted to read the file:

```bash
sudo -u employee1 cat /home/alejandro/final-lab/status.txt
```

The access attempt failed with:

```text
Permission denied
```

This confirmed that the account could not read the status information.

## 4. Investigation

The investigation focused on Linux file ownership, user identity, group membership, and filesystem permissions.

Relevant commands included:

```bash
ls -l ~/final-lab/status.txt
ls -ld ~/final-lab
id employee1
```

The initial status file permissions were:

```text
-rw-------
```

The affected user belonged to the `securityteam` group.

However, the file's initial permission settings did not grant that group read access.

The investigation also considered directory traversal permissions because access to a file requires appropriate permissions along its directory path.

## 5. Root Cause

**Insufficient access permissions for the monitoring account.**

The status file was initially accessible only to its owner through ordinary Unix file permissions.

The monitoring account, `employee1`, was not the file owner and therefore could not read the file using its initial permissions.

This incident also depended on permissions inherited from the existing lab environment. During an earlier exercise, a targeted ACL had already granted `employee1` traversal permission on `/home/alejandro`.

## 6. Resolution

The status file's group ownership was changed to `securityteam`:

```bash
sudo chgrp securityteam ~/final-lab/status.txt
```

The file permissions were adjusted:

```bash
chmod 640 ~/final-lab/status.txt
```

The resulting permission model was:

| Category | Permissions |
|---|---|
| Owner | Read and write |
| Securityteam group | Read only |
| Others | No permissions |

This allowed members of `securityteam` to read the status file without granting ordinary group write permission.

No broad world-readable or world-writable permissions were introduced.

## 7. Verification

The original access test was repeated:

```bash
sudo -u employee1 cat /home/alejandro/final-lab/status.txt
```

The command succeeded:

```text
SYSTEM STATUS: HEALTHY
```

This confirmed that `employee1` could read the status file.

**Final status: Resolved.**

## 8. Security Considerations

The solution followed the principle of least privilege by allowing the monitoring account to read the necessary information without granting unnecessary write access.

However, access to the status file also depends on directory traversal permissions and any applicable ACLs.

Further validation of the complete directory permissions would be appropriate before deploying a similar access-control design in a production environment.

A group-readable file is not necessarily group-modifiable, and the ability to delete a file is primarily governed by permissions on its parent directory.

## 9. Lessons Learned

- File access depends on ownership, group membership, and permissions.
- `chmod 600` restricts ordinary access to the owner.
- `chgrp` can assign a file to a shared access group.
- `chmod 640` permits group read access without group write access.
- Directory traversal permissions must also be considered.
- Existing ACLs can influence later troubleshooting exercises.
- Testing access using the affected account is essential.
- Changes should be verified rather than assumed successful.

## 10. Evidence

The final assessment was completed in the same Ubuntu training environment used for the earlier incidents.

The successful verification returned:

```text
SYSTEM STATUS: HEALTHY
```

A dedicated screenshot of this incident is not yet included in the repository. The result was verified during the practical lab exercise and can be reproduced during the final evidence collection stage.

## 11. Outcome

The simulated monitoring account successfully regained read access to the status file.

The exercise demonstrated independent troubleshooting using Linux identities, group ownership, filesystem permissions, and previously configured access controls.

---

*This incident was intentionally simulated in a controlled educational environment. No production infrastructure or confidential company information was involved.*