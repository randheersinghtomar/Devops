<h1 align="center">Project Name:- Take Control (Covers Aws work)</h1>


 **“In my current AWS environment, I mainly work on AWS infrastructure administration and production support. I manage EC2 instances, VPC, IAM, S3, EBS, RDS and support EKS environments. I also work with Load Balancers, Auto Scaling and Security Groups.**

 **My day-to-day work is mostly monitoring and troubleshooting production issues. I handle alerts such as EC2 host down, disk-space issues, high CPU, HTTP 502 or application issues, file-upload issues, security/open-port alerts and RDS or EKS health issues. I investigate the issue using CloudWatch, AWS console, Linux commands and application logs, perform the required fix, verify the recovery and update the incident in Jira/OpsGenie.”**

If the interviewer asks **“What exactly do you do day-to-day?”**, then we can go into the **6 real alerts one by one** 

---
# 7 Practical Production Alerts

Exactly what I do in production:

1. **EC2 Host Down** *(EC2, CloudWatch, VPC, Security Groups, Load Balancer)*
2. **High CPU / Performance Issue** *(EC2, CloudWatch, Auto Scaling, Load Balancer)*
3. **Disk Space / Filesystem Full** *(EC2, EBS, CloudWatch, Linux Filesystem/LVM)*
4. **HTTP 502 / Bad Request / Application Issue** *(Load Balancer, EC2, Security Groups, CloudWatch)*
5. **File Upload Failure** *(S3, EC2, IAM, Security Groups, CloudWatch)*
6. **Security / Open Port Alert** *(EC2, Security Groups, VPC, IAM, CloudTrail)*
7. **EKS / Kubernetes Application Issue** *(EKS, EC2, CloudWatch, IAM, VPC, Load Balancer)*

For each one, the same simple interview format:

**Alert → Initial Checks → AWS Checks → Linux/Application Checks → Fix → Verification → Jira/OpsGenie Update**

---
---

<h1 align="center"> 1. EC2 Host Down</h1>

**Services:** EC2, CloudWatch, VPC, Security Groups, Load Balancer

## Alert

I receive an **EC2 Host Down alert** from **CloudWatch/Zabbix/OpsGenie** indicating that an EC2 server is not reachable or is unhealthy.

## Initial Checks

First, I check:

- Which EC2 instance is affected?
- Instance ID, private IP and hostname
- When the alert started
- Whether the issue is with one server or multiple servers
- Whether the server is reachable through SSH

## AWS Checks

From the AWS Console, I check:

1. **EC2 Instance State**
   - Is the instance `Running`, `Stopped` or `Terminated`?

2. **Status Checks**
   - System status check
   - Instance status check

3. **CloudWatch Metrics**
   - CPU utilization
   - Network traffic
   - Status check failures

4. **Security Group**
   - Check whether SSH (22) or required application ports are allowed.

5. **VPC / Network**
   - Check subnet and route table
   - Check Network ACL if required

6. **Load Balancer**
   - If the server is behind an ALB, check whether the EC2 instance is showing as **Healthy or Unhealthy** in the target group.

## Linux Checks

If I can access the server, I check:

