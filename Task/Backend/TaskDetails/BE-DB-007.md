# Task BE-DB-007: Xây dựng System Configuration Models

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 2 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - System Configuration APIs:
    - [API-ADM-011: Get System Configs API](../../../DD/API/API-ADM-011.md)
    - [API-ADM-012: Update System Config API](../../../DD/API/API-ADM-012.md)
    - [API-ADM-013: System Logs API](../../../DD/API/API-ADM-013.md)
  - System Tasks:
    - [BE-SYS-001: Triển khai System Config APIs](../BackendTasks.md)
    - [BE-SYS-002: Triển khai System Logs API](../BackendTasks.md)
    - [BE-SYS-003: Triển khai Scheduled Tasks](../BackendTasks.md)
  - Yêu cầu chức năng: 
    - [F-6.3: Cấu hình Danh mục Hệ thống](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.4: Cấu hình Ngưỡng Cảnh báo](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.5: Quản lý Kết nối API (Hubspot)](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.6: Xem Log Hệ thống](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến cấu hình hệ thống (system configuration), quản lý log, và các thiết lập chung của hệ thống. Các models này giúp hệ thống có thể được cấu hình linh hoạt mà không cần thay đổi code, cũng như hỗ trợ tracking và debug các vấn đề.

## Chi tiết công việc
1. **Xây dựng SystemConfig model**:
   - Thiết kế các fields cần thiết: key, value, value_type, description, category, is_encrypted, is_editable, created_at, updated_at, updated_by, v.v.
   - Hỗ trợ nhiều loại giá trị (string, number, boolean, json, v.v.) và type conversion.
   - Triển khai cơ chế mã hóa cho các giá trị nhạy cảm (API keys, credentials).
   - Tạo seed data cho các cấu hình mặc định của hệ thống.
   - Thiết kế caching mechanism để tối ưu hiệu năng truy vấn configuration.

2. **Xây dựng NotificationTemplate model**:
   - Thiết kế các fields: id, name, type, subject, content, placeholders, is_active, created_at, updated_at, v.v.
   - Hỗ trợ templates cho nhiều loại thông báo khác nhau (email, in-app, etc.).
   - Tích hợp với template engine (Jinja2 hoặc tương tự).
   - Tạo seed data cho các mẫu thông báo cơ bản.

3. **Xây dựng SystemLog model**:
   - Thiết kế các fields: id, timestamp, level, source, message, user_id, context, additional_data, trace_id, v.v.
   - Hỗ trợ các log levels khác nhau (debug, info, warning, error, critical).
   - Triển khai các indexes để tối ưu việc query và filter logs.
   - Thiết kế cơ chế rotation và archiving cho logs cũ.
   - Tạo methods hỗ trợ tìm kiếm và phân tích logs.

4. **Xây dựng ScheduledTask model**:
   - Thiết kế các fields: id, name, description, task_type, cron_expression, is_active, last_run, next_run, parameters, created_at, updated_at, v.v.
   - Triển khai các task types khác nhau (data synchronization, notifications, cleanup).
   - Tạo cơ chế để theo dõi trạng thái và lịch sử chạy của các scheduled tasks.
   - Hỗ trợ cấu hình parameters cho các tasks.

5. **Triển khai các helper models và business logic**:
   - Xây dựng ApiConnection model để quản lý các kết nối tới dịch vụ bên ngoài (Hubspot).
   - Tạo ConfigCategory model/enum để phân loại các cấu hình.
   - Thiết kế AccessLog model để theo dõi các thao tác trên system configs.
   - Triển khai helper methods để đọc/ghi configs với type conversion tự động.
   - Cơ chế validation cho các giá trị cấu hình đặc biệt.

## Đầu ra dự kiến
- SystemConfig, NotificationTemplate, SystemLog, và ScheduledTask models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Seed data cho các cấu hình mặc định và templates.
- Utility functions để làm việc với các models này.
- Unit tests cho các models và business logic.
- Documentation về cấu trúc models và cách sử dụng.