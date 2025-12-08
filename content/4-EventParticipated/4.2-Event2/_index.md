+++
title = "Event 2"
type = ""
weight = 2
pre = " <b> 4.2. </b> "
+++

# Event Summary: “AWS CLOUD MASTERY SERIES #3 – Security on AWS”

### Event Objectives

- A deep-dive sharing session that helps participants understand the 5 security pillars of the AWS Well-Architected Security Pillar and how to protect cloud systems against increasing threats.

- Sharing about security-supporting services and system auditing methods to protect AWS accounts and systems from attacks.

- An opportunity to connect with the First Cloud Journey community.

### Speakers

- **Le Vu Xuan An**: AWS Cloud Club Captain HCMUTE, First Cloud AI Journey
- **Tran Duc Anh**: AWS Cloud Club Captain SGU, First Cloud AI Journey, Cloud Security Engineer Trainee
- **Tran Doan Cong Ly**: AWS Cloud Club Captain PTIT, First Cloud AI Journey
- **Dang Hoang Hieu Nghi**: AWS Cloud Club Captain HUFLIT, First Cloud AI Journey
- **Nguyen Tuan Thinh**: First Cloud AI Journey, Cloud Engineer Trainee
- **Huynh Hoang Long**: First Cloud AI Journey, Cloud Engineer Trainee
- **Dinh Le Hoang Anh**: First Cloud AI Journey, Cloud Engineer Trainee
- **Nguyen Do Thanh Dat**: First Cloud AI Journey, Cloud Engineer Trainee
- **Van Hoang Kha**: First Cloud AI Journey, Cloud Engineer Trainee
- **Thinh Lam**: First Cloud AI Journey
- **Viet Nguyen**: First Cloud AI Journey
- **Mendel Grabski (Long)**: ex Head of Security & DevOps, Cloud Security Solutions Architect
- **Tinh Truong**: AWS Community Builder, Platform Engineer at TymeX

### Key Highlights

#### Identity & Access Management Service

- Understand what IAM is used for
- Best practices for using IAM in system security
- SCP and permission boundaries for multi-account setups
- MFA, credential rotation, Access Analyzer

#### Detection & Continuous Monitoring

- CloudTrail (org-level), GuardDuty, Security Hub
- Logging at all layers: VPC Flow Logs, ALB/S3 logs
- Alerting & automation with EventBridge
- Detection-as-Code (infrastructure + rules)

#### Infrastructure Protection

- VPC segmentation, private vs public placement
- Security Groups vs NACLs: usage models
- WAF + Shield + Network Firewall
- Workload protection: EC2, ECS/EKS security basics

#### Data Protection

- KMS: key policies, grants, rotation
- Encryption at-rest & in-transit: S3, EBS, RDS, DynamoDB
- Secrets Manager & Parameter Store — rotation patterns
- Data classification & access guardrails

#### Incident Response

- AWS Incident Response lifecycle
- Playbooks:
  - Compromised IAM key
  - S3 public exposure
  - EC2 malware detection
- Snapshot, isolation, evidence collection
- Auto-response using Lambda/Step Functions

### Key Takeaways

#### How to protect a personal AWS account

- Apply the Least Privilege Principle for IAM Users
- Do not use the root account for daily tasks
- Avoid using "*" in policy actions/resources
- Enable MFA for enhanced security

#### Overview of GuardDuty

- An intelligent threat detection service that continuously monitors for malicious activities in AWS accounts and workloads.
- GuardDuty analyzes data from multiple sources including: AWS CloudTrail logs, VPC Flow Logs, DNS logs (Route 53 Resolver DNS query logs).
- Can use CloudFormation to build EventBridge rules that trigger Lambda functions and send SNS notifications when GuardDuty detects threats.

#### Principles for building secure systems

- How to use Security Groups and NACLs to defend against external and internal threats.
- Understand common attack vectors to react and mitigate in time.
- Use services like AWS WAF, AWS Network Firewall, and AWS Shield Advanced (for DDoS protection) to strengthen system security both externally and internally.

#### Using KMS to encrypt and protect data

- Create and manage encryption keys
- Control who can use or manage these keys
- Monitor all KMS-related activities with CloudTrail

### Event Experience

Participating in the **“AWS CLOUD MASTERY SERIES #3 – Security on AWS”** workshop was a highly valuable experience, giving me a broader understanding of how to protect accounts and systems from basic to advanced levels. Some notable experiences:

#### Learning from highly experienced speakers

- Speakers from the First Cloud Journey team and major tech organizations shared best practices for designing secure applications and preventing unwanted attacks.
- Through real-world case studies, I gained deeper insight into applying Cloud Security in large-scale projects.

#### Networking and discussions

- The workshop provided opportunities to directly interact with mentors and peers from the First Cloud Journey program.

#### Key takeaways

- When building a system, aside from infrastructure and architecture, it is crucial to focus on security from the smallest steps to prevent external threats and internal vulnerabilities.
- Tools like Amazon GuardDuty can be applied to optimize workload, reduce manual effort, and increase automation in existing systems.

![image](/images/event2.jpg)