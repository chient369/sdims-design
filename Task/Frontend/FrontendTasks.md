# Frontend Task Breakdown

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2024-07-29 | Claude AI Assistant | Khởi tạo danh sách task frontend dựa trên tài liệu thiết kế | -           | Draft     |

---

## 1. Mục tiêu  
Phân chia chi tiết các task phát triển frontend cho Hệ thống Quản lý Nội bộ (SDIMS), dựa trên tài liệu thiết kế màn hình và yêu cầu chức năng.

**Ghi chú**: Một số task đã có tài liệu mô tả chi tiết trong thư mục [TaskDetails](TaskDetails/README.md). Các task này được đánh dấu với liên kết "Xem chi tiết".

## Frontend Tasks

### Infrastructure & Setup

- [ ] Task FE-INFRA-001: Khởi tạo project React với Vite hoặc Create React App (High Priority) [Xem chi tiết](TaskDetails/FE-INFRA-001.md)
  - Thiết lập cấu trúc thư mục frontend theo best practices
  - Cấu hình linting và formatting (ESLint, Prettier)
  - Thiết lập các file cấu hình (.env, tsconfig)

- [ ] Task FE-INFRA-002: Cấu hình CI/CD pipeline cho frontend (Medium Priority) [Xem chi tiết](TaskDetails/FE-INFRA-002.md)
  - Thiết lập GitHub Actions/GitLab CI 
  - Tạo script deployment tự động cho môi trường staging và production
  - Cấu hình testing tự động trong pipeline

- [ ] Task FE-INFRA-003: Thiết lập router và cấu trúc điều hướng (High Priority) [Xem chi tiết](TaskDetails/FE-INFRA-003.md)
  - Cài đặt và cấu hình React Router
  - Xây dựng cấu trúc điều hướng theo luồng di chuyển đã thiết kế
  - Thiết lập route protection/guards dựa trên phân quyền

- [ ] Task FE-INFRA-004: Xây dựng hệ thống theme và design system (High Priority) [Xem chi tiết](TaskDetails/FE-INFRA-004.md)
  - Cài đặt Tailwind CSS và cấu hình theme
  - Thiết lập color palette, typography, spacing theo design
  - Xây dựng các component cơ bản (button, card, input, v.v.)

- [ ] Task FE-INFRA-005: Thiết lập state management (High Priority) [Xem chi tiết](TaskDetails/FE-INFRA-005.md)
  - Cài đặt và cấu hình Context API
  - Thiết lập store structure và organization
  - Xây dựng các hooks và utilities liên quan đến state management

- [ ] Task FE-INFRA-006: Thiết lập API client và interceptors (High Priority) [Xem chi tiết](TaskDetails/FE-INFRA-006.md)
  - Cài đặt Axios
  - Xây dựng hệ thống interceptors cho authentication và error handling
  - Tạo helper functions cho các loại API calls phổ biến

### Core & Shared Components

- [ ] Task FE-CORE-001: Xây dựng Layout components (High Priority) [Xem chi tiết](TaskDetails/FE-CORE-001.md)
  - MainLayout với sidebar, header, content area
  - AuthLayout cho các trang đăng nhập/đăng ký
  - ErrorBoundary component để bắt lỗi

- [ ] Task FE-CORE-002: Xây dựng Navigation components (High Priority) [Xem chi tiết](TaskDetails/FE-CORE-002.md)
  - Sidebar với danh sách menu có phân quyền
  - Header với user profile và notifications
  - Breadcrumbs component

- [ ] Task FE-CORE-003: Xây dựng Form components (High Priority) [Xem chi tiết](TaskDetails/FE-CORE-003.md)
  - Input, Select, Checkbox, Radio, DatePicker components
  - Form validation với React Hook Form hoặc Formik
  - Error message và form helpers

- [ ] Task FE-CORE-004: Xây dựng Table components (High Priority) [Xem chi tiết](TaskDetails/FE-CORE-004.md)
  - Reusable DataTable với sorting, pagination
  - Custom table cell renderers
  - Row actions và bulk actions

- [ ] Task FE-CORE-005: Xây dựng Modal và Dialog components (Medium Priority) [Xem chi tiết](TaskDetails/FE-CORE-005.md)
  - Confirmation dialogs
  - Custom modal system với animation
  - Drawer component cho side panels

