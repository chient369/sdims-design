**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2023-08-20 | Chiến Trần Văn | Đánh giá chuyển đổi từ RDBMS sang DynamoDB | -           | Draft     |

---

## 1. Mục tiêu  
Đánh giá các thay đổi cần thiết để chuyển đổi cấu trúc cơ sở dữ liệu hiện tại sang DynamoDB và xác định những thách thức, cũng như lợi ích trong quá trình này.

## 2. Thay đổi về mô hình dữ liệu

### 2.1 Từ cấu trúc quan hệ sang NoSQL

#### Mô hình dữ liệu hiện tại
- Cơ sở dữ liệu hiện tại sử dụng mô hình quan hệ với nhiều bảng có liên kết thông qua khóa ngoại
- Lược đồ chặt chẽ với các ràng buộc toàn vẹn (khoá chính, khoá ngoại, ràng buộc unique)
- Sử dụng nhiều quan hệ nhiều-nhiều (Many-to-Many) thông qua các bảng trung gian

#### Những thay đổi khi chuyển sang DynamoDB
- **Chuyển từ lược đồ cố định sang linh hoạt**: DynamoDB là NoSQL không có lược đồ cố định
- **Phi chuẩn hóa dữ liệu**: Cần phi chuẩn hóa dữ liệu để tối ưu cho mô hình truy vấn
- **Cấu trúc phân cấp**: Sử dụng cấu trúc lồng ghép, không phải bảng phẳng như SQL

### 2.2 Thiết kế khóa

#### Thiết kế khóa trong DynamoDB
- **Partition Key (PK)**: Xác định cách phân phối dữ liệu, cần chọn kỹ để tránh hotspot
- **Sort Key (SK)**: Cho phép sắp xếp và truy vấn dữ liệu hiệu quả trong cùng một partition
- **Composite Keys**: Kết hợp nhiều thuộc tính để tạo khóa tổng hợp phù hợp với mẫu truy vấn

#### Đề xuất thiết kế cho các entity chính
1. **Users/Employees**:
   - PK: `USER#id` hoặc `EMPLOYEE#id`
   - SK: Có thể là metadata hoặc `#PROFILE` để lưu thông tin cơ bản

2. **Contracts**:
   - PK: `CONTRACT#id`
   - SK: Có thể là `#METADATA` hoặc `#YYYY-MM` để truy vấn theo thời gian

3. **Opportunities**:
   - PK: `OPPORTUNITY#id`
   - SK: `#METADATA` hoặc `#STATUS#stage` để truy vấn theo trạng thái

## 3. Quản lý quan hệ dữ liệu

### 3.1 Quan hệ một-nhiều (One-to-Many)
- **Nested Collections**: Sử dụng cấu trúc lồng ghép cho các quan hệ đơn giản (nhưng cần chú ý giới hạn 400KB/item)
- **Global Secondary Indexes (GSI)**: Cho phép truy vấn hiệu quả dựa trên các thuộc tính không phải khóa chính
- **Overloaded Indexes**: Sử dụng cùng một bảng cho nhiều loại entity, phân biệt bằng prefixes trong khóa

### 3.2 Quan hệ nhiều-nhiều (Many-to-Many)
Hệ thống hiện tại có nhiều quan hệ M-M cần xử lý đặc biệt:

1. **UserRoles & RolePermissions**:
   - Có thể lồng ghép permissions vào roles, và roles vào users
   - Hoặc tạo items riêng với khóa: PK=`USER#id`, SK=`ROLE#roleId`

2. **EmployeeSkills**:
   - PK: `EMPLOYEE#id`
   - SK: `SKILL#skillId`
   - Hoặc ngược lại tùy vào mẫu truy vấn

3. **ContractEmployees**:
   - PK: `CONTRACT#id`
   - SK: `EMPLOYEE#employeeId`
   - Đồng thời tạo GSI với PK=`EMPLOYEE#id`, SK=`CONTRACT#contractId`

