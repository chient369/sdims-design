# API Details: Lấy dữ liệu margin của nhân viên

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Tạo tài liệu API lấy dữ liệu margin | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để truy vấn và theo dõi dữ liệu margin của nhân viên, hỗ trợ cho việc quản lý hiệu suất tài chính.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-MGN-001                                  |
| **Tên API**        | Lấy dữ liệu margin của nhân viên             |
| **Mô tả**          | API lấy dữ liệu margin của nhân viên với các bộ lọc |
| **Module**         | Quản lý Hiệu suất & Margin                   |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/margins/employee`                   |
| **Quyền truy cập** | margin:read:all, margin:read:team                                  |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên            | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|----------------|------------------|----------|-------|
| `employeeId`   | Integer          | Không    | ID của nhân viên cụ thể cần xem margin, bỏ trống nếu muốn xem nhiều nhân viên |
| `teamId`       | Integer          | Không    | ID của team cần lọc |
| `period`       | String           | Không    | Chu kỳ thời gian: `month`, `quarter`, `year` (mặc định: `month`) |
| `fromDate`     | String           | Không    | Lọc từ ngày (định dạng: YYYY-MM-DD) |
| `toDate`       | String           | Không    | Lọc đến ngày (định dạng: YYYY-MM-DD) |
| `yearMonth`    | String           | Không    | Chỉ lấy một tháng cụ thể (định dạng: YYYY-MM) |
| `yearQuarter`  | String           | Không    | Chỉ lấy một quý cụ thể (định dạng: YYYY-Q1, YYYY-Q2, YYYY-Q3, YYYY-Q4) |
| `year`         | Integer          | Không    | Chỉ lấy một năm cụ thể |
| `status`       | String           | Không    | Lọc theo trạng thái margin: `Red`, `Yellow`, `Green` |
| `sortBy`       | String           | Không    | Trường sắp xếp (mặc định: `margin`) |
| `sortDir`      | String           | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |
| `page`         | Integer          | Không    | Trang cần lấy (mặc định: `1`) |
| `size`         | Integer          | Không    | Số bản ghi mỗi trang (mặc định: `20`) |

### 3.3 Validate Rule

| Trường         | Điều kiện hợp lệ |
|----------------|------------------|
| `employeeId`   | Phải là số nguyên dương và tồn tại trong hệ thống |
| `teamId`       | Phải là số nguyên dương và tồn tại trong hệ thống |
| `period`       | Một trong: `month`, `quarter`, `year` |
| `fromDate`     | Định dạng: YYYY-MM-DD |
| `toDate`       | Định dạng: YYYY-MM-DD, phải sau hoặc bằng fromDate |
| `yearMonth`    | Định dạng: YYYY-MM (VD: 2025-05) |
| `yearQuarter`  | Định dạng: YYYY-Q[1-4] (VD: 2025-Q2) |
| `year`         | Số 4 chữ số (VD: 2025) |
| `status`       | Một trong: `Red`, `Yellow`, `Green` |
| `sortBy`       | Một trong: `name`, `cost`, `revenue`, `margin`, `status` |
| `sortDir`      | Một trong: `asc`, `desc` |
| `page`         | ≥ 1 |
| `size`         | 1 → 100 |

### 3.4 Phân quyền đặc biệt
- Người dùng có quyền `margin:read:team`: Chỉ xem được margin của nhân viên trong team của mình
- Người dùng có quyền `margin:read:department`: Xem được margin của tất cả nhân viên thuộc bộ phận mình quản lý
- Người dùng có quyền `margin:read:all`: Xem được margin của tất cả nhân viên

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "summary": {
      "period": "month",
      "periodLabel": "Tháng 5/2025",
      "totalEmployees": 45,
      "averageCost": 25000000,
      "averageRevenue": 37500000,
      "averageMargin": 33.33,
      "statusCounts": {
        "Red": 8,
        "Yellow": 12,
        "Green": 25
      }
    },
    "content": [
      {
        "employeeId": 12,
        "employeeCode": "NV012",
        "name": "Hoàng Văn F",
        "position": "Developer",
        "team": {
          "id": 2,
          "name": "Team Beta"
        },
        "status": "Allocated",
        "currentProject": "Dự án ABC",
        "allocation": 100,
        "periods": [
          {
            "period": "2025-05",
            "periodLabel": "Tháng 5/2025",
            "cost": 30000000,
            "revenue": 52500000,
            "margin": 42.86,
            "marginStatus": "Green"
          }
        ]
      },
      {
        "employeeId": 8,
        "employeeCode": "NV008",
        "name": "Lý Thị G",
        "position": "Developer",
        "team": {
          "id": 1,
          "name": "Team Alpha"
        },
        "status": "EndingSoon",
        "currentProject": "Dự án XYZ",
        "allocation": 100,
        "periods": [
          {
            "period": "2025-05",
            "periodLabel": "Tháng 5/2025",
            "cost": 20000000,
            "revenue": 24000000,
            "margin": 16.67,
            "marginStatus": "Red"
          }
        ]
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 2,
      "totalPages": 23,
      "totalElements": 45,
      "sort": "margin,desc"
    }
  }
}
```

### 4.2 Success - 200 OK (Employee Detail)

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "employee": {
      "id": 12,
      "employeeCode": "NV012",
      "name": "Hoàng Văn F",
      "position": "Developer",
      "team": {
        "id": 2,
        "name": "Team Beta"
      },
      "status": "Allocated",
      "currentProject": "Dự án ABC"
    },
    "summary": {
      "period": "month",
      "fromDate": "2025-01-01",
      "toDate": "2025-05-31",
      "averageCost": 27500000,
      "averageRevenue": 45000000,
      "averageMargin": 38.89
    },
    "margins": [
      {
        "period": "2025-05",
        "periodLabel": "Tháng 5/2025",
        "cost": 30000000,
        "revenue": 52500000,
        "margin": 42.86,
        "marginStatus": "Green",
        "billableHours": 160,
        "billableRate": 328125
      },
      {
        "period": "2025-04",
        "periodLabel": "Tháng 4/2025",
        "cost": 30000000,
        "revenue": 48000000,
        "margin": 37.50,
        "marginStatus": "Green",
        "billableHours": 160,
        "billableRate": 300000
      },
      {
        "period": "2025-03",
        "periodLabel": "Tháng 3/2025",
        "cost": 25000000,
        "revenue": 40000000,
        "margin": 37.50,
        "marginStatus": "Green",
        "billableHours": 160,
        "billableRate": 250000
      },
      {
        "period": "2025-02",
        "periodLabel": "Tháng 2/2025",
        "cost": 25000000,
        "revenue": 40000000,
        "margin": 37.50,
        "marginStatus": "Green",
        "billableHours": 160,
        "billableRate": 250000
      },
      {
        "period": "2025-01",
        "periodLabel": "Tháng 1/2025",
        "cost": 25000000,
        "revenue": 37500000,
        "margin": 33.33,
        "marginStatus": "Yellow",
        "billableHours": 160,
        "billableRate": 234375
      }
    ]
  }
}
```

### 4.3 Error Responses

#### 400 Bad Request
```json
{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "period",
      "message": "Chu kỳ 'weekly' không hợp lệ. Các giá trị hợp lệ: month, quarter, year"
    }
  ]
}
```

#### 401 Unauthorized
```json
{
  "status": "error",
  "code": "E1001",
  "message": "Chưa đăng nhập"
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
  "code": "E3001",
  "message": "Không tìm thấy nhân viên với ID: 999"
}
``` 