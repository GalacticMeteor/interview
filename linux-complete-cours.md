# LINUX REVISION GUIDE FOR DEVOPS ENGINEERS

---

## 1. FILE AND DIRECTORY MANAGEMENT

### Basic Commands
| Command | Purpose | Example |
|---------|---------|---------|
| `mkdir -p` | Create directories with parents, no error if exists | `mkdir -p /home/user/project/src` |
| `mkdir -p {a,b}` | Brace expansion - create multiple directories | `mkdir -p project/{src,bin,docs}` |

### Environment Variables
| Command | Purpose | Example |
|---------|---------|---------|
| `echo $SHELL` | Shows current shell | `/bin/bash` |
| `echo $HOME` | Shows home directory path | `/home/username` |
| `sudo chsh -s /bin/sh bob` | Change user's login shell | Change bob's shell to sh |
| `export VAR=value` | Set environment variable (session) | `export PROJECT=MERCURY` |
| `echo "export VAR=value" >> ~/.profile` | Make variable persistent | Survives reboot |

### ~/.bashrc vs ~/.profile
| File | Purpose | When it Runs |
|------|---------|--------------|
| `~/.bashrc` | Shell config for interactive non-login shells | Every new terminal window |
| `~/.profile` | Shell config for login shells | On login (console, SSH, GUI) |

### Shell Customization
```bash
# Create persistent alias
echo 'alias up=uptime' >> ~/.profile

# Customize prompt (PS1)
PS1='[\d]\u@\h:\w$'
```

**PS1 Variables:**
- `\d` - Current date (Weekday Month Date)
- `\u` - Username
- `\h` - Hostname (up to first dot)
- `\w` - Current working directory
- `$` - Dollar sign (normal users)
- `#` - Hash sign (root user)

---

## 2. SYSTEM INFORMATION & KERNEL

| Command | Purpose | Example/Notes |
|---------|---------|---------------|
| `uname -r` | Show kernel release version | `5.15.0-67-generic` |
| `uname -a` | Show all system information | Kernel, hostname, architecture |
| `dmesg` | Print kernel messages | `dmesg \| grep error` |
| `sudo ls -l /sbin/init` | Show init process (systemd) | Check system init system |
| `sudo lshw` | List hardware (full tree) | Detailed hardware info |
| `lshw -class cpu` | Show specific hardware class | Classes: cpu, memory, disk, network |

### /bin vs /sbin
| Directory | Purpose | Who Uses It |
|-----------|---------|-------------|
| `/bin` | Essential user binaries (ls, cat, cp) | All users |
| `/sbin` | System binaries (init, fdisk, ifconfig) | Root/admin only |

---

## 3. SYSTEMD & SERVICES

### Boot Targets
| Command | Purpose | Common Targets |
|---------|---------|----------------|
| `sudo systemctl get-default` | Show default boot target | Current target |
| `sudo systemctl set-default multi-user.target` | Change default boot target | Persistent change |
| `sudo systemctl isolate multi-user.target` | Switch target immediately | Temporary change |

**Common Targets:**
- `multi-user.target` → Text-mode, multi-user (no GUI)
- `graphical.target` → GUI mode
- `rescue.target` → Single-user, emergency mode

### Service Management
| Command | Purpose |
|---------|---------|
| `sudo systemctl status <service>` | Check service status |
| `sudo systemctl start <service>` | Start service |
| `sudo systemctl stop <service>` | Stop service |
| `sudo systemctl restart <service>` | Restart service |
| `sudo systemctl enable <service>` | Enable service at boot |
| `sudo systemctl disable <service>` | Disable service at boot |
| `sudo systemctl daemon-reload` | Reload systemd configuration |

### Logging with journalctl
| Command | Purpose |
|---------|---------|
| `sudo journalctl` | Show all logs |
| `journalctl -u <service>` | Show logs for specific service |
| `journalctl -b` | Show logs since last boot |
| `journalctl -f` | Follow logs in real-time (like tail -f) |
| `journalctl --since "2026-01-27"` | Logs since specific date |

### Sample Systemd Unit File
```ini
[Unit]
Description=Sample service description

[Service]
Restart=always
ExecStart=/bin/bash /root/sample_script.sh

[Install]
WantedBy=multi-user.target
```

**After editing:** `sudo systemctl daemon-reload`

---

## 4. DISK & PARTITION MANAGEMENT

