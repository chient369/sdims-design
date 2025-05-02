# Task BE-API-HRM-008: Triển khai Employee Suggestion API

## Thông tin chung
- **Độ ưu tiên**: Thấp
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
    - [BE-CORE-002: Module Authentication & Authorization](BE-CORE-002.md)
    - [BE-CORE-003: Module Validation & Exception Handling](BE-CORE-003.md)
    - [BE-CORE-004: Module Logger](BE-CORE-004.md)
  - Database Models:
    - [BE-DB-002: Employee Models](BE-DB-002.md)
  - Tasks liên quan:
    - [BE-API-HRM-001: Triển khai Employee List API](BE-API-HRM-001.md)
    - [BE-API-HRM-003: Triển khai Employee Skill APIs](BE-API-HRM-003.md)
    - [BE-API-HRM-004: Triển khai Skill & Category APIs](BE-API-HRM-004.md)
    - [BE-API-HRM-005: Triển khai Employee Status & Assignment APIs](BE-API-HRM-005.md)
  - Yêu cầu chức năng: 
    - [F-1.7: Tìm kiếm Nhân sự theo Skills](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.8: Gợi ý Nhân sự phù hợp](../../../BD/FunctionDesign/FunctionList.md)
    - [F-1.12: Báo cáo Tỷ lệ Sử dụng Nguồn lực](../../../BD/FunctionDesign/FunctionList.md)

## Mô tả
Triển khai API để gợi ý nhân viên phù hợp dựa trên yêu cầu kỹ năng, trạng thái, và các tiêu chí khác. API này giúp Leader/TP nhanh chóng tìm ra nhân viên phù hợp cho dự án hoặc công việc mới, dựa trên matching algorithm thông minh kết hợp nhiều yếu tố khác nhau.

## Chi tiết công việc
1. **Triển khai API Get Employee Suggestions**:
   - Xây dựng Lambda handler cho endpoint `/api/v1/employee-suggestions` với method POST.
   - Thiết kế request schema với các tham số:
     - required_skills: Array của {skill_id, level} - các kỹ năng yêu cầu
     - preferred_skills: Array của {skill_id, level} - các kỹ năng ưu tiên (không bắt buộc)
     - availability_date: Ngày cần nhân viên (default: ngay)
     - duration_months: Thời lượng dự kiến (months)
     - minimum_experience: Số năm kinh nghiệm tối thiểu
     - team_id: Filter theo team (optional)
     - limit: Số lượng kết quả trả về (default: 10)
   - Áp dụng phân quyền (chỉ Leader/TP/Admin có quyền sử dụng).
   - Format response với ranked list của nhân viên phù hợp.

2. **Triển khai Matching Algorithm**:
   - Xây dựng thuật toán matching dựa trên nhiều tiêu chí:
     - Skill matching score: So khớp skills yêu cầu vs skills của nhân viên
     - Availability score: Mức độ khả dụng của nhân viên (dựa trên project assignments)
     - Experience score: Kinh nghiệm tổng thể và kinh nghiệm với các skills yêu cầu
     - Team context score: Đánh giá dựa trên team và khả năng làm việc nhóm
   - Triển khai weighted scoring system để tính điểm tổng hợp.
   - Cân bằng giữa các yếu tố khác nhau để đưa ra kết quả phù hợp nhất.

3. **Triển khai Required Skills Matching**:
   - Xây dựng logic để so khớp required skills với skills của nhân viên:
     - Kiểm tra nhân viên có tất cả các required skills hay không
     - So sánh level của mỗi skill với yêu cầu
     - Tính điểm cho mỗi skill match
   - Đảm bảo nhân viên không thiếu kỹ năng quan trọng nào.
   - Xử lý logic để đánh giá "close matches" (ví dụ skill level thấp hơn một chút).

4. **Triển khai Preferred Skills Bonus**:
   - Xây dựng logic để tính điểm bonus cho các preferred skills:
     - Kiểm tra nhân viên có preferred skills hay không
     - Tính điểm bonus cho mỗi preferred skill match
     - Áp dụng trọng số thấp hơn so với required skills
   - Đảm bảo preferred skills chỉ là yếu tố cộng thêm, không phải yếu tố loại trừ.

5. **Triển khai Availability Checking**:
   - Xây dựng logic để kiểm tra và đánh giá tính khả dụng của nhân viên:
     - Kiểm tra trạng thái hiện tại (active, bench, etc.)
     - Kiểm tra project assignments và utilization rate
     - Dự đoán available date dựa trên end_date của các dự án hiện tại
     - Tính available capacity dựa trên utilization hiện tại
   - Ưu tiên nhân viên hoàn toàn available hoặc sắp available.

6. **Triển khai Experience Evaluation**:
   - Xây dựng logic đánh giá kinh nghiệm:
     - Tính toán tổng số năm kinh nghiệm
     - Đánh giá kinh nghiệm cụ thể với các required skills
     - Xem xét project history liên quan đến skills yêu cầu
   - Tạo trọng số phù hợp cho yếu tố kinh nghiệm.

7. **Triển khai Ranking và Sorting**:
   - Xây dựng logic để tính điểm tổng hợp cho mỗi nhân viên.
   - Sắp xếp kết quả theo điểm từ cao xuống thấp.
   - Bổ sung thông tin matching reason cho mỗi nhân viên được gợi ý.
   - Format response với các thông tin:
     - Thông tin cơ bản của nhân viên
     - Matching score và breakdown
     - Availability information
     - Skill matching details
     - Available date dự kiến
     - Lý do được gợi ý

8. **Triển khai Caching và Performance Optimization**:
   - Đảm bảo API có thể xử lý nhiều requests cùng lúc.
   - Cân nhắc caching intermediate results cho các queries phổ biến.
   - Tối ưu database queries để giảm latency.
   - Đảm bảo thuật toán matching có thể xử lý dataset lớn một cách hiệu quả.

## Đầu ra dự kiến
- Lambda handler function cho Employee Suggestion API.
- Advanced matching algorithm với weighted scoring system.
- Components cho skills matching, availability checking, và experience evaluation.
- Ranking và sorting logic.
- Optimized database queries cho performance.
- Caching mechanism (nếu cần).
- Unit tests cho các matching scenarios khác nhau.
- Documentation về cách sử dụng API, request schema, và scoring system. 