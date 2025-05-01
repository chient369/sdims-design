# API Details: Xem log đồng bộ Hubspot

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-20 | Chiến Trần Văn | Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để người dùng có quyền thích hợp có thể xem lịch sử và chi tiết các quá trình đồng bộ dữ liệu từ Hubspot, bao gồm thông tin tổng hợp và chi tiết từng quá trình đồng bộ cụ thể.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-OPP-004                                  |
| **Tên API**        | Xem log đồng bộ Hubspot                      |
| **Mô tả**          | API lấy danh sách các log đồng bộ Hubspot và chi tiết |
| **Module**         | Quản lý Cơ hội Kinh doanh                    |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/opportunities/sync/logs`            |
| **Quyền truy cập** | opportunity-log:read:all                                   |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên            | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|----------------|------------------|----------|-------|
| `syncId`       | String           | Không    | ID của tiến trình đồng bộ cụ thể cần xem log |
| `status`       | String           | Không    | Lọc theo trạng thái: `queued`, `processing`, `completed`, `failed` |
| `fromDate`     | String           | Không    | Lọc từ ngày (định dạng: YYYY-MM-DD) |
| `toDate`       | String           | Không    | Lọc đến ngày (định dạng: YYYY-MM-DD) |
| `sortBy`       | String           | Không    | Trường sắp xếp (mặc định: `startedAt`) |
| `sortDir`      | String           | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |
| `page`         | Integer          | Không    | Trang cần lấy (mặc định: `1`) |
| `size`         | Integer          | Không    | Số bản ghi mỗi trang (mặc định: `20`) |

### 3.3 Validate Rule

| Trường         | Điều kiện hợp lệ |
|----------------|------------------|
| `syncId`       | Phải tuân theo định dạng: `SYNC-YYYYMMDD-XXXXXX` |
| `status`       | Một trong: `queued`, `processing`, `completed`, `failed` |
| `fromDate`     | Định dạng: YYYY-MM-DD |
| `toDate`       | Định dạng: YYYY-MM-DD, phải sau hoặc bằng `fromDate` (nếu cả hai đều được cung cấp) |
| `sortBy`       | Một trong: `syncId`, `startedAt`, `completedAt`, `status` |
| `sortDir`      | Một trong: `asc`, `desc` |
| `page`         | ≥ 1 |
| `size`         | 1 → 100 |

### 3.4 Phân quyền đặc biệt
- Chỉ người dùng có quyền `sync:read` mới được xem log đồng bộ
- Theo ma trận CRUD, chỉ Admin và Division Manager có quyền này
- Dữ liệu log có thể chứa thông tin nhạy cảm về cơ hội kinh doanh

---

## 4. Response

### 4.1 Success - 200 OK (Danh sách log)

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "content": [
      {
        "syncId": "SYNC-20250531-123456",
        "status": "completed",
        "initiatedBy": {
          "id": 1,
          "name": "Admin Nguyễn"
        },
        "startedAt": "2025-05-31T15:00:22Z",
        "completedAt": "2025-05-31T15:02:45Z",
        "duration": 143,
        "syncParams": {
          "syncMode": "incremental",
          "fromDate": "2025-05-01",
          "toDate": "2025-05-31",
          "dealStage": "all",
          "overwriteExisting": false
        },
        "summary": {
          "totalOpportunities": 25,
          "newOpportunities": 12,
          "updatedOpportunities": 10,
          "skippedOpportunities": 3,
          "failedOpportunities": 0
        }
      },
      {
        "syncId": "SYNC-20250530-123455",
        "status": "failed",
        "initiatedBy": {
          "id": 3,
          "name": "Trưởng Phòng Lê"
        },
        "startedAt": "2025-05-30T09:15:10Z",
        "completedAt": "2025-05-30T09:15:45Z",
        "duration": 35,
        "syncParams": {
          "syncMode": "full",
          "dealStage": "all",
          "overwriteExisting": true
        },
        "summary": {
          "totalOpportunities": 0,
          "newOpportunities": 0,
          "updatedOpportunities": 0,
          "skippedOpportunities": 0,
          "failedOpportunities": 0
        },
        "error": {
          "code": "HUBSPOT_API_ERROR",
          "message": "Không thể kết nối với Hubspot API sau 3 lần thử lại"
        }
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 2,
      "totalPages": 5,
      "totalElements": 10,
      "sort": "startedAt,desc"
    }
  }
}
```

### 4.2 Success - 200 OK (Chi tiết log một tiến trình)

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "syncId": "SYNC-20250531-123456",
    "status": "completed",
    "initiatedBy": {
      "id": 1,
      "name": "Admin Nguyễn"
    },
    "startedAt": "2025-05-31T15:00:22Z",
    "completedAt": "2025-05-31T15:02:45Z",
    "duration": 143,
    "syncParams": {
      "syncMode": "incremental",
      "fromDate": "2025-05-01",
      "toDate": "2025-05-31",
      "dealStage": "all",
      "overwriteExisting": false
    },
    "summary": {
      "totalOpportunities": 25,
      "newOpportunities": 12,
      "updatedOpportunities": 10,
      "skippedOpportunities": 3,
      "failedOpportunities": 0
    },
    "details": [
      {
        "hubspotDealId": "12345678",
        "action": "created",
        "status": "success",
        "opportunity": {
          "id": 134,
          "code": "OPP-2025050134",
          "name": "Hệ thống CRM cho công ty ABC"
        },
        "timestamp": "2025-05-31T15:01:12Z"
      },
      {
        "hubspotDealId": "12345679",
        "action": "updated",
        "status": "success",
        "opportunity": {
          "id": 133,
          "code": "OPP-2025050133",
          "name": "Phát triển ứng dụng di động cho ngân hàng XYZ"
        },
        "changes": [
          {
            "field": "status",
            "oldValue": "proposal",
            "newValue": "negotiation"
          },
          {
            "field": "amount",
            "oldValue": "3000000000",
            "newValue": "3500000000"
          }
        ],
        "timestamp": "2025-05-31T15:01:35Z"
      },
      {
        "hubspotDealId": "12345680",
        "action": "skipped",
        "status": "skipped",
        "reason": "No changes detected",
        "timestamp": "2025-05-31T15:01:55Z"
      }
    ],
    "logs": [
      {
        "level": "INFO",
        "message": "Bắt đầu quá trình đồng bộ",
        "timestamp": "2025-05-31T15:00:22Z"
      },
      {
        "level": "INFO",
        "message": "Lấy thành công 25 deal từ Hubspot API",
        "timestamp": "2025-05-31T15:00:45Z"
      },
      {
        "level": "INFO",
        "message": "Hoàn thành quá trình đồng bộ",
        "timestamp": "2025-05-31T15:02:45Z"
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
      "field": "syncId",
      "message": "Định dạng syncId không hợp lệ. Yêu cầu định dạng SYNC-YYYYMMDD-XXXXXX"
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
      "field": "syncId",
      "message": "Không tìm thấy log đồng bộ với ID: SYNC-20250520-123456"
    }
  ]
}
``` 