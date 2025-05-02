# Task BE-API-ADM-002: Triển khai Role Management APIs

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 4 ngày
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
    - [API-ADM-006: Get Roles List API](../../../DD/API/API-ADM-006.md)
    - [API-ADM-007: Create Role API](../../../DD/API/API-ADM-007.md)
    - [API-ADM-008: Update Role API](../../../DD/API/API-ADM-008.md)
    - [API-ADM-009: Delete Role API](../../../DD/API/API-ADM-009.md)
    - [API-ADM-010: Get Permissions List API](../../../DD/API/API-ADM-010.md)
  - Tasks liên quan:
    - [BE-API-ADM-001: Triển khai User Management APIs](BE-API-ADM-001.md)
  - Yêu cầu chức năng: 
    - [F-6.2: Quản lý Vai trò & Phân quyền](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai các API để quản lý vai trò (Roles) và phân quyền (Permissions) trong hệ thống, bao gồm xem danh sách vai trò, tạo vai trò mới, cập nhật vai trò, xóa vai trò, và lấy danh sách các quyền. Các API này chỉ dành cho Admin và là cơ sở cho hệ thống phân quyền RBAC của ứng dụng.

## Chi tiết công việc
1. **Triển khai API Get Roles List (API-ADM-006)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/roles` với method GET.
   - Triển khai logic truy vấn danh sách roles với các tính năng:
     - Phân trang (page, limit)
     - Sắp xếp (sort_by, sort_order)
     - Tìm kiếm (search_term)
   - Truy vấn tối ưu kèm số lượng users và permissions cho mỗi role.
   - Đảm bảo kiểm tra quyền (chỉ Admin mới được truy cập).
   - Format response kèm thông tin pagination metadata.

2. **Triển khai API Create Role (API-ADM-007)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/roles` với method POST.
   - Triển khai validation schema cho request body:
     - name (required, unique)
     - description (optional)
     - permissions (array of permission_ids)
   - Xử lý tạo role mới trong database:
     - Kiểm tra name không trùng lặp
     - Gán các permissions được chọn
   - Format response với thông tin role đã tạo.
   - Đảm bảo consistency khi có lỗi (transaction nếu cần).

3. **Triển khai API Update Role (API-ADM-008)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/roles/{roleId}` với method PUT.
   - Triển khai validation schema cho request body:
     - name (optional, unique)
     - description (optional)
     - permissions (array of permission_ids)
   - Xử lý cập nhật thông tin role trong database:
     - Cập nhật name, description nếu có
     - Xóa tất cả permissions hiện tại và gán lại các permissions mới (hoặc sử dụng method hiệu quả hơn)
   - Xử lý các trường hợp đặc biệt:
     - Không thể sửa đổi role mặc định của hệ thống (nếu có)
     - Đảm bảo có ít nhất một role với quyền admin
   - Format response với thông tin role đã cập nhật.
   - Đảm bảo consistency khi có lỗi (transaction nếu cần).

4. **Triển khai API Delete Role (API-ADM-009)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/roles/{roleId}` với method DELETE.
   - Kiểm tra các ràng buộc trước khi xóa:
     - Không thể xóa role mặc định của hệ thống
     - Cảnh báo hoặc ngăn chặn xóa role đang được gán cho nhiều user
     - Đảm bảo có ít nhất một role với quyền admin
   - Xử lý xóa role và các liên kết (role_permissions).
   - Format response success/error.
   - Đảm bảo consistency khi có lỗi (transaction nếu cần).

5. **Triển khai API Get Permissions List (API-ADM-010)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/permissions` với method GET.
   - Triển khai logic truy vấn danh sách permissions với các tính năng:
     - Lọc theo resource hoặc action nếu cần
     - Sắp xếp theo resource/action
   - Tổ chức permissions theo resource groups để dễ hiển thị trên UI.
   - Format response với cấu trúc rõ ràng cho frontend sử dụng trong form quản lý roles.

6. **Triển khai Validation và Error Handling chung**:
   - Sử dụng Validation module để xác thực input.
   - Xử lý các trường hợp lỗi đặc biệt:
     - Validation errors
     - Duplicate resources
     - Not found errors
     - Permission errors
     - Constraint violation errors
   - Đảm bảo thông báo lỗi rõ ràng và nhất quán.

7. **Triển khai Logging và Auditing**:
   - Ghi log cho mọi thao tác quản lý roles (create, update, delete).
   - Lưu thông tin chi tiết về người thực hiện thao tác, thời gian, và nội dung thay đổi.
   - Đặc biệt quan trọng với những thay đổi về phân quyền ảnh hưởng đến bảo mật hệ thống.

## Đầu ra dự kiến
- 5 Lambda handler functions cho các Role Management APIs.
- Validation schemas cho request body.
- Cơ chế xử lý lỗi nhất quán và chi tiết.
- Transaction logic để đảm bảo data consistency.
- Business rules cho role management (ràng buộc, kiểm tra tính hợp lệ).
- Unit tests cho mỗi API với các use cases khác nhau.
- Logging và auditing cho các thao tác quản lý roles.
- Documentation về cách sử dụng APIs và cấu trúc request/response. 