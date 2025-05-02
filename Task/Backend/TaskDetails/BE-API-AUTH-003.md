# Task BE-API-AUTH-003: Triển khai Get Current User API (API-AUTH-003)

## Thông tin chung
- **Độ ưu tiên**: Cao
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
    - [API-AUTH-003: Get Current User API](../../../DD/API/API-AUTH-003.md)
  - Các API liên quan:
    - [API-AUTH-001: Login API](../../../DD/API/API-AUTH-001.md)
  - Task liên quan:
    - [BE-API-AUTH-001: Triển khai Login API](BE-API-AUTH-001.md)
  - Yêu cầu chức năng: 
    - [F-6.1: Quản lý Người dùng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API Get Current User để lấy thông tin người dùng hiện tại và danh sách quyền của họ dựa trên JWT token. API này rất quan trọng cho hầu hết các màn hình của ứng dụng để xác định người dùng đang đăng nhập và các quyền mà họ sở hữu.

## Chi tiết công việc
1. **Tạo Lambda Handler Function cho Get Current User API**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/auth/me` với method GET.
   - Cấu hình API Gateway cho route này (qua serverless.yml hoặc framework tương ứng).
   - Đảm bảo endpoint này yêu cầu authentication (cần JWT token hợp lệ).

2. **Triển khai JWT Middleware/Validation**:
   - Sử dụng JWT middleware từ module Authentication & Authorization để xác thực token.
   - Giải mã token để lấy thông tin user_id và các thông tin cơ bản.
   - Xử lý các trường hợp token không hợp lệ, hết hạn.

3. **Truy vấn Thông tin User**:
   - Dựa vào user_id từ token, truy vấn đầy đủ thông tin user từ database.
   - Sử dụng Database Connection module để tối ưu truy vấn.
   - Đảm bảo xử lý các trường hợp user không tồn tại hoặc bị vô hiệu hóa.

4. **Truy vấn Thông tin Quyền và Vai trò**:
   - Lấy danh sách vai trò (roles) của user.
   - Tính toán và tổng hợp tất cả các quyền (permissions) từ các vai trò đó.
   - Đảm bảo tối ưu truy vấn để tránh N+1 query problem.

5. **Tối ưu hóa Response**:
   - Cấu trúc response bao gồm thông tin user và danh sách đầy đủ các quyền.
   - Đảm bảo không trả về thông tin nhạy cảm (password hash, private data).
   - Format response đúng theo API spec.
   - Tạo cơ chế cache (nếu cần) để tối ưu hiệu năng.

6. **Triển khai Logging**:
   - Ghi log các lần truy cập API với mức độ phù hợp.
   - Đảm bảo masking dữ liệu nhạy cảm trong log.

7. **Triển khai Error Handling**:
   - Xử lý và trả về error responses phù hợp cho các trường hợp lỗi khác nhau.
   - Xử lý trường hợp token hết hạn với thông báo cụ thể.
   - Đảm bảo thông báo lỗi đủ chi tiết cho client.

## Đầu ra dự kiến
- Lambda handler function hoàn chỉnh cho Get Current User API.
- Cơ chế truy vấn thông tin user và permissions tối ưu.
- Cấu trúc response chuẩn bao gồm user info và permissions.
- Xử lý lỗi và response format chuẩn.
- Unit tests cho các use cases khác nhau (token hợp lệ, token không hợp lệ, v.v.).
- Documentation về cấu trúc response và cách sử dụng API. 