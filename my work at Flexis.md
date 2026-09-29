<h1 align="center">1. Host Down Alert.</h1>


# Host_Down Alert Flow

```text
Host_Down Alert
       |
       v
    Try SSH
       |
   +---+---+
   |       |
Success   Failed
   |       |
Check     Login to iDRAC
server    + Virtual Console
             |
             v
      Does server respond?
         /          \
       Yes           No
        |             |
        v             v
  Check Console    Check Power
  Screen           & iDRAC Health
        |             |
        v             v
  Check OS Health   Check Hardware
  CPU/Memory/       & iDRAC Logs
  Disk/Logs              |
        |                v
        v             Reboot /
  Check SSH &        Power Cycle
  Network                 |
        |                 |
        v                 |
  Fix the Issue           |
  - Restart Service       |
  - Resolve Network       |
  - Troubleshoot OS       |
        |                 |
        v                 |
  Is Reboot Required?     |
      /       \           |
    Yes        No         |
     |          |         |
     v          |         |
Controlled      |         |
Reboot /        |         |
Power Cycle     |         |
     |          |         |
     +----------+---------+
                |
                v
        Server Recovered?
           /          \
         Yes           No
          |             |
          v             v
     Verify Ping,    Escalate to
     SSH, Services   On-Call /
     & Monitoring    Opsgenie
          |
          v
       Close Alert
```


# Host_Down Troubleshooting — If server respond.

If the **Virtual Console is responding**,  can access the server screen and investigate the Linux OS.


## 1. Check if Linux is Running

### `uptime`

```bash
uptime
````

**Purpose:**
Checks whether the Linux OS is responding and shows how long the server has been running.

**Example:**

```text
21:30:10 up 5 days, 3:20, 1 user, load average: 0.20, 0.30, 0.25
```

**What to check:**

* If you get output → Linux is responding.
* If the command hangs → OS may be stuck or frozen.

---

## 2. Check CPU Usage

### `top`

```bash
top
```

**Purpose:**
Shows running processes and CPU/memory usage in real time.

**Look for:**

* Very high CPU usage
* Process consuming excessive CPU
* High load average
* Memory/Swap problems

**Example:**

```text
%Cpu(s): 95.0 us
```

This indicates that the CPU is heavily utilized.

**To exit:**

```text
q
```

---

## 3. Check Memory

### `free -h`

```bash
free -h
```

**Purpose:**
Checks RAM and Swap utilization.

**Example:**

```text
              total   used   free
