# Task BE-DB-001: Xây dựng User & Auth Models

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-002: Module Authentication & Authorization](BE-CORE-002.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Authentication APIs:
    - [API-AUTH-001: Login API](../../../DD/API/API-AUTH-001.md)
    - [API-AUTH-002: Logout API](../../../DD/API/API-AUTH-002.md)
    - [API-AUTH-003: Get Current User API](../../../DD/API/API-AUTH-003.md)
  - User Management APIs:
    - [API-ADM-001 đến API-ADM-005: User Management](../../../BD/API/api_list.md)
    - [API-ADM-006 đến API-ADM-010: Role Management](../../../BD/API/api_list.md)
  - Yêu cầu chức năng: 
    - [F-6.1: Quản lý Người dùng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)
  - Tài liệu:
    - [Permissions Define](../../../BD/permissions_definition.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến User và Authentication/Authorization, bao gồm User, Role, Permission và các mối quan hệ cần thiết. Các models này sẽ là nền tảng cho hệ thống phân quyền và xác thực người dùng.

## Chi tiết công việc
1. **Xây dựng User model**:
   - Thiết kế các fields cần thiết: username, email, password_hash, full_name, is_active, created_at, updated_at, last_login, etc.
   - Định nghĩa các methods cho việc xác thực và quản lý người dùng.
   - Đảm bảo bảo mật thông tin nhạy cảm (mã hóa mật khẩu, etc).
   - Triển khai các validation rules cho user data.
   - Đảm bảo tính duy nhất của username/email.

2. **Xây dựng Role model**:
   - Thiết kế các fields: name, description, created_at, updated_at, etc.
   - Định nghĩa mối quan hệ nhiều-nhiều giữa User và Role.
   - Triển khai các methods để kiểm tra và quản lý roles.
   - Đảm bảo tính duy nhất của role name.

3. **Xây dựng Permission model**:
   - Thiết kế các fields: resource, action, scope (optional), description, etc.
   - Định nghĩa mối quan hệ nhiều-nhiều giữa Role và Permission.
   - Triển khai các methods để kiểm tra quyền truy cập.
   - Tạo seed data cho các permissions cơ bản của hệ thống.

4. **Xây dựng UserSession model (nếu cần)**:
   - Thiết kế các fields: user_id, token_id, ip_address, device_info, is_active, created_at, expired_at, etc.
   - Định nghĩa mối quan hệ một-nhiều với User.
   - Triển khai các methods để kiểm tra và quản lý session.
   - Cấu hình auto cleanup cho expired sessions.

5. **Triển khai các relationships và business logic**:
   - Các methods để kiểm tra user có quyền trên một resource nhất định.
   - Logic để quản lý role hierarchy (nếu có).
   - Relationship methods để dễ dàng truy cập permissions của user.
   - Triển khai các helper functions cho việc truy vấn và filtering.

## Đầu ra dự kiến
- User, Role, Permission, và UserSession models hoàn chỉnh với các tính năng nêu trên.
- Migration scripts cho việc tạo database tables.
- Seed data cho basic roles và permissions.
- Unit tests cho các models và relationships.
- Documentation về cấu trúc models và cách sử dụng.
