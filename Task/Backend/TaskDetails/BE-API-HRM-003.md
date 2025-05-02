# Task BE-API-HRM-003: Triển khai Employee Skill APIs

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
    - [API-HRM-017: Get Employee Skills API](../../../DD/API/API-HRM-017.md)
    - [API-HRM-018: Add/Update Employee Skill API](../../../DD/API/API-HRM-018.md)
    - [API-HRM-019: Delete Employee Skill API](../../../DD/API/API-HRM-019.md)
  - Các API liên quan:
    - [API-HRM-003: Get Employee Detail API](../../../DD/API/API-HRM-003.md)
    - [API-HRM-009: Get Skill Categories API](../../../DD/API/API-HRM-009.md)
    - [API-HRM-013: Get Skills API](../../../DD/API/API-HRM-013.md)
  - Tasks liên quan:
    - [BE-API-HRM-004: Triển khai Skill & Category APIs](BE-API-HRM-004.md)
  - Yêu cầu chức năng: 
    - [F-1.6: Cập nhật/Xem Profile Skills Nhân viên](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.7: Tìm kiếm Nhân sự theo Skills](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.8: Gợi ý Nhân sự phù hợp](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai các API để quản lý kỹ năng (skills) của nhân viên, bao gồm việc xem danh sách kỹ năng của nhân viên, thêm/cập nhật kỹ năng cho nhân viên, và xóa kỹ năng khỏi hồ sơ nhân viên. Các API này hỗ trợ việc xây dựng và quản lý profile kỹ năng chi tiết cho từng nhân viên, phục vụ cho việc matching nhân sự với dự án.

## Chi tiết công việc
1. **Triển khai API Get Employee Skills (API-HRM-017)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}/skills` với method GET.
   - Truy vấn danh sách kỹ năng của nhân viên, bao gồm thông tin:
     - Skill ID, name
     - Skill Category
     - Level (1-5)
     - Years of experience
     - Certification information (nếu có)
     - Last updated
   - Hỗ trợ lọc theo category_id (optional).
   - Sắp xếp kết quả theo category, level (giảm dần) hoặc tùy chỉnh.
   - Áp dụng phân quyền: 
     - Mọi người dùng đều có thể xem skills của bất kỳ nhân viên nào
     - Chỉ hiển thị thông tin chi tiết hơn (ví dụ: notes) cho Leader/TP
   - Format response đúng theo API spec.

2. **Triển khai API Add/Update Employee Skill (API-HRM-018)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}/skills` với method POST.
   - Triển khai validation schema cho request body:
     - skill_id (required, valid skill ID)
     - level (required, 1-5)
     - years_of_experience (optional, number)
     - certified (optional, boolean)
     - certification_name (optional, string)
     - certification_date (optional, date)
     - notes (optional, string)
   - Áp dụng phân quyền:
     - Nhân viên có thể thêm/cập nhật kỹ năng cho chính mình
     - Leader có thể thêm/cập nhật kỹ năng cho nhân viên trong team
     - Admin/TP có thể thêm/cập nhật kỹ năng cho bất kỳ nhân viên nào
   - Xử lý logic thêm mới hoặc cập nhật kỹ năng đã tồn tại.
   - Ghi log việc thêm/cập nhật kỹ năng, đặc biệt là các thay đổi về level.
   - Format response trả về thông tin kỹ năng đã thêm/cập nhật.

3. **Triển khai API Delete Employee Skill (API-HRM-019)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employees/{employeeId}/skills/{skillId}` với method DELETE.
   - Kiểm tra sự tồn tại của mối quan hệ employee-skill.
   - Áp dụng phân quyền:
     - Nhân viên có thể xóa kỹ năng của chính mình
     - Leader có thể xóa kỹ năng của nhân viên trong team
     - Admin/TP có thể xóa kỹ năng của bất kỳ nhân viên nào
   - Xử lý xóa kỹ năng khỏi profile nhân viên.
   - Ghi log việc xóa kỹ năng.
   - Format response success/error.

4. **Triển khai Validation Logic cho Skill Level**:
   - Xây dựng validation rules phù hợp cho skill levels (1-5):
     - Level 1: Beginner
     - Level 2: Intermediate
     - Level 3: Advanced
     - Level 4: Expert
     - Level 5: Master
   - Cân nhắc triển khai validation logic phức tạp hơn, kết hợp giữa level và years_of_experience để đảm bảo tính nhất quán.
   - Tạo các helper functions để kiểm tra tính hợp lệ của các thông tin kỹ năng.

5. **Triển khai Business Rules cho Skill Management**:
   - Xây dựng các business rules như:
     - Nhân viên không thể tự đánh giá level skill quá cao nếu không có người duyệt (optional)
     - Giới hạn số lượng kỹ năng "Expert/Master" của một nhân viên (nếu cần)
     - Validation hợp lý giữa years of experience và join_date
   - Triển khai các checks để đảm bảo dữ liệu hợp lệ và nhất quán.

6. **Xây dựng Utility Functions cho Skill Analytics**:
   - Tạo các utility functions để:
     - Tính điểm skill match cho một nhân viên dựa trên bộ skills yêu cầu
     - Xác định các top skills của nhân viên
     - Phân tích sự phân bố kỹ năng trong một nhóm nhân viên
   - Các functions này sẽ được sử dụng bởi các API khác như Employee Suggestion.

7. **Triển khai Error Handling**:
   - Xử lý các trường hợp lỗi:
     - Skill không tồn tại
     - Employee không tồn tại
     - Invalid skill level/years of experience
     - Permission errors
   - Trả về thông báo lỗi rõ ràng và HTTP status codes phù hợp.
   - Logging các errors để debugging.

## Đầu ra dự kiến
- 3 Lambda handler functions cho các Employee Skill APIs.
- Validation schema chi tiết cho việc thêm/cập nhật skills.
- Business rules và validation logic cho skill levels.
- Phân quyền dựa trên vai trò và mối quan hệ (nhân viên, team leader, admin).
- Utility functions cho skill analytics và matching.
- Error handling và logging.
- Unit tests cho các use cases khác nhau.
- Documentation về cách sử dụng API, cấu trúc request/response, và các business rules. 