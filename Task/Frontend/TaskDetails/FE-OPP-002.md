# Task Detail: FE-OPP-002

## Thông tin chung
- **ID**: FE-OPP-002
- **Tên task**: Xây dựng Opportunity Detail Page (MH-OPP-02)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-OPP-02](../../../assets/frontend/img/MH-OPP-02.png),
  [MH-OPP-02 - TAB Lịch sử tương tác](../../../assets/frontend/img/MH-OPP-02%20-%20TAB%20L%E1%BB%8Bch%20s%E1%BB%AD%20t%C6%B0%C6%A1ng%20t%C3%A1c.png),
  [MH-OPP-02 - TAB Tài liệu đính kèm](../../../assets/frontend/img/MH-OPP-02%20-%20TAB%20T%C3%A0i%20li%E1%BB%87u%20%C4%91%C3%ADnh%20k%C3%A8m.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-OPP-02.md](../../../DD/SCREEN/MH-OPP-02.md)
  - API: [API-OPP-002](../../../DD/API/API-OPP-002.md), [API-OPP-005](../../../DD/API/API-OPP-005.md), [API-OPP-006](../../../DD/API/API-OPP-006.md), [API-OPP-007](../../../DD/API/API-OPP-007.md), [API-OPP-008](../../../DD/API/API-OPP-008.md)

## Mô tả
Xây dựng trang chi tiết cơ hội kinh doanh với nội dung đầy đủ về thông tin cơ hội, lịch sử tương tác và tài liệu đính kèm. Trang này cho phép người dùng xem thông tin chi tiết của một cơ hội cụ thể đồng bộ từ Hubspot, thực hiện các hành động như assign cho Leader, thêm ghi chú theo dõi và quản lý tài liệu đính kèm. Đây là nơi Sales, Leader và quản lý có thể theo dõi cập nhật tình trạng cơ hội.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với thông tin cơ bản và action buttons (Back, Assign, Priority)
- Tab navigation để chuyển đổi giữa các phần thông tin:
  - Thông tin cơ hội (mặc định)
  - Lịch sử tương tác
  - Tài liệu đính kèm
- Nội dung tab hiển thị thông tin tương ứng
- Content area có thể scroll nếu nội dung dài
- Sidebar hiển thị trạng thái và thông tin tóm tắt

### UI Components
- Sử dụng Tailwind CSS cho styling
- Header với thông tin cơ bản và các action button
- Tab navigation với indicators cho tab đang active
- Card layouts cho từng section thông tin
- Timeline component cho lịch sử tương tác
- Form components cho thêm ghi chú
- File upload/preview cho tài liệu đính kèm
- Status indicators cho trạng thái follow-up

