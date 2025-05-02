# Task Detail: FE-ADM-003

## Thông tin chung
- **ID**: FE-ADM-003
- **Tên task**: Xây dựng System Configuration Page (MH-ADM-03)
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - API: [API-ADM-011](../../../DD/API/API-ADM-011.md), [API-ADM-012](../../../DD/API/API-ADM-012.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components
  - FE-ADM-001: Xây dựng User Management Page (MH-ADM-01)
  - FE-ADM-002: Xây dựng Role & Permission Management Page (MH-ADM-02)

## Mô tả
Xây dựng trang cấu hình hệ thống (System Configuration) cho phép Admin tùy chỉnh và quản lý các tham số và cài đặt của hệ thống. Trang này bao gồm các cấu hình quan trọng như ngưỡng margin cảnh báo, ngưỡng follow-up cơ hội kinh doanh, cài đặt tích hợp với Hubspot, và các tham số hệ thống khác. Màn hình này chỉ được truy cập bởi người dùng có quyền Admin, với giao diện trực quan và có tổ chức phân theo danh mục để dễ dàng tìm kiếm và cập nhật cấu hình.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Header section với tiêu đề "Cấu hình Hệ thống" và search bar
- Tab navigation hoặc sidebar để chuyển đổi giữa các danh mục cấu hình
- Main content area hiển thị danh sách cấu hình của danh mục đang chọn
- Form chỉnh sửa cấu hình (inline hoặc modal/popup)
- Responsive design hỗ trợ từ desktop đến tablet

### 2. UI/UX cho Danh sách Cấu hình

#### UI Components
- Search bar để tìm kiếm nhanh theo tên cấu hình hoặc mô tả
- Danh mục cấu hình được hiển thị dưới dạng tabs hoặc menu bên (SYSTEM, MARGIN, OPPORTUNITY, INTEGRATION, SECURITY, etc.)
- Table hiển thị danh sách cấu hình với các cột:
  - Tên cấu hình (key)
  - Mô tả (description)
  - Giá trị hiện tại (value)
  - Kiểu dữ liệu (type)
  - Thao tác (chỉnh sửa/reset)
- Indicator cho cấu hình không thể chỉnh sửa (isEditable=false)
- Tooltip giải thích chi tiết về ý nghĩa và ảnh hưởng của cấu hình
- Pagination controls cho danh sách dài

#### Interactions
- Click vào "Chỉnh sửa" để mở form edit cấu hình (inline hoặc modal)
- Search tự động lọc danh sách khi nhập từ khóa
- Tab/category selection để lọc theo danh mục
- Badge hiển thị số lượng cấu hình trong mỗi danh mục
- Cơ chế xác nhận trước khi lưu thay đổi quan trọng

### 3. Form Chỉnh sửa Cấu hình

#### UI/UX
- Form edit với các controls phù hợp với từng loại dữ liệu:
  - STRING: Text input
  - INTEGER/FLOAT: Number input với validation
  - BOOLEAN: Toggle/Checkbox
  - SECRET: Password input (masked)
  - ENUM: Dropdown/Select
  - DATE: DatePicker
- Hiển thị giá trị hiện tại và mô tả cấu hình
- Nút "Lưu" và "Hủy"
- Validation feedback trực quan
- Success/error notifications sau khi lưu

#### Functionality
- Validation dựa trên kiểu dữ liệu của cấu hình
- Format data trước khi submit (số nguyên, boolean, v.v.)
- Kiểm tra độ hợp lệ của giá trị đặc biệt (cron expressions, URLs, etc.)
- Xử lý lỗi từ API và hiển thị feedback
- Optimistic UI update sau khi lưu thành công

### 4. Quản lý Cấu hình Đặc biệt

#### Cấu hình Margin
- UI chuyên biệt cho cấu hình ngưỡng margin (threshold.red, threshold.yellow)
- Visualization hiển thị mối quan hệ giữa các ngưỡng
- Validation đảm bảo ngưỡng red < ngưỡng yellow

#### Cấu hình Opportunity Follow-up
- UI chuyên biệt cho cấu hình thời gian follow-up cơ hội
- Visualization timeline cho ngưỡng cảnh báo
- Validation đảm bảo ngưỡng yellow < ngưỡng red

#### Cấu hình Hubspot Integration
- Form đặc biệt cho cấu hình API key (với mask/unmask)
- Chức năng test connection
- Cấu hình lịch trình đồng bộ với cron expression builder
- Thời gian đồng bộ lần cuối (readonly)

### 5. Chức năng Audit Trail/History (Optional)

#### UI/UX
- Tab hoặc section hiển thị lịch sử thay đổi cấu hình
- Danh sách các thay đổi với thông tin:
  - Thời gian thay đổi
  - Người thay đổi
  - Cấu hình đã thay đổi
  - Giá trị cũ và mới
- Filter lịch sử theo thời gian và loại cấu hình

#### Functionality
- Lấy và hiển thị lịch sử thay đổi từ API
- Pagination cho lịch sử dài
- Export lịch sử thay đổi

## Xử lý State và Performance

### State Management
- Global state cho danh sách cấu hình và danh mục
- Local state cho form edit và validation
- Cache dữ liệu cấu hình để giảm số lần gọi API
- Optimistic UI updates

### API Integration
- Kết nối với `API-ADM-011` (`GET /api/v1/admin/configs`) để lấy danh sách cấu hình
- Kết nối với `API-ADM-012` (`PUT /api/v1/admin/configs/{configKey}`) để cập nhật cấu hình
- Error handling và retry mechanism khi cần
- Ghi log thay đổi

### Performance Considerations
- Lazy loading các components không cần thiết ngay lập tức
- Memoization cho các calculations phức tạp
- Debounce cho search functionality
- Caching và invalidation strategy phù hợp
- Virtualized table nếu số lượng cấu hình lớn

## Phân quyền truy cập
- **Admin**: Đầy đủ quyền xem và chỉnh sửa tất cả cấu hình hệ thống
- **General Manager (課長)**: Chỉ được xem một số cấu hình cụ thể, không được chỉnh sửa
- **Team Leader (部長)**: Không có quyền truy cập trang này
- **Sales/Staff**: Không có quyền truy cập trang này
- **Kế toán**: Không có quyền truy cập trang này

## Các Task liên quan
- FE-ADM-001: User Management Page (trang quản lý người dùng)
- FE-ADM-002: Role & Permission Management Page (trang quản lý vai trò và phân quyền)
- FE-ADM-004: System Logs Page (trang xem log hệ thống)
- FE-MGN-001: Margin List Page (sử dụng ngưỡng margin từ cấu hình)
- FE-OPP-001: Opportunity List Page (sử dụng ngưỡng follow-up từ cấu hình)

## Tiêu chí chấp nhận
1. UI/UX:
   - Hiển thị đúng danh sách cấu hình theo danh mục
   - UI phù hợp với từng loại dữ liệu (STRING, INTEGER, BOOLEAN, SECRET, etc.)
   - Responsive design từ desktop đến tablet
   - Form validation hoạt động chính xác

2. Search & Filtering:
   - Tìm kiếm cấu hình theo tên hoặc mô tả hoạt động chính xác
   - Lọc theo danh mục hoạt động đúng
   - Hiển thị kết quả trong < 1 giây

3. Cập nhật Cấu hình:
   - Cập nhật thành công các cấu hình có thể chỉnh sửa
   - Hiển thị thông báo thành công/lỗi rõ ràng
   - Validation đúng theo kiểu dữ liệu
   - Không cho phép chỉnh sửa cấu hình có isEditable=false

4. Cấu hình Đặc biệt:
   - Cấu hình margin thresholds với validation logic đúng
   - Cấu hình follow-up thresholds hoạt động chính xác
   - Cấu hình Hubspot với test connection (nếu implement)

5. Phân quyền:
   - Chỉ Admin mới có thể truy cập và chỉnh sửa
   - General Manager chỉ xem được các cấu hình được phép
   - Các vai trò khác không thể truy cập

6. Performance:
   - Tải danh sách cấu hình < 2 giây
   - Cập nhật cấu hình < 1 giây
   - Search/filter phản hồi nhanh (< 500ms)

## Resources
- [Danh sách API - Admin Module](../../../BD/API/api_list.md#7-quản-trị-hệ-thống) 
- [Screen Design Diagram: MH-ADM-03](../../../BD/ScreenDesign/diagram/MH-ADM-03.puml) 