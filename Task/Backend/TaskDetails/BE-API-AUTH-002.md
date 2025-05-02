# Task BE-API-AUTH-002: Triển khai Logout API (API-AUTH-002)

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 1 ngày
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
    - [API-AUTH-002: Logout API](../../../DD/API/API-AUTH-002.md)
  - Các API liên quan:
    - [API-AUTH-001: Login API](../../../DD/API/API-AUTH-001.md)
  - Task liên quan:
    - [BE-API-AUTH-001: Triển khai Login API](BE-API-AUTH-001.md)
  - Yêu cầu chức năng: 
    - [F-6.1: Quản lý Người dùng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API Logout cho hệ thống, cho phép người dùng đăng xuất khỏi hệ thống một cách an toàn. API này sẽ vô hiệu hóa JWT token hiện tại và cập nhật trạng thái đăng nhập của người dùng.

## Chi tiết công việc
1. **Tạo Lambda Handler Function cho Logout API**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/auth/logout` với method POST.
   - Cấu hình API Gateway cho route này (qua serverless.yml hoặc framework tương ứng).
   - Đảm bảo endpoint này yêu cầu authentication (cần JWT token hợp lệ).

2. **Triển khai JWT Middleware/Validation**:
   - Sử dụng JWT middleware từ module Authentication & Authorization để xác thực token.
   - Xử lý các trường hợp token không hợp lệ, hết hạn, hoặc không tồn tại.

3. **Triển khai Session Management**:
   - Nếu sử dụng UserSession, cập nhật trạng thái session của user thành inactive/logged_out.
   - Xử lý việc lưu trữ token vào blacklist (nếu cần) để ngăn sử dụng lại.
   - Cập nhật thời gian đăng xuất và thông tin liên quan.

4. **Triển khai Response Handling**:
   - Trả về thông báo thành công khi logout hoàn tất.
   - Format response đúng theo API spec.

5. **Triển khai Logging và Auditing**:
   - Ghi log cho mỗi lần logout thành công.
   - Lưu thông tin về user, thời gian, device (nếu có).
   - Đảm bảo masking dữ liệu nhạy cảm trong log.

6. **Xử lý các trường hợp đặc biệt**:
   - Xử lý việc logout từ tất cả thiết bị (nếu có tham số force_all=true).
   - Xử lý trường hợp token đã hết hạn nhưng user vẫn muốn logout.
   - Đảm bảo idempotency (gọi API nhiều lần không gây lỗi).

7. **Triển khai Error Handling**:
   - Xử lý và trả về error responses phù hợp cho các trường hợp lỗi khác nhau.
   - Đảm bảo thông báo lỗi đủ chi tiết cho client.

## Đầu ra dự kiến
- Lambda handler function hoàn chỉnh cho Logout API.
- Cơ chế vô hiệu hóa JWT token (blacklist hoặc cập nhật session).
- Xử lý lỗi và response format chuẩn.
- Unit tests cho các use cases khác nhau (logout thành công, token không hợp lệ, v.v.).
- Logging và auditing cho security tracking. 