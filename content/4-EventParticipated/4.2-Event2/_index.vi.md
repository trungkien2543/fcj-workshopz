+++
title = "Sự kiện 2"
type = ""
weight = 2
pre = " <b> 4.2. </b> "
+++

# Bài thu hoạch “AWS CLOUD MASTERY SERIES #3 – Security on AWS”

### Mục Đích Của Sự Kiện

- Là buổi chia sẻ chuyên sâu giúp nắm vững 5 trụ cột bảo mật theo AWS Well-Architected Security Pillar và cách bảo vệ hệ thống cloud trước các mối đe dọa ngày càng tăng.
- Chia sẻ về các dịch vụ hỗ trợ bảo mật và rà soát hệ thống giúp bảo vệ tài khoản aws và hệ thống tránh các cuộc tấn công.
- Cơ hội kết nối với cộng đồng First Cloud Journey


### Danh sách các diễn giả

- **Le Vu Xuan An**: AWS Cloud Club Captian HCMUTE, First Cloud AI Journey
- **Tran Duc Anh**: AWS Cloud Club Captian SGU, First Cloud AI Journey, Cloud Security Engineer Trainee
- **Tran Doan Cong Ly**: AWS Cloud Club Captian PTIT, First Cloud AI Journey
- **Dang Hoang Hieu Nghi**: AWS Cloud Club Captian HUFLIT, First Cloud AI Journey
- **Nguyen Tuan Thinh**: First Cloud AI Journey, Cloud Engineer Trainee
- **Huynh Hoang Long**: First Cloud AI Journey, Cloud Engineer Trainee
- **Dinh Le Hoang Anh**: First Cloud AI Journey, Cloud Engineer Trainee
- **Nguyen Do Thanh Dat**: First Cloud AI Journey, Cloud Engineer Trainee
- **Van Hoang Kha**: First Cloud AI Journey, Cloud Engineer Trainee
- **Thinh Lam**: First Cloud AI Journey
- **Viet Nguyen**: First Cloud AI Journey
- **Mendel Grabski (Long)**: ex Head of Security & DevOps, Cloud Security Solutions Architect
- **Tinh Truong**: AWS Community Builder, Platform Engineer at TymeX

### Nội Dung Nổi Bật

#### Dịch vụ Identity & Access Management

- Hiểu được dịch vụ IAM được sử dụng để làm gì
- Các best practice cho việc sử dụng IAM cho hệ thống
- SCP và permission boundaries cho multi-account
- MFA, credential rotation, Access Analyzer

#### Detection & Continuous Monitoring

-​ CloudTrail (org-level), GuardDuty, Security Hub
​- Logging tại mọi tầng: VPC Flow Logs, ALB/S3 logs
​- Alerting & automation với EventBridge
- ​Detection-as-Code (infrastructure + rules)

#### Infrastructure Protection

- ​VPC segmentation, private vs public placement
- ​Security Groups vs NACLs: mô hình áp dụng
- WAF + Shield + Network Firewall
​- Workload protection: EC2, ECS/EKS security basics

#### Data Protection

- ​KMS: key policies, grants, rotation
​- Encryption at-rest & in-transit: S3, EBS, RDS, DynamoDB
​- Secrets Manager & Parameter Store — patterns rotation
​- Data classification & access guardrails

#### Incident Response

- ​IR lifecycle theo AWS
- ​Playbook:
  - ​compromised IAM key
  - ​S3 public exposure
  - ​EC2 malware detection
​- Snapshot, isolation, evidence collection
- ​Auto-response bằng Lambda/Step Functions

### Những gì học được

#### Cách để bảo vệ tài khoản AWS cá nhân

- Sử dụng nguyên tắc phân quyền tối thiểu Least privilege principle cho các tài khoản IAM User
- Không sử dụng tài khoản root cho các công việc thường ngày
- Tránh sử dụng "*" trong phần policy editor cho các actions/resources
- Tích hợp MFA cho tài khoản để tăng cường bảo mật

#### Tổng quan về dịch vụ GruardDuty

- Là một dịch vụ phát hiện mối đe dọa thông minh hoạt động liên tục để giám sát hoạt động độc hại trong tài khoản AWS và workload.
- GuardDuty phân tích dữ liệu từ nhiều nguồn, bao gồm: AWS CloudTrail logs, VPC Flow Logs, DNS logs (Route 53 Resolver DNS query logs)
- Có thể sử dụng CloudFormation để xây dựng EventBrigde để có thể thực hiện các hàm lambda và gửi SNS để người dùng khi mà GuardDuty phát hiện ra mối đe dọa

#### Nguyên tắc xây dựng hệ thống an toàn và bảo mật

- Cách sử dụng Security Group và NACL để chống lại các mối đe dọa từ bên ngoài và bên trong hệ thống
- Biết được các cách tấn công vào hệ thống để có thể kịp thời ngăn chặn
- Sử dụng các dịch vụ AWS WAF, AWS Network Firewall và AWS Shield Advanced (chống các cuộc tấn công DDoS)để tăng cường bảo vệ hệ thống từ bên ngoài và cả bên trong

#### Sử dụng KMS để mã hóa và bảo vệ dữ liệu

- Tạo và quản lý khóa mã hóa
- Kiểm soát ai có thể sử dụng hoặc quản lý các khóa đó
- Sử dụng CloudTrail để giám sát các hoạt động đối với KMS

### Trải nghiệm trong event

Tham gia workshop **“AWS CLOUD MASTERY SERIES #3 – Security on AWS”** là một trải nghiệm rất bổ ích, giúp em có cái nhìn bao quát hơn về công việc bảo vệ tài khoản và hệ thống từ đơn giản đến nâng cao. Một số trải nghiệm nổi bật:

#### Học hỏi từ các diễn giả có chuyên môn cao

- Các diễn giả đến từ team First Cloud Journey và các tổ chức công nghệ lớn đã chia sẻ **best practices** trong thiết kế ứng dụng an toàn và tránh các cuộc tấn công không mong muốn.
- Qua các case study thực tế, em hiểu rõ hơn cách áp dụng **Cloud Security** vào các project lớn.

#### Kết nối và trao đổi

- Workshop tạo cơ hội trao đổi trực tiếp với các anh/chị memtor, các bạn đang tham gia chương trình First Cloud Journey.

#### Bài học rút ra

- Khi xây dựng hệ thống ngoài việc xây dựng cấu trúc hệ thống, hạ tầng mà còn cần phải quan tâm tới việc bảo mật từ những bước nhỏ nhất để phòng tránh những mối đe dọa từ bên ngoài và những lỗi bảo mật từ bên trong hệ thống.
- Các công cụ như Amazon GuardDuty có thể được áp dụng để tối ưu hóa công sức làm việc và tăng tính tự động hóa các hệ thống hiện tại.