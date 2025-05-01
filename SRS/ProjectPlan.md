# Kế hoạch Phát triển Dự án: Hệ thống Quản lý Nội bộ (Internal Management System)

**Version Control:**

| Version | Date       | Author      | Changes                                     | Approved By | Status    |
| :------ | :--------- | :---------- | :------------------------------------------ | :---------- | :-------- |
| 1.0     | 2025-04-28 | Chien Tran Van | Initial Project Plan                      |- | Draft     |
|         |            |             |                                             |             |           |

---

## 1. Tổng quan Dự án

*   **Tên dự án:** Hệ thống Quản lý Nội bộ (Internal Management System)
*   **Mục tiêu:** Xây dựng một công cụ phần mềm tập trung để quản lý thông tin nhân sự, hiệu suất (margin), cơ hội kinh doanh, hợp đồng, và cung cấp dashboard báo cáo tổng quan, nhằm giải quyết các vấn đề về giao tiếp, theo dõi KPI, và phối hợp giữa các bộ phận.
*   **Phạm vi chính (Dựa trên Requirments.md):**
    *   Module 1: Quản lý Nhân sự (HRM)
    *   Module 2: Quản lý Hiệu suất & Margin
    *   Module 3: Quản lý Cơ hội Kinh doanh (Tích hợp Hubspot)
    *   Module 4: Quản lý Hợp đồng & Doanh thu (Liên kết Kế toán)
    *   Module 5: Dashboard & Báo cáo
    *   Module 6: Quản trị Hệ thống

## 2. Các Giai đoạn Dự án (Phases)

Dự án sẽ được triển khai theo các giai đoạn chính sau, áp dụng phương pháp phát triển linh hoạt (Agile - Scrum) với các Sprint (ví dụ: 2-3 tuần/Sprint).

| Giai đoạn                      | Mục tiêu chính                                                                 | Thời gian Ước tính |
| :----------------------------- | :----------------------------------------------------------------------------- | :---------------- |
| **Phase 1: Khởi tạo & Kế hoạch** | Hoàn thiện yêu cầu, xác định phạm vi MVP, lập kế hoạch chi tiết, chọn công nghệ | 1 tuần            |
| **Phase 2: Thiết kế**          | Thiết kế UI/UX, Database, API, kiến trúc hệ thống                               | 1-2 tuần          |
| **Phase 3: Phát triển (MVP)**  | Xây dựng các chức năng cốt lõi của các module (theo Sprint)                      | 4 tuần            |
| **Phase 4: Kiểm thử (MVP)**    | Integration Testing, System Testing, UAT, Security Testing                      | 1-2 tuần          |
| **Phase 5: Triển khai (MVP)**  | Chuẩn bị môi trường, triển khai phiên bản MVP, data migration ban đầu          | 1 tuần            |
| **Phase 6: Đào tạo & Go-Live** | Đào tạo người dùng, chuẩn bị tài liệu, chính thức vận hành                     | 1 tuần            |
| **Phase 7: Vận hành & Bảo trì** | Theo dõi, sửa lỗi, hỗ trợ người dùng, lên kế hoạch cho các phase tiếp theo     | Liên tục          |

**Lưu ý:** Thời gian ước tính có thể thay đổi dựa trên nguồn lực thực tế, độ phức tạp phát sinh và phản hồi trong quá trình phát triển.

## 3. Chi tiết Công việc theo Giai đoạn

### Phase 1: Khởi tạo & Kế hoạch (Week 1)

*   [ ] **Task 1.1:** Họp Kick-off dự án.
*   [ ] **Task 1.2:** Rà soát và hoàn thiện tài liệu yêu cầu (`Requirments.md`).
*   [ ] **Task 1.3:** Xác định phạm vi chi tiết cho Minimum Viable Product (MVP).
    *   *Quyết định:* Module/Chức năng nào là cốt lõi cần có trong lần ra mắt đầu tiên?
*   [ ] **Task 1.4:** Lựa chọn Công nghệ (Technology Stack) chi tiết:
    *   Backend (VD: Java/Spring Boot)
    *   Frontend (VD: React)
    *   Database (VD: PostgreSQL)
    *   Cloud Platform / Hosting
*   [ ] **Task 1.5:** Thành lập đội ngũ dự án & Phân công vai trò.
*   [ ] **Task 1.6:** Thiết lập môi trường quản lý dự án (Jira, Git Repository...).
*   [ ] **Task 1.7:** Lập kế hoạch Sprint ban đầu (Sprint Planning 0/1).
*   [ ] **Task 1.8:** Xác định quy trình làm việc (Git workflow, Code review process, CI/CD strategy).
*   [ ] **Task 1.9:** Phân tích và Lập kế hoạch quản lý rủi ro ban đầu.

### Phase 2: Thiết kế (Weeks 2-3)

*   [ ] **Task 2.1:** Thiết kế Kiến trúc Hệ thống tổng thể.
*   [ ] **Task 2.2:** Thiết kế Database Schema chi tiết.
*   [ ] **Task 2.3:** Thiết kế API (RESTful endpoints, request/response models).
*   [ ] **Task 2.4:** Thiết kế Giao diện Người dùng (UI/UX):
    *   Wireframes
    *   Mockups
    *   (Optional) Prototypes
