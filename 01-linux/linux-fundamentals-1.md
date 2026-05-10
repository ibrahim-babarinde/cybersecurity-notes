# Linux Fundamentals 1 — Notes

## What Is Linux?

Linux is an open source operating system. Unlike Windows, the source
code is publicly available — meaning anyone can read it, modify it,
and build on top of it.

**Why Linux matters in cybersecurity:**
Linux gives you full, unrestricted access to the system. You can
control every process, every file, every network connection at a
deep level. This is why almost every penetration testing tool —
Nmap, Metasploit, Hydra, Gobuster — runs natively on Linux. Kali
Linux, the industry standard hacking operating system, is built
entirely on Linux.

---

## The Terminal

The terminal is how you communicate directly with the operating system
through text commands. Instead of clicking buttons in a graphical
interface, you type instructions and the system executes them.

In cybersecurity, the terminal is everything. Most tools have no
graphical interface at all — they only run through the terminal.
Getting comfortable with it is non-negotiable.

---

## Essential Linux Commands

### Navigation

| Command | What It Does | Example |
|---------|-------------|---------|
| `pwd` | Print working directory — shows where you are | `pwd` |
| `ls` | List files and folders in current location | `ls` |
| `ls -la` | List all files including hidden ones with details | `ls -la` |
| `cd` | Change directory — move to a different folder | `cd Documents` |
| `cd ..` | Go back one directory | `cd ..` |
| `cd ~` | Go directly to home directory | `cd ~` |

### File Management

| Command | What It Does | Example |
|---------|-------------|---------|
| `touch` | Create a new empty file | `touch notes.txt` |
| `mkdir` | Make a new directory | `mkdir new_folder` |
| `cp` | Copy a file or directory | `cp file.txt backup.txt` |
| `mv` | Move or rename a file | `mv old.txt new.txt` |
| `rm` | Remove a file | `rm file.txt` |
| `rm -rf` | Remove a folder and all its contents | `rm -rf folder_name` |

> ⚠️ `rm -rf` is dangerous — it deletes permanently with no confirmation.
> Always double check before running it.

### Reading Files

| Command | What It Does | Example |
|---------|-------------|---------|
| `cat` | Print file contents to terminal | `cat notes.txt` |
| `less` | View file contents page by page | `less notes.txt` |
| `head` | Show first 10 lines of a file | `head notes.txt` |
| `tail` | Show last 10 lines of a file | `tail notes.txt` |

### Searching

| Command | What It Does | Example |
|---------|-------------|---------|
| `find` | Search for files by name or type | `find / -name passwords.txt` |
| `grep` | Search inside files for a keyword | `grep "admin" users.txt` |

### Permissions

| Command | What It Does | Example |
|---------|-------------|---------|
| `chmod` | Change file permissions | `chmod 777 file.txt` |
| `chown` | Change file owner | `chown root file.txt` |
| `sudo` | Run a command as administrator | `sudo apt update` |
| `su` | Switch to another user | `su root` |

---

## The Linux File System

Linux uses a hierarchical tree-like structure starting at the
**root directory** (`/`). Everything on the system — files, folders,
hardware devices — lives somewhere under `/`.

**Key characteristics:**
- **Single root:** Everything descends from `/` including separate
hard drives and network shares
- **Case sensitive:** `Folder` and `folder` are two completely
different directories
- **No drive letters:** There are no `C:` or `D:` drives like Windows.
Hardware mounts directly into folders

---

### Core Directories

**📂 `/home` — User Data**
Stores personal files, configurations, and documents for every
non-root user on the system. Each user gets their own private
subdirectory — for example `/home/ibrahim`. This is where you
land when you first open a terminal as a normal user.

**📂 `/etc` — System Configuration**
Contains administrative configuration files for the operating
system and all installed applications. Examples include network
settings, user databases, and startup scripts. In penetration
testing, `/etc/passwd` and `/etc/shadow` are critical targets —
they store user account information and password hashes.

**📂 `/var` — Variable Data**
Holds files that grow dynamically during system operation —
system logs (`/var/log`), mail spools, database storage, and
printer queues. In penetration testing, `/var/log` is important
because it records system activity that can reveal what happened
on a compromised machine.

**📂 `/tmp` — Temporary Files**
Acts as a scratchpad for applications to store temporary data.
Usually emptied automatically on reboot. In penetration testing,
`/tmp` is commonly used to upload and execute malicious files
because it is almost always writable by any user.

**📂 `/root` — Root User Home**
The isolated home directory dedicated solely to the system
administrator (root user). Separate from `/home` — accessing
`/root` means you have the highest level of privilege on the system.

**📂 `/bin` — Essential Binaries**
Holds essential command binaries required for the system to
function — basic commands like `ls`, `cp`, `mv` live here.

**📂 `/sbin` — System Binaries**
Stores system administration binaries reserved for the root user —
commands for managing the system at a low level.

**📂 `/usr` — User Programs**
Houses user programs, documentation, libraries, and source code.
Most installed software ends up here.

**📂 `/opt` — Optional Software**
Reserved for manually installed optional software packages.

**📂 `/dev` — Device Files**
Maps hardware devices like hard drives, USB drives, and mice as
special files. Everything in Linux is treated as a file — including
hardware.

**📂 `/proc` — Process Information**
A virtual filesystem providing real-time kernel and process
information. Not a real folder on disk — it exists only in memory
and shows you what is currently running on the system.

**📂 `/boot` — Boot Files**
Contains the kernel, bootloader files, and initialization RAM
disks — everything the system needs to start up.

---

## Why This Matters In Penetration Testing

Understanding the Linux file system is critical because when you
compromise a Linux machine, you need to know exactly where to look:

- `/etc/passwd` — list of all users on the system
- `/etc/shadow` — password hashes (requires root to read)
- `/var/log` — system logs showing recent activity
- `/tmp` — writable directory for dropping files
- `/home` — user files, SSH keys, browser history
- `/root` — the highest value target on any Linux machine

Knowing these locations by heart saves time during a real engagement
and is a standard expectation in penetration testing interviews.

---

## Key Takeaways

1. Linux is the foundation of cybersecurity — get comfortable with
it before anything else
2. The terminal is your most important tool — practice daily
3. The file system structure is logical and consistent — learn it
once and it applies everywhere
4. `/etc`, `/tmp`, `/home`, and `/root` are the most important
directories in offensive security
5. `rm -rf` is permanent — always think before you run it

---

## Resources

- [TryHackMe — Linux Fundamentals 1](https://tryhackme.com/room/linuxfundamentalspart1)
- [Linux Command Line Cheatsheet](https://cheatography.com/davechild/cheat-sheets/linux-command-line/)
- [Linux File System Hierarchy Explained](https://www.linux.org/threads/linux-file-system-hierarchy.9024/)

---

*Notes by Ibrahim Babarinde | [GitHub](https://github.com/ibrahim-babarinde) | [TryHackMe](https://tryhackme.com/p/Anzai)*
