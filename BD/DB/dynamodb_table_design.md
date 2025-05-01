**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2023-08-20 | Chiến Trần Văn | Thiết kế bảng DynamoDB cho hệ thống | -           | Draft     |

---

## 1. Mục tiêu  
Đề xuất mô hình thiết kế bảng DynamoDB cụ thể cho hệ thống quản lý nhân sự, dự án và cơ hội kinh doanh.

## 2. Thiết kế Single-Table

### 2.1 Nguyên tắc thiết kế

DynamoDB hoạt động hiệu quả nhất với thiết kế single-table, trong đó:

1. **Tất cả dữ liệu trong một bảng**: Hầu hết entity được lưu trong một bảng duy nhất
2. **Sử dụng prefix trong key**: Phân biệt loại entity qua prefix trong partition và sort key
3. **Overloaded indexes**: Sử dụng các GSI (Global Secondary Index) để hỗ trợ nhiều mẫu truy vấn
4. **Phi chuẩn hóa có kiểm soát**: Sao chép dữ liệu có chọn lọc để tránh joins

### 2.2 Cấu trúc khóa chính

```
MainTable:
- PK (Partition Key): EntityType#ID
- SK (Sort Key): Context#Value
```

### 2.3 Các Global Secondary Index (GSI) chính

```
GSI1:
- PK: SK (từ bảng chính)
- SK: PK (từ bảng chính)

GSI2:
- PK: GSI2PK (trường tùy chỉnh)
- SK: GSI2SK (trường tùy chỉnh)

GSI3:
- PK: GSI3PK (trường tùy chỉnh)
- SK: GSI3SK (trường tùy chỉnh)
```

## 3. Ánh xạ Entity sang Items

### 3.1 User và Employee

**User Item:**
```json
{
  "PK": "USER#12345",
  "SK": "METADATA",
  "username": "john.doe",
  "email": "john.doe@example.com",
  "fullName": "John Doe",
  "passwordHash": "hashed_password",
  "createdAt": "2023-01-15T10:30:00Z",
  "updatedAt": "2023-05-20T14:45:00Z",
  "GSI1PK": "EMAIL#john.doe@example.com",
  "GSI1SK": "USER#12345",
  "GSI2PK": "USERNAME#john.doe",
  "GSI2SK": "USER#12345"
}
```

**Employee Item:**
```json
{
  "PK": "USER#12345",
  "SK": "EMPLOYEE#98765",
  "employeeCode": "EMP-123",
  "firstName": "John",
  "lastName": "Doe",
  "position": "Senior Developer",
  "team": "Engineering",
  "companyEmail": "john.doe@company.com",
  "currentStatus": "Allocated",
  "statusUpdatedAt": "2023-06-01T09:00:00Z",
  "reportingLeaderId": "USER#54321",
  "GSI1PK": "EMPLOYEE#98765",
  "GSI1SK": "METADATA",
  "GSI2PK": "TEAM#Engineering",
  "GSI2SK": "EMPLOYEE#98765",
  "GSI3PK": "STATUS#Allocated",
  "GSI3SK": "EMPLOYEE#98765"
}
```

**User Roles:**
```json
{
  "PK": "USER#12345",
  "SK": "ROLE#7890",
  "roleName": "Developer",
  "assignedAt": "2023-01-15T10:30:00Z",
  "GSI1PK": "ROLE#7890",
  "GSI1SK": "USER#12345"
}
```

### 3.2 Skills và Employee Skills

**Skill:**
```json
{
  "PK": "SKILL#5678",
  "SK": "METADATA",
  "name": "Java",
  "categoryId": "CATEGORY#123",
  "categoryName": "Programming Language",
  "description": "Java programming language",
  "GSI1PK": "CATEGORY#123",
  "GSI1SK": "SKILL#5678"
}
```

**Employee Skill:**
```json
{
  "PK": "EMPLOYEE#98765",
  "SK": "SKILL#5678",
  "skillName": "Java",
  "yearsExperience": 5,
  "selfAssessmentLevel": "Advanced",
  "leaderAssessmentLevel": "Advanced",
  "GSI1PK": "SKILL#5678",
  "GSI1SK": "EMPLOYEE#98765",
  "GSI2PK": "SKILLLEVEL#Advanced",
  "GSI2SK": "EMPLOYEE#98765#SKILL#5678"
}
```

### 3.3 Contracts và Relationships

