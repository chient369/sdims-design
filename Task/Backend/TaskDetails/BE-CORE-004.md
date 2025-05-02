# Task BE-CORE-004: Xây dựng module Logger

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 2 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Infrastructure Tasks:
    - [BE-INFRA-006: Cấu hình CloudWatch Logs và Monitoring](../BackendTasks.md)
  - Core Modules:
    - [BE-CORE-003: Module Validation & Exception Handling](BE-CORE-003.md)
  - APIs:
    - [API-ADM-013: Xem Log Hệ thống](../../../DD/API/API-ADM-013.md)
  - Yêu cầu chức năng:
    - [F-6.6: Xem Log Hệ thống](../../../BD/FunctionDesign/FunctionList.md)
  - Yêu cầu phi chức năng:
    - NF-14 (Hệ thống logging chi tiết)
    - NF-9 (Tính khả dụng - monitoring)

## Mô tả
Xây dựng module Logger để chuẩn hóa và quản lý việc ghi log trong toàn bộ hệ thống, đảm bảo khả năng theo dõi, phân tích và debug các tương tác trong hệ thống.

## Chi tiết công việc
1. **Tạo utility logging với các level khác nhau**:
   - Xây dựng wrapper cho thư viện logging của Python.
   - Hỗ trợ các level log khác nhau (DEBUG, INFO, WARNING, ERROR, CRITICAL).
   - Tạo các helper functions để log theo các context khác nhau.
   - Đảm bảo mã định danh duy nhất cho mỗi request (request ID).
   - Tự động log các thông tin quan trọng như request/response timing.

2. **Cấu hình log format và context cho dễ debug**:
   - Thiết lập format log chuẩn và có cấu trúc (JSON format).
   - Đảm bảo logs chứa đầy đủ thông tin cần thiết (timestamp, request ID, user ID, function name, v.v.).
   - Cấu hình context tracking giữa các Lambda functions.
   - Tạo các filters và formatters phù hợp cho các môi trường khác nhau (dev, test, production).

3. **Triển khai log masking cho dữ liệu nhạy cảm**:
   - Tạo cơ chế để che giấu thông tin nhạy cảm trong logs (passwords, tokens, personal data).
   - Đảm bảo compliance với các quy định về bảo vệ dữ liệu (GDPR, v.v.).
   - Cấu hình các field cần masking theo từng API endpoint.

4. **Tích hợp với CloudWatch Logs**:
   - Cấu hình log streams và log groups trong CloudWatch.
   - Tạo các metrics và alarms dựa trên log patterns.
   - Đảm bảo retention policy phù hợp cho các loại logs khác nhau.

5. **Triển khai performance logging và monitoring**:
   - Tự động log thời gian thực thi của các operations chính.
   - Tạo các decorators để log performance metrics.
   - Cấu hình alerting cho các performance issues.

## Đầu ra dự kiến
- Module Logger hoàn chỉnh với các tính năng nêu trên.
- Utility functions cho việc ghi log có thể sử dụng trong toàn bộ hệ thống.
- Cấu hình CloudWatch Logs được thiết lập.
- Unit tests cho các chức năng chính.
- Documentation cho việc sử dụng module này.

## Tài liệu tham khảo
- [Python Logging Best Practices](https://docs.python.org/3/howto/logging.html)
- [AWS CloudWatch Logs Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)
- [Serverless Logging Best Practices](https://www.serverless.com/blog/serverless-monitoring-the-good-the-bad-and-the-ugly/)
- [AWS Lambda PowerTools for Python](https://awslabs.github.io/aws-lambda-powertools-python/latest/)
- [ELK Stack Documentation](https://www.elastic.co/guide/index.html) (Elastic Stack - optional)
- [AWS X-Ray Documentation](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html) (cho distributed tracing)
- [JSON Logging Best Practices](https://www.loggly.com/blog/json-logging-best-practices/) 