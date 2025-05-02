# Task BE-DB-002: Xây dựng Employee Models

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 4 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Employee Management APIs:
    - [API-HRM-001: Employee List](../../../DD/API/API-HRM-001.md)
    - [API-HRM-002: Create Employee](../../../DD/API/API-HRM-002.md)
    - [API-HRM-003: Get Employee Detail](../../../DD/API/API-HRM-003.md)
    - [API-HRM-004: Update Employee](../../../DD/API/API-HRM-004.md)
    - [API-HRM-005: Delete Employee](../../../DD/API/API-HRM-005.md)
  - Employee Skills APIs:
    - [API-HRM-017: Get Employee Skills](../../../DD/API/API-HRM-017.md)
    - [API-HRM-018: Add/Update Employee Skill](../../../DD/API/API-HRM-018.md)
    - [API-HRM-019: Delete Employee Skill](../../../DD/API/API-HRM-019.md)
  - Employee Status/Project APIs:
    - [API-HRM-020: Update Employee Status](../../../DD/API/API-HRM-020.md)
    - [API-HRM-021: Get Project History](../../../DD/API/API-HRM-021.md)
  - Skill Management APIs:
    - [API-HRM-009 đến API-HRM-016: Skill/Category Management](../../../BD/API/api_list.md)
  - Yêu cầu chức năng: 
    - [F-1.1: Quản lý Hồ sơ Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.5: Quản lý Danh mục Skills](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.6: Cập nhật/Xem Profile Skills Nhân viên](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.9: Quản lý Trạng thái Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.10: Quản lý Phân bổ Dự án](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.11: Xem Lịch sử Dự án Nhân viên](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến quản lý nhân viên (Employee), kỹ năng (Skill), lịch sử dự án (Project History) và các mối quan hệ cần thiết. Các models này là nền tảng cho module quản lý nhân sự và liên kết với các module khác trong hệ thống.

## Chi tiết công việc
1. **Xây dựng Employee model**:
   - Thiết kế các fields cần thiết: employee_id, full_name, email, phone, position, team, level, status, join_date, user_id (liên kết với User model), contract_type, salary_info (optional), v.v.
   - Định nghĩa các relationships với các bảng liên quan (User, Skills, Projects, v.v.).
   - Triển khai validation rules cho employee data.
   - Đảm bảo bảo mật thông tin nhạy cảm (salary, personal info, v.v.).
   - Thiết kế cơ chế soft delete (xóa mềm).

2. **Xây dựng SkillCategory model**:
   - Thiết kế các fields: name, description, created_at, updated_at, v.v.
   - Định nghĩa relationship với Skill model.
   - Triển khai validation rules (tên category duy nhất).

3. **Xây dựng Skill model**:
   - Thiết kế các fields: name, description, category_id, created_at, updated_at, v.v.
   - Định nghĩa relationship với SkillCategory và EmployeeSkill.
   - Triển khai validation rules (tên skill duy nhất trong một category).

4. **Xây dựng EmployeeSkill model (association table)**:
   - Thiết kế các fields: employee_id, skill_id, level (1-5), years_of_experience, certified, notes, last_updated, v.v.
   - Định nghĩa relationships với Employee và Skill.
   - Triển khai validation rules cho skill level và years_of_experience.

5. **Xây dựng ProjectAssignment và ProjectHistory models**:
   - Thiết kế các fields cho ProjectAssignment: employee_id, project_name, client_name, start_date, end_date, utilization_percentage, role_in_project, status, v.v.
   - Thiết kế ProjectHistory để lưu lịch sử các dự án đã hoàn thành.
   - Định nghĩa relationships với Employee model.
   - Triển khai validation rules và business logic cho project assignments.
   - Xây dựng mechanism để update utilization rate dựa trên phân bổ dự án.

6. **Thiết kế và triển khai các methods và business logic**:
   - Methods để tính toán total years of experience, dominant skills, current availability, v.v.
   - Helper functions để filter nhân viên dựa trên skills, availability, v.v.
   - Cơ chế để tracking changes trong profile nhân viên.
   - Thiết kế interface để tương tác với Cost & Margin models.

## Đầu ra dự kiến
- Employee, SkillCategory, Skill, EmployeeSkill, ProjectAssignment, và ProjectHistory models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Seed data cho basic skill categories và skills.
- Unit tests cho các models và relationships.
- Documentation về cấu trúc models và cách sử dụng.
