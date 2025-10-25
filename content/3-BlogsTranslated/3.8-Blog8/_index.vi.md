+++
title = " Blog 8"
type = ""
weight = 8
pre = "<b> 3.8. </b>"
+++

# **Kích hoạt phân tích dữ liệu Genomic và Multiomic nhanh chóng với Illumina DRAGEN™ v4.4 trên các instance Amazon EC2 F2**

_Eric Allen, Mark Azadpour, Deepthi Shankar, Olivia Choudhury, và Shyamal Mehtalia_ | 15/07/2025 | [High Performance Computing](https://aws.amazon.com/blogs/hpc/category/high-performance-computing/), [Life Sciences](https://aws.amazon.com/blogs/hpc/category/industries/life-sciences/), [Partner solutions](https://aws.amazon.com/blogs/hpc/category/post-types/partner-solutions/)

Bài viết này được đóng góp bởi Eric Allen (AWS), Olivia Choudhury (AWS), Mark Azadpour (AWS), Deepthi Shankar (Illumina), và Shyamal Mehtalia (Illumina)

![image1](../../../images/Blog8/HPCBlog-366-p1-1024x286.png)

Việc phân tích lượng dữ liệu genomic và multiomic ngày càng gia tăng đòi hỏi các giải pháp tính toán hiệu quả, có khả năng mở rộng và tiết kiệm chi phí. Amazon Web Services (AWS) tiếp tục hỗ trợ các khối lượng công việc này thông qua các dịch vụ tính toán tăng tốc bằng FPGA như các [instance Amazon EC2 F2](https://aws.amazon.com/ec2/instance-types/f2/).

Giải pháp phân tích thứ cấp [DRAGEN (Dynamic Read Analysis for GENomics)](https://www.illumina.com/products/by-type/informatics-products/dragen-secondary-analysis.html) của Illumina đã khẳng định vị thế là một trong những giải pháp phân tích thứ cấp hàng đầu cho dữ liệu sequencing thế hệ mới, cung cấp các thuật toán được tối ưu hóa cao cho phân tích genomic và triển khai tăng tốc phần cứng cho các pipeline phân tích toàn diện về genomics và multiomics, bao gồm DNA germline, DNA somatic, cũng như phân tích RNA ở mức bulk và single-cell, proteomics, spatial, và nhiều hơn nữa.

DRAGEN chạy nguyên bản trên các instance EC2 F2 và cung cấp cho khách hàng một phương pháp nhanh chóng để phân tích tập dữ liệu sinh học của họ. Việc di chuyển sang F2 được đơn giản hóa vì cùng một DRAGEN AMI được sử dụng cho cả F1 và F2, và kết quả phân tích sẽ tương đương nhau. Trong bài viết này, chúng tôi sẽ thảo luận về các đặc tính hiệu năng của DRAGEN trên các môi trường tính toán AWS khác nhau, cũng như cách triển khai [phiên bản DRAGEN v4.4 mới ra mắt gần đây](https://investor.illumina.com/news/press-release-details/2025/Illumina-DRAGEN-v4-4-powers-clinical-oncology-research-and-multiomic-applications/default.aspx) trên các instance Amazon EC2 F2.

## Tổng quan về các instance Amazon EC2 F2

Các instance F2 là thế hệ thứ 2 của các instance EC2 được trang bị FPGA để tăng tốc phân tích dữ liệu genomic và multimedia trong môi trường đám mây. Những instance này mang lại cải tiến đáng kể so với thế hệ trước — các instance F1 — với hiệu suất giá thành được cải thiện tốt hơn tới 60%. Dưới đây là các tính năng và thông số kỹ thuật chính của instance F2:

- Cấu hình FPGA: Instance F2 được trang bị tối đa tám AMD Virtex UltraScale+ HBM VU47P FPGAs, mỗi FPGA tích hợp 16GB bộ nhớ băng thông cao (HBM – High-Bandwidth Memory).

- Bộ xử lý: Được vận hành bởi bộ vi xử lý AMD EPYC thế hệ thứ 3 (Milan), instance F2 cung cấp tới 192 vCPU — gấp ba lần số lõi xử lý so với instance F1.

- Bộ nhớ: Instance này hỗ trợ tới 2 TiB bộ nhớ hệ thống, gấp đôi dung lượng bộ nhớ của instance F1.

- Lưu trữ: F2 đi kèm với tối đa 7.6 TiB bộ nhớ SSD NVMe, gấp đôi dung lượng lưu trữ của F1.

- Networking: Tốc độ băng thông mạng lên tới 100 Gbps, cao gấp bốn lần so với băng thông mạng có sẵn trên instance F1.

<table>
  <tr>
    <th>Instance Name</th>
    <th>FPGAs</th>
    <th>vCPUs</th>
    <th>Instance Memory</th>
    <th>NVMe Storage</th>
    <th>Network Bandwidth</th>
  </tr>
  <tr>
    <td>f1.2xlarge</td>
    <td>1</td>
    <td>8</td>
    <td>122</td>
    <td>470</td>
    <td>Up to 10 Gbps</td>
  </tr>
  <tr style="background-color:#A5CEF0;color: black;"> <!-- tô màu xanh lá -->
    <td>f1.4xlarge</td>
    <td>2</td>
    <td>16</td>
    <td>244</td>
    <td>940</td>
    <td>10 Gbps</td>
  </tr>
  <tr style="background-color:#A9D979;color: black;"> <!-- tô màu xanh lá -->
    <td>f2.6xlarge</td>
    <td>1</td>
    <td>24</td>
    <td>256</td>
    <td>950</td>
    <td>10 Gbps</td>
  </tr>
   <tr>
    <td>F2.12xlarge</td>
    <td>2</td>
    <td>48</td>
    <td>512</td>
    <td>1900</td>
    <td>25 Gbps</td>
  </tr>
   <tr>
    <td>f1.16xlarge</td>
    <td>8</td>
    <td>64</td>
    <td>976</td>
    <td>44x940</td>
    <td>25 Gbps</td>
  </tr>
   <tr>
    <td>F2.48xlarge</td>
    <td>8</td>
    <td>192</td>
    <td>2048</td>
    <td>7600</td>
    <td>100 Gbps</td>
  </tr>
</table>

*Bảng 1: Bảng so sánh thông số kỹ thuật về compute, memory, storage, và networking giữa các instance F1 và F2.*

## Phương pháp đánh giá hiệu năng

Illumina khuyến nghị sử dụng f1.4xlarge khi dùng instance F1 và f2.6xlarge khi dùng instance F2. Để đánh giá hiệu năng trên các instance này, DRAGEN đã được cấu hình và chạy trên AWS theo [hướng dẫn người dùng Illumina DRAGEN](https://help.dragen.illumina.com/product-guides/dragen-v4.4), và [các liên kết tới genome reference file] có thể tìm thấy trên [trang web Illumina DRAGEN Product Files](https://support.illumina.com/sequencing/sequencing_software/dragen-bio-it-platform/product_files.html).

1. Phân tích Whole Genome Sequencing (WGS) với độ phủ khoảng 35x sử dụng một mẫu có sẵn công khai. Mẫu HG002 từ [dự án NIST Genome in a Bottle](https://www.nist.gov/programs-projects/genome-bottle) đã được sử dụng. Mẫu này được phân tích bằng DRAGEN v4.4 Germline pipeline theo hai cách khác nhau. Phân tích “cơ bản” chỉ bao gồm alignment cơ bản và small variant calling, nhằm tạo điều kiện so sánh với các pipeline tin sinh học phổ biến cho mẫu germline như BWA/GATK. Phân tích “đầy đủ” sử dụng tất cả các variant caller, bao gồm cả copy number và structural variants, cùng các tùy chọn bổ sung như gọi pharmacogenetic star allele và gọi HLA (Human Leukocyte Antigen), để tạo ra một bộ genome được phân tích đầy đủ. Trong cả hai trường hợp, hg38 multigenome graph reference của DRAGEN được sử dụng cho phân tích WGS. Các file dữ liệu fastq của mẫu có thể truy cập trên Amazon S3 qua các liên kết: [fastq R1](https://s3/ilmn-dragen-giab-samples/WGS/precisionFDA_v2_HG002/HG002.novaseq.pcr-free.35x.R1.fastq.gz), [fastq R2](https://s3/ilmn-dragen-giab-samples/WGS/precisionFDA_v2_HG002/HG002.novaseq.pcr-free.35x.R2.fastq.gz).

2. Phân tích Tumor-Normal sử dụng một cặp mẫu đã được khảo sát trong một [ấn phẩm về phân tích ung thư của DRAGEN](https://www.biorxiv.org/content/10.1101/2023.03.23.534011v2) trước đây. Hai mẫu này có độ phủ khoảng 110x (tumor) và 40x (normal). Các mẫu được phân tích bằng DRAGEN v4.4 Somatic pipeline, bao gồm alignment, small variant calling, cũng như phân tích CNV và SV. hg38 linear genome reference của DRAGEN được sử dụng cho phân tích Tumor-Normal. Các file dữ liệu fastq của mẫu có thể truy cập trên Amazon S3 qua các liên kết: [Tumor fastq R1](https://s3/dragen-wgs-tn-somatic-benchmarking-datasets/fastq/HCC1395_75pc/tumor/T_SRR7890895_75pc_1.fastq.gz), [Tumor fastq R2](https://s3/dragen-wgs-tn-somatic-benchmarking-datasets/fastq/HCC1395_75pc/tumor/T_SRR7890895_75pc_2.fastq.gz), [Normal fastq R1](https://s3/dragen-wgs-tn-somatic-benchmarking-datasets/fastq/HCC1395_75pc/normal/N_SRR7890889_1.fastq.gz), [Normal fastq R2](https://s3/dragen-wgs-tn-somatic-benchmarking-datasets/fastq/HCC1395_75pc/normal/N_SRR7890889_2.fastq.gz).