### Functionality
- Chuyển đổi giữa các tab mà không reload trang
- Hiển thị các action button tùy theo quyền của người dùng
- Thêm ghi chú/log hoạt động
- Upload và quản lý tài liệu đính kèm
- Assign cơ hội cho Leader
- Đánh dấu ưu tiên Onsite
- Tự động cập nhật ngày tương tác cuối khi thêm ghi chú
- Hiển thị trạng thái follow-up với màu sắc tương ứng

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/opportunities/{oppId}` (API-OPP-002) - Lấy chi tiết cơ hội
  - `POST /api/v1/opportunities/{oppId}/assign` (API-OPP-005) - Gán Leader vào cơ hội
  - `POST /api/v1/opportunities/{oppId}/notes` (API-OPP-006) - Thêm ghi chú
  - `GET /api/v1/opportunities/{oppId}/notes` (API-OPP-007) - Lấy danh sách ghi chú
  - `PUT /api/v1/opportunities/{oppId}/onsite` (API-OPP-008) - Đánh dấu ưu tiên Onsite

## Chi tiết các thành phần
Dựa trên thiết kế [MH-OPP-02.png](../../../assets/frontend/img/MH-OPP-02.png) và các hình ảnh tab chi tiết:

### Header Section
- Breadcrumb (Cơ hội > Chi tiết)
- Tiêu đề cơ hội (tên)
- Badge trạng thái follow-up (Red/Yellow/Green)
- Button "Back" - Quay lại danh sách
- Button "Assign Leader" (nếu có quyền)
- Toggle/Checkbox "Ưu tiên Onsite" (nếu có quyền)

### Tab Navigation
- Tab "Thông tin cơ hội" (mặc định selected)
- Tab "Lịch sử tương tác"
- Tab "Tài liệu đính kèm"

### Tab Content: Thông tin cơ hội
- Card thông tin cơ hội:
  - Tên cơ hội
  - Mã/ID Hubspot
  - Khách hàng
  - Giá trị dự kiến (số tiền + đơn vị tiền tệ)
  - Giai đoạn (Deal Stage)
  - Người phụ trách Sales (từ Hubspot)
  - Leader được assign
  - Ngày tạo
  - Ngày tương tác cuối
  - Mô tả cơ hội (nếu có)
- Card thông tin khách hàng:
  - Tên công ty
  - Lĩnh vực
  - Địa chỉ
  - Website
  - Người liên hệ chính

### Tab Content: Lịch sử tương tác
- Form thêm ghi chú mới:
  - Textarea cho nội dung ghi chú
  - Button "Thêm ghi chú"
- Timeline hiển thị các ghi chú và hoạt động:
  - Sắp xếp theo thời gian giảm dần (mới nhất lên đầu)
  - Mỗi mục hiển thị:
    - Thời gian tạo
    - Người tạo
    - Nội dung ghi chú
    - Loại hoạt động (Ghi chú, Assign, Cập nhật trạng thái...)
  - Phân trang nếu có nhiều hoạt động

### Tab Content: Tài liệu đính kèm
- Upload control cho thêm tài liệu mới (drag & drop hoặc button)
- Danh sách tài liệu đã đính kèm:
  - Tên file
  - Kích thước
  - Loại file (với icon tương ứng)
  - Ngày upload
  - Người upload
  - Action buttons (Download, Delete nếu có quyền)
- Preview tài liệu khi click vào (nếu hỗ trợ định dạng)

### Modals/Dialogs
- Modal Assign Leader:
  - Dropdown chọn Leader
  - Ô nhập ghi chú/lý do
  - Buttons: Cancel và Confirm
- Modal xác nhận xóa tài liệu đính kèm

## Phân quyền truy cập
- **Admin**: Xem tất cả thông tin, đầy đủ quyền Assign Leader, thêm ghi chú, đánh dấu ưu tiên, quản lý tài liệu
- **General Manager (課長)**: Xem tất cả thông tin, quyền Assign Leader, thêm ghi chú, đánh dấu ưu tiên, quản lý tài liệu
- **Team Leader (部長)**: Xem cơ hội được assign cho mình, quyền thêm ghi chú, quản lý tài liệu
- **Sales**: Xem cơ hội do mình phụ trách (đồng bộ từ Hubspot), quyền thêm ghi chú, quản lý tài liệu
- Áp dụng các quyền chi tiết theo định nghĩa trong file permissions_definition.md

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-002: Tab components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-CORE-005: Modal components (dependency)
- FE-CORE-008: File Upload/Preview components (dependency)
- FE-OPP-001: Opportunity List Page (related)
- FE-OPP-003: Note/Activity components (related)

## Tiêu chí chấp nhận
- Trang chi tiết cơ hội hiển thị đúng theo thiết kế MH-OPP-02.png và các thiết kế tab
- Tất cả các tab hiển thị đầy đủ và chính xác thông tin
- API calls được thực hiện đúng và hiệu quả (tránh gọi không cần thiết khi chuyển tab)
- Chức năng thêm ghi chú hoạt động chính xác và cập nhật ngày tương tác cuối
- Chức năng Assign Leader hoạt động chính xác
- Chức năng Upload/manage tài liệu hoạt động chính xác
- UI responsive từ desktop đến tablet (>= 768px)
- Tab navigation hoạt động mượt mà, không reload trang
- Các action (assign, thêm ghi chú, upload) hiển thị dựa trên quyền người dùng
- Timeline lịch sử tương tác hiển thị đúng theo thứ tự thời gian
- RBAC áp dụng đúng (hiển thị các action chỉ khi có quyền tương ứng) 