```bash
uptime
top
free -m
df -h
systemctl --failed
systemctl status <service>
````

For network-related issues:

```bash
ip addr
ip route
ss -lntp
```

I also check system/application logs if required.

## Troubleshooting / Fix

Depending on the cause:

* If the instance is stopped unexpectedly, investigate the reason and start it if appropriate.
* If the issue is related to CPU, memory or disk, troubleshoot the resource problem.
* If a service is down, restart the required service.
* If it is a network/security issue, correct the required Security Group, route or network configuration through the appropriate process.
* If the EC2 instance has failed an AWS status check, investigate the AWS infrastructure or instance-level issue and take the appropriate recovery action.

I don't restart or terminate a production instance blindly. I first identify the cause and follow the incident/change process.

## Verification

After the fix, I verify:

* EC2 instance is running
* AWS status checks are healthy
* SSH connectivity is restored
* Required Linux services are running
* Application is responding
* ALB target becomes **Healthy**
* CloudWatch/Zabbix alert is cleared

For example:

```bash
systemctl status <service>
df -h
uptime
```

I also verify the application from the Load Balancer side if applicable.

## Jira / OpsGenie Update

Finally, I update the incident with:

* Alert time
* Affected EC2 instance
* Initial findings
* Root cause / issue identified
* Actions performed
* Verification results
* Current status

Then I close or resolve the alert after confirming that the server and application are healthy.

## Interview Answer

> **“When I receive an EC2 host-down alert, I first identify the affected instance and check whether the issue is with a single server or multiple servers. I check the EC2 instance state, AWS status checks and CloudWatch metrics. Then I check connectivity, Security Groups, VPC networking and, if the server is behind a Load Balancer, I check the target health.**
>
> **If I can access the server, I check CPU, memory, disk space, services and system logs using Linux commands. Based on the root cause, I restart the required service, resolve the resource or network issue, or take the appropriate EC2 recovery action. After the fix, I verify the EC2 status, Linux services, application response and Load Balancer target health. Finally, I update the incident in Jira/OpsGenie with the findings, actions and verification results.”**

```
```

**Alert → Initial Checks → AWS Checks → Linux/Application Checks → Fix → Verification → Jira/OpsGenie Update**

---
---


<h1 align="center">  2. High CPU / Performance Issue -Detailed</h1>


**Services:** EC2, CloudWatch, Auto Scaling, Load Balancer

---

## 1. Alert

I receive a **High CPU alert** from **CloudWatch / Zabbix / OpsGenie** for an EC2 instance.

Example:

> EC2 instance `i-xxxxxxxx` has CPU utilization above the configured threshold.

---

## 2. Initial Checks

First, I identify:

- EC2 Instance ID
- Hostname / Private IP
- Current CPU utilization
- When the CPU spike started
- Whether one or multiple EC2 instances are affected
- Whether users are experiencing slow application response

---

## 3. AWS Checks

### 3.1 EC2 Instance

Go to:

**AWS Console → EC2 → Instances → Select Instance**

Check:

- Instance state → `Running`
- Instance type
- Availability Zone
- Status checks
- Monitoring status

---

### 3.2 CloudWatch

Go to:

**CloudWatch → Metrics → EC2 → Per-Instance Metrics**

Check:

- `CPUUtilization`
- CPU trend for the last 15 minutes / 1 hour
- Time when CPU started increasing
- Whether CPU is continuously high or only spiking

Also check:

- `NetworkIn`
- `NetworkOut`
- `DiskReadOps`
- `DiskWriteOps`
- `StatusCheckFailed`

This helps determine whether the CPU issue is related to increased traffic, disk activity or another instance problem.

---

### 3.3 Auto Scaling

Go to:

**EC2 → Auto Scaling Groups → Select ASG → Activity**

Check:

- Desired capacity
- Minimum capacity
- Maximum capacity
- Current number of instances
- Recent scaling activities
- Whether a new EC2 instance was launched because of high workload

---

### 3.4 Load Balancer

Go to:

**EC2 → Target Groups → Select Target Group → Targets**

Check:

- Target health
- Healthy / Unhealthy targets
- Number of EC2 instances
- Whether the affected instance is receiving traffic

If one instance is receiving unusually high traffic, I investigate the traffic distribution and application workload.

---

# 4. Linux Checks

After connecting to the affected EC2 instance, I identify which process is consuming CPU.

### 4.1 Check CPU

```bash
top
````

Check:

* `%us` → CPU used by user processes
* `%sy` → CPU used by system/kernel
* `%id` → CPU idle
* `%wa` → CPU waiting for I/O

---

### 4.2 Find Top CPU-Consuming Processes

```bash
ps -eo pid,ppid,user,%cpu,%mem,cmd --sort=-%cpu | head
```

Example:

```text
PID    USER     %CPU    %MEM    CMD
2456   appuser  92.5    4.2     java -jar application.jar
1823   mysql    35.2    8.1     /usr/sbin/mysqld
```

I identify the process consuming the highest CPU.

---

### 4.3 Check Load Average

```bash
uptime
```

Example:

```text
load average: 8.50, 7.90, 7.20
```

I compare the load average with the number of CPU cores.

---

### 4.4 Check Memory

```bash
free -m
```

Check:

* Available memory
* Used memory
* Swap usage