### Disk Information Commands
| Command | Purpose | Example/Notes |
|---------|---------|---------------|
| `lsblk` | List block devices in tree format | Shows partitions, mount points |
| `lsblk -f` | Add filesystem type, UUID, labels | For /etc/fstab reference |
| `blkid /dev/sda1` | Show UUID, TYPE, LABEL | Essential for fstab entries |
| `df -h` | Show disk usage (human-readable) | Mounted filesystems |
| `du -sh /path` | Disk usage of directory | Total size of folder |
| `sudo fdisk -l` | List partition tables (MBR) | Shows all partitions |
| `sudo parted -l` | List partitions (GPT support) | Better for GPT disks |
| `mount` | Show mounted filesystems | Current mounts |
| `sudo umount /mnt/usb` | Unmount filesystem | Safely remove |

### MBR vs GPT
| Feature | MBR | GPT |
|---------|-----|-----|
| Max Partitions | 4 primary OR 3 primary + 1 extended | 128 (default) |
| Max Disk Size | 2 TB | >2 TB (9.4 ZB) |
| Boot Mode | BIOS | UEFI |
| Partition Structure | Primary, Extended, Logical | All primary-like |

**ASCII Diagram - MBR Layout:**
```
┌──────────────────────────────────────────┐
│  MBR (Master Boot Record)                │
├──────────┬──────────┬──────────┬─────────┤
│ Primary1 │ Primary2 │ Primary3 │ Extended│
│          │          │          │ ┌───┬───┤
│          │          │          │ │L1 │L2 │
└──────────┴──────────┴──────────┴─┴───┴───┘
```

### Creating and Mounting Filesystem
```bash
# Create ext4 filesystem
sudo mkfs.ext4 /dev/vde

# Create mount point
sudo mkdir /mnt/data

# Mount filesystem
sudo mount /dev/vde /mnt/data

# Make persistent (add to /etc/fstab)
echo "/dev/vde /mnt/data ext4 defaults 0 0" | sudo tee -a /etc/fstab

# Or use UUID (recommended)
echo "UUID=<uuid> /mnt/data ext4 defaults 0 0" | sudo tee -a /etc/fstab
```

**Interview Tip:** Always use UUID in `/etc/fstab` for stable mounts across reboots.

---

## 5. LVM (LOGICAL VOLUME MANAGEMENT)

### LVM Architecture
```
┌─────────────────────────────────────────────┐
│ Physical Disks: /dev/sdb, /dev/sdc          │
└─────────────┬───────────────────────────────┘
              │ pvcreate
┌─────────────▼───────────────────────────────┐
│ Physical Volumes (PV): /dev/sdb, /dev/sdc   │
└─────────────┬───────────────────────────────┘
              │ vgcreate
┌─────────────▼───────────────────────────────┐
│ Volume Group (VG): vg_data                  │
└─────────────┬───────────────────────────────┘
              │ lvcreate
┌─────────────▼───────────────────────────────┐
│ Logical Volumes (LV): lv_data, lv_backup    │
└─────────────────────────────────────────────┘
```

### LVM Commands
| Command | Purpose | Example |
|---------|---------|---------|
| `sudo pvcreate /dev/sdb` | Create physical volume | Initialize disk for LVM |
| `sudo vgcreate vg_data /dev/sdb /dev/sdc` | Create volume group | Combine PVs |
| `sudo lvcreate -L 10G -n lv_data vg_data` | Create logical volume | 10GB LV named lv_data |
| `sudo lvs` | List logical volumes | Show all LVs |
| `sudo vgs` | List volume groups | Show all VGs |
| `sudo pvs` | List physical volumes | Show all PVs |

### LVM Workflow Example
```bash
# 1. Create physical volumes
sudo pvcreate /dev/sdb /dev/sdc

# 2. Create volume group
sudo vgcreate vg_data /dev/sdb /dev/sdc

# 3. Create logical volume
sudo lvcreate -L 10G -n lv_data vg_data

# 4. Create filesystem
sudo mkfs.ext4 /dev/vg_data/lv_data

# 5. Mount
sudo mkdir /mnt/data
sudo mount /dev/vg_data/lv_data /mnt/data
```

### Resizing LVM
```bash
# Extend logical volume by 500MB
sudo lvresize -L +500M /dev/mapper/vg_data-lv_data

# Resize filesystem to use new space
sudo resize2fs /dev/mapper/vg_data-lv_data
```

