# API Details: Lấy danh sách kỹ năng của nhân viên

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-03 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cung cấp danh sách các kỹ năng (skills) đã được khai báo cho một nhân viên cụ thể, giúp người dùng xem và đánh giá các kỹ năng chuyên môn của nhân viên để phục vụ cho việc phân công công việc, tìm kiếm nhân sự phù hợp cho dự án, và phát triển nghề nghiệp của nhân viên.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-HRM-017                                  |
| **Tên API**        | Lấy danh sách kỹ năng của nhân viên          |
| **Mô tả**          | API lấy danh sách các kỹ năng đã được khai báo của một nhân viên |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/employees/{employeeId}/skills`      |
| **Quyền truy cập** | employee-skill:read:all, employee-skill:read:team, employee-skill:read:own |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên          | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------------|--------------|----------|-------|
| `employeeId` | Integer      | Có       | ID của nhân viên cần lấy danh sách kỹ năng |

### 3.3 Query Parameters

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `categoryId`   | Integer      | Không    | Lọc theo ID loại kỹ năng |
| `keyword`      | String       | Không    | Tìm kiếm theo tên kỹ năng |
| `sortBy`       | String       | Không    | Trường sắp xếp (mặc định: `level`) |
| `sortDir`      | String       | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |

### 3.4 Validate Rule

| Trường        | Điều kiện hợp lệ |
|---------------|------------------|
| `employeeId`  | Phải là số nguyên dương và tồn tại trong hệ thống |
| `categoryId`  | Phải là số nguyên dương |
| `keyword`     | Độ dài tối đa: 100 ký tự |
| `sortBy`      | Một trong: `skill`, `level`, `years`, `lastUpdated` |
| `sortDir`     | Một trong: `asc`, `desc` |

### 3.5 Phân quyền đặc biệt
- Người dùng chỉ có quyền `employee.skill:read` trên tài khoản của chính mình
- Người dùng có quyền `team.skill:read` có thể xem kỹ năng của nhân viên trong team mình quản lý
- Người dùng có quyền `department.skill:read` có thể xem kỹ năng của tất cả nhân viên thuộc bộ phận mình quản lý
- Người dùng có quyền `system.skill:read` có thể xem kỹ năng của tất cả nhân viên

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "employeeId": 12,
    "employeeCode": "NV012",
    "name": "Hoàng Văn F",
    "position": "Developer",
    "skills": [
      {
        "id": 1,
        "employeeSkillId": 101,
        "name": "Java",
        "category": {
          "id": 1,
          "name": "Programming Language"
        },
        "level": "Advanced",
        "years": 5,
        "description": "Có kinh nghiệm phát triển ứng dụng enterprise với Java",
        "isVerified": true,
        "verifiedBy": {
          "id": 2,
          "name": "Nguyễn Văn Leader"
        },
        "lastUpdated": "2025-03-15T10:30:00Z"
      },
      {
        "id": 2,
        "employeeSkillId": 102,
        "name": "Spring Boot",
        "category": {
          "id": 2,
          "name": "Framework"
        },
        "level": "Advanced",
        "years": 4,
        "description": "Có kinh nghiệm phát triển RESTful API và microservices với Spring Boot",
        "isVerified": true,
        "verifiedBy": {
          "id": 2,
          "name": "Nguyễn Văn Leader"
        },
        "lastUpdated": "2025-03-15T10:30:00Z"
      },
      {
        "id": 4,
        "employeeSkillId": 103,
        "name": "MySQL",
        "category": {
          "id": 3,
          "name": "Database"
        },
        "level": "Intermediate",
        "years": 3,
        "description": "Có kinh nghiệm thiết kế database và viết các truy vấn phức tạp",
        "isVerified": false,
        "verifiedBy": null,
        "lastUpdated": "2025-04-10T14:45:00Z"
      }
    ]
  }
}
```

### 4.2 Error Responses

#### 400 Bad Request
```json
{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "employeeId",
      "message": "ID nhân viên phải là số nguyên dương"
    }
  ]
}
```

#### 401 Unauthorized
```json
{
  "status": "error",
  "code": "E1000",
  "message": "Token không hợp lệ hoặc đã hết hạn"
}
```

#### 403 Forbidden
```json
{
  "status": "error",
  "code": "E1002",
  "message": "Không có quyền truy cập thông tin kỹ năng của nhân viên này"
}
```

#### 404 Not Found
```json
{
  "status": "error",
  "code": "E3000",
  "message": "Không tìm thấy nhân viên với ID: 999"
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi lấy danh sách kỹ năng của nhân viên"
}
``` 