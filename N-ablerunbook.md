# Step-by-step Guide - Host Down

   ## Router/Switch Down Alert

   ## SN - Host_Down

# Step-by-step Guide - "Bad Requests" or "HTTPS Connection Failure"

# Step-by-step Guide - Open Port(s) Detected

# Step-by-step Guide - File Upload

# Ncod

# Add to 
---
---



# Step-by-Step Guide — Host Down

## 1. Router/Switch Down Alert

### Steps to Follow

1. **Check connectivity:**
   - Ping the router/switch from a **local machine**.
   - Ping the router/switch from **US storage nodes**.
   - If ping is successful from at least one site, consider it a **false positive**.

2. **Notify the Cove on-call engineer.**

3. **Cove on-call engineer actions:**
   - Attempt to log in to the router via **SSH from Zabbix**.
   - Attempt to log in via **SSH from a US storage node**.
   - If both attempts fail, **escalate the issue to the DC team**.

---

# 2. SN — Host_Down

### Example Alert

```text
[Zabbix]: us.dc11.0103.03 - Host_Down
````

### Steps to Follow

1. Attempt to log in via **Cyolo** and check **SSH access**.

2. If SSH is unavailable, perform a **Graceful Shutdown via iDRAC**.

3. If SSH is still unavailable after the Graceful Shutdown, perform a **soft boot**.

4. If the issue persists, **notify the Cove on-call engineer**.

### Note

If the issue is resolved, the alert in **Opsgenie** should **heal automatically**.

---

# 3. Hardware Failure / Long-Running Issue

If a cause such as **hardware failure** is confirmed and the resolution is going to take time:

1. Create a **separate Microsoft Teams group/chat**.
2. Post ongoing updates **every 1 hour**, even if there is no new information.
3. Include:

   * Engineer on duty
   * `@Yury Bialkevich`
   * `@jitender kumar`
   * All NOC team members in the current shift

---

# 4. Detailed Host Down Procedure

### Example Zabbix Alert

```text
Subj. [Zabbix] de.du.06.dss.08 - Disaster PROBLEM: Host_Down

Host: de.du.06.dss.08
Visible name: de.du.06.dss.08
Trigger: Host_Down
Trigger status: PROBLEM
Trigger severity: Disaster
DNS: de-du-06-dss-08.cloudbackup.management

Item values:

1. Ping : 0

