# Task Detail: FE-OPP-001

## Thông tin chung
- **ID**: FE-OPP-001
- **Tên task**: Xây dựng Opportunity List Page (MH-OPP-01)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-OPP-01](../../../assets/frontend/img/MH-OPP-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-OPP-01.md](../../../DD/SCREEN/MH-OPP-01.md)
  - API: [API-OPP-001](../../../DD/API/API-OPP-001.md), [API-OPP-003](../../../DD/API/API-OPP-003.md), [API-OPP-005](../../../DD/API/API-OPP-005.md), [API-OPP-008](../../../DD/API/API-OPP-008.md)

## Mô tả
Xây dựng trang danh sách cơ hội kinh doanh với các chức năng tìm kiếm, lọc, và hiển thị trạng thái theo dõi (follow-up) của từng cơ hội. Trang này hiển thị dữ liệu được đồng bộ từ Hubspot và cho phép người dùng quản lý, phân công cơ hội cho Leader. Màn hình này hiển thị danh sách các cơ hội kinh doanh được đồng bộ từ Hubspot và cho phép người dùng (Sales, Team Leader, General Manager, Admin) xem tổng quan, theo dõi trạng thái, lọc, tìm kiếm và truy cập vào chi tiết của từng cơ hội để thực hiện các hành động cụ thể như ghi chú hoặc phân công.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề và action buttons (Sync Hubspot)
- Filter section có thể mở rộng/thu gọn
- Bảng dữ liệu chính hiển thị danh sách cơ hội
- Pagination controls ở cuối bảng
- (Optional) Support chuyển đổi giữa dạng bảng và dạng Kanban view

### UI Components
- Sử dụng Tailwind CSS cho styling
- Filter panel với:
  - Text search cho tên cơ hội/khách hàng
  - Select dropdowns cho giai đoạn, trạng thái follow-up
  - DatePicker cho khoảng thời gian tạo/cập nhật
  - Checkbox "Ưu tiên Onsite"
  - Nút Clear và Apply filters
- DataTable với các columns:
  - Thông tin cơ bản cơ hội (Tên, Khách hàng)
  - Giai đoạn (Stage)
  - Giá trị dự kiến
  - Trạng thái follow-up (với color indicators)
  - Ngày tương tác cuối
  - Leader được phân công
  - Quick action buttons
- (Optional) Kanban Board view với cột đại diện cho mỗi Deal Stage

