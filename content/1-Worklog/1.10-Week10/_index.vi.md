+++
title = "Tuần 10"
weight = 10
pre = "<b> 1.10. </b>"
+++

### Mục tiêu tuần 10:

- Hoàn thành xong phần giao diện cho comment trong các task trong dự án

- Hoàn thành xong giao diện notifications có thể thông báo realtime

- Xây dựng các API cho việc lưu trữ dữ liệu lên S3 của AWS

- Xây dựng API cho chức năng comment attach của phần comment trong task


### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                               | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                           |
| --- | ----------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | -------------------------------------------------------- |
| 2   | - Cài đặt WebSocket cho frontend và tạo kết nối với phía server <br> - Tạo ModalNotification, ItemNotification cho giao diện hiển thị thông báo                                                                  | 10/11/2025   | 10/11/2025      |  |  
| 3   | - Xây dựng giao diện cho chức năng comment trong phần xem chi tiết task <br> - Tích hợp API vào giao diện comment                                                        | 11/11/2025   | 11/11/2025      |  |
| 4   | - Xây dựng API cho chức năng upload file lên S3 của AWS  |  12/11/2025   | 12/11/2025      |  |
| 5   | - Xây dựng API cho phép người dùng thực hiện đính kèm các file được lưu trên S3 vào comment  | 13/11/2025   | 13/11/2025      | |
| 6   | - Hoàn tất giao diện notification | 14/11/2025   | 14/11/2025      |  |

### Kết quả đạt được tuần 10:

- Hoàn thành được giao diện comment trong phần chi tiết task:
  - Người dùng có thể thêm, sửa, xóa comment của bản thân
  - Có thể xem được tất cả comment của người khác trong task

- Hoàn thành xong giao diện notifications:
  - Người dùng có thể xem toàn bộ thông báo hoặc chỉ xem những thông báo chưa được đánh dấu đã xem
  - Người dùng cũng có thể thực hiện thao tác chấp nhận/từ chối tham gia dự án tại trang này
  - Có thể thực hiện đánh dấu đã xem từng cái hoặc đánh dấu toàn bộ

- Xây dụng API cho việc upload file lên S3 và comment attach:
  - Người dùng có thể upload file lên S3
  - Cho phép người dùng có thể dùng file đã upload lên S3 để đính kèm vào comment

