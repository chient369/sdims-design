# Task BE-API-HRM-002: Triển khai Employee CRUD APIs

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 4 ngày
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
    - [API-HRM-002: Create Employee API](../../../DD/API/API-HRM-002.md)
    - [API-HRM-003: Get Employee Detail API](../../../DD/API/API-HRM-003.md)
    - [API-HRM-004: Update Employee API](../../../DD/API/API-HRM-004.md)
    - [API-HRM-005: Delete Employee API](../../../DD/API/API-HRM-005.md)
  - Các API liên quan:
    - [API-HRM-001: Employee List API](../../../DD/API/API-HRM-001.md)
  - Task liên quan:
    - [BE-API-HRM-001: Triển khai Employee List API](BE-API-HRM-001.md)
  - Yêu cầu chức năng: 
    - [F-1.1: Quản lý Hồ sơ Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.6: Cập nhật/Xem Profile Skills Nhân viên](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.9: Quản lý Trạng thái Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.10: Quản lý Phân bổ Dự án](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai các API cơ bản để quản lý nhân viên (CRUD - Create, Read, Update, Delete), bao gồm việc tạo mới nhân viên, xem chi tiết thông tin nhân viên, cập nhật thông tin nhân viên và xóa (vô hiệu hóa) nhân viên. Các API này là nền tảng cho toàn bộ chức năng quản lý nhân sự trong hệ thống.

## Chi tiết công việc
1. **Triển khai API Create Employee (API-HRM-002)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees` với method POST.
   - Triển khai validation schema cho request body với các trường bắt buộc và tùy chọn:
     - full_name (required)
     - email (required, unique, valid format)
     - employee_id (required, unique)
     - position (required)
     - team (required)
     - join_date (required)
     - phone, address, department, level, v.v. (optional)
   - Kiểm tra quyền truy cập (chỉ Admin/TP có quyền tạo nhân viên).
   - Xử lý tạo bản ghi nhân viên trong database.
   - Triển khai logic tạo tài khoản user (nếu cần) hoặc liên kết với tài khoản hiện có.
   - Format response trả về với thông tin nhân viên đã tạo.

2. **Triển khai API Get Employee Detail (API-HRM-003)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}` với method GET.
   - Truy vấn thông tin chi tiết nhân viên với eager loading cho các relationships (team, skills, projects).
   - Áp dụng phân quyền: 
     - Admin/TP: Xem đầy đủ thông tin
     - Leader: Xem đầy đủ thông tin của nhân viên trong team
     - Nhân viên: Xem thông tin công khai, thông tin cá nhân chỉ của mình
   - Xử lý trường hợp nhân viên không tồn tại.
   - Format response với đầy đủ thông tin theo yêu cầu API.

3. **Triển khai API Update Employee (API-HRM-004)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}` với method PUT.
   - Triển khai validation schema cho request body, cho phép cập nhật các trường:
     - full_name, email, phone
     - position, team, level
     - status
     - address và các thông tin cá nhân khác
   - Áp dụng phân quyền chi tiết:
     - Admin/TP: Cập nhật tất cả thông tin
     - Leader: Cập nhật một số thông tin của nhân viên trong team
     - Nhân viên: Chỉ cập nhật một số thông tin cá nhân của mình
   - Xử lý cập nhật thông tin nhân viên trong database.
   - Ghi log các thay đổi quan trọng (thay đổi team, position, status).
   - Format response với thông tin nhân viên đã cập nhật.

4. **Triển khai API Delete Employee (API-HRM-005)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}` với method DELETE.
   - Kiểm tra quyền truy cập (chỉ Admin/TP có quyền xóa nhân viên).
   - Triển khai soft delete (đánh dấu xóa) thay vì xóa thật.
   - Kiểm tra và xử lý các ràng buộc khi xóa (ví dụ: nhân viên đang trong dự án).
   - Xử lý vô hiệu hóa tài khoản user liên kết (nếu cần).
   - Format response success/error.

5. **Triển khai Validation và Error Handling chung**:
   - Sử dụng Validation module để xác thực input cho tất cả các API.
   - Triển khai error handling cho các trường hợp:
     - Validation errors (dữ liệu không hợp lệ)
     - Trùng lặp (email, employee_id)
     - Not found errors (nhân viên không tồn tại)
     - Permission errors (không đủ quyền)
     - Business rule violations (vi phạm quy tắc nghiệp vụ)
   - Đảm bảo thông báo lỗi rõ ràng, nhất quán và có thể định vị chính xác vấn đề.

6. **Triển khai Transaction Management**:
   - Đảm bảo tất cả các thao tác ghi database (create, update, delete) sử dụng transactions.
   - Xử lý rollback khi có lỗi để đảm bảo tính nhất quán của dữ liệu.
   - Tối ưu hóa transaction isolation level phù hợp với yêu cầu.

7. **Triển khai Logging và Auditing**:
   - Ghi log đầy đủ cho mọi thao tác CRUD.
   - Lưu thông tin chi tiết về người thực hiện, thời gian, và nội dung thay đổi.
   - Đặc biệt quan trọng với các thao tác delete và các thay đổi trạng thái quan trọng.

## Đầu ra dự kiến
- 4 Lambda handler functions cho các Employee CRUD APIs.
- Validation schemas chi tiết cho request body của create và update.
- Schema và business rules xác thực dữ liệu nhân viên.
- Cơ chế phân quyền chi tiết dựa trên vai trò và mối quan hệ.
- Transaction handling để đảm bảo tính nhất quán dữ liệu.
- Xử lý lỗi chi tiết và nhất quán.
- Unit tests cho các use cases khác nhau của mỗi API.
- Logging và auditing cho các thao tác nhạy cảm.
- Documentation về cách sử dụng APIs và cấu trúc request/response. 