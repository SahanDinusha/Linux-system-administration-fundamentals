# Lab 04 – Linux File Permissions and Ownership

## 1. Project Overview

This practical lab demonstrates Linux file permission and ownership management using Red Hat Enterprise Linux 10.2.

A simulated company shared-storage environment was created to practise access control, file ownership, directory permissions, and special permission bits.

The lab focuses on how Linux administrators manage access to files and directories while maintaining system security.

## 2. Objectives

- Understand Linux file and directory permissions.
- Manage permissions using `chmod`.
- Change file ownership using `chown` and `chgrp`.
- Understand numeric and symbolic permission representations.
- Configure shared directories for multiple users.
- Implement SUID, SGID, and Sticky Bit permissions.
- Understand how `umask` affects default permissions.
- Verify access control using different Linux user accounts.

## 3. Environment

| Component | Details |
|---|---|
| Operating System | Red Hat Enterprise Linux 10.2 |
| Virtualization | Oracle VirtualBox |
| Shell | Bash |
| Administrative User | rhvm_1 |
| Employee Accounts | dev01, ops01 |
| Department Group | operations |

## 4. Company Scenario

A company has Development and Operations employees who need access to shared resources.

The administrator must configure a shared-storage environment with the following requirements:

- Authorized employees should be able to collaborate on reports.
- Files created inside shared directories should inherit the correct department group.
- Employees should not be able to delete other employees' files in a shared drop directory.
- Access should be restricted to authorized users.

The following directory structure was created:

```text
/srv/company-share/
├── reports/
└── team-drop/
```

## 5. Practical Implementation

### 5.1 Understanding Linux File Permissions

Linux uses three permission categories:

- User (u): The file owner.
- Group (g): Members of the file's group.
- Others (o): All remaining users.

Three basic permissions are available:

| Permission | Symbol | Numeric Value |
|---|---|---|
| Read | r | 4 |
| Write | w | 2 |
| Execute | x | 1 |

Example:

```text
-rwxr-xr--
```

This represents:

- Owner: Read, write, execute.
- Group: Read, execute.
- Others: Read only.

The first character identifies the file type. The remaining nine characters represent the permissions assigned to the owner, group, and others.

### 5.2 Managing Permissions Using chmod

The `chmod` command modifies file and directory permissions.

**Numeric permission example:**

```bash
touch /tmp/lab04-permissions-demo.txt

chmod 640 /tmp/lab04-permissions-demo.txt

ls -l /tmp/lab04-permissions-demo.txt
```

Permission `640` represents:

- Owner: Read and write (6).
- Group: Read only (4).
- Others: No permissions (0).

**Symbolic permission example:**

```bash
chmod g+w /tmp/lab04-permissions-demo.txt

chmod o+r /tmp/lab04-permissions-demo.txt

ls -l /tmp/lab04-permissions-demo.txt
```

Common symbolic operators:

| Symbol | Meaning |
|---|---|
| u | User/owner |
| g | Group |
| o | Others |
| a | All |
| + | Add permission |
| - | Remove permission |
| = | Set exact permissions |

### 5.3 Managing File Ownership

The `chown` command changes file ownership.

```bash
touch /tmp/lab04-ownership-demo.txt

sudo chown dev01:development /tmp/lab04-ownership-demo.txt

ls -l /tmp/lab04-ownership-demo.txt
```

The `chgrp` command changes only the group ownership.

```bash
sudo chgrp operations /tmp/lab04-ownership-demo.txt

ls -l /tmp/lab04-ownership-demo.txt
```

The `-R` option allows ownership changes recursively across a directory and its contents.

Example:

```bash
sudo chown -R dev01:development /some/directory
```

### 5.4 Creating the Company Shared-Storage Environment

Created the shared directory structure:

```bash
sudo mkdir -p /srv/company-share/{reports,team-drop}
```

Assigned the ownership:

```bash
sudo chown -R root:operations /srv/company-share
```

Configured the directory permissions:

```bash
sudo chmod 2770 /srv/company-share

sudo chmod 2770 /srv/company-share/reports

sudo chmod 3770 /srv/company-share/team-drop
```

Permission configuration:

| Directory | Permission | Purpose |
|---|---|---|
| company-share | 2770 | Group collaboration with SGID |
| reports | 2770 | Shared report management |
| team-drop | 3770 | Shared access with SGID and Sticky Bit |

### 5.5 Understanding SGID (Set Group ID)

The SGID permission on a directory makes newly created files and subdirectories inherit the directory's group ownership.

This is useful in shared environments where multiple employees need to work with files belonging to the same department group.

Example:

```bash
ls -ld /srv/company-share/reports
```

The `s` character in the group permission position indicates SGID.

