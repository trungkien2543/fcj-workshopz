+++
title = " Blog 5"
type = ""
weight = 5
pre = "<b> 3.5. </b>"
+++

# **Tạo ứng dụng đa vùng với dịch cụ AWS - Phần 1, Tính toán, Mạng và Bảo mật**

*Joe Chapman và Sebastian Leks* | 08/12/2021 | [Amazon CloudFront](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/amazon-cloudfront/), [Amazon EC2](https://aws.amazon.com/blogs/architecture/category/compute/amazon-ec2/), [Amazon Elastic Block Store (Amazon EBS)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-elastic-block-storage-ebs/), [Amazon Route 53](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/amazon-route-53/), [Amazon Simple Storage Service (S3)](https://aws.amazon.com/blogs/architecture/category/storage/amazon-simple-storage-services-s3/), [Amazon VPC](https://aws.amazon.com/blogs/architecture/category/compute/amazon-vpc/), [Architecture](https://aws.amazon.com/blogs/architecture/category/architecture/), [AWS CloudTrail](https://aws.amazon.com/blogs/architecture/category/management-tools/aws-cloudtrail/), [AWS Global Accelerator](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/aws-global-accelerator/), [AWS Identity and Access Management (IAM)](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-identity-and-access-management-iam/), [AWS Secrets Manager](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-secrets-manager/), [AWS Security Hub](https://aws.amazon.com/blogs/architecture/category/security-identity-compliance/aws-security-hub/), [AWS Transit Gateway](https://aws.amazon.com/blogs/architecture/category/networking-content-delivery/aws-transit-gateway/), [AWS Well-Architected](https://aws.amazon.com/blogs/architecture/category/aws-well-architected/) | [Permalink](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-1-compute-and-security/)

Nhiều dịch vụ AWS có các tính năng để giúp bạn xây dựng và quản lý một kiến trúc đa vùng nhưng việc xác định những khả năng đó trên hơn 200 dịch vụ có thể rất khó khăn.

Đây là chuỗi blog gồm 3 phần, chúng tôi lọc thông qua hơn 200 dịch vụ và tập trung vào những thứ có tính năng cụ thể để hỗ trợ bạn trong việc xây dụng các ứng dụng đa vùng. Trong phần 1, chúng ta sẽ xây dựng một nền tảng với các dịch vụ bảo mật, mạng và tính toán của AWS. Trong phần 2, chúng ta sẽ thêm vào dữ liệu và các chiến lược sao chép. Cuối cùng, trong phần 3, chúng ta sẽ xem xét các lớp ứng dụng và quản lý. Khi chúng ta đi qua từng phần, chúng ta sẽ xây dựng được một ứng dụng ví dụ để minh họa một cách kết hợp các dịch vụ này nhằm tạo ra một ứng dụng đa vùng.

## Những cân nhắc trước khi bắt đầu

[AWS Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) được xây dựng với nhiều [Availbility Zone](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/#Availability_Zones) (AZs) riêng biệt và tách biệt về mặt vật lý. Điều đó cho phép bạn tạo khôi lượng công việc [Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html) có tính khả dụng cao trải dài trên AZs để đạt được mức khả năng chịu lỗi cao hơn. Điều đó thỏa mãn được các mục tiêu hiện có của hầu hết các ứng dụng, nhưng có một vài lý do khiến bạn có thể nghĩ về việc mở rộng ra ngoài một Region duy nhất:

- **Mở rộng đến đối tượng toàn cầu** khi một ứng dụng phát triển và cơ sở người dùng ngày càng phân tán về mặt địa lý, có thể cần phải giảm độ trễ cho các khu vực khác nhau trên thế giới
- **Giảm [Recovery Point Objectives (RPO) and Recovery Time Objectives (RTO)](https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/disaster-recovery-dr-objectives.html)** như một phần của [kế hoạch phục hồi thảm họa](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html) đa khu vực
- **Luật lệ và quy định địa phương** có thể có các yêu cầu nghiêm ngặt về quyền riêng tư và lưu trữ dữ liệu phải tuân theo

Nếu bạn đang xây dựng một ứng dụng đa vùng mới, bạn có thể muốn xem xét việc tập trung vào những dịch vụ AWS có chức năng tích hợp để hỗ trợ. Các ứng dụng hiện có sẽ cần phải được kiểm tra thêm để xác định kiến trúc có khả năng mở rộng nhất để hỗ trợ sự phát triển của nó. Các phần sau đây sẽ xem xét các dịch vụ này và nêu bật các trường hợp sử dụng cũng như các phương pháp hay nhất.

## Xác thực và truy cập khắp các Regions

Việc tạo một nền tảng bảo mật bắt đầu với việc cài đặt các luật lệ về xác thực và ủy quyền phù hợp. Hệ thống xử lý các yêu cầu phải có khả năng phục hồi cao để xác minh và cấp phép yêu cầu nhanh chóng và đáng tin cậy. [AWS Identity and Access Management (IAM)](http://aws.amazon.com/iam) hoàn thành được việc đó bằng việc tạo một cơ chế đáng tin cậy cho bạn để quản lý truy cập vào tài nguyên và dịch vụ AWS. IAM có sẵn khả dụng ở nhiều vùng một cách tự động, bạn không cần hình gì cả.

Để giúp việc quản lý các người dùng , thiết bị, và các ứng dụng Windows trong một mạng đa vùng, bạn có thể thiết lập [AWS Directory Service for Microsoft Active Directory](https://aws.amazon.com/directoryservice/active-directory/) phiên bản doanh nghiệp để [có thể sao lưu dữ liệu thư mục một cách tự động giữa các Regions](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_configure_multi_region_replication.html). Điều này làm giảm độ trễ tra cứu thư mục bằng cách sử sụng thư mục ở gần nhất và tạo độ bền bằng việc trải dài trên nhiều vùng. Lưu ý rằng điều này cũng sẽ tạo ra cùng một số phận giữa các bộ điều khiển miền trong kiến trúc đa vùng, bởi vì các thay đổi trong group policy sẽ được truyền đến tất cả các máy chủ thành viên.

Các ứng dụng cần lưu trữ, xoay vòng và kiểm toán thông tin bí mật một cách an toàn, chẳng hạn như mật khẩu cơ sở dữ liệu, nên sử dụng [AWS Secrets Manager](https://aws.amazon.com/secrets-manager). Dịch vụ này mã hóa các bí mật với các khóa [AWS Key Management Service (AWS KMS)](http://aws.amazon.com/kms) và có thể [sao chép bí mật đến vùng thứ hai](https://docs.aws.amazon.com/secretsmanager/latest/userguide/create-manage-multi-region-secrets.html) để đảm bảo các ứng dụng có thể nhanh chóng lấy lại một bí mật tại vùng gần nhất.

## Mã hóa trên khắp các khu vực

AWS KMS có thể được sử dụng để mã hóa dữ liệu khi lưu trữ và được sử dụng rộng rãi trong các dịch vụ AWS để thực hiện mã hóa. Theo mặc định, các khóa chỉ giới hạn trong một vùng. Các dịch vụ [AWS như Amazon Simple Storage Service (Amazon S3)](http://aws.amazon.com/s3) sao chép liên vùng và Amazon Aurora Global Database (cả hai sẽ được đề cập trong [phần 2](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-2-data-and-replication/)) giúp đơn giản hóa quá trình mã hóa và giải mã với các khóa khác nhau ở từng vùng. Đối với những phần khác của ứng dụng đa vùng phụ thuộc vào khóa KMS, bạn có thể thiết lập [AWS KMS multi-Region keys](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html) để sao chép vật liệu khóa (key material) và ID khóa sang một vùng khác. Điều này loại bỏ nhu cầu phải giải mã rồi mã hóa lại dữ liệu bằng các khóa khác nhau ở mỗi vùng. Ví dụ: multi-Region keys có thể được sử dụng để giảm độ phức tạp trong các hoạt động mã hóa của ứng dụng đa vùng khi dữ liệu được lưu trữ trên nhiều vùng.

## Kiểm toán và khả năng quan sát trên khắp các khu vực

Một phương pháp tốt nhất là cấu hình [AWS CloudTrail](http://aws.amazon.com/cloudtrail) để ghi lại tất cả các hoạt động API liên quan trên tài khoản của bạn phục vụ mục đích kiểm toán. Khi sử dụng nhiều vùng hoặc nhiều tài khoản, các log của CloudTrail nên được tập trung vào một Amazon S3 bucket duy nhất để dễ dàng phân tích. Để tránh việc sử dụng sai mục đích, các log tập trung này nên được xử lý nghiêm ngặt hơn, chỉ cấp quyền truy cập hạn chế cho các hệ thống và nhân sự quan trọng.

Để quản lý hiệu quả các kết quả từ [AWS Security Hub](https://aws.amazon.com/security-hub), bạn có thể [tập hợp và liên kết các kết quả từ nhiều vị trí về một vùng (Region) duy nhất.](https://docs.aws.amazon.com/securityhub/latest/userguide/finding-aggregation-enable.html) Đây là cách đơn giản để tạo góc nhìn tập trung về các kết quả của Security Hub trên nhiều tài khoản và vùng. Khi thiết lập xong, các kết quả sẽ được đồng bộ liên tục giữa các vùng, giúp bạn cập nhật thông tin toàn cầu ngay trên một bảng điều khiển duy nhất.

Chúng tôi tổng hợp các tính năng này trong Hình 1. Chúng tôi sử dụng IAM để cấp quyền truy cập chi tiết đến các dịch vụ và tài nguyên AWS, Directory Service for Microsoft AD để xác thực người dùng trên các ứng dụng Microsoft, và Secrets Manager để lưu trữ thông tin nhạy cảm như mật khẩu cơ sở dữ liệu. Dữ liệu của chúng tôi, di chuyển tự do giữa các vùng (Regions), được mã hóa bằng KMS multi-Region keys, và tất cả các truy cập API của AWS đều được ghi lại bởi CloudTrail và tập trung vào một S3 bucket trung tâm, chỉ nhóm bảo mật của chúng tôi mới có quyền truy cập.

![Figure 1. Multi-Region security, identity, and compliance services](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-1.-Multi-Region-security-identity-and-compliance-services-1.png)

*Figure 1. Multi-Region security, identity, and compliance services*