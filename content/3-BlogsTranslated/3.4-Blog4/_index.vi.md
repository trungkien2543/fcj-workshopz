+++
title = " Blog 4"
type = ""
weight = 4
pre = "<b> 3.4. </b>"
+++

# **Mở rộng môi trường phát triển của Cloudera: Tận dụng Amazon EKS, Karpenter, Bottlerocket và Cilium cho mô hình Hybrid Cloud**

*Harpreet Virk and Padma Iyer* | **26/09/2025** | in [Advanced (300)](https://aws.amazon.com/blogs/migration-and-modernization/category/learning-levels/advanced-300/), [Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/migration-and-modernization/category/compute/amazon-kubernetes-service/), [Best Practices](https://aws.amazon.com/blogs/migration-and-modernization/category/post-types/best-practices/), [Containers](https://aws.amazon.com/blogs/migration-and-modernization/category/containers/), [Graviton](https://aws.amazon.com/blogs/migration-and-modernization/category/compute/graviton/), [Migration](https://aws.amazon.com/blogs/migration-and-modernization/category/enterprise-strategy/migration-enterprise-strategy/), [Migration Acceleration Program (MAP)](https://aws.amazon.com/blogs/migration-and-modernization/category/migration-solutions/map/) | [Permalink](https://aws.amazon.com/blogs/migration-and-modernization/scaling-clouderas-development-environment-leveraging-amazon-eks-karpenter-bottlerocket-and-cilium-for-hybrid-cloud/) |

Bài viết này được viết cùng với Shreelola Hegde,Sriharsha Devineni và Lee Watterworth đến từ Cloudera

[Cloudera](https://www.cloudera.com/) là một công ty hàng đầu thế giới về quản trị dữ liệu doanh nghiệp, phân tích và AI. Nền tảng Cloudera cho phép các tổ chức có thể quản lý, xử lý và phân tích các tập dữ liệu lớn, giúp các doanh nghiệp trong nhiều ngành công nghiệp như tài chính, chăm sóc sức khỏe, sản xuất, và viễn thông đẩy nhanh việc áp dụng AI/ML và mở khóa thông tin chi tiết theo thời gian thực.

Yếu tổ then chốt để đạt dược thành công của nền tảng là môi trường phát triển, nơi mà các nhà phát triển có thể xây dựng và kiểm thử tính năng mới để phát hành. Môi trường được xây dựng trên Kubernets, đã đối mặt với nhiều thách thức trong hệ thống on-premises, đặc biệt là trong khả năng mở rộng và tận dụng tài nguyên.

Bài viết đề cập đến cách Cloudera đã hiện đại hóa hoạt động phát triển của họ bằng việc áp dụng một hyrid cloud được xây dựng bởi [Amazon Elastic Kubernetes Service (Amazon EKS)](https://aws.amazon.com/eks/). Chiến lượt này cân bằng giữa dung lượng hiện có của môi trường on-premises với khả năng co giãn của cloud trong khi vẫn tận dụng các cải tiến như [Karpenter](https://docs.aws.amazon.com/eks/latest/best-practices/karpenter.html), [Bottlerocker](https://aws.amazon.com/bottlerocket/), và [Cilium](https://cilium.io/) để tối ưu việc mở rộng, bảo mật và hiệu quả về chi phí.

### Thách thức vận hành đối với On-Premises

- Môi trường phát triển gặp những thách thức khi chạy on-premises:
  - Môi trường yêu cầu phải mở rộng và thu hẹp thường xuyên nhưng bị hạn chế bởi dung lượng cố định
  - Quá trình xây dụng và kiểm thử cho dịch vụ như Apache Spark, Hive, và HBase yêu cầu container với dụng lượng lên đến 64GB RAM và 32 vCPu, nhanh chóng vượt quá nguyền tài nguyên có sẵn
  - Các yêu cầu pull trong các sprints lập trình cường độ cao tăng lên 300% dẫn tới thời gian xây dựng tăng thêm 45 phút, tạo ra các nút thắt trong CI/CD pipeline điều này làm chậm đáng kể tốc độ phát triển. Điều này, lần lượt, làm trì hoãn việc bàn giao tính năng, gây rủi ro cho lịch phát hành, đồng thời làm tăng chi phí hạ tầng và thời gian nhàn rỗi của lập trình viên.
  - Thiết kế ứng dụng cũng liên quan đến việc truy xuất và lưu trữ artifacts và datasets từ Amazon S3, điều này gây ra độ trễ cho các agent on-premises, làm kéo dài thêm các chu kỳ build và test.
  - Những hạn chế này đã tạo ra các nút thắt trong pipeline phát triển và nhấn mạnh nhu cầu về khả năng co giãn vượt ra ngoài các cụm on-premises.

### Giải pháp tổng quan

Cloudera đã giải quyết các thách thức bằng cách áp dụng mô hình hyrid cloud trong đó khối lượng công việc có thể dự đoán được đã được duy trì trong on-premises và khối lượng công việc động được đưa lên AWS. Kiến trúc này kết hợp sự co giãn của AWS với cơ sở hạ tầng on-premises đã được thiết lập của Cloudera, mang lại khả năng mở rộng liền mạch, giảm sự độ trễ và tối ưu hóa chi phí.

![image_architecture](https://d2908q01vomqb2.cloudfront.net/1f1362ea41d1bc65be321c0a378a20159f9a26d0/2025/09/24/AWS-3.jpg)

*Kiến trúc bật cao của môi trường phát triển trong AWS*

Kiến trúc Kubernets hiện đại cung cấp những lợi ích dưới đây:

- **Sự co giãn với Amazon EKS và Karpenter**: Cloudera, sử dụng Karpenter, để có thể mở rộng khối lượng công việc của họ từ một số node lên vài nghìn trong một vài phút và cũng có thể thu hẹp khi có nhu cầu cắt giảm. Điều này đảm bảo việc mở rộng hiểu quả trong các đợt tăng đột biến, đồng thời loại bỏ lãng phí tài nguyên khi khối lượng công việc thấp. Điều này cũng cho phép nhiều yêu cầu pull được chạy song song mà không cần đợi tài nguyên, giúp những nhà phát triển có thời gian phản hồi nhanh hơn và cải thiện tốc độ phát hành. Việc cấp phát thông minh đảm bảo rằng các instance tính toán luôn phù hợp với yêu cầu khối lượng công việc, từ đó tăng tỷ lệ sử dụng tài nguyên và giảm chi phí lên đến 40%.

- **Xử lý các container lớn với Karpenter**: Các công việc build và test yêu cầu các container lớn ngay lập tức được ghép với tài nguyên tính toán tối ưu thông qua cơ chế cấp phát node thông minh của Karpenter. Khả năng co giãn theo thời gian thực này đảm bảo không có trễ hay xung đột tài nguyên.

- **Tăng cường bảo mật với Bottlerocket**: Hệ điều hành dựa trên Linux này còn cải thiện quá trình mở rộng bằng cách cung cấp một môi trường tối ưu cho container với chi phí hệ điều hành tối thiểu. Hệ thống tệp bất biến tăng cường bảo mật bằng cách ngăn chặn các thay đổi trái phép, trong khi các cập nhật nguyên tử (atomic updates) giúp đơn giản hóa việc vá hệ thống và giảm thời gian bảo trì. Sự thay đổi này làm giảm bề mặt tấn công của môi trường phát triển đi 60%, tinh giản việc vá lỗi nhờ các cập nhật nguyên tử, và tăng hiệu quả sử dụng tài nguyên tính toán 35%.

- **Giảm thời gian build với Bottlerocket và [Amazon Elastic Block Store](https://aws.amazon.com/ebs/) snapshot Amazon EBS**: Thời gian khởi chạy pod giảm từ 30 phút xuống chỉ còn vài giây nhờ sử dụng Bottlerocket và snapshot Amazon EBS để tiền tải trước các hình ảnh lớn. Cải tiến này giúp lập trình viên bắt đầu các build mới gần như ngay lập tức, cải thiện năng suất, và các đỉnh pull request không còn tạo ra nút thắt.

- **Mở rộng mạng lưới với Cilium**: Hạ tầng mạng được hiện đại hóa nhờ Cilium, cung cấp bảo mật dựa trên nhận dạng, quan sát nâng cao ở mức pod, và mạng dựa trên eBPF. Bằng cách triển khai quản lý địa chỉ IP linh hoạt, Cilium cho phép Cloudera mở rộng trên 10.000 workloads mà không gặp vấn đề cạn kiệt IP, đồng thời cung cấp tầm nhìn rõ ràng về mạng ở mức pod.

- **Loại bỏ lãng phí tài nguyên nhàn rỗi với [AWS Graviton](https://aws.amazon.com/ec2/graviton/) và Flex instances**: Graviton và Flex instances đóng vai trò quan trọng trong tối ưu chi phí và hiệu năng. Graviton mang lại lợi ích hiệu năng-giá tốt cho workloads dựa trên ARM, trong khi Flex instances tăng hiệu quả cho các tác vụ biên dịch x64. Kết hợp lại, các tùy chọn tính toán này giảm gần một phần ba chi phí vận hành và tối đa 40% chi phí hạ tầng, đảm bảo Cloudera cân bằng giữa hiệu suất và chi phí cho các nhu cầu workload đa dạng.

- **Giải quyết độ trễ S3 với tích hợp native cloud**: Chạy build trên Amazon EKS giảm độ trễ tới [Amazon S3](https://aws.amazon.com/s3/) xuống mức mili giây, giúp tăng tốc việc truy xuất artifacts. Điều này còn mang lại lợi ích bổ sung là giảm 30% chi phí truyền tải mạng.

Giải pháp tổng thể này không chỉ giải quyết từng nút thắt mà còn tạo ra một nền tảng có khả năng mở rộng linh hoạt, vận hành an toàn, và tối ưu chi phí trên toàn bộ môi trường hybrid.

### Kết quả kinh doanh

Việc áp dụng môi trường hybrid Kubernetes đã biến đổi hoạt động phát triển của Cloudera. Thời gian chu kỳ build và test được cải thiện 50%, giúp giao hàng các tính năng và cải tiến mới nhanh hơn. Khả năng mở rộng từ 10 lên hơn 1.000 node chỉ trong vài phút đã cung cấp cho lập trình viên quyền truy cập đáng tin cậy vào tài nguyên cần thiết, loại bỏ các nút thắt trong thời điểm nhu cầu cao. Bằng cách tối ưu hóa truyền dữ liệu với Amazon S3, chi phí mạng giảm 30% và độ trễ giảm xuống mili giây. Việc mở rộng thông minh và lựa chọn compute phù hợp với khối lượng công việc đã giảm chi phí hạ tầng 40%. Bottlerocket giảm bề mặt tấn công 60% và tăng hiệu quả sử dụng tài nguyên tính toán 35%.

Những cải tiến này không chỉ tăng cường bảo mật, mà còn giải phóng các kỹ sư khỏi việc quản lý hạ tầng, cho phép họ tập trung vào phát triển cốt lõi.

### Kết luận

Việc triển khai thành công của Cloudera minh chứng sức mạnh biến đổi của stack container của AWS – bao gồm Amazon EKS, Karpenter và Bottlerocket. Môi trường Kubernetes được hiện đại hóa đã mang lại khả năng mở rộng liền mạch, tăng cường bảo mật, và tối ưu quản lý chi phí, đồng thời cung cấp hiệu năng tối đa cho các workloads động. Hành trình của Cloudera chứng minh cách tích hợp các giải pháp AWS chuyên biệt có thể cải thiện đáng kể quản lý hạ tầng, giảm tải vận hành, và tăng tốc năng suất của lập trình viên.

Thông qua cấp phát node tự động, sắp xếp workloads thông minh, và vận hành tinh gọn, Cloudera chứng minh cách các tổ chức có thể đạt được hiệu quả trong môi trường container. Theo kiến trúc đã được Cloudera chứng minh, các doanh nghiệp có thể xây dựng một môi trường Kubernetes mạnh mẽ, có khả năng mở rộng và tiết kiệm chi phí, đáp ứng nhu cầu phát triển hiện nay đồng thời chuẩn bị cho sự tăng trưởng trong tương lai. Hãy liên hệ với đội ngũ AWS của bạn để tiến tới xây dựng một môi trường Amazon EKS hiện đại hóa.