- [ ] Task FE-CORE-006: Xây dựng Notification components (Medium Priority) [Xem chi tiết](TaskDetails/FE-CORE-006.md)
  - Toast notifications
  - Alert components (success, error, warning, info)
  - Notification center UI (nếu cần)

- [ ] Task FE-CORE-007: Xây dựng Chart/Graph components (Medium Priority) [Xem chi tiết](TaskDetails/FE-CORE-007.md)
  - Các biểu đồ cơ bản cho dashboard (line, bar, pie)
  - KPI stat cards và metrics
  - Filter controls cho biểu đồ

- [ ] Task FE-CORE-008: Xây dựng File Upload/Preview components (Medium Priority)[Xem chi tiết](TaskDetails/FE-CORE-008.md)
  - File upload với drag-drop support
  - File preview cho các loại file phổ biến
  - Progress indicator và error handling

- [ ] Task FE-CORE-009: Xây dựng Authorization components (High Priority)[Xem chi tiết](TaskDetails/FE-CORE-009.md)
  - PrivateRoute component cho route protection
  - AuthGuard để kiểm tra quyền truy cập
  - Permission-based rendering

### Authentication & User Management Pages

- [ ] Task FE-AUTH-001: Xây dựng Login Page (MH-AUTH-01) (High Priority) [Xem chi tiết](TaskDetails/FE-AUTH-001.md)
  - Form đăng nhập với validation
  - Xử lý lỗi đăng nhập
  - Lưu token và chuyển hướng sau khi đăng nhập

- [ ] Task FE-ADM-001: Xây dựng User Management Page (MH-ADM-01) (Medium Priority) [Xem chi tiết](TaskDetails/FE-ADM-001.md)
  - Danh sách người dùng dạng bảng với phân trang, tìm kiếm
  - Modal thêm/sửa người dùng
  - Xử lý xóa/khóa tài khoản với confirmation

- [ ] Task FE-ADM-002: Xây dựng Role & Permission Management Page (MH-ADM-02) (Medium Priority) [Xem chi tiết](TaskDetails/FE-ADM-002.md)
  - Danh sách vai trò và quyền hạn
  - Form thêm/sửa vai trò
  - UI gán quyền cho vai trò (matrix hoặc checklist)

### Dashboard Module

- [ ] Task FE-DSH-001: Xây dựng Dashboard Page (MH-DSH-01) (High Priority) [Xem chi tiết](TaskDetails/FE-DSH-001.md)
  - Layout với grid của các widget
  - Card hiển thị metrics và KPIs
  - Filter controls (theo thời gian, team)

- [ ] Task FE-DSH-002: Xây dựng HR metrics widgets (High Priority) [Xem chi tiết](TaskDetails/FE-DSH-002.md)
  - Hiển thị số liệu nhân sự (bench, distributed, etc.)
  - Biểu đồ phân bổ nhân sự theo dự án
  - Quick access tới các nhân sự cần attention

- [ ] Task FE-DSH-003: Xây dựng Revenue/Margin widgets (High Priority) [Xem chi tiết](TaskDetails/FE-DSH-003.md)
  - Biểu đồ doanh thu theo thời gian
  - Margin overview với color indicators
  - So sánh KPI vs Actual

- [ ] Task FE-DSH-004: Xây dựng Opportunity widgets (Medium Priority) [Xem chi tiết](TaskDetails/FE-DSH-004.md)
  - Recent opportunities list
  - Opportunity funnel chart
  - Follow-up status indicators

### Human Resource Module (HRM)

- [ ] Task FE-HRM-001: Xây dựng Employee List Page (MH-HRM-01) (High Priority) [Xem chi tiết](TaskDetails/FE-HRM-001.md)
  - Danh sách nhân viên dạng bảng với phân trang, tìm kiếm, lọc
  - Quick filters cho trạng thái (bench, assigned, etc.)
  - Export functionality

- [ ] Task FE-HRM-002: Xây dựng Employee Detail Page (MH-HRM-02) (High Priority) [Xem chi tiết](TaskDetails/FE-HRM-002.md)
  - Layout hiển thị thông tin nhân viên
  - Tab navigation (info, skills, projects, etc.)
  - Edit/Actions menu

