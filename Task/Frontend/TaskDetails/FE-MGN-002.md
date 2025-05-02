# Task Detail: FE-MGN-002

## Thông tin chung
- **ID**: FE-MGN-002
- **Tên task**: Xây dựng Cost Input Form (MH-MGN-02)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-MGN-02.md](../../../DD/SCREEN/MH-MGN-02.md)
  - API: [API-MGN-003](../../../DD/API/API-MGN-003.md), [API-MGN-004](../../../DD/API/API-MGN-004.md)

## Mô tả
Xây dựng form nhập liệu chi phí nhân viên, phục vụ cho việc tính toán margin. Form này cho phép nhập thủ công hoặc import dữ liệu chi phí nhân viên theo tháng/kỳ, và chỉ dành cho những người dùng được cấp quyền (Admin, General Manager, Team Leader). Dữ liệu chi phí là cơ sở quan trọng để hệ thống tính toán margin, hỗ trợ việc phân tích hiệu quả tài chính từ nguồn lực nhân sự. Giao diện cần có các lựa chọn nhập chi phí đơn lẻ hoặc theo batch và hỗ trợ tải lên file để nhập một lúc nhiều dữ liệu.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề "Cập nhật Chi phí Nhân viên"
- Tab navigation cho 2 phương thức nhập:
  - Tab 1: Nhập thủ công (Manual Input)
  - Tab 2: Import từ file (Batch Import)
- Form controls tương ứng với từng tab
- Preview table hiển thị dữ liệu (trước/sau khi import)
- Footer section với các nút hành động (Lưu, Hủy, Import)

### UI Components
- Sử dụng Tailwind CSS cho styling
- Tab component để chuyển đổi giữa các chế độ nhập
- Trong tab "Nhập thủ công":
  - Month/Year picker để chọn kỳ nhập liệu
  - Team selector (dropdown/multi-select)
  - DataTable cho danh sách nhân viên với editable cells cho nhập chi phí
  - Pagination nếu danh sách nhân viên lớn
- Trong tab "Import từ file":
  - Month/Year picker để chọn kỳ import
  - File upload area với drag & drop
  - Preview table hiển thị dữ liệu từ file
  - Error indicators cho dữ liệu không hợp lệ
  - Template download button

### Functionality
#### Tab 1: Nhập thủ công
- Chọn tháng/năm cần nhập chi phí
- Lọc theo team (tùy quyền)
- Load danh sách nhân viên tương ứng
- Nhập chi phí trực tiếp trên bảng (in-line editing)
- Validation dữ liệu (format tiền tệ, giá trị hợp lệ)
- Lưu dữ liệu cho tất cả nhân viên hoặc từng nhân viên

#### Tab 2: Import từ file
- Chọn tháng/năm cần import chi phí
- Upload file Excel/CSV
- Validate cấu trúc và dữ liệu của file
- Preview dữ liệu từ file trước khi import
- Hiển thị lỗi và cảnh báo (nếu có)
- Cho phép sửa trực tiếp các lỗi trước khi import
- Cung cấp template download

### API Integration
- Tích hợp với Backend API endpoints:
  - `POST /api/v1/margins/costs/import` (API-MGN-003) - Import chi phí nhân viên từ file
  - `POST /api/v1/margins/costs` (API-MGN-004) - Nhập chi phí thủ công cho nhân viên

## Chi tiết các thành phần
Dựa trên mô tả [MH-MGN-02.md](../../../DD/SCREEN/MH-MGN-02.md):

### Header Section
- Tiêu đề "Cập nhật Chi phí Nhân viên"
- Month/Year picker để chọn kỳ nhập liệu (hiển thị ở cả 2 tab)
- Tabs để chuyển đổi giữa "Nhập thủ công" và "Import từ file"

### Tab 1: Nhập thủ công
#### Controls
- Team filter: Dropdown cho phép chọn team/bộ phận (dựa trên quyền)
- Search box để tìm kiếm nhân viên cụ thể
- Button "Lưu tất cả" và "Reset" 

#### DataTable Nhân viên và Chi phí
- Column Nhân viên:
  - Avatar (optional)
  - Tên nhân viên
  - Mã nhân viên
- Column Team/Bộ phận (read-only)
- Column Chi phí cơ bản:
  - Editable cell với format tiền tệ
  - Validation khi nhập
- Column Chi phí khác:
  - Editable cell với format tiền tệ
  - Tooltip giải thích các chi phí khác nếu cần
- Column Tổng chi phí:
  - Auto-calculated từ các cột chi phí khác
  - Read-only
- Column Actions với các nút:
  - Save (lưu từng dòng)
  - Reset (reset từng dòng)

### Tab 2: Import từ file
#### Controls
- File upload area với:
  - Drag & drop support
  - Button "Chọn file" với file type restrictions (.xlsx, .csv)
  - Hiển thị thông tin file đã chọn
- Button "Tải template" để download mẫu file import
- Button "Kiểm tra dữ liệu" và "Import"

#### Preview Table
- Hiển thị sau khi upload file và click "Kiểm tra dữ liệu"
- Columns tương tự như trong file template và Tab 1
- Error indicators cho các cell có lỗi
- Validation summary hiển thị tổng số lỗi và cảnh báo

### Footer Section
- Trong Tab 1:
  - Button "Lưu tất cả" (primary) - Submit form với tất cả dữ liệu đã nhập
  - Button "Reset" (secondary) - Reset form về giá trị ban đầu
- Trong Tab 2:
  - Button "Kiểm tra dữ liệu" - Validate file và hiển thị preview
  - Button "Import" (primary, disabled cho đến khi file đã được kiểm tra và không có lỗi nghiêm trọng)
  - Button "Hủy" (secondary)

## Phân quyền truy cập
- **Admin**: Truy cập đầy đủ, có thể nhập/import chi phí cho tất cả nhân viên
- **General Manager (課長)**: Truy cập đầy đủ, có thể nhập/import chi phí cho nhân viên thuộc các bộ phận quản lý
- **Team Leader (部長)**: Chỉ có thể nhập/import chi phí cho nhân viên trong team mình
- **Nhân viên**: Không có quyền truy cập
- **Kế toán**: Có thể được cấp quyền đặc biệt tùy theo cấu hình

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CORE-008: File Upload/Preview components (dependency)
- FE-MGN-001: Margin List Page (related)
- FE-MGN-003: Cost Import UI (related/breakdown)

## Tiêu chí chấp nhận
- Cả hai tab "Nhập thủ công" và "Import từ file" hoạt động chính xác
- Chọn tháng/năm và team lọc dữ liệu chính xác
- In-line editing trong tab "Nhập thủ công" hoạt động mượt mà với validation hợp lý
- Upload file và preview dữ liệu trong tab "Import" hoạt động đúng
- Validation file import chính xác, hiển thị lỗi và cảnh báo rõ ràng
- Template download bao gồm đúng format và hướng dẫn
- Lưu dữ liệu thành công với API endpoints tương ứng
- Phân quyền truy cập và nhập liệu theo đúng vai trò
- UI responsive từ desktop đến tablet (>= 768px)
- Hiển thị thông báo thành công/lỗi phù hợp khi nhập liệu
- Performance: xử lý nhanh ngay cả với số lượng nhân viên lớn (>100) 