---

## 6. PACKAGE MANAGEMENT

### RPM (Red Hat Package Manager)
| Option | Purpose | Example |
|--------|---------|---------|
| `-ivh` | Install package (verbose + progress) | `rpm -ivh nginx.rpm` |
| `-Uvh` | Upgrade or install package | `rpm -Uvh nginx.rpm` |
| `-e` | Remove (erase) package | `rpm -e nginx` |
| `-qa` | List all installed packages | `rpm -qa` |
| `-q` | Check if package is installed | `rpm -q nginx` |
| `-ql` | List files installed by package | `rpm -ql nginx` |
| `-qf` | Find which package owns a file | `rpm -qf /etc/nginx/nginx.conf` |
| `-V` | Verify package integrity | `rpm -V nginx` |
| `-qi` | Show package information | `rpm -qi nginx` |

### YUM (Yellowdog Updater Modified)
| Command | Purpose |
|---------|---------|
| `sudo yum install <package>` | Install package |
| `sudo yum update` | Update all packages |
| `sudo yum update <package>` | Update specific package |
| `sudo yum remove <package>` | Remove package |
| `sudo yum search <keyword>` | Search for packages |
| `sudo yum info <package>` | Show package info |
| `sudo yum list installed` | List installed packages |
| `sudo yum repolist` | List enabled repositories |
| `sudo yum provides <file>` | Find package providing file |
| `sudo yum clean all` | Clean cache |

### DPKG (Debian Package Manager)
| Command | Purpose |
|---------|---------|
| `sudo dpkg -i package.deb` | Install package |
| `sudo dpkg -r package` | Remove package (keep config) |
| `sudo dpkg -P package` | Purge package (remove all) |
| `dpkg -l` | List installed packages |
| `dpkg -l \| grep package` | Search installed package |
| `dpkg -L package` | List files installed by package |
| `dpkg -S /path/to/file` | Find package owning file |
| `dpkg -s package` | Show package status |

### APT (Advanced Package Tool)
| Command | Purpose |
|---------|---------|
| `sudo apt update` | Update package lists |
| `sudo apt upgrade` | Upgrade all packages |
| `sudo apt install <package>` | Install package |
| `sudo apt remove <package>` | Remove package (keep config) |
| `sudo apt purge <package>` | Remove package + config |
| `sudo apt autoremove` | Remove unused dependencies |
| `apt search <keyword>` | Search for packages |
| `apt show <package>` | Show package details |
| `apt list --installed` | List installed packages |

---

## 7. COMPRESSION & ARCHIVING

### TAR (Tape Archive)
| Option | Purpose | Example |
|--------|---------|---------|
| `-c` | Create archive | `tar -cf archive.tar /path` |
| `-x` | Extract archive | `tar -xf archive.tar` |
| `-v` | Verbose (show files) | `tar -cvf archive.tar /path` |
| `-f` | Specify filename | Required for file operations |
| `-z` | Compress with gzip | `tar -czf archive.tar.gz /path` |
| `-j` | Compress with bzip2 | `tar -cjf archive.tar.bz2 /path` |
| `-t` | List contents | `tar -tf archive.tar` |

**Example:**
```bash
# Create tar archive
tar -cf python.tar /home/bob/reptile/snake/python/

# Compress with gzip
gzip python.tar
# Result: python.tar.gz

# Create and compress in one command
tar -czf python.tar.gz /home/bob/reptile/snake/python/

# Extract
tar -xzf python.tar.gz
```

### GZIP/GUNZIP
```bash
# Compress file (removes original)
gzip file.txt         # Creates file.txt.gz

# Decompress
gunzip file.txt.gz    # Restores file.txt

# Keep original
gzip -k file.txt
```

### cat vs zcat
| Command | Purpose |
|---------|---------|
| `cat file.txt` | View plain text file |
| `zcat file.txt.gz` | View compressed file without extracting |
| `zgrep "pattern" file.gz` | Grep in compressed files |

---

## 8. FILE SEARCH & TEXT PROCESSING

### FIND Command
```bash
# Find by name
find /path -name "*.txt"

# Find by type (f=file, d=directory)
find /home -type f -name "config"

# Find by size
find /var -size +100M

# Find and execute command
find /tmp -type f -mtime +7 -delete

# Find by permissions
find /home -perm 777

# Find by user
find /home -user bob
```

