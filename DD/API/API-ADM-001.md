# API Details: Lấy danh sách người dùng hệ thống

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Cập nhật quyền truy cập theo định dạng mới, chuẩn hóa mã lỗi | -           | Draft     |

---

## 1. Mục tiêu  
API này cung cấp khả năng truy xuất danh sách người dùng hệ thống với thông tin cơ bản và phân quyền, cho phép người quản trị quản lý người dùng một cách hiệu quả.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-ADM-001                                  |
| **Tên API**        | Lấy danh sách người dùng hệ thống            |
| **Mô tả**          | API cung cấp danh sách người dùng hệ thống với các thông tin cơ bản và phân quyền |
| **Module**         | Quản trị Hệ thống (Admin)                   |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/admin/users`                        |
| **Quyền truy cập** | user:read                                    |

---

## 2. Parameters

### 2.1 Query Parameters

| Tên            | Kiểu dữ liệu         | Bắt buộc | Mô tả |
|----------------|----------------------|----------|-------|
| `keyword`      | String               | Không    | Từ khóa tìm kiếm theo tên, username, email |
| `roleId`       | Integer              | Không    | ID vai trò cần lọc |
| `status`       | String               | Không    | Trạng thái: `Active`, `Inactive`, `Locked` |
| `page`         | Integer              | Không    | Trang cần lấy (mặc định: `1`) |
| `size`         | Integer              | Không    | Số bản ghi mỗi trang (mặc định: `20`) |
| `sortBy`       | String               | Không    | Trường sắp xếp (mặc định: `created_at`) |
| `sortDir`      | String               | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |

### 2.2 Validate Rule
| Trường         | Điều kiện hợp lệ |
|----------------|------------------|
| `page`         | ≥ 1              |
| `size`         | 1 → 100          |
| `sortDir`      | `asc`, `desc`    |
| `status`       | Một trong: `Active`, `Inactive`, `Locked` |
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
        "username": "admin",
        "email": "admin@company.com",
        "fullname": "System Administrator",
        "employee": null,
        "role": {
          "id": 1,
          "name": "Admin"
        },
        "status": "Active",
        "lastLogin": "2025-04-30T15:45:30Z",
        "createdAt": "2025-01-01T08:00:00Z",
        "updatedAt": "2025-04-30T15:45:30Z"
      },
      {
        "id": 2,
        "username": "leader1",
        "email": "leader1@company.com",
        "fullname": "Trần Văn B",
        "employee": {
          "id": 5,
          "code": "NV005"
        },
        "role": {
          "id": 2,
          "name": "Leader"
        },
        "status": "Active",
        "lastLogin": "2025-04-30T14:20:15Z",
        "createdAt": "2025-01-15T09:30:00Z",
        "updatedAt": "2025-04-30T14:20:15Z"
      },
      {
        "id": 3,
        "username": "user1",
        "email": "user1@company.com",
        "fullname": "Nguyễn Văn A",
        "employee": {
          "id": 1,
          "code": "NV001"
        },
        "role": {
          "id": 3,
          "name": "Employee"
        },
        "status": "Locked",
        "lastLogin": "2025-04-15T10:20:30Z",
        "createdAt": "2025-01-20T14:15:00Z",
        "updatedAt": "2025-04-29T09:10:00Z"
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 20,
      "totalPages": 2,
      "totalElements": 25,
      "sort": "created_at,desc"
    }
  }
}
```

### 3.2 Error Responses

#### 3.2.1 401 Unauthorized
```json
{
  "status": "error",
  "code": "E1001",
  "message": "Chưa đăng nhập"
}
```

#### 3.2.2 403 Forbidden
```json
{
  "status": "error", 
  "code": "E1002",
  "message": "Không có quyền truy cập chức năng này"
}
```

#### 3.2.3 400 Bad Request
```json
{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "status",
      "message": "Giá trị 'Disabled' không được hỗ trợ. Các giá trị hợp lệ: Active, Inactive, Locked"
    }
  ]
}
```

#### 3.2.4 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi truy vấn danh sách người dùng"
}
``` 