- [ ] Task FE-HRM-003: Xây dựng Employee Form (MH-HRM-03) (High Priority) [Xem chi tiết](TaskDetails/FE-HRM-003.md)
  - Form thêm/sửa thông tin nhân viên
  - Upload/crop ảnh đại diện
  - Multi-step form nếu cần

- [ ] Task FE-HRM-004: Xây dựng Skills Management UI (MH-HRM-04) (High Priority) [Xem chi tiết](TaskDetails/FE-HRM-004.md)
  - UI quản lý danh mục skills (admin)
  - UI gán/cập nhật skills cho nhân viên
  - Skill level selection và visualization

- [ ] Task FE-HRM-005: Xây dựng Project Assignment UI (High Priority) [Xem chi tiết](TaskDetails/FE-HRM-005.md)
  - UI phân bổ nhân viên vào dự án
  - Date range picker cho thời gian assignment
  - Allocation percentage controls

- [ ] Task FE-HRM-006: Xây dựng Import/Export functionality (Medium Priority) [Xem chi tiết](TaskDetails/FE-HRM-006.md)
  - UI upload file cho import
  - Template download
  - Progress và error reporting

- [ ] Task FE-HRM-007: Xây dựng Employee Skills Search (Medium Priority) [Xem chi tiết](TaskDetails/FE-HRM-007.md)
  - Advanced search UI cho tìm kiếm theo skills
  - Filters cho experience level, availability
  - Result ranking và sorting

### Margin Management Module

- [ ] Task FE-MGN-001: Xây dựng Margin List Page (MH-MGN-01) (High Priority) [Xem chi tiết](TaskDetails/FE-MGN-001.md)
  - Bảng margin với color indicators
  - Filters cho team, time period
  - Permission-restricted access

- [ ] Task FE-MGN-002: Xây dựng Cost Input Form (MH-MGN-02) (High Priority) [Xem chi tiết](TaskDetails/FE-MGN-002.md)
  - Form nhập chi phí thủ công
  - Batch update UI
  - Validation và confirmation

- [ ] Task FE-MGN-003: Xây dựng Cost Import UI (Medium Priority) [Xem chi tiết](TaskDetails/FE-MGN-003.md)
  - Upload template cho import chi phí
  - Validation preview
  - Error handling và reporting

- [ ] Task FE-MGN-004: Xây dựng Margin Chart components (Medium Priority) [Xem chi tiết](TaskDetails/FE-MGN-004.md)
  - Trend chart cho margin theo thời gian
  - Breakdown chart theo team/department
  - Drill-down capability

### Opportunity Management Module

- [ ] Task FE-OPP-001: Xây dựng Opportunity List Page (MH-OPP-01) (High Priority) [Xem chi tiết](TaskDetails/FE-OPP-001.md)
  - Danh sách cơ hội với lọc, tìm kiếm
  - Status indicators (follow-up colors)
  - Assignment controls

- [ ] Task FE-OPP-002: Xây dựng Opportunity Detail Page (MH-OPP-02) (High Priority) [Xem chi tiết](TaskDetails/FE-OPP-002.md)
  - Chi tiết cơ hội với tabs
  - Activity/notes timeline
  - Action buttons (assign, prioritize)

- [ ] Task FE-OPP-003: Xây dựng Note/Activity components (Medium Priority) [Xem chi tiết](TaskDetails/FE-OPP-003.md)
  - Input form cho note mới
  - Activity timeline visualization
  - Rich text editor (nếu cần)

- [ ] Task FE-OPP-004: Xây dựng Hubspot Sync controls (Low Priority) [Xem chi tiết](TaskDetails/FE-OPP-004.md)
  - Manual sync trigger UI
  - Sync logs và status
  - Error reporting

### Contract & Revenue Module

- [ ] Task FE-CTR-001: Xây dựng Contract List Page (MH-CTR-01) (High Priority) [Xem chi tiết](TaskDetails/FE-CTR-001.md)
  - Danh sách hợp đồng với lọc, tìm kiếm
  - Status indicators (payment status)
  - Quick actions

- [ ] Task FE-CTR-002: Xây dựng Contract Detail Page (MH-CTR-02) (High Priority) [Xem chi tiết](TaskDetails/FE-CTR-002.md)
  - Chi tiết hợp đồng với tabs
  - Payment terms timeline
  - File attachments preview

