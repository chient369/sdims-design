# Task BE-API-AUTH-001: Triển khai Login API (API-AUTH-001)

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 2 ngày
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
    - [API-AUTH-001: Login API](../../../DD/API/API-AUTH-001.md)
  - Yêu cầu chức năng: 
    - [F-6.1: Quản lý Người dùng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API Login cho hệ thống, cho phép người dùng xác thực bằng username/password và nhận về JWT token để sử dụng cho các request tiếp theo. API này là cơ sở cho toàn bộ hệ thống authentication của ứng dụng.

## Chi tiết công việc
1. **Tạo Lambda Handler Function cho Login API**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/auth/login` với method POST.
   - Cấu hình API Gateway cho route này (qua serverless.yml hoặc framework tương ứng).
   - Đảm bảo endpoint này không yêu cầu authentication.

2. **Triển khai Validation Logic**:
   - Sử dụng Pydantic để tạo schema validate cho request body:
     - username/email (required)
     - password (required)
     - remember_me (optional, boolean)
   - Kiểm tra và xử lý lỗi validation, trả về response phù hợp.

3. **Triển khai Authentication Logic**:
   - Kiểm tra thông tin đăng nhập trong database (User model).
   - Sử dụng mã hóa an toàn (bcrypt/argon2) để xác nhận password.
   - Xử lý các trường hợp tài khoản không tồn tại, mật khẩu sai, tài khoản bị khóa.
   - Giới hạn số lần đăng nhập thất bại (optional).

4. **Tạo JWT Token**:
   - Sau khi xác thực thành công, tạo JWT token chứa:
     - User ID
     - Username
     - Role(s)
     - Permissions array
     - Issue time, expiry time (tùy thuộc vào remember_me)
   - Ký token với secret key được cấu hình an toàn.
   - Cấu hình thời gian hết hạn của token (vd: 24h hoặc 7 ngày nếu remember_me=true).

5. **Triển khai User Session Tracking (nếu cần)**:
   - Lưu thông tin session vào UserSession model.
   - Ghi nhận thông tin device, IP, thời gian đăng nhập.
   - Cập nhật last_login trên User model.

6. **Triển khai Logging và Auditing**:
   - Ghi log cho mọi lần đăng nhập (thành công và thất bại).
   - Đảm bảo log chứa đủ thông tin để debug và theo dõi bảo mật.
   - Masking dữ liệu nhạy cảm trong log.

7. **Tối ưu hóa Response**:
   - Trả về JWT token và thông tin user cơ bản sau khi đăng nhập thành công.
   - Đảm bảo không trả về thông tin nhạy cảm của user.
   - Format response đúng theo API spec.

8. **Triển khai Error Handling**:
   - Xử lý và trả về error responses phù hợp cho các trường hợp lỗi khác nhau.
   - Đảm bảo thông báo lỗi đủ chi tiết cho client, nhưng không lộ thông tin nhạy cảm.

## Đầu ra dự kiến
- Lambda handler function hoàn chỉnh cho Login API.
- Validation schema cho request body.
- Cấu hình JWT đúng tiêu chuẩn bảo mật.
- Xử lý lỗi và response format chuẩn.
- Unit tests cho các use cases khác nhau (đăng nhập thành công, thất bại, validation errors).
- Logging và auditing cho security tracking. 