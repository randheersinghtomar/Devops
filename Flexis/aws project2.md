> **“In my current AWS environment, I mainly work on AWS infrastructure administration and production support. I manage EC2 instances, VPC, IAM, S3, EBS, RDS and support EKS environments. I also work with Load Balancers, Auto Scaling and Security Groups.**
>
> **My day-to-day work is mostly monitoring and troubleshooting production issues. I handle alerts such as EC2 host down, disk-space issues, high CPU, HTTP 502 or application issues, file-upload issues, security/open-port alerts and RDS or EKS health issues. I investigate the issue using CloudWatch, AWS console, Linux commands and application logs, perform the required fix, verify the recovery and update the incident in Jira/OpsGenie.”**

If the interviewer asks **“What exactly do you do day-to-day?”**, then we can go into the **6 real alerts one by one** 

---
# 6 Practical Production Alerts

Exactly what I do in production:

1. **EC2 Host Down** *(EC2, CloudWatch, VPC, Security Groups, Load Balancer)*
2. **High CPU / Performance Issue** *(EC2, CloudWatch, Auto Scaling, Load Balancer)*
3. **Disk Space / Filesystem Full** *(EC2, EBS, CloudWatch, Linux Filesystem/LVM)*
4. **HTTP 502 / Bad Request / Application Issue** *(Load Balancer, EC2, Security Groups, CloudWatch)*
5. **File Upload Failure** *(S3, EC2, IAM, Security Groups, CloudWatch)*
6. **Security / Open Port Alert** *(EC2, Security Groups, VPC, IAM, CloudTrail)*

For each one, the same simple interview format:

**Alert → Initial Checks → AWS Checks → Linux/Application Checks → Fix → Verification → Jira/OpsGenie Update**
```
