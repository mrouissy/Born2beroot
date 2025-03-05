# Born2beroot Project Guide

## Overview
This project introduces you to the world of system administration by guiding you through the process of setting up a Virtual Machine with specific configuration requirements. The goal is to create a secure, customized Linux environment while learning crucial system management skills.

## Command Reference for Each Configuration Stage

### System Preparation

#### 1. Sudo Configuration
```bash
# Switch to root
su -

# Install sudo package
apt install sudo

# Add user to sudo group
adduser <username> sudo
# Alternative method
usermod -aG sudo <username>

# Verify sudo installation
dpkg -l | grep sudo

# Create sudo configuration file
sudo vi /etc/sudoers.d/<filename>

# Example sudo configuration
echo "Defaults        passwd_tries=3
Defaults        badpass_message=\"Incorrect sudo password\"
Defaults        logfile=\"/var/log/sudo/sudo.log\"
Defaults        log_input,log_output
Defaults        iolog_dir=\"/var/log/sudo\"
Defaults        requiretty
Defaults        secure_path=\"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin\"" | sudo tee /etc/sudoers.d/custom_sudo_config
```

#### 2. SSH Setup
```bash
# Install OpenSSH server
sudo apt install openssh-server

# Verify SSH installation
dpkg -l | grep ssh

# Edit SSH configuration
sudo vi /etc/ssh/sshd_config

# Modify configuration
# Change Port 22 to Port 4242
# Set PermitRootLogin to no

# Restart SSH service
sudo systemctl restart ssh

# Check SSH status
sudo service ssh status
```

#### 3. Firewall Configuration
```bash
# Install UFW
sudo apt install ufw

# Enable firewall
sudo ufw enable

# Allow SSH port
sudo ufw allow 4242

# Check firewall status
sudo ufw status
```

### User and Password Management

#### 1. Password Policy Configuration
```bash
# Edit password age settings
sudo vi /etc/login.defs
# Set PASS_MAX_DAYS to 30
# Set PASS_MIN_DAYS to 2

# Install password quality tools
sudo apt install libpam-pwquality

# Configure password strength
sudo vi /etc/pam.d/common-password
# Add policy options:
# password requisite pam_pwquality.so \
#   retry=3 \
#   minlen=10 \
#   ucredit=-1 \
#   dcredit=-1 \
#   lcredit=-1 \
#   maxrepeat=3 \
#   reject_username \
#   difok=7 \
#   enforce_for_root
```

#### 2. User and Group Management
```bash
# Create a new user
sudo adduser <username>

# Verify user creation
getent passwd <username>

# Check password expiry
sudo chage -l <username>

# Create a new group
sudo addgroup user42

# Add user to group
sudo adduser <username> user42
# Alternative method
sudo usermod -aG user42 <username>

# Verify group membership
getent group user42
```

### Monitoring and Scheduling

#### Cron Job Setup
```bash
# Edit root's crontab
sudo crontab -u root -e

# Add a cron job (e.g., run script every 10 minutes)
*/10 * * * * /path/to/monitoring.sh

# List scheduled cron jobs
sudo crontab -u root -l
```

#### Monitoring Script (monitoring.sh)
```bash
#!/bin/bash
# Comprehensive system monitoring script

# Collect system information
arch=$(uname -a)
pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l)
vcpu=$(grep "processor" /proc/cpuinfo | wc -l)
ram_total=$(free -m | awk 'NR==2{printf "%s/%sMB", $3,$2}')
ram_percent=$(free | awk 'NR==2{printf "%.2f%%", $3*100/$2}')
disk_usage=$(df -h | awk '$NF=="/" {printf "%d/%dGB (%s)", $3, $2, $5}')
cpu_load=$(top -bn1 | grep load | awk '{printf "%.2f%%", $(NF-2)}')
last_boot=$(who -b | awk '{print $3 " " $4}')
lvm_status=$(lsblk | grep lvm | wc -l)
tcp_connections=$(ss -t | grep ESTAB | wc -l)
users_logged=$(users | wc -w)
ipv4=$(hostname -I | awk '{print $1}')
mac_address=$(ip link | awk '$1 ~ /link\/ether/ {print $2}')
sudo_cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

# Broadcast message to all users
wall "
#Architecture: $arch
#CPU physical: $pcpu
#CPU virtual: $vcpu
#Memory Usage: $ram_total ($ram_percent)
#Disk Usage: $disk_usage
#CPU load: $cpu_load
#Last boot: $last_boot
#LVM active: $lvm_status ? yes : no
#Active connections: $tcp_connections
#Users logged: $users_logged
#Network: IP $ipv4 ($mac_address)
#Sudo: $sudo_cmds commands"
```

## Key Concepts and Technologies

(Retain the existing sections from the original document about Virtual Machines, Key Technologies, etc.)

## Bonus Recommendations
* Install additional services like WordPress
* Set up File Transfer Protocol (FTP)
* Implement a web server stack (Lighttpd, MariaDB, PHP)

## Best Practices
* Always use `sudo` for system-level operations
* Keep configurations minimal and secure
* Regularly update and patch the system
* Document all configuration changes

## Troubleshooting Commands
```bash
# Check system logs
journalctl

# Verify sudo configuration
sudo -l

# Check firewall status
ufw status

# Restart services
sudo systemctl restart <service>

# View system resource usage
top
htop

# Check disk usage
df -h

# Monitor system performance
vmstat
iostat
```

## Conclusion
The Born2beroot project provides an excellent opportunity to:
* Understand virtualization
* Learn system administration
* Implement security best practices
* Gain hands-on experience with Linux systems

**Note**: This guide provides a comprehensive overview. Always consult the project's specific requirements and adapt the configuration accordingly.