*   [ ] **Task 2.5:** Thiết kế chi tiết luồng nghiệp vụ cho từng module MVP.
*   [ ] **Task 2.6:** Họp Review và Phê duyệt Thiết kế.

### Phase 3: Phát triển MVP (Weeks 4-7 - Theo Sprints)

*   **Sprint 1 -> n:**
    *   [ ] Lập kế hoạch Sprint (Sprint Planning): Chọn User Stories/Tasks từ Backlog.
    *   [ ] Phát triển Backend (API, Business Logic, Database Interaction).
    *   [ ] Phát triển Frontend (UI Implementation, API Integration).
    *   [ ] Viết Unit Tests / Integration Tests cơ bản.
    *   [ ] Thực hiện Code Review.
    *   [ ] Họp Daily Scrum.
    *   [ ] Demo cuối Sprint (Sprint Review).
    *   [ ] Họp Cải tiến (Sprint Retrospective).
*   **Các hạng mục chính cần phát triển:**
    *   [ ] Module 6: Admin (Quản lý User, Roles, Permissions cơ bản) - *Nền tảng*
    *   [ ] Module 1: HRM (Core features: Hồ sơ NV, Skills cơ bản, Trạng thái)
    *   [ ] Module 3: Opportunity Management (Core features: Đồng bộ Hubspot, Tracking cơ bản, Assign Leader) - *Cần ưu tiên sớm để test tích hợp*
    *   [ ] Module 4: Contract Management (Core features: Nhập HĐ, Tracking thanh toán cơ bản)
    *   [ ] Module 2: Margin Management (Core features: Nhập cost/revenue, Tính margin, Cảnh báo) - *Chú ý phân quyền chặt chẽ*
    *   [ ] Module 5: Dashboard (Các widget cơ bản cho MVP)
    *   [ ] Xây dựng CI/CD pipeline cơ bản.

### Phase 4: Kiểm thử MVP (Weeks 8-9)

*   [ ] **Task 4.1:** Chuẩn bị Kế hoạch Kiểm thử (Test Plan) & Test Cases chi tiết.
*   [ ] **Task 4.2:** Thực hiện Integration Testing giữa các module.
*   [ ] **Task 4.3:** Thực hiện System Testing (End-to-End).
*   [ ] **Task 4.4:** Thực hiện Performance Testing (cho các chức năng quan trọng như báo cáo, dashboard).
*   [ ] **Task 4.5:** Thực hiện Security Testing (Kiểm tra phân quyền, lỗ hổng cơ bản).
*   [ ] **Task 4.6:** Tổ chức User Acceptance Testing (UAT) với stakeholders (Leaders, Sales, Admin...).
*   [ ] **Task 4.7:** Log bugs và thực hiện Bug Fixing.
*   [ ] **Task 4.8:** Regression Testing sau khi fix bugs.

### Phase 5: Triển khai MVP (Week 10)

*   [ ] **Task 5.1:** Chuẩn bị Môi trường Staging & Production.
*   [ ] **Task 5.2:** Hoàn thiện CI/CD pipeline cho Staging/Production.
*   [ ] **Task 5.3:** Lập kế hoạch và thực hiện Data Migration ban đầu (nếu cần).
*   [ ] **Task 5.4:** Triển khai ứng dụng lên môi trường Staging.
*   [ ] **Task 5.5:** Kiểm tra lần cuối trên Staging.
*   [ ] **Task 5.6:** Triển khai ứng dụng lên môi trường Production (Go-live).
*   [ ] **Task 5.7:** Thực hiện Post-Deployment Monitoring & Sanity Checks.

### Phase 6: Đào tạo & Go-Live (Week 11)

*   [ ] **Task 6.1:** Chuẩn bị Tài liệu Hướng dẫn Sử dụng (User Manuals/Guides) cho các nhóm người dùng.
*   [ ] **Task 6.2:** Tổ chức các buổi Đào tạo (Training Sessions) cho người dùng.
*   [ ] **Task 6.3:** Thông báo Chính thức về việc Vận hành Hệ thống (Go-Live Announcement).
*   [ ] **Task 6.4:** Thiết lập kênh Hỗ trợ Người dùng ban đầu (Helpdesk/Support Channel).

### Phase 7: Vận hành & Bảo trì (Liên tục)

*   [ ] **Task 7.1:** Theo dõi hiệu suất và log hệ thống liên tục.
*   [ ] **Task 7.2:** Tiếp nhận phản hồi từ người dùng.
*   [ ] **Task 7.3:** Sửa lỗi phát sinh (Bug Fixing).
*   [ ] **Task 7.4:** Thực hiện bảo trì định kỳ (Backup dữ liệu, cập nhật hệ điều hành/thư viện...).
*   [ ] **Task 7.5:** Lập kế hoạch cho các phiên bản/phase tiếp theo (Bổ sung tính năng, cải thiện...).

## 4. Đội ngũ Dự án (Roles & Responsibilities)