### GREP Command
```bash
# Basic search
grep "pattern" file.txt

# Case-insensitive
grep -i "pattern" file.txt

# Recursive search in directories
grep -r "pattern" /path

# Show line numbers
grep -n "pattern" file.txt

# Invert match (lines NOT matching)
grep -v "pattern" file.txt

# Count matches
grep -c "pattern" file.txt

# Multiple files
grep "error" /var/log/*.log
```

### VIM Basics
| Mode | Key | Action |
|------|-----|--------|
| Normal | `i` | Enter insert mode |
| Normal | `ESC` | Return to normal mode |
| Normal | `:w` | Save file |
| Normal | `:q` | Quit |
| Normal | `:wq` | Save and quit |
| Normal | `:q!` | Quit without saving |
| Normal | `dd` | Delete line |
| Normal | `yy` | Copy line |
| Normal | `p` | Paste |
| Normal | `/pattern` | Search forward |
| Normal | `n` | Next search result |

---

## 9. NETWORKING & DNS

### Network Configuration Files
```bash
# DNS configuration
cat /etc/resolv.conf
# Shows DNS server IP addresses
# Example: nameserver 8.8.8.8

# Hosts file (static hostname to IP mapping)
cat /etc/hosts
# Example: 192.168.1.10 server01.local server01

# Name Service Switch configuration
cat /etc/nsswitch.conf
# Defines order for hostname resolution
# Example: hosts: files dns
```

### IP Commands
| Command | Purpose |
|---------|---------|
| `ip a` or `ip addr show` | Show all IP addresses |
| `ip r` or `ip route show` | Show routing table |
| `ip link show` | Show network interfaces |
| `sudo ip link set dev eth0 up` | Bring interface up |
| `sudo ip link set dev eth0 down` | Bring interface down |
| `sudo ip addr add 192.168.1.10/24 dev eth0` | Add IP address |
| `sudo ip route add default via 192.168.1.1` | Add default gateway |

### Testing Connectivity
```bash
# Test port connectivity
telnet hostname 80

# Test HTTP connectivity
curl http://example.com

# DNS lookup
nslookup example.com
dig example.com
```

### Stream Redirection
| Stream | Number | Purpose |
|--------|--------|---------|
| stdin | 0 | Standard input |
| stdout | 1 | Standard output |
| stderr | 2 | Standard error |

**Examples:**
```bash
# Redirect stdout to file
command > output.txt

# Redirect stderr to file
command 2> error.txt

# Redirect both stdout and stderr
command > output.txt 2>&1

# Example
python3 script.py 2> error.txt
```

---

## 10. USER & GROUP MANAGEMENT

### User Commands
| Command | Purpose | Example |
|---------|---------|---------|
| `id` | Show UID, GID, groups | `id` or `id username` |
| `sudo useradd bob` | Create user | Basic user creation |
| `sudo useradd -u 1010 -g 1010 -s /bin/sh bob` | Create user with options | UID, GID, shell |
| `sudo passwd bob` | Set/change password | Interactive password |
| `sudo userdel bob` | Delete user | Keep home directory |
| `sudo userdel -r bob` | Delete user + home | Remove all files |
| `sudo usermod -aG sudo bob` | Add user to group | Add to existing groups |

### Group Commands
```bash
# Create group
sudo groupadd developers

# Create group with specific GID
sudo groupadd -g 1010 developers

# Add user to group
sudo usermod -aG developers bob

# View user's groups
groups bob
```

### Important Files
```bash
# User account information
cat /etc/passwd
# Format: user:x:UID:GID:comment:home:shell

# Encrypted passwords
sudo cat /etc/shadow
# Format: user:encrypted_password:last_change:...

# Sudoers configuration
sudo cat /etc/sudoers | grep bob
# Or use: sudo visudo (safe editing)
```

### File Permissions
```bash
# Change owner
sudo chown user file
sudo chown user:group file
sudo chown -R user directory/

# Change permissions
chmod 755 file    # rwxr-xr-x
chmod 644 file    # rw-r--r--
chmod 777 file    # rwxrwxrwx

# Permission numbers:
# 4 = read (r)
# 2 = write (w)
# 1 = execute (x)
# 7 = rwx (4+2+1)
# 6 = rw- (4+2)
# 5 = r-x (4+1)
```

