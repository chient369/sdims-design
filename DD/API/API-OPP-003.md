# API Details: Kích hoạt đồng bộ thủ công từ Hubspot

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-01 | Chiến Trần Văn |Tạo mới | -           | Draft     |

---

## 1. Mục tiêu  
Cung cấp API để Admin hoặc Trưởng bộ phận có thể kích hoạt thủ công quá trình đồng bộ dữ liệu cơ hội kinh doanh từ Hubspot vào hệ thống, với các tùy chọn về chế độ và phạm vi đồng bộ.

---

## 2. Overview

| Thuộc tính         | Giá trị                                      |
|--------------------|----------------------------------------------|
| **API Code**       | API-OPP-003                                  |
| **Tên API**        | Kích hoạt đồng bộ thủ công từ Hubspot        |
| **Mô tả**          | API cho phép kích hoạt thủ công việc đồng bộ dữ liệu cơ hội từ Hubspot |
| **Module**         | Quản lý Cơ hội Kinh doanh                    |
| **Phương thức**    | `POST`                                       |
| **Endpoint**       | `/api/v1/opportunities/sync`                 |
| **Quyền truy cập** | opportunities:sync                           |

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
  "syncMode": "incremental",
  "fromDate": "2025-05-01",
  "toDate": "2025-05-31",
  "dealStage": "all",
  "overwriteExisting": false
}
```

### 3.3 Body Parameters

| Tên                  | Kiểu dữ liệu | Bắt buộc | Mô tả |
|----------------------|--------------|----------|-------|
| `syncMode`           | String       | Không    | Chế độ đồng bộ: `incremental` (mặc định) hoặc `full` |
| `fromDate`           | String       | Không    | Ngày bắt đầu khoảng thời gian cần đồng bộ (định dạng: YYYY-MM-DD) |
| `toDate`             | String       | Không    | Ngày kết thúc khoảng thời gian cần đồng bộ (định dạng: YYYY-MM-DD) |
| `dealStage`          | String       | Không    | Trạng thái deal cần đồng bộ: `all` (mặc định), `open`, `closed_won`, `closed_lost` |
| `overwriteExisting`  | Boolean      | Không    | Có ghi đè lên dữ liệu đã tồn tại không (mặc định: `false`) |

### 3.4 Validate Rule

| Trường               | Điều kiện hợp lệ |
|----------------------|------------------|
| `syncMode`           | Một trong: `incremental`, `full` |
| `fromDate`           | - Định dạng: YYYY-MM-DD<br>- Không được là ngày trong tương lai<br>- Tối đa 365 ngày trước ngày hiện tại |
| `toDate`             | - Định dạng: YYYY-MM-DD<br>- Không được là ngày trong tương lai<br>- Phải sau hoặc bằng `fromDate` (nếu cả hai được cung cấp) |
| `dealStage`          | Một trong: `all`, `open`, `closed_won`, `closed_lost` |
| `overwriteExisting`  | Boolean: `true` hoặc `false` |

### 3.5 Phân quyền đặc biệt
- Chỉ người dùng có quyền `opportunities:sync` mới được kích hoạt đồng bộ
- Theo ma trận CRUD, chỉ Admin và Division Manager có quyền này
- Việc đồng bộ có thể tạo tải cho hệ thống, nên cần giới hạn tần suất sử dụng

---

## 4. Response

### 4.1 Success - 202 Accepted

```json
{
  "status": "success",
  "code": 202,
  "data": {
    "syncId": "SYNC-20250531-123456",
    "message": "Quá trình đồng bộ đã được khởi tạo",
    "status": "queued",
    "estimatedTime": 120,
    "syncParams": {
      "syncMode": "incremental",
      "fromDate": "2025-05-01",
      "toDate": "2025-05-31",
      "dealStage": "all",
      "overwriteExisting": false
    },
    "logsUrl": "/api/v1/opportunities/sync/logs"
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
      "field": "syncMode",
      "message": "Chế độ đồng bộ 'partial' không hợp lệ. Các giá trị hợp lệ: incremental, full"
    },
    {
      "field": "fromDate",
      "message": "Định dạng ngày không hợp lệ. Yêu cầu định dạng YYYY-MM-DD"
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

#### 409 Conflict
```json
{
  "status": "error",
  "code": "E4004",
  "message": "Xung đột dữ liệu",
  "errors": [
    {
      "field": "sync",
      "message": "Đã có tiến trình đồng bộ đang chạy. Vui lòng đợi tiến trình hiện tại hoàn thành."
    }
  ],
  "data": {
    "syncId": "SYNC-20250531-123455",
    "status": "processing",
    "startedAt": "2025-05-31T14:50:22Z",
    "estimatedCompletion": "2025-05-31T14:52:22Z"
  }
}
```

#### 502 Bad Gateway
```json
{
  "status": "error",
  "code": "E5002",
  "message": "Lỗi đồng bộ dữ liệu Hubspot",
  "errors": [
    {
      "field": "hubspot",
      "message": "Dịch vụ Hubspot hiện không khả dụng. Vui lòng thử lại sau."
    }
  ]
}
``` 