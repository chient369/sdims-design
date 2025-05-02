# Task BE-DB-006: Xây dựng KPI & Revenue Models

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Database Models:
    - [BE-DB-005: Contract Models](BE-DB-005.md) (nguồn dữ liệu cho doanh thu thực tế)
    - [BE-DB-003: Cost & Margin Models](BE-DB-003.md) (liên quan đến tính toán doanh thu nhân viên)
  - KPI Management APIs:
    - [API-CTR-015: Get Sales KPIs API](../../../DD/API/API-CTR-015.md)
    - [API-CTR-016: Create/Update Sales KPI API](../../../DD/API/API-CTR-016.md)
    - [API-CTR-017: Delete Sales KPI API](../../../DD/API/API-CTR-017.md)
  - Report APIs:
    - [API-RPT-007: KPI Progress Reports API](../../../DD/API/API-RPT-007.md)
  - Yêu cầu chức năng: 
    - [F-4.8: Thiết lập KPI Doanh thu Sales/Bộ phận](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.9: Tính toán Doanh thu Thực tế](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.10: So sánh Doanh thu Thực tế vs KPI](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.11: Báo cáo Tổng hợp Doanh thu](../../../BD/FunctionDesign/FunctionList.md)
    - [F-5.1: Hiển thị Dashboard Tổng hợp](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến quản lý KPI doanh thu (target), theo dõi doanh thu thực tế (actual revenue), và tính toán các chỉ số tiến độ (progress). Các models này là nền tảng cho module theo dõi hiệu quả sales và tình hình đạt KPI doanh thu của công ty.

## Chi tiết công việc
1. **Xây dựng SalesKPI model**:
   - Thiết kế các fields cần thiết: id, user_id (sales), team_id (optional), year, quarter, month, target_amount, currency, notes, is_active, created_at, updated_at, v.v.
   - Định nghĩa relationship với User model.
   - Triển khai validation rules và business logic cho việc thiết lập KPI.
   - Hỗ trợ các loại KPI khác nhau (monthly, quarterly, yearly).
   - Tính năng phân bổ target theo tháng trong một quý hoặc năm (nếu cần).

2. **Xây dựng ActualRevenue model**:
   - Thiết kế các fields: id, user_id (sales), contract_id, payment_term_id, amount, currency, year, quarter, month, transaction_date, notes, created_at, updated_at, v.v.
   - Định nghĩa relationships với các models liên quan (User, Contract, PaymentTerm).
   - Triển khai logic để lấy dữ liệu từ các khoản thanh toán đã nhận (PaymentTerm).
   - Hỗ trợ cơ chế ghi nhận doanh thu theo nhiều tiêu chí khác nhau (ngày thanh toán, ngày ghi nhận, v.v.).

3. **Xây dựng KPIProgress model (hoặc view)**:
   - Thiết kế cấu trúc để lưu/tính toán: user_id, year, quarter, month, target_amount, actual_amount, progress_percentage, status, v.v.
   - Định nghĩa relationships với SalesKPI và ActualRevenue.
   - Triển khai các methods tính toán tiến độ đạt KPI: (Actual / Target) * 100%.
   - Tạo logic để xác định trạng thái (on-track, behind, ahead, completed).
   - Tính năng dự báo khả năng đạt KPI dựa trên trend hiện tại (optional).

4. **Xây dựng RevenueSummary model (hoặc view)**:
   - Thiết kế cấu trúc để lưu/tính toán dữ liệu tổng hợp theo nhiều chiều: team, thời gian, khách hàng, loại dự án, v.v.
   - Triển khai các methods tính toán và báo cáo tổng hợp.
   - Tạo cơ chế caching/materialized view cho các báo cáo tổng hợp thường xuyên sử dụng.

5. **Triển khai các helper models và business logic**:
   - Tạo các functions/methods hỗ trợ tính toán doanh thu theo nhiều góc nhìn khác nhau.
   - Thiết kế logic để tự động cập nhật ActualRevenue khi có thay đổi trong PaymentTerm.
   - Triển khai các calculations cho dashboard widgets.
   - Tạo cơ chế alert/notification khi đạt milestone KPI quan trọng.
   - Xây dựng logic phân tích xu hướng doanh thu theo thời gian.

## Đầu ra dự kiến
- SalesKPI, ActualRevenue, KPIProgress, và RevenueSummary models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Business logic cho việc tính toán KPI progress và revenue summary.
- Cơ chế integration với Contract/Payment để cập nhật doanh thu thực tế.
- Unit tests cho các models và business logic.
- Documentation về cấu trúc models và cách sử dụng.
