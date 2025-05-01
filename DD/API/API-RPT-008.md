# API Details: Báo cáo tỷ lệ sử dụng nguồn lực

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API báo cáo tỷ lệ sử dụng nguồn lực | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để tạo báo cáo về tỷ lệ sử dụng nguồn lực nhân sự, giúp người quản lý theo dõi hiệu suất phân bổ nhân viên vào các dự án, phân tích tỷ lệ nhân viên đang làm việc trong các dự án so với tổng số nhân viên sẵn có, hỗ trợ việc tối ưu hóa việc sử dụng nguồn lực.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-RPT-008                                  |
| **Tên API**        | Báo cáo tỷ lệ sử dụng nguồn lực              |
| **Mô tả**          | API cung cấp báo cáo chi tiết về tỷ lệ sử dụng nguồn lực nhân sự |
| **Module**         | Dashboard & Báo cáo                          |
| **Phương thức**    | `GET`                                        |
| **Endpoint**       | `/api/v1/reports/utilization`                |
| **Quyền truy cập** | report:read:all, report:read:team            |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |

### 3.2 Query Parameters

| Tên                | Kiểu dữ liệu         | Bắt buộc | Mô tả |
|--------------------|----------------------|----------|-------|
| `teamId`           | Integer              | Không    | ID của team cần lọc |
| `leaderId`         | Integer              | Không    | ID của leader cần lọc |
| `employeeId`       | Integer              | Không    | ID của nhân viên cụ thể cần lọc |
| `positionId`       | Integer              | Không    | ID của vị trí/chức danh cần lọc |
| `skillId`          | Integer              | Không    | ID của kỹ năng cần lọc |
| `status`           | String               | Không    | Trạng thái nhân viên: `Allocated`, `PartiallyAllocated`, `Available`, `EndingSoon` |
| `minUtilization`   | Double               | Không    | Tỷ lệ % sử dụng tối thiểu cần lọc |
| `maxUtilization`   | Double               | Không    | Tỷ lệ % sử dụng tối đa cần lọc |
| `periodType`       | String               | Không    | Loại kỳ báo cáo: `currentMonth`, `nextMonth`, `custom` (mặc định: `currentMonth`) |
| `fromDate`         | Date (yyyy-MM-dd)    | Không    | Từ ngày (bắt buộc nếu periodType=`custom`) |
| `toDate`           | Date (yyyy-MM-dd)    | Không    | Đến ngày (bắt buộc nếu periodType=`custom`) |
| `groupBy`          | String               | Không    | Nhóm theo: `team`, `leader`, `position`, `skill` (mặc định: `team`) |
| `includeDetails`   | Boolean              | Không    | Bao gồm chi tiết phân bổ dự án (mặc định: `true`) |
| `exportType`       | String               | Không    | Loại xuất báo cáo: `json`, `csv`, `excel` (mặc định: `json`) |
| `page`             | Integer              | Không    | Trang cần lấy (mặc định: `1`) |
| `size`             | Integer              | Không    | Số bản ghi mỗi trang (mặc định: `20`) |
| `sortBy`           | String               | Không    | Trường sắp xếp (mặc định: `utilizationRate`) |
| `sortDir`          | String               | Không    | Hướng sắp xếp: `asc` hoặc `desc` (mặc định: `desc`) |

### 3.3 Validate Rule
| Trường            | Điều kiện hợp lệ |
|-------------------|------------------|
| `page`            | ≥ 1              |
| `size`            | 1 → 100          |
| `sortDir`         | `asc`, `desc`    |
| `status`          | Một trong: `Allocated`, `PartiallyAllocated`, `Available`, `EndingSoon` |
| `periodType`      | Một trong: `currentMonth`, `nextMonth`, `custom` |
| `groupBy`         | Một trong: `team`, `leader`, `position`, `skill` |
| `fromDate`        | Định dạng yyyy-MM-dd, ≤ toDate |
| `toDate`          | Định dạng yyyy-MM-dd, ≥ fromDate |
| `minUtilization`  | 0 → 100          |
| `maxUtilization`  | 0 → 100, ≥ minUtilization (nếu có) |
| `exportType`      | Một trong: `json`, `csv`, `excel` |

