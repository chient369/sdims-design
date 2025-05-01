# API Details: Lấy chi tiết một cơ hội kinh doanh

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-31 | Chiến Trần Văn | Chuẩn hóa tài liệu API lấy chi tiết một cơ hội kinh doanh | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để truy xuất thông tin chi tiết của một cơ hội kinh doanh cụ thể, bao gồm thông tin cơ bản, lịch sử hoạt động, ghi chú và tài liệu đính kèm, giúp người dùng có cái nhìn đầy đủ về tiến trình của cơ hội.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-OPP-002                                  |
| **Tên API**        | Lấy chi tiết một cơ hội kinh doanh           |
| **Mô tả**          | API lấy thông tin chi tiết của một cơ hội kinh doanh bao gồm lịch sử hoạt động và ghi chú |
| **Module**         | Quản lý Cơ hội Kinh doanh                    |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/opportunities/{oppId}`              |
| **Quyền truy cập** | opportunity:read:all, opportunity:read:own, opportunity:read:assigned |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên         | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-------------|--------------|----------|-------|
| `oppId`     | Integer      | Có       | ID của cơ hội kinh doanh cần xem chi tiết |

### 3.3 Query Parameters

| Tên              | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|------------------|------------------|----------|-------|
| `includeNotes`   | Boolean          | Không    | Có kèm theo ghi chú và lịch sử hoạt động không (mặc định: `true`) |
| `includeHistory` | Boolean          | Không    | Có kèm theo lịch sử thay đổi không (mặc định: `false`) |

### 3.4 Validate Rule

| Trường            | Điều kiện hợp lệ |
|-------------------|------------------|
| `oppId`           | Số nguyên dương, phải tồn tại trong hệ thống |
| `includeNotes`    | Boolean: `true` hoặc `false` |
| `includeHistory`  | Boolean: `true` hoặc `false` |

### 3.5 Phân quyền đặc biệt
- Leader: Chỉ xem được chi tiết các cơ hội được gán cho mình
- Sales: Xem được chi tiết các cơ hội do mình tạo và các cơ hội chưa được gán
- Division Manager: Xem được chi tiết tất cả cơ hội thuộc bộ phận mình quản lý
- Admin: Xem được chi tiết tất cả cơ hội

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
      "description": "Triển khai hệ thống CRM cho công ty ABC trong vòng 6 tháng",
      "customerDetails": {
        "name": "Công ty ABC",
        "contact": "Nguyễn Văn A",
        "email": "nguyenvana@abc.com",
        "phone": "+84901234567",
        "address": "123 Đường Lê Lợi, Quận 1, TP.HCM",
        "website": "https://abc.com.vn",
        "industry": "Thương mại điện tử"
      },
      "status": "proposal",
      "dealSize": "large",
      "amount": 1250000000,
      "priority": true,
      "createdBy": {
        "id": 5,
        "name": "Trần Văn Sales",
        "email": "sales@company.com",
        "phone": "+84987654321"
      },
      "assignedTo": {
        "id": 8,
        "name": "Lê Thị Leader",
        "email": "leader@company.com",
        "phone": "+84987654322"
      },
      "source": "hubspot",
      "externalId": "HS-12345678",
      "closingDate": "2025-07-31",
      "closingProbability": 70,
      "requirements": [
        {
          "name": "Tích hợp hệ thống thanh toán",
          "description": "Tích hợp với cổng thanh toán VNPay và ZaloPay"
        },
        {
          "name": "Quản lý khách hàng",
          "description": "Module quản lý thông tin khách hàng và lịch sử giao dịch"
        },
        {
          "name": "Báo cáo thống kê",
          "description": "Dashboard và các báo cáo tổng hợp"
        }
      ],
      "tags": ["Java", "Web", "CRM"],
      "createdAt": "2025-05-10T09:15:32Z",
      "updatedAt": "2025-05-15T14:22:45Z"
    },
    "notes": [
      {
        "id": 35,
        "content": "Đã gửi đề xuất chi tiết qua email",
        "attachments": [
          {
            "id": 12,
            "name": "proposal_abc_v1.pdf",
            "type": "application/pdf",
            "size": 2400000,
            "url": "/api/v1/files/12"
          }
        ],
        "createdBy": {
          "id": 8,
          "name": "Lê Thị Leader"
        },
        "createdAt": "2025-05-15T14:22:45Z"
      },
      {
        "id": 28,
        "content": "Đã trao đổi với khách hàng về yêu cầu ban đầu. Khách hàng quan tâm đến tính năng quản lý khách hàng và báo cáo thống kê.",
        "attachments": [],
        "createdBy": {
          "id": 5,
          "name": "Trần Văn Sales"
        },
        "createdAt": "2025-05-12T10:30:15Z"
      }
    ],
    "history": [
      {
        "id": 142,
        "type": "status_change",
        "field": "status",
        "oldValue": "qualified",
        "newValue": "proposal",
        "createdBy": {
          "id": 8,
          "name": "Lê Thị Leader"
        },
        "createdAt": "2025-05-15T14:22:45Z"
      },
      {
        "id": 135,
        "type": "assignment",
        "field": "assignedTo",
        "oldValue": null,
        "newValue": "Lê Thị Leader",
        "createdBy": {
          "id": 5,
          "name": "Trần Văn Sales"
        },
        "createdAt": "2025-05-11T08:45:20Z"
      },
      {
        "id": 129,
        "type": "creation",
        "field": "opportunity",
        "oldValue": null,
        "newValue": "created",
        "createdBy": {
          "id": 5,
          "name": "Trần Văn Sales"
        },
        "createdAt": "2025-05-10T09:15:32Z"
      }
    ],
    "suggestedResources": [
      {
        "id": 12,
        "name": "Hoàng Văn F",
        "position": "Senior Java Developer",
        "skills": ["Java", "Spring Boot", "Microservices"],
        "status": "Allocated",
        "availableFrom": "2025-08-01"
      },
      {
        "id": 25,
        "name": "Đỗ Thị K",
        "position": "UI/UX Designer",
        "skills": ["Figma", "Adobe XD", "Web Design"],
        "status": "Available",
        "availableFrom": "2025-06-01"
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
      "field": "includeNotes",
      "message": "Giá trị 'yes' không hợp lệ. Giá trị phải là true hoặc false"
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