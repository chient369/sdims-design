# API Details: Tạo hợp đồng mới

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API tạo hợp đồng mới | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để tạo một hợp đồng mới trong hệ thống, bao gồm thông tin cơ bản, điều khoản thanh toán, danh sách các sản phẩm/dịch vụ cần bàn giao và các tài liệu đính kèm, giúp quản lý thông tin hợp đồng từ giai đoạn bắt đầu.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-CTR-001                                  |
| **Tên API**        | Tạo hợp đồng mới                             |
| **Mô tả**          | API cho phép tạo một hợp đồng mới với khách hàng, liên kết với cơ hội kinh doanh |
| **Module**         | Quản lý Hợp đồng & Doanh thu                 |
| **Phương thức**    | `POST`                                       |
| **Endpoint**       | `/api/v1/contracts`                          |
| **Quyền truy cập** | contract:create                              |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |
| `Content-Type`  | String       | Có       | Phải là `application/json` |

### 3.2 Request Body

```json
{
  "title": "Hợp đồng phát triển phần mềm XYZ",
  "contractCode": "CTR-2025-045",
  "customerId": 12,
  "opportunityId": 78,
  "value": 1200,
  "startDate": "2025-06-01",
  "endDate": "2026-05-31",
  "contractType": "Fixed",
  "contractStatus": "Draft",
  "description": "Hợp đồng phát triển hệ thống quản lý nhân sự cho Công ty ABC",
  "contactId": 34,
  "ownerId": 5,
  "paymentTerms": {
    "paymentMethod": "BankTransfer",
    "paymentDays": 30,
    "installments": 3,
    "currency": "VND",
    "vatIncluded": true,
    "vatRate": 10
  },
  "billings": [
    {
      "milestone": "Ký hợp đồng",
      "amount": 360,
      "percentage": 30,
      "dueDate": "2025-06-15",
      "description": "Thanh toán đợt 1 sau khi ký hợp đồng"
    },
    {
      "milestone": "Hoàn thành giai đoạn 1",
      "amount": 480,
      "percentage": 40,
      "dueDate": "2025-09-30",
      "description": "Thanh toán đợt 2 sau khi hoàn thành phân tích và thiết kế"
    },
    {
      "milestone": "Nghiệm thu",
      "amount": 360,
      "percentage": 30,
      "dueDate": "2025-12-31",
      "description": "Thanh toán đợt 3 sau khi nghiệm thu"
    }
  ],
  "deliverables": [
    {
      "name": "Phân hệ quản lý nhân viên",
      "quantity": 1,
      "unit": "Module",
      "unitPrice": 400,
      "totalPrice": 400,
      "description": "Module quản lý thông tin cơ bản của nhân viên"
    },
    {
      "name": "Phân hệ đánh giá hiệu suất",
      "quantity": 1,
      "unit": "Module",
      "unitPrice": 300,
      "totalPrice": 300,
      "description": "Module đánh giá KPI và hiệu suất nhân viên"
    },
    {
      "name": "Phân hệ quản lý đào tạo",
      "quantity": 1,
      "unit": "Module",
      "unitPrice": 250,
      "totalPrice": 250,
      "description": "Module quản lý đào tạo và phát triển nhân viên"
    },
    {
      "name": "Tích hợp hệ thống",
      "quantity": 1,
      "unit": "Gói",
      "unitPrice": 250,
      "totalPrice": 250,
      "description": "Tích hợp với các hệ thống khác của khách hàng"
    }
  ],
  "attachments": [
    {
      "name": "TOR.pdf",
      "fileUrl": "https://example.com/files/contracts/45/TOR.pdf",
      "fileType": "PDF",
      "fileSize": 2048,
      "uploadedBy": "Trần Văn A",
      "uploadedAt": "2025-05-30T10:15:30Z"
    }
  ],
  "tags": ["Software", "HR", "Enterprise"],
  "notes": "Khách hàng yêu cầu triển khai theo phương pháp Agile"
}
```

### 3.3 Body Parameters