### 3.4 Phân quyền đặc biệt
- Leader: Chỉ xem được báo cáo sử dụng nguồn lực của team mình quản lý
- Division Manager: Xem được báo cáo sử dụng nguồn lực của tất cả team thuộc bộ phận mình quản lý
- Admin: Xem được báo cáo sử dụng nguồn lực của tất cả team

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "reportInfo": {
      "reportName": "Báo cáo tỷ lệ sử dụng nguồn lực",
      "generatedAt": "2025-05-01T17:30:15Z",
      "period": {
        "type": "currentMonth",
        "fromDate": "2025-05-01",
        "toDate": "2025-05-31",
        "description": "Tháng 5/2025"
      },
      "filters": {
        "groupBy": "team"
      }
    },
    "summaryMetrics": {
      "totalEmployees": 50,
      "fullyAllocated": 30,
      "partiallyAllocated": 10,
      "available": 8,
      "endingSoon": 2,
      "averageUtilization": 78.5,
      "byUtilizationRange": {
        "0%": 8,
        "1-25%": 2,
        "26-50%": 5,
        "51-75%": 10,
        "76-99%": 5,
        "100%": 20
      }
    },
    "groups": [
      {
        "name": "Team Alpha",
        "id": 1,
        "totalEmployees": 15,
        "averageUtilization": 85.3,
        "fullyAllocated": 10,
        "partiallyAllocated": 3,
        "available": 2,
        "endingSoon": 0
      },
      {
        "name": "Team Beta",
        "id": 2,
        "totalEmployees": 12,
        "averageUtilization": 75.8,
        "fullyAllocated": 8,
        "partiallyAllocated": 2,
        "available": 1,
        "endingSoon": 1
      },
      {
        "name": "Team Gamma",
        "id": 3,
        "totalEmployees": 10,
        "averageUtilization": 90.0,
        "fullyAllocated": 9,
        "partiallyAllocated": 1,
        "available": 0,
        "endingSoon": 0
      }
    ],
    "content": [
      {
        "employee": {
          "id": 101,
          "code": "NV001",
          "name": "Nguyễn Văn A",
          "position": "Senior Developer",
          "team": {
            "id": 1,
            "name": "Team Alpha"
          },
          "leader": {
            "id": 201,
            "name": "Lê Văn Leader"
          }
        },
        "utilizationInfo": {
          "utilizationRate": 100,
          "status": "Allocated",
          "contracts": [
            {
              "id": 301,
              "code": "HD-2025-001",
              "name": "Phát triển phần mềm quản lý nhân sự",
              "customer": "ABC Corporation",
              "allocation": 100,
              "fromDate": "2025-01-15",
              "toDate": "2025-12-31",
              "role": "Lead Developer"
            }
          ]
        }
      },
      {
        "employee": {
          "id": 102,
          "code": "NV002",
          "name": "Trần Thị B",
          "position": "Developer",
          "team": {
            "id": 1,
            "name": "Team Alpha"
          },
          "leader": {
            "id": 201,
            "name": "Lê Văn Leader"
          }
        },
        "utilizationInfo": {
          "utilizationRate": 80,
          "status": "PartiallyAllocated",
          "contracts": [
            {
              "id": 302,
              "code": "HD-2025-002",
              "name": "Bảo trì hệ thống kế toán",
              "customer": "XYZ Inc",
              "allocation": 50,
              "fromDate": "2025-02-01",
              "toDate": "2026-01-31",
              "role": "Developer"
            },
            {
              "id": 305,
              "code": "HD-2025-005",
              "name": "Phát triển ứng dụng di động",
              "customer": "PQR Corp",
              "allocation": 30,
              "fromDate": "2025-04-01",
              "toDate": "2025-07-31",
              "role": "Mobile Developer"
            }
          ]
        }
      },
      {
        "employee": {
          "id": 103,
          "code": "NV003",
          "name": "Phạm Văn C",
          "position": "Tester",
          "team": {
            "id": 2,
            "name": "Team Beta"
          },
          "leader": {
            "id": 202,
            "name": "Trần Thị Manager"
          }
        },
        "utilizationInfo": {
          "utilizationRate": 50,
          "status": "PartiallyAllocated",
          "contracts": [
            {
              "id": 310,
              "code": "HD-2025-010",
              "name": "Nâng cấp hệ thống ERP",
              "customer": "MNO Group",
              "allocation": 50,
              "fromDate": "2025-03-15",
              "toDate": "2025-06-30",
              "role": "QA Engineer"
            }
          ]
        }
      },
      {
        "employee": {
          "id": 104,
          "code": "NV004",
          "name": "Hoàng Thị D",
          "position": "BA",
          "team": {
            "id": 2,
            "name": "Team Beta"
          },
          "leader": {
            "id": 202,
            "name": "Trần Thị Manager"
          }
        },
        "utilizationInfo": {
          "utilizationRate": 0,
          "status": "Available",
          "contracts": []
        }
      },
      {
        "employee": {
          "id": 105,
          "code": "NV005",
          "name": "Lê Văn E",
          "position": "Developer",
          "team": {
            "id": 1,
            "name": "Team Alpha"
          },
          "leader": {
            "id": 201,
            "name": "Lê Văn Leader"
          }
        },
        "utilizationInfo": {
          "utilizationRate": 100,
          "status": "EndingSoon",
          "contracts": [
            {
              "id": 315,
              "code": "HD-2025-015",
              "name": "Xây dựng website bán hàng",
              "customer": "RST Company",
              "allocation": 100,
              "fromDate": "2025-02-15",
              "toDate": "2025-05-15",
              "role": "Front-end Developer"
            }
          ]
        }
      }
    ],
    "paging": {
      "page": 1,
      "size": 20,
      "totalElements": 50,
      "totalPages": 3
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
      "field": "periodType",
      "message": "Loại kỳ báo cáo không hợp lệ"
    },
    {
      "field": "fromDate",
      "message": "Từ ngày là bắt buộc khi periodType là 'custom'"
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