This helps identify whether memory pressure is also contributing to the performance issue.

---

### 4.5 Check Disk I/O

```bash
iostat -xz 1 5
```

Check:

* `%util`
* `await`
* I/O activity
* `%iowait`

This helps determine whether the system is waiting on disk I/O rather than purely consuming CPU.

---

# 5. Application / Service Checks

After identifying the high-CPU process, I check the corresponding application/service.

## Example: Apache

Check service:

```bash
systemctl status httpd
```

Check Apache processes:

```bash
ps -ef | grep httpd
```

Check Apache logs:

```bash
tail -100 /var/log/httpd/error_log
```

---

## Example: Nginx

Check service:

```bash
systemctl status nginx
```

Check Nginx processes:

```bash
ps -ef | grep nginx
```

Check Nginx logs:

```bash
tail -100 /var/log/nginx/error.log
```

---

## Example: Java Application

Find Java process:

```bash
ps -ef | grep java
```

Then check the application's configured log location for:

* Exceptions
* Repeated errors
* High request activity
* Application failures
* Abnormal processes

---

# 6. Troubleshooting / Fix

The fix depends on the actual cause.

### Case 1: Application Process Consuming High CPU

I identify why the application process is consuming CPU.

I check:

* Application logs
* Recent application activity
* Number of requests
* Whether the process is stuck
* Whether the application team has identified an issue

If the application team confirms that the service needs to be restarted:

```bash
sudo systemctl restart <application-service>
```

---

### Case 2: High Traffic

I check:

**EC2 → Target Groups → Targets**

and

**CloudWatch → EC2 Metrics**

If the high CPU is caused by increased workload, I check:

**EC2 → Auto Scaling Groups → Activity**

to verify whether Auto Scaling is launching additional instances.

---

### Case 3: Disk I/O Causing Performance Issue

I check:

```bash
iostat -xz 1 5
```

and CloudWatch:

* `DiskReadOps`
* `DiskWriteOps`

If the issue is related to storage performance, I investigate the EBS volume and follow the approved change process.

---

### Case 4: Instance Capacity Is Not Sufficient

If the workload consistently exceeds the current instance capacity, I raise it with the appropriate team.

After approval, the instance type or Auto Scaling configuration can be adjusted through the approved change process.

---

## Important

I do **not** immediately run:

```bash
kill -9 <PID>
```

or reboot the production server just because CPU is high.

First, I identify:

**Which process → Why CPU is high → What is the impact → What is the correct fix**

---

# 7. Verification

After applying the fix, I verify the issue from AWS, Linux and application sides.

### CloudWatch

Go to:

**CloudWatch → Metrics → EC2**

Verify:

* `CPUUtilization` has returned to normal
* No new CPU alerts
* Network metrics are normal
* Disk metrics are normal

---

### Linux

```bash
top
uptime
free -m
```

Confirm:

* CPU utilization has reduced
* Load average has reduced
* Memory is normal

---

### Application Service

For Apache:

```bash
systemctl status httpd
```

For Nginx:

```bash
systemctl status nginx
```

For the application:

```bash
systemctl status <application-service>
```

Verify that the application service is running normally.

---

### Load Balancer

Go to:

**EC2 → Target Groups → Targets**

Verify:

* Target status = `Healthy`
* No unexpected unhealthy targets
* Traffic is being distributed normally

---

### Auto Scaling

Go to:

**EC2 → Auto Scaling Groups → Activity**

Verify:

* Scaling activity completed successfully
* Desired and current capacity are correct
* New instances are healthy, if scaling occurred

---

# 8. Jira / OpsGenie Update

Finally, I update the incident with:

* Affected EC2 instance
* CPU utilization observed
* Time of CPU spike
* High-CPU process identified
* CloudWatch findings
* Linux investigation
* Application/service findings
* Action performed
* Verification results
* Current status

---

# 9. Interview Answer