| Tên                 | Kiểu dữ liệu | Bắt buộc | Mô tả |
|---------------------|--------------|----------|-------|
| `title`             | String       | Có       | Tiêu đề hợp đồng |
| `contractCode`      | String       | Có       | Mã hợp đồng |
| `customerId`        | Integer      | Có       | ID của khách hàng |
| `opportunityId`     | Integer      | Không    | ID của cơ hội kinh doanh liên quan (nếu có) |
| `value`             | Number       | Có       | Giá trị hợp đồng (đơn vị: triệu VND) |
| `startDate`         | String       | Có       | Ngày bắt đầu hợp đồng (định dạng: YYYY-MM-DD) |
| `endDate`           | String       | Có       | Ngày kết thúc hợp đồng (định dạng: YYYY-MM-DD) |
| `contractType`      | String       | Có       | Loại hợp đồng |
| `contractStatus`    | String       | Có       | Trạng thái hợp đồng |
| `description`       | String       | Không    | Mô tả chi tiết về hợp đồng |
| `contactId`         | Integer      | Có       | ID của người liên hệ chính từ phía khách hàng |
| `ownerId`           | Integer      | Có       | ID của người quản lý hợp đồng |
| `paymentTerms`      | Object       | Có       | Điều khoản thanh toán |
| `billings`          | Array        | Không    | Thông tin các đợt thanh toán |
| `deliverables`      | Array        | Không    | Các sản phẩm/dịch vụ cần bàn giao |
| `attachments`       | Array        | Không    | Các file đính kèm |
| `tags`              | Array        | Không    | Các thẻ gắn với hợp đồng |
| `notes`             | String       | Không    | Ghi chú bổ sung |

#### Chi tiết Object `paymentTerms`

| Tên               | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-------------------|--------------|----------|-------|
| `paymentMethod`   | String       | Có       | Phương thức thanh toán |
| `paymentDays`     | Integer      | Có       | Số ngày thanh toán sau khi xuất hóa đơn |
| `installments`    | Integer      | Không    | Số đợt thanh toán (nếu thanh toán nhiều đợt) |
| `currency`        | String       | Có       | Đơn vị tiền tệ (mặc định: `VND`) |
| `vatIncluded`     | Boolean      | Có       | Đã bao gồm VAT hay chưa |
| `vatRate`         | Number       | Không    | Thuế suất VAT (%) |

#### Chi tiết Object trong mảng `billings`

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `milestone`    | String       | Có       | Tên mốc thanh toán |
| `amount`       | Number       | Có       | Số tiền thanh toán (đơn vị: triệu VND) |
| `percentage`   | Number       | Có       | Phần trăm so với tổng giá trị hợp đồng |
| `dueDate`      | String       | Có       | Ngày đến hạn thanh toán (định dạng: YYYY-MM-DD) |
| `description`  | String       | Không    | Mô tả chi tiết về đợt thanh toán |

#### Chi tiết Object trong mảng `deliverables`

| Tên            | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------|--------------|----------|-------|
| `name`         | String       | Có       | Tên sản phẩm/dịch vụ |
| `quantity`     | Number       | Có       | Số lượng |
| `unit`         | String       | Không    | Đơn vị tính |
| `unitPrice`    | Number       | Có       | Đơn giá (đơn vị: triệu VND) |
| `totalPrice`   | Number       | Có       | Tổng giá trị (đơn vị: triệu VND) |
| `description`  | String       | Không    | Mô tả chi tiết |

#### Chi tiết Object trong mảng `attachments`

| Tên           | Kiểu dữ liệu | Bắt buộc | Mô tả |
|---------------|--------------|----------|-------|
| `name`        | String       | Có       | Tên file |
| `fileUrl`     | String       | Có       | URL của file |
| `fileType`    | String       | Có       | Loại file |
| `fileSize`    | Number       | Có       | Kích thước file (đơn vị: KB) |
| `uploadedBy`  | String       | Không    | Người tải lên |
| `uploadedAt`  | String       | Không    | Thời gian tải lên (định dạng: YYYY-MM-DD'T'HH:mm:ss'Z') |

### 3.3 Validate Rule

| Trường             | Điều kiện hợp lệ |
|--------------------|------------------|
| `title`            | Độ dài: 3-200 ký tự |
| `contractCode`     | Độ dài: 3-50 ký tự, phải là duy nhất |
| `customerId`       | Phải là ID của khách hàng hợp lệ trong hệ thống |
| `opportunityId`    | Nếu có, phải là ID của cơ hội hợp lệ trong hệ thống |
| `value`            | > 0 |
| `startDate`        | Định dạng ngày tháng hợp lệ (YYYY-MM-DD) |
| `endDate`          | Định dạng ngày tháng hợp lệ (YYYY-MM-DD), phải sau startDate |
| `contractType`     | Một trong: `Fixed`, `TimeAndMaterials`, `Retainer`, `Maintenance` |
| `contractStatus`   | Một trong: `Draft`, `InReview`, `Approved`, `Active`, `Completed`, `Terminated`, `Expired` |
| `contactId`        | Phải là ID của người liên hệ hợp lệ trong hệ thống |
| `ownerId`          | Phải là ID của nhân viên hợp lệ trong hệ thống |

### 3.4 Phân quyền đặc biệt
- Sales: Có thể tạo hợp đồng mới, nhưng chỉ được phân công làm Owner cho hợp đồng đó
- Division Manager: Có thể tạo hợp đồng mới và phân công bất kỳ Sales nào trong phòng làm Owner
- Admin: Có thể tạo hợp đồng mới và phân công bất kỳ người dùng nào trong hệ thống làm Owner

---