*   **Project Sponsor:** (Xác định người đại diện Ban Lãnh đạo) - Quyết định cuối cùng, đảm bảo nguồn lực.
*   **Project Manager (PM):** Quản lý tổng thể dự án, kế hoạch, rủi ro, giao tiếp.
*   **Tech Lead / Architect:** Chịu trách nhiệm về mặt kỹ thuật, thiết kế, chất lượng code.
*   **Backend Developer(s):** Xây dựng logic nghiệp vụ, API.
*   **Frontend Developer(s):** Xây dựng giao diện người dùng.
*   **QA Engineer(s):** Lập kế hoạch và thực hiện kiểm thử.
*   **UI/UX Designer:** Thiết kế giao diện và trải nghiệm người dùng.
*   **Stakeholders:** Trưởng bộ phận, Team Leaders, Sales, Nhân viên (Tham gia UAT, cung cấp feedback).

## 5. Công nghệ Đề xuất

*   **Backend:** Java 17+, Spring Boot 3+
*   **Frontend:** React 18+ / TypeScript
*   **Database:** PostgreSQL 15+
*   **API:** RESTful JSON
*   **Authentication:** Spring Security + JWT/OAuth2
*   **Caching:** Redis (Optional)
*   **Hubspot Integration:** API / batch
*   **Deployment:** Docker, AWS
*   **CI/CD:** Code Pipeline
*   **Version Control:** Git
*   **Project Management:** 

## 6. Quản lý Rủi ro (Initial Assessment)

| Rủi ro tiềm ẩn                               | Mức độ Ảnh hưởng | Khả năng Xảy ra | Biện pháp Giảm thiểu / Xử lý                                                                                                |
| :-------------------------------------------- | :--------------- | :-------------- | :------------------------------------------------------------------------------------------------------------------------- |
| Yêu cầu thay đổi thường xuyên (Scope Creep)  | Cao              | Trung bình      | Quy trình quản lý thay đổi chặt chẽ (Change Request), xác định rõ MVP, ưu tiên hóa backlog thường xuyên.                     |
| Tích hợp Hubspot/Hệ thống kế toán phức tạp   | Cao              | Trung bình      | Phân tích kỹ API, liên hệ support Hubspot/Kế toán sớm, xây dựng module riêng biệt, thử nghiệm tích hợp sớm (Proof of Concept). |
| Khó khăn trong việc thu thập dữ liệu chính xác (Cost, Revenue, Hợp đồng) | Cao | Cao | Xác định rõ nguồn dữ liệu, quy trình nhập liệu/đồng bộ, đào tạo người dùng nhập liệu, có cơ chế kiểm tra/đối chiếu dữ liệu. |
| Người dùng khó làm quen với hệ thống mới     | Trung bình       | Cao             | Thiết kế UI/UX thân thiện, tài liệu hướng dẫn rõ ràng, tổ chức training kỹ lưỡng, có kênh hỗ trợ nhiệt tình.                |
| Thiếu nguồn lực (Developer, QA)             | Cao              | Trung bình      | Lập kế hoạch nguồn lực thực tế, xác định ưu tiên MVP.                             |
| Vấn đề về hiệu năng khi dữ liệu lớn         | Trung bình       | Trung bình      | Thiết kế database tối ưu, indexing, caching, thực hiện Performance Testing kỹ lưỡng, tối ưu code/query.                  |
| Vấn đề bảo mật/phân quyền                   | Cao              | Trung bình      | Thiết kế phân quyền chi tiết ngay từ đầu, áp dụng các best practices về bảo mật, thực hiện Security Testing.               |

## 7. Giả định (Assumptions)

*   Các yêu cầu chức năng trong `Requirments.md` là tương đối đầy đủ và chính xác cho giai đoạn MVP.
*   Có sự hợp tác và cung cấp thông tin kịp thời từ các bên liên quan (Sales, Trưởng bộ phận, Leaders...).
*   Có sẵn API hoặc cơ chế để tích hợp/lấy dữ liệu từ Hubspot và hệ thống Kế toán (hoặc có phương án nhập liệu thủ công/import file khả thi).
*   Nguồn lực (nhân sự, ngân sách) được đảm bảo theo kế hoạch.
*   Có hạ tầng phù hợp để triển khai ứng dụng (Cloud hoặc On-premise).

## 8. Tiêu chí Thành công Dự án (Success Criteria)

*   Hệ thống MVP được triển khai đúng thời hạn và trong ngân sách dự kiến.
*   Các chức năng chính của MVP hoạt động ổn định và đáp ứng đúng yêu cầu nghiệp vụ cốt lõi.
*   Người dùng (Leaders, Sales, Admin...) có thể sử dụng hệ thống hiệu quả sau khi được đào tạo.
*   Dữ liệu trong hệ thống (Nhân sự, Cơ hội, Margin, Hợp đồng) được cập nhật tương đối đầy đủ và chính xác.
*   Hệ thống giúp cải thiện khả năng theo dõi KPI và ra quyết định dựa trên dữ liệu.
*   Nhận được phản hồi tích cực từ người dùng trong quá trình UAT và sau Go-live. 