Original event ID: 143449499
```

---

## 5. Initial Troubleshooting

### Step 1 — Try SSH Login

Try to log in to the server via **SSH**.

```text
SSH → Server
```

If SSH login is successful:

* Check the server status.
* Investigate the reason for the Host_Down alert.
* Escalate to the on-call engineer if required.

If SSH is unsuccessful, continue with the next step.

---

## 6. Access iDRAC and Virtual Console

### Step 2 — Login to iDRAC

1. Log in to **iDRAC**.
2. Open **Virtual Console**.
3. Press **Enter** to check whether the server responds to keyboard actions.
4. Take a **screenshot of the Virtual Console**.

---

## 7. If the Server Responds in Virtual Console

If the server responds to commands/actions through the Virtual Console:

1. Escalate the **Opsgenie issue to the on-call engineer**.
2. Skip unnecessary reboot steps.
3. Attach the Virtual Console screenshot when reporting the issue.

---

## 8. Important — SSH Unavailable

> **Do not immediately select the Warm Boot option if SSH is unavailable.**

Follow these steps first:

1. Open the **Virtual Console**.
2. Press:

```text
CTRL + ALT + DEL
```

3. Wait a few minutes and check whether the display appears/reboots in the Virtual Console.
4. If the server still does not reboot, choose:

   * **Warm Boot**, or
   * **Cold Boot** if Warm Boot does not work.

---

# 9. If the Server Does Not Respond

If the server does not respond to commands/actions through the Virtual Console, reboot the server.

### Step 3.1 — Open Virtual Console

Open the server's **Virtual Console** from iDRAC.

### Step 3.2 — Warm Boot

Go to:

```text
Menu → Power → Reset System (Warm Boot)
```

### Step 3.3 — Cold Reboot

If the system cannot be booted using **Warm Boot**, try:

```text
Cold Reboot
```

### Step 3.4 — Initializing Devices

If the server remains stuck at:

```text
Initializing Devices
```

do not wait for more than **30 minutes**.

After 30 minutes:

* Escalate the issue via **Opsgenie**.
* Notify the appropriate on-call/next-level team.

---

# 10. Check Server Restart History

Check how many restarts/Host_Down alerts are associated with the server during the past month.

Use the **Backup Host Down Alerts** Opsgenie search.

### Steps

1. Open the Opsgenie Alerts page.
2. Replace the server name with the appropriate hostname.
3. Select a **1-month timeframe**.
4. Search for Host_Down alerts associated with the server.

### Opsgenie

[Opsgenie Alerts Page](https://swmspdevops.app.opsgenie.com/alert/list)

### Example Opsgenie Query

```text
teams: "MSP_Backup" AND message: *host_down* AND entity: nl.ams.16.19
```

---

# 11. Inform Backup DevOps Team

Inform the **Backup DevOps team** in the Microsoft Teams:

**Team:** `FlexisIT-Backup`
**Channel:** `General`

[FlexisIT-Backup — General Channel](https://teams.microsoft.com/l/channel/19%3aKAM9ANntlxR8QfL7NFq7-PV6vHGVP5lXxLkDkMMLcgU1%40thread.tacv2/General?groupId=8f862022-9575-49e0-971a-8179fd8be808&tenantId=6324f4fb-86ee-4493-ba16-c819a916b487)

### Message Format

```text
<server_name> is down. We restarted it and the issue was resolved.

Based on Opsgenie history, there were found <X> "host_down"
alerts associated with this server during the last month.
```

---

# 12. Important — More Than 3 Host_Down Alerts

If there are **more than 3 Host_Down alerts** related to the problem host during the last month:

1. Escalate the issue to the **MSP Backup DevOps team via Opsgenie**.
2. Create a **Jira ticket**.
3. Set the **Due Date** to a **1-day interval** to address the issue.
4. Assign the ticket to the appropriate **on-call engineer**.
5. Inform the on-call engineer directly to draw attention to the issue.

### Jira

[Create/Update Jira Ticket](https://n-able.atlassian.net/wiki/spaces/DO/pages/4508951397)

### On-Call Ticket

[BR-11117](https://n-able.atlassian.net/browse/BR-11117)

---

# 13. Attach Screenshot

Attach the **Virtual Console screenshot** taken during troubleshooting to the Microsoft Teams message.

---

# 14. Verify Server Status

After the reboot:

1. Confirm that the server is **up and running**.
2. Check whether **SSH access** is available.
3. Confirm that the **Host_Down alert has recovered/healed**.
4. If the issue is not resolved, **escalate the Opsgenie alert to the Next Level**.

---

# 15. Server Down for More Than 30 Minutes

If the server has been down for **more than 30 minutes** and further action/dependency is required from:

* Dell engineer
* Equinix/DC engineer
* Other infrastructure teams

create a **separate Microsoft Teams group/chat**.

### Group Name

```text
<hostname>_down
```

### Add

* All 24x7 team members currently in shift
* `@Igor Lushchik`
* `@Yury Bialkevich`
* `@jitender kumar`
* Engineer on duty

### Update Frequency

Post all relevant updates **every 1 hour**, even when there is no new information.

---

# Quick Decision Flow

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
Escalate to       Reboot
On-Call           server
                     |
              +------+------+
              |             |
          Warm Boot     Cold Reboot
              |             |
              +------+------+
                     |
                     v
             Server recovered?
                /        \
              Yes         No
               |           |
         Verify & close   Escalate
                         Opsgenie
```

# Important Thresholds

