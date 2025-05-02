# Task Detail: FE-HRM-003

## Thông tin chung
- **ID**: FE-HRM-003
- **Tên task**: Xây dựng Employee Form (MH-HRM-03)
- **Độ ưu tiên**: Cao
- **Estimate**: 4 days
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-03.md](../../../DD/SCREEN/MH-HRM-03.md)
  - API: [API-HRM-002](../../../DD/API/API-HRM-002.md), [API-HRM-004](../../../DD/API/API-HRM-004.md), [API-HRM-017](../../../DD/API/API-HRM-017.md), [API-HRM-018](../../../DD/API/API-HRM-018.md), [API-HRM-019](../../../DD/API/API-HRM-019.md), [API-HRM-020](../../../DD/API/API-HRM-020.md)

## Mô tả
Xây dựng form thêm mới và chỉnh sửa thông tin nhân viên với đầy đủ các trường thông tin, phân quyền chặt chẽ theo vai trò, và validation đầy đủ. Form này cho phép thêm mới nhân viên (Admin, General Manager) hoặc chỉnh sửa thông tin nhân viên hiện có (theo phân quyền), bao gồm các nhóm thông tin: cơ bản, tổ chức, kỹ năng, và trạng thái/phân bổ dự án. Màn hình này có hai chế độ hoạt động (Thêm mới/Chỉnh sửa) và hiển thị các trường thông tin khác nhau tùy theo vai trò người dùng và chế độ đang sử dụng.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề ("Thêm mới Nhân sự" hoặc "Chỉnh sửa Nhân sự: [Tên]")
- Form chia thành các section rõ ràng:
  - Section 1: Thông tin Cơ bản & Cá nhân
  - Section 2: Thông tin Tổ chức
  - Section 3: Quản lý Skills & Kinh nghiệm
  - Section 4: Trạng thái & Phân bổ Dự án
- Footer section với các nút hành động (Lưu, Hủy)

### UI Components
- Sử dụng Tailwind CSS cho styling
- Form layout responsive
- Các input controls:
  - Text inputs (single-line, multi-line)
  - Date pickers
  - Dropdowns/Select controls
  - Radio/Checkbox groups
  - Dynamic table (cho skills management)
  - File upload (cho avatar)
- Section headers với collapse/expand
- Conditional form fields (hiển thị/ẩn dựa trên giá trị trường khác)
- Error messages inline

### Functionality
- Hai chế độ: Thêm mới (Create) và Sửa (Edit)
- Phân quyền hiển thị và chỉnh sửa từng trường theo vai trò
- Validation dữ liệu real-time và khi submit
- Dynamic skill table với các chức năng thêm/sửa/xóa
- Conditional rendering cho các trường phụ thuộc (VD: các trường hiển thị khi chọn trạng thái "Allocated")
- Upload và crop ảnh đại diện
- Form state management với React Hook Form
- Xử lý submit và error handling
- Xác nhận trước khi cancel form khi có thay đổi

### API Integration
- Tích hợp với Backend API endpoints:
  - `POST /api/v1/employees` (API-HRM-002) - Tạo nhân viên mới
  - `PUT /api/v1/employees/{employeeId}` (API-HRM-004) - Cập nhật thông tin nhân viên
  - `GET /api/v1/employees/{employeeId}/skills` (API-HRM-017) - Lấy skills của nhân viên (cho chế độ Edit)
  - `POST /api/v1/employees/{employeeId}/skills` (API-HRM-018) - Thêm/cập nhật skills cho nhân viên
  - `DELETE /api/v1/employees/{employeeId}/skills/{skillId}` (API-HRM-019) - Xóa skill khỏi nhân viên
  - `PUT /api/v1/employees/{employeeId}/status` (API-HRM-020) - Cập nhật trạng thái và phân bổ dự án

## Chi tiết các thành phần
Dựa trên mô tả [MH-HRM-03.md](../../../DD/SCREEN/MH-HRM-03.md):

