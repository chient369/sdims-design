# Backend Task Breakdown

**Version Control:**

| Version | Date       | Author         | Changes                        | Approved By | Status    |
| :--- | :--- | :---- | :----- | :---- | :----- |
| 1.0     | 2025-05-02 | Chiến Trần Văn | Khởi tạo danh sách task backend dựa trên tài liệu thiết kế | -           | Draft     |
| 1.1     | 2024-05-03 | Chiến Trần Văn | Bổ sung task triển khai API Refresh Token | -           | Draft     |
---

## 1. Mục tiêu  
Phân chia chi tiết các task phát triển backend cho Hệ thống Quản lý Nội bộ (SDIMS), dựa trên tài liệu thiết kế và API.

## Backend Tasks

### Infrastructure & Setup (AWS Serverless)

- [ ] Task BE-INFRA-001: Thiết lập cấu trúc project serverless Python (High Priority) [Chi tiết](DetailedTasks/BE-INFRA-001.md)
  - Tạo cấu trúc thư mục tuân theo AWS Lambda best practices
  - Thiết lập template serverless.yml hoặc SAM/CDK cho các Lambda function
  - Thiết lập các file cấu hình (requirements.txt, .env.example)

- [ ] Task BE-INFRA-002: Cấu hình CI/CD pipeline cho backend (Medium Priority)
  - Thiết lập GitHub Actions/GitLab CI hoặc AWS CodePipeline
  - Tạo script deployment tự động cho môi trường dev và production
  - Cấu hình testing tự động trong pipeline

- [ ] Task BE-INFRA-003: Thiết lập cơ sở dữ liệu DynamoDB (High Priority) [Chi tiết](DetailedTasks/BE-INFRA-003.md)
  - Tạo instance DynamoDB trên AWS
  - Cấu hình VPC, security group và các kết nối Lambda đến DynamoDB
  - Tạo schema database ban đầu

- [ ] Task BE-INFRA-004: Cấu hình API Gateway (High Priority)
  - Thiết lập API Gateway cho các API endpoint
  - Cấu hình CORS, custom domain và API throttling
  - Kết nối API Gateway với các Lambda function

- [ ] Task BE-INFRA-005: Thiết lập AWS S3 cho lưu trữ tập tin (Medium Priority)
  - Tạo S3 bucket cho lưu trữ file đính kèm (hợp đồng, ảnh, v.v.)
  - Cấu hình IAM policy cho việc truy cập S3
  - Thiết lập lifecycle policy cho tự động archive/cleanup

- [ ] Task BE-INFRA-006: Cấu hình CloudWatch Logs và Monitoring (Medium Priority)
  - Thiết lập log groups cho các Lambda
  - Tạo các CloudWatch Alarm cho các metrics quan trọng
  - Cấu hình lambda để ghi log theo format chuẩn

- [ ] Task BE-INFRA-007: Cấu hình IAM Roles và Permissions (High Priority)
  - Tạo các IAM role với least-privilege access
  - Thiết lập quyền truy cập cho Lambda tới các service khác
  - Cấu hình resource-based policies khi cần thiết

### Core & Shared Components

- [ ] Task BE-CORE-001: Xây dựng module Database Connection (High Priority) [Chi tiết](TaskDetails/BE-CORE-001.md)
  - Tạo utility cho kết nối/quản lý database (SQLAlchemy)
  - Triển khai connection pooling cho Lambda
  - Xử lý việc đóng kết nối DB sau khi Lambda thực thi xong

- [ ] Task BE-CORE-002: Xây dựng module Authentication & Authorization (High Priority) [Chi tiết](TaskDetails/BE-CORE-002.md)
  - Triển khai API-AUTH-001, API-AUTH-002, API-AUTH-003
  - Xây dựng middleware JWT authentication
  - Triển khai hệ thống phân quyền (role-based access control)

- [ ] Task BE-CORE-003: Xây dựng module Validation & Exception Handling (High Priority) [Chi tiết](TaskDetails/BE-CORE-003.md)
  - Tạo decorator/middleware xử lý exception
  - Xây dựng validator (sử dụng Pydantic) cho request data
  - Thiết lập response formatter chuẩn cho API

