# API Details: Lấy dữ liệu tổng hợp cho dashboard

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Tạo tài liệu API dashboard tổng hợp | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để lấy dữ liệu tổng hợp từ nhiều module khác nhau nhằm hiển thị trên dashboard chính của hệ thống, tùy theo phân quyền và vai trò của người dùng, giúp người dùng có cái nhìn tổng quan về tình hình kinh doanh và hoạt động của công ty.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-RPT-001                                  |
| **Tên API**        | Lấy dữ liệu tổng hợp cho dashboard          |
| **Mô tả**          | API cho phép lấy dữ liệu tổng hợp từ nhiều module để hiển thị trên dashboard chính |
| **Module**         | Dashboard & Báo cáo                          |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/dashboard/summary`                  |
| **Quyền truy cập** | dashboard:read:all, dashboard:read:team, dashboard:read:own |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `fromDate`     | Date         | Không    | Ngày bắt đầu dữ liệu (yyyy-MM-dd, mặc định: đầu tháng hiện tại) |
| `toDate`       | Date         | Không    | Ngày kết thúc dữ liệu (yyyy-MM-dd, mặc định: ngày hiện tại) |
| `teamId`       | Long         | Không    | ID của team cần lọc dữ liệu |
| `widgets`      | Array[String]| Không    | Danh sách các widget cần lấy dữ liệu, nếu không có sẽ lấy tất cả |

### 3.3 Validate Rule

| Trường         | Điều kiện hợp lệ                     |
|----------------|------------------------------------- |
| `fromDate`     | Định dạng yyyy-MM-dd                |
| `toDate`       | Định dạng yyyy-MM-dd và >= fromDate |
| `teamId`       | Phải tồn tại trong hệ thống (nếu có) |
| `widgets`      | Các giá trị có thể: `opportunity_status`, `margin_distribution`, `revenue_summary`, `employee_status`, `utilization_rate` |

### 3.4 Phân quyền đặc biệt
- Mỗi người dùng chỉ nhận được dữ liệu dựa trên quyền truy cập của họ:
  - Admin và Division Manager: Xem tất cả dữ liệu
  - Leader: Chỉ xem dữ liệu của team mình và các nhân viên trong team
  - Sales: Chỉ xem dữ liệu cơ hội và doanh thu liên quan đến mình
- Phạm vi dữ liệu mặc định là từ đầu tháng hiện tại đến ngày hiện tại
- Widget `margin_distribution` chỉ hiển thị cho Admin và Division Manager

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "dateRange": {
      "fromDate": "2025-06-01",
      "toDate": "2025-06-15"
    },
    "widgets": {
      "opportunity_status": {
        "totalOpportunities": 28,
        "byStatus": {
          "green": 15,
          "yellow": 8,
          "red": 5
        },
        "byDealStage": [
          { "stage": "Prospecting", "count": 10 },
          { "stage": "Needs Analysis", "count": 5 },
          { "stage": "Proposal", "count": 8 },
          { "stage": "Negotiation", "count": 3 },
          { "stage": "Closed Won", "count": 2 }
        ],
        "topOpportunities": [
          {
            "id": 45,
            "name": "Dự án quản lý bán hàng ABC Corp",
            "customer": "ABC Corporation",
            "value": 500000000,
            "stage": "Proposal",
            "lastInteraction": "2025-06-10"
          },
          {
            "id": 48,
            "name": "Nâng cấp hệ thống XYZ",
            "customer": "XYZ Company",
            "value": 350000000,
            "stage": "Negotiation",
            "lastInteraction": "2025-06-12"
          }
        ]
      },
      "margin_distribution": {
        "totalEmployees": 45,
        "distribution": {
          "green": { "count": 28, "percentage": 62.2 },
          "yellow": { "count": 12, "percentage": 26.7 },
          "red": { "count": 5, "percentage": 11.1 }
        },
        "trend": [
          { "month": "2025-01", "value": 38.5 },
          { "month": "2025-02", "value": 39.2 },
          { "month": "2025-03", "value": 40.1 },
          { "month": "2025-04", "value": 38.7 },
          { "month": "2025-05", "value": 40.5 },
          { "month": "2025-06", "value": 41.2 }
        ]
      },
      "revenue_summary": {
        "currentMonth": {
          "target": 1500000000,
          "actual": 1250000000,
          "achievement": 83.3
        },
        "currentQuarter": {
          "target": 4000000000,
          "actual": 3200000000,
          "achievement": 80.0
        },
        "ytd": {
          "target": 15000000000,
          "actual": 13800000000,
          "achievement": 92.0
        },
        "contracts": {
          "total": 15,
          "newlyAdded": 2
        },
        "payment": {
          "totalDue": 2500000000,
          "overdue": 500000000,
          "upcoming": 1000000000
        }
      },
      "employee_status": {
        "totalEmployees": 45,
        "byStatus": {
          "allocated": 35,
          "available": 5,
          "endingSoon": 4,
          "onLeave": 1
        },
        "endingSoonList": [
          {
            "id": 102,
            "name": "Trần Thị B",
            "projectEndDate": "2025-07-15"
          },
          {
            "id": 105,
            "name": "Lê Văn E",
            "projectEndDate": "2025-07-30"
          }
        ]
      },
      "utilization_rate": {
        "overall": 85.5,
        "byTeam": [
          { "team": "Team Alpha", "rate": 92.0 },
          { "team": "Team Beta", "rate": 87.5 },
          { "team": "Team Gamma", "rate": 78.0 }
        ],
        "trend": [
          { "month": "2025-01", "value": 82.5 },
          { "month": "2025-02", "value": 84.0 },
          { "month": "2025-03", "value": 83.5 },
          { "month": "2025-04", "value": 84.5 },
          { "month": "2025-05", "value": 86.0 },
          { "month": "2025-06", "value": 85.5 }
        ]
      }
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
      "field": "toDate",
      "message": "Ngày kết thúc phải lớn hơn hoặc bằng ngày bắt đầu"
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
  "message": "Bạn không có quyền xem dữ liệu dashboard này"
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