| Condition                                    | Action                               |
| -------------------------------------------- | ------------------------------------ |
| Ping successful from at least one site       | Consider false positive              |
| SSH unavailable                              | Check iDRAC + Virtual Console        |
| Server responds via Virtual Console          | Escalate to on-call                  |
| Warm Boot unsuccessful                       | Try Cold Reboot                      |
| Stuck at `Initializing Devices` > 30 min     | Escalate via Opsgenie                |
| More than 3 Host_Down alerts in 1 month      | Escalate to MSP Backup DevOps        |
| Server down > 30 min with DC/Dell dependency | Create `<hostname>_down` Teams group |
| Long-running issue                           | Post updates every 1 hour            |
| Issue resolved                               | Verify server + SSH + alert recovery |

```
```
---

# Step-by-step Guide - "Bad Requests" or "HTTPS Connection Failure"


Check Nginx service status

Alert example:

Click here to expand...

Subj. [Zabbix] uk.slo.101.03-Disaster PROBLEM: Bad requests

Host: uk.slo.101.03 
Visible name : uk.slo.101.03 
Trigger: Bad requests 
Trigger status: PROBLEM 
Trigger severity: Disaster 
DNS: uk-slo-101-03.mob.system-monitor.com 

Item values: 

1. sla.result : CRITICAL: Total=160\nhttps_5xx 

Original event ID: 204492160

Actions:

Login to the server via SSH and check Nginx access log by running the following command:

If the access to the server is denied, please escalate the issue to MSP Backup DevOps team using OpsGenie. Please also register the issue in JIRA, i.e. clone the BR-2256: [Zabbix] us.atl.03.08 Disaster Status: PROBLEM Host_Down Done JIRA issue and update accordingly. 

Do not forget to add the newly created task to the active "BR DevOps 2018 Week X" sprint (X is the number of the week of the year).

IMPORTANT! Inform MSP Backup DevOps team about the need to check Flexis team permissions on the problem host. 

# tail -n 100000 /storage/ManagementCloud/var/log/nginx/nginx-access.log | grep --color 'HTTP/1.1" 50'
...
58.162.302.145 - data [24/Apr/2018:22:05:32 +0200] "REQUEST_EXAMPLE HTTP/1.1" 502 352 "-" "CLIENT" ...
# date
Wed Apr 24 22:09:20 CEST 2018

Escalate the issue to MSP Backup DevOps team if no 50x HTTP errors were found in the Nginx logs. Otherwise, proceed with the issue resolution process as described below (Errors caused by ReportingService. HTTP error code - 502).

2.1.1. Check the Nginx access log on the problem host to make sure that there are "POST /defragment" requests failed:

