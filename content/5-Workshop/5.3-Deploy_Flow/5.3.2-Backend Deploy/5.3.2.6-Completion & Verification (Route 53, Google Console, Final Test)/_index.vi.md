+++
title =  "Hoàn thiện & Xác nhận (Route 53, Google Console, Final Test)"
weight = 6
chapter = false
pre = " <b> 5.3.2.6. </b> "
alwaysopen = true
+++

Giai đoạn cuối này kết nối backend đã triển khai với domain public và kiểm tra toàn bộ luồng hoạt động end-to-end.

### DNS Configuration

### **Bước 1: Tạo bản ghi A trong Route 53**

1. Truy cập **Route 53** → **Hosted zones**
2. Chọn hosted zone `sgutodolist.com`
3. Nhấn **Create record**
4. Cấu hình bản ghi:
   - **Record name**: `api`
   - **Record type**: A
   - **Alias**: Enable
   - **Route traffic to**: Alias to Application Load Balancer
   - **Region**: Asia Pacific (Singapore)
   - **Load balancer**: chọn `sgu-alb`
5. Nhấn **Create records**

**DNS Propagation:** Chờ 2–5 phút để DNS cập nhật.

**Kiểm tra:**

```bash
nslookup sgutodolist.com
# Kết quả phải trả về IP của ALB
```

* * * * *

### Cập nhật cấu hình Google OAuth

**Bước 1: Cập nhật Authorized Redirect URIs**

1. Truy cập **Google Cloud Console** (console.cloud.google.com)  
2. Điều hướng đến **APIs & Services** → **Credentials**  
3. Chọn OAuth 2.0 Client ID của bạn  
4. Trong mục **Authorized redirect URIs**, thêm vào:

```
   https://sgutodolist.com/api/auth/login/oauth2/code/google
```


5. Nhấn **Save** để lưu lại

**Lưu ý:** Giữ nguyên các localhost URIs để phục vụ cho môi trường phát triển local.


* * * * *

### Xác minh tình trạng hệ thống

Thực hiện các kiểm tra sau để xác nhận triển khai thành công:

#### Test 1: API Gateway Health Check

bash


```
curl https://sgutodolist.com/actuator/health
```


**Kết quả mong đợi:**

json


```
{
  "status": "UP"
}
```

#### Test 2: Kiểm tra từng dịch vụ riêng lẻ

bash



```
# Dịch vụ Auth
curl https://sgutodolist.com/api/auth/actuator/health

# Dịch vụ User
curl https://sgutodolist.com/api/user/actuator/health

# Dịch vụ Taskflow
curl https://sgutodolist.com/api/taskflow/actuator/health

# Dịch vụ Notification
curl https://sgutodolist.com/api/notification/actuator/health
```

Tất cả phải trả về `{"status":"UP"}`.

#### Test 3: Xác minh Service Discovery

Từ Bastion Host, kiểm tra phân giải DNS nội bộ:

bash


```
# SSH tới Bastion
ssh -i sgutodolist-key.pem ec2-user@[BASTION-IP]

# Kiểm tra phân giải DNS
nslookup auth.sgu.local
nslookup user.sgu.local
nslookup taskflow.sgu.local
nslookup notification.sgu.local
nslookup ai-model.sgu.local
nslookup kafka.sgu.local
```


Tất cả phải phân giải ra địa chỉ IP nội bộ của ECS task.

#### Test 4: Kết nối Database

Xác minh các dịch vụ có thể kết nối tới RDS:

1. Kiểm tra CloudWatch Logs cho bất kỳ dịch vụ nào
2. Tìm thông báo kết nối cơ sở dữ liệu thành công
3. Xác nhận không có lỗi kết nối trong logs khởi động

#### Test 5: Kết nối Redis

bash


```
# Từ Bastion Host
redis-cli -h [REDIS-ENDPOINT] ping
# Kết quả mong đợi: PONG
```


#### Test 6: Luồng xác thực end-to-end

1. Truy cập frontend tại `https://sgutodolist.com`
2. Nhấn "Sign in with Google"
3. Hoàn thành luồng OAuth
4. Xác minh đăng nhập thành công và token được cấp
5. Xác minh thông tin profile người dùng hiển thị đúng

* * * * *

### Cơ sở đo lường hiệu năng

Ghi nhận các chỉ số hiệu năng ban đầu:

**Benchmark thời gian phản hồi:**

bash

```
# Thời gian phản hồi API Gateway
time curl -o /dev/null -s https://sgutodolist.com/actuator/health

# Thời gian phản hồi dịch vụ Auth
time curl -o /dev/null -s https://sgutodolist.com/api/auth/actuator/health
```


**Chỉ số CloudWatch cần theo dõi:**

- Sử dụng CPU ECS Task
- Sử dụng Memory ECS Task
- Thời gian phản hồi ALB Target
- Số lượng request ALB
- Sử dụng CPU RDS
- Sử dụng CPU Redis

* * * * *

### Danh sách kiểm tra bảo mật sau triển khai

