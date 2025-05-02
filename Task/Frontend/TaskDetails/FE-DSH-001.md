# Task Detail: FE-DSH-001

## Thông tin chung
- **ID**: FE-DSH-001
- **Tên task**: Xây dựng Dashboard Page (MH-DSH-01)
- **Độ ưu tiên**: Cao
- **Estimate**: 3-4 days
- **Tham chiếu**: 
  - Màn hình: [MH-DSH-01](../../../assets/frontend/img/MH-DSH-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-DSH-01.md](../../../DD/SCREEN/MH-DSH-01.md)
  - API: [API-RPT-001](../../../DD/API/API-RPT-001.md)

## Mô tả
Xây dựng trang Dashboard chính của hệ thống, hiển thị tổng quan các KPI, metrics và thông tin quan trọng từ các module khác nhau trong một giao diện thống nhất. Dashboard là màn hình chính sau khi người dùng đăng nhập thành công. Trang này cung cấp cái nhìn tổng quan về tình trạng nhân sự, margin, cơ hội kinh doanh và doanh thu, đồng thời là điểm điều hướng chính đến các module chức năng khác trong hệ thống.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Triển khai grid layout responsive với các card widgets
- Phân vùng Dashboard thành các sections: 
  - Nhân sự (Employee stats)
  - Tài chính/Margin
  - Cơ hội kinh doanh
  - Doanh thu và KPI
- Mỗi widget có title, content area và action links (nếu có)
- Hiển thị các widget theo quyền hạn của từng vai trò người dùng
- Khu vực bộ lọc toàn cục (global filters) phía trên cho chọn khoảng thời gian và team/bộ phận

### UI Components
- Sử dụng Tailwind CSS cho styling
- Triển khai filter controls phía trên dashboard:
  - Date range picker cho khoảng thời gian
  - Filter theo team/bộ phận
  - Các filter khác
- Các widgets bao gồm:
  - Stat cards (số liệu với icons)
  - Line/Bar/Pie charts
  - Mini tables cho danh sách ngắn
  - Progress indicators
  - Gauge/Donut charts

