# Task Detail: FE-DSH-004

## Thông tin chung
- **ID**: FE-DSH-004
- **Tên task**: Xây dựng Opportunity widgets
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-DSH-01](../../../assets/frontend/img/MH-DSH-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-DSH-01.md](../../../DD/SCREEN/MH-DSH-01.md)
  - API: [API-OPP-001](../../../DD/API/API-OPP-001.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-DSH-001: Xây dựng Dashboard Page (MH-DSH-01)

## Mô tả
Xây dựng các widget liên quan đến cơ hội kinh doanh (opportunities) trên Dashboard chính, bao gồm danh sách cơ hội mới nhất, biểu đồ phân bố cơ hội theo giai đoạn (funnel chart), và các chỉ số theo dõi trạng thái cập nhật (follow-up status). Các widget này sẽ cung cấp cái nhìn tổng quan về pipeline cơ hội kinh doanh, giúp sales team và quản lý nhanh chóng nắm bắt thông tin và ưu tiên các hoạt động follow-up quan trọng.

## Yêu cầu kỹ thuật

### 1. Widget W-OPP-01 - Cơ hội Mới Nhất

#### UI/UX
- Thiết kế dạng card với header, scrollable content area và footer
- Danh sách hiển thị 5-10 cơ hội được tạo/cập nhật gần đây nhất
- Mỗi item trong danh sách hiển thị:
  - Tên cơ hội và khách hàng
  - Giai đoạn hiện tại (Stage)
  - Trạng thái follow-up bằng visual indicator (Red/Yellow/Green)
  - Ngày cập nhật cuối
  - Người phụ trách (assigned leader)
- Color-coded indicators tương ứng với trạng thái follow-up
- Cung cấp link "Xem tất cả" ở footer

#### Functionality
- Lấy dữ liệu cơ hội từ API, sắp xếp theo ngày tạo/cập nhật
- Giới hạn hiển thị các cơ hội mới nhất (tuỳ theo thiết kế)
- Tính toán và hiển thị trạng thái follow-up dựa trên ngày cập nhật cuối
- Khi click vào một cơ hội, mở trang chi tiết cơ hội tương ứng
- Click "Xem tất cả" sẽ chuyển đến MH-OPP-01 (danh sách cơ hội)

### 2. Widget W-OPP-02 - Opportunity Funnel

#### UI/UX
- Thiết kế dạng card với header và content area chứa biểu đồ
- Hiển thị biểu đồ phễu (funnel chart) thể hiện phân bố cơ hội theo giai đoạn
- Các giai đoạn (từ trên xuống): Lead, Meeting, Demo, Proposal, Negotiation, Closed Won
- Mỗi giai đoạn hiển thị:
  - Số lượng cơ hội trong giai đoạn đó
  - Tỷ lệ % so với tổng số cơ hội
  - Giá trị dự kiến (tổng value của các cơ hội trong giai đoạn)
- Sử dụng màu sắc khác nhau cho mỗi giai đoạn
- Interactive: hover/click để xem chi tiết về mỗi giai đoạn

#### Functionality
- Lấy dữ liệu phân bố cơ hội theo giai đoạn từ API
- Tính toán tỷ lệ % và tổng giá trị cho mỗi giai đoạn
- Khi hover vào một giai đoạn, hiển thị tooltip với thông tin chi tiết
- Khi click vào một giai đoạn, chuyển đến MH-OPP-01 với filter giai đoạn đã được áp dụng
- Tùy chọn filter theo thời gian (Quarter hiện tại, 6 tháng, 12 tháng)

### 3. Widget W-OPP-03 - Follow-up Status Summary

#### UI/UX
- Thiết kế dạng card với header và content area
- Hiển thị summary về trạng thái follow-up của tất cả cơ hội hiện tại
- Biểu đồ tròn (pie/donut chart) thể hiện tỷ lệ:
  - Green: Đã follow-up gần đây
  - Yellow: Cần follow-up sớm
  - Red: Quá hạn follow-up
- Hiển thị số lượng cụ thể bên cạnh biểu đồ
- Đặc biệt nhấn mạnh các cơ hội "Red" cần attention ngay

#### Functionality
- Lấy dữ liệu trạng thái follow-up từ API
- Tính toán phân loại dựa trên ngưỡng cấu hình (VD: Red: >14 ngày, Yellow: 7-14 ngày, Green: <7 ngày)
- Khi click vào một phần của biểu đồ, chuyển đến MH-OPP-01 với filter trạng thái follow-up tương ứng
- Có thể lọc theo người phụ trách/team (để manager xem tình trạng follow-up của từng team)

### 4. Integration với Dashboard

#### UI/UX
- Layout nhất quán với các widget khác trên dashboard
- Responsive design phù hợp với các kích thước màn hình
- Consistent styling với theme chung của hệ thống
- Loading, empty và error states có thiết kế phù hợp

#### Functionality
- Các widget được cập nhật khi thay đổi filter chung của dashboard (thời gian, team)
- Single API call cho tất cả dữ liệu opportunity widgets (nếu có thể) để tối ưu performance
- Shared state giữa các widget khi cần thiết
- Caching dữ liệu để giảm số lượng API calls không cần thiết

### 5. Phân quyền

- Admin: Thấy tất cả widget và dữ liệu
- Sales/Marketing Manager: Thấy tất cả widget và dữ liệu trong phạm vi team
- Sales Person: Có thể giới hạn chỉ thấy các cơ hội họ được phân công
- Team Leader: Thấy dữ liệu của team và các cơ hội họ được assign
- Các role khác: Tùy chỉnh theo policy của doanh nghiệp

## Xử lý State và Performance

### State Management
- Sử dụng React Query hoặc Redux để quản lý state và cache API data
- Tạo custom hooks để xử lý dữ liệu cho các widget
- Implement lazy loading cho các widget khi cần thiết
- Sử dụng memoization để tránh re-rendering không cần thiết

### API Integration
- Kết nối với các endpoints:
  - `GET /api/v1/opportunities` (API-OPP-001) với các filter phù hợp
  - Các endpoints tổng hợp khác nếu có
- Implement caching và invalidation policy phù hợp
- Error handling và retry logic
- Tối ưu số lượng API calls

## Các Task liên quan
- FE-DSH-001: Dashboard Page (parent task)
- FE-OPP-001: Opportunity List Page (liên kết từ các widget)
- FE-OPP-002: Opportunity Detail Page (liên kết khi click vào một cơ hội cụ thể)

## Tiêu chí chấp nhận
1. Các widget opportunity hiển thị đúng theo thiết kế trong MH-DSH-01.png
2. Dữ liệu được load thành công từ API endpoints và hiển thị chính xác
3. Biểu đồ funnel và pie chart hiển thị đúng dữ liệu và có interactive elements
4. Navigation đến các trang chi tiết hoạt động chính xác với filter đã được áp dụng
5. Phân quyền được áp dụng đúng, mỗi vai trò chỉ thấy dữ liệu được phép
6. Các widget cập nhật khi filter dashboard thay đổi (khoảng thời gian, team)
7. Các trạng thái loading/empty/error được xử lý hợp lý
8. Performance tốt, không gây chậm dashboard khi load dữ liệu
9. Responsive design phù hợp với các kích thước màn hình khác nhau
10. Hiển thị chính xác trạng thái follow-up theo ngưỡng cấu hình

## Resources
- [Thiết kế màn hình: MH-DSH-01 - Dashboard](../../../BD/ScreenDesign/ScreenBD.md#mh-dsh-01)
- [Screen Design Diagram: MH-DSH-01](../../../BD/ScreenDesign/diagram/MH-DSH-01.puml)
- [Thiết kế màn hình: MH-OPP-01 - Danh sách Cơ hội Kinh doanh](../../../BD/ScreenDesign/ScreenBD.md#mh-opp-01)
- [API Opportunity Documentation](../../../BD/API/api_list.md#4-quản-lý-cơ-hội-kinh-doanh)
- [Chức năng liên quan: F-3.6, F-3.8 (Hiển thị Trạng thái Follow-up, Xem/Lọc/Tìm kiếm Cơ hội)](../../../BD/FunctionDesign/FunctionList.md) 