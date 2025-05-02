# Frontend Task Details

## Tổng quan

Thư mục này chứa các tài liệu chi tiết mô tả các task phát triển frontend cho hệ thống SDIMS (Internal Management System). Mỗi task được mô tả chi tiết với các yêu cầu kỹ thuật, tham chiếu đến thiết kế, API endpoints và các tiêu chí chấp nhận.

## Cập nhật Mới Nhất

Tất cả các task đã được cập nhật để đảm bảo tính nhất quán giữa:
- Tham chiếu đến tài liệu thiết kế màn hình (MH-*)
- Tham chiếu đến các API endpoints theo đúng chuẩn định nghĩa
- Phân quyền truy cập (RBAC) và thuật ngữ vai trò (Admin, General Manager (課長), Team Leader (部長))
- Đồng bộ với các tài liệu mô tả chi tiết trong Design/DD/SCREEN/

## Cấu trúc Thư mục

- `README.md`: Tài liệu này
- `FE-<Module>-<Number>.md`: Các file mô tả chi tiết cho từng task frontend

## Danh sách Task Chính

### Dashboard
- **FE-DSH-001**: Dashboard Page (MH-DSH-01) - Trang Dashboard chính với các widget
- **FE-DSH-002**: HR metrics widgets - Widget hiển thị metrics nhân sự trên dashboard
- **FE-DSH-003**: Revenue/Margin widgets - Widget hiển thị thông tin doanh thu và margin
- **FE-DSH-004**: Opportunity widgets - Widget hiển thị thông tin cơ hội kinh doanh

### Human Resource Management (HRM)
- **FE-HRM-001**: Employee List Page (MH-HRM-01) - Trang danh sách nhân viên
- **FE-HRM-002**: Employee Detail Page (MH-HRM-02) - Trang chi tiết nhân viên
- **FE-HRM-003**: Employee Form (MH-HRM-03) - Form thêm/sửa thông tin nhân viên (chưa chi tiết hóa)
- **FE-HRM-004**: Skills Management UI (MH-HRM-04) - UI quản lý danh mục kỹ năng
- **FE-HRM-005**: Project Assignment UI - UI phân bổ nhân viên vào dự án
- **FE-HRM-006**: Import/Export functionality - Chức năng nhập/xuất dữ liệu nhân sự
- **FE-HRM-007**: Employee Skills Search - Tìm kiếm nhân viên theo kỹ năng

### Margin Management
- **FE-MGN-001**: Margin List Page (MH-MGN-01) - Trang danh sách margin nhân sự
- **FE-MGN-002**: Cost Input Form (MH-MGN-02) - Form nhập chi phí thủ công
- **FE-MGN-003**: Cost Import UI - Giao diện import chi phí từ file
- **FE-MGN-004**: Margin Chart components - Các biểu đồ hiển thị dữ liệu margin

### Opportunity Management
- **FE-OPP-001**: Opportunity List Page (MH-OPP-01) - Trang danh sách cơ hội kinh doanh
- **FE-OPP-002**: Opportunity Detail Page (MH-OPP-02) - Trang chi tiết cơ hội kinh doanh
- **FE-OPP-003**: Note/Activity components - Components quản lý ghi chú và hoạt động
- **FE-OPP-004**: Hubspot Sync controls - UI điều khiển đồng bộ với Hubspot

### Core Components
- **FE-CORE-001**: Layout Components - Các components layout chung của hệ thống

### Contract & Revenue Module
- **FE-CTR-001**: Contract List Page (MH-CTR-01) - Trang danh sách hợp đồng
- **FE-CTR-002**: Contract Detail Page (MH-CTR-02) - Trang chi tiết hợp đồng 
- **FE-CTR-003**: Contract Form (MH-CTR-03) - Form thêm/sửa hợp đồng
- **FE-CTR-004**: Payment Status Update UI (MH-CTR-04) - Giao diện cập nhật trạng thái thanh toán
- **FE-CTR-005**: Revenue KPI Management (MH-CTR-05) - Quản lý KPI doanh thu

### Reports & Analytics Module
- **FE-RPT-001**: Report List Page (MH-RPT-01) - Trang danh sách báo cáo
- **FE-RPT-002**: Report Viewer (MH-RPT-02) - Trình xem báo cáo
- **FE-RPT-003**: Custom Report Builder - Công cụ tạo báo cáo tùy chỉnh

