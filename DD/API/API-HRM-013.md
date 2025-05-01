# API Details: Lấy danh sách kỹ năng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-03 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cung cấp danh sách các kỹ năng (skills) có trong hệ thống với các tùy chọn lọc và phân trang, hỗ trợ cho việc quản lý và tra cứu kỹ năng khi cập nhật hồ sơ nhân viên hoặc tìm kiếm nhân sự theo yêu cầu kỹ năng.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-HRM-013                                  |
| **Tên API**        | Lấy danh sách kỹ năng                        |
| **Mô tả**          | API lấy danh sách các kỹ năng với bộ lọc và phân trang |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/skills`                             |
| **Quyền truy cập** | skill:read                                   |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `keyword`      | String       | Không    | Tìm kiếm theo tên kỹ năng |
| `categoryId`   | Integer      | Không    | Lọc theo ID loại kỹ năng |
| `active`       | Boolean      | Không    | Lọc theo trạng thái: `true` - chỉ lấy kỹ năng đang active, `false` - chỉ lấy kỹ năng không active, bỏ trống - lấy tất cả (mặc định: `true`) |
| `page`         | Integer      | Không    | Trang cần lấy (mặc định: `1`) |
| `size`         | Integer      | Không    | Số bản ghi mỗi trang (mặc định: `20`) |
| `sortBy`       | String       | Không    | Trường sắp xếp (mặc định: `name`) |
| `sortDir`      | String       | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `asc`) |

### 3.3 Validate Rule

| Trường        | Điều kiện hợp lệ |
|---------------|------------------|
| `keyword`     | Độ dài tối đa: 100 ký tự |
| `categoryId`  | Số nguyên dương |
| `page`        | ≥ 1 |
| `size`        | 1 → 100 |
| `sortBy`      | Một trong: `id`, `name`, `category`, `popularity` |
| `sortDir`     | Một trong: `asc`, `desc` |

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "content": [
      {
        "id": 1,
        "name": "Java",
        "description": "Ngôn ngữ lập trình Java",
        "category": {
          "id": 1,
          "name": "Programming Language"
        },
        "active": true,
        "popularity": 85,
        "employeeCount": 25
      },
      {
        "id": 2,
        "name": "Spring Boot",
        "description": "Framework phát triển ứng dụng Java",
        "category": {
          "id": 2,
          "name": "Framework"
        },
        "active": true,
        "popularity": 80,
        "employeeCount": 20
      },
      {
        "id": 3,
        "name": "React",
        "description": "Thư viện JavaScript để xây dựng giao diện người dùng",
        "category": {
          "id": 2,
          "name": "Framework"
        },
        "active": true,
        "popularity": 75,
        "employeeCount": 15
      },
      {
        "id": 4,
        "name": "MySQL",
        "description": "Hệ quản trị cơ sở dữ liệu quan hệ",
        "category": {
          "id": 3,
          "name": "Database"
        },
        "active": true,
        "popularity": 70,
        "employeeCount": 30
      },
      {
        "id": 5,
        "name": "Japanese",
        "description": "Tiếng Nhật",
        "category": {
          "id": 4,
          "name": "Language"
        },
        "active": true,
        "popularity": 90,
        "employeeCount": 35
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 5,
      "totalPages": 10,
      "totalElements": 50,
      "sort": "name,asc"
    }
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
      "field": "size",
      "message": "Kích thước trang phải nhỏ hơn hoặc bằng 100"
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

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E6000",
  "message": "Lỗi hệ thống khi truy vấn danh sách kỹ năng"
}
``` 