### Functionality
- Widgets phải được load dựa trên quyền truy cập của người dùng (RBAC)
- Cài đặt lazy loading cho dữ liệu của các widgets để tối ưu performance
- Cấu hình refresh interval hoặc manual refresh
- Xử lý click interactions để điều hướng đến trang chi tiết tương ứng
- Lưu trạng thái filter người dùng vào localStorage
- Cung cấp khả năng tùy chỉnh hiển thị widgets (ẩn/hiện) nếu có quyền
- Drill-down capability cho một số widget (hiển thị modal với danh sách chi tiết khi click)

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/dashboard/summary` (API-RPT-001) - Dữ liệu tổng hợp cho dashboard
  - Các API liên quan khác có thể bao gồm:
    - `GET /api/v1/margins/summary` (API-MGN-002) - Dữ liệu margin tổng hợp
    - `GET /api/v1/employees` (API-HRM-001) - Dữ liệu nhân sự (với filter)
    - `GET /api/v1/opportunities` (API-OPP-001) - Dữ liệu cơ hội (với filter)
    - `GET /api/v1/sales-kpis` (API-CTR-015) - Dữ liệu KPI doanh thu

## Chi tiết các Widget
Dựa trên thiết kế [MH-DSH-01.png](../../../assets/frontend/img/MH-DSH-01.png) và mô tả [MH-DSH-01.md](../../../DD/SCREEN/MH-DSH-01.md), cần triển khai các widgets sau:

### Nhân sự (HR Widgets)
1. **Widget W-HRM-01 - Nhân sự Sẵn sàng (Bench)**:
   - Hiển thị số lượng nhân viên đang ở trạng thái Available/Bench
   - Biểu đồ tròn thể hiện tỷ lệ phân bố
   - Quick action để chuyển đến danh sách nhân viên với filter đã được áp dụng
   - Hiển thị cho: Admin, Trưởng bộ phận, Team Leader

2. **Widget W-HRM-02 - Nhân sự Sắp hết Dự án**:
   - Danh sách nhân viên sắp hết dự án (< 30 ngày)
   - Hiển thị tên, team, ngày kết thúc dự án
   - Sắp xếp theo thời gian còn lại (gần nhất lên đầu)
   - Hiển thị cho: Admin, Trưởng bộ phận, Team Leader

3. **Widget W-HRM-03 - Tỷ lệ Sử dụng Nguồn lực (%)**:
   - Biểu đồ (Gauge/Donut) hiển thị % Utilization Rate trung bình
   - Có thể lọc theo team/bộ phận
   - Hiển thị cho: Admin, Trưởng bộ phận, Team Leader

### Margin & Revenue Widgets
1. **Widget W-MGN-01 - Phân bổ Margin (Team/Bộ phận)**:
   - Hiển thị thông tin tổng hợp về margin theo team
   - Sử dụng color indicators (Red/Yellow/Green) dựa trên ngưỡng cấu hình
   - Biểu đồ tròn thể hiện tỷ lệ % nhân viên theo trạng thái Margin
   - Cho phép drill-down (click vào phần Red -> popup list)
   - Hiển thị cho: Admin, Trưởng bộ phận, Team Leader

2. **Widget W-CTR-02 - Doanh thu (vs KPI)**:
   - So sánh doanh thu thực tế với KPI
   - Progress bar với % hoàn thành
   - Breakdown theo team/sales person
   - Hiển thị cho: Admin, Trưởng bộ phận, Leader, Sales

3. **Widget W-CTR-03 - Công nợ Quá hạn**:
   - Hiển thị số lượng hoặc tổng giá trị các khoản thanh toán quá hạn
   - Click để xem chi tiết
   - Hiển thị cho: Admin, Trưởng bộ phận, Leader, Kế toán

### Opportunity Widgets
1. **Widget W-OPP-01 - Cơ hội Mới**:
   - Số lượng cơ hội mới được tạo/đồng bộ (theo bộ lọc thời gian)
   - Click để xem danh sách
   - Hiển thị cho: Admin, Trưởng bộ phận, Leader, Sales

2. **Widget W-OPP-02 - Cơ hội Cần Theo dõi (Red/Yellow)**:
   - Số lượng cơ hội đang ở trạng thái Follow-up Red hoặc Yellow
   - Hiển thị thời gian kể từ lần follow-up cuối
   - Click để xem danh sách (lọc tương ứng)
   - Hiển thị cho: Admin, Trưởng bộ phận, Leader, Sales

3. **Widget W-OPP-03 - Phễu Bán hàng (Tổng quan)**:
   - Biểu đồ cột/phễu hiển thị số lượng hoặc giá trị cơ hội theo từng Deal Stage
   - Hiển thị tổng giá trị các cơ hội
   - Hiển thị cho: Admin, Trưởng bộ phận, Leader, Sales

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-007: Chart components (dependency)
- FE-DSH-002: HR metrics widgets (breakdown task)
- FE-DSH-003: Revenue/Margin widgets (breakdown task)
- FE-DSH-004: Opportunity widgets (breakdown task)

## Phân chia Widgets theo Vai trò
- **Admin, Trưởng bộ phận**: Thấy tất cả widgets
- **Team Leader**: Thấy widget liên quan đến team mình (HR widgets, W-MGN-01), widget Cơ hội/Hợp đồng được assign
- **Sales**: Thấy widget Cơ hội (W-OPP-*), Hợp đồng (W-CTR-*) và KPI của mình
- **Nhân viên**: Thấy các widget thông tin chung (nếu có) và widget liên quan đến bản thân
- **Kế toán**: Thấy widget liên quan đến Hợp đồng/Công nợ (W-CTR-*)

## Tiêu chí chấp nhận
- Dashboard hiển thị đúng layout theo thiết kế MH-DSH-01.png
- Tất cả widgets load dữ liệu thành công từ API endpoints
- Filters hoạt động chính xác và ảnh hưởng đến dữ liệu hiển thị
- UI responsive từ desktop đến tablet (>= 768px)
- Dashboard được render phù hợp với quyền của người dùng (hiển thị/ẩn widgets theo vai trò)
- Các widget có trạng thái loading/empty/error được xử lý hợp lý
- Performance: trang dashboard load hoàn chỉnh trong < 3 giây
- Lưu trạng thái bộ lọc thời gian và team người dùng đã chọn cho lần truy cập sau
- Các drill-down action hiển thị thông tin chi tiết khi được kích hoạt 