**Contract:**
```json
{
  "PK": "CONTRACT#4567",
  "SK": "METADATA",
  "contractCode": "CONT-2023-01",
  "name": "Enterprise Web Application",
  "clientName": "ABC Corporation",
  "effectiveDate": "2023-01-01",
  "expiryDate": "2023-12-31",
  "status": "Ongoing",
  "totalValue": 150000,
  "currency": "USD",
  "assignedSalesId": "USER#9876",
  "GSI1PK": "CLIENT#ABC Corporation",
  "GSI1SK": "CONTRACT#4567",
  "GSI2PK": "STATUS#Ongoing",
  "GSI2SK": "CONTRACT#4567",
  "GSI3PK": "SALESUSER#9876",
  "GSI3SK": "CONTRACT#4567"
}
```

**Contract Employee:**
```json
{
  "PK": "CONTRACT#4567",
  "SK": "EMPLOYEE#98765",
  "employeeName": "John Doe",
  "role": "Lead Developer",
  "allocationPercentage": 75,
  "startDate": "2023-01-15",
  "endDate": "2023-10-31",
  "GSI1PK": "EMPLOYEE#98765",
  "GSI1SK": "CONTRACT#4567",
  "GSI2PK": "YEAR#2023#MONTH#01",
  "GSI2SK": "CONTRACT#4567#EMPLOYEE#98765"
}
```

**Monthly Revenue Calculation:**
```json
{
  "PK": "EMPLOYEE#98765",
  "SK": "REVENUE#2023-06",
  "contractId": "CONTRACT#4567",
  "contractName": "Enterprise Web Application",
  "year": 2023,
  "month": 6,
  "billingRate": 100,
  "allocationPercentage": 75,
  "calculatedRevenue": 12375,
  "currency": "USD",
  "GSI1PK": "CONTRACT#4567",
  "GSI1SK": "REVENUE#2023-06#EMPLOYEE#98765",
  "GSI2PK": "YEAR#2023#MONTH#06",
  "GSI2SK": "REVENUE#EMPLOYEE#98765"
}
```

### 3.4 Opportunities

**Opportunity:**
```json
{
  "PK": "OPPORTUNITY#7890",
  "SK": "METADATA",
  "hubspotId": "HS-12345",
  "name": "Cloud Migration Project",
  "clientName": "XYZ Industries",
  "estimatedValue": 200000,
  "currency": "USD",
  "dealStage": "Proposal",
  "assignedSalesId": "USER#9876",
  "lastInteractionDate": "2023-07-01T15:30:00Z",
  "followUpStatus": "Green",
  "GSI1PK": "CLIENT#XYZ Industries",
  "GSI1SK": "OPPORTUNITY#7890",
  "GSI2PK": "DEALSTAGE#Proposal",
  "GSI2SK": "OPPORTUNITY#7890",
  "GSI3PK": "SALESUSER#9876",
  "GSI3SK": "OPPORTUNITY#7890"
}
```

**Opportunity Assignment:**
```json
{
  "PK": "OPPORTUNITY#7890",
  "SK": "LEADER#54321",
  "userName": "Jane Smith",
  "assignedAt": "2023-06-15T09:00:00Z",
  "GSI1PK": "LEADER#54321",
  "GSI1SK": "OPPORTUNITY#7890"
}
```

### 3.5 Notifications

```json
{
  "PK": "USER#12345",
  "SK": "NOTIFICATION#1001",
  "message": "You've been assigned to a new contract",
  "isRead": false,
  "relatedEntityType": "CONTRACT",
  "relatedEntityId": "CONTRACT#4567",
  "createdAt": "2023-07-10T08:15:00Z",
  "GSI1PK": "NOTIFICATION#1001",
  "GSI1SK": "USER#12345",
  "GSI2PK": "NOTIFICATION#UNREAD",
  "GSI2SK": "USER#12345#2023-07-10T08:15:00Z"
}
```

## 4. Mẫu truy vấn (Access Patterns)

### 4.1 Truy vấn User và Employee

1. **Lấy thông tin user theo ID**:
   ```
   PK = USER#12345, SK = METADATA
   ```

2. **Lấy thông tin user theo email**:
   ```
   GSI1PK = EMAIL#john.doe@example.com, GSI1SK = USER#12345
   ```

3. **Lấy thông tin employee của user**:
   ```
   PK = USER#12345, SK = EMPLOYEE#98765
   ```

4. **Tìm tất cả employee theo team**:
   ```
   GSI2PK = TEAM#Engineering, GSI2SK begins_with EMPLOYEE#
   ```

