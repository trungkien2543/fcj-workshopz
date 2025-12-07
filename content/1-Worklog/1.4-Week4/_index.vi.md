+++
title = "Tuần 4"
weight = 4
pre = "<b> 1.4. </b>"
+++

### Mục tiêu tuần 4:

* Tìm hiểu về Dịch vụ lưu trữ Amazon S3
* Tìm hiểu về Dịch vụ bảo mật trên AWS
* Thực hiện triển khai được ứng dụng static web thông qua S3 và cloudfront

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc| Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Xem video bài giảng về dịch vụ lưu trữ S3 <br> - Dịch blogs cho tuần 4 <br> - Họp nhóm cập nhật công việc hàng tuần | 29/9/2025 | 29/9/2025 | [link bài viết gốc](https://aws.amazon.com/blogs/migration-and-modernization/scaling-clouderas-development-environment-leveraging-amazon-eks-karpenter-bottlerocket-and-cilium-for-hybrid-cloud/)
| 3   | - **Thực hành:** <br>+ lab57:  Khởi Đầu Với Amazon S3 <br> + lab13: Triển khai AWS Backup cho hệ thống <br> + lab 14: VM Import/Export <br> <br>- Nghiên cứu và lập danh sách các API cho taskflow service | 30/9/2025 | 30/9/2025 | <https://000057.awsstudygroup.com> <https://000014.awsstudygroup.com> <https://000013.awsstudygroup.com>
| 4   | - Xem video bài giảng về dịch vụ bảo mật trên AWS| 1/10/2025 | 1/10/2025 | 
| 5   | - **Thực hành:** <br>+ lab2:  Quản trị quyền truy cập với AWS Identity and Access Management (IAM) <br> + lab44: IAM Role & Condition <br> + lab 48: Cấp quyền cho ứng dụng truy cập dịch vụ AWS với IAM Role | 2/10/2025 | 2/10/2025 | <https://000048.awsstudygroup.com/vi/> <https://000002.awsstudygroup.com/vi/> <https://000044.awsstudygroup.com/vi/>
| 6   | - **Thực hành:** <br>+ lab30:  Giới hạn quyền truy cập với IAM Permission Boundary <br> - Cập nhật worklog | 3/10/2025 | 3/10/2025 | <https://000030.awsstudygroup.com/vi/>
### Kết quả đạt được tuần 4:

* Hiểu được về dịch vụ lưu trữ trên S3

* Thực hành hosting một website tĩnh bằng Amazon S3:
  * Biết cách khởi tạo một S3 và tải dữ liệu của website tĩnh lên đó
  * Thực hành cấu hình AWS CloudFront để host một website tĩnh trên S3 mà không cần public thông tin về bucket
  * Tìm hiểu về chức năng Bucket Versioning để bảo toàn và khôi phục phiên bản của mọi đối tượng được lưu trong bucket
  * Tìm hiểu chức năng Amzon S3 Cross-Region Replication (CRR) để tự động sao chép các đối tượng qua các vùng AWS khác nhau

* Thực hành triển khai backup cho hệ thống, hiểu về hai khái niệm chính là RPO và RTO

* Biết cách thực hiện import máy chủ ảo từ vm ware vào máy chủ EC2 và ngược lại

* Thực hành quản trị quyền truy cập vào hệ thống với AWS IAM:
  * Biết cách tạo ra IAM User có quyền với S3 và dùng access key đã tạo cho user để upload file từ máy chủ lên S3
  * Biết cách tạo IAM Role để gán quyền cho EC2 có thể gửi file lên S3 mà không cần dùng access key để tránh bị lộ thông tin 

* Thực hành sử dụng IAM Permissions Boundary để giới hạn quyền của người dùng, từ đó đơn giản hóa việc quản lý quyền và tránh cấp phát các quyền không cần thiết.

* Nghiên cứu và lập danh sách các API cần cho taskflow service