- [ ] Task BE-CORE-004: Xây dựng module Logger (Medium Priority) [Chi tiết](TaskDetails/BE-CORE-004.md)
  - Tạo utility logging với các level khác nhau
  - Cấu hình log format và context cho dễ debug
  - Triển khai log masking cho dữ liệu nhạy cảm

- [ ] Task BE-CORE-005: Xây dựng module S3 Storage (Medium Priority) [Chi tiết](TaskDetails/BE-CORE-005.md)
  - Tạo utility cho upload/download file từ S3
  - Quản lý file metadata và định danh
  - Xử lý file validation và security

- [ ] Task BE-CORE-006: Xây dựng module Notification (Medium Priority) [Chi tiết](TaskDetails/BE-CORE-006.md)
  - Thiết lập cơ chế để gửi thông báo trong hệ thống
  - Triển khai queue cho notification processing
  - Tạo các template cho các loại thông báo

- [ ] Task BE-CORE-007: Xây dựng Seeder/Migrations Framework (Medium Priority) [Chi tiết](TaskDetails/BE-CORE-007.md)
  - Thiết lập Alembic cho database migrations
  - Tạo seed data cho development/testing
  - Triển khai schema versioning

### Database Models

- [ ] Task BE-DB-001: Xây dựng User & Auth Models (High Priority) [Chi tiết](TaskDetails/BE-DB-001.md)
  - User model với các field cần thiết
  - Role và Permission models
  - UserSession model (nếu cần)

- [ ] Task BE-DB-002: Xây dựng Employee Models (High Priority) [Chi tiết](TaskDetails/BE-DB-002.md)
  - Employee model với các field theo yêu cầu
  - EmployeeSkill, Skill, SkillCategory models
  - EmployeeProject và ProjectHistory models

- [ ] Task BE-DB-003: Xây dựng Cost & Margin Models (High Priority) [Chi tiết](TaskDetails/BE-DB-003.md)
  - EmployeeCost model để theo dõi chi phí nhân sự
  - MarginCalculation và MarginThreshold models
  - Revenue tracking models

- [ ] Task BE-DB-004: Xây dựng Opportunity Models (High Priority) [Chi tiết](TaskDetails/BE-DB-004.md)
  - Opportunity model (sync từ Hubspot)
  - OpportunityNote và OpportunityActivity models
  - HubspotSyncLog model

- [ ] Task BE-DB-005: Xây dựng Contract Models (High Priority) [Chi tiết](TaskDetails/BE-DB-005.md)
  - Contract model với các field cần thiết
  - PaymentTerm model để theo dõi các đợt thanh toán
  - ContractFile model cho quản lý file đính kèm
  - ContractEmployee model để liên kết nhân viên

- [ ] Task BE-DB-006: Xây dựng KPI & Revenue Models (Medium Priority) [Chi tiết](TaskDetails/BE-DB-006.md)
  - SalesKPI model để quản lý target doanh thu
  - ActualRevenue model để theo dõi doanh thu thực tế
  - KPIProgress model (nếu cần)

- [ ] Task BE-DB-007: Xây dựng System Configuration Models (Medium Priority) [Chi tiết](TaskDetails/BE-DB-007.md)
  - SystemConfig model cho cấu hình hệ thống
  - NotificationTemplate model (nếu cần)
  - SystemLog model

### API Implementation - Authentication & User Management

- [ ] Task BE-API-AUTH-001: Triển khai Login API (API-AUTH-001) (High Priority) [Chi tiết](TaskDetails/BE-API-AUTH-001.md)
  - Xử lý request với username/password
  - Validate thông tin đăng nhập
  - Tạo và trả về JWT token với thông tin user và permissions

- [ ] Task BE-API-AUTH-002: Triển khai Logout API (API-AUTH-002) (Medium Priority) [Chi tiết](TaskDetails/BE-API-AUTH-002.md)
  - Xử lý hủy token/session
  - Cập nhật trạng thái đăng nhập

