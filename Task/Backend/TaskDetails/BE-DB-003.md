# Task BE-DB-003: Xây dựng Cost & Margin Models

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Database Models:
    - [BE-DB-002: Employee Models](BE-DB-002.md) (quan hệ với Employee model)
  - Margin Management APIs:
    - [API-MGN-001: Margin List API](../../../DD/API/API-MGN-001.md)
    - [API-MGN-002: Margin Summary API](../../../DD/API/API-MGN-002.md)
    - [API-MGN-003: Cost Import API](../../../DD/API/API-MGN-003.md)
    - [API-MGN-004: Manual Cost Entry API](../../../DD/API/API-MGN-004.md)
  - Report APIs:
    - [API-RPT-003: Margin Reports API](../../../DD/API/API-RPT-003.md)
  - Yêu cầu chức năng: 
    - [F-2.1: Nhập/Import Chi phí Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-2.2: Tính toán Doanh thu Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-2.3: Tính toán Margin Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-2.5: Tổng hợp Margin theo Team/Bộ phận](../../../BD/FunctionDesign/FunctionList.md)
    - [F-2.6: Cảnh báo Trạng thái Margin](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến quản lý chi phí (Cost), tính toán margin, và các ngưỡng cảnh báo (Threshold). Các models này là nền tảng cho module theo dõi hiệu quả kinh doanh, tính lợi nhuận của dự án và nhân sự.

## Chi tiết công việc
1. **Xây dựng EmployeeCost model**:
   - Thiết kế các fields cần thiết: employee_id, year, month, base_salary, benefits, allowances, taxes, total_cost, created_at, updated_at, v.v.
   - Định nghĩa relationship với Employee model.
   - Triển khai validation rules cho cost data.
   - Đảm bảo bảo mật thông tin nhạy cảm (mã hóa dữ liệu lương).
   - Thiết kế cơ chế lưu history changes (audit trail).

2. **Xây dựng EmployeeRevenue model**:
   - Thiết kế các fields: employee_id, year, month, contract_id, billing_rate, working_days, billable_hours, total_revenue, created_at, updated_at, v.v.
   - Định nghĩa relationships với Employee và Contract models.
   - Triển khai các methods để tính toán revenue dựa trên thông tin từ các hợp đồng.
   - Hỗ trợ multiple revenue streams cho một nhân viên trong cùng một tháng.

3. **Xây dựng MarginCalculation model (hoặc view)**:
   - Thiết kế cấu trúc để lưu/tính toán margin information: employee_id, year, month, cost, revenue, margin_amount, margin_percentage, status_color, v.v.
   - Định nghĩa relationships với EmployeeCost và EmployeeRevenue.
   - Triển khai các methods tính toán margin tự động: ((Revenue - Cost) / Revenue) * 100%.
   - Tạo cơ chế caching/materialized view cho việc tính toán margin (tối ưu hiệu năng).

4. **Xây dựng MarginThreshold model**:
   - Thiết kế các fields: name, red_threshold, yellow_threshold, green_threshold, description, is_active, v.v.
   - Triển khai logic để áp dụng ngưỡng cảnh báo vào MarginCalculation.
   - Tạo seed data cho các ngưỡng mặc định.

5. **Triển khai các helper models và business logic**:
   - Xây dựng CostImportLog model để tracking việc import chi phí.
   - Tạo các helper functions/methods để tổng hợp margin theo team, thời gian.
   - Triển khai các methods để lọc, tìm kiếm và phân tích dữ liệu margin.
   - Thiết kế logic để tự động cập nhật MarginCalculation khi có thay đổi trong EmployeeCost hoặc EmployeeRevenue.

## Đầu ra dự kiến
- EmployeeCost, EmployeeRevenue, MarginCalculation, và MarginThreshold models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Seed data cho margin thresholds.
- Logic calculations cho việc tính toán và cập nhật margin.
- Unit tests cho các models và business logic.
- Documentation về cấu trúc models và cách sử dụng.
