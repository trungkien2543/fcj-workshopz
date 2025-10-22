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

*Hình 1. Multi-Region security, identity, and compliance services*

## Xây dựng một mạng toàn cầu

Với các tài nguyên cần đưa vào mạng ảo trong các vùng khác nhau, [Amazon Virtual Cloud (Amazon VPC)](http://aws.amazon.com/vpc) cho phép [định tuyến riêng biệt giữa các vùng và tài khoản với VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html). Những tài nguyên này có thể giao tiếp bằng cách dùng địa chỉ IP private và không yêu cầu một internet gateway, VPN, hoặc các thiết bị mạng riêng biệt. Tính năng này hoạt động tốt trong các mạng nhỏ vì chỉ yêu cầu một vài kết nối peering. Tuy nhiên, định tuyến chuyển tiếp không cho phép điều đó, và vì số lượng đám mây riêng ảo peering (VPCs) tăng lên, mạng kết nối lưới trở lên khó để quản lý và sửa lỗi.

[AWS Transit Gateway](https://aws.amazon.com/transit-gateway/) làm giảm các khó khăn bằng cách tạo một hub định tuyến chuyển tiếp để kết nối VPCs và mạng on-premises của bạn. Khả năng định tuyến của Transit Gateway có thể mở rộng sang các Regions khác với [Transit Gateway inter-Region peering](https://docs.aws.amazon.com/vpc/latest/tgw/tgw-peering.html) dể tạo ra mạng lưới riêng tư, phân tán cho các tài nguyên của bạn.

Việc xây dựng một cách đáng tin cậy, tiết kiệm chi phí để định tuyến các người dùng đến các ứng dụng Internet phân tán yêu cầu Domain Name System (DNS) có khả năng sẵn sàng cao và có thể mở rộng. [Amazon Route 53]() là dịch vụ có thể đáp ứng yêu cầu này.

Route 53 bao gồm nhiều chính sách cho việc định tuyến. Ví dụ, bạn có thể định tuyến một yêu cầu tới một bản ghi có độ trễ mạng thấp nhất, hoặc gửi người dùng ở một vị trí địa lý cụ thể đến điểm cuối ứng dụng gần nhất. Đối với khôi phục sau thảm họa (Disaster Recovery – DR), Route 53 Application Recovery Controller (Route 53 ARC) cung cấp giải pháp chuyển đổi dự phòng toàn diện với ít phụ thuộc nhất. Các chính sách định tuyến, kiểm tra an toàn, và kiểm tra mức sẵn sàng của Route 53 ARC giúp bạn chuyển đổi dự phòng giữa các Region, AZs và on-premises một cách đáng tin cậy.

[Amazon CloundFront](http://aws.amazon.com/cloudfront), mạng phân phối nội dung toàn cầu (CDN), được xây dựng trên hơn 300 point of presence (PoP) trải rộng trên khắp thế giới. Các ứng dụng có nhiều nguồn gốc dữ liệu, như là giữa các Region, có thể sử dụng [CloudFront origin failover](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/high_availability_origin_failover.html) để có thể chuyển đổi dự phòng tự động tới origin dự phòng khi cái chính không còn khả dụng. Khả năng của CloudFront không chỉ dừng ở việc phân phối nội dung mà còn có khả năng chạy xử lý ở edge. [CloudFront Functions](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/cloudfront-functions.html), bạn có thể dễ dàng chạy mã JavaScript nhẹ, và [AWS Lambda@Edge](https://docs.aws.amazon.com/lambda/latest/dg/lambda-edge.html), bạn có thể chạy các hàm Node.js và Python gần hơn với người dùng của ứng dụng, giúp cải thiện hiệu năng và giảm độ trễ. Bằng cách đặt xử lý tại edge, bạn giảm tải cho origin và mang lại phản hồi nhanh hơn cho người dùng toàn cầu.

Dựa trên mạng lưới toàn cầu của AWS, [AWS Global Accelerator](http://aws.amazon.com/global-accelerator) cung cấp hai địa chỉ IP anycast tĩnh để làm điểm vào duy nhất cho các ứng dụng Internet-facing. Bạn có thể thêm hoặc gỡ bỏ origin mà không làm gián đoạn, trong khi hệ thống tự động định tuyến đến endpoint Regional khỏe mạnh gần nhất. Nếu phát hiện sự cố, Global Accelerator sẽ tự động chuyển hướng lưu lượng đến endpoint khả dụng trong vài giây mà không cần thay đổi IP tĩnh.

Hình 2 sử dụng một chính sách định tuyến dựa trên độ trễ của Route53 để định tuyến các người dụng đển endpoint nhanh nhất, CloudFront được sử dụng để phục vụ các nội dung tĩnh như video, hình ảnh và Transit Gateways tạo một mạng riêng toàn cầu để các thiết bị của bạn có thể giao tiếp an toàn giữa các vùng.

![figure2](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-2.-AWS-VPC-connectivity-and-content-delivery.png)

*Hình 2. AWS VPC connectivity and content delivery*

## Xây dựng và quản lý lớp tính toán (compute layer)

Mặc dù các instance [Amazon Elastic Compute Cloud (Amazon EC2)](http://aws.amazon.com/ec2) và các volume [Amazon Elastic Block Store (Amazon EBS)](http://aws.amazon.com/ebs) liên quan chỉ nằm trong một Availability Zone (AZ), [Amazon Data Lifecycle Manager](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshot-lifecycle.html) có thể tự động hóa quá trình tạo và sao chép snapshot của EBS qua nhiều Region. Điều này giúp nâng cao chiến lược khôi phục sau thảm họa (DR) bằng cách cung cấp một tùy chọn sao lưu “cold” dễ dàng cho các EBS volume. Nếu bạn cần sao lưu nhiều hơn chỉ EBS volume, [AWS Backup](https://aws.amazon.com/backup/) cung cấp một nơi tập trung để thực hiện việc này trên nhiều dịch vụ, và sẽ được trình bày chi tiết trong [phần 2](https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-2-data-and-replication/).

Một instance Amazon EC2 được tạo dựa trên một [Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html). Một AMI xác định cấu hình của instance, chẳng hạn như: lưu trữ, quyền khởi chạy, và ánh xạ thiết bị. Khi cần tạo và phát hành một hình ảnh chuẩn mới, [EC2 Image Builder](https://aws.amazon.com/image-builder/) giúp đơn giản hóa việc xây dựng, kiểm thử và triển khai các AMI mới. Nó cũng hỗ trợ sao chép AMI sang các Region khác, không cần phải sao chép thủ công từ Region nguồn sang Region đích.

Các ứng dụng dựa trên microservices sử dụng container được hưởng lợi từ thời gian khởi động nhanh hơn. [Amazon Elastic Container Registry (Amazon ECR)](http://aws.amazon.com/ecr/) giúp đảm bảo điều này xảy ra nhất quán giữa các Region thông qua [sao chép image private](https://aws.amazon.com/blogs/containers/cross-region-replication-in-amazon-ecr-has-landed/) ở cấp registry. Một registry private ECR có thể được cấu hình để sao chép giữa các Region hoặc giữa các tài khoản, đảm bảo các image sẵn sàng ở Region phụ khi cần.

Khi kiến trúc mở rộng ra nhiều Region, việc theo dõi nơi các tài nguyên được cung cấp có thể trở nên khó khăn. [Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view) giúp giảm bớt khó khăn này bằng cách cung cấp một bảng điều khiển tập trung, hiển thị các tài nguyên EC2 như instances, VPC, subnet, security group, và volumes trong tất cả các Region đang hoạt động.

Chúng tôi tổng hợp các tính năng của compute layer trong Hình 3 bằng cách sử dụng EC2 Image Builder để sao chép golden AMI mới nhất của chúng tôi sang các Region khác để triển khai. Chúng tôi cũng sao lưu mỗi EBS volume trong 3 ngày và sao chép chúng qua các Region bằng Data Lifecycle Manager.

![Figure 3](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-3.-AMI-and-EBS-snapshot-copy-across-Regions.png)

*Hình 3. AMI and EBS snapshot copy across Regions*

## Đưa tất cả lại với nhau

Ở cuối mỗi phần của loạt bài blog này, chúng tôi sẽ xây dựng một ứng dụng mẫu dựa trên các dịch vụ đã đề cập. Điều này cho thấy cách bạn có thể kết hợp các dịch vụ để xây dựng một ứng dụng đa Vùng (multi-Region) với AWS. Chúng tôi không sử dụng tất cả dịch vụ được nhắc đến, chỉ chọn những dịch vụ phù hợp với trường hợp sử dụng.

Chúng tôi xây dựng ví dụ này để mở rộng đến phạm vi toàn cầu. Ứng dụng yêu cầu tính sẵn sàng cao giữa các Vùng, và ưu tiên hiệu năng hơn là tính nhất quán tuyệt đối. Chúng tôi đã chọn các dịch vụ sau (trong bài viết này) để đạt được mục tiêu:

- Route 53 với chính sách định tuyến theo độ trễ (latency routing) để đưa người dùng đến vùng triển khai có độ trễ thấp nhất.

- CloudFront được thiết lập để phân phối nội dung tĩnh. Region 1 là nguồn gốc chính, nhưng chúng tôi đã cấu hình dự phòng nguồn gốc (origin failover) sang Region 2 trong trường hợp có sự cố.

- Ứng dụng phụ thuộc vào một số API của bên thứ ba, vì vậy Secrets Manager với khả năng sao chép đa Vùng đã được thiết lập để lưu trữ thông tin khóa API nhạy cảm.

- CloudTrail logs được tập trung tại Region 1 để dễ dàng phân tích và kiểm toán.

- Security Hub tại Region 1 được chọn làm nơi tập hợp các phát hiện từ tất cả các Vùng.

- Đây là ứng dụng dựa trên container, chúng tôi dựa vào Amazon ECR replication tại mỗi vị trí để nhanh chóng tải về các image mới nhất tại chỗ.

- Để liên lạc qua IP riêng giữa các Vùng, một Transit Gateway được thiết lập tại mỗi Vùng với kết nối liên Vùng. VPC peering cũng có thể hoạt động, nhưng vì dự kiến mở rộng ra nhiều Vùng hơn trong tương lai nên chúng tôi chọn Transit Gateway như giải pháp lâu dài.

- IAM được dùng để cấp quyền quản lý tài nguyên AWS.

![Figure 4](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2022/03/31/Figure-4.-Building-an-application-with-AWS-multi-Region-services-using-services-covered-in-Part-1.png)

*Hình 4. Xây dựng ứng dụng với các dịch vụ AWS đa Vùng, sử dụng những dịch vụ đã đề cập trong Phần 1*

### Tóm tắt

Khi thiết kế một ứng dụng đa Vùng (multi-Region), việc xây dựng một nền tảng vững chắc là vô cùng quan trọng. Nền tảng này sẽ giúp bạn phát triển ứng dụng nhanh chóng theo cách an toàn, đáng tin cậy và linh hoạt. Nhiều dịch vụ AWS đã tích hợp sẵn các tính năng hỗ trợ bạn xây dựng kiến trúc đa Vùng.

Tùy vào lý do mở rộng ra ngoài một Vùng duy nhất mà kiến trúc của bạn sẽ khác nhau. Trong bài viết này, chúng tôi đã đề cập đến các tính năng cụ thể của những dịch vụ AWS về bảo mật, mạng và tính toán (compute) — với khả năng tích hợp sẵn để giảm bớt khối lượng công việc nặng nề và lặp lại.

Trong các bài viết tiếp theo, chúng tôi sẽ tiếp tục đề cập đến các dịch vụ về dữ liệu, ứng dụng và quản lý.

**Sẵn sàng để bắt đầu?**
Chúng tôi đã chọn một số [AWS Solutions](https://aws.amazon.com/solutions/) và [AWS Blogs](https://aws.amazon.com/blogs/?awsf.blog-master-category=*all&awsf.blog-master-learning-levels=*all&awsf.blog-master-industry=*all&awsf.blog-master-analytics-products=*all&awsf.blog-master-artificial-intelligence=*all&awsf.blog-master-aws-cloud-financial-management=*all&awsf.blog-master-business-applications=*all&awsf.blog-master-compute=*all&awsf.blog-master-customer-enablement=*all&awsf.blog-master-customer-engagement=*all&awsf.blog-master-database=*all&awsf.blog-master-developer-tools=*all&awsf.blog-master-devops=*all&awsf.blog-master-end-user-computing=*all&awsf.blog-master-mobile=*all&awsf.blog-master-iot=*all&awsf.blog-master-management-governance=*all&awsf.blog-master-media-services=*all&awsf.blog-master-migration-transfer=*all&awsf.blog-master-migration-solutions=*all&awsf.blog-master-networking-content-delivery=*all&awsf.blog-master-programming-language=*all&awsf.blog-master-sector=*all&awsf.blog-master-security=*all&awsf.blog-master-storage=*all&filtered-posts.q=multi-region&filtered-posts.q_operator=AND) để hỗ trợ bạn!

**Bạn đang tìm thêm nội dung về kiến trúc?**
 [AWS Architecture Center](https://aws.amazon.com/architecture/) cung cấp sơ đồ kiến trúc tham chiếu, các giải pháp kiến trúc đã được kiểm chứng, những thực tiễn tốt nhất theo Well-Architected, các mẫu (patterns), biểu tượng và nhiều hơn nữa!

 Link bài viết gốc: (https://aws.amazon.com/blogs/architecture/creating-a-multi-region-application-with-aws-services-part-1-compute-and-security/)