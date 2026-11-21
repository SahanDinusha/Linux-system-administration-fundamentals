# Linux Filesystem Exploration

## Objective
Understand the Linux filesystem hierarchy and explore
important directories in Red Hat Enterprise Linux 10.2.

## Environment
- OS: Red Hat Enterprise Linux 10.2
- Virtualization: Oracle VirtualBox

## Commands Practised

### 1. Explore root directory

Command:
ls -la /

Purpose:
Displays all files and directories in the root filesystem,
including hidden entries and detailed information.

### 2. Explore system logs

Command:
ls -lh /var/log

Purpose:
Displays log files and directories with human-readable sizes.

## Key Learnings

- Linux uses a hierarchical filesystem.
- The root directory is represented by /.
- /etc stores system configuration files.
- /var/log stores system logs.
- /home contains normal users' home directories.
- Modern RHEL uses a merged /usr structure.
