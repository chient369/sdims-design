# Task Detail: FE-CTR-003

## Thông tin chung
- **ID**: FE-CTR-003
- **Tên task**: Xây dựng Contract Form (MH-CTR-03)
- **Độ ưu tiên**: Cao
- **Estimate**: 4 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-CTR-03.md](../../../DD/SCREEN/MH-CTR-03.md)
  - API: [API-CTR-004](../../../DD/API/API-CTR-004.md), [API-CTR-005](../../../DD/API/API-CTR-005.md), [API-CTR-007](../../../DD/API/API-CTR-007.md), [API-CTR-010](../../../DD/API/API-CTR-010.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-008: Xây dựng File Upload/Preview components
  - FE-CTR-001: Xây dựng Contract List Page (MH-CTR-01)
  - FE-CTR-002: Xây dựng Contract Detail Page (MH-CTR-02)

## Mô tả
Xây dựng form thêm mới và chỉnh sửa hợp đồng, cho phép người dùng nhập các thông tin chi tiết của hợp đồng bao gồm thông tin cơ bản, giá trị và ngày tháng, điều khoản thanh toán, liên kết với cơ hội kinh doanh và nhân sự, cùng với tài liệu đính kèm. Form này có hai chế độ hoạt động: "Thêm mới" (khi tạo hợp đồng mới) và "Chỉnh sửa" (khi cập nhật hợp đồng hiện có). Chỉ những người dùng có quyền thích hợp (Admin, General Manager, Sales đối với hợp đồng của mình) mới có thể truy cập và sử dụng form này.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Form được chia thành các section rõ ràng:
  - Section 1: Thông tin chung (Mã, tên, khách hàng, loại, trạng thái)
  - Section 2: Giá trị & ngày tháng (Giá trị, đơn vị tiền tệ, ngày ký, hiệu lực, hết hạn)
  - Section 3: Điều khoản thanh toán (Dynamic table)
  - Section 4: Liên kết (Cơ hội, nhân sự)
  - Section 5: Tài liệu đính kèm
- Khu vực action buttons (Lưu, Hủy) ở cuối form
- Responsive design hỗ trợ từ desktop đến tablet

### 2. UI/UX cho Form Chung
- Header hiển thị tiêu đề "Thêm mới Hợp đồng" hoặc "Chỉnh sửa Hợp đồng: [Mã] - [Tên]"
- Navigation breadcrumbs cho phép quay lại danh sách hợp đồng
- Các section có thể collapse/expand để tối ưu không gian hiển thị
- Các trường bắt buộc phải được đánh dấu rõ ràng (*)
- Validation messages hiển thị rõ ràng khi có lỗi
- Scrollable layout cho form dài với sticky header và action buttons

### 3. Section: Thông tin Chung
- Input field cho Mã hợp đồng (có thể tự động sinh hoặc yêu cầu nhập unique)
- Input field cho Tên hợp đồng/dự án (Bắt buộc)
- Dropdown/Autocomplete để chọn Khách hàng
- Dropdown để chọn Người phụ trách Sales (mặc định là người tạo nếu là Sales)
- Dropdown để chọn Loại hợp đồng (Fixed Price, T&M...)
- Dropdown để chọn Trạng thái hợp đồng (Mới ký, Đang thực hiện, Tạm dừng, Hoàn thành, Đã hủy...)
- Text area cho Mô tả/Ghi chú

### 4. Section: Giá trị & Ngày tháng
- Input number cho Giá trị hợp đồng (định dạng tiền tệ)
- Dropdown cho Đơn vị tiền tệ (VND, USD, JPY...)
- DatePicker cho Ngày ký
- DatePicker cho Ngày hiệu lực
- DatePicker cho Ngày hết hạn (dự kiến)

### 5. Section: Điều khoản Thanh toán
- Dynamic table cho phép thêm/sửa/xóa nhiều mốc thanh toán
- Button để thêm mốc thanh toán mới
- Mỗi dòng trong bảng có:
  - Input text cho Mô tả đợt thanh toán
  - DatePicker cho Ngày dự kiến thu
  - Input number cho Số tiền dự kiến
  - Input text cho Ghi chú thanh toán (không bắt buộc)
  - Button xóa mốc
- Tính toán và hiển thị tổng giá trị các mốc thanh toán

### 6. Section: Liên kết
- Dropdown/Autocomplete để chọn Cơ hội kinh doanh (từ danh sách Opportunity)
- UI để thêm nhân sự tham gia:
  - Button mở popup để chọn nhân viên từ danh sách
  - Bảng hiển thị nhân sự đã chọn với:
    - Thông tin nhân viên (Mã, Họ và Tên)
    - Input text cho Vai trò
    - Input number cho % Phân bổ
    - Button xóa nhân sự

### 7. Section: Tài liệu Đính kèm
- File upload area với drag & drop support
- Hiển thị danh sách file đã upload với:
  - Tên file
  - Kích thước
  - Ngày upload
  - Button xóa file
- Progress bar khi upload file
- Hỗ trợ preview file (nếu có thể)

### 8. Action Buttons
- Button "Lưu" để lưu hợp đồng (validation trước khi lưu)
- Button "Hủy" để hủy thay đổi và quay lại màn hình trước
- Confirmation dialog khi có thay đổi chưa lưu

## Xử lý State và Performance

### State Management
- Sử dụng React Hook Form hoặc Formik để quản lý state của form phức tạp
- Triển khai validations theo yêu cầu nghiệp vụ
- Quản lý các dynamic tables (điều khoản thanh toán, nhân sự) hiệu quả
- Lưu trạng thái form tạm thời để tránh mất dữ liệu khi reload

### API Integration
- Kết nối với `API-CTR-004` (`POST /api/v1/contracts`) để tạo hợp đồng mới
- Kết nối với `API-CTR-005` (`PUT /api/v1/contracts/{id}`) để cập nhật hợp đồng hiện có
- Kết nối với `API-CTR-007` (`POST/PUT /api/v1/contracts/{id}/payment-terms`) để quản lý điều khoản thanh toán
- Kết nối với `API-CTR-010` (`POST /api/v1/contracts/{id}/files`) để upload files
- Xử lý multipart/form-data cho upload files
- Implement optimistic updates khi thích hợp

### Performance Considerations
- Lazy loading cho components không cần thiết ngay lập tức
- Pagination/virtualization cho danh sách dài (nếu có)
- Optimized file uploads (chunking cho file lớn)
- Debounce cho các operations như auto-validation
- Memoization cho các calculations phức tạp

## Phân quyền truy cập
- **Admin**: Đầy đủ quyền tạo mới và chỉnh sửa tất cả hợp đồng
- **General Manager (課長)**: Đầy đủ quyền tạo mới và chỉnh sửa tất cả hợp đồng
- **Sales**: Quyền tạo mới hợp đồng và chỉnh sửa hợp đồng mình phụ trách
- **Team Leader (部長)**: Không có quyền truy cập form này
- **Kế toán**: Không có quyền truy cập form này

## Các Task liên quan
- FE-CTR-001: Contract List Page (trang danh sách hợp đồng)
- FE-CTR-002: Contract Detail Page (trang chi tiết hợp đồng)
- FE-CTR-004: Payment Status Update UI (cập nhật trạng thái thanh toán)
- FE-OPP-001: Opportunity List Page (liên kết với cơ hội)
- FE-HRM-001: Employee List Page (liên kết với nhân sự)

## Tiêu chí chấp nhận
1. Form hiển thị đúng theo thiết kế và yêu cầu:
   - Các section được phân chia rõ ràng
   - UI components hoạt động chính xác (dropdowns, date pickers, number inputs, etc.)
   - Responsive từ desktop đến tablet

2. Validation hoạt động chính xác:
   - Hiển thị lỗi cho các trường bắt buộc
   - Validate định dạng dữ liệu (số, ngày tháng)
   - Validate business logic (tổng giá trị điều khoản thanh toán phải bằng giá trị hợp đồng)

3. Dynamic tables hoạt động mượt mà:
   - Thêm/sửa/xóa mốc thanh toán
   - Thêm/xóa nhân sự liên kết

4. File upload:
   - Upload file thành công
   - Hiển thị progress
   - Xóa file chưa lưu

5. Tương tác với API:
   - Tạo mới hợp đồng thành công
   - Cập nhật hợp đồng thành công
   - Upload files đính kèm thành công

6. Phân quyền:
   - Hiển thị form phù hợp với quyền của người dùng
   - Sales chỉ có thể chỉnh sửa hợp đồng mình phụ trách

7. UX/Navigation:
   - Navigation thông qua breadcrumbs hoạt động chính xác
   - Confirm dialog khi có thay đổi chưa lưu
   - Thông báo thành công/lỗi rõ ràng sau khi thực hiện hành động

## Resources
- [Tài liệu thiết kế màn hình MH-CTR-03](../../../DD/SCREEN/MH-CTR-03.md)
- [Tài liệu thiết kế màn hình MH-CTR-01](../../../DD/SCREEN/MH-CTR-01.md) (liên quan đến navigation)
- [Tài liệu thiết kế màn hình MH-CTR-02](../../../DD/SCREEN/MH-CTR-02.md) (liên quan đến navigation)
- [Danh sách API - Contract Management](../../../BD/API/api_list.md#4-quản-lý-hợp-đồng-và-doanh-thu) 