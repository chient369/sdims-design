# Task Detail: FE-ADM-002

## Thông tin chung

- **Task ID:** FE-ADM-002
- **Task Name:** Xây dựng Role & Permission Management Page (MH-ADM-02)
- **Priority:** Medium
- **Estimated Effort:** 6 ngày
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
  - FE-ADM-001: Xây dựng User Management Page

## Mô tả Task

Xây dựng màn hình quản lý vai trò và phân quyền (MH-ADM-02) cho hệ thống SDIMS, cho phép Admin định nghĩa các vai trò (roles) trong hệ thống và gán quyền truy cập chi tiết cho từng vai trò. Màn hình này là một phần quan trọng của hệ thống phân quyền RBAC (Role-Based Access Control) và chỉ được truy cập bởi người dùng có quyền Admin.

## Yêu cầu chi tiết

### 1. UI/UX

- Thiết kế màn hình quản lý vai trò và phân quyền với hai tab chính:
  - Tab "Vai trò" (Roles): Hiển thị danh sách vai trò hiện có
  - Tab "Quyền hạn" (Permissions): Hiển thị danh sách quyền hạn hệ thống (chỉ xem)
- Tab "Vai trò" bao gồm:
  - Danh sách vai trò dạng bảng với thông tin: Tên vai trò, Mô tả, Số người dùng, Ngày cập nhật, Actions
  - Nút "Thêm vai trò mới"
  - Chức năng tìm kiếm vai trò
- Tab "Quyền hạn" bao gồm:
  - Danh sách quyền hạn được nhóm theo module/function
  - Mô tả chi tiết từng quyền
  - Filter theo module/nhóm quyền
- Modal thêm/sửa vai trò với form nhập thông tin và matrix gán quyền
- Responsive design ưu tiên cho desktop (do tính chất admin tool)

### 2. Chức năng quản lý vai trò

#### 2.1. Xem danh sách vai trò
- Lấy dữ liệu từ API `/api/v1/admin/roles`
- Hiển thị dạng bảng với pagination
- Hỗ trợ tìm kiếm theo tên vai trò
- Hiển thị thông tin về số người dùng đang sử dụng mỗi vai trò

#### 2.2. Thêm vai trò mới
- Form thêm vai trò với các trường:
  - Tên vai trò (bắt buộc, unique)
  - Mô tả (optional)
  - Matrix gán quyền (xem mục 3)
- Validation đầy đủ cho tất cả các trường
- Gửi dữ liệu đến API `/api/v1/admin/roles` (POST)
- Hiển thị thông báo kết quả và cập nhật danh sách

#### 2.3. Chỉnh sửa vai trò
- Form tương tự thêm mới, nhưng đã điền sẵn thông tin hiện tại
- Gửi dữ liệu đến API `/api/v1/admin/roles/{roleId}` (PUT)
- Cập nhật danh sách sau khi sửa thành công

#### 2.4. Xóa vai trò
- Dialog xác nhận trước khi xóa
- Kiểm tra và cảnh báo nếu vai trò đang được gán cho người dùng
- Gửi request đến API `/api/v1/admin/roles/{roleId}` (DELETE)
- Cập nhật danh sách sau khi xóa thành công

### 3. Chức năng gán quyền cho vai trò

#### 3.1. Matrix gán quyền
- Thiết kế matrix gán quyền dạng bảng với:
  - Các nhóm quyền (module) theo cột dọc
  - Các loại hành động (view, create, update, delete, etc.) theo hàng ngang
  - Checkbox tại mỗi giao điểm
- Hỗ trợ chức năng:
  - Check/uncheck tất cả quyền trong một module
  - Check/uncheck tất cả quyền của một hành động
  - Hiển thị thông tin mô tả khi hover vào quyền
- Tùy chọn hiển thị matrix dạng cây (tree-view) hoặc dạng bảng (table-view)

#### 3.2. Lấy dữ liệu quyền hạn
- Lấy danh sách quyền từ API `/api/v1/admin/permissions`
- Nhóm và hiển thị theo cấu trúc hợp lý
- Hiển thị các quyền đã được chọn cho vai trò khi chỉnh sửa