## 4. Response

### 4.1 Success - 201 Created

```json
{
  "status": "success",
  "code": 201,
  "message": "Tạo hợp đồng thành công",
  "data": {
    "id": 45,
    "title": "Hợp đồng phát triển phần mềm XYZ",
    "contractCode": "CTR-2025-045",
    "customer": {
      "id": 12,
      "name": "Công ty ABC",
      "type": "Corporate"
    },
    "opportunity": {
      "id": 78,
      "name": "Dự án phần mềm XYZ"
    },
    "value": 1200,
    "startDate": "2025-06-01",
    "endDate": "2026-05-31",
    "contractType": "Fixed",
    "contractStatus": "Draft",
    "description": "Hợp đồng phát triển hệ thống quản lý nhân sự cho Công ty ABC",
    "contact": {
      "id": 34,
      "name": "Nguyễn Văn B",
      "position": "IT Director",
      "email": "b.nguyenvan@abc-company.com",
      "phone": "0901234567"
    },
    "owner": {
      "id": 5,
      "name": "Trần Văn A",
      "email": "a.tranvan@company.com"
    },
    "paymentTerms": {
      "paymentMethod": "BankTransfer",
      "paymentDays": 30,
      "installments": 3,
      "currency": "VND",
      "vatIncluded": true,
      "vatRate": 10
    },
    "billings": [
      {
        "id": 1,
        "milestone": "Ký hợp đồng",
        "amount": 360,
        "percentage": 30,
        "dueDate": "2025-06-15",
        "description": "Thanh toán đợt 1 sau khi ký hợp đồng",
        "status": "Pending"
      },
      {
        "id": 2,
        "milestone": "Hoàn thành giai đoạn 1",
        "amount": 480,
        "percentage": 40,
        "dueDate": "2025-09-30",
        "description": "Thanh toán đợt 2 sau khi hoàn thành phân tích và thiết kế",
        "status": "Pending"
      },
      {
        "id": 3,
        "milestone": "Nghiệm thu",
        "amount": 360,
        "percentage": 30,
        "dueDate": "2025-12-31",
        "description": "Thanh toán đợt 3 sau khi nghiệm thu",
        "status": "Pending"
      }
    ],
    "deliverables": [
      {
        "id": 1,
        "name": "Phân hệ quản lý nhân viên",
        "quantity": 1,
        "unit": "Module",
        "unitPrice": 400,
        "totalPrice": 400,
        "description": "Module quản lý thông tin cơ bản của nhân viên"
      },
      {
        "id": 2,
        "name": "Phân hệ đánh giá hiệu suất",
        "quantity": 1,
        "unit": "Module",
        "unitPrice": 300,
        "totalPrice": 300,
        "description": "Module đánh giá KPI và hiệu suất nhân viên"
      },
      {
        "id": 3,
        "name": "Phân hệ quản lý đào tạo",
        "quantity": 1,
        "unit": "Module",
        "unitPrice": 250,
        "totalPrice": 250,
        "description": "Module quản lý đào tạo và phát triển nhân viên"
      },
      {
        "id": 4,
        "name": "Tích hợp hệ thống",
        "quantity": 1,
        "unit": "Gói",
        "unitPrice": 250,
        "totalPrice": 250,
        "description": "Tích hợp với các hệ thống khác của khách hàng"
      }
    ],
    "attachments": [
      {
        "id": 1,
        "name": "TOR.pdf",
        "fileUrl": "https://example.com/files/contracts/45/TOR.pdf",
        "fileType": "PDF",
        "fileSize": 2048,
        "uploadedBy": "Trần Văn A",
        "uploadedAt": "2025-05-30T10:15:30Z"
      }
    ],
    "tags": ["Software", "HR", "Enterprise"],
    "notes": "Khách hàng yêu cầu triển khai theo phương pháp Agile",
    "createdBy": {
      "id": 5,
      "name": "Trần Văn A"
    },
    "createdAt": "2025-05-30T09:45:20Z"
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
      "field": "value",
      "message": "Giá trị hợp đồng phải là số dương"
    },
    {
      "field": "endDate",
      "message": "Ngày kết thúc phải sau ngày bắt đầu"
    },
    {
      "field": "billings",
      "message": "Tổng giá trị các đợt thanh toán phải bằng giá trị hợp đồng"
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
  "message": "Bạn không có quyền tạo hợp đồng"
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
      "field": "customerId",
      "message": "Không tìm thấy khách hàng với ID: 999"
    }
  ]
}
```

#### 409 Conflict
```json
{
  "status": "error",
  "code": "E4000",
  "message": "Xung đột dữ liệu",
  "errors": [
    {
      "field": "contractCode",
      "message": "Mã hợp đồng CTR-2025-045 đã tồn tại"
    }
  ]
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống khi tạo dữ liệu hợp đồng"
}
``` 