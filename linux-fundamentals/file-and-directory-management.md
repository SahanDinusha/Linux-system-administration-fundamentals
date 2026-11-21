
# Practical Lab 02: Linux File and Directory Management

## 1. Objective

Practise creating, organizing, copying, moving, renaming, and deleting files and directories in a Linux environment.

## 2. Environment

- Operating System: Red Hat Enterprise Linux 10.2
- Environment: Oracle VirtualBox
- User: rhvm_1
- Shell: Bash

## 3. Practical Scenario

Simulated a company server directory structure to organize application files, configuration files, backups, logs, and deployment documents.

## 4. Implementation

### 4.1 Create directories

Used `mkdir` to create the company-server directory structure.

```bash
mkdir company-server
cd company-server
mkdir applications configurations backups logs deployments
```

### 4.2 Create multiple files

Used `touch` and Bash brace expansion to create multiple files.

```bash
touch applications/{app1.txt,app2.txt}
touch configurations/{database.conf,server.conf}
touch logs/{application.log,error.log}
touch deployments/deployment-notes.txt
```

### 4.3 Write configuration content

Used `echo` with output redirection.

```bash
echo "DB_HOST=localhost" > configurations/database.conf
echo "DB_PORT=3306" >> configurations/database.conf
echo "DB_NAME=company_db" >> configurations/database.conf
```

- `>` creates or overwrites file content.
- `>>` appends content to an existing file.

### 4.4 Create configuration backups

Used `cp` to copy important configuration files.

```bash
cp configurations/database.conf backups/
cp configurations/server.conf backups/
```

Used `diff` to compare original and backup files.

```bash
diff configurations/database.conf backups/database.conf
diff configurations/server.conf backups/server.conf
```

No output from `diff` confirmed that the compared files were identical.

### 4.5 Move and rename files

Renamed a deployment document:

```bash
mv deployments/deployment-notes.txt deployments/release-notes.txt
```

Moved an application file:

```bash
mv applications/app1.txt deployments/
```

### 4.6 Copy log files

```bash
cp logs/application.log backups/
```

### 4.7 Delete files and directories

Created and deleted a temporary file:

```bash
touch applications/temporary.txt
rm applications/temporary.txt
```

Created and removed an empty directory:

```bash
mkdir test-directory
rmdir test-directory
```

## 5. Final Directory Structure

```text
company-server/
├── applications/
│   └── app2.txt
├── backups/
│   ├── application.log
│   ├── database.conf
│   └── server.conf
├── configurations/
│   ├── database.conf
│   └── server.conf
├── deployments/
│   ├── app1.txt
│   └── release-notes.txt
└── logs/
    ├── application.log
    └── error.log
```

## 6. Commands Learned

| Command | Purpose |
|---|---|
| mkdir | Create directories |
| touch | Create empty files |
| echo | Write text |
| cat | Display file content |
| cp | Copy files |
| mv | Move or rename files |
| rm | Remove files |
| rmdir | Remove empty directories |
| ls | List directory contents |
| find | Search for files and directories |
| diff | Compare files |

## 7. Key Learnings

- Organizing application files using directories.
- Creating multiple files efficiently using brace expansion.
- Writing and appending configuration data.
- Creating and verifying file backups.
- Moving and renaming files.
- Safely deleting temporary files and empty directories.
- Inspecting directory structures using `find`.

## 8. Conclusion

Successfully completed a practical Linux file and directory management exercise by simulating common system administration operations on a company server.
:wq!






