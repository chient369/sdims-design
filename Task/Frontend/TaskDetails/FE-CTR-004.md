# Task Detail: FE-CTR-004

## Thông tin chung
- **ID**: FE-CTR-004
- **Tên task**: Xây dựng Payment Status Update UI (MH-CTR-04)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-CTR-04.md](../../../DD/SCREEN/MH-CTR-04.md)
  - API: [API-CTR-008](../../../DD/API/API-CTR-008.md), [API-CTR-013](../../../DD/API/API-CTR-013.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components
  - FE-CORE-008: Xây dựng File Upload/Preview components
  - FE-CTR-002: Xây dựng Contract Detail Page (MH-CTR-02)

## Mô tả
Xây dựng giao diện người dùng (UI) dành riêng cho bộ phận Kế toán để cập nhật trạng thái thanh toán của các đợt thanh toán trong hợp đồng. Màn hình này tách biệt với giao diện chi tiết hợp đồng để đảm bảo tính chính xác và kiểm soát đối với dữ liệu tài chính. Màn hình hỗ trợ hai phương thức cập nhật: cập nhật thủ công từng đợt thanh toán và import hàng loạt từ file (Excel/CSV). Chỉ người dùng có vai trò Kế toán và Admin mới có quyền truy cập và thao tác trên màn hình này.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Khu vực bộ lọc ở phía trên để lọc các đợt thanh toán cần cập nhật
- Tab navigation để chuyển đổi giữa hai phương thức cập nhật:
  - Tab 1: Cập nhật Thủ công
  - Tab 2: Import từ File
- Khu vực hiển thị thông báo kết quả ở cuối màn hình
- Responsive design hỗ trợ từ desktop đến tablet

### 2. Khu vực Bộ lọc

#### UI/UX
- Search bar để tìm kiếm nhanh theo Mã HĐ, Tên HĐ, hoặc Khách hàng
- Dropdown filters cho:
  - Hợp đồng (chọn từ danh sách có điều khoản thanh toán)
  - Khách hàng
  - Trạng thái thanh toán (Chưa thu, Quá hạn)
  - DateRangePicker cho ngày dự kiến thu
- Nút "Tìm kiếm/Lọc" và "Xóa bộ lọc"
- Filter chips hiển thị các bộ lọc đang áp dụng
- Collapsible panel để tối ưu không gian hiển thị

#### Functionality
- Lọc đợt thanh toán theo các tiêu chí đã chọn
- Tìm kiếm nhanh với auto-suggestion
- Lưu trạng thái bộ lọc trong session/local storage
- Reset bộ lọc về trạng thái mặc định

### 3. Tab: Cập nhật Thủ công

#### UI/UX
- Bảng hiển thị danh sách đợt thanh toán phù hợp với bộ lọc
- Các cột cố định (read-only):
  - Mã HĐ (có thể là link đến trang chi tiết hợp đồng)
  - Tên HĐ
  - Khách hàng
  - Mô tả Đợt TT
  - Ngày dự kiến Thu
  - Số tiền dự kiến
- Các cột có thể chỉnh sửa:
  - Trạng thái Thu tiền (Dropdown: Chưa thu, Đã thu)
  - Ngày Thu thực tế (DatePicker)
  - Số tiền Thực thu (Input number)
  - Ghi chú TT (Input text)
- Nút "Lưu thay đổi" ở cuối bảng
- Visual indicators cho các dòng đã thay đổi
- Hỗ trợ inline editing hoặc popup editing

#### Functionality
- Editable cells cho phép cập nhật thông tin thanh toán
- Validation các trường bắt buộc và định dạng dữ liệu
- Inline validation và hiển thị lỗi khi nhập liệu không hợp lệ
- Lưu các thay đổi đã thực hiện thông qua API
- Cơ chế xử lý lỗi và hiển thị thông báo kết quả

### 4. Tab: Import từ File

#### UI/UX
- Khu vực hướng dẫn rõ ràng về cách thức sử dụng
- Link "Tải file mẫu (Template)" nổi bật
- Khu vực upload với drag & drop support
- Hiển thị tên file đã chọn và trạng thái
- Progress bar khi đang xử lý file
- Nút "Import Dữ liệu Thanh toán"
- Khu vực hiển thị kết quả validation và import

#### Functionality
- Tạo và tải template Excel/CSV với cấu trúc phù hợp
- Upload file lên server
- Validation file (định dạng, cấu trúc)
- Validation dữ liệu (tồn tại hợp đồng/đợt thanh toán, tính hợp lệ của dữ liệu)
- Hiển thị kết quả validation chi tiết trước khi import
- Xử lý import vào database
- Hiển thị báo cáo kết quả (số lượng thành công, lỗi)

### 5. Khu vực Phản hồi/Kết quả
- Toast notifications cho kết quả thao tác (thành công/lỗi)
- Chi tiết lỗi được hiển thị rõ ràng
- Summary kết quả cập nhật (số lượng records đã cập nhật)
- Log lại các thao tác cập nhật để tracking

## Xử lý State và Performance

### State Management
- Quản lý state của bảng dữ liệu và các cell đang edit
- Tracking các thay đổi để chỉ gửi những dữ liệu đã được chỉnh sửa
- Quản lý trạng thái validation và hiển thị lỗi
- Lưu trạng thái tab và bộ lọc

### API Integration
- Kết nối với `API-CTR-008` (`PUT /api/v1/contracts/payment-terms/{id}`) để cập nhật trạng thái thanh toán
- Kết nối với `API-CTR-013` (`POST /api/v1/contracts/payment-terms/import`) để import dữ liệu từ file
- Xử lý batch updates khi cần
- Xử lý multipart/form-data cho upload file
- Error handling và retry mechanism

### Performance Considerations
- Pagination cho danh sách lớn
- Virtualized table để xử lý hiệu quả danh sách dài
- Optimized batch updates
- Debounce cho inline validation
- Caching và invalidation strategy phù hợp

## Phân quyền truy cập
- **Kế toán (Accountant)**: Vai trò chính, có quyền xem và cập nhật trạng thái thanh toán
- **Admin**: Có quyền truy cập để hỗ trợ hoặc xem log
- **General Manager (課長)**: Không có quyền truy cập màn hình này
- **Team Leader (部長)**: Không có quyền truy cập màn hình này
- **Sales**: Không có quyền truy cập màn hình này

## Các Task liên quan
- FE-CTR-001: Contract List Page (trang danh sách hợp đồng)
- FE-CTR-002: Contract Detail Page (trang chi tiết hợp đồng)
- FE-CTR-003: Contract Form (form tạo/sửa hợp đồng)
- FE-CTR-005: Revenue KPI Management (quản lý KPI doanh thu)

## Tiêu chí chấp nhận
1. Bộ lọc:
   - Lọc chính xác theo các tiêu chí
   - Search box hoạt động đúng
   - Hiển thị kết quả lọc trong < 2 giây

2. Cập nhật Thủ công:
   - Hiển thị chính xác danh sách đợt thanh toán cần cập nhật
   - Inline/popup editing hoạt động mượt mà
   - Validation hoạt động chính xác
   - Lưu thay đổi thành công và hiển thị kết quả

3. Import từ File:
   - Tạo template với cấu trúc đúng
   - Upload file thành công
   - Validation dữ liệu chính xác
   - Import thành công và hiển thị kết quả
   - Báo cáo lỗi chi tiết nếu có

4. Phân quyền:
   - Chỉ Kế toán và Admin mới truy cập được màn hình
   - Các vai trò khác không thể truy cập

5. Hiệu năng:
   - Thời gian tải danh sách < 3 giây với 200 bản ghi
   - Inline editing phản hồi trong < 0.5 giây
   - Import file lớn (50+ bản ghi) hoàn thành trong < 10 giây

6. UX:
   - Tab navigation mượt mà
   - Thông báo rõ ràng sau mỗi thao tác
   - Hiển thị trạng thái loading khi cần

## Resources
- [Tài liệu thiết kế màn hình MH-CTR-04](../../../DD/SCREEN/MH-CTR-04.md)
- [Tài liệu thiết kế màn hình MH-CTR-02](../../../DD/SCREEN/MH-CTR-02.md) (liên quan đến danh sách thanh toán)
- [Danh sách API - Contract Management](../../../BD/API/api_list.md#4-quản-lý-hợp-đồng-và-doanh-thu) 