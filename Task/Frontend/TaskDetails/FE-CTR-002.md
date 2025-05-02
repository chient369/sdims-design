# Task Detail: FE-CTR-002

## Thông tin chung
- **ID**: FE-CTR-002
- **Tên task**: Xây dựng Contract Detail Page (MH-CTR-02)
- **Độ ưu tiên**: Cao
- **Estimate**: 4 days
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-CTR-02.md](../../../DD/SCREEN/MH-CTR-02.md)
  - API: [API-CTR-003](../../../DD/API/API-CTR-003.md), [API-CTR-006](../../../DD/API/API-CTR-006.md), [API-CTR-009](../../../DD/API/API-CTR-009.md), [API-CTR-012](../../../DD/API/API-CTR-012.md)

## Mô tả
Xây dựng trang chi tiết hợp đồng, hiển thị toàn bộ thông tin của một hợp đồng cụ thể bao gồm thông tin cơ bản, điều khoản thanh toán, trạng thái thu tiền, files đính kèm và danh sách nhân viên được phân bổ. Trang này cho phép người dùng xem chi tiết đầy đủ và chuyển đến chức năng chỉnh sửa hợp đồng hoặc cập nhật trạng thái thanh toán tùy theo quyền hạn. Giao diện được tổ chức theo các tab để dễ dàng truy cập các nhóm thông tin khác nhau của hợp đồng, đồng thời cung cấp các hành động phù hợp với vai trò của người dùng.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với thông tin cơ bản của hợp đồng và action buttons
- Tab navigation để chuyển đổi giữa các nhóm thông tin:
  - Tab 1: Thông tin hợp đồng (Contract Info)
  - Tab 2: Điều khoản thanh toán (Payment Terms)
  - Tab 3: Files đính kèm (Attachments)
  - Tab 4: Nhân viên phân bổ (Allocated Employees)
- Content area hiển thị chi tiết tương ứng với tab được chọn
- Action buttons phù hợp với từng tab và quyền người dùng

### UI Components
- Sử dụng Tailwind CSS cho styling
- Header với các thông tin cơ bản và status indicator
- Tab component để chuyển đổi giữa các nhóm thông tin
- Info cards hiển thị chi tiết với label-value pairs
- DataTable cho danh sách điều khoản thanh toán
- File list/grid cho files đính kèm với preview
- DataTable cho danh sách nhân viên được phân bổ
- Status badges với color indicators
- Action buttons và dropdowns
- File preview modal/lightbox