### Functionality
- Tìm kiếm thông minh theo tên cơ hội, khách hàng
- Lọc theo multiple criteria
- Sắp xếp theo các cột (sortable columns)
- Phân trang với cấu hình số records/trang
- Quick filters cho các trạng thái follow-up phổ biến
- Click vào dòng để xem chi tiết cơ hội
- Assign Leader thông qua dropdown hoặc modal
- Manually trigger Hubspot sync
- Lưu trạng thái bộ lọc người dùng đã chọn cho lần truy cập sau
- (Optional) Kéo thả thẻ giữa các cột trong Kanban view để cập nhật Deal Stage

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/opportunities` (API-OPP-001) - Lấy danh sách cơ hội (với query params cho search, filter, sort, pagination)
  - `POST /api/v1/opportunities/{oppId}/assign` (API-OPP-005) - Gán Leader vào cơ hội
  - `POST /api/v1/opportunities/sync` (API-OPP-003) - Kích hoạt đồng bộ thủ công từ Hubspot
  - `PUT /api/v1/opportunities/{oppId}/onsite` (API-OPP-008) - Đánh dấu/bỏ đánh dấu ưu tiên Onsite

## Chi tiết các thành phần
Dựa trên thiết kế [MH-OPP-01.png](../../../assets/frontend/img/MH-OPP-01.png) và mô tả [MH-OPP-01.md](../../../DD/SCREEN/MH-OPP-01.md):

### Header Section
- Tiêu đề "Quản lý Cơ hội Kinh doanh"
- Button "Sync Hubspot" với icon (trên góc phải)
- Thông tin lần đồng bộ cuối
- (Optional) Nút chuyển đổi giữa dạng Bảng và dạng Kanban

### Filter Section
- Search bar chính với placeholder "Tìm theo tên cơ hội, khách hàng..."
- Quick filter chips cho trạng thái follow-up: "Tất cả", "Cần liên hệ", "Đã follow-up", "Chưa follow-up"
- Advanced filter panel (collapsible):
  - Row 1: Giai đoạn (Select), Trạng thái follow-up (Select), Ngày tạo (DateRange)
  - Row 2: Leader (Select), Checkbox "Ưu tiên Onsite", Ngày cập nhật (DateRange)
  - Footer với "Clear" và "Apply" buttons

### DataTable
- Column Thông tin cơ hội:
  - Tên cơ hội (in đậm)
  - Tên khách hàng (text thường)
  - Hubspot ID (text nhỏ, mờ)
- Column Giai đoạn (Stage):
  - Hiển thị tên giai đoạn (Qualification, Demo, Negotiation, Closed...)
  - Có thể hiển thị dạng badge với màu tương ứng theo giai đoạn
- Column Giá trị dự kiến:
  - Số tiền dự kiến
  - Định dạng currency (VND/USD)
- Column Trạng thái follow-up:
  - Badge với màu tương ứng:
    - Cần liên hệ gấp (Red)
    - Đã follow-up gần đây (Green)
    - Sắp cần follow-up (Yellow)
  - Hiển thị thời gian kể từ lần follow-up cuối
- Column Ngày tương tác cuối:
  - Ngày giờ tương tác cuối
  - Format thân thiện (hôm nay, hôm qua, 3 ngày trước...)
- Column Leader được phân công:
  - Tên Leader với avatar nhỏ
  - Nếu chưa phân công: hiển thị "Chưa phân công" và nút Assign
- Column Actions với các nút:
  - View details (icon) - Chuyển đến màn hình MH-OPP-02
  - Assign Leader (icon/dropdown)
  - More actions (dropdown)

### Assign Leader Modal
- Dropdown chọn Leader từ danh sách (lấy từ API-AUTH-003/me tùy theo vai trò)
- Tùy chọn thêm ghi chú
- Buttons: Cancel và Confirm

### Kanban View (Optional)
- Mỗi cột đại diện cho một Deal Stage từ Hubspot
- Thẻ (Card) đại diện cho một cơ hội, hiển thị các thông tin tóm tắt quan trọng:
  - Tên cơ hội và khách hàng
  - Giá trị dự kiến
  - Trạng thái Follow-up (indicator màu)
  - Leader được phân công
- Kéo thả thẻ giữa các cột để cập nhật Deal Stage (cần confirm và đồng bộ lại Hubspot nếu có thể)

### Pagination
- Hiển thị "Showing X to Y of Z entries"
- Page number navigation
- Records per page selector (10, 20, 50)

## Phân quyền truy cập
- **Admin**: Xem tất cả cơ hội, đầy đủ quyền Đồng bộ thủ công, xem logs
- **General Manager (課長)**: Xem tất cả cơ hội (có thể giới hạn trong bộ phận/team của mình), quyền assign Team Leader và Đồng bộ thủ công
- **Team Leader (部長)**: Xem các cơ hội được assign cho mình, hoặc tất cả cơ hội (tùy cấu hình), quyền truy cập chi tiết để xem/thêm ghi chú
- **Sales**: Xem các cơ hội mình phụ trách (đồng bộ từ Hubspot), quyền truy cập chi tiết để xem/thêm ghi chú
- Áp dụng các quyền chi tiết theo định nghĩa trong file permissions_definition.md

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CORE-005: Modal components (dependency)
- FE-OPP-002: Opportunity Detail Page (related)
- FE-OPP-004: Hubspot Sync controls (related)

## Tiêu chí chấp nhận
- Danh sách cơ hội hiển thị đúng theo thiết kế MH-OPP-01.png
- Tìm kiếm và lọc hoạt động chính xác, kết quả load trong < 2 giây
- Phân trang hoạt động mượt mà với số lượng record lớn
- Trạng thái follow-up hiển thị chính xác với màu sắc tương ứng
- Chức năng Assign Leader hoạt động chính xác
- Manual Hubspot sync khởi chạy thành công
- UI responsive từ desktop đến tablet (>= 768px)
- Hiển thị thông báo phù hợp khi không có kết quả hoặc lỗi sync
- Lưu trạng thái bộ lọc người dùng đã chọn cho lần truy cập sau
- RBAC áp dụng đúng (phân quyền xem/quản lý theo vai trò)
- (Optional) Nếu triển khai Kanban view, các thẻ hiển thị chính xác và kéo thả hoạt động đúng 