- [ ] Task BE-API-AUTH-003: Triển khai Get Current User API (API-AUTH-003) (High Priority) [Chi tiết](TaskDetails/BE-API-AUTH-003.md)
  - Lấy thông tin user hiện tại từ token
  - Lấy danh sách các quyền hiện có của user

- [ ] Task BE-API-AUTH-004: Triển khai Refresh Token API (API-AUTH-004) (High Priority) [Chi tiết](TaskDetails/BE-API-AUTH-004.md)
  - Xử lý việc làm mới access token sử dụng refresh token
  - Thiết lập logic xác thực refresh token
  - Triển khai cơ chế rotation refresh token
  - Xây dựng hệ thống blacklist cho refresh token đã sử dụng

- [ ] Task BE-API-ADM-001: Triển khai User Management APIs (High Priority) [Chi tiết](TaskDetails/BE-API-ADM-001.md)
  - API-ADM-001: Get Users List
  - API-ADM-002: Create User
  - API-ADM-003: Get User Detail
  - API-ADM-004: Update User
  - API-ADM-005: Delete User

- [ ] Task BE-API-ADM-002: Triển khai Role Management APIs (High Priority) [Chi tiết](TaskDetails/BE-API-ADM-002.md)
  - API-ADM-006: Get Roles List
  - API-ADM-007: Create Role
  - API-ADM-008: Update Role
  - API-ADM-009: Delete Role
  - API-ADM-010: Get Permissions List

### API Implementation - Employee Management (HRM)

- [ ] Task BE-API-HRM-001: Triển khai Employee List API (API-HRM-001) (High Priority) [Chi tiết](TaskDetails/BE-API-HRM-001.md)
  - Implement Lambda handler cho API
  - Xử lý phân trang, lọc, tìm kiếm
  - Xử lý phân quyền (RBAC) cho danh sách nhân viên

- [ ] Task BE-API-HRM-002: Triển khai Employee CRUD APIs (High Priority) [Chi tiết](TaskDetails/BE-API-HRM-002.md)
  - API-HRM-002: Create Employee
  - API-HRM-003: Get Employee Detail
  - API-HRM-004: Update Employee
  - API-HRM-005: Delete Employee

- [ ] Task BE-API-HRM-003: Triển khai Employee Skill APIs (High Priority) [Chi tiết](TaskDetails/BE-API-HRM-003.md)
  - API-HRM-017: Get Employee Skills
  - API-HRM-018: Add/Update Employee Skill
  - API-HRM-019: Delete Employee Skill
  - Xử lý logic skill level và validation

- [ ] Task BE-API-HRM-004: Triển khai Skill & Category APIs (Medium Priority) [Chi tiết](TaskDetails/BE-API-HRM-004.md)
  - API-HRM-009: Get Skill Categories
  - API-HRM-010 ~ API-HRM-016: CRUD for Skills and Categories
  - Xử lý ràng buộc khi xóa categories/skills

- [ ] Task BE-API-HRM-005: Triển khai Employee Status & Assignment APIs (High Priority) [Chi tiết](TaskDetails/BE-API-HRM-005.md)
  - API-HRM-020: Update Employee Status
  - Xử lý logic phân bổ nhân viên vào dự án
  - Tự động cập nhật utilization rate

- [ ] Task BE-API-HRM-006: Triển khai Project History API (API-HRM-021) (Medium Priority) [Chi tiết](TaskDetails/BE-API-HRM-006.md)
  - Lấy và hiển thị lịch sử dự án của nhân viên
  - Kết hợp thông tin từ nhiều nguồn dữ liệu

- [ ] Task BE-API-HRM-007: Triển khai Employee Import/Export (Medium Priority) [Chi tiết](TaskDetails/BE-API-HRM-007.md)
  - API-HRM-007: Import Employees
  - API-HRM-008: Export Employees
  - Xử lý validation và mapping data trong file

- [ ] Task BE-API-HRM-008: Triển khai Employee Suggestion API (API-HRM-006) (Low Priority) [Chi tiết](TaskDetails/BE-API-HRM-008.md)
  - Logic gợi ý nhân viên dựa trên skills
  - Xử lý kết quả matchmaking

