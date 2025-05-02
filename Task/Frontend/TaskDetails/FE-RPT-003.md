# Task Detail: FE-RPT-003

## Thông tin chung
- **ID**: FE-RPT-003
- **Tên task**: Xây dựng Custom Report Builder
- **Độ ưu tiên**: Thấp
- **Estimate**: 7 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo tài liệu mô tả)
  - API: [API-RPT-004](../../../DD/API/API-RPT-004.md), [API-RPT-005](../../../DD/API/API-RPT-005.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-004: Xây dựng Table components
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-RPT-001: Xây dựng Report List Page (MH-RPT-01)
  - FE-RPT-002: Xây dựng Report Viewer (MH-RPT-02)

## Mô tả
Xây dựng Custom Report Builder - một giao diện nâng cao cho phép người dùng được ủy quyền (Admin, 課長) tạo và tùy chỉnh báo cáo riêng theo nhu cầu cụ thể. Tool này cho phép người dùng chọn nguồn dữ liệu, thiết kế cấu trúc báo cáo, chọn các trường muốn hiển thị, áp dụng các điều kiện lọc, chọn visualizations và tùy chỉnh giao diện. Các báo cáo tùy chỉnh có thể được lưu, chia sẻ và quản lý như các báo cáo có sẵn trong hệ thống. Đây là công cụ mạnh mẽ dành cho người dùng nâng cao, giúp họ khai thác dữ liệu một cách linh hoạt mà không cần đến developer.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Wizard/step-based UI flow hoặc multi-panel workspace
- Sidebar cho data sources, fields và components
- Main workspace cho report design/preview
- Properties panel cho configuration
- Toolbar với controls để lưu, preview, share
- Responsive design (ưu tiên cho desktop do tính phức tạp của UI)

### 2. Data Source Selection

#### UI/UX
- List hoặc dropdown các data sources được phép truy cập
- Search/filter cho data sources
- Data schema preview (tables, fields)
- Visual indicators cho relationships giữa các tables
- Info tooltips giải thích ý nghĩa của fields
- "Continue" button khi đã chọn xong data source

#### Functionality
- Load danh sách data sources từ API
- Filter data sources theo module hoặc category
- Preview schema của data source đã chọn
- Select primary entity và related entities
- Validation khi chọn data source không compatible
- Save selection vào report configuration

### 3. Field Selection & Data Manipulation

#### UI/UX
- Available fields list (từ data source đã chọn)
- Drag-and-drop interface để thêm fields vào báo cáo
- Grouping controls (group by, aggregations)
- Sorting controls
- Calculated fields/expressions editor
- Field properties panel (format, alias, etc.)
- "Preview Data" button để xem kết quả query

#### Functionality
- Dynamic field list dựa trên data source
- Drag-and-drop fields vào report layout
- Field property editing (rename, format, etc.)
- Define calculated fields với expression builder
- Apply grouping, aggregation (sum, avg, count, etc.)
- Apply sorting (multi-level)
- Filter criteria editor với conditional logic
- Data preview with limits

### 4. Visualization Design

#### UI/UX
- Toolbox với các visualization types (table, chart, pivot, etc.)
- Properties panel cho mỗi visualization
- Drag-and-drop interface để arrange layout
- Resizable containers cho các visualization
- Chart type selector với preview
- Color palette và theme controls
- Title/legend/axis configuration
- "Preview" để xem báo cáo đầy đủ

#### Functionality
- Add/remove/resize visualizations
- Configure visualization properties
- Map data fields to visualization attributes (x-axis, y-axis, series, etc.)
- Apply formatting và styling
- Set conditional formatting
- Define drill-down relationships
- Support for multiple visualizations in one report
- Real-time preview with sample data

### 5. Filter & Parameter Definition

#### UI/UX
- Interface để định nghĩa báo cáo filters
- Parameter configuration panel
- Predefined vs. runtime filter options
- Default value settings
- Dependent filters configuration
- Date range presets
- Parameter preview

#### Functionality
- Define report parameters và filters
- Set parameter types (string, number, date, enum, etc.)
- Configure default values and constraints
- Define dependencies giữa các filters
- Apply filters to query
- Test filters với preview
- Save filter configuration

### 6. Save, Share & Management

#### UI/UX
- Save dialog với fields:
  - Report name
  - Description
  - Category/folder
  - Visibility/sharing options
  - Tags
- Success/error notifications
- "Run Report" button để chạy báo cáo đầy đủ
- Management controls (duplicate, edit, delete)

#### Functionality
- Save report configuration to server
- Validate required fields
- Error handling khi lưu
- Permissions assignment cho báo cáo
- Generate shareable link
- Redirect to Report Viewer sau khi lưu
- Version control (optional)

## Xử lý State và Performance

### State Management
- Complex state management cho report builder workflow
- Undo/redo functionality
- Auto-save draft reports
- Wizard state preservation giữa các bước
- Configuration validation

### API Integration
- Kết nối với `API-RPT-004` (`GET /api/v1/report-builder/data-sources`) để lấy data sources
- Kết nối với `API-RPT-005` (`POST /api/v1/report-builder/preview`) để preview báo cáo
- APIs cho lưu và quản lý báo cáo
- Validation APIs
- Error handling và retry mechanism

### Performance Considerations
- Lazy loading cho components phức tạp
- Debounced preview updates để tránh quá nhiều API calls
- Memory management cho large report configs
- Optimized drag-and-drop operations
- Efficient rendering cho visualization previews
- Caching metadata và schema
- Progressive loading cho data previews

## Phân quyền truy cập
- **Admin**: Đầy đủ quyền tạo, chỉnh sửa, xóa báo cáo tùy chỉnh, truy cập tất cả data sources
- **General Manager (課長)**: Có thể tạo báo cáo tùy chỉnh trong phạm vi dữ liệu được phép truy cập
- **Team Leader (部長)**: Có thể tạo báo cáo đơn giản với data sources giới hạn (tùy theo cấu hình hệ thống)
- **Staff**: Không có quyền tạo báo cáo tùy chỉnh, chỉ có thể xem báo cáo được chia sẻ
- **Kế toán**: Quyền tạo báo cáo tùy chỉnh trong phạm vi tài chính/kế toán

## Các Task liên quan
- FE-RPT-001: Report List Page (quản lý báo cáo đã tạo)
- FE-RPT-002: Report Viewer (xem báo cáo đã tạo)
- FE-CORE-007: Chart/Graph components (sử dụng trong visualizations)
- FE-CORE-004: Table components (sử dụng trong data preview và visualizations)

## Tiêu chí chấp nhận
1. Data Source Selection:
   - Hiển thị đúng danh sách data sources người dùng có quyền truy cập
   - Preview schema của data source hoạt động chính xác
   - Chọn và validate data source thành công

2. Field Selection & Data Manipulation:
   - Hiển thị chính xác danh sách fields từ data source
   - Drag-and-drop fields vào báo cáo hoạt động mượt mà
   - Calculated fields expression builder hoạt động đúng
   - Preview data hiển thị dữ liệu chính xác theo query configuration

3. Visualization Design:
   - Thêm/xóa/resize visualizations hoạt động đúng
   - Property configuration cho mỗi visualization type hoạt động chính xác
   - Chart configuration (axes, legends, colors) áp dụng đúng
   - Real-time preview cập nhật chính xác theo thay đổi

4. Filter & Parameter Definition:
   - Định nghĩa filters thành công
   - Parameter type configuration hoạt động đúng
   - Default values và validation rules áp dụng chính xác
   - Filter dependencies hoạt động như mong đợi

5. Save, Share & Management:
   - Lưu báo cáo thành công với metadata đầy đủ
   - Permissions được áp dụng đúng
   - Báo cáo đã lưu hiển thị trong Report List
   - Chạy báo cáo đã lưu trong Report Viewer hoạt động đúng

6. Phân quyền:
   - Chỉ những người dùng có quyền mới truy cập được Report Builder
   - Data sources và fields được giới hạn theo quyền của người dùng
   - Báo cáo được share với đúng permissions

7. Performance:
   - UI phản hồi nhanh khi thao tác (< 500ms)
   - Preview data load trong < 3 giây
   - Large reports (nhiều visualizations) vẫn hoạt động mượt mà
   - Không có memory leaks khi sử dụng lâu

## Resources
- [Danh sách API - Reports & Analytics](../../../BD/API/api_list.md#6-báo-cáo--analytics)
- [Tài liệu phân quyền báo cáo](../../../BD/FunctionDesign/ReportPermissions.md)
- [API Schema cho Report Builder](../../../DD/API/ReportBuilder_Schema.md) 