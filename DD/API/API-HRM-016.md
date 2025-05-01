# API Details: Xóa kỹ năng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-03 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cho phép quản trị viên xóa một kỹ năng (skill) khỏi hệ thống, giúp duy trì danh sách kỹ năng gọn gàng và phù hợp với nhu cầu thực tế của tổ chức, đồng thời kiểm tra các ràng buộc liên quan đến việc sử dụng kỹ năng của nhân viên.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-HRM-016                                  |
| **Tên API**        | Xóa kỹ năng                                  |
| **Mô tả**          | API cho phép xóa một kỹ năng (cần kiểm tra ràng buộc) |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `DELETE`                                     |
| **Endpoint**       | `/api/v1/admin/skills/{id}`                  |
| **Quyền truy cập** | skill:delete                                 |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên  | Kiểu dữ liệu | Bắt buộc | Mô tả |
|------|--------------|----------|-------|
| `id` | Integer      | Có       | ID của kỹ năng cần xóa |

### 3.3 Query Parameters

| Tên          | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------------|--------------|----------|-------|
| `force`      | Boolean      | Không    | Nếu `true`, xóa kỹ năng kể cả khi có nhân viên đang sử dụng (xóa cả các bản ghi kỹ năng của nhân viên); nếu `false`, chỉ xóa khi không có nhân viên nào sử dụng kỹ năng này (mặc định: `false`) |

### 3.4 Validate Rule

| Trường     | Điều kiện hợp lệ |
|------------|------------------|
| `id`       | Phải là số nguyên dương và tồn tại trong hệ thống |

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "message": "Xóa kỹ năng thành công",
  "data": {
    "id": 51,
    "name": "Docker & Kubernetes",
    "employeeCount": 0
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
      "field": "id",
      "message": "ID kỹ năng phải là số nguyên dương"
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
  "message": "Không có quyền truy cập chức năng này"
}
```

#### 404 Not Found
```json
{
  "status": "error",
  "code": "E3000",
  "message": "Không tìm thấy kỹ năng với ID: 99"
}
```

#### 409 Conflict
```json
{
  "status": "error",
  "code": "E4001",
  "message": "Không thể xóa kỹ năng",
  "errors": [
    {
      "field": "id",
      "message": "Kỹ năng này đang được sử dụng bởi 15 nhân viên. Hãy sử dụng tham số 'force=true' nếu muốn xóa kỹ năng và tất cả dữ liệu liên quan."
    }
  ]
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi xóa kỹ năng"
}
``` 