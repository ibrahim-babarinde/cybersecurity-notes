# Linux Fundamentals 2 — Notes

## File Permissions

File permissions in Linux determine who can read, write, or execute
a file or directory. Every file has three permission groups:

- **Owner** — the user who created the file
- **Group** — a group of users who share access
- **Others** — everyone else on the system

---

### Reading Permissions

```
rwxr-xr--
```

| Characters | Who | Permissions |
|------------|-----|-------------|
| `rwx` | Owner | Read, Write, Execute |
| `r-x` | Group | Read, Execute |
| `r--` | Others | Read only |

**What each symbol means:**

| Symbol | Meaning |
|--------|---------|
| `r` | Read — can view the file contents |
| `w` | Write — can modify the file |
| `x` | Execute — can run the file as a program |
| `-` | No permission |

---

### Viewing Permissions

```bash
ls -la
```

Example output:
```
-rwxr-xr-- 1 ibrahim ibrahim 1234 May 2026 script.sh
```

Breaking this down:
- `-` — file type (- means regular file, d means directory)
- `rwx` — owner permissions
- `r-x` — group permissions
- `r--` — others permissions
- `ibrahim` — owner name
- `ibrahim` — group name

---

### Numeric Permission System

Permissions can also be represented as numbers:

| Number | Permission | Meaning |
|--------|-----------|---------|
| 7 | rwx | Read + Write + Execute |
| 6 | rw- | Read + Write |
| 5 | r-x | Read + Execute |
| 4 | r-- | Read only |
| 0 | --- | No permissions |

So `chmod 755 file.sh` means:
- Owner → 7 → rwx (full access)
- Group → 5 → r-x (read and execute)
- Others → 5 → r-x (read and execute)

---

## Root User vs Regular User

| | Regular User | Root User |
|--|-------------|-----------|
| Access level | Limited | Unlimited |
| Can install software | No | Yes |
| Can modify system files | No | Yes |
| Can manage other users | No | Yes |
| Can read all files | No | Yes |
| Home directory | `/home/username` | `/root` |

**In penetration testing:**
Getting root access on a target machine is called **privilege
escalation** — one of the most critical skills in offensive security.
It means going from a limited user account to full control of
the system.

---

## Permission Commands

### `chmod` — Change File Permissions

Changes who can read, write, or execute a file.

```bash
# Give owner full access, group and others read/execute
chmod 755 script.sh

# Give everyone full access (dangerous)
chmod 777 file.txt

# Make a script executable
chmod +x script.sh

# Remove write permission from others
chmod o-w file.txt
```

### `chown` — Change File Owner

Changes the owner of a file or directory.

```bash
# Make john the owner of file.txt
chown john file.txt

# Change both owner and group
chown john:developers file.txt

# Change ownership of an entire directory
chown -R john /home/john/documents
```

### `sudo` — Run As Administrator

Runs a single command with root privileges without fully
switching to the root account.

```bash
# Update package list
sudo apt update

# Install a package
sudo apt install nmap

# Edit a system file
sudo nano /etc/hosts
```

**Why `sudo` matters in pentesting:**
When you compromise a machine and find a user who can run `sudo`
without a password, you instantly have root access. Always check
`sudo -l` to see what commands a user can run with sudo.

```bash
# Check what sudo permissions current user has
sudo -l
```

### `su` — Switch User

Switches from your current user account to another user.

```bash
# Switch to root
su

# Switch to a specific user
su ibrahim

# Switch to root and load root's environment
su -
```

---

## SSH — Secure Shell

SSH (Secure Shell) is a cryptographic protocol used to connect
to and control another computer remotely over a network. All
traffic is encrypted — nobody intercepting the connection can
read what is being sent.

**Basic connection syntax:**
```bash
ssh username@ip_address
```

**Example:**
```bash
ssh ibrahim@192.168.1.10
```

This connects to the machine at `192.168.1.10` as the user `ibrahim`.
You will be prompted for a password, then dropped into a terminal
on the remote machine.

---

### SSH With A Key File

Many CTF machines and servers use SSH keys instead of passwords
for authentication:

```bash
ssh -i private_key.pem username@ip_address
```

The `-i` flag specifies the private key file to use.

**Important:** Key files must have strict permissions or SSH
will refuse to use them:

```bash
chmod 600 private_key.pem
```

---

### Why SSH Matters In Penetration Testing

SSH is one of the most common services found on Linux machines.
In penetration testing:

- Finding SSH credentials (through brute force with Hydra or
credential reuse) gives you remote access to a machine
- SSH keys found on a compromised machine can unlock access
to other machines in the network
- Misconfigured SSH (allowing root login, weak passwords,
default credentials) is one of the most common vulnerabilities
found in real engagements

---

## Processes In Linux

A process is any program currently running on the system.
Every process has a unique **PID (Process ID)** assigned by
the kernel when it starts.

---

### Process Management Commands

| Command | What It Does |
|---------|-------------|
| `ps` | Shows processes in current terminal session only |
| `ps aux` | Shows ALL running processes for ALL users with full details |
| `top` | Live real-time process monitor — updates automatically |
| `kill PID` | Stops a process by its PID |
| `pkill name` | Kills a process by its name |
| `jobs` | Shows processes running in the background |
| `bg` | Sends a process to the background |
| `fg` | Brings a background process to the foreground |

---

### `ps` vs `ps aux` vs `top`

**`ps`**
Shows only the processes running in your current terminal session.
Limited view — not very useful on its own.

```bash
ps
```

**`ps aux`**
Shows every process running on the entire system for all users.
The most useful version for getting a full picture of what is running.

```bash
ps aux
```

Output columns:
- `USER` — who owns the process
- `PID` — process ID
- `%CPU` — CPU usage
- `%MEM` — memory usage
- `COMMAND` — what command started the process

**`top`**
A live, continuously updating monitor of all running processes.
Sorts by CPU usage by default. Press `q` to quit.

```bash
top
```

---

### Why Processes Matter In Penetration Testing

When you gain access to a machine, checking running processes
tells you:

- What services are running that might be exploitable
- Whether antivirus or monitoring tools are active
- What other users are doing on the machine
- Whether your malicious process is still running

```bash
# Find a specific process by name
ps aux | grep apache

# Kill a process immediately
kill -9 PID
```

---

## Key Takeaways

1. File permissions control who can do what with every file —
understanding them is essential for both offense and defense
2. Root is the ultimate goal in any Linux privilege escalation
3. `sudo -l` is one of the first commands to run after getting
access to a Linux machine — it shows instant privilege escalation
opportunities
4. SSH is everywhere — knowing how to use it and abuse it is
non-negotiable
5. `ps aux` gives you a complete picture of everything running
on a system — always run it after gaining access to a machine

---

## Resources

- [TryHackMe — Linux Fundamentals 2](https://tryhackme.com/room/linuxfundamentalspart2)
- [Linux Permissions Explained](https://www.guru99.com/file-permissions.html)
- [SSH Explained](https://www.ssh.com/academy/ssh)
- [Linux Process Management](https://www.geeksforgeeks.org/processes-in-linuxunix/)

---

*Notes by Ibrahim Babarinde | [GitHub](https://github.com/ibrahim-babarinde) | [TryHackMe](https://tryhackme.com/p/Anzai)*
