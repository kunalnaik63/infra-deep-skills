# 🖥 systemd vs init, and journalctl

## 1️⃣ Overview – init vs systemd
### init (SysVinit)
- The traditional service manager for Linux systems.
- Executes startup scripts located in `/etc/init.d/` sequentially.
- Simpler, but slower boot times due to lack of parallelization.

### systemd
- Modern replacement for init, adopted by most major Linux distributions.
- Starts services **in parallel**, reducing boot time.
- Uses the concept of **units**:
  - `.service` – individual services
  - `.target` – groups of services, like runlevels
  - `.mount` – filesystem mounts
- Handles dependency management between services.
- Integrates with **journald** for centralized logging.

---

## 2️⃣ Common systemctl Commands
`systemctl` is the control interface for systemd.

```bash
# View status of a service
systemctl status <service>

# Start/Stop/Restart a service
systemctl start <service>
systemctl stop <service>
systemctl restart <service>

# Enable/Disable at boot
systemctl enable <service>
systemctl disable <service>

# List all running services
systemctl list-units --type=service --state=running
```


# Journalctl 
### Logs for a specific service
journalctl -u <service>

### Logs since a certain time
journalctl --since "2024-08-11 10:00:00"
journalctl --since "2 hours ago"

### Only errors
journalctl -p err

### Logs from the previous boot
journalctl -b -1

### Live (follow) logs
journalctl -f

### Check how much space logs are using
journalctl --disk-usage

### Remove logs older than 7 days
sudo journalctl --vacuum-time=7d

### Limit total journal size to 200M
sudo journalctl --vacuum-size=200M

### Show Errors from Last 1 Hour
journalctl -p err --since "1 hour ago"

### Show Last 20 Logs of sshd Service
journalctl -u sshd.service -n 20


# Systemd and journald (ASCII Diagram)

            +--------------------+
            |  Power ON / BIOS   |
            +---------+----------+
                      |
                      v
            +--------------------+
            |  Boot Loader (GRUB)|
            +---------+----------+
                      |
                      v
            +--------------------+
            |     Kernel Init    |
            +---------+----------+
                      |
                      v
            +--------------------+
            |   systemd process  |
            |  (PID 1)           |
            +---------+----------+
              /       |        \
             v        v         v
       +--------+ +--------+ +--------+
       | Targets| | Mounts | | Services|
       +--------+ +--------+ +--------+
             \        |        /
              \       v       /
            +--------------------+
            | journald logging   |
            +--------------------+
