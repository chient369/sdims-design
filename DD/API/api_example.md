# API Details: Lấy danh sách nhân sự

---

## 1. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | HRM-001                                      |
| **Tên API**        | Lấy danh sách nhân sự                        |
| **Mô tả**          | API cho phép lấy danh sách nhân sự với các bộ lọc và phân trang |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/employees`                          |
| **Quyền truy cập** | Leader, 課長, Admin                          |

---

## 2. Parameters

### 2.1 Query Parameters

| Tên            | Kiểu dữ liệu         | Bắt buộc | Mô tả |
|----------------|----------------------|----------|-------|
| `keyword`      | String               | Không    | Tìm theo tên hoặc mã nhân viên |
| `teamId`       | Integer              | Không    | ID của team cần lọc |
| `position`     | String               | Không    | Vị trí công việc (Developer, Tester, BA, ...) |
| `status`       | String               | Không    | Trạng thái: `Allocated`, `Available`, `EndingSoon`, `OnLeave`, `Resigned` |
| `skills`       | Array of Integer     | Không    | Danh sách ID kỹ năng cần lọc |
| `minExperience`| Integer              | Không    | Số năm kinh nghiệm tối thiểu |
| `page`         | Integer              | Không    | Trang cần lấy (mặc định: `1`) |
| `size`         | Integer              | Không    | Số bản ghi mỗi trang (mặc định: `10`) |
| `sortBy`       | String               | Không    | Trường sắp xếp (mặc định: `name`) |
| `sortDir`      | String               | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `asc`) |


### 2.2 Validate Rule
| Trường      | Điều kiện hợp lệ |
|-------------|------------------|
| `page`      | ≥ 1              |
| `size`      | 1 → 100          |
| `sortDir`   | `asc`, `desc`    |
| `status`    | Một trong: `Allocated`, `Available`, `EndingSoon`, `OnLeave`, `Resigned` |
---

## 3. Response

### 3.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "content": [
      {
        "id": 1,
        "employeeCode": "NV001",
        "name": "Nguyễn Văn A",
        "email": "a.nguyenvan@company.com",
        "position": "Developer",
        "team": {
          "id": 1,
          "name": "Team Alpha"
        },
        "status": "Allocated",
        "currentProject": "Project XYZ",
        "utilization": 100,
        "endDate": "2025-12-31",
        "skills": [
          {
            "id": 1,
            "name": "Java",
            "level": "Advanced",
            "years": 5
          },
          {
            "id": 2,
            "name": "Spring Boot",
            "level": "Intermediate",
            "years": 3
          }
        ]
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 10,
      "totalPages": 5,
      "totalElements": 45,
      "sort": "name,asc"
    }
  }
}
```
### 3.2 Error Responses
#### 400 Bad Request
```json
{
  "status": "error",
  "code": 400,
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "size",
      "message": "Kích thước trang phải nhỏ hơn hoặc bằng 100"
    }
  ]
}
```
<Các lỗi còn lại>

####
