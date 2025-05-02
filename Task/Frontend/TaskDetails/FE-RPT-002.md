# Task Detail: FE-RPT-002

## Thông tin chung
- **ID**: FE-RPT-002
- **Tên task**: Xây dựng Report Viewer (MH-RPT-02)
- **Độ ưu tiên**: Trung bình
- **Estimate**: 5 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo tài liệu mô tả)
  - API: [API-RPT-002](../../../DD/API/API-RPT-002.md), [API-RPT-003](../../../DD/API/API-RPT-003.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-004: Xây dựng Table components
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-RPT-001: Xây dựng Report List Page (MH-RPT-01)

## Mô tả
Xây dựng Report Viewer - một generic framework để hiển thị và tương tác với các loại báo cáo khác nhau trong hệ thống. Report Viewer sẽ cung cấp giao diện thống nhất cho phép người dùng xem, lọc, xuất và tương tác với dữ liệu báo cáo. Framework này cần có khả năng render nhiều loại báo cáo khác nhau (tùy thuộc vào cấu hình từ backend) bao gồm các bảng dữ liệu, biểu đồ, KPIs và các visualizations khác. Người dùng có thể áp dụng các bộ lọc, thay đổi kỳ báo cáo, xuất dữ liệu ra các định dạng khác nhau và lưu cấu hình báo cáo ưa thích.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Header section hiển thị tên báo cáo, mô tả, và action buttons (Export, Share, Favorite)
- Filter section cho phép người dùng áp dụng các bộ lọc cụ thể cho báo cáo
- Tab navigation (nếu báo cáo có nhiều views/sections)
- Main content area hiển thị dữ liệu báo cáo (tables, charts, KPIs)
- Responsive design hỗ trợ từ desktop đến tablet
- Toolbar với các controls cho xuất dữ liệu, làm mới, v.v.

### 2. Report Header & Controls

#### UI/UX
- Title và description của báo cáo
- Breadcrumb navigation (Reports > [Category] > [Report Name])
- Date/period selector (nếu báo cáo có timeframe)
- Refresh button để làm mới dữ liệu
- Export dropdown (PDF, Excel, CSV)
- Favorite/Bookmark button
- Share/Link button (nếu có chức năng share)
- Print button
- Tab navigation nếu báo cáo có nhiều sections
- Responsive adjustments cho các screen sizes khác nhau

#### Functionality
- Load metadata báo cáo từ API
- Lưu báo cáo vào favorites
- Generate và tải xuống báo cáo ở các định dạng khác nhau (PDF, Excel, CSV)
- Refresh dữ liệu báo cáo
- Copy link để share (với parameters)
- Print preview và printing

### 3. Filter Controls

#### UI/UX
- Filter panel có thể expand/collapse
- Các loại filter controls khác nhau:
  - Dropdowns (single/multi select)
  - Date/DateTime pickers
  - Range sliders
  - Checkboxes/Radio buttons
  - Search inputs
- "Apply Filters" và "Reset Filters" buttons
- Visual indicators cho active filters
- Filter chips hiển thị filters đang áp dụng
- Responsive design cho filter panel

#### Functionality
- Load filter options từ API
- Apply filters và refresh dữ liệu báo cáo
- Reset filters về trạng thái mặc định
- Save filter preferences cho user
- Generate URL với filter parameters
- Validation cho filter values

### 4. Report Content Rendering

#### UI/UX
- Khung chứa nội dung báo cáo với loading indicator
- Các loại visualizations:
  - Data tables với sorting, filtering, pagination
  - Charts (Bar, Line, Pie, Area, etc.)
  - KPI cards và metrics
  - Combination views (tables + charts)
- Empty states cho mỗi loại visualization
- Error states khi không thể load dữ liệu
- Responsive layout adjustments

#### Functionality
- Dynamic rendering dựa trên cấu trúc báo cáo từ API
- Tương tác với các visualization (drill-down, tooltips, etc.)
- Sorting, filtering trong data tables
- Cross-filtering giữa các components
- Resize và responsive adjustments cho các visualization
- Refresh partial data khi cần

### 5. Export & Sharing

#### UI/UX
- Export dropdown với các options:
  - PDF (toàn bộ báo cáo)
  - Excel/CSV (raw data)
  - Image (cho charts)
- Share dialog với các options:
  - Copy link
  - Email report
  - Schedule report (nếu có chức năng)
- Progress indicator khi đang generate/export
- Success/error notifications

#### Functionality
- Generate báo cáo ở các định dạng khác nhau
- Xử lý báo cáo lớn (pagination, streaming)
- Create shareable links với filter state
- Email báo cáo (nếu có API support)
- Schedule periodic reports (nếu có)

## Xử lý State và Performance

### State Management
- Quản lý complex filter state
- Caching báo cáo đã load để cải thiện UX
- Report configuration state
- User preferences (saved filters, display options)
- Loading và error states

### API Integration
- Kết nối với `API-RPT-002` (`GET /api/v1/reports/{reportId}`) để lấy metadata và cấu trúc báo cáo
- Kết nối với `API-RPT-003` (`GET /api/v1/reports/{reportId}/data`) để lấy dữ liệu báo cáo với filters
- APIs cho export và share functionality
- Error handling và retry mechanism
- Cancel/abort requests khi filters thay đổi nhanh

### Performance Considerations
- Lazy loading cho data lớn
- Virtualized tables cho large datasets
- Progressive rendering cho complex reports
- Caching và memoization cho calculations và renders
- Optimize chart re-rendering
- Chunked/streamed data loading cho very large reports
- Filter debouncing để tránh quá nhiều API calls

## Phân quyền truy cập
- **Admin**: Xem tất cả báo cáo với đầy đủ chức năng
- **General Manager (課長)**: Xem các báo cáo liên quan đến phạm vi quản lý
- **Team Leader (部長)**: Xem báo cáo liên quan đến team của họ (data được filter)
- **Staff**: Chỉ xem báo cáo được phân quyền, với data filters theo scope
- Tất cả người dùng: Có thể xuất báo cáo trong phạm vi quyền của mình

## Các Task liên quan
- FE-RPT-001: Report List Page (danh sách báo cáo)
- FE-RPT-003: Custom Report Builder (tùy chỉnh báo cáo)
- FE-CORE-007: Chart/Graph components (sử dụng để hiển thị báo cáo)
- FE-CORE-004: Table components (sử dụng để hiển thị báo cáo dạng bảng)

## Tiêu chí chấp nhận
1. Report Header & Controls:
   - Hiển thị đúng metadata (tên, mô tả) của báo cáo
   - Favorite/bookmark hoạt động chính xác
   - Export PDF/Excel/CSV tạo files với dữ liệu đúng
   - Print functionality hoạt động đúng
   - Breadcrumb navigation hoạt động chính xác

2. Filter Controls:
   - Hiển thị đúng các filter options cho mỗi báo cáo
   - Apply filters làm mới dữ liệu báo cáo chính xác
   - Reset filters hoạt động đúng
   - URL parameters được generate và parse chính xác
   - Responsive design cho filter panel hoạt động tốt

3. Report Content Rendering:
   - Render chính xác các loại visualizations từ cấu trúc API
   - Data tables hiển thị với sorting, pagination
   - Charts hiển thị đúng dữ liệu với các tương tác
   - Drill-down functionality (nếu có) hoạt động đúng
   - Responsive design cho tất cả visualizations

4. Export & Sharing:
   - Export PDF tạo file với layout và styling đúng
   - Export Excel/CSV chứa dữ liệu đầy đủ và định dạng đúng
   - Share link tạo URL với state được bảo toàn
   - Email functionality (nếu có) gửi báo cáo chính xác

5. Performance:
   - Tải báo cáo lần đầu < 3 giây
   - Apply filter cập nhật trong < 2 giây
   - Export báo cáo lớn (500+ rows) < 10 giây
   - Không có UI freezes khi tương tác với báo cáo phức tạp

6. Phân quyền:
   - Chỉ hiển thị dữ liệu mà người dùng có quyền xem
   - Scope của dữ liệu đúng với vai trò người dùng
   - Export chỉ bao gồm dữ liệu người dùng có quyền

## Resources
- [Danh sách API - Reports & Analytics](../../../BD/API/api_list.md#6-báo-cáo--analytics)
- [Tài liệu phân quyền báo cáo](../../../BD/FunctionDesign/ReportPermissions.md)
- [API Schema cho Report Structure](../../../DD/API/Report_Schema.md) 