Mem:            8Gi    7Gi   500Mi
Swap:           2Gi    1Gi     1Gi
```

**What to check:**

* Is available memory very low?
* Is swap heavily used?

If memory is exhausted, applications or the OS may become slow or unresponsive.

---

## 4. Check Disk Space

### `df -h`

```bash
df -h
```

**Purpose:**
Checks filesystem disk usage.

**Example:**

```text
Filesystem      Size  Used Avail Use%
/dev/xvda1       20G   20G     0G  100%
```

If a critical filesystem is **100% full**, it can cause applications and services to fail.

### Find Large Directories

```bash
du -sh /* 2>/dev/null | sort -h
```

**Purpose:**
Helps identify which directories are consuming disk space.

**Important:**
Do not blindly delete files in production. Follow the approved cleanup/runbook procedure.

---

## 5. Check System Errors

### `journalctl`

```bash
journalctl -p err -b
```

**Purpose:**
Shows error-level messages from the **current boot**.

Useful for identifying:

* Kernel errors
* Service failures
* Filesystem problems
* Hardware-related errors
* Other OS errors

### Check Recent Messages

```bash
journalctl -n 50
```

**Purpose:**
Shows the latest 50 journal messages.

---

## 6. Check Kernel Messages

### `dmesg`

```bash
dmesg -T | tail -50
```

**Purpose:**
Shows recent kernel messages.

Useful for identifying:

* Disk errors
* Network interface problems
* Hardware issues
* Kernel problems
* Filesystem errors

---

## 7. Check SSH Service

If Linux is running but the Host_Down alert occurred because SSH is unavailable, check the SSH service.

### Ubuntu/Debian

```bash
systemctl status ssh
```

### RHEL/CentOS

```bash
systemctl status sshd
```

**Purpose:**
Checks whether the SSH service is running.

**Possible result:**

```text
Active: active (running)
```

This means SSH is running.

If you see:

```text
inactive
```

or:

```text
failed
```

SSH may be the reason you cannot connect.

---

## 8. Restart SSH if Required

If SSH is stopped and there is no other issue preventing it from starting:

### Ubuntu/Debian

```bash
sudo systemctl restart ssh
```

### RHEL/CentOS

```bash
sudo systemctl restart sshd
```

Then verify:

```bash
systemctl status ssh
```

or:

```bash
systemctl status sshd
```

**Purpose:**
Restarts the SSH service so remote SSH access can be restored.

---

## 9. Check Whether SSH Port Is Listening

### `ss`

```bash
ss -lntp | grep :22
```

**Purpose:**
Checks whether something is listening on TCP port `22`.

**Example:**

```text
LISTEN 0 128 0.0.0.0:22
```

This indicates that something is listening on port 22.

If there is no output, SSH may not be listening on port 22.

---

## 10. Check IP Address

### `ip addr`

```bash
ip addr
```

or:

```bash
ip a
```

**Purpose:**
Shows the server's network interfaces and IP addresses.

**Check:**

* Network interface is present
* Interface is UP
* Expected IP address is assigned

**Example:**

```text
ens5:
    inet 172.31.36.153/20
```

---

## 11. Check Routing

### `ip route`

```bash
ip route
```

**Purpose:**
Shows the server's routing table.

Look for a default route such as:

```text
default via 172.31.32.1 dev ens5
```

This tells the server where to send traffic destined for other networks.

---

## 12. Test Network Connectivity

### Ping the Gateway

```bash
ping -c 4 <gateway-ip>
```

**Example:**

```bash
ping -c 4 172.31.32.1
```

**Purpose:**
Checks whether the server can communicate with its gateway.

If gateway ping fails, investigate:

* Network interface
* Routing
* Firewall/security rules
* Network connectivity

---

## 13. Test Remote Server Connectivity

From another server or management machine:

```bash
ping -c 4 <server-ip>
```

**Example:**

```bash
ping -c 4 172.31.36.153
```

**Purpose:**
Checks whether the Host_Down server is reachable over the network.

---

## 14. Test SSH

From another machine:

```bash
ssh <user>@<server-ip>
```

**Example:**

```bash
ssh ubuntu@172.31.36.153
```

**Purpose:**
Confirms whether remote SSH access has been restored.

---

## 15. Check Required Services

If SSH is working but the application is still down, check the required service.

### Check Service

```bash
systemctl status <service-name>
```

**Example:**

```bash
systemctl status nginx
```

**Purpose:**
Checks whether the service is running.

### Restart Service if Required

```bash
sudo systemctl restart <service-name>
```

**Example:**

```bash
sudo systemctl restart nginx
```

Then verify:

```bash
systemctl status nginx
```

---

## 16. Reboot the Server if Required

If the OS is hung or the issue cannot be resolved through normal troubleshooting, L2 may perform an approved reboot.

### Normal Reboot

```bash
sudo reboot
```

or:

```bash
sudo systemctl reboot
```

**Purpose:**
Gracefully restarts the operating system.

After reboot, wait for the server to come back and then verify:

```bash
uptime
```

**Important:**
Before rebooting a production server, follow the organization's recovery/change procedure and check the impact on applications.

---

## 17. If Normal Reboot Does Not Work

If the OS is completely frozen and a normal reboot cannot be performed, L2 may use **iDRAC** to perform an approved power cycle.

This is commonly called:

```text
Cold Reboot / Power Cycle
```

It means:

```text
Power OFF
    ↓
Power ON
    ↓
Server boots again
```

**Important:**
A power cycle is more disruptive than a normal reboot and should be performed according to the production recovery procedure.

---

## 18. Verify Server Recovery

After the issue is fixed, L2 should verify that the server is actually healthy.

### Check Ping

```bash
ping -c 4 <server-ip>
```

**Purpose:**
Confirms network reachability.

### Check SSH

```bash
ssh <user>@<server-ip>
```

**Purpose:**
Confirms remote login is working.

### Check Required Services

```bash
systemctl status <service-name>
```

**Purpose:**
Confirms that required services are running.

### Check Server Uptime

```bash
uptime
```

**Purpose:**
Confirms that the OS is running after recovery/reboot.

### Check Monitoring

Verify that the monitoring system shows:

```text
Host_Down Alert → Cleared
Host Status → UP
Required Services → Running
Monitoring → Healthy
```

---

## 19. If the Server Is Still Down

If L2 cannot recover the server:

```text
L2 Troubleshooting
       |
       v
Server recovered?
    /        \
  Yes         No
   |           |
Verify       Escalate
   |           |
Close       On-Call/L3
Alert       via Opsgenie
```

Depending on the issue, L2 may escalate to:

* Linux/OS L3 team
* Network team
* Storage team
* Hardware/OEM team
* Application team

---

# Most Important Commands to Remember

| Command                       | Purpose               | What You Are Checking       |
| ----------------------------- | --------------------- | --------------------------- |
| `uptime`                      | Check system status   | OS responding, load, uptime |
| `top`                         | Check CPU/processes   | High CPU/process issues     |
| `free -h`                     | Check memory          | RAM/Swap usage              |
| `df -h`                       | Check disk            | Filesystem full             |
| `journalctl -p err -b`        | Check OS errors       | Current boot errors         |
| `dmesg -T \| tail -50`        | Check kernel messages | Hardware/kernel issues      |
| `systemctl status ssh`        | Check SSH             | SSH running or failed       |
| `systemctl restart ssh`       | Restart SSH           | Restore SSH service         |
| `ss -lntp \| grep :22`        | Check port 22         | SSH listening               |
| `ip addr`                     | Check IP/interface    | Network interface/IP        |
| `ip route`                    | Check routing         | Default route/routes        |
| `ping -c 4 <IP>`              | Test connectivity     | Network reachability        |
| `systemctl status <service>`  | Check service         | Application/service status  |
| `systemctl restart <service>` | Restart service       | Restore failed service      |
| `sudo reboot`                 | Reboot OS             | Recover OS if required      |

---
---






