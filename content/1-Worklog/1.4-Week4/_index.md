+++
title = "Week 4"
type = ""
weight = 4
pre = "<b> 1.4. </b>"
+++

### Week 4 Objectives:

* Learn about Amazon S3 Storage Service
* Learn about Security Services on AWS
* Implement static web application deployment through S3 and cloudfront

### Tasks to be carried out this week:
| Day | Task| Start Date | Completion Date | Reference Material |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | --------------- | ----------------------------------------- |
| 2 | - Watch video lectures on S3 storage service <br> - Translate blogs for week 4 <br> - Hold weekly team meetings to update work progress.| 9/29/2025 | 9/29/2025 | [original link](https://aws.amazon.com/blogs/migration-and-modernization/scaling-clouderas-development-environment-leveraging-amazon-eks-karpenter-bottlerocket-and-cilium-for-hybrid-cloud/)
| 3 | - **Practice:** <br>+ lab57: STARTING WITH AMAZON S3 <br> + lab13: Deploy AWS Backup to the System <br> + lab 14: VM Import/Export <br> <br> - Research and make a list of APIs needed for the TaskFlow Service. | 9/30/2025 | 9/30/2025 | <https://000057.awsstudygroup.com> <https://000014.awsstudygroup.com> <https://000013.awsstudygroup.com/>
| 4 | - Watch video lectures on security services on AWS| 10/1/2025 | 10/1/2025 |
| 5 | - **Practice:** <br>+ lab2: AWS Identity and Access Management (IAM) Access Control <br> + lab44: IAM Role & Condition <br> + lab 48: Granting authorization for an application to access AWS services with an IAM role. | 10/2/2025 | 10/2/2025 | <https://000048.awsstudygroup.com> <https://000002.awsstudygroup.com> <https://000044.awsstudygroup.com>
| 6 | - **Practice:** <br>+ lab30: LIMITATION OF USER RIGHTS WITH IAM PERMISSION BOUNDARY <br> - Update the worklog.| 3/10/2025 | 3/10/2025 | <https://000030.awsstudygroup.com>

### Week 4 Achievements:

* Understand about storage services on S3

* Practice hosting a static website using Amazon S3:
  * Know how to initialize an S3 and upload data of the static website to it
  * Practice configuring AWS CloudFront to host a static website on S3 without having to public information about the bucket
  * Learn about the Bucket Versioning to preserve and restore versions of all objects stored in the bucket
  * Learn about the Amzon S3 Cross-Region Replication (CRR) to automatically copy objects across different AWS regions

* Practice implementing backups for the system, understand the two main concepts of RPO and RTO

* Learn how to import virtual machines from VMware to Amazon EC2 instances and migrate EC2 instances back to on-premises VMware environments. 

* Practice managing system access using AWS IAM by:
  * Creating an IAM user with permissions to access Amazon S3, and using the user’s access keys to upload files from a server to an S3 bucket.
  * Creating an IAM role for EC2 instances that grants S3 upload permissions, enabling secure access to S3 without embedding access keys (thus avoiding credential exposure).

* Practice using IAM PERMISSION BOUNDARY to limit user permissions, thereby simplifying permission management and avoiding the assignment of unnecessary privileges.

* Research and create a list of APIs required for the Taskflow service.
