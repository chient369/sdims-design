# Task Detail: FE-RPT-001

## Thông tin chung
- **ID**: FE-RPT-001
- **Tên task**: Xây dựng Report List Page (MH-RPT-01)
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: N/A (Thiết kế theo tài liệu mô tả)
  - API: [API-RPT-001](../../../DD/API/API-RPT-001.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-004: Xây dựng Table components
  - FE-CORE-005: Xây dựng Modal và Dialog components

## Mô tả
Xây dựng trang danh sách báo cáo (Report List Page) cho phép người dùng xem, tìm kiếm và truy cập các báo cáo có sẵn trong hệ thống. Danh sách báo cáo được tổ chức theo nhóm (modules) và hiển thị các báo cáo mà người dùng có quyền truy cập dựa trên vai trò của họ. Người dùng có thể đánh dấu báo cáo yêu thích, xem báo cáo đã truy cập gần đây, và chuyển đến xem chi tiết báo cáo. Trang này là điểm khởi đầu cho module Reports & Analytics, cung cấp giao diện dễ sử dụng để người dùng tìm và truy cập các báo cáo mà họ cần.

## Yêu cầu kỹ thuật

### 1. Layout & Structure
- Sử dụng MainLayout chung của hệ thống
- Header section với tiêu đề và search bar
- Navigation tabs hoặc filter controls để chuyển đổi giữa các views:
  - All Reports (Tất cả báo cáo)
  - Favorites (Báo cáo yêu thích)
  - Recent (Báo cáo xem gần đây)
- Section hiển thị các nhóm báo cáo được tổ chức theo module
- Card-based layout hoặc table view để hiển thị báo cáo
- Pagination/infinite scroll cho danh sách dài
- Responsive design hỗ trợ từ desktop đến tablet

### 2. UI/UX cho Danh sách Báo cáo

#### UI Components
- Search bar với placeholder text rõ ràng
- Filter controls (dropdowns, toggle buttons)
- Report cards hoặc table rows hiển thị:
  - Tên báo cáo
  - Mô tả ngắn
  - Module liên quan (icon hoặc badge)
  - Last accessed (nếu đã xem trước đó)
  - Star/favorite icon
- Empty states cho mỗi view (All, Favorites, Recent)
- Loading state và error state
- Visual hierarchy rõ ràng giữa các nhóm báo cáo

#### Interactions
- Click vào report card/row để mở báo cáo
- Click vào favorite icon để thêm/xóa khỏi danh sách yêu thích
- Hover states cung cấp thêm thông tin/actions
- Search suggestions khi nhập từ khóa
- Lazy loading khi scroll đến cuối danh sách

### 3. Grouped Reports Section

#### UI/UX
- Section headers cho mỗi nhóm (module) báo cáo
- Collapsible sections để tối ưu không gian
- Visual indicators cho các báo cáo phổ biến/quan trọng
- Consistent styling với các section khác
- Count badges hiển thị số lượng báo cáo trong mỗi nhóm

#### Functionality
- Hiển thị báo cáo được nhóm theo module (HRM, Finance, Sales, etc.)
- Expand/collapse các nhóm
- Filter báo cáo trong mỗi nhóm
- Permission-based visibility (chỉ hiển thị báo cáo người dùng có quyền xem)

### 4. Search & Filter

#### UI/UX
- Search bar nổi bật ở vị trí dễ thấy
- Filter dropdowns cho:
  - Module (HRM, Finance, Sales, etc.)
  - Loại báo cáo (Analytical, Operational, etc.)
  - Thời gian tạo/cập nhật
- Clear/reset filters button
- Search results highlight từ khóa tìm kiếm
- "No results" state với gợi ý

#### Functionality
- Full-text search trên tên và mô tả báo cáo
- Filtering theo nhiều tiêu chí
- Lưu trạng thái search/filter trong session
- Auto-suggest khi search (nếu được hỗ trợ bởi API)
- Debounced search để tránh gọi API nhiều lần

### 5. Favorites & Recents Management

#### UI/UX
- Favorites tab hiển thị báo cáo đã đánh dấu yêu thích
- Recent tab hiển thị báo cáo đã xem gần đây, sắp xếp theo thời gian
- Visual indicator cho báo cáo đang là favorite
- Empty state cho mỗi tab

#### Functionality
- Add/remove favorites thông qua API
- Tracking recently viewed reports
- Lưu trữ favorites/recents trong user preferences
- Đồng bộ trạng thái favorite giữa các tabs

## Xử lý State và Performance

### State Management
- Sử dụng global state để quản lý danh sách báo cáo, favorites, recents
- Local state cho UI states (search query, active filters, active tab)
- Caching danh sách báo cáo để giảm số lần gọi API
- Optimistic UI updates cho favorites

### API Integration
- Kết nối với `API-RPT-001` (`GET /api/v1/reports`) để lấy danh sách báo cáo
- Kết nối với API quản lý favorites (`POST/DELETE /api/v1/user/favorites/reports`)
- Tracking recent reports (`POST /api/v1/user/recent-reports`)
- Error handling và retry mechanism
- Loading states và pagination

### Performance Considerations
- Lazy loading cho danh sách dài
- Virtualized list nếu số lượng báo cáo lớn
- Caching dữ liệu để giảm số lần gọi API
- Debounce cho search
- Optimized assets (icons, images)

## Phân quyền truy cập
- **Admin**: Xem tất cả báo cáo trong hệ thống
- **General Manager (課長)**: Xem tất cả báo cáo liên quan đến các module họ quản lý
- **Team Leader (部長)**: Xem báo cáo liên quan đến team của họ
- **Staff**: Xem báo cáo liên quan đến công việc của họ và báo cáo công khai
- **Kế toán**: Xem báo cáo liên quan đến tài chính, hợp đồng, thanh toán

## Các Task liên quan
- FE-RPT-002: Report Viewer (xem chi tiết báo cáo)
- FE-RPT-003: Custom Report Builder (tạo báo cáo tùy chỉnh)
- FE-CORE-007: Chart/Graph components (sử dụng trong báo cáo)
- FE-CORE-009: Authorization components (phân quyền)

## Tiêu chí chấp nhận
1. UI/Layout:
   - Hiển thị danh sách báo cáo theo nhóm module
   - Navigation tabs hoạt động đúng (All, Favorites, Recent)
   - Responsive design từ desktop đến tablet
   - Empty/loading/error states hiển thị phù hợp

2. Search & Filter:
   - Search hoạt động chính xác với kết quả liên quan
   - Filters áp dụng đúng và có thể kết hợp
   - Debounced search để tránh gọi API quá nhiều
   - Clear filters hoạt động đúng

3. Favorites:
   - Add/remove từ favorites hoạt động chính xác
   - Favorites tab hiển thị đúng báo cáo đã đánh dấu
   - Trạng thái favorite được lưu và đồng bộ giữa các sessions

4. Recents:
   - Recent tab hiển thị chính xác báo cáo đã xem gần đây
   - Sắp xếp theo thời gian xem gần nhất

5. Permission:
   - Chỉ hiển thị báo cáo người dùng có quyền xem
   - Admin thấy tất cả báo cáo
   - Người dùng khác chỉ thấy báo cáo họ có quyền

6. Performance:
   - Tải danh sách ban đầu < 2 giây
   - Search/filter phản hồi < 1 giây
   - Lazy loading/pagination hoạt động mượt mà

7. Integration:
   - Chuyển đến Report Viewer khi click vào báo cáo
   - Thêm vào Recent khi truy cập báo cáo

## Resources
- [Danh sách API - Reports & Analytics](../../../BD/API/api_list.md#6-báo-cáo--analytics)
- [Tài liệu phân quyền báo cáo](../../../BD/FunctionDesign/ReportPermissions.md) 