# Task Detail: FE-HRM-004

## Thông tin chung
- **ID**: FE-HRM-004
- **Tên task**: Xây dựng Skills Management UI (MH-HRM-04)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-02](../../../assets/frontend/img/MH-HRM-04.png),
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-04.md](../../../DD/SCREEN/MH-HRM-04.md)
  - API: [API-HRM-009](../../../DD/API/API-HRM-009.md), [API-HRM-010](../../../DD/API/API-HRM-010.md), [API-HRM-011](../../../DD/API/API-HRM-011.md), [API-HRM-012](../../../DD/API/API-HRM-012.md), [API-HRM-013](../../../DD/API/API-HRM-013.md), [API-HRM-014](../../../DD/API/API-HRM-014.md), [API-HRM-015](../../../DD/API/API-HRM-015.md), [API-HRM-016](../../../DD/API/API-HRM-016.md)

## Mô tả
Xây dựng giao diện quản lý danh mục skills và kỹ năng chuyên môn cho hệ thống. Màn hình này chỉ dành cho Admin và Team Leader, cho phép quản lý các loại kỹ năng (skill categories) và kỹ năng (skills) cụ thể. Danh mục này sẽ được sử dụng trong toàn bộ hệ thống để đảm bảo tính nhất quán khi gán và tìm kiếm nhân viên theo kỹ năng. Giao diện gồm hai phần chính: quản lý loại kỹ năng (categories) và quản lý chi tiết các kỹ năng thuộc từng loại.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề "Quản lý Danh mục Kỹ năng"
- Tab navigation để chuyển đổi giữa hai view:
  - Tab 1: Quản lý Loại Kỹ năng (Skill Categories)
  - Tab 2: Quản lý Chi tiết Kỹ năng (Skills)
- DataTable hiển thị danh sách (cho cả hai tab)
- Panel/Modal thêm/sửa (cho cả hai tab)

### UI Components
- Sử dụng Tailwind CSS cho styling
- Tab component để chuyển đổi giữa các view
- DataTables với sorting, filtering, và pagination
- Search box cho tìm kiếm nhanh
- Modal forms cho thêm/sửa
- Confirmation dialogs cho xóa
- Toast notifications
- Dropdown filters (lọc skills theo category)

### Functionality
#### Tab 1: Quản lý Loại Kỹ năng
- Hiển thị danh sách các loại kỹ năng (VD: Ngôn ngữ lập trình, Framework, Database, v.v.)
- Thêm loại kỹ năng mới
- Sửa thông tin loại kỹ năng
- Xóa loại kỹ năng (với kiểm tra ràng buộc)
- Tìm kiếm/lọc loại kỹ năng

#### Tab 2: Quản lý Chi tiết Kỹ năng
- Hiển thị danh sách các kỹ năng cụ thể
- Lọc theo loại kỹ năng (category)
- Thêm kỹ năng mới (với loại kỹ năng đã chọn)
- Sửa thông tin kỹ năng
- Xóa kỹ năng (với kiểm tra ràng buộc)
- Tìm kiếm/lọc kỹ năng

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/skill-categories` (API-HRM-009) - Lấy danh sách loại kỹ năng
  - `POST /api/v1/admin/skill-categories` (API-HRM-010) - Thêm loại kỹ năng mới
  - `PUT /api/v1/admin/skill-categories/{id}` (API-HRM-011) - Cập nhật loại kỹ năng
  - `DELETE /api/v1/admin/skill-categories/{id}` (API-HRM-012) - Xóa loại kỹ năng
  - `GET /api/v1/skills` (API-HRM-013) - Lấy danh sách kỹ năng (có thể filter theo category)
  - `POST /api/v1/admin/skills` (API-HRM-014) - Thêm kỹ năng mới
  - `PUT /api/v1/admin/skills/{id}` (API-HRM-015) - Cập nhật kỹ năng
  - `DELETE /api/v1/admin/skills/{id}` (API-HRM-016) - Xóa kỹ năng

## Chi tiết các thành phần
Dựa trên mô tả [MH-HRM-04.md](../../../DD/SCREEN/MH-HRM-04.md):

### Header Section
- Tiêu đề "Quản lý Danh mục Kỹ năng"
- Tabs để chuyển đổi giữa "Loại Kỹ năng" và "Chi tiết Kỹ năng"

### Tab 1: Quản lý Loại Kỹ năng
#### Controls
- Button "Thêm Loại Kỹ năng" - Mở modal thêm mới
- Search box để tìm kiếm loại kỹ năng

#### DataTable Loại Kỹ năng
- Columns:
  - ID
  - Tên loại kỹ năng (VD: "Ngôn ngữ lập trình", "Framework", "Database", v.v.)
  - Mô tả
  - Số lượng kỹ năng thuộc loại (tùy chọn)
  - Actions (Edit, Delete)

#### Modal Thêm/Sửa Loại Kỹ năng
- Field: Tên loại kỹ năng (required)
- Field: Mô tả
- Buttons: Lưu, Hủy

### Tab 2: Quản lý Chi tiết Kỹ năng
#### Controls
- Button "Thêm Kỹ năng" - Mở modal thêm mới
- Dropdown filter để lọc theo loại kỹ năng
- Search box để tìm kiếm kỹ năng

#### DataTable Kỹ năng
- Columns:
  - ID
  - Tên kỹ năng (VD: "JavaScript", "React", "MySQL", v.v.)
  - Loại kỹ năng (hiển thị tên category)
  - Mô tả
  - Actions (Edit, Delete)

#### Modal Thêm/Sửa Kỹ năng
- Field: Tên kỹ năng (required)
- Field: Loại kỹ năng (select dropdown, required)
- Field: Mô tả
- Buttons: Lưu, Hủy

### Confirmation Dialogs
- Dialog xác nhận xóa loại kỹ năng (với cảnh báo về ràng buộc)
- Dialog xác nhận xóa kỹ năng (với cảnh báo về ràng buộc)

## Phân quyền truy cập
- **Admin**: Truy cập đầy đủ, có thể thêm/sửa/xóa cả loại kỹ năng và kỹ năng
- **General Manager (課長)**: Truy cập chỉ để xem, không có quyền thêm/sửa/xóa (tùy thuộc vào cấu hình)
- **Team Leader (部長)**: Có thể có quyền giới hạn (tùy thuộc vào cấu hình)
- **Nhân viên**: Không có quyền truy cập màn hình này

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CORE-005: Modal components (dependency)
- FE-HRM-003: Employee Form (related)
- FE-HRM-007: Employee Skills Search (related)

## Tiêu chí chấp nhận
- Hiển thị chính xác hai tab với các chức năng CRUD tương ứng
- Quản lý loại kỹ năng (thêm/sửa/xóa) hoạt động đúng
- Quản lý chi tiết kỹ năng (thêm/sửa/xóa) hoạt động đúng
- Kiểm tra ràng buộc khi xóa (không cho phép xóa loại kỹ năng đang được sử dụng)
- Phân quyền truy cập và chỉnh sửa tuân thủ chặt chẽ theo vai trò
- Search và filter hoạt động chính xác
- Hiển thị thông báo thành công/lỗi (toast notifications)
- Validation form đầy đủ (client-side và server-side)
- UI responsive từ desktop đến tablet (>= 768px)
- Refresh danh sách sau khi thêm/sửa/xóa
- Tab state được lưu trữ khi chuyển đổi giữa các tab 