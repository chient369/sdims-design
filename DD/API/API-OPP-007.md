# API Details: Lấy danh sách ghi chú của cơ hội

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-31 | Chiến Trần Văn |   Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để Sales, Leader hoặc quản lý có thể xem lịch sử ghi chú và hoạt động liên quan đến một cơ hội kinh doanh, bao gồm thông tin chi tiết ghi chú và tài liệu đính kèm.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-OPP-007                                  |
| **Tên API**        | Lấy danh sách ghi chú của cơ hội             |
| **Mô tả**          | API lấy danh sách ghi chú và lịch sử hoạt động của một cơ hội kinh doanh |
| **Module**         | Quản lý Cơ hội Kinh doanh                    |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/opportunities/{oppId}/notes`        |
| **Quyền truy cập** | opportunity-note:read:all, opportunity-note:read:assigned |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên         | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-------------|--------------|----------|-------|
| `oppId`     | Integer      | Có       | ID của cơ hội kinh doanh cần lấy ghi chú |

### 3.3 Query Parameters

| Tên                | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|--------------------|------------------|----------|-------|
| `activityType`     | String           | Không    | Lọc theo loại hoạt động, có thể truyền nhiều giá trị, phân tách bằng dấu phẩy |
| `fromDate`         | String           | Không    | Lọc từ ngày (định dạng: YYYY-MM-DD) |
| `toDate`           | String           | Không    | Lọc đến ngày (định dạng: YYYY-MM-DD) |
| `createdBy`        | Integer          | Không    | ID của người tạo ghi chú |
| `includeAttachments`| Boolean         | Không    | Có kèm theo thông tin file đính kèm không (mặc định: `true`) |
| `sortBy`           | String           | Không    | Trường sắp xếp (mặc định: `createdAt`) |
| `sortDir`          | String           | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |
| `page`             | Integer          | Không    | Trang cần lấy (mặc định: `1`) |
| `size`             | Integer          | Không    | Số bản ghi mỗi trang (mặc định: `20`) |

### 3.4 Validate Rule

| Trường             | Điều kiện hợp lệ |
|--------------------|------------------|
| `oppId`            | Số nguyên dương, phải tồn tại trong hệ thống |
| `activityType`     | Một hoặc nhiều giá trị từ: `note`, `email`, `call`, `meeting`, `document_sent`, `other` |
| `fromDate`         | Định dạng: YYYY-MM-DD |
| `toDate`           | Định dạng: YYYY-MM-DD, phải sau hoặc bằng `fromDate` (nếu cả hai đều được cung cấp) |
| `createdBy`        | Số nguyên dương, ID của người dùng tồn tại trong hệ thống |
| `includeAttachments`| Boolean: `true` hoặc `false` |
| `sortBy`           | Một trong: `createdAt`, `activityType`, `createdBy` |
| `sortDir`          | Một trong: `asc`, `desc` |
| `page`             | ≥ 1 |
| `size`             | 1 → 100 |

### 3.5 Phân quyền đặc biệt
- Leader: Chỉ xem được ghi chú của các cơ hội được gán cho mình
- Sales: Xem được ghi chú của các cơ hội do mình tạo hoặc phụ trách
- Division Manager: Xem được ghi chú của tất cả cơ hội thuộc bộ phận mình quản lý
- Admin: Xem được ghi chú của tất cả cơ hội

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "opportunity": {
      "id": 134,
      "code": "OPP-2025050134",
      "name": "Hệ thống CRM cho công ty ABC",
      "status": "proposal",
      "lastInteractionDate": "2025-05-16T17:30:45Z"
    },
    "content": [
      {
        "id": 36,
        "content": "Đã gửi đề xuất chi tiết cho khách hàng",
        "activityType": "document_sent",
        "meetingDate": null,
        "attachments": [
          {
            "id": 12,
            "name": "proposal_abc_v1.pdf",
            "type": "application/pdf",
            "size": 2400000,
            "url": "/api/v1/files/12"
          },
          {
            "id": 13,
            "name": "timeline.xlsx",
            "type": "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
            "size": 1500000,
            "url": "/api/v1/files/13"
          }
        ],
        "createdBy": {
          "id": 8,
          "name": "Lê Thị Leader"
        },
        "createdAt": "2025-05-16T17:30:45Z"
      },
      {
        "id": 35,
        "content": "Đã họp với khách hàng vào ngày 16/05/2025. Khách hàng quan tâm đến giải pháp của chúng ta và muốn xem demo trong tuần sau.",
        "activityType": "meeting",
        "meetingDate": "2025-05-16T14:30:00Z",
        "attachments": [],
        "createdBy": {
          "id": 8,
          "name": "Lê Thị Leader"
        },
        "createdAt": "2025-05-16T16:45:20Z"
      },
      {
        "id": 28,
        "content": "Đã trao đổi với khách hàng về yêu cầu ban đầu. Khách hàng quan tâm đến tính năng quản lý khách hàng và báo cáo thống kê.",
        "activityType": "call",
        "meetingDate": "2025-05-12T10:00:00Z",
        "attachments": [],
        "createdBy": {
          "id": 5,
          "name": "Trần Văn Sales"
        },
        "createdAt": "2025-05-12T10:30:15Z"
      }
    ],
    "pageable": {
      "pageNumber": 1,
      "pageSize": 20,
      "totalPages": 1,
      "totalElements": 3,
      "sort": "createdAt,desc"
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
      "message": "Ngày kết thúc phải sau hoặc bằng ngày bắt đầu"
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
      "field": "oppId",
      "message": "Không tìm thấy cơ hội với ID: 999"
    }
  ]
}
``` 