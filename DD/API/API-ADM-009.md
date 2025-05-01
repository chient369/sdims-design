# API Details: Xóa vai trò

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-02 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cho phép quản trị viên xóa vai trò khỏi hệ thống, giúp duy trì cấu trúc phân quyền gọn gàng và phù hợp với nhu cầu thực tế của tổ chức, đồng thời đảm bảo chỉ những vai trò không còn được sử dụng mới có thể bị xóa.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-ADM-009                                  |
| **Tên API**        | Xóa vai trò                                  |
| **Mô tả**          | API xóa vai trò khỏi hệ thống                |
| **Module**         | Quản trị Hệ thống (Admin)                   |
| **Phương thức**    | `DELETE`                                     |
| **Endpoint**       | `/api/v1/admin/roles/{roleId}`               |
| **Quyền truy cập** | role:delete                                  |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên        | Kiểu dữ liệu | Bắt buộc | Mô tả |
|------------|--------------|----------|-------|
| `roleId`   | Integer      | Có       | ID của vai trò cần xóa |

### 3.3 Validate Rule
| Trường     | Điều kiện hợp lệ |
|------------|------------------|
| `roleId`   | Số nguyên dương, phải tồn tại trong hệ thống, không phải là vai trò hệ thống (isSystem=true), không có người dùng nào đang dùng vai trò này |

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "id": 6,
    "name": "Project Manager",
    "message": "Vai trò đã được xóa thành công",
    "deletedAt": "2025-05-02T11:20:15Z",
    "deletedBy": {
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
  "code": "E3004",
  "message": "Không tìm thấy vai trò với ID: 999"
}
```

#### 409 Conflict
```json
{
  "status": "error",
  "code": "E4004",
  "message": "Không thể xóa vai trò hệ thống"
}
```

#### 422 Unprocessable Entity
```json
{
  "status": "error",
  "code": "E4005",
  "message": "Không thể xóa vai trò vì đang có 5 người dùng sử dụng"
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi xóa vai trò"
}
``` 