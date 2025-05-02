# Task BE-DB-005: Xây dựng Contract Models

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 4 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-005: Module S3 Storage](BE-CORE-005.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Database Models:
    - [BE-DB-002: Employee Models](BE-DB-002.md)
    - [BE-DB-004: Opportunity Models](BE-DB-004.md)
  - Contract Management APIs:
    - [API-CTR-001: Contract List API](../../../DD/API/API-CTR-001.md)
    - [API-CTR-002: Create Contract API](../../../DD/API/API-CTR-002.md)
    - [API-CTR-003: Get Contract Detail API](../../../DD/API/API-CTR-003.md)
    - [API-CTR-004: Update Contract API](../../../DD/API/API-CTR-004.md)
    - [API-CTR-005: Delete Contract API](../../../DD/API/API-CTR-005.md)
    - [API-CTR-006: Get Payment Terms API](../../../DD/API/API-CTR-006.md)
    - [API-CTR-007: Update Payment Term Status API](../../../DD/API/API-CTR-007.md)
    - [API-CTR-008: Import Payment Status API](../../../DD/API/API-CTR-008.md)
  - Contract File APIs:
    - [API-CTR-009: Get Contract Files API](../../../DD/API/API-CTR-009.md)
    - [API-CTR-010: Upload Contract File API](../../../DD/API/API-CTR-010.md)
    - [API-CTR-011: Delete Contract File API](../../../DD/API/API-CTR-011.md)
  - Contract Employee APIs:
    - [API-CTR-012: Get Contract Employees API](../../../DD/API/API-CTR-012.md)
    - [API-CTR-013: Link Employee to Contract API](../../../DD/API/API-CTR-013.md)
    - [API-CTR-014: Unlink Employee from Contract API](../../../DD/API/API-CTR-014.md)
  - Report APIs:
    - [API-RPT-005: Contract Reports API](../../../DD/API/API-RPT-005.md)
    - [API-RPT-006: Payment Status Reports API](../../../DD/API/API-RPT-006.md)
  - Yêu cầu chức năng: 
    - [F-4.1: Quản lý Thông tin Hợp đồng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.2: Liên kết Hợp đồng với Cơ hội/Nhân sự](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.3: Upload/Đính kèm File Hợp đồng](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.4: Quản lý Điều khoản Thanh toán](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.5: Cập nhật Trạng thái Thu tiền](../../../BD/FunctionDesign/FunctionList.md)
    - [F-4.7: Báo cáo Công nợ](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến quản lý hợp đồng (Contract), điều khoản thanh toán, file đính kèm, và liên kết với nhân viên. Các models này là nền tảng cho module quản lý hợp đồng và công nợ, cũng như tính toán doanh thu.

## Chi tiết công việc
1. **Xây dựng Contract model**:
   - Thiết kế các fields cần thiết: contract_id, name, contract_number, opportunity_id, client_name, client_address, start_date, end_date, total_value, currency, status, payment_terms, description, delivery_notes, owner_user_id, created_at, updated_at, v.v.
   - Định nghĩa các relationships với các bảng liên quan (Opportunity, User, PaymentTerm, ContractFile, ContractEmployee).
   - Triển khai validation rules cho contract data.
   - Thiết kế cơ chế để tracking changes và versioning contracts.

2. **Xây dựng PaymentTerm model**:
   - Thiết kế các fields: id, contract_id, term_number, description, amount, percentage, due_date, payment_date, status, invoice_number, invoice_date, payment_proof, notes, created_at, updated_at, v.v.
   - Định nghĩa relationship với Contract model.
   - Triển khai logic để tính toán tự động (ví dụ: tính amount dựa trên percentage của total_value).
   - Hỗ trợ các trạng thái thanh toán khác nhau (pending, invoiced, paid, overdue).

3. **Xây dựng ContractFile model**:
   - Thiết kế các fields: id, contract_id, file_name, file_type, file_size, s3_key, description, uploaded_by, created_at, v.v.
   - Định nghĩa relationship với Contract model.
   - Tích hợp với S3 Storage module để quản lý file uploads.
   - Hỗ trợ các loại file khác nhau (contract, invoice, amendment, etc.).

4. **Xây dựng ContractEmployee model (association table)**:
   - Thiết kế các fields: contract_id, employee_id, role, allocation_percentage, start_date, end_date, billing_rate, created_at, updated_at, v.v.
   - Định nghĩa relationships với Contract và Employee models.
   - Triển khai validation rules và business logic cho việc phân bổ nhân viên.
   - Hỗ trợ tracking historical assignments.

5. **Triển khai các helper models và business logic**:
   - Xây dựng ContractStatus enum/model để quản lý các trạng thái hợp đồng.
   - Tạo PaymentStatus enum/model để quản lý các trạng thái thanh toán.
   - Thiết kế logic để tính toán tổng giá trị đã thanh toán/chưa thanh toán.
   - Triển khai methods để tính toán doanh thu theo thời gian, khách hàng, v.v.
   - Hỗ trợ tính năng cảnh báo các khoản thanh toán sắp đến hạn/quá hạn.
   - Tạo cơ chế tracking cho quá trình import payment status.

## Đầu ra dự kiến
- Contract, PaymentTerm, ContractFile, và ContractEmployee models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Business rules và calculations cho quản lý hợp đồng và thanh toán.
- Tích hợp với S3 Storage cho việc quản lý file.
- Unit tests cho các models và business logic.
- Documentation về cấu trúc models và cách sử dụng.