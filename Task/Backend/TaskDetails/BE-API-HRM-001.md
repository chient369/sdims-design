# Task BE-API-HRM-001: Triển khai Employee List API (API-HRM-001)

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
  - API Specifications:
    - [API-HRM-001: Employee List API](../../../DD/API/API-HRM-001.md)
  - Yêu cầu chức năng: 
    - [F-1.1: Quản lý Hồ sơ Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.3: Tìm kiếm/Lọc Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.9: Quản lý Trạng thái Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.12: Báo cáo Tỷ lệ Sử dụng Nguồn lực](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API để lấy danh sách nhân viên với các tính năng phân trang, sắp xếp, tìm kiếm và lọc. API này là nền tảng cho màn hình danh sách nhân viên và nhiều chức năng khác trong hệ thống, hỗ trợ việc quản lý nhân sự.

## Chi tiết công việc
1. **Tạo Lambda Handler Function cho Employee List API**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees` với method GET.
   - Cấu hình API Gateway cho route này (qua serverless.yml hoặc framework tương ứng).
   - Đảm bảo endpoint này được bảo vệ bởi authentication middleware.

2. **Triển khai Phân trang và Sắp xếp**:
   - Xử lý các query parameters:
     - page, limit (default values: page=1, limit=20)
     - sort_by (các trường như full_name, employee_id, team, position, status, v.v.)
     - sort_order (asc, desc)
   - Triển khai logic phân trang hiệu quả.
   - Đảm bảo truy vấn được tối ưu hóa.

3. **Triển khai Tìm kiếm và Lọc**:
   - Xử lý các query parameters:
     - search_term (tìm kiếm theo tên, mã nhân viên)
     - team (lọc theo team)
     - status (lọc theo trạng thái: active, bench, onsite, v.v.)
     - position (lọc theo vị trí công việc)
     - skill_ids (lọc theo skills, nếu có)
   - Triển khai full-text search hoặc tìm kiếm kết hợp để tìm kiếm hiệu quả.
   - Xây dựng conditional queries dựa trên các tham số lọc.

4. **Triển khai Phân quyền (RBAC)**:
   - Áp dụng các rules phân quyền khác nhau dựa trên vai trò:
     - Admin/TP: Xem toàn bộ danh sách nhân viên
     - Leader: Chỉ xem nhân viên trong team của mình
     - Nhân viên thường: Chỉ xem thông tin công khai
   - Lọc dữ liệu nhạy cảm dựa trên quyền hạn.
   - Sử dụng module Authorization để kiểm tra quyền.

5. **Triển khai Response Format**:
   - Cấu trúc response với:
     - Danh sách nhân viên
     - Metadata phân trang (tổng số, số trang, page hiện tại)
     - Format dữ liệu nhân viên theo yêu cầu API
   - Đảm bảo response nhẹ, chỉ bao gồm các thông tin cần thiết.
   - Cung cấp endpoints phụ để tải thêm thông tin chi tiết nếu cần.

6. **Triển khai Optimized Data Loading**:
   - Sử dụng eager loading cho các relationships (như team, position) để tránh N+1 query problem.
   - Thiết kế query sao cho chỉ lấy các trường cần thiết.
   - Cân nhắc việc sử dụng joins và subqueries để tối ưu hiệu suất.

7. **Triển khai Query Caching (nếu cần)**:
   - Thiết lập cache cho các queries phổ biến.
   - Cấu hình cache invalidation khi dữ liệu thay đổi.
   - Đảm bảo cache keys phản ánh đúng các tham số query.

8. **Triển khai Error Handling**:
   - Xử lý các trường hợp lỗi:
     - Invalid query parameters
     - Permission errors
     - Database errors
   - Trả về thông báo lỗi rõ ràng và HTTP status codes phù hợp.
   - Logging các errors để debugging.

## Đầu ra dự kiến
- Lambda handler function hoàn chỉnh cho Employee List API.
- Cơ chế phân trang, sắp xếp, tìm kiếm và lọc hiệu quả.
- Phân quyền dựa trên vai trò người dùng.
- Response format chuẩn và nhất quán.
- Hiệu suất tốt khi xử lý danh sách lớn.
- Unit tests cho các use cases khác nhau.
- Documentation về cách sử dụng API và các tham số. 