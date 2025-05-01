# API Details: Lấy chi tiết người dùng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cung cấp thông tin chi tiết về một người dùng cụ thể trong hệ thống, bao gồm thông tin cá nhân, phân quyền, lịch sử đăng nhập và các thông tin liên quan, giúp quản trị viên kiểm tra và quản lý người dùng một cách hiệu quả.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-ADM-003                                  |
| **Tên API**        | Lấy chi tiết người dùng                      |
| **Mô tả**          | API cung cấp thông tin chi tiết của một người dùng cụ thể |
| **Module**         | Quản trị Hệ thống (Admin)                   |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/admin/users/{userId}`               |
| **Quyền truy cập** | user:read                                    |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên        | Kiểu dữ liệu | Bắt buộc | Mô tả |
|------------|--------------|----------|-------|
| `userId`   | Integer      | Có       | ID của người dùng cần xem thông tin |

### 3.3 Validate Rule
| Trường     | Điều kiện hợp lệ |
|------------|------------------|
| `userId`   | Số nguyên dương  |

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "id": 2,
    "username": "leader1",
    "email": "leader1@company.com",
    "fullname": "Trần Văn B",
    "employee": {
      "id": 5,
      "code": "NV005",
      "name": "Trần Văn B",
      "team": {
        "id": 1,
        "name": "Team Alpha"
      },
      "position": "Leader"
    },
    "role": {
      "id": 2,
      "name": "Leader",
      "permissions": [
        "employee:read",
        "employee:update",
        "project:read",
        "project:update",
        "margin:read",
        "report:read"
      ]
    },
    "status": "Active",
    "lastLogin": "2025-04-30T14:20:15Z",
    "loginHistory": [
      {
        "timestamp": "2025-04-30T14:20:15Z",
        "ipAddress": "192.168.1.25",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/110.0.0.0"
      },
      {
        "timestamp": "2025-04-29T09:15:10Z",
        "ipAddress": "192.168.1.25",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/110.0.0.0"
      },
      {
        "timestamp": "2025-04-28T08:30:45Z",
        "ipAddress": "192.168.1.25",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/110.0.0.0"
      }
    ],
    "createdAt": "2025-01-15T09:30:00Z",
    "updatedAt": "2025-04-30T14:20:15Z",
    "createdBy": {
      "id": 1,
      "username": "admin"
    }
  }
}
```

### 4.2 Error Responses

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
  "message": "Không có quyền truy cập chức năng này"
}
```

#### 404 Not Found
```json
{
  "status": "error",
  "code": "E3002",
  "message": "Không tìm thấy người dùng với ID: 999"
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi truy vấn thông tin người dùng"
}
``` 