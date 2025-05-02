# Task BE-CORE-006: Xây dựng module Notification

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-003: Module Validation & Exception Handling](BE-CORE-003.md)
    - [BE-CORE-004: Module Logger](BE-CORE-004.md)
  - Yêu cầu chức năng:
    - [F-1.13: Cảnh báo Nhân sự sắp hết Dự án](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.9: Thông báo liên quan đến Cơ hội](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.6: Cảnh báo Thanh toán](../../../BD/FunctionDesign/FunctionList.md)
  - Scheduled Tasks:
    - [BE-SYS-003: Triển khai Scheduled Tasks](../BackendTasks.md)
  - Yêu cầu phi chức năng:
    - NF-9 (Tính khả dụng - notifications về hệ thống)
    - NF-10 (Tính dễ sử dụng - notifications rõ ràng)

## Mô tả
Xây dựng module Notification để quản lý và gửi các thông báo trong hệ thống, bao gồm thông báo trong ứng dụng (in-app notifications), email, và các loại thông báo khác.

## Chi tiết công việc
1. **Thiết lập cơ chế để gửi thông báo trong hệ thống**:
   - Xây dựng class `NotificationManager` để quản lý việc tạo và gửi thông báo.
   - Tạo các models để lưu trữ thông báo (NotificationType, NotificationTemplate, UserNotification).
   - Triển khai các methods để tạo và gửi thông báo đến người dùng hoặc nhóm người dùng.
   - Hỗ trợ các loại thông báo khác nhau (in-app notifications, email, system alerts).
   - Tạo API endpoints để lấy danh sách thông báo của người dùng.

2. **Triển khai queue cho notification processing**:
   - Thiết lập AWS SQS hoặc SNS cho việc xử lý thông báo bất đồng bộ.
   - Xây dựng consumer Lambda function để xử lý queue messages.
   - Đảm bảo at-least-once delivery và xử lý các trường hợp thất bại.
   - Triển khai retry mechanism và dead-letter queues.

3. **Tạo các template cho các loại thông báo**:
   - Thiết kế và triển khai các template cho các loại thông báo khác nhau.
   - Hỗ trợ templating với placeholder variables (Jinja2 hoặc tương tự).
   - Tạo các email templates với HTML và plain text versions.
   - Đảm bảo các templates có thể được cấu hình và cập nhật qua database.

4. **Triển khai email notification service**:
   - Thiết lập tích hợp với AWS SES hoặc dịch vụ email tương tự.
   - Tạo wrapper cho việc gửi email với các tính năng như attachments, HTML content.
   - Theo dõi trạng thái gửi email (delivered, opened, bounced, etc.).
   - Đảm bảo compliance với các quy định về email marketing.

5. **Xây dựng API endpoints cho notification management**:
   - Tạo API để lấy danh sách thông báo của người dùng hiện tại.
   - Tạo API để đánh dấu thông báo đã đọc/chưa đọc.
   - Tạo API để quản lý notification preferences của người dùng.
   - Tạo API để testing và preview notifications.

## Đầu ra dự kiến
- Module Notification hoàn chỉnh với các tính năng nêu trên.
- Quy trình gửi và quản lý thông báo được thiết lập.
- Tích hợp với AWS SQS/SNS và SES.
- Unit tests cho các chức năng chính.
- Documentation cho việc sử dụng module này.

## Tài liệu tham khảo
- [AWS SQS Documentation](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
- [AWS SNS Documentation](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)
- [AWS SES Documentation](https://docs.aws.amazon.com/ses/latest/dg/Welcome.html)
- [Jinja2 Documentation](https://jinja.palletsprojects.com/en/3.0.x/)
- [AWS EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) (cho scheduled notifications)
- [React-Toastify](https://fkhadra.github.io/react-toastify/introduction) (client-side notification library)
- [Web Push Notifications MDN](https://developer.mozilla.org/en-US/docs/Web/API/Push_API) (future enhancement) 