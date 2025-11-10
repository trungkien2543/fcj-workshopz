+++
title = "Tuần 8"
weight = 8
pre = "<b> 1.8. </b>"
+++

### Mục tiêu tuần 8:

- Dịch bài blogs còn lại 

- Thực hành một số bài hỗ trợ cho việc tối ưu hệ thống

- Tìm các tối ưu ứng dụng cho dự án cuối khóa

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                           |
| --- | ----------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | -------------------------------------------------------- |
| 2   | - Dịch bài blogs 8: Kích hoạt phân tích dữ liệu Genomic và Multiomic nhanh chóng với Illumina DRAGEN™ v4.4 trên các instance Amazon EC2 F2                                                                  | 27/10/2025   | 27/10/2025      | [Blog 8](../../3-BlogsTranslated/3.7-Blog7/_index.vi.md) |  
| 3   | - Chỉnh sửa lại cấu trúc cho các worklogs <br> - Xem lại nội dung lý thuyết của một số dịch vụ trên AWS <br> - Tìm hiểu về Desgin Pattern để ứng dụng cho dự án cuối khóa như: Proxy Pattern, Prototype Pattern, Builder Pattern                                                       | 28/10/2025   | 28/10/2025      | <https://viblo.asia/p/proxy-design-pattern-tro-thu-dac-luc-cua-developers-RQqKLB2bl7z> <https://viblo.asia/p/prototype-design-pattern-tro-thu-dac-luc-cua-developers-GrLZDBQO5k0>                  |
| 4   | - **Thực hành:** lab 22: Tối ưu chi phí EC2 với Lambda | 29/10/2025   | 29/10/2025      | <https://000022.awsstudygroup.com/vi/> |
| 5   | - **Thực hành:** lab 29: Bắt đầu với Grafana trên AWS  | 30/10/2025   | 30/10/2025      | <https://000029.awsstudygroup.com/vi/> |
| 6   | - Tìm hiểu về WebSocket | 31/10/2025   | 31/10/2025      | <https://www.geeksforgeeks.org/web-tech/what-is-web-socket-and-how-it-is-different-from-the-http/> |

### Kết quả đạt được tuần 8:

- Tìm hiểu về 1 số design pattern hữu ích để tích hợp vào đồ án cuối khóa:
  - Proxy Pattern: Giúp tăng tính bảo mật bằng việc xây dựng 1 Api Gateway cho hệ thống để không bị lộ endpoint của các module khác
  - Builder Pattern: Giúp việc tạo các đối tượng trong hệ thống được đơn giản hơn bằng việc xây dựng từng bước

- Thực hành sử dụng AWS Lambda để tối ưu hệ thống:
  - Biết cách tạo ra các Lambda Function để có thể bật tắt các instance tự động
  - Biết sử dụng biến môi trường cho các hàm trong lambda
  - Sử dụng CloudWatch để lên lịch tự động thực hiện Lambda Function

- Thực hành sử dụng Grafana để theo dõi các tài nguyên trên AWS

- Hiểu về web socket để có thể bắt đầu tích hợp vào dự án. Để làm thông báo realtime cho người mà không cần reload lại trang để xem thông báo, hỗ trợ người dùng được cập thông tin kịp thời