### API Implementation - Margin & Cost Management

- [ ] Task BE-API-MGN-001: Triển khai Margin List API (API-MGN-001) (High Priority) [Chi tiết](DetailedTasks/BE-API-MGN-001.md)
  - Lấy và tính toán dữ liệu margin của nhân viên
  - Áp dụng các bộ lọc team, thời gian
  - Phân quyền chặt chẽ (chỉ Leader/TP)

- [ ] Task BE-API-MGN-002: Triển khai Margin Summary API (API-MGN-002) (High Priority)
  - Tính toán margin tổng hợp cho dashboard
  - Áp dụng các ngưỡng cảnh báo

- [ ] Task BE-API-MGN-003: Triển khai Cost Import API (API-MGN-003) (High Priority)
  - Xử lý import chi phí nhân viên từ file
  - Validation và mapping data
  - Cập nhật vào database

- [ ] Task BE-API-MGN-004: Triển khai Manual Cost Entry API (API-MGN-004) (Medium Priority)
  - Xử lý nhập chi phí thủ công cho từng nhân viên
  - Validation và calculation sau khi nhập

### API Implementation - Opportunity Management

- [ ] Task BE-API-OPP-001: Triển khai Opportunity List API (API-OPP-001) (High Priority)
  - Lấy danh sách cơ hội với lọc, phân trang
  - Tính toán trạng thái follow-up
  - Áp dụng phân quyền theo vai trò

- [ ] Task BE-API-OPP-002: Triển khai Opportunity Detail API (API-OPP-002) (High Priority)
  - Lấy chi tiết một cơ hội kinh doanh
  - Kết hợp data từ nhiều bảng liên quan

- [ ] Task BE-API-OPP-003: Triển khai Hubspot Sync API (Medium Priority)
  - API-OPP-003: Manual Hubspot Sync
  - API-OPP-004: Get Sync Logs
  - Xử lý logic đồng bộ dữ liệu từ Hubspot

- [ ] Task BE-API-OPP-004: Triển khai Opportunity Management APIs (High Priority)
  - API-OPP-005: Assign Leader to Opportunity
  - API-OPP-006: Add Opportunity Note
  - API-OPP-007: Get Opportunity Notes
  - API-OPP-008: Toggle Onsite Priority

- [ ] Task BE-API-OPP-005: Triển khai Scheduled Hubspot Sync (Medium Priority)
  - Thiết lập AWS EventBridge để trigger đồng bộ định kỳ
  - Xử lý lỗi và retry logic

### API Implementation - Contract & Revenue Management

- [ ] Task BE-API-CTR-001: Triển khai Contract List API (API-CTR-001) (High Priority)
  - Lấy danh sách hợp đồng với lọc, phân trang
  - Áp dụng phân quyền theo vai trò

- [ ] Task BE-API-CTR-002: Triển khai Contract CRUD APIs (High Priority)
  - API-CTR-002: Create Contract
  - API-CTR-003: Get Contract Detail
  - API-CTR-004: Update Contract
  - API-CTR-005: Delete Contract

- [ ] Task BE-API-CTR-003: Triển khai Payment Terms APIs (High Priority)
  - API-CTR-006: Get Payment Terms
  - API-CTR-007: Update Payment Term Status
  - API-CTR-008: Import Payment Status

- [ ] Task BE-API-CTR-004: Triển khai Contract File APIs (Medium Priority)
  - API-CTR-009: Get Contract Files
  - API-CTR-010: Upload Contract File
  - API-CTR-011: Delete Contract File
  - Xử lý upload/storage trên S3

- [ ] Task BE-API-CTR-005: Triển khai Contract Employee APIs (Medium Priority)
  - API-CTR-012: Get Contract Employees
  - API-CTR-013: Link Employee to Contract
  - API-CTR-014: Unlink Employee from Contract

- [ ] Task BE-API-CTR-006: Triển khai Sales KPI APIs (Medium Priority)
  - API-CTR-015: Get Sales KPIs
  - API-CTR-016: Create/Update Sales KPI
  - API-CTR-017: Delete Sales KPI

