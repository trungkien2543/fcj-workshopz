+++
title = "Clean Up"
weight = 4
chapter = false
pre = " <b> 5.4.  </b> "
+++

Hướng Dẫn Dọn Dẹp
-----------------------------------------------------------

Hướng dẫn này cung cấp chỉ dẫn chi tiết để **hoàn toàn dọn dẹp tất cả tài nguyên AWS đã triển khai** ở hai khu vực **ap-southeast-1 (Singapore)** và **us-east-1 (N. Virginia)**. Trình tự thực hiện được tối ưu để tránh tài nguyên bị bỏ sót và các chi phí phát sinh không mong muốn.

### **I. Tóm Tắt Dịch Vụ AWS Đang Triển Khai**

Trước khi thực hiện, hãy kiểm tra sự tồn tại của các dịch vụ sau trong tài khoản của bạn:

| Loại Dịch Vụ | Tên Dịch Vụ/Tài Nguyên | Khu Vực | Mục Đích |
| --- | --- | --- | --- |
| **Compute/Scaling** | ECS Cluster/ASG, EC2 Instances | ap-southeast-1 | Hosting ứng dụng |
| **Database/Caching** | RDS DB Instance (Multi-AZ), ElastiCache Redis Cluster | ap-southeast-1 | Lưu trữ dữ liệu & Quản lý session |
| **Networking** | Application Load Balancer (ALB), Target Groups, NAT Gateway, VPC | ap-southeast-1 | Phân phối traffic & Truy cập Internet |
| **Storage/Artifacts** | S3 Primary Bucket, S3 DR Bucket, ECR Repositories | ap-southeast-1, us-east-1 | Lưu trữ file & Hosting image |
| **Global/DNS/Security** | CloudFront Distribution, ACM Certificates, Route 53 Hosted Zone | Global (Edge), us-east-1, ap-southeast-1 | CDN & Bảo mật SSL |

* * * * *

### **II. Quy Trình Dọn Dẹp (Theo Bước)**

#### **1\. Dọn Dẹp Lớp Ứng Dụng (ap-southeast-1)**

Bắt đầu bằng việc loại bỏ các tài nguyên hosting ứng dụng và kết nối tới database.

1.  **Dừng ECS/EC2 Compute:**

    -   **Nếu sử dụng ECS:** 
        -   Vào **Amazon ECS** → **Clusters**. 
        -   Chọn Cluster → Tab **Services**. 
        -   Chọn tất cả Services → Click **Update**. 
        -   Đặt **Desired tasks** thành **0** → **Next** → **Update Service**. 
        -   Chờ tasks dừng. Sau đó, chọn Services → **Delete** → Xác nhận bằng `delete me`.

2.  **Xóa Load Balancer (ALB) Components:**

    -   Vào **EC2** → **Target Groups**. Chọn Target Groups → Click **Actions** → **Delete**.

    -   Vào **EC2** → **Load Balancers**. Chọn ALB → Click **Actions** → **Delete**.

#### **2\. Xóa Database và Dịch Vụ Caching**

1.  **Xóa ElastiCache (Redis):**

    -   Vào **ElastiCache** → **Redis Clusters**. Chọn Cluster → Click **Actions** → **Delete**.

2.  **Xóa RDS Database (BƯỚC QUAN TRỌNG CHI PHÍ):**

    -   Vào **RDS** → **Databases**. Chọn Instance.

    -   Nếu **Deletion protection** đang *Enabled*, click **Modify** → Cuộn xuống *Deletion protection* → Bỏ chọn → **Continue** → **Apply immediately**.

    -   Chọn Instance → Click **Actions** → **Delete**.

    -   **Bỏ chọn** **Create final snapshot** → Nhập tên DB để xác nhận → Click **Delete**.

#### **3\. Dọn Dẹp Dịch Vụ Global và Chứng Chỉ**

Các bước này liên quan tới tài nguyên ở nhiều khu vực.

1.  **Xóa CloudFront Distribution:**

    -   Vào **CloudFront**. Chọn Distribution → Click **Disable**. (Chờ 10-15 phút để trạng thái thay đổi).

    -   Khi Disabled, chọn Distribution → Click **Delete**.

2.  **Xóa ACM Certificates (Thực hiện ở cả hai khu vực):**

    -   **us-east-1 (N. Virginia):** Chuyển Region → Vào **ACM**. Chọn Certificate → Click **Delete**.

    -   **ap-southeast-1 (Singapore):** Chuyển Region → Vào **ACM**. Chọn Certificate → Click **Delete**.

#### **4\. Dọn Dẹp Storage và Artifacts**

1.  **Xóa ECR Repositories:**

    -   Vào **Elastic Container Registry (ECR)**. Chọn Repositories → Click **Delete** → Nhập `delete` để xác nhận.

2.  **Dọn dẹp và Xóa S3 Buckets:**

    -   **Gỡ CRR:** Vào **S3** → Chọn Primary Bucket (`ap-southeast-1`) → Tab **Management** → **Replication Rules** → Xóa rule.

    -   **Empty Bucket:** Với cả hai S3 Buckets (ap-southeast-1 và us-east-1), vào Tab **Objects** → Chọn tất cả → Click **Delete** → Nhập `permanently delete` để xác nhận.

    -   **Xóa Bucket:** Sau khi empty, vào Tab **Properties** → Click **Delete** → Nhập tên Bucket để xác nhận.

#### **5\. Xóa Networking và DNS**

1.  **Xóa NAT Gateway (BƯỚC QUAN TRỌNG CHI PHÍ):**

    -   Vào **VPC** → **NAT Gateways**. Chọn Gateway → Click **Actions** → **Delete NAT Gateway**.

2.  **Xóa Internet Gateway (IGW):**

    -   Vào **VPC** → **Internet Gateways**. Chọn IGW → Click **Actions** → **Detach from VPC**.

    -   Chọn IGW → Click **Actions** → **Delete Internet Gateway**.

3.  **Xóa các thành phần VPC:**

    -   Xóa **Security Groups** → Xóa **Subnets** → Xóa **VPC**.

4.  **Xóa Route 53 Hosted Zone:**

    -   Vào **Route 53** → **Hosted Zones**. Xóa tất cả custom records → Click **Delete Hosted Zone**.

* * * * *

### **III. Kiểm Tra Cuối Cùng**

-   **AWS Billing/Cost Explorer:** Ngay lập tức kiểm tra để đảm bảo chi phí ước tính hàng giờ giảm xuống **$0.00**.

-   **Dashboards:** Kiểm tra bảng điều khiển EC2, ECS, và RDS ở **ap-southeast-1** để đảm bảo không còn tài nguyên đang chạy.
