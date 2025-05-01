# API Details: Báo cáo chi tiết danh sách hợp đồng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API báo cáo chi tiết danh sách hợp đồng | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để tạo báo cáo chi tiết về danh sách hợp đồng và tình trạng thực hiện, cho phép người dùng lọc và truy vấn thông tin hợp đồng theo nhiều tiêu chí khác nhau, hỗ trợ việc phân tích doanh thu, quản lý trạng thái thanh toán và theo dõi hiệu quả kinh doanh từ các hợp đồng.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-RPT-005                                  |
| **Tên API**        | Báo cáo chi tiết danh sách hợp đồng           |
| **Mô tả**          | API cung cấp báo cáo chi tiết về danh sách hợp đồng và tình trạng thực hiện |
| **Module**         | Dashboard & Báo cáo                          |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/reports/contract-list`              |
| **Quyền truy cập** | report:read:all, report:read:team, report:read:own |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên                | Kiểu dữ liệu         | Bắt buộc | Mô tả |
|--------------------|----------------------|----------|-------|
| `customerId`       | Integer              | Không    | ID của khách hàng cần lọc |
| `salesId`          | Integer              | Không    | ID của nhân viên Sales phụ trách |
| `status`           | String               | Không    | Trạng thái hợp đồng: `New`, `InProgress`, `Paused`, `Completed`, `Cancelled` |
| `type`             | String               | Không    | Loại hợp đồng: `FixedPrice`, `TimeAndMaterial` |
| `opportunityId`    | Integer              | Không    | ID của cơ hội liên kết |
| `minValue`         | Double               | Không    | Giá trị hợp đồng tối thiểu |
| `maxValue`         | Double               | Không    | Giá trị hợp đồng tối đa |
| `fromDate`         | Date (yyyy-MM-dd)    | Không    | Ngày ký hợp đồng từ (mặc định: 1 năm trước) |
| `toDate`           | Date (yyyy-MM-dd)    | Không    | Ngày ký hợp đồng đến (mặc định: hiện tại) |
| `expiryFromDate`   | Date (yyyy-MM-dd)    | Không    | Ngày hết hạn từ |
| `expiryToDate`     | Date (yyyy-MM-dd)    | Không    | Ngày hết hạn đến |
| `paymentStatus`    | String               | Không    | Trạng thái thanh toán: `Pending`, `PartiallyPaid`, `FullyPaid`, `Overdue` |
| `keyword`          | String               | Không    | Tìm kiếm theo tên hợp đồng hoặc mã |
| `includePayments`  | Boolean              | Không    | Bao gồm thông tin thanh toán trong kết quả (mặc định: `true`) |
| `includeEmployees` | Boolean              | Không    | Bao gồm thông tin nhân viên được gán (mặc định: `true`) |
| `exportType`       | String               | Không    | Loại xuất báo cáo: `json`, `csv`, `excel` (mặc định: `json`) |
| `page`             | Integer              | Không    | Trang cần lấy (mặc định: `1`) |
| `size`             | Integer              | Không    | Số bản ghi mỗi trang (mặc định: `50`) |
| `sortBy`           | String               | Không    | Trường sắp xếp (mặc định: `signedDate`) |
| `sortDir`          | String               | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |


### 3.3 Validate Rule
| Trường            | Điều kiện hợp lệ |
|-------------------|------------------|
| `page`            | ≥ 1              |
| `size`            | 1 → 500          |
| `sortDir`         | `asc`, `desc`    |
| `status`          | Một trong: `New`, `InProgress`, `Paused`, `Completed`, `Cancelled` |
| `type`            | Một trong: `FixedPrice`, `TimeAndMaterial` |
| `paymentStatus`   | Một trong: `Pending`, `PartiallyPaid`, `FullyPaid`, `Overdue` |
| `exportType`      | Một trong: `json`, `csv`, `excel` |
| `fromDate`        | Định dạng yyyy-MM-dd, ≤ toDate |
| `toDate`          | Định dạng yyyy-MM-dd, ≥ fromDate |
| `expiryFromDate`  | Định dạng yyyy-MM-dd, ≤ expiryToDate |
| `expiryToDate`    | Định dạng yyyy-MM-dd, ≥ expiryFromDate |
| `minValue`        | ≥ 0, ≤ maxValue (nếu có) |
| `maxValue`        | ≥ 0, ≥ minValue (nếu có) |

### 3.4 Phân quyền đặc biệt
- Leader: Chỉ xem được báo cáo hợp đồng có nhân viên thuộc team mình quản lý
- Sales: Chỉ xem được báo cáo của các hợp đồng do mình phụ trách
- Division Manager: Xem được báo cáo của tất cả hợp đồng thuộc bộ phận mình quản lý
- Kế toán: Xem được báo cáo của tất cả hợp đồng
- Admin: Xem được báo cáo của tất cả hợp đồng

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "reportInfo": {
      "reportName": "Báo cáo danh sách hợp đồng",
      "generatedAt": "2025-05-01T11:45:30Z",
      "fromDate": "2024-05-01",
      "toDate": "2025-04-30",
      "filters": {
        "status": "InProgress",
        "includePayments": true
      }
    },
    "summaryMetrics": {
      "totalContracts": 85,
      "byStatus": {
        "New": 15,
        "InProgress": 45,
        "Paused": 5,
        "Completed": 15,
        "Cancelled": 5
      },
      "byType": {
        "FixedPrice": 35,
        "TimeAndMaterial": 50
      },
      "byPaymentStatus": {
        "Pending": 20,
        "PartiallyPaid": 40,
        "FullyPaid": 20,
        "Overdue": 5
      },
      "totalValue": 12500000000,
      "totalPaid": 7500000000,
      "totalPending": 5000000000,
      "byCustomer": [
        {"name": "ABC Corporation", "count": 10, "value": 2500000000},
        {"name": "XYZ Inc", "count": 8, "value": 2000000000},
        {"name": "DEF Limited", "count": 6, "value": 1800000000}
      ],
      "bySales": [
        {"name": "Nguyễn Văn X", "count": 25, "value": 5000000000},
        {"name": "Trần Thị Y", "count": 20, "value": 4000000000},
        {"name": "Lê Anh Z", "count": 15, "value": 3500000000}
      ]
    },
    "content": [
      {
        "id": 101,
        "code": "HD-2025-001",
        "name": "Phát triển phần mềm quản lý nhân sự",
        "customer": {
          "id": 15,
          "name": "ABC Corporation",
          "industry": "Manufacturing"
        },
        "opportunity": {
          "id": 50,
          "name": "ABC Corp - Hệ thống quản lý nhân sự"
        },
        "type": "FixedPrice",
        "value": 500000000,
        "status": "InProgress",
        "signedDate": "2025-01-15",
        "effectiveDate": "2025-01-20",
        "expiryDate": "2025-12-31",
        "sales": {
          "id": 20,
          "name": "Nguyễn Văn X",
          "email": "x.nguyenvan@company.com"
        },
        "paymentTerms": [
          {
            "id": 201,
            "description": "Thanh toán đợt 1 (30%)",
            "amount": 150000000,
            "dueDate": "2025-02-15",
            "actualPaidDate": "2025-02-10",
            "status": "Paid"
          },
          {
            "id": 202,
            "description": "Thanh toán đợt 2 (30%)",
            "amount": 150000000,
            "dueDate": "2025-06-15",
            "actualPaidDate": "2025-06-20",
            "status": "Paid"
          },
          {
            "id": 203,
            "description": "Thanh toán đợt 3 (40%)",
            "amount": 200000000,
            "dueDate": "2025-12-15",
            "actualPaidDate": null,
            "status": "Pending"
          }
        ],
        "paymentSummary": {
          "totalAmount": 500000000,
          "paidAmount": 300000000,
          "pendingAmount": 200000000,
          "percentPaid": 60,
          "status": "PartiallyPaid"
        },
        "employees": [
          {
            "id": 1,
            "name": "Nguyễn Văn A",
            "position": "Developer",
            "allocation": 100
          },
          {
            "id": 2,
            "name": "Lê Thị C",
            "position": "Developer",
            "allocation": 100
          },
          {
            "id": 5,
            "name": "Trần Văn B",
            "position": "Leader",
            "allocation": 50
          }
        ]
      },
      {
        "id": 102,
        "code": "HD-2025-002",
        "name": "Bảo trì hệ thống kế toán",
        "customer": {
          "id": 16,
          "name": "XYZ Inc",
          "industry": "Finance"
        },
        "opportunity": {
          "id": 51,
          "name": "XYZ Inc - Bảo trì kế toán 2025"
        },
        "type": "TimeAndMaterial",
        "value": 320000000,
        "status": "InProgress",
        "signedDate": "2025-01-30",
        "effectiveDate": "2025-02-01",
        "expiryDate": "2026-01-31",
        "sales": {
          "id": 21,
          "name": "Trần Thị Y",
          "email": "y.tranthi@company.com"
        },
        "paymentTerms": [
          {
            "id": 205,
            "description": "Thanh toán tháng 2/2025",
            "amount": 80000000,
            "dueDate": "2025-03-15",
            "actualPaidDate": "2025-03-12",
            "status": "Paid"
          },
          {
            "id": 206,
            "description": "Thanh toán tháng 3/2025",
            "amount": 80000000,
            "dueDate": "2025-04-15",
            "actualPaidDate": "2025-04-18",
            "status": "Paid"
          },
          {
            "id": 207,
            "description": "Thanh toán tháng 4/2025",
            "amount": 80000000,
            "dueDate": "2025-05-15",
            "actualPaidDate": null,
            "status": "Pending"
          },
          {
            "id": 208,
            "description": "Thanh toán tháng 5/2025",
            "amount": 80000000,
            "dueDate": "2025-06-15",
            "actualPaidDate": null,
            "status": "Pending"
          }
        ],
        "paymentSummary": {
          "totalAmount": 320000000,
          "paidAmount": 160000000,
          "pendingAmount": 160000000,
          "percentPaid": 50,
          "status": "PartiallyPaid"
        },
        "employees": [
          {
            "id": 8,
            "name": "Phạm Văn D",
            "position": "Developer",
            "allocation": 50
          },
          {
            "id": 10,
            "name": "Hoàng Thị E",
            "position": "Tester",
            "allocation": 30
          }
        ]
      }
    ],
    "paging": {
      "page": 1,
      "size": 50,
      "totalElements": 85,
      "totalPages": 2
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
      "field": "status",
      "message": "Trạng thái hợp đồng không hợp lệ"
    },
    {
      "field": "fromDate",
      "message": "Định dạng ngày không hợp lệ"
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
  "message": "Bạn không có quyền truy cập báo cáo này"
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống khi tạo báo cáo"
}
``` 