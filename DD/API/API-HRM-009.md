# API Details: Lấy danh sách loại kỹ năng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-2 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để lấy danh sách các loại kỹ năng (skill categories) trong hệ thống với các tùy chọn lọc và tìm kiếm.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-HRM-009                                  |
| **Tên API**        | Lấy danh sách loại kỹ năng                   |
| **Mô tả**          | API lấy danh sách các loại kỹ năng (skill categories) |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/skill-categories`                   |
| **Quyền truy cập** | skill:read                                   |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên        | Kiểu dữ liệu | Bắt buộc | Mô tả |
|------------|--------------|----------|-------|
| `keyword`  | String       | Không    | Tìm kiếm theo tên loại kỹ năng |
| `active`   | Boolean      | Không    | Lọc theo trạng thái: `true` - chỉ lấy loại đang active, `false` - chỉ lấy loại không active, bỏ trống - lấy tất cả (mặc định: `true`) |

### 3.3 Validate Rule

| Trường      | Điều kiện hợp lệ |
|-------------|------------------|
| `keyword`   | Độ dài tối đa: 100 ký tự |

### 3.4 Phân quyền đặc biệt
- Người dùng có quyền `skill:read`: Xem được danh sách loại kỹ năng
- Các vai trò Division Manager, 部長 (Leader) và Nhân viên đều có quyền xem danh sách loại kỹ năng

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "categories": [
      {
        "id": 1,
        "name": "Programming Language",
        "description": "Ngôn ngữ lập trình",
        "active": true,
        "sortOrder": 1,
        "skillCount": 15
      },
      {
        "id": 2,
        "name": "Framework",
        "description": "Framework phát triển",
        "active": true,
        "sortOrder": 2,
        "skillCount": 25
      },
      {
        "id": 3,
        "name": "Database",
        "description": "Hệ quản trị cơ sở dữ liệu",
        "active": true, 
        "sortOrder": 3,
        "skillCount": 10
      },
      {
        "id": 4,
        "name": "Language",
        "description": "Ngoại ngữ",
        "active": true,
        "sortOrder": 4,
        "skillCount": 5
      },
      {
        "id": 5,
        "name": "Tool",
        "description": "Công cụ phát triển",
        "active": true,
        "sortOrder": 5,
        "skillCount": 20
      },
      {
        "id": 6,
        "name": "Certificate",
        "description": "Chứng chỉ",
        "active": true,
        "sortOrder": 6,
        "skillCount": 8
      },
      {
        "id": 7,
        "name": "Cloud",
        "description": "Công nghệ Cloud",
        "active": true,
        "sortOrder": 7,
        "skillCount": 12
      },
      {
        "id": 8,
        "name": "Other",
        "description": "Kỹ năng khác",
        "active": true,
        "sortOrder": 99,
        "skillCount": 5
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
      "field": "keyword",
      "message": "Độ dài từ khóa tìm kiếm không được vượt quá 100 ký tự"
    }
  ]
}
```

#### 401 Unauthorized
```json
{
  "status": "error",
  "code": "E1001",
  "message": "Chưa xác thực hoặc phiên làm việc đã hết hạn"
}
```

#### 403 Forbidden
```json
{
  "status": "error",
  "code": "E1002",
  "message": "Bạn không có quyền truy cập chức năng này"
}
``` 