5. **Tìm tất cả employee theo trạng thái**:
   ```
   GSI3PK = STATUS#Allocated, GSI3SK begins_with EMPLOYEE#
   ```

### 4.2 Truy vấn Skills và Employee Skills

1. **Lấy kỹ năng theo ID**:
   ```
   PK = SKILL#5678, SK = METADATA
   ```

2. **Lấy tất cả kỹ năng trong một category**:
   ```
   GSI1PK = CATEGORY#123, GSI1SK begins_with SKILL#
   ```

3. **Lấy tất cả kỹ năng của một employee**:
   ```
   PK = EMPLOYEE#98765, SK begins_with SKILL#
   ```

4. **Tìm tất cả employee có một kỹ năng nhất định**:
   ```
   GSI1PK = SKILL#5678, GSI1SK begins_with EMPLOYEE#
   ```

5. **Tìm tất cả employee với level kỹ năng cao**:
   ```
   GSI2PK = SKILLLEVEL#Advanced, GSI2SK begins_with EMPLOYEE#
   ```

### 4.3 Truy vấn Contracts và Revenue

1. **Lấy thông tin contract theo ID**:
   ```
   PK = CONTRACT#4567, SK = METADATA
   ```

2. **Lấy tất cả contract của một client**:
   ```
   GSI1PK = CLIENT#ABC Corporation, GSI1SK begins_with CONTRACT#
   ```

3. **Lấy tất cả nhân viên trong một contract**:
   ```
   PK = CONTRACT#4567, SK begins_with EMPLOYEE#
   ```

4. **Lấy tất cả contract của một nhân viên**:
   ```
   GSI1PK = EMPLOYEE#98765, GSI1SK begins_with CONTRACT#
   ```

5. **Lấy revenue theo employee và tháng**:
   ```
   PK = EMPLOYEE#98765, SK = REVENUE#2023-06
   ```

6. **Lấy tất cả revenue trong một tháng**:
   ```
   GSI2PK = YEAR#2023#MONTH#06, GSI2SK begins_with REVENUE#
   ```

### 4.4 Truy vấn Opportunities

1. **Lấy thông tin opportunity theo ID**:
   ```
   PK = OPPORTUNITY#7890, SK = METADATA
   ```

2. **Lấy tất cả opportunity ở một giai đoạn**:
   ```
   GSI2PK = DEALSTAGE#Proposal, GSI2SK begins_with OPPORTUNITY#
   ```

3. **Lấy tất cả opportunity của một sales**:
   ```
   GSI3PK = SALESUSER#9876, GSI3SK begins_with OPPORTUNITY#
   ```

4. **Lấy tất cả leader được chỉ định cho một opportunity**:
   ```
   PK = OPPORTUNITY#7890, SK begins_with LEADER#
   ```

## 5. Transactions và Consistency

### 5.1 Transactional Operations

DynamoDB hỗ trợ giao dịch qua API TransactWriteItems và TransactGetItems. Ví dụ:

1. **Tạo User và Employee**:
   ```
   TransactWriteItems:
   - PutItem: USER#12345 + METADATA
   - PutItem: USER#12345 + EMPLOYEE#98765
   ```

2. **Thêm nhân viên vào Contract và cập nhật trạng thái**:
   ```
   TransactWriteItems:
   - PutItem: CONTRACT#4567 + EMPLOYEE#98765
   - UpdateItem: EMPLOYEE#98765 + METADATA (update status)
   - PutItem: EMPLOYEE#98765 + STATUSLOG#timestamp
   ```

### 5.2 Strong Consistency

Mặc định DynamoDB sử dụng Eventually Consistent Reads. Khi cần đọc dữ liệu mới nhất:

```
Specify ConsistentRead = true for GetItem, Query, Scan operations
```

## 6. Kết luận

Thiết kế DynamoDB trên đây:

1. **Tối ưu hóa cho các mẫu truy vấn** thường gặp trong hệ thống
2. **Phi chuẩn hóa có chọn lọc** để tránh joins và truy vấn nhiều lần
3. **Sử dụng GSI hiệu quả** để hỗ trợ nhiều cách truy vấn khác nhau
4. **Áp dụng single-table design** để tối ưu hiệu suất và chi phí

Lưu ý rằng DynamoDB có giới hạn 400KB/item, cần tránh lưu trữ quá nhiều thông tin trong một item (đặc biệt với nested collections) và cân nhắc chiến lược để xử lý những tập dữ liệu lớn. 