### System Admin Module
- **FE-ADM-003**: System Configuration Page (MH-ADM-03) - Trang cấu hình hệ thống
- **FE-ADM-004**: System Logs Page (MH-ADM-04) - Trang xem log hệ thống

### Testing & Quality Assurance Module
- **FE-QA-001**: Unit Tests cho Components - Bộ unit tests cho shared components
- **FE-QA-002**: Integration Tests - Bộ tests cho tương tác giữa các components
- **FE-QA-003**: End-to-End Tests - Bộ tests từ đầu đến cuối cho toàn bộ hệ thống

## Cấu trúc Task

Mỗi task được mô tả với cấu trúc thống nhất:

1. **Thông tin chung**: ID, tên task, độ ưu tiên, estimate và tham chiếu
2. **Mô tả**: Mô tả tổng quan về task
3. **Yêu cầu kỹ thuật**: Layout, UI components, Functionality, API Integration
4. **Chi tiết các thành phần**: Mô tả chi tiết từng thành phần của UI
5. **Phân quyền truy cập**: Các vai trò và quyền hạn
6. **Các task liên quan**: Các dependencies và related tasks
7. **Tiêu chí chấp nhận**: Các điều kiện để task được chấp nhận

## Ưu tiên và Dependency

Các task được triển khai theo thứ tự ưu tiên sau:
1. Core Components (FE-CORE-*) - Các components shared dùng chung
2. Dashboard (FE-DSH-001) - Trang chính sau khi đăng nhập
3. Module-specific pages - Các trang theo từng module chức năng

## Hướng dẫn Quản lý Task

### Cập nhật Task
Khi cần cập nhật task, đảm bảo:
- Tham chiếu đúng đến các tài liệu thiết kế mới nhất
- Cập nhật API endpoint theo chuẩn mới
- Kiểm tra tính nhất quán giữa các tài liệu liên quan

### Thêm Task Mới
Khi thêm task mới:
- Sử dụng template có sẵn từ task hiện có
- Đặt ID theo chuẩn: `FE-<Module>-<Number>` 
- Tham chiếu đến tài liệu thiết kế và API liên quan
- Xác định rõ các dependencies và related tasks

### Ước lượng Thời gian
- Ước lượng thực hiện task dựa trên độ phức tạp:
  - Task đơn giản: 1-2 ngày
  - Task trung bình: 3-4 ngày
  - Task phức tạp: 5+ ngày
- Tính đến thời gian cần thiết cho testing và fixing bugs

## Danh sách Task Chi tiết Hiện có

Các task đã được chi tiết hóa:

1. **FE-CORE-001**: Layout Components
2. **FE-DSH-001**: Dashboard Page
3. **FE-DSH-002**: HR metrics widgets
4. **FE-DSH-003**: Revenue/Margin widgets
5. **FE-DSH-004**: Opportunity widgets
6. **FE-HRM-001**: Employee List Page
7. **FE-HRM-002**: Employee Detail Page
8. **FE-HRM-003**: Employee Form
9. **FE-HRM-004**: Skills Management UI
10. **FE-HRM-005**: Project Assignment UI
11. **FE-HRM-006**: Import/Export functionality
12. **FE-HRM-007**: Employee Skills Search
13. **FE-MGN-001**: Margin List Page
14. **FE-MGN-002**: Cost Input Form
15. **FE-MGN-003**: Cost Import UI
16. **FE-MGN-004**: Margin Chart components
17. **FE-OPP-001**: Opportunity List Page
18. **FE-OPP-002**: Opportunity Detail Page
19. **FE-OPP-003**: Note/Activity components
20. **FE-OPP-004**: Hubspot Sync controls
21. **FE-CTR-001**: Contract List Page
22. **FE-CTR-002**: Contract Detail Page
23. **FE-CTR-003**: Contract Form
24. **FE-CTR-004**: Payment Status Update UI
25. **FE-CTR-005**: Revenue KPI Management
26. **FE-RPT-001**: Report List Page
27. **FE-RPT-002**: Report Viewer
28. **FE-RPT-003**: Custom Report Builder
29. **FE-ADM-003**: System Configuration Page
30. **FE-ADM-004**: System Logs Page
31. **FE-QA-001**: Unit Tests cho Components
32. **FE-QA-002**: Integration Tests
33. **FE-QA-003**: End-to-End Tests

Các task quan trọng cần chi tiết hóa tiếp theo:

1. **FE-CORE-002**: Tab components
2. **FE-CORE-003**: Form components
3. **FE-CORE-004**: Table components 