## 4. Vấn đề về truy vấn dữ liệu

### 4.1 Những thay đổi về cách truy vấn
- **Single-table Design**: Thiết kế single-table mang lại hiệu quả với DynamoDB
- **No JOINs**: Không hỗ trợ JOIN như SQL, phải phi chuẩn hóa hoặc thực hiện nhiều lần truy vấn
- **Access Patterns First**: Thiết kế bảng dựa trên mẫu truy vấn, không phải dựa trên entity

### 4.2 Truy vấn phức tạp
Các truy vấn phức tạp cần xử lý đặc biệt:

1. **Tìm kiếm nhân viên theo kỹ năng**:
   - Tạo GSI với PK=`SKILL#skillId`
   - Hoặc sử dụng DynamoDB Streams + Elasticsearch

2. **Báo cáo revenue/margin**:
   - Có thể cần aggregation services bên ngoài như AWS Lambda
   - Tạo bảng tổng hợp được cập nhật định kỳ

3. **Tìm kiếm full-text**:
   - Tích hợp với Amazon Elasticsearch Service

## 5. Quản lý giao dịch và tính nhất quán

### 5.1 Mất các ràng buộc toàn vẹn
- **Không có Foreign Key Constraints**: Logic ràng buộc phải được xử lý ở tầng ứng dụng
- **Triggers**: Cần sử dụng DynamoDB Streams + Lambda để thay thế triggers trong SQL

### 5.2 Transaction Management
- **DynamoDB Transactions**: Hỗ trợ giao dịch trên nhiều items, nhưng có giới hạn và chi phí cao hơn
- **Optimistic Concurrency Control**: Sử dụng điều kiện và version numbers để quản lý đồng thời

## 6. Vấn đề về hiệu suất và chi phí

### 6.1 Capacity Planning
- **Provisioned Capacity**: Cần tính toán đơn vị đọc/ghi (RCU/WCU) cho từng bảng
- **On-Demand Capacity**: Linh hoạt nhưng chi phí có thể cao hơn cho workload ổn định

### 6.2 Hotspots và Throttling
- **Partition Key Design**: Chọn PK để phân phối đều truy cập
- **Burst Capacity**: Cần xử lý throttling trong mã nguồn

## 7. Chiến lược di chuyển dữ liệu

### 7.1 Phương pháp di chuyển
- **Dual-write**: Ghi đồng thời vào cả hai hệ thống trong giai đoạn chuyển tiếp
- **AWS Database Migration Service (DMS)**: Sử dụng cho quá trình ban đầu
- **Phát triển microservices**: Chuyển từng phần hệ thống

### 7.2 Testing và Validation
- **Shadow Testing**: Chạy truy vấn trên cả hai hệ thống và so sánh kết quả
- **Performance Benchmarks**: Đánh giá hiệu suất trước và sau khi chuyển đổi

## 8. Kết luận

### 8.1 Lợi ích
- **Khả năng mở rộng**: DynamoDB có khả năng mở rộng ưu việt so với RDBMS
- **Hiệu suất**: Độ trễ thấp, ổn định dù dữ liệu tăng
- **Quản lý**: Serverless, AWS quản lý hạ tầng

### 8.2 Thách thức
- **Complexity**: Thiết kế phức tạp hơn, đặc biệt với dữ liệu có nhiều quan hệ
- **Learning Curve**: Cần đào tạo đội ngũ về mô hình NoSQL
- **Vendor Lock-in**: Phụ thuộc vào AWS và các dịch vụ liên quan

### 8.3 Đề xuất tiếp theo
- Xác định access patterns chính trước khi thiết kế cấu trúc DynamoDB
- Đánh giá cost-benefit cho từng phần của hệ thống
- Cân nhắc hybrid approach (dữ liệu giao dịch trong DynamoDB, dữ liệu phân tích trong RDS) 