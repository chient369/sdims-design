# API Details: Lấy chi tiết hợp đồng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API lấy chi tiết hợp đồng | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để lấy thông tin chi tiết của một hợp đồng, bao gồm các điều khoản thanh toán và danh sách nhân viên được gán vào dự án, giúp người dùng theo dõi tiến độ thực hiện, trạng thái thanh toán và phân bổ nguồn lực cho hợp đồng.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-CTR-003                                  |
| **Tên API**        | Lấy chi tiết hợp đồng                        |
| **Mô tả**          | API lấy thông tin chi tiết của một hợp đồng bao gồm các điều khoản thanh toán và nhân viên được gán |
| **Module**         | Quản lý Hợp đồng & Doanh thu                 |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/contracts/{contractId}`             |
| **Quyền truy cập** | contract:read                                |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Path Parameters

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `contractId`   | Integer      | Có       | ID của hợp đồng cần lấy thông tin |

### 3.3 Query Parameters

| Tên                    | Kiểu dữ liệu     | Bắt buộc | Mô tả |
|------------------------|------------------|----------|-------|
| `includePaymentTerms`  | Boolean          | Không    | Có kèm theo thông tin các đợt thanh toán không (mặc định: `true`) |
| `includeEmployees`     | Boolean          | Không    | Có kèm theo thông tin nhân viên tham gia không (mặc định: `true`) |
| `includeFiles`         | Boolean          | Không    | Có kèm theo thông tin file đính kèm không (mặc định: `true`) |

### 3.3 Validate Rule

| Trường               | Điều kiện hợp lệ |
|----------------------|------------------|
| `contractId`         | Số nguyên dương, phải tồn tại trong hệ thống |
| `includePaymentTerms`| Boolean: `true` hoặc `false` |
| `includeEmployees`   | Boolean: `true` hoặc `false` |
| `includeFiles`       | Boolean: `true` hoặc `false` |

### 3.4 Phân quyền đặc biệt
- Leader: Chỉ xem được các hợp đồng có liên kết với nhân viên thuộc team mình quản lý
- Sales: Xem được các hợp đồng do mình phụ trách
- Division Manager: Xem được tất cả hợp đồng thuộc bộ phận mình quản lý
- Kế toán: Xem được tất cả hợp đồng nhưng chỉ tập trung vào thông tin thanh toán
- Admin: Xem được tất cả hợp đồng

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "contract": {
      "id": 45,
      "contractCode": "CTR-2025-045",
      "name": "Hệ thống CRM cho Công ty ABC",
      "customer": {
        "id": 12,
        "name": "Công ty ABC"
      },
      "contractType": "FixedPrice",
      "amount": 1250000000,
      "signDate": "2025-05-15",
      "startDate": "2025-05-20",
      "endDate": "2025-11-20",
      "status": "InProgress",
      "salesPerson": {
        "id": 5,
        "name": "Trần Văn Sales"
      },
      "relatedOpportunity": {
        "id": 134,
        "code": "OPP-2025050134",
        "name": "Hệ thống CRM cho công ty ABC"
      },
      "description": "Hợp đồng phát triển hệ thống CRM bao gồm quản lý khách hàng, bán hàng và báo cáo",
      "paymentTerms": [
        {
          "id": 120,
          "termNumber": 1,
          "dueDate": "2025-05-25",
          "amount": 312500000,
          "description": "Tạm ứng 25% khi khởi động dự án",
          "status": "paid",
          "paidDate": "2025-05-23T00:00:00Z",
          "paidAmount": 312500000
        },
        {
          "id": 121,
          "termNumber": 2,
          "dueDate": "2025-07-20",
          "amount": 312500000,
          "description": "25% sau khi hoàn thành giai đoạn phân tích yêu cầu và thiết kế",
          "status": "unpaid",
          "paidDate": null,
          "paidAmount": 0
        },
        {
          "id": 122,
          "termNumber": 3,
          "dueDate": "2025-09-20",
          "amount": 312500000,
          "description": "25% sau khi hoàn thành phát triển và bàn giao cho UAT",
          "status": "unpaid",
          "paidDate": null,
          "paidAmount": 0
        },
        {
          "id": 123,
          "termNumber": 4,
          "dueDate": "2025-11-30",
          "amount": 312500000,
          "description": "25% sau khi hoàn thành dự án và nghiệm thu",
          "status": "unpaid",
          "paidDate": null,
          "paidAmount": 0
        }
      ],
      "employeeAssignments": [
        {
          "id": 230,
          "employee": {
            "id": 15,
            "name": "Nguyễn Văn A",
            "position": "Senior Developer",
            "team": {
              "id": 3,
              "name": "Team Alpha"
            }
          },
          "startDate": "2025-05-20",
          "endDate": "2025-11-20",
          "allocationPercentage": 100,
          "billRate": 25000000
        },
        {
          "id": 231,
          "employee": {
            "id": 22,
            "name": "Lê Thị B",
            "position": "Developer",
            "team": {
              "id": 3,
              "name": "Team Alpha"
            }
          },
          "startDate": "2025-05-20",
          "endDate": "2025-09-30",
          "allocationPercentage": 100,
          "billRate": 20000000
        },
        {
          "id": 232,
          "employee": {
            "id": 28,
            "name": "Phạm Văn C",
            "position": "Tester",
            "team": {
              "id": 4,
              "name": "Team Beta"
            }
          },
          "startDate": "2025-06-01",
          "endDate": "2025-11-20",
          "allocationPercentage": 50,
          "billRate": 18000000
        }
      ],
      "files": [
        {
          "id": 45,
          "name": "hop_dong_abc_signed.pdf",
          "type": "application/pdf",
          "size": 3256400,
          "uploadedAt": "2025-05-15T14:22:10Z",
          "uploadedBy": {
            "id": 5,
            "name": "Trần Văn Sales"
          },
          "url": "/api/v1/files/45"
        },
        {
          "id": 46,
          "name": "phu_luc_abc.pdf",
          "type": "application/pdf",
          "size": 1520300,
          "uploadedAt": "2025-05-15T14:22:35Z",
          "uploadedBy": {
            "id": 5,
            "name": "Trần Văn Sales"
          },
          "url": "/api/v1/files/46"
        }
      ],
      "paymentStatus": {
        "status": "partial",
        "totalTerms": 4,
        "paidTerms": 1,
        "remainingAmount": 937500000,
        "nextDueDate": "2025-07-20",
        "nextDueAmount": 312500000
      },
      "createdBy": {
        "id": 5,
        "name": "Trần Văn Sales"
      },
      "createdAt": "2025-05-15T11:30:45Z",
      "updatedBy": {
        "id": 3,
        "name": "Trưởng Phòng Lê"
      },
      "updatedAt": "2025-05-23T14:10:25Z"
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
      "field": "contractId",
      "message": "ID hợp đồng phải là số nguyên dương"
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
  "message": "Bạn không có quyền truy cập hợp đồng này"
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
      "field": "contractId",
      "message": "Không tìm thấy hợp đồng với ID: 999"
    }
  ]
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống khi truy vấn dữ liệu hợp đồng"
}
``` 