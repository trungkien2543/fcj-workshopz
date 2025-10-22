+++
title = " Blog 5"
type = ""
weight = 5
pre = "<b> 3.5. </b>"
+++

# **Creating a Multi-Region Application with AWS Services – Part 1, Compute, Networking, and Security**

*Joe Chapman và Sebastian Leks* | 08/12/2021 | [Amazon CloudFront](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/amazon-cloudfront/), [Amazon EC2](https://aws.amazon.com/blogs/architecture/category/compute/amazon-ec2/), [Amazon Elastic Block Store (Amazon EBS)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-elastic-block-storage-ebs/), [Amazon Route 53](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/amazon-route-53/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-simple-storage-services-s3/), [Amazon VPC](https://aws.amazon.com/blogs/architecture/category/compute/amazon-vpc/), [Architecture](https://aws.amazon.com/blogs/architecture/category/architecture/), [AWS CloudTrail](https://aws.amazon.com/blogs/architecture/category/management-tools/aws-cloudtrail/), [AWS Global Accelerator](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/aws-global-accelerator/), [AWS Identity and Access Management (IAM)](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-identity-and-access-management-iam/), [AWS Secrets Manager](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-secrets-manager/), [AWS Security Hub](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-security-hub/), [AWS Transit Gateway](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/aws-transit-gateway/), [AWS Well-Architected](https://aws.amazon.com/blogs/architecture/category/aws-well-architected/) | [Permalink](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-1-compute-and-security/)

Many AWS services have features to help you build and manage a multi-Region architecture, but identifying those capabilities across 200+ services can be overwhelming.

In this 3-part blog series, we filter through those 200+ services and focus on those that have specific features to assist you in building multi-Region applications. In Part 1, we’ll build a foundation with AWS security, networking, and compute services. In Part 2, we’ll add in data and replication strategies. Finally, in Part 3, we’ll look at the application and management layers. As we go through each part, we’ll build up an example application to display one way of combining these services to create a multi-Region application.

## Considerations before getting started

[AWS Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) are built with multiple isolated and physically separate [Availbility Zone](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/#Availability_Zones). . This approach allows you to create highly available [Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) workloads that span AZs to achieve greater fault tolerance. This satisfies the availability goals for most applications, but there are some general reasons that you may be thinking about expanding beyond a single Region:

- **Expansion to a global audience** as an application grows and its user base becomes more geographically dispersed, there can be a need to reduce latencies for different parts of the world.

- **Reducing [Recovery Point Objectives (RPO) and Recovery Time Objectives (RTO)](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/disaster-recovery-dr-objectives.html)** as part of a multi-Region [disaster recovery (DR) plan.](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)

- **Local laws and regulations**may have strict data residency and privacy requirements that must be followed.

If you’re building a new multi-Region application, you may want to consider focusing on AWS services that have built-in functionality to assist. Existing applications will need to be further examined to determine the most expandable architecture to support its growth. The following sections review these services, and highlight use cases and best practices.

## Identity and access across Regions

Creating a security foundation starts with setting proper authentication and authorization rules. The system handling these requests must be highly resilient to verify and authorize requests quickly and reliably. [AWS Identity and Access Management (IAM)](http://aws.amazon.com/iam) accomplishes this by creating a reliable mechanism for you to manage access to AWS services and resources. IAM has multi-Region availability automatically, with no configuration required on your part.

For help managing Windows users, devices, and applications on a multi-Region network, you can set up [AWS Directory Service for Microsoft Active Directory](https://aws.amazon.com/directoryservice/active-directory/) Enterprise Edition to [automatically replicate directory data across Regions.](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_configure_multi_region_replication.html).This reduces directory lookup latencies by using the closest directory and creates durability by spanning multiple Regions. Note that this will also introduce a shared fate across domain controllers for multi-Region topologies, because group policy changes will be propagated to all member servers.

Applications that need to securely store, rotate, and audit secrets, such as database passwords, should use [AWS Secrets Manager](https://aws.amazon.com/secrets-manager). This service encrypts secrets with [AWS Key Management Service (AWS KMS)](http://aws.amazon.com/kms) keys and can [replicate secrets to secondary Regions](https://docs.aws.amazon.com/secretsmanager/latest/userguide/create-manage-multi-region-secrets.html) to ensure applications are able to quickly retrieve a secret in the closest Region.

## Encryption across Regions

AWS KMS can be used to encrypt data at rest, and is used extensively for encryption across AWS services. By default, keys are confined to a single Region. AWS services such as [AWS như Amazon Simple Storage Service (Amazon S3)](http://aws.amazon.com/s3) scross-Region replication and Amazon Aurora Global Database (both covered in [phần 2](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-2-data-and-replication/)), simplify the process of encryption and decryption with different keys in each Region. For other parts of your multi-Region application that rely on KMS keys, you can set up [AWS KMS multi-Region keys](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html) to replicate the key material and key ID to a second Region. This eliminates the need to decrypt and re-encrypt data with a different key in each Region. For example, multi-Region keys can be used to reduce the complexity of a multi-Region application’s encryption operations for data that is stored across Regions.

## Auditing and observability across Regions

It is a best practice to configure [AWS CloudTrail](http://aws.amazon.com/cloudtrail) to keep a record of all relevant AWS API activity in your account for auditing purposes. When you utilize multiple Regions or accounts, these CloudTrail logs should be [aggregated](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) into a single Amazon S3 bucket for easier analysis. To prevent misuse, the centralized logs should be treated with higher severity, with only limited access to key systems and personnel.

To stay on top of [AWS Security Hub](https://aws.amazon.com/security-hub) findings, you can [aggregate and link findings from multiple locations to a single Region. ](https://docs.aws.amazon.com/securityhub/latest/userguide/finding-aggregation-enable.html)This is an easy way to create a centralized view of Security Hub findings across accounts and Regions. Once set up, the findings are continuously synced between Regions to keep you updated on global results in a single dashboard.

We put these features together in Figure 1. We used IAM to grant fine-grained access to AWS services and resources, Directory Service for Microsoft AD for authentication to Microsoft applications, and Secrets Manager to store sensitive database credentials. Our data, which moves freely between Regions, is encrypted with KMS multi-Region keys, and all AWS API access is logged with CloudTrail and aggregated to a central S3 bucket that only our security team has access to.

![Figure 1. Multi-Region security, identity, and compliance services](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-1.-Multi-Region-security-identity-and-compliance-services-1.png)

*Figure 1. Multi-Region security, identity, and compliance services*

## Building a global network

For resources launched into virtual networks in different Regions, [Amazon Virtual Cloud (Amazon VPC)](http://aws.amazon.com/vpc) allows [private routing between Regions and accounts with VPC peering](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html). These resources can communicate using private IP addresses and do not require an internet gateway, VPN, or separate network appliances. This feature works well for smaller networks that only require a few peering connections. However, transitive routing is not allowed, and as the number of peered virtual private cloud (VPCs) increases, the mesh of peered connections can become difficult to manage and troubleshoot.

[AWS Transit Gateway](https://aws.amazon.com/transit-gateway/) reduces these difficulties by creating a network transit hub that connects your VPCs and on-premises networks. A Transit Gateway’s routing capabilities can expand to additional Regions with [Transit Gateway inter-Region peering](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-peering.html) to create a globally distributed, private network for your resources.

Building a reliable, cost-effective way to route users to distributed Internet applications requires highly available and scalable Domain Name System (DNS) records. [Amazon Route 53](http://aws.amazon.com/route53) does exactly that.

Route 53 includes many [routing policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html). For example, you can route a request to a record with the lowest network latency, or send users in a specific geolocation to a localized application endpoint. For DR, [Route 53 Application Recovery Controller (Route 53 ARC)]() offers a comprehensive failover solution with minimal dependencies. Route 53 ARC routing policies, safety checks, and readiness checks help you to failover across Regions, AZs, and on-premises reliably.

Route 53 includes many [routing policies](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html). For example, you can route a request to a record with the lowest network latency, or send users in a specific geolocation to a localized application endpoint. For DR, [Route 53 Application Recovery Controller (Route 53 ARC)](https://aws.amazon.com/route53/application-recovery-controller/) offers a comprehensive failover solution with minimal dependencies. Route 53 ARC routing policies, safety checks, and readiness checks help you to failover across Regions, AZs, and on-premises reliably.

The [Amazon CloundFront](http://aws.amazon.com/cloudfront) content delivery network is global, built across 300+ points of presence (PoP) spread throughout the world. Applications that have multiple possible origins, such as across Regions, can use [CloudFront origin failover](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html) to automatically fail over to a recovery origin when the primary is not available. CloudFront’s capabilities expand beyond serving content, with the ability to run compute at the edge. [CloudFront Functions](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cloudfront-functions.html) make it easy to run lightweight JavaScript code, and [AWS Lambda@Edge](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html) enables you to run Node.js and Python functions closer to users of your application, which improves performance and reduces latency. By placing compute at the edge, you can take load off of your origin and provide quicker responses for your global end users.

Built on the AWS global network, [AWS Global Accelerator](http://aws.amazon.com/global-accelerator) provides two static anycast IPs to give a single-entry point for internet-facing applications. You can seamlessly add or remove origins while continuing to automatically route traffic to the closest healthy Regional endpoint. If a failure is detected, Global Accelerator will [automatically redirect traffic to a healthy endpoint](https://docs.aws.amazon.com/global-accelerator/latest/dg/disaster-recovery-resiliency.html) within seconds, with no changes to the static IP.

Figure 2 uses a Route 53 latency-based routing policy to route users to the quickest endpoint, CloudFront is used to serve static content such as videos and images, and Transit Gateway creates a global private network for our devices to talk securely across Regions.

![figure2](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-2.-AWS-VPC-connectivity-and-content-delivery.png)

*Building and managing the compute layer*

## Xây dựng và quản lý lớp tính toán (compute layer)

Although [Amazon Elastic Compute Cloud (Amazon EC2)](http://aws.amazon.com/ec2) instances and their associated [Amazon Elastic Block Store (Amazon EBS)](http://aws.amazon.com/ebs) volumes reside in a single AZ [Amazon Data Lifecycle Manager](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshot-lifecycle.html) can automate the process of taking and copying EBS snapshots across Regions. This can enhance DR strategies by providing an easy cold backup-and-restore option for EBS volumes. If you need to back up more than just EBS volumes, [AWS Backup](https://aws.amazon.com/backup/)  provides a central place to do this across multiple services and is covered in [part 2](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-2-data-and-replication/).

An Amazon EC2 instance is based on an [Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html).  An AMI specifies instance configurations such as the instance’s storage, launch permissions, and device mappings. When a new standard image needs to be created and released,  [EC2 Image Builder](https://aws.amazon.com/image-builder/) simplifies the building, testing, and deployment of new AMIs. It can also help with copying of AMIs to additional Regions to eliminate needing to manually copy source AMIs to target Regions.

Microservice-based applications that use containers benefit from quicker start-up times. [Amazon Elastic Container Registry (Amazon ECR)](http://aws.amazon.com/ecr/) can help ensure this happens consistently across Regions with [private image replication](https://aws.amazon.com/blogs/containers/cross-region-replication-in-amazon-ecr-has-landed/) at the registry level. An ECR private registry can be configured for either cross-Region or cross-account replication to ensure your images are ready in secondary Regions when needed.

As an architecture expands into multiple Regions, it can become difficult to track where resources are provisioned. [Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) helps alleviate this by providing a centralized dashboard to see Amazon EC2 resources such as instances, VPCs, subnets, security groups, and volumes in all active Regions.

We bring these compute layer features together in Figure 3 by using EC2 Image Builder to copy our latest golden AMI across Regions for deployment. We also back up each EBS volume for 3 days and replicate it across Regions using Data Lifecycle Manager.

![Figure 3](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-3.-AMI-and-EBS-snapshot-copy-across-Regions.png)

*Figure 3. AMI and EBS snapshot copy across Regions*

## Bringing it together

At the end of each part of this blog series, we build on a sample application based on the services covered. This shows you how to bring these services together to build a multi-Region application with AWS services. We don’t use every service mentioned, just those that fit the use case.

We built this example to expand to a global audience. It requires high availability across Regions, and favors performance over strict consistency. We have chosen the following services covered in this post to accomplish our goals:

- A Route 53 latency routing policy that routes users to the deployment with the least latency.

- CloudFront is set up to serve our static content. Region 1 is our primary origin, but we’ve configured origin failover to Region 2 in case of a disaster.

- The application relies on several third-party APIs, so Secrets Manager with cross-Region replication has been set up to store sensitive API key information.

- We centralize our CloudTrail logs in Region 1 for easier analysis and auditing.

- Security Hub in Region 1 is where we have chosen to aggregate findings from all Regions.

- This is a containers-based application, and we rely on Amazon ECR replication for each location to quickly pull the latest images locally.

- To communicate using private IPs across Regions, a Transit Gateway is set up in each Region with intra-Region between them. VPC peering could have also worked, but we expect to expand to several more Regions in the future and decided this would be the better long-term choice.

- IAM is used to grant access to manage our AWS resources.

![Figure 4](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-4.-Building-an-application-with-AWS-multi-Region-services-using-services-covered-in-Part-1.png)

*Figure 4. Building an application with AWS multi-Region services using services covered in Part 1*

### Summary

It’s important to create a solid foundation when architecting a multi-Region application. These foundations lay the groundwork for you to move fast in a secure, reliable, and elastic way as you build out your application. Many AWS services include native features to help you build a multi-Region architecture. Your architecture will be different depending on the reason for expanding beyond a single Region. In this post, we covered specific features across AWS security, networking, and compute services that have built-in functionality to take away some of the undifferentiated heavy lifting. We’ll cover data, application, and management services in future posts.


**Ready to get started?**
We’ve chosen some [AWS Solutions](https://aws.amazon.com/solutions/) and [AWS Blogs](https://aws.amazon.com/blogs/?awsf.blog-master-category=*all&awsf.blog-master-learning-levels=*all&awsf.blog-master-industry=*all&awsf.blog-master-analytics-products=*all&awsf.blog-master-artificial-intelligence=*all&awsf.blog-master-aws-cloud-financial-management=*all&awsf.blog-master-business-applications=*all&awsf.blog-master-compute=*all&awsf.blog-master-customer-enablement=*all&awsf.blog-master-customer-engagement=*all&awsf.blog-master-database=*all&awsf.blog-master-developer-tools=*all&awsf.blog-master-devops=*all&awsf.blog-master-end-user-computing=*all&awsf.blog-master-mobile=*all&awsf.blog-master-iot=*all&awsf.blog-master-management-governance=*all&awsf.blog-master-media-services=*all&awsf.blog-master-migration-transfer=*all&awsf.blog-master-migration-solutions=*all&awsf.blog-master-networking-content-delivery=*all&awsf.blog-master-programming-language=*all&awsf.blog-master-sector=*all&awsf.blog-master-security=*all&awsf.blog-master-storage=*all&filtered-posts.q=multi-region&filtered-posts.q_operator=AND) để hỗ trợ bạn!

**Looking for more architecture content?**
 [AWS Architecture Center](https://aws.amazon.com/architecture/) provides reference architecture diagrams, vetted architecture solutions, Well-Architected best practices, patterns, icons, and more!

 Link bài viết gốc: https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-1-compute-and-security/