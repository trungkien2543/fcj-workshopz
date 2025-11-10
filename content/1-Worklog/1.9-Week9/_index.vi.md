+++
title = "Tuần 9"
weight = 9
pre = "<b> 1.9. </b>"
+++

### Mục tiêu tuần 9:

- Tích hợp WebSocket vào module Notification

- Thực hành bài lab có liên quan tới Serverless Computing

- Hoàn thành xong giao diện và call api cho phần Task UpComing

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                           |
| --- | ----------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | -------------------------------------------------------- |
| 2   | - Tích hợp WebSocket vào dự án và chạy demo                                                                  | 3/11/2025   | 3/11/2025      |  |  
| 3   | - Làm giao diện cho phần Task UpComing bao gồm hiển thị danh sách, lọc theo ngày, tạo task                                                       | 4/11/2025   | 4/11/2025      |  |
| 4   | - **Thực hành:** lab 78: Serverless - Tương tác giữa Lambda với S3 và DynamoDB |  5/11/2025   | 5/11/2025      | <https://000078.awsstudygroup.com/vi/> |
| 5   | - Hoàn thành việc tạo, sửa, xóa task trong phần Task UpComing <br> - Thiết kế giao diện cho phần comment của từng task  | 6/11/2025   | 6/11/2025      | |
| 6   | - Hoàn việc tích hợp api cho phần Task UpComing | 7/11/2025   | 7/11/2025      |  |

### Kết quả đạt được tuần 9:

- Hoàn thành phần Task UpComming:
  - Có thể lấy ra danh sách các task sắp đến thời gian thực hiện
  - Có thể tạo mới, xóa và chỉnh sửa task (bao gồm cập nhật trạng thái, cập nhật ngày bắt đầu mới, ...)
  - Lấy ra danh sách các comment của task, thêm comment mới, chỉnh sửa và xóa comment

- Thực hành bài đầu tiên trong series serverless - Tương tác giữa Lambda với S3 và DynamoDB:
  - Tạo Lambda function với Node.js 18.x runtime cho việc xử Lý ảnh
  - Sử dụng Lambda functiond để ghi dữ liệu vào DynamoDB
