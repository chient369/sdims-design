# Task BE-API-ADM-001: Triển khai User Management APIs

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 5 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-002: Module Authentication & Authorization](BE-CORE-002.md)
    - [BE-CORE-003: Module Validation & Exception Handling](BE-CORE-003.md)
    - [BE-CORE-004: Module Logger](BE-CORE-004.md)
  - Database Models:
    - [BE-DB-001: User & Auth Models](BE-DB-001.md)
  - API Specifications:
    - [API-ADM-001: Get Users List API](../../../DD/API/API-ADM-001.md)
    - [API-ADM-002: Create User API](../../../DD/API/API-ADM-002.md)
    - [API-ADM-003: Get User Detail API](../../../DD/API/API-ADM-003.md)
    - [API-ADM-004: Update User API](../../../DD/API/API-ADM-004.md)
    - [API-ADM-005: Delete User API](../../../DD/API/API-ADM-005.md)
  - Yêu cầu chức năng: 
    - [F-6.1: Quản lý Người dùng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai các API để quản lý người dùng hệ thống, bao gồm xem danh sách, tạo mới, xem chi tiết, cập nhật và xóa người dùng. Các API này chỉ dành cho Admin và cung cấp đầy đủ chức năng để quản lý tài khoản truy cập hệ thống.

## Chi tiết công việc
1. **Triển khai API Get Users List (API-ADM-001)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/users` với method GET.
   - Triển khai logic truy vấn danh sách user với các tính năng:
     - Phân trang (page, limit)
     - Sắp xếp (sort_by, sort_order)
     - Tìm kiếm và lọc (search_term, status, role_id)
   - Tối ưu truy vấn với JOIN bảng roles (nếu cần).
   - Đảm bảo kiểm tra quyền (chỉ Admin mới được truy cập).
   - Format response kèm thông tin pagination metadata.

2. **Triển khai API Create User (API-ADM-002)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/users` với method POST.
   - Triển khai validation schema cho request body:
     - username (required, unique)
     - email (required, unique, valid email)
     - password (required, strong password)
     - full_name (required)
     - roles (array of role_ids)
     - status (active/inactive)
   - Xử lý tạo user mới trong database:
     - Hash password an toàn
     - Gán các roles được chọn
     - Thiết lập giá trị mặc định khác
   - Kiểm tra và xử lý các trường hợp trùng lặp (username, email).
   - Format response với thông tin user đã tạo (không bao gồm password).

3. **Triển khai API Get User Detail (API-ADM-003)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/users/{userId}` với method GET.
   - Truy vấn thông tin chi tiết user với roles, permissions.
   - Xử lý trường hợp user không tồn tại.
   - Format response bao gồm đầy đủ thông tin user và roles.

4. **Triển khai API Update User (API-ADM-004)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/users/{userId}` với method PUT.
   - Triển khai validation schema cho request body:
     - email (optional, unique, valid email)
     - full_name (optional)
     - roles (optional, array of role_ids)
     - status (optional, active/inactive)
     - password (optional, strong password - nếu cần reset)
   - Xử lý cập nhật thông tin user trong database.
   - Xử lý đặc biệt cho các trường hợp:
     - Thay đổi roles
     - Reset password (nếu có)
     - Khóa/mở khóa tài khoản
   - Kiểm tra và xử lý các trường hợp trùng lặp (email).
   - Format response với thông tin user đã cập nhật.

5. **Triển khai API Delete User (API-ADM-005)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/users/{userId}` với method DELETE.
   - Triển khai soft delete (đánh dấu xóa) thay vì xóa thật.
   - Kiểm tra các ràng buộc trước khi xóa (ví dụ: không thể xóa admin duy nhất của hệ thống).
   - Xử lý trường hợp user không tồn tại.
   - Format response success/error.

6. **Triển khai Validation và Error Handling chung**:
   - Sử dụng Validation module để xác thực input.
   - Xử lý các trường hợp lỗi đặc biệt:
     - Validation errors
     - Duplicate resources
     - Not found errors
     - Permission errors
   - Đảm bảo thông báo lỗi rõ ràng và nhất quán.

7. **Triển khai Logging và Auditing**:
   - Ghi log cho mọi thao tác quản lý user (create, update, delete).
   - Lưu thông tin chi tiết về người thực hiện thao tác, thời gian, và nội dung thay đổi.
   - Đảm bảo masking thông tin nhạy cảm trong log.

## Đầu ra dự kiến
- 5 Lambda handler functions cho các User Management APIs.
- Validation schemas cho request body.
- Cơ chế xử lý lỗi nhất quán và chi tiết.
- Unit tests cho mỗi API với các use cases khác nhau.
- Logging và auditing cho các thao tác quản lý user.
- Documentation về cách sử dụng APIs và cấu trúc request/response. 