### Functionality
- Hiển thị thông tin chi tiết hợp đồng dựa trên ID được truyền vào
- Tab navigation không làm mất trạng thái/reload trang
- File preview/download
- Links đến các trang liên quan (Chỉnh sửa hợp đồng, Cập nhật trạng thái thanh toán)
- Actions context-sensitive dựa trên vai trò người dùng
- Timeline visualization cho điều khoản thanh toán (trạng thái, lịch sử)

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/contracts/{contractId}` (API-CTR-003) - Lấy chi tiết hợp đồng
  - `GET /api/v1/contracts/{contractId}/payment-terms` (API-CTR-006) - Lấy điều khoản thanh toán
  - `GET /api/v1/contracts/{contractId}/files` (API-CTR-009) - Lấy danh sách file đính kèm
  - `GET /api/v1/contracts/{contractId}/employees` (API-CTR-012) - Lấy danh sách nhân viên liên kết

## Chi tiết các thành phần
Dựa trên mô tả [MH-CTR-02.md](../../../DD/SCREEN/MH-CTR-02.md):

### Header Section
- Breadcrumb navigation (Hợp đồng > [Tên hợp đồng])
- Tiêu đề hợp đồng, mã hợp đồng
- Status badge hiển thị trạng thái thanh toán tổng thể (với color indicator)
- Button "Chỉnh sửa" (nếu có quyền)
- Button "Cập nhật trạng thái thanh toán" (nếu có quyền Kế toán)
- Other actions dropdown (in, export, etc.)

### Tab 1: Thông tin hợp đồng
- Card view với các nhóm thông tin:
  - Thông tin cơ bản:
    - Mã hợp đồng
    - Tên hợp đồng
    - Khách hàng
    - Loại hợp đồng
    - Giá trị hợp đồng (định dạng tiền tệ)
  - Thời gian và trạng thái:
    - Ngày ký
    - Ngày hiệu lực
    - Ngày kết thúc (nếu có)
    - Trạng thái hợp đồng
  - Thông tin bổ sung:
    - Người phụ trách (Sales)
    - Mô tả
    - Ghi chú (nếu có)
    - Liên kết với cơ hội (nếu có)

### Tab 2: Điều khoản thanh toán
- Summary card hiển thị tổng quan thanh toán:
  - Tổng giá trị hợp đồng
  - Đã thanh toán (giá trị và %)
  - Còn lại (giá trị và %)
  - Visualization (progress bar)
- DataTable điều khoản thanh toán với các columns:
  - Đợt thanh toán
  - Milestone/Mô tả
  - Giá trị (định dạng tiền tệ)
  - Ngày dự kiến thanh toán
  - Trạng thái thanh toán (với badge màu):
    - Chưa đến hạn (Blue)
    - Đã thanh toán (Green)
    - Đến hạn (Yellow)
    - Quá hạn (Red)
  - Ngày thực thanh toán (nếu đã thanh toán)
  - Actions (cập nhật trạng thái - nếu có quyền)

### Tab 3: Files đính kèm
- File upload area (nếu có quyền)
- List/Grid view của các files với:
  - Icon/Thumbnail theo loại file
  - Tên file
  - Loại file
  - Kích thước
  - Ngày upload
  - Người upload
  - Actions (download, delete - nếu có quyền)
- Preview modal khi click vào file (hỗ trợ các loại file phổ biến)

### Tab 4: Nhân viên phân bổ
- DataTable nhân viên với columns:
  - Thông tin nhân viên (Tên, Mã NV)
  - Team/Bộ phận
  - Vị trí
  - % Phân bổ vào hợp đồng
  - Thời gian phân bổ (từ ngày - đến ngày)
  - Actions (remove - nếu có quyền)
- Button "Thêm nhân viên" (nếu có quyền)

## Phân quyền truy cập
- **Admin**: Xem tất cả thông tin, đầy đủ quyền chỉnh sửa
- **General Manager (課長)**: Xem tất cả thông tin, quyền chỉnh sửa
- **Team Leader (部長)**: Xem hợp đồng liên quan đến team mình, quyền chỉnh sửa hạn chế
- **Sales**: Xem hợp đồng mình phụ trách, chỉnh sửa hợp đồng mình phụ trách
- **Kế toán**: Xem tất cả thông tin, chỉ có quyền cập nhật trạng thái thanh toán

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CORE-008: File Upload/Preview components (dependency)
- FE-CTR-001: Contract List Page (related)
- FE-CTR-003: Contract Form (related)
- FE-CTR-004: Payment Status Update UI (related)

## Tiêu chí chấp nhận
- Trang chi tiết hiển thị đúng thông tin hợp đồng từ API
- Tab navigation hoạt động mượt mà không reload trang
- Điều khoản thanh toán hiển thị đúng với color indicators
- File attachments hiển thị và cho phép download/preview
- Danh sách nhân viên hiển thị chính xác
- Actions (Edit, Update Payment Status) điều hướng đến đúng chức năng
- Phân quyền áp dụng chính xác (hiển thị/ẩn các action buttons theo vai trò)
- UI responsive từ desktop đến tablet (>= 768px)
- File preview hoạt động với các loại file phổ biến (PDF, image, etc.)
- Performance: trang load dưới 3 giây
- Status indicators (badges, progress bars) hiển thị chính xác theo dữ liệu 