# Lab 03 – Linux User and Group Management

## 1. Project Overview

This practical lab demonstrates Linux user and group administration using Red Hat Enterprise Linux 10.2.

A simulated company environment was created to practise employee account management, department group assignments, password administration, account modification, and account removal.

## 2. Objectives

- Create and manage Linux user accounts.
- Create department groups.
- Understand primary and supplementary groups.
- Assign users to supplementary groups.
- Configure and verify user passwords.
- Explore Linux account databases.
- Modify user account properties.
- Lock and unlock user accounts.
- Delete user accounts and groups.

## 3. Environment

| Component | Details |
|---|---|
| Operating System | Red Hat Enterprise Linux 10.2 |
| Virtualization | Oracle VirtualBox |
| Architecture | x86_64 |
| Administration User | rhvm_1 |
| Shell | Bash |

## 4. Company Scenario

A company has three departments:

- Development
- Operations
- Security

The administrator creates the following employee accounts:

| Username | Department |
|---|---|
| dev01 | Development |
| ops01 | Operations |
| sec01 | Security |

## 5. Practical Implementation

### 5.1 Creating Department Groups

Commands:

```bash
sudo groupadd development
sudo groupadd operations
sudo groupadd security
```

Verification:

```bash
getent group development
getent group operations
getent group security
```

Created groups:

| Group | GID |
|---|---:|
| development | 1001 |
| operations | 1002 |
| security | 1003 |

### 5.2 Creating Employee Accounts

Commands:

```bash
sudo useradd dev01
sudo useradd ops01
sudo useradd sec01
```

Verification:

```bash
getent passwd dev01
getent passwd ops01
getent passwd sec01
```

Home directory verification:

```bash
ls -ld /home/dev01 /home/ops01 /home/sec01
```

Each employee received a home directory and an individual primary group.

### 5.3 Assigning Supplementary Groups

Commands:

```bash
sudo usermod -aG development dev01
sudo usermod -aG operations ops01
sudo usermod -aG security sec01
```

Verification:

```bash
id dev01
id ops01
id sec01
```

The `-aG` option appends supplementary group memberships without replacing existing memberships.

### 5.4 Password Management

Commands:

```bash
sudo passwd dev01
sudo passwd ops01
sudo passwd sec01
```

Password status verification:

```bash
sudo passwd -S dev01
sudo passwd -S ops01
sudo passwd -S sec01
```

The `P` status indicates that a password is configured.

### 5.5 Exploring Linux Account Databases

Important system files:

| File | Purpose |
|---|---|
| /etc/passwd | User account information |
| /etc/shadow | Password hashes and password aging information |
| /etc/group | Group information |

Commands:

```bash
getent passwd dev01
sudo grep -E '^(dev01|ops01|sec01):' /etc/shadow
getent group development
getent group operations
getent group security
```

Note: Password hashes from `/etc/shadow` must never be exposed in public documentation or screenshots.

### 5.6 Modifying User Accounts

Added dev01 to the Operations group:

```bash
sudo usermod -aG operations dev01
```

Changed sec01's login shell:

```bash
sudo usermod -s /bin/bash sec01
```

Verification:

```bash
id dev01
getent passwd sec01
```

### 5.7 Locking and Unlocking User Accounts

Locked ops01:

```bash
sudo passwd -l ops01
```

Verified account status:

```bash
sudo passwd -S ops01
```

Unlocked ops01:

```bash
sudo passwd -u ops01
```

A locked password prevents password-based authentication but does not necessarily disable every authentication method.

### 5.8 Deleting User Accounts

Removed the Security employee account and its home directory:

```bash
sudo userdel -r sec01
```

Verification:

```bash
getent passwd sec01
ls -ld /home/sec01
```

No matching account or directory was returned.

### 5.9 Deleting Department Groups

Removed the Security department group:

```bash
sudo groupdel security
```

Verification:

```bash
getent group security
```

No matching group was returned.

## 6. Final System Verification

Final user accounts:

```bash
getent passwd dev01
getent passwd ops01
```

Final group memberships:

```bash
id dev01
id ops01
```

Final department groups:

```bash
getent group development
getent group operations
```

Final home directories:

```bash
ls -ld /home/dev01 /home/ops01
```

Final configuration:

| User | Primary Group | Supplementary Groups |
|---|---|---|
| dev01 | dev01 | development, operations |
| ops01 | ops01 | operations |

The `sec01` account and `security` group were successfully removed.

## 7. Key Learnings

- Linux users and groups are fundamental to access control.
- Every user has a UID and a primary GID.
- Supplementary groups allow users to access additional group-controlled resources.
- The `usermod -aG` command adds group membership without replacing existing memberships.
- The `/etc/passwd`, `/etc/shadow`, and `/etc/group` files maintain important account information.
- Password locking and unlocking are useful account administration operations.
- The `userdel -r` command removes a user and their home directory.
- Group membership and account information can be verified using `id` and `getent`.

## 8. Conclusion

This lab provided practical experience in Linux user and group administration using RHEL 10.2.

The company scenario demonstrated how a system administrator can create employee accounts, organize users into departments, manage authentication, modify account properties, and safely remove accounts and groups.

These skills are important for Linux system administration, server management, and cloud infrastructure operations.
