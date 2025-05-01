# API Details: Import chi phí nhân viên hàng tháng

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn | Chuẩn hóa tài liệu API import chi phí nhân viên | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để import dữ liệu chi phí nhân viên hàng tháng từ file Excel hoặc CSV, hỗ trợ việc cập nhật nhanh và đồng bộ thông tin chi phí cho nhiều nhân viên, giúp tính toán chính xác margin và các báo cáo hiệu suất tài chính.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-MGN-003                                  |
| **Tên API**        | Import chi phí nhân viên hàng tháng từ file  |
| **Mô tả**          | API cho phép import chi phí của nhân viên từ file Excel hoặc CSV |
| **Module**         | Quản lý Hiệu suất & Margin                   |
| **Phương thức**    | `POST`                                       |
| **Endpoint**       | `/api/v1/margins/costs/import`               |
| **Quyền truy cập** | employee-cost:import                          |

---

## 3. Parameters

### 3.1 Header Parameters

| Tên             | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-----------------|--------------|----------|-------|
| `Authorization` | String       | Có       | Định dạng: `Bearer {token}` |
| `Content-Type`  | String       | Có       | Phải là `multipart/form-data` |

### 3.2 Form Data Parameters

| Tên         | Kiểu dữ liệu | Bắt buộc | Mô tả |
|-------------|--------------|----------|-------|
| `file`      | File         | Có       | File Excel (.xlsx) hoặc CSV (.csv) chứa dữ liệu chi phí nhân viên |
| `month`     | String       | Có       | Tháng cần import dữ liệu (định dạng: YYYY-MM) |
| `teamId`    | Integer      | Không    | ID của team (nếu là Leader và muốn import cho team cụ thể) |
| `overwrite` | Boolean      | Không    | Có ghi đè dữ liệu đã tồn tại không (mặc định: `false`) |

### 3.3 Validate Rule

| Trường         | Điều kiện hợp lệ |
|----------------|------------------|
| `file`         | - Định dạng phải là .xlsx hoặc .csv<br>- Dung lượng tối đa 5MB<br>- Cấu trúc file phải đúng theo template hệ thống |
| `month`        | - Định dạng: YYYY-MM<br>- Không được là tháng trong tương lai |
| `teamId`       | - Nếu cung cấp, phải là số nguyên dương và tồn tại trong hệ thống<br>- Người dùng phải có quyền quản lý team này |
| `overwrite`    | - Phải là giá trị boolean: `true` hoặc `false` |

### 3.4 Yêu cầu về cấu trúc file

1. **Định dạng cột trong file Excel/CSV**:
   - Cột A (bắt buộc): Mã nhân viên (Employee Code)
   - Cột B (bắt buộc): Tên nhân viên (Employee Name)
   - Cột C (bắt buộc): Chi phí cơ bản (Basic Cost) - VNĐ
   - Cột D (không bắt buộc): Chi phí phụ cấp (Allowance) - VNĐ
   - Cột E (không bắt buộc): Chi phí tăng ca (Overtime) - VNĐ
   - Cột F (không bắt buộc): Chi phí khác (Other Costs) - VNĐ

2. **Dòng đầu tiên phải là tiêu đề các cột**.

3. **Mã nhân viên phải tồn tại** trong hệ thống và thuộc quyền quản lý của người dùng hiện tại.

### 3.5 Phân quyền đặc biệt
- Leader: Chỉ import được chi phí của nhân viên trong team của mình
- Division Manager: Import được chi phí của tất cả nhân viên thuộc bộ phận mình quản lý
- Admin: Import được chi phí của tất cả nhân viên
- Người dùng thông thường không có quyền import chi phí

---

## 4. Response

### 4.1 Success - 200 OK

```json
{
  "status": "success",
  "code": 200,
  "data": {
    "importId": "IMPORT-20250531-123456",
    "month": "2025-05",
    "totalRecords": 32,
    "successCount": 30,
    "errorCount": 2,
    "summary": {
      "teamId": 1,
      "teamName": "Team Alpha",
      "totalEmployees": 30,
      "totalCost": 750000000,
      "averageCost": 25000000
    },
    "errors": [
      {
        "rowNumber": 12,
        "employeeCode": "NV099",
        "errorType": "NOT_FOUND",
        "message": "Không tìm thấy nhân viên với mã NV099"
      },
      {
        "rowNumber": 25,
        "employeeCode": "NV034",
        "errorType": "ACCESS_DENIED",
        "message": "Bạn không có quyền cập nhật chi phí cho nhân viên này"
      }
    ],
    "importedBy": {
      "id": 5,
      "name": "Nguyễn Văn Leader"
    },
    "importedAt": "2025-05-31T10:15:30Z"
  }
}
```

### 4.2 Error Responses

#### 400 Bad Request - Lỗi định dạng file
```json
{
  "status": "error",
  "code": "E2000",
  "message": "Tham số không hợp lệ",
  "errors": [
    {
      "field": "file",
      "message": "Định dạng file không hợp lệ. Chỉ chấp nhận file .xlsx hoặc .csv"
    }
  ]
}
```

#### 400 Bad Request - Lỗi cấu trúc file
```json
{
  "status": "error",
  "code": "E2001",
  "message": "Cấu trúc file không hợp lệ",
  "errors": [
    {
      "field": "file",
      "message": "Cột 'Mã nhân viên' bắt buộc nhưng không tìm thấy trong file"
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
  "message": "Bạn không có quyền thực hiện chức năng này"
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

#### 422 Unprocessable Entity - Không thể xử lý file
```json
{
  "status": "error",
  "code": "E3002",
  "message": "Không thể xử lý file",
  "errors": [
    {
      "message": "File bị hỏng hoặc không đọc được. Vui lòng kiểm tra lại."
    }
  ]
}
```

#### 500 Internal Server Error
```json
{
  "status": "error",
  "code": "E5000",
  "message": "Lỗi hệ thống khi xử lý file import"
}
``` 