#### 3.3. Lưu cấu hình quyền
- Gửi danh sách quyền đã chọn khi thêm/sửa vai trò
- Format dữ liệu phù hợp với yêu cầu API
- Xử lý logic dependencies giữa các quyền (nếu có)

### 4. Xem danh sách quyền hệ thống

- Hiển thị danh sách tất cả quyền hệ thống theo nhóm/module
- Cung cấp mô tả chi tiết cho từng quyền
- Hỗ trợ tìm kiếm và lọc theo module
- Chỉ cho phép xem, không cho phép thêm/sửa/xóa quyền (quyền được định nghĩa bởi backend)

### 5. Phân quyền và bảo mật

- Chỉ người dùng có quyền "role:manage" mới truy cập được màn hình này
- Đảm bảo không thể thay đổi các vai trò đặc biệt (SuperAdmin, v.v.) nếu không có quyền
- Hiển thị cảnh báo khi thực hiện các thay đổi quan trọng

## Technical Requirements

### 1. State Management

- Quản lý state phức tạp của matrix phân quyền
- Sử dụng React Query hoặc Context API từ FE-INFRA-005
- Xử lý hiệu quả dependencies giữa các quyền

### 2. API Integration

- Sử dụng axios client từ FE-INFRA-006
- Xử lý và hiển thị lỗi từ API
- Caching danh sách quyền để tối ưu performance

### 3. Components

- Sử dụng Table components từ FE-CORE-004
- Sử dụng Modal/Dialog components từ FE-CORE-005
- Sử dụng Form components từ FE-CORE-003
- Xây dựng custom PermissionMatrix component cho matrix phân quyền

### 4. Testing

- Unit tests cho logic phân quyền và form validation
- Integration tests cho các flow chính
- Kiểm tra các edge cases và validation

## Acceptance Criteria

1. Admin có thể xem danh sách tất cả vai trò trong hệ thống
2. Admin có thể thêm vai trò mới với tên và mô tả
3. Admin có thể chỉnh sửa thông tin vai trò hiện có
4. Admin có thể xóa vai trò (nếu không được sử dụng)
5. Admin có thể gán quyền cho vai trò thông qua matrix gán quyền trực quan
6. Admin có thể xem danh sách tất cả quyền trong hệ thống và mô tả
7. Matrix gán quyền hỗ trợ check/uncheck nhóm quyền
8. System alerts khi có thay đổi quan trọng (ảnh hưởng đến security)
9. Chỉ người dùng có quyền phù hợp mới truy cập được màn hình
10. UI hoạt động mượt mà, không bị lag khi làm việc với matrix quyền lớn

## Documentation

- Thêm mô tả về cấu trúc permission matrix component
- Thêm mô tả về logic dependencies giữa các quyền (nếu có)
- Thêm hướng dẫn sử dụng màn hình cho admin

## Resources

- [API Role & Permission Management Documentation](../../BD/API/api_list.md#7-quản-trị-hệ-thống-admin)
- [API-ADM-006: Lấy danh sách các vai trò](../../DD/API/API-ADM-006.md)
- [API-ADM-007: Tạo vai trò mới](../../DD/API/API-ADM-007.md)
- [API-ADM-008: Cập nhật vai trò (gán/gỡ quyền)](../../DD/API/API-ADM-008.md)
- [API-ADM-009: Xóa vai trò](../../DD/API/API-ADM-009.md)
- [API-ADM-010: Lấy danh sách các quyền](../../DD/API/API-ADM-010.md)
- [Chức năng liên quan: F-6.2 (Quản lý vai trò & phân quyền)](../../BD/FunctionDesign/FunctionList.md)
- [Thiết kế màn hình: MH-ADM-02 - Quản lý Vai trò & Phân quyền](../../BD/ScreenDesign/ScreenBD.md#mh-adm-02)
- [Screen Design Diagram: MH-ADM-02](../../BD/ScreenDesign/diagram/MH-ADM-02.puml)
- [Screen Flow Overview](../../BD/ScreenDesign/ScreenBD.md#2-luồng-di-chuyển-chính-high-level-flow) 