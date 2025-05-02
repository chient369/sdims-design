# Task BE-API-HRM-005: Triển khai Employee Status & Assignment APIs

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-002: Module Authentication & Authorization](BE-CORE-002.md)
    - [BE-CORE-003: Module Validation & Exception Handling](BE-CORE-003.md)
    - [BE-CORE-004: Module Logger](BE-CORE-004.md)
  - Database Models:
    - [BE-DB-002: Employee Models](BE-DB-002.md)
    - [BE-DB-003: Cost & Margin Models](BE-DB-003.md)
  - API Specifications:
    - [API-HRM-020: Update Employee Status API](../../../DD/API/API-HRM-020.md)
  - Các API liên quan:
    - [API-HRM-001: Employee List API](../../../DD/API/API-HRM-001.md)
    - [API-HRM-003: Get Employee Detail API](../../../DD/API/API-HRM-003.md)
    - [API-HRM-004: Update Employee API](../../../DD/API/API-HRM-004.md)
  - Tasks liên quan:
    - [BE-API-HRM-001: Triển khai Employee List API](BE-API-HRM-001.md)
    - [BE-API-HRM-002: Triển khai Employee CRUD APIs](BE-API-HRM-002.md)
  - Yêu cầu chức năng: 
    - [F-1.9: Quản lý Trạng thái Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.10: Quản lý Phân bổ Dự án](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.12: Báo cáo Tỷ lệ Sử dụng Nguồn lực](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API để quản lý trạng thái nhân viên và phân bổ dự án, cho phép Leader/TP cập nhật trạng thái của nhân viên (active, bench, onsite, etc) và phân bổ nhân viên vào các dự án với tỷ lệ phân bổ (utilization) cụ thể. API này đóng vai trò quan trọng trong việc quản lý resource allocation và theo dõi utilization rate.

## Chi tiết công việc
1. **Triển khai API Update Employee Status (API-HRM-020)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}/status` với method PUT.
   - Triển khai validation schema cho request body:
     - status (required, enum: 'active', 'bench', 'onsite', 'training', 'leave', 'ending_soon', etc.)
     - project_assignments (array của các project assignments):
       - project_name (required)
       - client_name (required)
       - start_date (required)
       - end_date (required hoặc null cho ongoing projects)
       - utilization_percentage (required, 0-100)
       - role_in_project (optional)
       - notes (optional)
     - status_notes (optional, ghi chú về việc thay đổi trạng thái)
   - Áp dụng phân quyền:
     - Leader có thể cập nhật trạng thái nhân viên trong team
     - TP/Admin có thể cập nhật trạng thái bất kỳ nhân viên nào
   - Triển khai business logic:
     - Tự động đánh dấu 'bench' nếu không có project_assignments
     - Kiểm tra tổng utilization_percentage không vượt quá 100%
     - Xử lý các ràng buộc về thời gian (end_date >= start_date)

2. **Triển khai Project Assignment Management**:
   - Xử lý logic thêm/sửa/xóa project assignments:
     - Thêm assignments mới
     - Cập nhật assignments hiện tại
     - Kết thúc assignments (set end_date)
     - Xóa assignments không còn phù hợp
   - Đảm bảo duy trì lịch sử project assignments của nhân viên.
   - Quản lý các trạng thái phân bổ (pending, active, completed).

3. **Triển khai Utilization Rate Calculation**:
   - Xây dựng logic để tự động tính toán utilization rate của nhân viên dựa trên:
     - Tỷ lệ phân bổ vào các dự án
     - Thời gian (tính theo ngày, tuần, tháng)
     - Trạng thái hiện tại (bench, leave, etc.)
   - Tạo các utility functions để tính toán utilization metrics.
   - Cập nhật trường calculated_utilization của nhân viên sau mỗi thay đổi.

4. **Triển khai Status Change Workflow**:
   - Xây dựng workflow cho việc thay đổi trạng thái:
     - Ghi log mỗi khi trạng thái thay đổi
     - Cập nhật history của trạng thái
     - Thực hiện các side effects khi có thay đổi trạng thái (ví dụ: notifications)
   - Quản lý các business rules liên quan đến chuyển trạng thái (ví dụ: không thể chuyển từ 'leave' sang 'onsite' trực tiếp).

5. **Triển khai Integration với Cost & Margin Calculation**:
   - Đảm bảo thay đổi về status và project assignment được phản ánh trong:
     - Tính toán margin của nhân viên
     - Hiển thị trạng thái nhân viên trong các báo cáo
   - Xử lý logic update EmployeeRevenue khi có thay đổi về project assignment.

6. **Triển khai Notification Logic (nếu cần)**:
   - Thiết lập cơ chế thông báo khi:
     - Nhân viên sắp hết dự án (transition to 'ending_soon')
     - Nhân viên bị đưa vào trạng thái 'bench'
     - Các thay đổi quan trọng khác về trạng thái
   - Tích hợp với module Notification để gửi thông báo cho các bên liên quan.

7. **Triển khai Validation và Error Handling**:
   - Xử lý các trường hợp lỗi đặc biệt:
     - Nhân viên không tồn tại
     - Trạng thái không hợp lệ
     - Tổng utilization vượt quá 100%
     - Ràng buộc về thời gian không hợp lệ
     - Permission errors
   - Trả về thông báo lỗi rõ ràng và HTTP status codes phù hợp.
   - Logging các errors để debugging.

8. **Triển khai Transaction Management**:
   - Đảm bảo tất cả các thao tác liên quan (cập nhật status, thêm/sửa/xóa project assignments) đều được thực hiện trong một transaction.
   - Xử lý rollback khi có lỗi để đảm bảo tính nhất quán của dữ liệu.

## Đầu ra dự kiến
- Lambda handler function cho Update Employee Status API.
- Business logic cho quản lý project assignments.
- Utilization rate calculation utilities.
- Status change workflow và history tracking.
- Integration với Cost & Margin calculation.
- Notification logic cho status changes.
- Transaction handling để đảm bảo data consistency.
- Validation và error handling.
- Unit tests cho các use cases khác nhau.
- Documentation về cách sử dụng API và business rules. 