---
title: "Frontend hosting"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 4.3.2 </b> "
aliases:
  - /4-workshop/4.3-deployment-operations-monitoring/4.3.2-frontend-hosting-auth/
---

## 4.3.2 Frontend hosting

CloudMenu sử dụng Amazon S3 để lưu trữ các file frontend và Amazon CloudFront để phân phối nội dung đến người dùng thông qua Internet.

Frontend của CloudMenu hiện có ba giao diện chính:

Customer: giao diện dành cho khách hàng quét QR tại bàn và thực hiện gọi món.
Kitchen: giao diện dành cho bếp theo dõi và cập nhật trạng thái đơn hàng.
Manager: giao diện Dashboard dành cho quản lý theo dõi dữ liệu và thống kê.

Kiến trúc frontend được triển khai theo mô hình:
![Kiến trúc frontend](/images/4-Workshop/AWS_CloudMenu_Frontend.png)

Khi người dùng truy cập CloudFront domain, CloudFront lấy các file frontend từ S3 và phân phối nội dung đến trình duyệt. Nhờ đó, frontend không cần chạy trực tiếp từ máy phát triển hoặc IDE như VS Code.

### Luồng triển khai Frontend

Hiện tại CloudMenu chưa sử dụng CI/CD tự động cho frontend. Quy trình triển khai được thực hiện thủ công sau khi developer hoàn thành và build source code.

Luồng triển khai hiện tại:
![Luồng triển khai Frontend](/images/4-Workshop/AWS_CloudMenu_Frontend2.png)

Cách triển khai này phù hợp với giai đoạn phát triển và workshop hiện tại vì đơn giản, dễ kiểm tra và không yêu cầu thiết lập thêm pipeline CI/CD.

Tuy nhiên, việc upload thủ công có một số hạn chế:

- Phải thực hiện build và upload sau mỗi lần cập nhật frontend.
- Dễ xảy ra sai sót khi upload thiếu file hoặc sử dụng nhầm phiên bản.
- Chưa có quy trình tự động kiểm tra source code trước khi deploy.
- Việc rollback về phiên bản trước chưa được tự động hóa.


### Kiểm tra Frontend sau khi triển khai

Sau khi frontend được upload lên S3 và CloudFront đã phân phối phiên bản mới, có thể kiểm tra hệ thống bằng CloudFront domain được AWS cung cấp.

Ví dụ:

https://d3be9t7i3323e7.cloudfront.net/index.html?table=01

Khi truy cập thành công, người dùng sẽ thấy giao diện CloudMenu và có thể kiểm tra lần lượt:

- Customer: truy cập menu thông qua QR của bàn và tạo đơn hàng.
![Customer](/images/4-Workshop/AWS_CloudMenu_Customer.png)

- Kitchen: kiểm tra đơn hàng mới và cập nhật trạng thái.
![Kitchen](/images/4-Workshop/AWS_CloudMenu_Kitchen.png)

- Manager: kiểm tra Dashboard và dữ liệu thống kê.
![Manager](/images/4-Workshop/AWS_CloudMenu_Manager.png)

Nếu cả ba giao diện hoạt động bình thường và frontend có thể gọi API Gateway thành công, quá trình hosting frontend đã được triển khai đúng.
