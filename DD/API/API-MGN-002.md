# API Details: Lấy dữ liệu margin tổng hợp

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-06-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API lấy dữ liệu margin tổng hợp | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để truy xuất dữ liệu tổng hợp về biên lợi nhuận (margin) cho các team và nhân viên, hỗ trợ việc theo dõi, phân tích hiệu quả kinh doanh và lợi nhuận trên dashboard, giúp lãnh đạo ra quyết định về chiến lược phân bổ nguồn lực và cải thiện hiệu suất.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-MGN-002                                  |
| **Tên API**        | Lấy dữ liệu margin tổng hợp                  |
| **Mô tả**          | API lấy dữ liệu margin tổng hợp cho dashboard |
| **Module**         | Quản lý Hiệu suất & Margin                   |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/margins/summary`                    |
| **Quyền truy cập** | margin-summary:read:all, margin-summary:read:team |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên            | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|----------------|------------------|----------|-------|
| `teamId`       | Integer          | Không    | ID của team cần lọc |
| `period`       | String           | Không    | Chu kỳ thời gian: `month`, `quarter`, `year` (mặc định: `month`) |
| `fromDate`     | String           | Không    | Lọc từ ngày (định dạng: YYYY-MM-DD) |
| `toDate`       | String           | Không    | Lọc đến ngày (định dạng: YYYY-MM-DD) |
| `yearMonth`    | String           | Không    | Chỉ lấy một tháng cụ thể (định dạng: YYYY-MM) |
| `yearQuarter`  | String           | Không    | Chỉ lấy một quý cụ thể (định dạng: YYYY-Q1, YYYY-Q2, YYYY-Q3, YYYY-Q4) |
| `year`         | Integer          | Không    | Chỉ lấy một năm cụ thể |
| `view`         | String           | Không    | Kiểu hiển thị: `table`, `chart` (mặc định: `table`) |
| `groupBy`      | String           | Không    | Nhóm theo: `team`, `status` (chỉ áp dụng khi không có teamId, mặc định: `team`) |

### 3.3 Validate Rule

| Trường         | Điều kiện hợp lệ |
|----------------|------------------|
| `teamId`       | Phải là số nguyên dương và tồn tại trong hệ thống |
| `period`       | Một trong: `month`, `quarter`, `year` |
| `fromDate`     | Định dạng: YYYY-MM-DD |
| `toDate`       | Định dạng: YYYY-MM-DD, phải sau hoặc bằng fromDate |
| `yearMonth`    | Định dạng: YYYY-MM (VD: 2025-05) |
| `yearQuarter`  | Định dạng: YYYY-Q[1-4] (VD: 2025-Q2) |
| `year`         | Số 4 chữ số (VD: 2025) |
| `view`         | Một trong: `table`, `chart` |
| `groupBy`      | Một trong: `team`, `status` |

### 3.4 Phân quyền đặc biệt
- Leader: Chỉ xem được tổng hợp margin của team mình
- Division Manager: Xem được tổng hợp margin của tất cả team thuộc bộ phận mình quản lý
- Admin: Xem được tổng hợp margin của tất cả team

---

## 4. Response

### 4.1 Success - 200 OK (Table View - Group by Team)

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "summary": {
      "period": "month",
      "periodLabel": "Tháng 5/2025",
      "totalTeams": 5,
      "totalEmployees": 45,
      "averageCost": 25000000,
      "averageRevenue": 37500000,
      "averageMargin": 33.33,
      "totalStatusCounts": {
        "Red": 8,
        "Yellow": 12,
        "Green": 25
      }
    },
    "teams": [
      {
        "id": 1,
        "name": "Team Alpha",
        "employeeCount": 10,
        "cost": 250000000,
        "revenue": 412500000,
        "margin": 39.39,
        "marginStatus": "Green",
        "statusCounts": {
          "Red": 1,
          "Yellow": 2,
          "Green": 7
        },
        "trends": {
          "margin": [35.0, 36.5, 37.8, 38.2, 39.39],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        }
      },
      {
        "id": 2,
        "name": "Team Beta",
        "employeeCount": 8,
        "cost": 200000000,
        "revenue": 280000000,
        "margin": 28.57,
        "marginStatus": "Yellow",
        "statusCounts": {
          "Red": 2,
          "Yellow": 4,
          "Green": 2
        },
        "trends": {
          "margin": [25.0, 26.2, 27.8, 28.0, 28.57],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        }
      },
      {
        "id": 3,
        "name": "Team Gamma",
        "employeeCount": 12,
        "cost": 300000000,
        "revenue": 360000000,
        "margin": 16.67,
        "marginStatus": "Red",
        "statusCounts": {
          "Red": 5,
          "Yellow": 5,
          "Green": 2
        },
        "trends": {
          "margin": [20.0, 18.5, 17.5, 17.0, 16.67],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        }
      }
    ]
  }
}
```