# tail -n 100000 /storage/ManagementCloud/var/log/nginx/nginx-access.log | grep --color 'HTTP/1.1" 50'
...
58.162.302.145 - data [24/Apr/2018:22:05:32 +0200] "POST /defragment HTTP/1.1" 502 352 "-" "BackupClient/17.12.1.17351" 1.151 1.151 "1524600332.803" {\x22cabinetPath\x22:\x223270.0398
103.212.96.72 - data [24/Apr/2018:22:05:34 +0200] "POST /defragment HTTP/1.1" 502 377 "-" "BackupClient/18.2.0.18036" 1.370 1.370 "1524600334.173" {\x22cabinetPath\x22:\x22us-hsc_bp_2
# date
Wed Apr 24 22:10:20 CEST 2018

2.1.2. Kill "ReportingService" process to remediate the issue:

# pgrep ReportingService | xargs sudo kill -9

2.1.3. Reset nginx sla counters

# sudo kill `cat /storage/ManagementCloud/var/run/nginx.pid`
# sleep 30
check that nginx is UP
# ps auxwww | grep 'nginx: master'

2.1.4. Ensure that the alert is closed automatically during next 5 mins.

If the issue is still exists, or "Nginx" process can not be killed, or it has "STOP"/"vodead" status, please reboot the server by running `reboot` command. Before the reboot, make sure that you can run iDRAC Virtual Console. 

:warning: In case of any issues with this, proceed with the steps mentioned at Monitoring Production Environment#Step-by-stepguide-HostDown

Below is an example of "top" command output on a problem server:

35621 iasouser      1  31    0 81940K 61536K vodead  4 338:21   0.00% nginx 

If this does not happen, inform MSP Backup DevOps team about the issue and escalate alert from OpsGenie as well.

2.1.5. Check the history of "Bad requests" alerts escalated by OpsGenie for last 7 days using the following alert search query:

teams: "MSP_Backup" AND message: *Bad requests* and entity: au.sy.02.09

IMPORTANT

If there are more than 3 alerts related to "Bad requests" status of the problem host, escalate the issue to MSP Backup DevOps team via Opsgenie.

Ensure to register a Jira ticket via this link, update "Due date" field to set 1-day interval to address the issue, assign the ticket to an on-call engineer (who is assigned to this ticket) and inform him/her directly to draw attention to the issue.

The link to OpsGenie Alerts page: https://app.opsgenie.com/alert/V2#/alert-genie

---
# Step-by-step Guide - Open Port(s) Detected


# Issues related to firewall configuration

## Alert example:

Click here to expand...

Link to the OpsGenie: [https://swmspdevops.app.opsgenie.com/alert/detail/612a9c75-7696-4a26-a194-36c153bdbdf1-1603370248490/details](https://swmspdevops.app.opsgenie.com/alert/detail/612a9c75-7696-4a26-a194-36c153bdbdf1-1603370248490/details)

[http://openvas1.globaldevops.swmsp.net:9093/#/alerts?receiver=Backup-team-alerts](http://openvas1.globaldevops.swmsp.net:9093/#/alerts?receiver=Backup-team-alerts)

**Integration**

Open ports scanner (Prometheus)

**Responders**

[MSP_Backup](https://swmspdevops.app.opsgenie.com/teams/dashboard/06e9570d-a8ca-4b7a-b591-931afded7178)

**Alias**

f798a1898f35dea1aa3a0f7c47f33a9c704ee39c059e13c5f6bd4db68ec37097

**Last Updated At**

Oct 22, 2020 6:37 PM (GMT+03:00)

**Last Duplicated At**

Oct 22, 2020 5:37 PM (GMT+03:00)

**Description**

The ip address 115.70.197.109 has 2 TCP port(s) open Open ports: 22/tcp 5900/tcp

**Alerts Firing:**

**Labels:**

- alertname = Open port(s) detected
- instance = 115.70.197.109
- service = Backup

**Annotations:**

- info = The ip address 115.70.197.109 has 2 TCP port(s) open
- summary = Open ports: 22/tcp 5900/tcp

**Source:**

**Priority**

P3 - Moderate

## Actions:

*Update on July 24, 2018: New logic was added and Opsgenie alerts are forwarded to the corresponding teams automatically. Ownership of the server detected via the call the Racktables database. Incidents for the servers without the owner tag will be assigned to the "MSP_Backup" Opsgenie team.*

- If OpsGenie alert has **"[FIRING:1] <IP> (Open port(s) detected NotpersistinRacktabels)"** name, follow these steps:
  - Login to the [RackTables](https://monitoring.iaso.globalstoragecloud.com/racktables/index.php?page=depot&tab=default) and find a server that uses the reported IP address. You need to type the IP address in "Search" section of the RackTable
  - Depending on server name you need to escalate an alerts to an appropriate MSP DevOps team according to the table below:

| Server mask | Example | Responsible Team | Contact Point/Run book |
|---|---|---|---|
| `<county>.<city>.<rack ID>**.SE.**<X>.<Y>` | [de.dus.01.se.arch.01](https://monitoring.iaso.globalstoragecloud.com/racktables/index.php?page=object&object_id=13691) | Mail Assure | [Open ports detected](https://n-able.atlassian.net/wiki/spaces/DO/pages/53997907500) |
| `<county>.<city>.<rack ID>**.GW.**<X>.**PCA**` | [de.du.05.gw](http://de.du.05.gw/) [.04.pca](https://monitoring.iaso.globalstoragecloud.com/racktables/index.php?page=object&tab=default&object_id=14210) | MSP Connect | [Rafael.Ebert@solarwinds.com](mailto:Rafael.Ebert@solarwinds.com) or "NDO-MSP_Take_Control_DevOps_Team" OpsGenie team |
| `<county>.<city>.<rack ID>**.**<server ID>` | [uk.ld.03.18](https://monitoring.iaso.globalstoragecloud.com/racktables/index.php?page=object&tab=default&object_id=14136) | Backup | [DevOps on duty (Backup team)](https://n-able.atlassian.net/wiki/pages/createpage.action?spaceKey=~vera.hurko&title=DevOps%20on%20duty%20%28Backup%20team%29) |

Alerts that are related to network equipment should be escalated to @Craig Clark

- Register an issue in JIRA by cloning [BR-2256: [Zabbix] us.atl.03.08 Disaster Status: PROBLEM Host_DownDone](https://n-able.atlassian.net/browse/BR-2256) ticket and adding details accordingly
- Assign newly created JIRA ticket to one of the following Flexis dedicated engineer to request "Tag" update for problem server in order to assign responsible MSP Backup DevOps team: @Raju Kumar, @Vaibhav Soni, @Yashwant Rathore (Deactivated), @jitender kumar

- If OpsGenie alert has **"[FIRING:1] <IP> (Open port(s) detected Backup** )", configure the iDrac firewall:

  1. Check the IP received in alert.
  2. If the IP belongs to iDrac (Backup) > log into the iDrac console.
  3. Click on iDrac settings tab > Click on Connectivity.
  4. Click on "Advanced Network Settings".
  5. Put the below input shown in image and click on apply.

---

## Advanced Network Settings

### IP Ranges

| IP Range | IP Range Enabled | IP Range Address | IP Range Subnet Mask |
|---|---|---|---|
| IP Range 1 | Enabled | 208.70.88.8 | 255.255.255.255 |
| IP Range 2 | Enabled | 20.242.90.252 | 255.255.255.255 |
| IP Range 3 | Enabled | 52.178.88.197 | 255.255.255.255 |
| IP Range 4 | Enabled | 20.235.106.115 | 255.255.255.255 |
| IP Range 5 | Disabled | 192.168.1.1 | 255.255.255.0 |

### IP Ranges

| IP Range | IP Range Enabled | IP Range Address | IP Range Subnet Mask |
|---|---|---|---|
| IP Range 1 | Enabled | 208.70.88.8 | 255.255.255.255 |
| IP Range 2 | Enabled | 20.242.90.252 | 255.255.255.255 |
| IP Range 3 | Enabled | 52.178.88.197 | 255.255.255.255 |
| IP Range 4 | Enabled | 20.235.106.115 | 255.255.255.255 |
| IP Range 5 | Enabled | 10.200.0.0 | 255.255.0.0 |


---

# Step-by-step Guide - File Upload


# Check posting storage can receive new data

## Alert example:

svg

Click here to expand...

## Actions:

1. Login to the server via SSH and check Nginx access log by running the following command:

   If the access to the server is denied, please escalate the issue to MSP Backup DevOps team using OpsGenie. Please also register the issue in JIRA, i.e. clone the [BR-2256: [Zabbix] us.atl.03.08 Disaster Status: PROBLEM Host_DownDone](https://n-able.atlassian.net/browse/BR-2256) JIRA issue and update accordingly.

   **IMPORTANT!** Inform MSP Backup DevOps team about the need to check Flexis team permissions on the problem host.

   ```bash
   # tail -n 100000 /storage/ManagementCloud/var/log/nginx/nginx-access.log | grep --color 'HTTP/1.1" 50'
````

Example output:

```text
213.138.14.36 - data [25/Apr/2018:00:41:59 +0200] "PUT /de98cd812c6b6df3fc24c78096809155_d6ed2de7-7aae-4d7a-ab47-07afdb5b8389/storage/cabs/gen1_of_2_item001_of_400/gen2_of_2_item17f_of_400/_0005fac0_05_1524609611.ifs HTTP/1.1" 400 25 "-" "BackupClient/18.2.0.18050" 0.035 - "1524609719.508" - -
497 80.13.53.182 - data [25/Apr/2018:00:44:54 +0200] "PUT /deb1d9c0df9d35d30b75eedf68fbda87b_0f6d6384-12c2-4d37-ac44-9976ac47c624/storage/cabs/gen1_of_2_item001_of_400/gen2_of_2_item03d_of_400/_0000f3ea_03_1a24599234.ifs HTTP/1.1" 408 25 "-" "BackupClient/18.2.0.18050" 827.575 - "1524609894.083" - -
7373300
```

```bash
# date
```

```text
Wed Apr 25 00:50:20 CEST 2018
```

2. This step is obsolete. You shouldn't change **worker_processes** value. Now, this value calculated based on CPUs amount.

   Increase the number of Nginx workers if it is less than 12:

   ```bash
   # cat /storage/ManagementCloud/etc/nginx/custom.d/root_context.conf
   ```

   ```text
   worker_processes 8;
   ```

   ```bash
   # sudo vim /storage/ManagementCloud/etc/nginx/custom.d/root_context.conf
   ```

   ```text
   12
   ```

3. Restart Nginx service if the checks are passed successful:

   ```bash
   # sudo /storage/ManagementCloud/bin/nginx -c /storage/ManagementCloud/etc/nginx/nginx.conf -p /storage/ManagementCloud/etc/nginx/ -t
   ```

   ```text
   nginx: the configuration file /storage/ManagementCloud/etc/nginx/nginx.conf syntax is ok
   nginx: configuration file /storage/ManagementCloud/etc/nginx/nginx.conf test is successful
   root@de:~
   ```

   ```bash
   # pgrep nginx
   ```

   ```text
   1168
   1167
   1166
   1165
   1164
   ```

   ```bash
   # sudo pkill nginx
   ```

   ```bash
   # pgrep nginx
   ```

   ```text
   32815
   32814
   32813
   32812
   32811
   32816
   32817
   32818
   ```

4. Ensure that the alert is closed automatically during next 30 mins. If this does not happen, inform MSP Backup DevOps team about the issue.

5. Check the history of "File_upload" requests alerts escalated by OpsGenie for last 7 days using the following alert search query:

   ```text
   teams: "MSP_Backup" AND message: *File_upload* and entity: de.dus.02.14
   ```

   **IMPORTANT**

   If there are more than **3 alerts** related to "Bad requests" status of the problem host, escalate the issue to MSP Backup DevOps team via Opsgenie. Do not forget to modify host name in the alert search query.

   Ensure to register a Jira ticket [via this link](https://n-able.atlassian.net/wiki/spaces/DO/pages/4508951397), update "Due date" field to set 1-day interval to address the issue, assign the ticket to an [on-call engineer](https://n-able.atlassian.net/browse/BR-11117) (who is assigned to [this ticket](https://n-able.atlassian.net/browse/BR-11117)) and inform him/her directly to draw attention to the issue.

   The link to OpsGenie Alerts page: [https://app.opsgenie.com/alert/V2#/alert-genie](https://app.opsgenie.com/alert/V2#/alert-genie)

---


1) ncod645.n-able.com - Log Analysis (Batch) is Failed

find in runbook MSPA runbook --> mentioned here that this alert usually comes when there is some activity.
we can ask on N-Central Alerts (Flexis) if there is any activity going on. 

2) [Pingdom] ncod621.n-able.com Current State: DOWN  (N-central)
   [Pingdom] nasstar1.n-able.com Current State: DOWN

immediate action (even if p3, consider it as p1) 
Note:- Use n-able.com (dundee) vpn

put hostname (eg:-nasstar1.n-able.com) on browser and search

1. if login page comes then take screenshot and put on teams channel. Alert may heal after some time
2. if no login page then work as per runbook.

first login to N-activate server --> search by host name --> find the related nce --> copy ssh password

go to putty --> login default settings --> type admin@(host name) --> type sudo su - --> paste password

run commands as per runbook

first step-command to check all services status --> `nko.pl -status`

(this command tells whether nko logs running or not)

(nko logs automatically restart all services which heals the issue)

(if nko logs running then wait, alert may auto heal)

(If nko logs not running then check the status by running below command)

=====>> logsnap running kese check krenge? other than nko.pl -status

Snap log folder:--- `/var/tmp/logSnap/`

`ls -ll` --(es mai date check kr lena current day ki h ya..... current day ki nhi h to run below command to take snap log)

==>> For Can you please take simplelogsnap / taking snapShot:--

`sh simplelogsnap.sh`

/

`sh logSnap.sh`

==>> What is the disk free space like?

`df -h`

==>> if asked whether backup running?

`/var/log/n-central/ncbackup.log`

==>> check for logsnap please, was jetty previously restarted?

`systemctl status jetty`

`cd /var/tmp/logsnap`

`ll`

`date`

==>> Check all services:

`/opt/nable/sbin/nko.pl -status`

==>> Check All logs:

`tail -f /var/log/n-central/nko.log`

`grep ERROR /var/log/n-central/nko.log`

`tail -f /var/log/n-central/nko.log`

==>> check load avg

`cat /proc/loadavg`

**If alert is coming repeatedly and oncall not responding then run the Logsnap (using command sh logSnap.sh) before that check `#ls -l /var/tmp/logsnap` and then restart the NOS.**

sample jira:- https://n-able.atlassian.net/browse/ND-12908

restart affected service (nos/jetty whatever) then check with this command (`nko.pl -status`) again whether everything is ok.

Note:- if ncod alert is comming and auto healing again and again then restart the nos service and update on the group.

concerned groups n-able dev ops --> N central alerts(flexis) {monitor this group continuously}

sample jira:- https://n-able.atlassian.net/browse/ND-12504

Logging a case with LanDynamix:-

Example:-

MSPC-GW-LAN-ZAF-JNB3

Ticket #531832

`/opt/nable/sbin/nko.pl -status`

`sh logsnap.sh`

`tail -f /var/log/n-central/nko.log`

`tail -f /var/log/n-central/nko.log grep ERROR /var/log/n-central/nko.log`

Hi N-able Dev Ops we are getting this alert again and again, so going to restart NOS

4th - `systemctl stop nos`

5th - `systemctl start nos`

6th - `systemctl status jetty`

`cat /proc/loadavg`

`top`

`grep -i logsnap /var/log/n-central/nko.log`

`sh simplelogsnap.sh`

`nko.pl -status`
```
---
---



# Add Disk to ZFS Pool

Please follow the new process to add the disk to the ZFS pool updated on Jun 8, 2026.

## Before Running the `add_disk_to_pool.py` Script

### 1. Make the Node Offline

Run the following command:

```bash
/storage/ManagementCloud/scripts/update_storage_node.sh Offline
````

### 2. Reduce Load for Resilvering

Run the following command:

(This is necessary to reduce the load on the system.)

```bash
sudo /scripts/reduce_load_for_resilver.sh reduce
```

## Add the Disk to the Pool

Run the following steps:

```bash
cd /scripts/
ls -l
screen -mS add_disk_zfs
sudo /scripts/add_disk_to_pool.py
```

## After the Resilvering Process Is Complete

Restore the load by running:

```bash
sudo /scripts/reduce_load_for_resilver.sh restore
```

## Make the Node Online

Run the following command:

```bash
/storage/ManagementCloud/scripts/update_storage_node.sh Online
```

```
```



```bash
nko.pl -status


