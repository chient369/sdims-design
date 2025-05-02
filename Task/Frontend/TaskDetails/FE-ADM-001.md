# Task Detail: FE-ADM-001

## Thông tin chung

- **Task ID:** FE-ADM-001
- **Task Name:** Xây dựng User Management Page (MH-ADM-01)
- **Priority:** Medium
- **Estimated Effort:** 5 ngày
- **Prerequisites:** 
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-003: Thiết lập router
  - FE-INFRA-004: Xây dựng theme và design system
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-001: Xây dựng Layout components
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components
  - FE-CORE-005: Xây dựng Modal và Dialog components
  - FE-CORE-009: Xây dựng Authorization components
  - FE-AUTH-001: Xây dựng Login Page

## Mô tả Task

Xây dựng màn hình quản lý người dùng (MH-ADM-01) cho hệ thống SDIMS, cho phép Admin xem danh sách, thêm mới, chỉnh sửa, và quản lý trạng thái của người dùng trong hệ thống. Màn hình này chỉ được truy cập bởi người dùng có quyền Admin.

## Yêu cầu chi tiết

### 1. UI/UX

- Thiết kế màn hình quản lý người dùng với hai phần chính:
  - Phần trên: Thanh search/filter và nút "Thêm người dùng mới"
  - Phần dưới: Bảng danh sách người dùng với pagination
- Bảng danh sách người dùng hiển thị các thông tin:
  - ID/Mã người dùng
  - Avatar (nếu có)
  - Họ tên
  - Email
  - Department/Team
  - Vai trò/Roles (hiển thị dạng tags)
  - Trạng thái (Active/Inactive - hiển thị với màu sắc khác nhau)
  - Ngày tạo/cập nhật gần nhất
  - Actions (Sửa, Khóa/Mở khóa, Reset mật khẩu)
- Modal thêm/sửa thông tin người dùng
- Dialog xác nhận cho các hành động quan trọng (khóa tài khoản, reset mật khẩu)
- Responsive design cho desktop và tablet (mobile không bắt buộc do tính chất admin tool)

### 2. Chức năng tìm kiếm và lọc

- Tìm kiếm theo tên, email, ID
- Lọc theo:
  - Trạng thái (Active/Inactive/All)
  - Vai trò (Admin, Manager, User, v.v.)
  - Department/Team
- Pagination với các tùy chọn số item trên trang (10, 20, 50)
- Sắp xếp theo các cột (tên, ngày tạo, vai trò, v.v.)

### 3. Quản lý người dùng

#### 3.1. Xem danh sách người dùng
- Lấy dữ liệu từ API `/api/v1/admin/users`
- Hỗ trợ pagination, sorting, và filtering
- Hiển thị loading state khi đang tải dữ liệu
- Xử lý các trường hợp lỗi và trạng thái "không có dữ liệu"

#### 3.2. Thêm người dùng mới
- Form thêm người dùng với các trường:
  - Thông tin cơ bản (Họ tên, Email, Username, v.v.)
  - Department/Team
  - Vai trò (multi-select)
  - Mật khẩu ban đầu (hoặc tự động generate)
  - Trạng thái mặc định
- Validation đầy đủ cho tất cả các trường
- Gửi dữ liệu đến API `/api/v1/admin/users` (POST)
- Hiển thị thông báo thành công/thất bại phù hợp

#### 3.3. Chỉnh sửa thông tin người dùng
- Form tương tự thêm mới, nhưng đã điền sẵn thông tin hiện tại
- Không hiển thị/cho phép thay đổi mật khẩu trong form sửa
- Gửi dữ liệu đến API `/api/v1/admin/users/{userId}` (PUT)
- Cập nhật danh sách sau khi sửa thành công

#### 3.4. Khóa/Mở khóa tài khoản
- Dialog xác nhận trước khi thực hiện
- Gửi request đến API `/api/v1/admin/users/{userId}` (PUT) với trạng thái mới
- Cập nhật UI sau khi thay đổi thành công

#### 3.5. Reset mật khẩu
- Dialog xác nhận với các tùy chọn:
  - Reset về mật khẩu mặc định
  - Generate mật khẩu mới và hiển thị/gửi email
- Gửi request đến API thích hợp
- Hiển thị thông báo kết quả

### 4. Phân quyền và bảo mật

- Chỉ người dùng có quyền "user:manage" mới được truy cập màn hình này
- Sử dụng Authorization components từ FE-CORE-009 để kiểm tra quyền
- Không hiển thị các action không được phép
- Bảo vệ API routes với interceptors

## Technical Requirements

### 1. State Management

- Quản lý state của người dùng (danh sách, filter, pagination)
- Sử dụng React Query hoặc Context API từ FE-INFRA-005
- Optimistic updates cho UX tốt hơn

### 2. API Integration

- Sử dụng axios client từ FE-INFRA-006
- Xử lý và hiển thị lỗi từ API
- Caching và invalidation phù hợp

### 3. Components

- Sử dụng Table components từ FE-CORE-004
- Sử dụng Modal/Dialog components từ FE-CORE-005
- Sử dụng Form components từ FE-CORE-003

### 4. Testing

- Unit tests cho form validation và business logic
- Integration tests cho các main user flows
- Kiểm tra bảo mật và phân quyền

## Acceptance Criteria

1. Admin có thể xem danh sách người dùng với đầy đủ thông tin
2. Hệ thống hỗ trợ tìm kiếm, lọc, phân trang hiệu quả
3. Admin có thể thêm người dùng mới với đầy đủ thông tin
4. Admin có thể chỉnh sửa thông tin người dùng hiện có
5. Admin có thể khóa/mở khóa tài khoản người dùng
6. Admin có thể reset mật khẩu cho người dùng
7. Chỉ người dùng có quyền phù hợp mới truy cập được màn hình
8. Tất cả form validation đều hoạt động chính xác
9. Responsive design cho desktop và tablet
10. Tất cả API calls và error handling đúng

## Resources

- [API User Management Documentation](../../BD/API/api_list.md#7-quản-trị-hệ-thống-admin)
- [API-ADM-001: Lấy danh sách người dùng hệ thống](../../DD/API/API-ADM-001.md)
- [API-ADM-002: Tạo người dùng mới](../../DD/API/API-ADM-002.md)
- [API-ADM-003: Lấy chi tiết người dùng](../../DD/API/API-ADM-003.md)
- [API-ADM-004: Cập nhật thông tin người dùng](../../DD/API/API-ADM-004.md)
- [API-ADM-005: Xóa người dùng](../../DD/API/API-ADM-005.md)
- [Chức năng liên quan: F-6.1 (Quản lý người dùng)](../../BD/FunctionDesign/FunctionList.md)
- [Thiết kế màn hình: MH-ADM-01 - Quản lý Người dùng](../../BD/ScreenDesign/ScreenBD.md#mh-adm-01)
- [Screen Design Diagram: MH-ADM-01](../../BD/ScreenDesign/diagram/MH-ADM-01.puml)
- [Screen Flow Overview](../../BD/ScreenDesign/ScreenBD.md#2-luồng-di-chuyển-chính-high-level-flow) 