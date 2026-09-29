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


<h1 align="center">2. Bad Requests / HTTPS Connection Failure</h1>


## 1. What is this alert?

Zabbix reports HTTP 5xx errors on the server, especially **502 Bad Gateway**.

Our task is to check Nginx logs, identify the issue, perform the approved fix, and verify whether the alert clears.

## 2. Troubleshooting Steps

### Step 1: Login to the Server

Connect to the affected server using SSH.

### Step 2: Check Nginx Logs

```
tail -n 100000 /storage/ManagementCloud/var/log/nginx/nginx-access.log | grep 'HTTP/1.1" 50'
```

**Purpose:** Find HTTP 5xx errors such as 500, 502, and 503.

* If no 5xx errors are found → escalate to MSP Backup DevOps via OpsGenie.

* If errors are found → check for `POST /defragment` requests returning 502.

### Step 3: Stop ReportingService

If the logs match the issue described in the runbook:

```
pgrep ReportingService | xargs sudo kill -9
```

**Purpose:** Forcefully stop the suspected problematic process.

### Step 4: Restart Nginx

```
sudo kill $(cat /storage/ManagementCloud/var/run/nginx.pid)
```

**Purpose:** Stop Nginx so it can restart and reset its SLA counters.

Wait 30 seconds:

```
sleep 30
```

Check whether Nginx is running:

```
ps auxwww | grep 'nginx: master'
```

### Step 5: Verify the Alert

Wait up to 5 minutes.

* **Alert closed:** Confirm recovery and update Jira.

* **Alert still active:** Continue troubleshooting and escalate.

### Step 6: If the Issue Persists

If Nginx cannot be stopped or shows `STOP` / `vodead` status:

1. Confirm iDRAC Virtual Console access.

2. Reboot the server if authorized.

3. If the issue continues, escalate to MSP Backup DevOps.

### Step 7: Check Repeated Alerts

Check OpsGenie history for the last 7 days.

* More than 3 related alerts → escalate to MSP Backup DevOps.

* Create or update the Jira ticket.

* Assign it to the on-call engineer.

* Set the required due date and notify the engineer.

## 3. Commands to Remember

| Command                  | Purpose                     |                      |
| ------------------------ | --------------------------- | -------------------- |
| `tail ...                | grep ...`                   | Find HTTP 5xx errors |
| `pgrep ReportingService` | Find the process            |                      |
| `kill -9`                | Forcefully stop the process |                      |
| `cat nginx.pid`          | Read Nginx process ID       |                      |
| `sleep 30`               | Wait 30 seconds             |                      |
| `ps auxwww`              | Check running processes     |                      |
| `reboot`                 | Restart the server          |                      |

> **Note:** `kill -9` and reboot are impactful actions. Follow the production runbook and authorization process.

## 4. Interview Answer

“When I receive a Bad Requests alert, I log in to the affected server and check the Nginx access logs for HTTP 5xx errors, especially 502 responses. If I find failed `/defragment` requests, I follow the runbook to stop the suspected ReportingService process and restart Nginx. I then verify that Nginx is running and check whether the alert clears within five minutes. If the issue persists, I check iDRAC access, follow the approved recovery procedure, escalate through OpsGenie, and update the Jira ticket.”


---
---


<h1 align="center">3. File Upload Alert </h1>


## 1. What is this alert?

The File Upload alert indicates that clients may be experiencing problems uploading backup data to the server.

Our task is to check Nginx logs, verify the Nginx configuration, restart Nginx if required, and confirm whether the alert clears.

## 2. Troubleshooting Steps

### Step 1: Login to the Server

Connect to the affected server using SSH.

If access is denied, escalate to MSP Backup DevOps via OpsGenie and create/update the Jira ticket. Ask the team to check Flexis team permissions on the host.

### Step 2: Check Nginx Logs

```
tail -n 100000 /storage/ManagementCloud/var/log/nginx/nginx-access.log | grep 'HTTP/1.1" 50'
```

**Purpose:** Find HTTP errors related to file uploads.

Look for upload requests such as `PUT` with HTTP error codes like:

* `400` — Bad Request

* `408` — Request Timeout

Check the server time to correlate errors with the alert:

```
date
```

### Step 3: Check Nginx Worker Configuration

**Important:** The old instruction to increase `worker_processes` to 12 is obsolete. Do not manually change this value.

The worker count is now calculated based on the number of CPUs.

### Step 4: Test Nginx Configuration

```
sudo /storage/ManagementCloud/bin/nginx -c /storage/ManagementCloud/etc/nginx/nginx.conf -p /storage/ManagementCloud/etc/nginx/ -t
```

**Purpose:** Check whether the Nginx configuration has any syntax errors.

Proceed only if the configuration test is successful.

### Step 5: Restart Nginx

Check the running Nginx processes:

```
pgrep nginx
```

The runbook's restart procedure is:

```
sudo pkill nginx
```

Then check again:

```
pgrep nginx
```

**Purpose:** Stop Nginx processes so they can be restarted.

Verify that Nginx has started successfully. Follow your team's approved procedure if it does not restart.

### Step 6: Verify the Alert

Wait up to **30 minutes**.

* **Alert closed:** Confirm recovery and update Jira.

* **Alert still active:** Escalate to MSP Backup DevOps via OpsGenie.

