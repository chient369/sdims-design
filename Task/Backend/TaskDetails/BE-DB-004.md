# Task BE-DB-004: Xây dựng Opportunity Models

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-007: Seeder/Migrations Framework](BE-CORE-007.md)
  - Opportunity Management APIs:
    - [API-OPP-001: Opportunity List API](../../../DD/API/API-OPP-001.md)
    - [API-OPP-002: Opportunity Detail API](../../../DD/API/API-OPP-002.md)
    - [API-OPP-003: Manual Hubspot Sync API](../../../DD/API/API-OPP-003.md)
    - [API-OPP-004: Get Sync Logs API](../../../DD/API/API-OPP-004.md)
    - [API-OPP-005: Assign Leader API](../../../DD/API/API-OPP-005.md)
    - [API-OPP-006: Add Opportunity Note API](../../../DD/API/API-OPP-006.md)
    - [API-OPP-007: Get Opportunity Notes API](../../../DD/API/API-OPP-007.md)
    - [API-OPP-008: Toggle Onsite Priority API](../../../DD/API/API-OPP-008.md)
  - Report APIs:
    - [API-RPT-004: Opportunity Reports API](../../../DD/API/API-RPT-004.md)
  - Yêu cầu chức năng: 
    - [F-3.1: Đồng bộ Cơ hội từ Hubspot](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.2: Ghi Log Đồng bộ Hubspot](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.3: Assign Leader vào Cơ hội](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.4: Ghi chú/Log Hoạt động Cơ hội](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.5: Cập nhật Ngày tương tác cuối](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.6: Hiển thị Trạng thái Follow-up](../../../BD/FunctionDesign/FunctionList.md)
    - [F-3.7: Đánh dấu Cơ hội Ưu tiên Onsite](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Thiết kế và xây dựng các database models liên quan đến quản lý cơ hội kinh doanh (Opportunity), đồng bộ dữ liệu từ Hubspot, ghi chú và hoạt động của cơ hội. Các models này là nền tảng cho module quản lý cơ hội kinh doanh và là cầu nối giữa hệ thống CRM (Hubspot) và hệ thống nội bộ.

## Chi tiết công việc
1. **Xây dựng Opportunity model**:
   - Thiết kế các fields cần thiết để lưu trữ dữ liệu từ Hubspot: opportunity_id, hubspot_id, name, description, client_name, client_contact, stage, probability, expected_value, expected_close_date, source, assigned_sales, assigned_leader, created_at, updated_at, last_interaction_date, follow_up_status, priority_onsite, etc.
   - Định nghĩa các relationships với các bảng liên quan (User, OpportunityNote, Contract).
   - Triển khai validation rules cho opportunity data.
   - Thiết kế cơ chế để tự động tính follow-up status (red/yellow/green).

2. **Xây dựng OpportunityNote model**:
   - Thiết kế các fields: id, opportunity_id, user_id, content, is_internal, created_at, updated_at, etc.
   - Định nghĩa relationships với Opportunity và User models.
   - Triển khai logic để tự động cập nhật last_interaction_date của Opportunity khi có ghi chú mới.

3. **Xây dựng OpportunityActivity model**:
   - Thiết kế các fields: id, opportunity_id, user_id, activity_type, description, happened_at, created_at, etc.
   - Định nghĩa relationships với Opportunity và User models.
   - Triển khai các activity_types khác nhau (meeting, call, email, task completion, etc).
   - Hỗ trợ logic để tracking các hoạt động quan trọng.

4. **Xây dựng HubspotSyncLog model**:
   - Thiết kế các fields: id, sync_start_time, sync_end_time, status, items_processed, items_created, items_updated, items_failed, error_message, triggered_by, etc.
   - Triển khai các methods để ghi log chi tiết về quá trình đồng bộ.
   - Hỗ trợ error reporting và monitoring.

5. **Triển khai các helper models và business logic**:
   - Xây dựng OpportunityStage enum/model để quản lý các giai đoạn của cơ hội.
   - Tạo các helper functions/methods để tính toán thống kê về cơ hội (tỷ lệ chuyển đổi, phân bố theo giai đoạn, v.v.).
   - Triển khai các methods để tính toán thời gian cơ hội đã ở mỗi giai đoạn.
   - Thiết kế logic để đồng bộ và mapping dữ liệu từ Hubspot API.
   - Cơ chế để quản lý xung đột dữ liệu khi đồng bộ.

## Đầu ra dự kiến
- Opportunity, OpportunityNote, OpportunityActivity, và HubspotSyncLog models hoàn chỉnh.
- Migration scripts cho việc tạo database tables.
- Logic để đồng bộ dữ liệu từ Hubspot (framework).
- Business rules để tính toán follow-up status và các chỉ số khác.
- Unit tests cho các models và business logic.
- Documentation về cấu trúc models và cách sử dụng.