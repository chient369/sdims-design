# Task BE-API-HRM-004: Triển khai Skill & Category APIs

## Thông tin chung
- **Độ ưu tiên**: Trung bình
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
    - [API-HRM-009: Get Skill Categories API](../../../DD/API/API-HRM-009.md)
    - [API-HRM-010: Create Skill Category API](../../../DD/API/API-HRM-010.md)
    - [API-HRM-011: Update Skill Category API](../../../DD/API/API-HRM-011.md)
    - [API-HRM-012: Delete Skill Category API](../../../DD/API/API-HRM-012.md)
    - [API-HRM-013: Get Skills API](../../../DD/API/API-HRM-013.md)
    - [API-HRM-014: Create Skill API](../../../DD/API/API-HRM-014.md)
    - [API-HRM-015: Update Skill API](../../../DD/API/API-HRM-015.md)
    - [API-HRM-016: Delete Skill API](../../../DD/API/API-HRM-016.md)
  - Tasks liên quan:
    - [BE-API-HRM-003: Triển khai Employee Skill APIs](BE-API-HRM-003.md)
  - Yêu cầu chức năng: 
    - [F-1.5: Quản lý Danh mục Skills](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.6: Cập nhật/Xem Profile Skills Nhân viên](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.7: Tìm kiếm Nhân sự theo Skills](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai các API để quản lý loại kỹ năng (skill categories) và kỹ năng (skills), bao gồm các thao tác CRUD cho cả categories và skills. Các API này cho phép Admin/Leader quản lý danh mục kỹ năng và tạo cơ sở dữ liệu kỹ năng chuẩn hóa để sử dụng trong toàn bộ hệ thống.

## Chi tiết công việc
1. **Triển khai API Get Skill Categories (API-HRM-009)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/skill-categories` với method GET.
   - Truy vấn danh sách các loại kỹ năng (skill categories).
   - Hỗ trợ các tính năng:
     - Phân trang (nếu cần)
     - Sắp xếp (theo tên, ID)
     - Tìm kiếm (theo tên category)
   - Bao gồm thông tin số lượng skills trong mỗi category.
   - Đảm bảo phân quyền phù hợp (mọi người dùng đều có thể xem).
   - Format response theo yêu cầu API.

2. **Triển khai API Create Skill Category (API-HRM-010)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skill-categories` với method POST.
   - Triển khai validation schema cho request body:
     - name (required, unique)
     - description (optional)
     - display_order (optional, cho phép sắp xếp categories)
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Xử lý tạo category mới trong database.
   - Kiểm tra và xử lý trường hợp trùng tên.
   - Format response với thông tin category đã tạo.

3. **Triển khai API Update Skill Category (API-HRM-011)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skill-categories/{id}` với method PUT.
   - Triển khai validation schema cho request body:
     - name (optional, unique)
     - description (optional)
     - display_order (optional)
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Xử lý cập nhật thông tin category trong database.
   - Kiểm tra và xử lý trường hợp category không tồn tại hoặc trùng tên.
   - Format response với thông tin category đã cập nhật.

4. **Triển khai API Delete Skill Category (API-HRM-012)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skill-categories/{id}` với method DELETE.
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Kiểm tra xem category có chứa skills hay không và xử lý phù hợp:
     - Nếu có: Trả về lỗi hoặc hỏi xác nhận xóa các skills liên quan (qua flag force=true)
     - Nếu không: Tiến hành xóa
   - Xử lý xóa category và các ràng buộc liên quan.
   - Format response success/error.

5. **Triển khai API Get Skills (API-HRM-013)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/skills` với method GET.
   - Truy vấn danh sách kỹ năng với thông tin category.
   - Hỗ trợ các tính năng:
     - Lọc theo category_id (required hoặc optional)
     - Phân trang (nếu cần)
     - Sắp xếp (theo tên, category)
     - Tìm kiếm (theo tên skill)
   - Đảm bảo phân quyền phù hợp (mọi người dùng đều có thể xem).
   - Format response theo yêu cầu API.

6. **Triển khai API Create Skill (API-HRM-014)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skills` với method POST.
   - Triển khai validation schema cho request body:
     - name (required, unique trong một category)
     - category_id (required, valid category_id)
     - description (optional)
     - display_order (optional)
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Xử lý tạo skill mới trong database.
   - Kiểm tra và xử lý trường hợp trùng tên trong cùng category.
   - Format response với thông tin skill đã tạo.

7. **Triển khai API Update Skill (API-HRM-015)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skills/{id}` với method PUT.
   - Triển khai validation schema cho request body:
     - name (optional, unique trong một category)
     - category_id (optional, valid category_id)
     - description (optional)
     - display_order (optional)
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Xử lý cập nhật thông tin skill trong database.
   - Kiểm tra và xử lý trường hợp skill không tồn tại hoặc trùng tên.
   - Format response với thông tin skill đã cập nhật.

8. **Triển khai API Delete Skill (API-HRM-016)**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/admin/skills/{id}` với method DELETE.
   - Kiểm tra quyền truy cập (chỉ Admin mới có quyền).
   - Kiểm tra xem skill có được sử dụng bởi bất kỳ nhân viên nào không:
     - Nếu có: Trả về lỗi hoặc hỏi xác nhận xóa các liên kết (qua flag force=true)
     - Nếu không: Tiến hành xóa
   - Xử lý xóa skill và các ràng buộc liên quan.
   - Format response success/error.

9. **Triển khai Constraint Management**:
   - Xây dựng logic để quản lý các ràng buộc giữa:
     - Categories và Skills
     - Skills và EmployeeSkills
   - Đảm bảo integrity khi xóa category hoặc skill (cascade, restrict, hoặc set null).
   - Triển khai cơ chế thông báo trước khi xóa nếu có dependencies.

10. **Triển khai Error Handling và Validation chung**:
    - Sử dụng Validation module để xác thực input.
    - Xử lý các trường hợp lỗi đặc biệt:
      - Validation errors
      - Duplicate entities
      - Not found errors
      - Permission errors
      - Constraint violations
    - Đảm bảo thông báo lỗi rõ ràng và hữu ích.

## Đầu ra dự kiến
- 8 Lambda handler functions cho các Skill & Category APIs.
- Validation schemas cho request body của các API.
- Business rules và constraint management.
- Error handling và logging.
- Unit tests cho các use cases khác nhau.
- Documentation về cách sử dụng APIs và cấu trúc request/response.
- Cơ chế quản lý phân quyền (chỉ Admin mới có quyền quản lý danh mục). 