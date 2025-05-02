# Task Detail: FE-CTR-005

## Thông tin chung
- **ID**: FE-CTR-005
- **Tên task**: Xây dựng Revenue KPI Management (MH-CTR-05)
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-CTR-05.md](../../../DD/SCREEN/MH-CTR-05.md)
  - API: [API-CTR-014](../../../DD/API/API-CTR-014.md), [API-CTR-015](../../../DD/API/API-CTR-015.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components

## Mô tả
Xây dựng giao diện quản lý KPI doanh thu dành cho Admin hoặc Quản lý cấp cao (General Manager), cho phép thiết lập, xem và quản lý chỉ tiêu doanh thu (Target Revenue) cho từng nhân viên Sales theo các kỳ (Tháng/Quý/Năm). Dữ liệu KPI này sẽ được sử dụng trong các biểu đồ dashboard và báo cáo để so sánh với doanh thu thực tế. Màn hình bao gồm các chức năng quản lý, bộ lọc linh hoạt và form nhập liệu/chỉnh sửa KPI, đồng thời hỗ trợ cả thao tác đơn lẻ và hàng loạt.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Khu vực bộ lọc ở phía trên để lọc theo Nhân viên Sales, Kỳ (Năm, Quý, Tháng)
- Khu vực action button "Thêm mới KPI" nổi bật
- Bảng danh sách KPI đã thiết lập
- Modal/Popup form để thêm mới/chỉnh sửa KPI
- Khu vực thông báo kết quả
- Responsive design hỗ trợ từ desktop đến tablet

### 2. Khu vực Bộ lọc

#### UI/UX
- Dropdown/Multi-select cho Nhân viên Sales (tìm kiếm và chọn nhiều)
- Bộ lọc Kỳ:
  - Dropdown chọn Năm (bắt buộc)
  - Dropdown chọn Quý (1-4, optional)
  - Dropdown chọn Tháng (1-12, optional)
- Nút "Xem/Lọc" và "Xóa bộ lọc"
- Filter chips hiển thị các bộ lọc đang áp dụng
- Collapsible panel để tối ưu không gian hiển thị

#### Functionality
- Lọc KPI theo nhân viên và thời gian
- Phân cấp lọc theo độ chi tiết thời gian (Năm -> Quý -> Tháng)
- Lưu trạng thái bộ lọc trong session/local storage
- Reset bộ lọc về trạng thái mặc định (Năm hiện tại, tất cả Sales)

### 3. Bảng Danh sách KPI

#### UI/UX
- DataTable hiển thị danh sách KPI phù hợp với bộ lọc
- Columns:
  - Nhân viên Sales (tên, avatar nếu có)
  - Kỳ (hiển thị Năm/Quý/Tháng theo định dạng phù hợp)
  - KPI Doanh thu (Target Revenue, định dạng tiền tệ)
  - Đơn vị tiền tệ
  - Ngày tạo/cập nhật (optional)
  - Người tạo/cập nhật (optional)
  - Actions (Sửa, Xóa)
- Sorting theo các cột (mặc định theo thời gian tạo mới nhất)
- Pagination controls cho danh sách dài
- Empty state khi không có dữ liệu
- Visual indicators cho KPI mới thêm/cập nhật

#### Functionality
- Load danh sách KPI từ API theo bộ lọc
- Sorting và pagination
- Click vào nút "Sửa" để mở form edit KPI
- Click vào nút "Xóa" hiển thị confirm dialog trước khi xóa
- Refresh data sau khi thêm/sửa/xóa KPI

### 4. Form Nhập liệu KPI (Modal/Popup)

#### UI/UX
- Modal/Popup form đầy đủ khi người dùng click "Thêm mới KPI" hoặc "Sửa"
- Fields:
  - Dropdown chọn Nhân viên Sales (required, disabled khi Edit)
  - Dropdown chọn Năm (required)
  - Dropdown chọn Quý (optional)
  - Dropdown chọn Tháng (optional, disable khi đã chọn Quý)
  - Input number cho KPI Doanh thu (required, định dạng tiền tệ)
  - Dropdown chọn Đơn vị tiền tệ (required)
- Nút "Lưu" và "Hủy"
- Validation errors hiển thị rõ ràng

#### Functionality
- Form validation đầy đủ
  - Nhân viên Sales bắt buộc phải chọn
  - Năm bắt buộc
  - KPI phải là số dương
  - Đơn vị tiền tệ bắt buộc
  - Không được trùng KPI (cùng Sales và cùng kỳ)
- Submit form để tạo mới hoặc cập nhật KPI
- Kiểm tra và hiển thị thông báo nếu KPI đã tồn tại
- Reset form sau khi submit thành công

### 5. Batch Operations (Optional Enhancement)

#### UI/UX
- Nút "Import KPI" để import nhiều KPI cùng lúc
- Template download cho import
- Upload area cho file import
- Progress và kết quả import

#### Functionality
- Tạo template Excel/CSV với cấu trúc phù hợp
- Validation khi import (định dạng đúng, dữ liệu hợp lệ)
- Xử lý lỗi và hiển thị báo cáo kết quả import

## Xử lý State và Performance

### State Management
- Quản lý state của các bộ lọc và kết quả tìm kiếm
- Tracking form state và validations
- Optimistic UI updates (thêm/sửa/xóa trực tiếp trên UI trước khi API hoàn thành)
- Form state persistence khi chuyển tab hoặc đóng modal

### API Integration
- Kết nối với `API-CTR-014` (`GET /api/v1/revenue-kpis`) để lấy danh sách KPI
- Kết nối với `API-CTR-015` (`POST/PUT/DELETE /api/v1/revenue-kpis`) để tạo mới/cập nhật/xóa KPI
- Error handling và retry mechanism
- Batch processing cho import hàng loạt (nếu triển khai)

### Performance Considerations
- Pagination cho danh sách lớn
- Caching data để giảm số lần gọi API
- Debounce cho các thao tác filter
- Lazy loading components khi cần

## Phân quyền truy cập
- **Admin**: Đầy đủ quyền xem, thêm, sửa, xóa tất cả KPI
- **General Manager (課長)**: Đầy đủ quyền xem, thêm, sửa, xóa KPI
- **Team Leader (部長)**: Chỉ xem KPI trên dashboard, không truy cập màn hình quản lý
- **Sales**: Chỉ xem KPI của mình trên dashboard, không truy cập màn hình quản lý
- **Kế toán**: Không có quyền truy cập màn hình quản lý KPI

## Các Task liên quan
- FE-DSH-003: Revenue/Margin widgets (sử dụng dữ liệu KPI)
- FE-CTR-001: Contract List Page (liên quan đến doanh thu)
- FE-CTR-004: Payment Status Update UI (ảnh hưởng đến doanh thu thực tế)
- FE-RPT-002: Report Viewer (sử dụng dữ liệu KPI cho báo cáo)

## Tiêu chí chấp nhận
1. Bộ lọc:
   - Lọc chính xác theo Nhân viên Sales và Kỳ (Năm/Quý/Tháng)
   - Hiển thị kết quả lọc trong < 2 giây
   - Reset bộ lọc hoạt động đúng

2. Bảng Danh sách KPI:
   - Hiển thị chính xác danh sách KPI theo bộ lọc
   - Sorting và pagination hoạt động chính xác
   - Actions (Sửa, Xóa) hoạt động đúng

3. Form Nhập liệu KPI:
   - Hiển thị form đúng cho các trường hợp thêm mới/chỉnh sửa
   - Validation hoạt động chính xác (required fields, trùng lặp KPI)
   - Submit form thành công và hiển thị kết quả

4. Thêm/Sửa/Xóa KPI:
   - Thêm mới KPI thành công
   - Cập nhật KPI thành công
   - Xóa KPI có confirm và thực hiện thành công

5. Phân quyền:
   - Chỉ Admin và General Manager mới truy cập được màn hình
   - Các vai trò khác không thể truy cập hoặc thấy nút dẫn đến màn hình

6. Hiệu năng:
   - Thời gian tải danh sách < 2 giây
   - Form validation phản hồi nhanh
   - Modal/popup mở và đóng mượt mà

## Resources
- [Tài liệu thiết kế màn hình MH-CTR-05](../../../DD/SCREEN/MH-CTR-05.md)
- [Danh sách API - Contract Management](../../../BD/API/api_list.md#4-quản-lý-hợp-đồng-và-doanh-thu) 