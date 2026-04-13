# Linux-Practical-Lab-Users-Groups-Permissions-Sudo-Processes-

# Linux DevSecOps Practical Lab (Users, Groups, Permissions, Sudo, Processes)

## Goal

This lab teaches Linux step by step using real-world DevOps examples.
You will simulate a small server environment with users, teams, permissions, admin actions, and running processes.

---

# 1. Users and Identity (Real Server View)

## Concept

Every Linux user has:

* UID (user ID)
* primary group (GID)
* extra groups (sudo, dev teams, etc.)

## Real checks

```bash
whoami
id
id -u
groups
```

## Real example (server user)

```
uid=1000(alice) gid=1000(alice) groups=1000(alice),27(sudo),1001(devops_team)
```

## See user in system database

```bash
grep "^$(whoami):" /etc/passwd
```

Example:

```
alice:x:1000:1000:Alice:/home/alice:/bin/bash
```

What this means in real life:

* Alice has a home folder
* Default shell is bash
* UID 1000 = normal human user

---

# 2. Users and Groups (Dev Team Simulation)

## Scenario

You are a DevOps engineer. You create a team called "devops_team" for developers.

## Create team

```bash
sudo groupadd devops_team
```

## Create real users

```bash
sudo useradd -m alice
sudo useradd -m bob
sudo useradd -m charlie
```

## Add them to team

```bash
sudo usermod -aG devops_team alice
sudo usermod -aG devops_team bob
sudo usermod -aG devops_team charlie
```

## Verify team

```bash
getent group devops_team
```

Example output:

```
devops_team:x:1002:alice,bob,charlie
```

Real meaning:

* These users can now share access to the same resources

---

# 3. File Permissions (Real Project Folder)

## Scenario

You are protecting a shared project folder used by developers.

## Create folder

```bash
sudo mkdir /project
```

## Assign ownership to team

```bash
sudo chown :devops_team /project
```

## Set secure permissions

```bash
sudo chmod 770 /project
```

## Check result

```bash
ls -ld /project
```

Expected:

```
drwxrwx--- root devops_team /project
```

## Real behavior test

```bash
touch /project/app.py
```

Results:

* alice → allowed
* bob → allowed
* other users → permission denied

## Real use case

This is exactly how companies protect:

* Git repositories
* deployment folders
* CI/CD artifacts

---

# 4. File Permissions Deep Understanding

## Example files in real system

```bash
ls -l
```

Example:

```
-rw-r--r-- 1 alice devops_team config.yaml
-rwxr-xr-x 1 alice devops_team deploy.sh
-rw------- 1 alice alice secrets.txt
```

## Meaning in real world

### config.yaml

* readable by everyone
* used for configuration sharing

### deploy.sh

* executable script
* used in CI/CD pipelines

### secrets.txt

* only owner can access
* used for API keys or passwords

---

# 5. sudo (Admin Power in Real Systems)

## Scenario

You are installing tools on a server.

## Install package

```bash
sudo apt update
sudo apt install htop
```

## Restart service

```bash
sudo systemctl restart ssh
```

## Check permissions

```bash
sudo -l
```

Example:

```
(alice) ALL : ALL
```

## Root shell (danger zone)

```bash
sudo -i
whoami
```

Output:

```
root
```

Exit immediately:

```bash
exit
```

Real rule:
Never stay root longer than needed.

---

# 6. Processes (Real Server Behavior)

## Scenario

A background service is running.

## Start process

```bash
sleep 300 &
```

## Find process

```bash
ps aux | grep sleep
```

Example:

```
alice 2456 0.0 0.0 8160 800 pts/0 S sleep 300
```

## Kill process (normal way)

```bash
kill 2456
```

## Force kill (if stuck)

```bash
kill -9 2456
```

Real use cases:

* stopping broken services
* killing stuck scripts
* restarting pipelines

---

# FINAL LAB (REAL COMPANY SCENARIO)

## Mission: Build secure DevOps environment

### Step 1: Create security team

```bash
sudo groupadd secure_team
sudo useradd -m dev1
sudo useradd -m dev2
sudo useradd -m dev3
sudo usermod -aG secure_team dev1
sudo usermod -aG secure_team dev2
sudo usermod -aG secure_team dev3
```

---

### Step 2: Create secure directory (like production folder)

```bash
sudo mkdir /secure_data
sudo chown :secure_team /secure_data
sudo chmod 770 /secure_data
```

---

### Step 3: Validate access

```bash
ls -ld /secure_data
```

Expected:

```
drwxrwx--- root secure_team /secure_data
```

---

### Step 4: Simulate application log

```bash
touch /secure_data/app.log
```

Add data:

```bash
echo "system started" >> /secure_data/app.log
```

---

### Step 5: sudo admin workflow

```bash
sudo -l
sudo -i
whoami
exit
```

---

### Step 6: Process simulation (real service)

```bash
sleep 200 &
ps aux | grep sleep
kill <PID>
```

---

# RESULT

After this lab you understand real Linux server behavior:

* user management in production
* group-based access control
* file security model
* sudo administration
* process lifecycle management

---

# NEXT LEVEL

* chmod symbolic mode (u+x, g-w)
* ACL permissions (advanced access control)
* systemd services (real production services)
* privilege escalation basics (DevSecOps security)
