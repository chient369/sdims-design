# Task Detail: FE-AUTH-001

## Thông tin chung

- **Task ID:** FE-AUTH-001
- **Task Name:** Xây dựng Login Page (MH-AUTH-01)
- **Priority:** High
- **Estimated Effort:** 3 ngày
- **Prerequisites:** 
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-003: Thiết lập router
  - FE-INFRA-004: Xây dựng theme và design system
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-001: Xây dựng Layout components (AuthLayout)
  - FE-CORE-003: Xây dựng Form components

## Mô tả Task

Xây dựng màn hình đăng nhập (MH-AUTH-01) cho hệ thống SDIMS, cho phép người dùng nhập thông tin đăng nhập, xử lý các trường hợp lỗi, và lưu token xác thực sau khi đăng nhập thành công.

## Yêu cầu chi tiết

### 1. UI/UX

- Thiết kế form đăng nhập đơn giản, sạch sẽ với logo công ty ở phía trên
- Các trường nhập liệu:
  - Username/Email (text input)
  - Password (password input với toggle hiển thị/ẩn mật khẩu)
  - Nút "Remember me" (checkbox)
  - Nút "Đăng nhập" (primary button)
  - Link "Quên mật khẩu" (optional - phụ thuộc requirements)
- Hiển thị loading indicator khi đang xử lý đăng nhập
- Responsive design: hoạt động tốt trên cả desktop, tablet và mobile
- Sử dụng AuthLayout từ FE-CORE-001

### 2. Form Validation

- Username/Email:
  - Bắt buộc nhập
  - Hợp lệ theo định dạng email (hoặc username, tùy vào yêu cầu API)
- Password:
  - Bắt buộc nhập
  - Độ dài tối thiểu (theo quy định hệ thống)
- Hiển thị message lỗi validation ngay dưới field tương ứng
- Sử dụng form components đã xây dựng từ FE-CORE-003
- Validation sử dụng React Hook Form kết hợp với Zod validation schema

### 3. Authentication Logic

- Kết nối đến API xác thực `/api/v1/auth/login`
- Xử lý các trường hợp lỗi:
  - Thông tin đăng nhập không chính xác
  - Tài khoản bị khóa
  - Lỗi kết nối server
  - Các lỗi khác từ API
- Hiển thị thông báo lỗi phù hợp cho từng trường hợp
- Lưu JWT token nhận được vào:
  - LocalStorage (nếu người dùng chọn "Remember me")
  - SessionStorage (nếu không chọn "Remember me")
- Lưu thông tin user cơ bản vào state management (Context/Redux)

### 4. Post-login Flow

- Sau khi đăng nhập thành công, lấy thông tin user và quyền hạn từ API `/api/v1/auth/me`
- Chuyển hướng người dùng đến:
  - Trang được yêu cầu trước khi chuyển đến login (nếu có)
  - Dashboard (trang mặc định)
- Lưu thông tin điều hướng trước login để có thể quay lại sau khi đăng nhập

### 5. Security Requirements

- Không lưu password trong bất kỳ state/storage nào
- Đảm bảo xóa form data sau khi submit thành công
- Implement CSRF protection nếu cần
- Giới hạn số lần đăng nhập thất bại (optional)

## Technical Requirements

### 1. State Management

- Sử dụng AuthContext từ FE-INFRA-005 để quản lý trạng thái authentication
- Lưu trữ JWT token, thông tin user, và trạng thái logged-in
- Cung cấp các hàm login, logout, và checkAuthStatus

### 2. API Integration

- Sử dụng axios client từ FE-INFRA-006
- Thêm interceptor cho các API call yêu cầu auth
- Thêm refresh token logic nếu cần

### 3. Routing

- Sử dụng React Router từ FE-INFRA-003
- Thiết lập protected routes ngăn truy cập khi chưa login
- Handle redirect sau khi login/logout

### 4. Testing

- Unit tests cho form validation logic
- Unit tests cho auth state management
- Integration tests cho login flow

## Acceptance Criteria

1. Người dùng có thể truy cập trang đăng nhập
2. Form validation hoạt động chính xác cho mọi trường hợp
3. Hiển thị lỗi phù hợp khi đăng nhập thất bại
4. Lưu token và thông tin user sau khi đăng nhập thành công
5. Chuyển hướng đến trang phù hợp sau khi đăng nhập
6. Responsive design hoạt động tốt trên các device khác nhau
7. Các unit tests và integration tests pass

## Documentation

- Thêm mô tả về cấu trúc component
- Thêm mô tả về auth flow
- Thêm mô tả về cách sử dụng authentication context/hooks

## Resources
- [Screen Design](../../../assets/frontend/img/MH-AUTH-01.png)
- [API Login Documentation](../../BD/API/api_list.md#1-authentication--authorization)
- [API-AUTH-001: Xác thực người dùng và trả về token (JWT)](../../DD/API/API-AUTH-001.md)
- [API-AUTH-003: Lấy thông tin user và quyền người dùng](../../DD/API/API-AUTH-003.md)
- [Chức năng liên quan: F-6.1, F-6.2 (Quản lý người dùng và phân quyền)](../../BD/FunctionDesign/FunctionList.md)
- [Thiết kế màn hình: MH-AUTH-01 - Đăng nhập](../../BD/ScreenDesign/ScreenBD.md#mh-auth-01)
- [Screen Design Diagram: MH-AUTH-01](../../BD/ScreenDesign/diagram/MH-AUTH-01.puml)
- [Screen Flow Overview](../../BD/ScreenDesign/ScreenBD.md#2-luồng-di-chuyển-chính-high-level-flow) 