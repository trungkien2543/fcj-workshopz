+++
title = " Blog 4"
type = ""
weight = 4
pre = "<b> 3.4. </b>"
+++

# **Scaling Cloudera’s development environment: Leveraging Amazon EKS, Karpenter, Bottlerocket, and Cilium for hybrid cloud**

*Harpreet Virk and Padma Iyer* | **26/09/2025** | in [Advanced (300)](https://aws.amazon.com/blogs/migration-and-modernization/category/learning-levels/advanced-300/), [Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/migration-and-modernization/category/compute/amazon-kubernetes-service/), [Best Practices](https://aws.amazon.com/blogs/migration-and-modernization/category/post-types/best-practices/), [Containers](https://aws.amazon.com/blogs/migration-and-modernization/category/containers/), [Graviton](https://aws.amazon.com/blogs/migration-and-modernization/category/compute/graviton/), [Migration](https://aws.amazon.com/blogs/migration-and-modernization/category/enterprise-strategy/migration-enterprise-strategy/), [Migration Acceleration Program (MAP)](https://aws.amazon.com/blogs/migration-and-modernization/category/migration-solutions/map/) | [Permalink](https://aws.amazon.com/blogs/migration-and-modernization/scaling-clouderas-development-environment-leveraging-amazon-eks-karpenter-bottlerocket-and-cilium-for-hybrid-cloud/) |

This post is co-written with Shreelola Hegde,Sriharsha Devineni and Lee Watterworth from Cloudera.

[Cloudera](https://www.cloudera.com/) is a global leader in enterprise data management, analytics, and AI. The Cloudera platform enables organizations to manage, process, and analyze massive datasets, helping businesses across industries like finance, healthcare, manufacturing, and telecommunications accelerate AI/ML adoption and unlock real-time insights.

A key element driving the success of the platform is the development environment, where developers can build and test new features for release. The environment, built on Kubernetes, faced multiple challenges on-premises especially in the scaling and utilization of resources.

This post covers how Cloudera modernized its development operations by adopting a hybrid cloud approach built on [Amazon Elastic Kubernetes Service (Amazon EKS)](https://aws.amazon.com/eks/). This strategy balances the existing capacity of on-premises environments with the elasticity of the cloud while leveraging enhancements such as [Karpenter](https://docs.aws.amazon.com/eks/latest/best-practices/karpenter.html), [Bottlerocker](https://aws.amazon.com/bottlerocket/), and [Cilium](https://cilium.io/) to optimize scaling, security, and cost efficiency.

### Operational Challenges faced On-Premises
- The development environment faced challenges running on-premises:
  - The environment was required to scale up and down frequently but was constrained by fixed capacity.
  - Build and test processes for services like Apache Spark, Hive, and HBase required containers as large as 64 GB RAM and 32 vCPUs, which quickly exceeded available resources.
  - Pull requests during intensive coding sprints surged as high as 300% leading to 45-minute build time increases, creating a bottleneck in the CI/CD pipeline that significantly slowed development velocity. This, in turn, delayed feature delivery, and risked release schedules while increasing infrastructure costs and developer idle time.
  - The application design also involved retrieving and saving artifacts and datasets from Amazon S3, which introduced latency for on-premises agents, further extending build and test cycles.
  - These constraints created bottlenecks in the development pipeline and highlighted the need for elasticity beyond on-premises clusters.

### Solution Overview

Cloudera addressed these challenges by adopting a hybrid cloud model where predictable workloads remained on-premises and dynamic workloads moved to AWS. The architecture brought together the elasticity of AWS with Cloudera’s established on-premises infrastructure, delivering seamless scaling, reduced latency, and optimized costs.

![image_architecture](https://d2908q01vomqb2.cloudfront.net/1f1362ea41d1bc65be321c0a378a20159f9a26d0/2025/09/24/AWS-3.jpg)

*High-level architecture of the development environment in AWS*

The modernized Kubernetes architecture provided the following benefits:

- **Elasticity with Amazon EKS and Karpenter**: Cloudera, using Karpenter, was able to scale its workloads from a handful of nodes to thousands within minutes and also contract when demand dropped. This ensured efficient scaling during surges while eliminating idle resource waste during freezes. This also enabled multiple pull requests to run in parallel without waiting for capacity, giving developers faster turnaround times and improving release velocity. Intelligent provisioning ensured that compute instances were always aligned with workload requirements, which improved utilization rates and reduced costs by up to 40%.

- **Handling large containers with Karpenter**: Build and test jobs requiring massive containers were matched instantly with optimized compute through Karpenter’s intelligent node provisioning. This real-time elasticity ensured no delays or resource contention.

- **Strengthening security with Bottlerocket**: The linux-based OS further enhanced the scaling process by delivering a container-optimized environment with minimal operating system overhead. Its immutable filesystem strengthened security by preventing unauthorized changes, while atomic updates simplified system patching and reduced maintenance downtime. This change reduced the development environment’s attack surface by 60%, streamlined patching through atomic updates, and improved compute efficiency by 35%.

- **Reducing build delays with Bottlerocket and [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) (Amazon EBS) (Amazon EBS) snapshots**: od launch times fell from 30 minutes to seconds using Bottlerocket and Amazon EBS snapshots to pre-cache large images. This improvement gave developers the ability to start new builds almost instantly, transforming productivity. Pull request spikes no longer created bottlenecks.

- **Scaling network with Cilium**: Networking was modernized with Cilium, which provided identity-based security, advanced pod-level observability, and eBPF-driven networking. By introducing flexible IP address management, Cilium allowed Cloudera to scale beyond 10,000 workloads without encountering IP exhaustion issues, all while offering clear visibility into pod-level networking.

- **Eliminating idle resource waste with [AWS Graviton](https://aws.amazon.com/ec2/graviton/) and Flex instances**: Graviton và Flex instances đóng vai trò quan trọng trong tối ưu chi phí và hiệu năng. Graviton mang lại lợi ích hiệu năng-giá tốt cho workloads dựa trên ARM, trong khi Flex instances tăng hiệu quả cho các tác vụ biên dịch x64. Kết hợp lại, các tùy chọn tính toán này giảm gần một phần ba chi phí vận hành và tối đa 40% chi phí hạ tầng, đảm bảo Cloudera cân bằng giữa hiệu suất và chi phí cho các nhu cầu workload đa dạng.

- **Resolving S3 latency with cloud-native integration**: Running builds in Amazon EKS, dropped latency to [Amazon S3](https://aws.amazon.com/s3/) to milliseconds, accelerating artifact retrieval. This had the additional benefit of lowering network transfer costs by 30%.

This holistic solution not only addressed each bottleneck but also created a foundation that scales elastically, operates securely, and optimizes costs across hybrid environments.

### Business Outcomes

The adoption of this hybrid Kubernetes environment transformed Cloudera’s development operations. Build and test cycle times improved by 50%, enabling faster delivery of new features and improvements. The ability to scale from 10 to more than 1,000 nodes in minutes gave developers reliable access to the resources they needed, eliminating bottlenecks during high demand. By optimizing data transfers with Amazon S3, network costs were reduced by 30% and latency was cut to milliseconds. Intelligent scaling and workload-aligned compute selection lowered infrastructure costs by 40%. Bottlerocket reduced the attack surface by 60% and improved compute efficiency by 35%.

These advances not only strengthened security but also delivered freed engineers from infrastructure management and allowing them to focus on core development.

### Conclusion

Cloudera’s successful implementation showcases the transformative power of AWS’s container stack – Amazon EKS, Karpenter, and Bottlerocket. The modernized Kubernetes environment resulted in seamless scaling, enhanced security, and optimized cost management while delivering peak performance for dynamic workloads. Cloudera’s journey proves how the integration of purpose-built AWS solutions can dramatically improve infrastructure management, reduce operational overhead, and accelerate developer productivity.

Through automated node provisioning, intelligent workload placement, and streamlined operations, Cloudera demonstrates how organizations can achieve efficiency in container environments. Following Cloudera’s proven architecture, enterprises can build a robust, scalable, and cost-effective Kubernetes environment that meets today’s demanding development needs while preparing for future growth. Speak with your AWS account team to take the next steps to building a modernized Amazon EKS environment.