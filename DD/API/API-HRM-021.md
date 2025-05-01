# API Details: Lấy lịch sử dự án của nhân viên

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-02 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
API này cho phép lấy danh sách lịch sử tham gia dự án của một nhân viên trong hệ thống quản lý nhân sự.

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-HRM-021                                      |
| **Tên API**        | Lấy lịch sử dự án của nhân viên              |
| **Mô tả**          | API lấy danh sách lịch sử tham gia dự án của một nhân viên |
| **Module**         | Quản lý Nhân sự (HRM)                        |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/employees/{employeeId}/project-history` |
| **Quyền truy cập** | project-history:read:all, project-history:read:team, project-history:read:own |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên          | Kiểu dữ liệu | Bắt buộc | Mô tả |
|--------------|--------------|----------|-------|
| `employeeId` | Integer      | Có       | ID của nhân viên cần lấy lịch sử dự án |

### 3.3 Query Parameters

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `fromDate`     | String       | Không    | Lọc từ ngày (định dạng: YYYY-MM-DD) |
| `toDate`       | String       | Không    | Lọc đến ngày (định dạng: YYYY-MM-DD) |
| `keyword`      | String       | Không    | Tìm kiếm theo tên dự án hoặc khách hàng |
| `sortBy`       | String       | Không    | Trường sắp xếp (mặc định: `startDate`) |
| `sortDir`      | String       | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |
| `billableOnly` | Boolean      | Không    | Chỉ lấy những dự án có tính doanh thu (mặc định: `false`) |

### 3.4 Validate Rule

| Trường        | Điều kiện hợp lệ |
|---------------|------------------|
| `employeeId`  | Phải là số nguyên dương và tồn tại trong hệ thống |
| `fromDate`    | Định dạng: YYYY-MM-DD |
| `toDate`      | Định dạng: YYYY-MM-DD, phải sau hoặc bằng fromDate |
| `keyword`     | Độ dài tối đa: 100 ký tự |
| `sortBy`      | Một trong: `startDate`, `endDate`, `projectName`, `clientName` |
| `sortDir`     | Một trong: `asc`, `desc` |

### 3.5 Phân quyền đặc biệt
- `employee:view`: Chỉ xem được lịch sử dự án của chính mình
- `team:employee:view`: Xem được lịch sử dự án của nhân viên trong team của mình
- `department:employee:view`: Xem được lịch sử dự án của tất cả nhân viên thuộc bộ phận mình quản lý
- `employee:view:all`: Xem được lịch sử dự án của tất cả nhân viên

---

## 4. Response

### 4.1 Success - 200 OK

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "status": "success",
  "code": 200,
  "data": {
    "employee": {
      "id": 12,
      "employeeCode": "NV012",
      "name": "Hoàng Văn F",
      "position": "Developer"
    },
    "currentProject": {
      "projectId": 5,
      "projectName": "Dự án ABC",
      "clientName": "Công ty XYZ",
      "allocation": 100,
      "role": "Backend Developer",
      "startDate": "2025-05-10",
      "endDate": "2025-08-31",
      "billable": true
    },
    "projectHistory": [
      {
        "id": 123,
        "projectId": 3,
        "projectName": "Dự án XYZ",
        "clientName": "Công ty ABC",
        "role": "Full Stack Developer",
        "startDate": "2024-10-15",
        "endDate": "2025-05-05",
        "allocation": 100,
        "billable": true,
        "notes": "Hoàn thành dự án đúng tiến độ, được khách hàng đánh giá cao"
      },
      {
        "id": 98,
        "projectId": 2,
        "projectName": "Dự án UVW",
        "clientName": "Công ty DEF",
        "role": "Backend Developer",
        "startDate": "2024-05-20",
        "endDate": "2024-10-10",
        "allocation": 80,
        "billable": true,
        "notes": "Phát triển các API và xử lý backend"
      },
      {
        "id": 76,
        "projectId": null,
        "projectName": "Đào tạo nội bộ",
        "clientName": "Nội bộ",
        "role": "Học viên",
        "startDate": "2024-04-01",
        "endDate": "2024-05-15",
        "allocation": 100,
        "billable": false,
        "notes": "Đào tạo các công nghệ mới"
      }
    ]
  }
}
```

### 4.2 Error Responses

#### 400 Bad Request
```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "toDate",
      "message": "Định dạng ngày không hợp lệ. Phải có dạng YYYY-MM-DD"
    }
  ]
}
```

#### 401 Unauthorized
```http
HTTP/1.1 401 Unauthorized
Content-Type: application/json

{
  "status": "error",
  "code": "E1000",
  "message": "Token không hợp lệ hoặc đã hết hạn"
}
```

#### 403 Forbidden
```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "status": "error",
  "code": "E1002",
  "message": "Không có quyền xem lịch sử dự án của nhân viên này"
}
```

#### 404 Not Found
```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "status": "error",
  "code": "E3000",
  "message": "Không tìm thấy nhân viên với ID: 999"
}
```

#### 500 Internal Server Error
```http
HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống, vui lòng thử lại sau hoặc liên hệ quản trị viên"
}
``` 