- [ ] Task FE-CTR-003: Xây dựng Contract Form (MH-CTR-03) (High Priority) [Xem chi tiết](TaskDetails/FE-CTR-003.md)
  - Form thêm/sửa hợp đồng
  - Dynamic payment terms entry
  - Client và opportunity linking

- [ ] Task FE-CTR-004: Xây dựng Payment Status Update UI (MH-CTR-04) (High Priority) [Xem chi tiết](TaskDetails/FE-CTR-004.md)
  - Interface cho việc cập nhật trạng thái thanh toán
  - Batch update capability
  - History/audit log

- [ ] Task FE-CTR-005: Xây dựng Revenue KPI Management (MH-CTR-05) (Medium Priority) [Xem chi tiết](TaskDetails/FE-CTR-005.md)
  - UI thiết lập KPI doanh thu
  - Visualization so sánh KPI vs actual
  - Time period management

### Reports & Analytics Module

- [ ] Task FE-RPT-001: Xây dựng Report List Page (MH-RPT-01) (Medium Priority) [Xem chi tiết](TaskDetails/FE-RPT-001.md)
  - Danh sách các báo cáo có sẵn
  - Favorites và recent reports
  - Permission-based visibility

- [ ] Task FE-RPT-002: Xây dựng Report Viewer (MH-RPT-02) (Medium Priority) [Xem chi tiết](TaskDetails/FE-RPT-002.md)
  - Generic report viewing framework
  - Filter controls 
  - Export capabilities

- [ ] Task FE-RPT-003: Xây dựng Custom Report Builder (Low Priority, if needed) [Xem chi tiết](TaskDetails/FE-RPT-003.md)
  - UI cho việc tùy chỉnh báo cáo
  - Field selector và aggregation controls
  - Save và share functionality

### System Admin Module

- [ ] Task FE-ADM-003: Xây dựng System Configuration Page (MH-ADM-03) (Medium Priority) [Xem chi tiết](TaskDetails/FE-ADM-003.md)
  - Form cấu hình các tham số hệ thống
  - Danh mục quản lý
  - Thresholds và notification settings

- [ ] Task FE-ADM-004: Xây dựng System Logs Page (MH-ADM-04) (Low Priority) [Xem chi tiết](TaskDetails/FE-ADM-004.md)
  - Log viewer với filters
  - Log level controls
  - Export capability

### AI Chat Agent Integration

- [ ] Task FE-AI-001: Xây dựng Chat Interface (High Priority)
  - Chat UI widget (có thể minimize/maximize)
  - Message history và threading
  - Input box với autocomplete/suggestions

- [ ] Task FE-AI-002: Xây dựng Message Rendering (High Priority)
  - Markdown/rich text rendering cho chatbot responses
  - Different message types (text, data tables, charts)
  - Loading/typing indicators

- [ ] Task FE-AI-003: Xây dựng Contextual Help (Medium Priority)
  - Help triggers based on current page/context
  - Quick suggestion buttons
  - Feedback mechanism

### Testing & Quality Assurance 

- [ ] Task FE-QA-001: Xây dựng Unit Tests cho Components (Medium Priority) [Xem chi tiết](TaskDetails/FE-QA-001.md)
  - Unit tests cho shared components
  - Mock services và API calls
  - Test coverage reporting

- [ ] Task FE-QA-002: Xây dựng Integration Tests (Medium Priority) [Xem chi tiết](TaskDetails/FE-QA-002.md)
  - Tests cho main user flows
  - Form submission và validation tests
  - Authentication flow tests

- [ ] Task FE-QA-003: Xây dựng End-to-End Tests (Low Priority) [Xem chi tiết](TaskDetails/FE-QA-003.md)
  - E2E tests cho critical paths
  - Cross-browser testing
  - Accessibility testing

## Priority Summary

### High Priority (Immediate Focus)
- Infrastructure setup (routing, theme, state management)
- Core shared components
- Authentication flow
- Dashboard main widgets
- Employee list và detail pages
- Margin list page
- Opportunity management pages
- Contract management pages
- AI Chat interface

### Medium Priority (Secondary Focus)
- System admin pages
- Advanced filtering and search
- Import/Export functionality
- Custom report viewing
- Integration and unit testing

### Low Priority (Can be deferred)
- Advanced customization features
- System logs
- Custom report builder
- E2E testing
- Component documentation 