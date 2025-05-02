# Task BE-API-HRM-006: Triển khai Project History API (API-HRM-021)

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 2 ngày
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
    - [API-HRM-021: Get Employee Project History API](../../../DD/API/API-HRM-021.md)
  - Các API liên quan:
    - [API-HRM-003: Get Employee Detail API](../../../DD/API/API-HRM-003.md)
    - [API-HRM-020: Update Employee Status API](../../../DD/API/API-HRM-020.md)
  - Tasks liên quan:
    - [BE-API-HRM-005: Triển khai Employee Status & Assignment APIs](BE-API-HRM-005.md)
  - Yêu cầu chức năng: 
    - [F-1.11: Xem Lịch sử Dự án Nhân viên](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API để lấy và hiển thị lịch sử dự án của nhân viên, bao gồm các dự án mà nhân viên đã tham gia trong quá khứ và các dự án hiện tại. API này cung cấp cái nhìn tổng quan về kinh nghiệm dự án của nhân viên, giúp việc đánh giá và phân bổ nhân sự hiệu quả hơn.

## Chi tiết công việc
1. **Triển khai API Get Employee Project History (API-HRM-021)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}/project-history` với method GET.
   - Hỗ trợ các query parameters:
     - page, limit cho phân trang
     - from_date, to_date để lọc theo khoảng thời gian
     - sort_by (start_date, end_date, project_name, etc.)
     - sort_order (asc, desc)
   - Áp dụng phân quyền:
     - Mọi user đều có thể xem lịch sử dự án của nhân viên (mức độ chi tiết tùy theo role)
     - Leader/TP/Admin có thể xem thông tin chi tiết hơn
   - Format response với đầy đủ thông tin cần thiết.

2. **Triển khai Data Aggregation từ nhiều nguồn**:
   - Tổng hợp dữ liệu từ:
     - ProjectAssignment history của nhân viên
     - Contract-Employee links (nếu có)
     - Opportunity assignments (nếu có)
   - Sắp xếp và loại bỏ trùng lặp.
   - Bổ sung thông tin chi tiết về dự án từ các nguồn khác nếu cần.

3. **Triển khai Filtering và Sorting**:
   - Xây dựng logic lọc theo thời gian:
     - Dự án hiện tại (end_date = null hoặc trong tương lai)
     - Dự án trong khoảng thời gian cụ thể
     - Dự án đã hoàn thành
   - Triển khai các tùy chọn sắp xếp:
     - Theo thời gian (mới nhất, cũ nhất)
     - Theo tên dự án
     - Theo client

4. **Triển khai Response Formatting**:
   - Định dạng response với các thông tin:
     - project_name, client_name
     - start_date, end_date
     - utilization_percentage
     - role_in_project
     - project_description (nếu có)
     - skills_used (optional, based on employee skills at the time)
     - performance_notes (for Leader/TP only, if available)
   - Tối ưu hóa format response để dễ sử dụng trên UI.

5. **Triển khai Project Statistics**:
   - Tính toán và bao gồm các statistics trong response:
     - Tổng số dự án đã tham gia
     - Thời gian trung bình trên mỗi dự án
     - Danh sách clients đã làm việc cùng
     - Utilization rate trung bình
   - Đảm bảo các calculations này được thực hiện hiệu quả.

6. **Triển khai Caching (nếu cần)**:
   - Thiết lập cache cho dữ liệu project history:
     - Cache per employeeId
     - Invalidate cache khi có thay đổi về project assignments
   - Đảm bảo performance tốt khi xử lý dữ liệu lớn.

7. **Triển khai Error Handling**:
   - Xử lý các trường hợp lỗi:
     - Nhân viên không tồn tại
     - Invalid date range parameters
     - Permission errors
   - Trả về thông báo lỗi rõ ràng và HTTP status codes phù hợp.
   - Logging các errors để debugging.

## Đầu ra dự kiến
- Lambda handler function cho Project History API.
- Data aggregation logic từ nhiều nguồn.
- Filtering và sorting functionality.
- Well-formatted response structure cho UI.
- Project statistics calculations.
- Caching strategy (nếu cần).
- Error handling và logging.
- Unit tests cho các use cases khác nhau.
- Documentation về cách sử dụng API, response format, và các query parameters. 