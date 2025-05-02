# Task Detail: FE-CTR-001

## Thông tin chung
- **ID**: FE-CTR-001
- **Tên task**: Xây dựng Contract List Page (MH-CTR-01)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-CTR-01.md](../../../DD/SCREEN/MH-CTR-01.md)
  - API: [API-CTR-001](../../../DD/API/API-CTR-001.md)

## Mô tả
Xây dựng trang danh sách hợp đồng với đầy đủ chức năng tìm kiếm, lọc, phân trang và hiển thị thông tin cơ bản về các hợp đồng trong hệ thống. Trang này cung cấp tổng quan về tất cả các hợp đồng, trạng thái thanh toán và giá trị của chúng. Đây là điểm khởi đầu cho nhiều hoạt động liên quan đến quản lý hợp đồng, doanh thu và công nợ. Người dùng có thể xem thông tin tóm tắt, chuyển đến trang chi tiết để xem thông tin đầy đủ hoặc tạo hợp đồng mới. Trang danh sách này giúp các đối tượng như Sales, Kế toán, và quản lý nắm bắt tình trạng hợp đồng và theo dõi doanh thu.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề và action buttons (Thêm mới)
- Filter section có thể mở rộng/thu gọn
- Bảng dữ liệu chính hiển thị danh sách hợp đồng
- Pagination controls ở cuối bảng
- Summary section hiển thị tổng hợp giá trị hợp đồng theo kết quả lọc

### UI Components
- Sử dụng Tailwind CSS cho styling
- Advanced filter panel với:
  - Text search cho tên/mã hợp đồng/khách hàng
  - Select dropdowns cho trạng thái, loại hợp đồng
  - DatePicker cho khoảng thời gian hợp đồng/thanh toán
  - Nút Clear và Apply filters
- DataTable với các columns:
  - Thông tin cơ bản (Mã HĐ, Tên HĐ, Khách hàng)
  - Loại hợp đồng
  - Giá trị hợp đồng
  - Ngày ký/Hiệu lực
  - Trạng thái thanh toán (với color indicators)
  - Tổng giá trị đã thanh toán / còn lại
  - Quick action buttons

### Functionality
- Tìm kiếm thông minh theo tên HĐ, mã HĐ, khách hàng
- Lọc nâng cao với multiple criteria
- Sắp xếp theo các cột (sortable columns)
- Phân trang với cấu hình số records/trang
- Quick filters cho các trạng thái thanh toán phổ biến (Chưa thanh toán, Đã thanh toán, Quá hạn)
- Click vào dòng để xem chi tiết hợp đồng
- Hiển thị tổng hợp giá trị hợp đồng theo kết quả lọc

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/contracts` (API-CTR-001) - Lấy danh sách hợp đồng (với query params cho search, filter, sort, pagination)

## Chi tiết các thành phần
Dựa trên mô tả [MH-CTR-01.md](../../../DD/SCREEN/MH-CTR-01.md):

### Header Section
- Tiêu đề "Quản lý Hợp đồng"
- Button "Thêm mới" (primary button) - Mở form tạo hợp đồng mới (điều hướng đến MH-CTR-03)
- (Optional) Dropdown "Xuất báo cáo" (secondary button) với các tùy chọn xuất dữ liệu

### Filter Section
- Search bar chính với placeholder "Tìm theo tên, mã hợp đồng, khách hàng..."
- Quick filter chips cho các trạng thái thanh toán: "Tất cả", "Đã thanh toán đủ", "Còn công nợ", "Có thanh toán quá hạn"
- Advanced filter panel (collapsible):
  - Row 1: Loại hợp đồng (Select), Trạng thái thanh toán (Select), Người phụ trách (Select)
  - Row 2: Thời gian ký hợp đồng (DateRange), Giá trị hợp đồng (Range)
  - Footer với "Clear" và "Apply" buttons

### DataTable
- Column Thông tin cơ bản:
  - Mã hợp đồng
  - Tên hợp đồng (in đậm)
  - Tên khách hàng
- Column Loại hợp đồng
- Column Giá trị hợp đồng (định dạng tiền tệ)
- Column Ngày ký / Hiệu lực:
  - Ngày ký
  - Ngày hiệu lực
- Column Trạng thái thanh toán (với badge màu tương ứng):
  - Chưa đến hạn (Blue)
  - Đã thanh toán đủ (Green)
  - Còn công nợ (Yellow)
  - Quá hạn (Red)
- Column Thanh toán:
  - Đã thanh toán: X% (định dạng tiền tệ và %)
  - Còn lại: Y% (định dạng tiền tệ và %)
  - Có thể hiển thị dạng progress bar
- Column Actions với các nút:
  - View details (icon) - Chuyển đến màn hình MH-CTR-02
  - Edit (icon) - Chuyển đến màn hình MH-CTR-03 với mode edit
  - More actions (dropdown)

### Summary Section
- Hiển thị ở dưới bảng dữ liệu hoặc cố định ở cuối màn hình
- Tổng số hợp đồng trong kết quả lọc hiện tại
- Tổng giá trị hợp đồng
- Tổng đã thanh toán
- Tổng còn lại
- Phân bố theo trạng thái thanh toán

### Pagination
- Hiển thị "Showing X to Y of Z entries"
- Page number navigation
- Records per page selector (10, 20, 50, 100)

## Phân quyền truy cập
- **Admin**: Xem tất cả hợp đồng, đầy đủ quyền thêm mới, sửa
- **General Manager (課長)**: Xem tất cả hợp đồng, quyền thêm mới, sửa
- **Team Leader (部長)**: Xem hợp đồng liên quan đến team mình và hợp đồng được assign, quyền thêm mới hạn chế, sửa hợp đồng liên quan
- **Sales**: Xem hợp đồng mình phụ trách, thêm mới, sửa hợp đồng mình phụ trách
- **Kế toán**: Xem tất cả hợp đồng, không có quyền sửa/thêm mới (chỉ cập nhật trạng thái thanh toán)

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CTR-002: Contract Detail Page (related)
- FE-CTR-003: Contract Form (related)
- FE-CTR-004: Payment Status Update UI (related)

## Tiêu chí chấp nhận
- Danh sách hợp đồng hiển thị đúng theo mô tả MH-CTR-01.md
- Tìm kiếm và lọc hoạt động chính xác, kết quả load trong < 2 giây
- Phân trang hoạt động mượt mà với số lượng record lớn
- Color indicators cho trạng thái thanh toán hiển thị chính xác
- UI responsive từ desktop đến tablet (>= 768px)
- Hiển thị thông báo phù hợp khi không có kết quả
- Column sorting hoạt động chính xác
- Summary tính toán chính xác dựa trên kết quả lọc hiện tại
- RBAC áp dụng đúng (phân quyền xem/sửa theo vai trò)
- Lưu trạng thái bộ lọc cho lần truy cập sau
- Định dạng tiền tệ, phần trăm và ngày tháng hiển thị nhất quán 