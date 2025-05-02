# Task BE-CORE-002: Xây dựng module Authentication & Authorization

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 5 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
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
    - [F-1.2: Phân quyền Truy cập Thông tin Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
  - Yêu cầu phi chức năng: NF-5, NF-6, NF-7, NF-8 (Bảo mật hệ thống)

## Mô tả
Xây dựng module Authentication & Authorization để quản lý việc xác thực người dùng, tạo và kiểm tra JWT token, cùng với hệ thống phân quyền (Role-Based Access Control) cho các API endpoint.

## Chi tiết công việc
1. **Triển khai API Login, Logout và Get Current User**:
   - Triển khai API-AUTH-001: Login API - Xử lý request với username/password, validate thông tin đăng nhập, tạo và trả về JWT token.
   - Triển khai API-AUTH-002: Logout API - Xử lý hủy token/session và cập nhật trạng thái đăng nhập.
   - Triển khai API-AUTH-003: Get Current User API - Lấy thông tin user hiện tại từ token và danh sách các quyền hiện có.

2. **Xây dựng middleware JWT authentication**:
   - Tạo middleware để xác thực JWT token trong request header.
   - Giải mã và kiểm tra tính hợp lệ của token (signature, expiration).
   - Xử lý các trường hợp lỗi (token hết hạn, không hợp lệ, v.v.).
   - Gắn thông tin user vào request context để sử dụng trong các handler tiếp theo.

3. **Triển khai hệ thống phân quyền (Role-Based Access Control)**:
   - Xây dựng cơ chế Role và Permission mapping.
   - Tạo decorator/middleware để kiểm tra quyền truy cập vào API.
   - Triển khai business logic cho việc kiểm tra quyền truy cập dựa trên vai trò.
   - Hỗ trợ kiểm tra quyền phức tạp (ví dụ: chỉ xem được dữ liệu của team mình).

4. **Triển khai các utility functions liên quan đến bảo mật**:
   - Tạo các functions cho password hashing và verification.
   - Utility cho JWT token generation và validation.
   - Các functions để kiểm tra phân quyền trong các tình huống khác nhau.

5. **Cấu hình bảo mật cho API Gateway và Lambda**:
   - Cấu hình CORS policy phù hợp.
   - Triển khai rate limiting để ngăn chặn brute force attack.
   - Cấu hình các HTTP security headers.

## Đầu ra dự kiến
- Module Authentication & Authorization hoàn chỉnh với các tính năng nêu trên.
- Middleware JWT authentication có thể sử dụng cho tất cả API endpoints.
- Hệ thống RBAC linh hoạt và dễ mở rộng.
- Unit tests cho các chức năng chính.
- Documentation cho việc sử dụng module này.

## Tài liệu tham khảo
- [JWT.io Documentation](https://jwt.io/introduction)
- [OWASP Authentication Best Practices](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [AWS Lambda Security Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/security-best-practices.html)
- [AWS API Gateway Authorization](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html)
- [Python Passlib Documentation](https://passlib.readthedocs.io/en/stable/)
- [Flask-Security Documentation](https://flask-security-too.readthedocs.io/en/stable/) 