> **“When I receive a high CPU alert, I first identify the affected EC2 instance and check the CPU utilization trend in CloudWatch. I also check network, disk and status-check metrics to understand whether the issue is related to workload or another resource.**
>
> **Then I connect to the Linux server and use `top`, `ps`, `uptime`, `free` and `iostat` to identify the process consuming CPU and check whether memory or I/O is also contributing. If, for example, Apache, Nginx or a Java application is consuming high CPU, I check the specific service status and its application logs.**
>
> **I also check the Load Balancer target health and Auto Scaling activity to see whether the instance is receiving high traffic or whether additional instances have been launched. Based on the root cause, I restart the affected application service if required or follow the approved scaling/change process.**
>
> **After the fix, I verify CPU utilization in CloudWatch, check the Linux service and application, confirm the Load Balancer target is healthy and verify Auto Scaling activity. Finally, I update the incident in Jira/OpsGenie with the findings, actions and verification results.”**

```
```

---
---

<h1 align="center"> 3. Disk Space / Filesystem Full</h1>


**Services:** EC2, EBS, CloudWatch, Linux Filesystem/LVM

## Sample Alert

> **ALERT: Disk Space Usage High**
>
> Host: `prod-app-01`  
> Instance: `i-0abc123456789`  
> Filesystem: `/var`  
> Usage: `92%`  
> Threshold: `90%`  
> Status: **PROBLEM**

---

## 1. Alert

I receive a **disk-space alert** from CloudWatch/Zabbix/OpsGenie indicating that a filesystem is above the configured threshold.

---

## 2. Initial Checks

Identify:

- EC2 instance ID
- Hostname / Private IP
- Affected filesystem
- Current usage
- Alert start time

---

## 3. AWS Checks

### EC2

**EC2 → Instances → Affected Instance**

Check:

- Instance is running
- Status checks are healthy
- Instance ID and AZ

### EBS

**EC2 → Elastic Block Store → Volumes**

Check:

- Attached EBS volume
- Volume ID
- Volume size
- Volume type, e.g. `gp3`

If cleanup is not sufficient, check whether the EBS volume needs to be expanded.

### CloudWatch

**CloudWatch → Metrics → EC2**

Check:

- Disk-related alarm
- Alert history
- Other metrics if the disk issue is affecting the application

---

## 4. Linux Checks

### Check filesystem usage

```bash
df -h
````

### Check inode usage

```bash
df -i
```

### Find which directory is consuming space

```bash
sudo du -xhd1 / | sort -h
```

Then go deeper into the affected directory:

```bash
sudo du -xhd1 /var | sort -h
```

Common locations:

```text
/var/log
/var/lib
/tmp
/home
```

### Check disks and mounts

```bash
lsblk
df -Th
```

If LVM is used:

```bash
pvs
vgs
lvs
```

---

## 5. Identify the Cause

For example, if `/var/log` is consuming most of the space:

```bash
sudo du -xhd1 /var/log | sort -h
```

Check the relevant logs/service before taking action.

---

## 6. Fix

### If logs/temp files are consuming space

Clean up according to the approved cleanup policy.

For systemd journal:

```bash
sudo journalctl --disk-usage
```

If approved:

```bash
sudo journalctl --vacuum-time=7d
```

### If EBS needs more space

Increase the volume from:

**EC2 → Volumes → Modify volume**

Then verify:

```bash
lsblk
```

For **ext4**:

```bash
sudo resize2fs /dev/...
```

For **XFS**:

```bash
sudo xfs_growfs /mount_point
```

If LVM is used:

```bash
pvs
vgs
lvs
```

Then typically:

```bash
sudo lvextend -r -L +10G /dev/mapper/...
```

---

## 7. Verification

```bash
df -h
df -i
lsblk
```

Verify:

* Filesystem usage is normal
* Application/service is working
* CloudWatch/Zabbix alert is cleared
* No new disk alert is generated

---

## 8. Jira / OpsGenie Update

Document:

* Affected EC2 instance
* Filesystem and usage %
* Root cause
* EBS volume details
* Cleanup or expansion performed
* Verification result
* Alert resolution

---

## Interview Answer

> **“When I receive a disk-space alert, I identify the affected EC2 instance and filesystem. I check `df -h` and `df -i` to determine whether it is space or inode related. Then I use `du` to find what is consuming the space and check the attached EBS volume from AWS. Depending on the cause, I clean up approved logs or temporary files, or expand the EBS volume and filesystem. Finally, I verify the filesystem, application health and monitoring alert, and update Jira or OpsGenie.”**

```
```
