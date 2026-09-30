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