### API Implementation - Reports & Dashboard

- [ ] Task BE-API-RPT-001: Triển khai Dashboard Summary API (API-RPT-001) (High Priority)
  - Lấy và tính toán dữ liệu tổng hợp cho dashboard
  - Tối ưu hiệu năng cho các widget

- [ ] Task BE-API-RPT-002: Triển khai Employee Reports API (API-RPT-002) (Medium Priority)
  - Xử lý báo cáo chi tiết danh sách nhân viên
  - Áp dụng phân quyền và bộ lọc

- [ ] Task BE-API-RPT-003: Triển khai Margin Reports API (API-RPT-003) (Medium Priority)
  - Báo cáo chi tiết margin theo nhân viên/team
  - Xử lý tính toán phức tạp và phân quyền

- [ ] Task BE-API-RPT-004: Triển khai Opportunity Reports API (API-RPT-004) (Medium Priority)
  - Báo cáo chi tiết danh sách cơ hội
  - Áp dụng các bộ lọc, phân quyền

- [ ] Task BE-API-RPT-005: Triển khai Contract Reports API (API-RPT-005) (Medium Priority)
  - Báo cáo chi tiết danh sách hợp đồng
  - Xử lý export data

- [ ] Task BE-API-RPT-006: Triển khai Payment Status Reports API (API-RPT-006) (Medium Priority)
  - Báo cáo chi tiết tình trạng thanh toán/công nợ
  - Tính toán các chỉ số công nợ

- [ ] Task BE-API-RPT-007: Triển khai KPI Progress Reports API (API-RPT-007) (Medium Priority)
  - Báo cáo tiến độ KPI doanh thu Sales
  - So sánh actual vs target

- [ ] Task BE-API-RPT-008: Triển khai Utilization Reports API (API-RPT-008) (Medium Priority)
  - Báo cáo tỷ lệ sử dụng nguồn lực
  - Tính toán utilization rate

### System Configuration & Monitoring

- [ ] Task BE-SYS-001: Triển khai System Config APIs (Medium Priority)
  - API-ADM-011: Get System Configs
  - API-ADM-012: Update System Config
  - Xử lý caching cho config

- [ ] Task BE-SYS-002: Triển khai System Logs API (API-ADM-013) (Medium Priority)
  - Lấy và hiển thị log hệ thống
  - Xử lý lọc và phân trang cho log data

- [ ] Task BE-SYS-003: Triển khai Scheduled Tasks (Medium Priority)
  - Tự động cảnh báo nhân viên sắp hết dự án
  - Tự động cảnh báo các khoản thanh toán sắp đến hạn
  - Các tác vụ maintenance định kỳ khác

### Testing

- [ ] Task BE-TEST-001: Viết Unit Tests cho Core Components (High Priority)
  - Test các module authentication, authorization
  - Test validation, exception handling
  - Test database models

- [ ] Task BE-TEST-002: Viết Integration Tests (Medium Priority)
  - Test các API endpoint chính
  - Test luồng dữ liệu end-to-end
  - Test các business logic phức tạp

- [ ] Task BE-TEST-003: Viết Tests cho Business Logic (Medium Priority)
  - Test tính toán margin
  - Test Hubspot sync logic
  - Test các validation rule phức tạp

- [ ] Task BE-TEST-004: Thiết lập Load Testing (Low Priority)
  - Test hiệu năng các API chính
  - Xác định bottlenecks
  - Tối ưu dựa trên kết quả test

### Documentation

- [ ] Task BE-DOC-001: Tạo Documentation cho API (Medium Priority)
  - Tài liệu OpenAPI/Swagger cho các API
  - Mô tả chi tiết request/response

- [ ] Task BE-DOC-002: Viết Hướng dẫn Deployment (Medium Priority)
  - Tài liệu setup môi trường development
  - Quy trình deployment lên production
  - Troubleshooting guide

- [ ] Task BE-DOC-003: Viết Technical Documentation (Medium Priority)
  - Mô tả kiến trúc hệ thống
  - Database schema documentation
  - Design decisions và patterns đã sử dụng 