### 4.2 Success - 200 OK (Chart View)

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "summary": {
      "period": "month",
      "fromDate": "2025-01-01",
      "toDate": "2025-05-31",
      "totalTeams": 5,
      "totalEmployees": 45
    },
    "chartData": {
      "labels": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"],
      "labelFormat": "month",
      "datasets": [
        {
          "id": 1,
          "label": "Team Alpha",
          "data": [35.0, 36.5, 37.8, 38.2, 39.39],
          "statusColors": ["Yellow", "Green", "Green", "Green", "Green"]
        },
        {
          "id": 2,
          "label": "Team Beta",
          "data": [25.0, 26.2, 27.8, 28.0, 28.57],
          "statusColors": ["Red", "Yellow", "Yellow", "Yellow", "Yellow"]
        },
        {
          "id": 3,
          "label": "Team Gamma",
          "data": [20.0, 18.5, 17.5, 17.0, 16.67],
          "statusColors": ["Red", "Red", "Red", "Red", "Red"]
        }
      ],
      "average": [28.33, 28.40, 29.03, 29.73, 30.21]
    },
    "thresholds": {
      "Red": 25,
      "Yellow": 35
    }
  }
}
```

### 4.3 Success - 200 OK (Group by Status)

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
      "averageMargin": 33.33
    },
    "statusGroups": [
      {
        "status": "Red",
        "count": 8,
        "percentage": 17.78,
        "avgMargin": 19.25,
        "totalCost": 175000000,
        "totalRevenue": 216666667,
        "trends": {
          "count": [12, 10, 9, 9, 8],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        },
        "teams": [
          {
            "id": 1,
            "name": "Team Alpha",
            "count": 1
          },
          {
            "id": 2,
            "name": "Team Beta",
            "count": 2
          },
          {
            "id": 3,
            "name": "Team Gamma",
            "count": 5
          }
        ]
      },
      {
        "status": "Yellow",
        "count": 12,
        "percentage": 26.67,
        "avgMargin": 31.20,
        "totalCost": 290000000,
        "totalRevenue": 421512195,
        "trends": {
          "count": [15, 14, 13, 13, 12],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        },
        "teams": [
          {
            "id": 1,
            "name": "Team Alpha",
            "count": 2
          },
          {
            "id": 2,
            "name": "Team Beta",
            "count": 4
          },
          {
            "id": 3,
            "name": "Team Gamma",
            "count": 5
          },
          {
            "id": 4,
            "name": "Team Delta",
            "count": 1
          }
        ]
      },
      {
        "status": "Green",
        "count": 25,
        "percentage": 55.56,
        "avgMargin": 42.15,
        "totalCost": 600000000,
        "totalRevenue": 1036987951,
        "trends": {
          "count": [18, 21, 23, 23, 25],
          "periods": ["2025-01", "2025-02", "2025-03", "2025-04", "2025-05"]
        },
        "teams": [
          {
            "id": 1,
            "name": "Team Alpha",
            "count": 7
          },
          {
            "id": 2,
            "name": "Team Beta",
            "count": 2
          },
          {
            "id": 3,
            "name": "Team Gamma",
            "count": 2
          },
          {
            "id": 4,
            "name": "Team Delta",
            "count": 8
          },
          {
            "id": 5,
            "name": "Team Epsilon",
            "count": 6
          }
        ]
      }
    ]
  }
}
```

### 4.4 Error Responses

#### 400 Bad Request
```json
{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "groupBy",
      "message": "Giá trị 'role' không hợp lệ. Các giá trị hợp lệ: team, status"
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

#### 404 Not Found
```json
{
  "status": "error",
  "code": "E3000",
  "message": "Không tìm thấy dữ liệu",
  "errors": [
    {
      "field": "teamId",
      "message": "Không tìm thấy team với ID: 999"
    }
  ]
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống khi truy vấn dữ liệu margin"
}
``` 