**Permission Format:** `owner group others`
- Example: `chmod 755` → Owner: rwx(7), Group: r-x(5), Others: r-x(5)

---

## 11. SSH & SECURE COPY

### SSH Key Authentication
```bash
# Generate SSH key pair
ssh-keygen -t rsa -b 4096

# Copy public key to remote server
ssh-copy-id user@server

# SSH with specific key
ssh -i ~/.ssh/id_rsa user@server

# SSH with specific port
ssh -p 2222 user@server
```

### Secure Copy (SCP)
```bash
# Copy file to remote server
scp /local/file user@server:/remote/path

# Copy file from remote server
scp user@server:/remote/file /local/path

# Copy directory recursively
scp -r /local/dir user@server:/remote/path

# Example
scp /home/bob/backup.tar.gz devapp01:/home/bob/
```

### IPTABLES Firewall
```bash
# List rules
sudo iptables -L -v
sudo iptables -L -v --line-numbers

# Allow incoming SSH from specific IP
sudo iptables -A INPUT -p tcp -s 192.168.1.100 --dport 22 -j ACCEPT

# Allow incoming HTTP
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Drop all other incoming traffic
sudo iptables -A INPUT -j DROP

# Allow outgoing to specific IP and port
sudo iptables -A OUTPUT -p tcp -d 192.168.1.11 --dport 5432 -j ACCEPT

# Insert rule at specific position
sudo iptables -I OUTPUT 1 -p tcp -d google.com --dport 443 -j ACCEPT

# Delete rule by line number
sudo iptables -D INPUT 3

# Save rules (Debian/Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4
```

---

## 12. CRON - SCHEDULED JOBS

### Crontab Commands
| Command | Purpose |
|---------|---------|
| `crontab -l` | List current user's cron jobs |
| `crontab -e` | Edit current user's cron jobs |
| `crontab -r` | Remove all cron jobs |
| `sudo crontab -u bob -l` | List another user's cron jobs |

### Cron Syntax
```
* * * * * command
│ │ │ │ │
│ │ │ │ └─── Day of week (0-7, 0 and 7 = Sunday)
│ │ │ └───── Month (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour (0-23)
└─────────── Minute (0-59)
```

### Examples
```bash
# Run at 11:23 PM every Tuesday
11 23 * * 2 /usr/local/bin/backup.sh

# Run every 30 minutes
*/30 * * * * /usr/local/bin/monitor.sh
# Or: 0,30 * * * * /usr/local/bin/monitor.sh

# Run every day at midnight
0 0 * * * /usr/local/bin/cleanup.sh

# Run every Monday at 6 AM
0 6 * * 1 /usr/local/bin/weekly-report.sh

# Run every 5 minutes
*/5 * * * * /usr/local/bin/check.sh
```

### Special Strings
```bash
@reboot     # Run once at startup
@daily      # Run once a day (midnight)
@hourly     # Run once an hour
@weekly     # Run once a week
@monthly    # Run once a month
```

---

## INTERVIEW TIPS & QUICK REFERENCE

### Common Interview Questions
1. **Difference between /bin and /sbin?**
   - /bin: User commands, /sbin: System admin commands

2. **How to make environment variable persistent?**
   - Add to ~/.profile or ~/.bashrc

3. **MBR vs GPT?**
   - MBR: 4 partitions max, 2TB limit
   - GPT: 128 partitions, >2TB support, UEFI

4. **LVM advantages?**
   - Dynamic resizing, snapshots, multiple disks as one volume

5. **How to check which process is using a port?**
   - `sudo lsof -i :80` or `sudo netstat -tulpn | grep :80`

6. **How to find files modified in last 7 days?**
   - `find /path -mtime -7`

7. **Difference between systemctl and service?**
   - systemctl: systemd, service: legacy init systems

8. **How to check system logs?**
   - `journalctl` (systemd) or `/var/log/` files

### Quick Command Reference
```bash
# System info
uname -a, hostnamectl, lsb_release -a

# Disk space
df -h, du -sh

# Memory
free -h

# CPU
lscpu, top, htop

# Processes
ps aux, pgrep, pkill

# Services
systemctl status/start/stop/restart

# Network
ip a, ip r, ss -tulpn, netstat -tulpn

# Logs
journalctl, tail -f /var/log/syslog
```

---

**END OF GUIDE**

*This guide covers essential Linux commands and concepts for DevOps engineers and system administrators preparing for interviews.*
