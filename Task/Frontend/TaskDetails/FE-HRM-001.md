# Task Detail: FE-HRM-001

## Thông tin chung
- **ID**: FE-HRM-001
- **Tên task**: Xây dựng Employee List Page (MH-HRM-01) 
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-01](../../../assets/frontend/img/MH-HRM-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-01.md](../../../DD/SCREEN/MH-HRM-01.md)
  - API: [API-HRM-001](../../../DD/API/API-HRM-001.md), [API-HRM-008](../../../DD/API/API-HRM-008.md), [API-HRM-009](../../../DD/API/API-HRM-009.md), [API-HRM-013](../../../DD/API/API-HRM-013.md)

## Mô tả
Xây dựng trang danh sách nhân viên với đầy đủ chức năng tìm kiếm, lọc, phân trang và hiển thị thông tin cơ bản về nhân viên. Trang này cung cấp một giao diện tập trung để xem, tìm kiếm, lọc danh sách nhân sự trong bộ phận/công ty. Nó là điểm khởi đầu để truy cập thông tin chi tiết của từng nhân viên hoặc thực hiện các thao tác quản lý như thêm mới hoặc xuất dữ liệu. Màn hình này hỗ trợ các nhà quản lý (Team Leader, General Manager) và Admin trong việc nắm bắt tình hình nguồn lực hiện tại.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề và action buttons (Thêm mới, Export)
- Filter section có thể mở rộng/thu gọn
- Bảng dữ liệu chính hiển thị danh sách nhân viên
- Pagination controls ở cuối bảng

### UI Components
- Sử dụng Tailwind CSS cho styling
- Advanced filter panel với:
  - Text search cho tên/mã nhân viên
  - Select dropdowns cho team, vị trí, trạng thái
  - DatePicker cho ngày gia nhập
  - Skill filter (Multiselect autocomplete)
  - Nút Clear và Apply filters
- DataTable với các columns:
  - Avatar và thông tin cơ bản (Tên, Mã NV, Email)
  - Team/Bộ phận
  - Vị trí công việc
  - Trạng thái (với color indicators)
  - Skills chính (hiển thị dạng tags)
  - Dự án hiện tại
  - Quick action buttons

### Functionality
- Tìm kiếm thông minh theo tên, mã NV, email
- Lọc nâng cao với multiple criteria
- Sắp xếp theo các cột (sortable columns)
- Phân trang với cấu hình số records/trang
- Export dữ liệu ra Excel/CSV
- Quick filters cho các trạng thái phổ biến (bench, ending soon)
- Click vào dòng để xem chi tiết nhân viên
- Hiển thị nhanh một số thông tin khi hover
- Tuỳ chỉnh cột hiển thị (column toggler)

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/employees` (API-HRM-001) - Lấy danh sách nhân viên (với query params cho search, filter, sort, pagination)
  - `GET /api/v1/skills` (API-HRM-013) - Lấy danh sách skills (cho skill filter options)
  - `GET /api/v1/skill-categories` (API-HRM-009) - Lấy danh sách các loại kỹ năng (để nhóm skills)
  - `GET /api/v1/employees/export` (API-HRM-008) - Export danh sách nhân viên

## Chi tiết các thành phần
Dựa trên thiết kế [MH-HRM-01.png](../../../assets/frontend/img/MH-HRM-01.png) và mô tả [MH-HRM-01.md](../../../DD/SCREEN/MH-HRM-01.md):

### Header Section
- Tiêu đề "Quản lý Nhân sự"
- Button "Thêm mới" (primary button) - Mở form tạo nhân viên mới
- Button "Export" (secondary button) - Hiển thị dropdown với các định dạng export (Excel, CSV)
- (Optional) Button "Import" nếu feature này được phát triển (API-HRM-007)

### Filter Section
- Search bar chính với placeholder "Tìm theo tên, mã nhân viên..."
- Quick filter chips cho các trạng thái: "Tất cả", "Bench", "Sắp hết dự án", "Đang làm dự án"
- Advanced filter panel (collapsible):
  - Row 1: Bộ phận (Select), Vị trí (Select), Trạng thái (Select)
  - Row 2: Skills (Multi-select với autocomplete), Khoảng thời gian gia nhập (DateRange)
  - Footer với "Clear" và "Apply" buttons

### DataTable
- Checkbox column cho bulk selection (nếu cần)
- Column Avatar + Thông tin cơ bản:
  - Avatar hình tròn (hoặc initials nếu không có avatar)
  - Tên nhân viên (in đậm)
  - Mã nhân viên (text nhỏ)
  - Email (có thể click để mở email client)
- Column Team/Bộ phận
- Column Vị trí công việc
- Column Trạng thái (với badge màu tương ứng): 
  - Bench/Available (Red)
  - Đang làm dự án (Green)
  - Sắp hết dự án (Yellow, với ngày kết thúc)
- Column Skills (hiển thị tối đa 3 skills chính dạng tags, với tooltip "Xem thêm")
- Column Dự án hiện tại
- Column Actions với các nút:
  - View details (icon) - Chuyển đến màn hình MH-HRM-02
  - Edit (icon) - Chuyển đến màn hình MH-HRM-03 với mode edit
  - More actions (dropdown)

### Pagination
- Hiển thị "Showing X to Y of Z entries"
- Page number navigation
- Records per page selector (10, 20, 50, 100)

## Phân quyền truy cập
- **Admin**: Xem danh sách tất cả nhân viên, đầy đủ quyền thêm mới, sửa, export
- **General Manager (課長)**: Xem danh sách tất cả nhân viên (hoặc giới hạn trong các bộ phận quản lý), quyền thêm mới, sửa, export
- **Team Leader (部長)**: Xem danh sách nhân viên thuộc team mình quản lý, chỉ có quyền sửa nhân viên trong team, export dữ liệu team mình
- **Nhân viên**: Chỉ xem danh sách, không có quyền thêm/sửa (trừ thông tin bản thân)
- Áp dụng các quyền chi tiết theo định nghĩa trong file permissions_definition.md

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-HRM-002: Employee Detail Page (related)
- FE-HRM-003: Employee Form (related)
- FE-HRM-006: Import/Export functionality (related)

## Tiêu chí chấp nhận
- Danh sách nhân viên hiển thị đúng theo thiết kế MH-HRM-01.png
- Tìm kiếm và lọc hoạt động chính xác, kết quả load trong < 2 giây
- Phân trang hoạt động mượt mà với số lượng record lớn (1000+)
- Export dữ liệu thành công với đúng format
- UI responsive từ desktop đến tablet (>= 768px)
- Hiển thị thông báo phù hợp khi không có kết quả
- Column sorting hoạt động chính xác
- Performance: trang load ban đầu < 3 giây
- RBAC áp dụng đúng (phân quyền xem/sửa theo vai trò)
- Lưu trạng thái bộ lọc cho lần truy cập sau 