### Step 7: Check Repeated Alerts

Search OpsGenie for File Upload alerts from the last 7 days.

```
teams: "MSP_Backup" AND message: *File_upload* and entity: <host>
```

Replace `<host>` with the affected server's entity.

If there are more than 3 related alerts, escalate to MSP Backup DevOps.

Create/update the Jira ticket, set the required 1-day due date, assign it to the on-call engineer, and notify them directly.

## 3. Commands to Remember

| Command            | Purpose                               |                                |
| ------------------ | ------------------------------------- | ------------------------------ |
| `tail ...          | grep ...`                             | Find HTTP errors in Nginx logs |
| `date`             | Check server date and time            |                                |
| `nginx ... -t`     | Test Nginx configuration              |                                |
| `pgrep nginx`      | Check Nginx processes                 |                                |
| `sudo pkill nginx` | Stop Nginx processes                  |                                |
| `pgrep nginx`      | Verify whether Nginx processes return |                                |

> **Production note:** `pkill nginx` stops matching Nginx processes; it does not itself guarantee that Nginx restarts. Follow the approved service recovery procedure and verify that the service is running before closing the incident.

## 4. Interview Answer

“When I receive a File Upload alert, I log in to the affected server and check the Nginx access logs for upload failures, such as HTTP 400 or 408 errors. I verify the Nginx configuration using the configuration test command. If the test passes, I follow the approved procedure to restart Nginx and verify that it is running. I then monitor the alert for up to 30 minutes. If it does not clear or the issue occurs repeatedly, I escalate it through OpsGenie and update the Jira ticket.”

---
---

<h1 align="center">4. Open Port(s) Detected </h1>


## 1. What is this alert?

This alert means the monitoring system has detected one or more **TCP ports open** on a server or iDRAC IP.

Example:

```text
Open ports: 22/tcp 5900/tcp
```

* `22` → SSH
* `5900` → VNC
* The alert means these ports are reachable from the scanner's network.

The main task is to **identify the server/IP, determine who owns it, and make sure the open port is expected**.

---

## 2. Troubleshooting Steps

### Step 1: Check the IP from the Alert

Look at the IP address mentioned in the OpsGenie alert.

Example:

```text
IP: 115.70.197.109
Open ports: 22/tcp 5900/tcp
```

---

### Step 2: Identify the Server

If the alert is:

```text
Open port(s) detected NotpersistinRacktabels
```

Search the IP address in **RackTables**.

**Purpose:** Find which server or equipment is using that IP.

---

### Step 3: Identify the Responsible Team

Once the server is identified, check its hostname/type and escalate to the appropriate team.

| Server Type       | Responsible Team                   |
| ----------------- | ---------------------------------- |
| Mail Assure       | Mail Assure team                   |
| MSP Connect       | MSP Connect team                   |
| Backup server     | Backup DevOps team                 |
| Network equipment | Network team / designated engineer |

**Important:** Do not assume the team from the IP alone. First identify the server in RackTables.

---

### Step 4: Create / Update Jira

Create or update the Jira ticket with:

* Affected IP
* Open ports
* Server hostname
* OpsGenie alert details
* Responsible team
* Actions taken

---

## 3. If the Alert is for iDRAC

If the alert is:

```text
Open port(s) detected Backup
```

First check whether the reported IP belongs to the server's **iDRAC**.

If it is an iDRAC IP:

1. Login to the iDRAC console.
2. Go to **iDRAC Settings**.
3. Select **Connectivity**.
4. Open **Advanced Network Settings**.
5. Check the configured IP ranges.
6. Apply the approved firewall/IP-range configuration.

### Important Concept

The iDRAC firewall controls **which source IP ranges are allowed to access iDRAC**.

Example:

```text
IP Range Address: 208.70.88.8
Subnet Mask:      255.255.255.255
```

`255.255.255.255` means **only that specific IP address** is allowed.

---

## 4. Simple Troubleshooting Flow

```text
Open Port Alert
       |
       ↓
Check IP and Open Port
       |
       ↓
Identify Server in RackTables
       |
       ↓
Identify Responsible Team
       |
       ↓
Is it iDRAC?
    /       \
  Yes       No
   |         |
   ↓         ↓
Check      Escalate to
iDRAC      correct team
Firewall
   |
   ↓
Apply approved
IP restrictions
   |
   ↓
Verify Alert
```

---

## 5. Commands to Remember

For this alert, there are usually **no primary Linux commands** in the provided runbook.

The important tools are:

| Tool           | Purpose                               |
| -------------- | ------------------------------------- |
| **OpsGenie**   | Receive and manage the alert          |
| **RackTables** | Identify the server using the IP      |
| **iDRAC**      | Manage server hardware/network access |
| **Jira**       | Track the incident and escalation     |

---

## 6. Interview Answer

“When I receive an Open Port alert, I first check the IP address and the ports reported by the monitoring system. I identify the server using RackTables and determine which team is responsible for that server. If the IP belongs to iDRAC, I check the iDRAC firewall and its allowed IP ranges and apply the approved configuration if required. I then update Jira, escalate to the responsible team, and verify that the alert is resolved.”

## 7. Easy Way to Remember

**Open Port Alert =**

**IP → Server → Team → iDRAC/Server → Firewall → Escalate → Verify**

---
---