### Section 1: Thông tin Cơ bản & Cá nhân
- Field: Mã nhân viên (tự sinh với mode Create, read-only với mode Edit)
- Field: Họ và tên (required)
- Field: Ngày sinh (date picker)
- Field: Email công ty (required, format validation)
- Field: Tài khoản nội bộ
- Field: Địa chỉ (text area)
- Field: Số điện thoại (format validation)
- Field: Thông tin liên hệ khẩn cấp (text area)
- Field: Ảnh đại diện (upload control với preview và crop options)

### Section 2: Thông tin Tổ chức
- Field: Ngày vào công ty (date picker)
- Field: Vị trí công việc (select từ danh mục)
- Field: Team (select từ danh sách)
- Field: Leader trực tiếp (select từ danh sách users có vai trò Leader)

### Section 3: Quản lý Skills & Kinh nghiệm
- Dynamic table với:
  - Nút "Thêm Skill" để thêm dòng mới
  - Columns:
    - Loại Skill (select)
    - Tên Skill (select/autocomplete dựa trên loại đã chọn)
    - Số năm kinh nghiệm (number input)
    - Cấp độ (Tự đánh giá) (select: Basic, Intermediate, Advanced)
    - Cấp độ (Leader đánh giá) (select, read-only cho nhân viên)
    - Nút xóa skill (cho từng dòng)
  - Validation cho bảng skills

### Section 4: Trạng thái & Phân bổ Dự án
- Field: Trạng thái hiện tại (select: Allocated, Available/Bench, Ending Soon, On Leave, Resigned...)
- Các field phụ thuộc vào Trạng thái:
  - Khi chọn Allocated/Ending Soon:
    - Field: Dự án đang tham gia (select/autocomplete)
    - Field: % Phân bổ (number input, 1-100)
    - Field: Ngày dự kiến kết thúc (date picker, required nếu Ending Soon)
  - Khi chọn Resigned:
    - Field: Ngày nghỉ việc (date picker)

### Footer và Actions
- Button "Lưu": Submit form với validation
- Button "Hủy": Quay về màn hình trước (với confirm dialog nếu có thay đổi)
- Hiển thị error messages và success messages

## Phân quyền truy cập
- **Admin**: Truy cập đầy đủ cả hai mode (Create/Edit), có thể sửa tất cả các trường
- **General Manager (課長)**: Truy cập cả hai mode, tập trung quyền sửa trên Thông tin Tổ chức
- **Team Leader (部長)**: Chỉ truy cập mode Edit cho nhân viên trong team, chỉ có thể sửa:
  - Quản lý Skills & Kinh nghiệm (thêm/sửa/xóa skill, đánh giá cấp độ)
  - Trạng thái & Phân bổ Dự án
- **Nhân viên**: Chỉ truy cập mode Edit cho chính mình, chỉ có thể sửa:
  - Thông tin liên hệ cá nhân (Địa chỉ, SĐT, Liên hệ khẩn cấp)
  - Tự cập nhật Skills & Kinh nghiệm (thêm/sửa/xóa skill, năm KN, tự đánh giá)

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-HRM-001: Employee List Page (related)
- FE-HRM-002: Employee Detail Page (related)
- FE-HRM-004: Skills Management UI (related)
- FE-HRM-005: Project Assignment UI (related)

## Tiêu chí chấp nhận
- Form hiển thị đúng theo mô tả MH-HRM-03.md
- Chế độ Create và Edit hoạt động chính xác
- Validation hoạt động đúng (client-side và server-side)
- Phân quyền truy cập và chỉnh sửa tuân thủ chặt chẽ theo vai trò
- Dynamic fields hiển thị/ẩn theo logic nghiệp vụ
- Skills table cho phép thêm/sửa/xóa thành công
- Upload avatar hoạt động đúng với preview và crop
- Form submit thành công, hiển thị thông báo phù hợp (success/error)
- Confirm dialog hiển thị khi hủy form có thay đổi
- UI responsive từ desktop đến tablet (>= 768px)
- Performance: form render và validation nhanh, không có lag khi thao tác 