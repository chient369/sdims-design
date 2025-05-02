# Task Detail: FE-ADM-004

## Thông tin chung
- **ID**: FE-ADM-004
- **Tên task**: Xây dựng System Logs Page (MH-ADM-04)
- **Độ ưu tiên**: Thấp
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo mô tả)
  - API: [API-ADM-013](../../../DD/API/API-ADM-013.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-004: Xây dựng Table components
  - FE-ADM-001: Xây dựng User Management Page (MH-ADM-01)
  - FE-ADM-003: Xây dựng System Configuration Page (MH-ADM-03)

## Mô tả
Xây dựng trang xem log hệ thống (System Logs) cho phép Admin theo dõi, tìm kiếm và lọc các sự kiện và lỗi đã được ghi lại trong hệ thống. Trang này cung cấp khả năng quan sát các hoạt động quan trọng, phát hiện lỗi, và kiểm tra các thay đổi cấu hình. Log được hiển thị với nhiều mức độ chi tiết, có thể được lọc theo thời gian, mức độ nghiêm trọng, thành phần hệ thống, và người dùng liên quan. Trang này chủ yếu dành cho Admin và người quản lý cao cấp để phục vụ mục đích vận hành và gỡ lỗi hệ thống.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Header section với tiêu đề "Log Hệ thống" và các controls chính
- Advanced filter panel có thể expand/collapse
- Main content area hiển thị bảng log với pagination
- Log detail panel/modal để xem chi tiết khi click vào một dòng log
- Responsive design hỗ trợ từ desktop đến tablet

### 2. Filter Controls

#### UI/UX
- Panel lọc nổi bật với các options:
  - DateRangePicker cho khoảng thời gian (startDate và endDate)
  - MultiSelect/Dropdown cho mức độ log (INFO, WARNING, ERROR, DEBUG)
  - MultiSelect/Dropdown cho thành phần hệ thống (AUTH, CONFIG, HRM, OPPORTUNITY, etc.)
  - Autocomplete/Dropdown cho người dùng liên quan (nếu có)
  - Search box cho từ khóa trong message hoặc details
- Nút "Áp dụng Bộ lọc" và "Xóa Bộ lọc"
- Filter chips hiển thị các bộ lọc đang active
- Collapsible panel để tối ưu không gian
- Lưu và tải preset filters (optional)

#### Functionality
- Apply filters và refresh danh sách log
- Clear all filters về mặc định
- Validation cho date range và các filter parameters
- Auto-submit sau khi thay đổi filter (debounced)
- URL parameters để bookmark/share filtered views

### 3. Log Table Display

#### UI/UX
- DataTable hiển thị danh sách log với các columns:
  - Thời gian (timestamp) với format dễ đọc
  - Mức độ (level) với visual indicator (icon + color)
  - Thành phần (component)
  - Message ngắn gọn
  - Người dùng liên quan (username)
  - Các thông tin bổ sung tùy thuộc vào không gian hiển thị
- Sorting theo thời gian (mặc định: newest first)
  - Có thể sort theo các cột khác nếu cần
- Mỗi dòng có thể click để xem chi tiết
- Color coding theo mức độ nghiêm trọng (ERROR: đỏ, WARNING: vàng, INFO: xanh, DEBUG: xám)
- Pagination controls ở cuối bảng
- Empty state và loading state
- Row styling cho các log quan trọng

#### Functionality
- Hiển thị danh sách log từ API với pagination
- Sorting inline
- Highlight dòng đang chọn
- Truncate message dài với tooltip để xem thêm
- Tự động refresh theo interval có thể cấu hình (optional)
- Export danh sách log hiện tại (CSV/Excel)

### 4. Log Detail View

#### UI/UX
- Modal/Drawer hiển thị khi click vào một dòng log
- Header với thông tin cơ bản (timestamp, level, component)
- Các sections rõ ràng cho:
  - Basic Info: timestamp, level, component, user
  - Message: Nội dung log chính
  - Details: Chi tiết lỗi hoặc thông tin bổ sung
  - Context: requestId, IP address, browser, etc.
- Format code/JSON/stacktrace với syntax highlighting
- Nút đóng và các actions khác (copy, export single log)
- Theme phù hợp với severity của log

#### Functionality
- Parse và format dữ liệu log theo cấu trúc
- Hiển thị JSON/XML được format đẹp
- Cung cấp context links (e.g. link đến user profile của người liên quan)
- Copy to clipboard functionality
- Chuyển qua lại giữa các log khi đang ở detail view

### 5. Export & Archive Features (Optional)

#### UI/UX
- Export controls cho phép tải xuống logs theo định dạng:
  - CSV
  - Excel
  - JSON
- Options cho phạm vi export:
  - Chỉ trang hiện tại
  - Tất cả kết quả phù hợp với bộ lọc
- Progress indicator cho quá trình export
- Archive controls (nếu được implement)

#### Functionality
- Generate và download file export
- Format dữ liệu để dễ đọc trong file export
- Xử lý large dataset exports
- Tương tác với API archive nếu có

## Xử lý State và Performance

### State Management
- Global state cho filter configuration
- Local state cho log table và pagination
- Caching filter results để cải thiện UX
- Preserve filter và pagination state khi navigate

### API Integration
- Kết nối với `API-ADM-013` (`GET /api/v1/admin/system-logs`) để lấy log data
- Truyền filter params thông qua query parameters
- Error handling và retry mechanism
- Pagination được implement đúng theo API spec
- Cancel/abort requests khi filter thay đổi

### Performance Considerations
- Virtualized table để xử lý hiệu quả danh sách log lớn
- Lazy loading cho log details
- Debounced search để tránh gọi API quá nhiều
- Memoization để tránh re-renders không cần thiết
- Rate limiting cho auto-refresh (nếu có)
- Incremental loading cho large exports
- Tối ưu hóa re-rendering của filter components

## Phân quyền truy cập
- **Admin**: Đầy đủ quyền xem tất cả log hệ thống
- **General Manager (課長)**: Quyền xem giới hạn (không bao gồm các log nhạy cảm và chỉ xem được log liên quan đến bộ phận mình)
- **Team Leader (部長)**: Không có quyền truy cập trang này
- **Sales/Staff**: Không có quyền truy cập trang này
- **Kế toán**: Không có quyền truy cập trang này

## Các Task liên quan
- FE-ADM-001: User Management Page (trang quản lý người dùng)
- FE-ADM-003: System Configuration Page (trang cấu hình hệ thống)
- FE-OPP-004: Hubspot Sync controls (liên quan đến các log đồng bộ)

## Tiêu chí chấp nhận
1. Filter Controls:
   - Lọc chính xác theo thời gian, mức độ, thành phần, người dùng
   - Filter chips hiển thị rõ ràng các bộ lọc đang áp dụng
   - Clear filters hoạt động đúng
   - URL parameters được generate và parse chính xác

2. Log Table:
   - Hiển thị đúng danh sách log với thông tin cần thiết
   - Pagination hoạt động chính xác
   - Sorting theo thời gian và các tiêu chí khác
   - Color coding đúng theo mức độ nghiêm trọng
   - Truncate message dài một cách hợp lý

3. Log Detail View:
   - Hiển thị đầy đủ thông tin chi tiết của log
   - Format đúng cho JSON, stacktrace
   - Các actions (copy, export) hoạt động đúng

4. Export Functionality:
   - Export CSV/Excel thành công với dữ liệu đúng
   - Progress indicator hiển thị đúng
   - File export có format hợp lý

5. Phân quyền:
   - Admin xem được tất cả log
   - General Manager chỉ xem được log trong phạm vi quyền
   - Các vai trò khác không thể truy cập

6. Performance:
   - Tải danh sách log ban đầu trong < 3 giây
   - Apply filter phản hồi trong < 2 giây
   - Detail view load nhanh (< 1 giây)
   - Không có UI freezes khi navigate hoặc filter
   - Xử lý được danh sách log lớn (1000+ entries) mà không bị lag

## Resources
- [Danh sách API - Admin Module](../../../BD/API/api_list.md#7-quản-trị-hệ-thống) 
- [Screen Design Diagram: MH-ADM-04](../../../BD/ScreenDesign/diagram/MH-ADM-04.puml) 