### 5.6 Testing Shared Group Access

Created a report as the Development employee:

```bash
sudo -u dev01 bash -c 'umask 0002; echo "Report created by dev01" > /srv/company-share/reports/dev01-report.txt'
```

Created another report as the Operations employee:

```bash
sudo -u ops01 bash -c 'umask 0002; echo "Report created by ops01" > /srv/company-share/reports/ops01-report.txt'
```

Verified the files:

```bash
ls -l /srv/company-share/reports/
```

Tested collaboration by modifying the Development employee's report as the Operations employee:

```bash
sudo -u ops01 bash -c 'echo "Updated by ops01" >> /srv/company-share/reports/dev01-report.txt'
```

Verified the updated content:

```bash
cat /srv/company-share/reports/dev01-report.txt
```

This demonstrates how SGID and group-write permissions support collaboration between department members.

### 5.7 Understanding Sticky Bit

The Sticky Bit prevents ordinary users from deleting or renaming files belonging to other users inside a shared directory.

Created files using different employee accounts:

```bash
sudo -u dev01 touch /srv/company-share/team-drop/dev01-file.txt

sudo -u ops01 touch /srv/company-share/team-drop/ops01-file.txt
```

Attempted to delete the Development employee's file using the Operations account:

```bash
sudo -u ops01 rm /srv/company-share/team-drop/dev01-file.txt
```

The operation was denied because the Sticky Bit restricts deletion of files owned by other users.

The file owner can still delete their own file:

```bash
sudo -u dev01 rm /srv/company-share/team-drop/dev01-file.txt
```

The `t` character in the others' execute permission position indicates the Sticky Bit.

### 5.8 Understanding SUID (Set User ID)

SUID is a special permission used on executable files.

When an executable has SUID enabled, it runs with the effective user ID of the file owner.

The Linux password utility provides a common example:

```bash
ls -l /usr/bin/passwd
```

A typical permission representation is:

```text
-rwsr-xr-x
```

The `s` character in the owner's execute permission position indicates SUID.

This allows the password utility to perform privileged password updates.

### 5.9 Understanding umask

The `umask` command controls which permissions are removed from the default permissions when new files and directories are created.

Checked the current umask:

```bash
umask
```

Tested umask `0027`:

```bash
umask 0027

touch /tmp/lab04-file-0027

mkdir /tmp/lab04-dir-0027
```

Tested umask `0002`:

```bash
umask 0002

touch /tmp/lab04-file-0002

mkdir /tmp/lab04-dir-0002
```

Verified the resulting permissions:

```bash
ls -l /tmp/lab04-file-*

ls -ld /tmp/lab04-dir-*
```

Typical permission results:

| Umask | New File | New Directory |
|---|---|---|
| 0027 | 640 | 750 |
| 0002 | 664 | 775 |

The default permissions before applying umask are:

- Files: 666
- Directories: 777

The umask removes selected permissions from these defaults.

## 6. Final System Verification

The following commands were used to verify the shared-storage configuration:

```bash
echo "=== Shared Directory Permissions ==="

ls -ld /srv/company-share

ls -ld /srv/company-share/reports

ls -ld /srv/company-share/team-drop

echo "=== Report Files ==="

ls -l /srv/company-share/reports/

echo "=== Ownership Information ==="

stat /srv/company-share/reports/dev01-report.txt

echo "=== SUID Example ==="

ls -l /usr/bin/passwd

echo "=== Sticky Bit Example ==="

ls -ld /tmp
```

## 7. Key Learnings

- Linux permissions control access to files and directories.
- `chmod` manages read, write, and execute permissions.
- `chown` and `chgrp` manage file ownership.
- Numeric and symbolic permission methods provide different ways to configure access.
- SGID enables consistent group ownership in shared directories.
- Sticky Bit prevents users from deleting files owned by other users in shared directories.
- SUID allows executable programs to run with the effective identity of their owner.
- `umask` controls default permissions for newly created files and directories.
- Proper permission management is important for Linux security and multi-user administration.

## 8. Conclusion

This lab provided practical experience in Linux file permission and ownership management using Red Hat Enterprise Linux 10.2.

A simulated company shared-storage environment was configured to demonstrate group collaboration, access restrictions, and special permissions.

These concepts are important for system administration, server security, and managing shared resources in enterprise Linux environments.

## 9. Evidence and Screenshots

Screenshots demonstrating the practical implementation are stored in the repository's `screenshots/` directory.

Evidence includes:

- Numeric and symbolic permission modifications.
- File ownership and group ownership changes.
- Shared directory permission configuration.
- Group ownership inheritance.
- Sticky Bit access restriction.
- SUID and SGID examples.
- umask experiments and final verification.