- [ ] Bảo mật tất cả biến môi trường nhạy cảm (không lưu trong version control)
- [ ] Mật khẩu database đáp ứng yêu cầu độ phức tạp
- [ ] Security groups tuân thủ nguyên tắc least privilege
- [ ] Chứng chỉ SSL/TLS hợp lệ và tự động gia hạn
- [ ] Bastion Host chỉ truy cập từ IP được phép
- [ ] Cấu hình lưu giữ logs CloudWatch
- [ ] Cảnh báo ngân sách AWS được kích hoạt

* * * * *

### Danh sách kiểm tra triển khai cuối cùng

**Hạ tầng:**

- [ ] VPC và subnets hoạt động
- [ ] 4 security groups được cấu hình đúng
- [ ] Database RDS truy cập được và đã khởi tạo
- [ ] Redis cache hoạt động
- [ ] Dịch vụ Kafka chạy
- [ ] ALB hoạt động với các target healthy

**Ứng dụng:**

- [ ] 6 dịch vụ triển khai và chạy
- [ ] Service Discovery hoạt động
- [ ] Quy tắc routing ALB hoạt động đúng
- [ ] Health checks vượt qua
- [ ] CloudWatch Logs thu thập dữ liệu

**Tích hợp:**

- [ ] Record DNS trỏ tới ALB
- [ ] Chứng chỉ SSL hợp lệ
- [ ] Google OAuth cấu hình đúng
- [ ] Frontend có thể giao tiếp với backend
- [ ] Luồng xác thực hoạt động

**Giám sát:**

- [ ] Dashboard CloudWatch tạo xong
- [ ] Cảnh báo ngân sách cấu hình xong
- [ ] Cơ sở đo lường hiệu năng ghi nhận xong

* * * * *

### Hạn chế hiện tại và cải tiến tương lai

**Hạn chế kiến trúc hiện tại:**

1. **Database Single-AZ**: RDS triển khai trong một AZ duy nhất để tối ưu chi phí
2. **Redis Single-Node**: Không có cơ chế failover tự động
3. **Kafka Single-Node**: Không đủ chuẩn sản xuất cho throughput cao
4. **ECS Tasks trên Public Subnet**: Trade-off bảo mật để tiết kiệm chi phí

**Cải tiến khuyến nghị cho môi trường production:**

1. Kích hoạt RDS Multi-AZ
2. Triển khai Redis Cluster Mode với nhiều replica
3. Triển khai Kafka với nhiều broker trên các AZ
4. Thêm NAT Gateway và di chuyển ECS tasks sang private subnet
5. Triển khai AWS WAF trên ALB để chống DDoS
6. Kích hoạt ECS Service Auto Scaling
7. Thiết lập pipeline CI/CD cho triển khai tự động

* * * * *

### Hướng dẫn khắc phục sự cố

#### Lệnh chẩn đoán nhanh

bash


```
# Kiểm tra trạng thái ECS service
aws ecs describe-services --cluster [CLUSTER-NAME] --services [SERVICE-NAME] --region ap-southeast-1

# Kiểm tra trạng thái task
aws ecs describe-tasks --cluster [CLUSTER-NAME] --tasks [TASK-ARN] --region ap-southeast-1

# Xem logs gần đây
aws logs tail /ecs/[SERVICE-NAME] --follow --region ap-southeast-1

# Kiểm tra trạng thái target
aws elbv2 describe-target-health --target-group-arn [TG-ARN] --region ap-southeast-1
```


#### Quy trình rollback khẩn cấp

Nếu triển khai thất bại:

1. **Xác định dịch vụ gặp lỗi:**

bash


```
   aws ecs list-services --cluster [CLUSTER-NAME] --region ap-southeast-1
```


2. **Cập nhật dịch vụ về revision task definition trước đó:**

bash


```
   aws ecs update-service\
     --cluster [CLUSTER-NAME]\
     --service [SERVICE-NAME]\
     --task-definition [TASK-DEF-FAMILY]:[PREVIOUS-REVISION]\
     --region ap-southeast-1
```


3. **Force triển khai mới:**

bash


```
   aws ecs update-service\
     --cluster [CLUSTER-NAME]\
     --service [SERVICE-NAME]\
     --force-new-deployment\
     --region ap-southeast-1
```

* * * * *

### Tiêu chí thành công
----------------

Triển khai được coi là thành công khi:

1. ✅ 6 dịch vụ ECS hiển thị trạng thái: RUNNING
2. ✅ 5 target groups hiển thị: Healthy
3. ✅ `https://sgutodolist.com/actuator/health` trả HTTP 200
4. ✅ Frontend tại `https://sgutodolist.com` có thể xác thực Google OAuth
5. ✅ CloudWatch Logs không có lỗi nghiêm trọng
6. ✅ Tất cả dịch vụ có thể truy cập qua DNS nội bộ (*.sgu.local)

* * * * *

<div style="display: flex; justify-content: space-between; align-items: center; margin-top: 20px;">
<a href="{{% relref "5-Workshop/5.3-Deploy_Flow/5.3.2-Backend Deploy/5.3.2.5-Services Deployment" %}}" style="text-decoration: none; font-weight: bold;">
⬅ BƯỚC 5: Code Update & Image Build
</a>
<a href="" style="text-decoration: none; font-weight: bold;">
</a>
</div>