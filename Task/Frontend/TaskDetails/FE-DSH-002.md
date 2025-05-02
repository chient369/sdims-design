# Task Detail: FE-DSH-002

## Thông tin chung
- **ID**: FE-DSH-002
- **Tên task**: Xây dựng HR metrics widgets
- **Độ ưu tiên**: Cao
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-DSH-01](../../../assets/frontend/img/MH-DSH-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-DSH-01.md](../../../DD/SCREEN/MH-DSH-01.md)
  - API: [API-HRM-001](../../../DD/API/API-HRM-001.md), [API-RPT-008](../../../DD/API/API-RPT-008.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-DSH-001: Xây dựng Dashboard Page (MH-DSH-01)

## Mô tả
Xây dựng các widget liên quan đến metrics nhân sự trên Dashboard chính, bao gồm thông tin về nhân viên bench, nhân viên sắp hết dự án, và tỷ lệ sử dụng nguồn lực. Các widget này sẽ cung cấp cái nhìn tổng quan về tình trạng nhân sự, giúp lãnh đạo và team leader nhanh chóng nắm bắt thông tin và đưa ra quyết định kịp thời.

## Yêu cầu kỹ thuật

### 1. Widget W-HRM-01 - Nhân sự Sẵn sàng (Bench)

#### UI/UX
- Thiết kế dạng card với header, content area và footer
- Hiển thị số lượng nhân viên đang ở trạng thái Available/Bench dưới dạng số lớn
- Biểu đồ tròn (pie/donut chart) thể hiện tỷ lệ phân bố nhân viên bench theo team/kỹ năng
- Cung cấp link "Xem chi tiết" ở footer để chuyển đến danh sách nhân viên với filter đã được áp dụng
- Hiển thị icon thích hợp (ví dụ: icon user hoặc team)
- Sử dụng màu sắc nhất quán với thiết kế chung

#### Functionality
- Lấy dữ liệu từ API endpoints liên quan đến nhân sự
- Tính toán số lượng nhân viên đang bench dựa trên trạng thái
- Phân loại nhân viên bench theo team hoặc kỹ năng để hiển thị trong biểu đồ
- Nếu click vào một phần của biểu đồ, navigate đến trang danh sách nhân viên với filter tương ứng
- Nếu click vào link "Xem chi tiết", chuyển đến trang MH-HRM-01 với filter status=bench

#### API Integration
- `GET /api/v1/employees` (API-HRM-001) với filter status=bench/available
- Xử lý dữ liệu trả về để hiển thị metrics phù hợp
- Implement caching thích hợp để tránh gọi API nhiều lần

### 2. Widget W-HRM-02 - Nhân sự Sắp hết Dự án

#### UI/UX
- Thiết kế dạng card với header và scrollable content area
- Hiển thị danh sách nhân viên sắp hết dự án (trong 30 ngày tới)
- Mỗi item trong danh sách hiển thị:
  - Tên nhân viên (có thể kèm avatar nhỏ)
  - Team/Department
  - Ngày kết thúc dự án
  - Số ngày còn lại (có thể hiển thị dạng badge với màu sắc khác nhau tùy độ khẩn cấp)
- Sắp xếp theo thời gian còn lại (gần nhất lên đầu)
- Cung cấp link "Xem tất cả" ở footer

#### Functionality
- Lấy dữ liệu về nhân viên và thông tin phân bổ dự án
- Tính toán ngày kết thúc dự án và số ngày còn lại
- Sắp xếp danh sách theo số ngày còn lại (ascending)
- Giới hạn hiển thị top N nhân viên sắp hết dự án nhất
- Click vào tên nhân viên sẽ mở trang chi tiết nhân viên đó
- Click vào "Xem tất cả" sẽ chuyển đến trang MH-HRM-01 với filter thích hợp

#### API Integration
- `GET /api/v1/employees` (API-HRM-001) với filter cho nhân viên sắp hết dự án
- Hoặc endpoint chuyên biệt nếu có (ví dụ: `/api/v1/employees/ending-soon`)

### 3. Widget W-HRM-03 - Tỷ lệ Sử dụng Nguồn lực (%)

#### UI/UX
- Thiết kế dạng card với header và content area
- Hiển thị % Utilization Rate trung bình dưới dạng số lớn
- Biểu đồ Gauge hoặc Donut hiển thị tỷ lệ này một cách trực quan
- Sử dụng màu sắc khác nhau để thể hiện mức độ (đỏ: thấp, vàng: trung bình, xanh: tốt)
- Có thể kèm theo chú thích nhỏ về sự thay đổi so với kỳ trước (ví dụ: "+2.5% so với tháng trước")
- Dropdown filter cho phép chọn team/bộ phận cụ thể

#### Functionality
- Lấy dữ liệu tỷ lệ sử dụng nguồn lực từ API
- Tính toán tỷ lệ trung bình và phân loại theo team nếu cần
- Cập nhật biểu đồ dựa trên team được chọn từ filter
- So sánh với dữ liệu kỳ trước để hiển thị % thay đổi
- Thay đổi màu sắc biểu đồ dựa trên ngưỡng cấu hình (ví dụ: <60%: đỏ, 60-80%: vàng, >80%: xanh)

#### API Integration
- `GET /api/v1/reports/utilization` (API-RPT-008) với filter team và thời gian
- Xử lý dữ liệu trả về để tính toán tỷ lệ trung bình và hiển thị trên biểu đồ

### Phân quyền
- Widget W-HRM-01, W-HRM-02, W-HRM-03 hiển thị cho: Admin, Trưởng bộ phận, Team Leader
- Team Leader chỉ thấy dữ liệu của team mình quản lý
- Trưởng bộ phận và Admin thấy dữ liệu của tất cả nhân viên/team
- Nhân viên thông thường không thấy các widget này

## Xử lý State và Performance

### State Management
- Sử dụng React Query hoặc Redux để quản lý state và cache API data
- Sử dụng thống nhất các filter đã được chọn trên dashboard để tối ưu số lần gọi API
- Implement loading, error, và empty states cho mỗi widget

### Performance Optimization
- Implement data memoization để tránh render lại không cần thiết
- Lazy load các components chart khi cần
- Áp dụng debounce cho các filter thay đổi thường xuyên
- Tối ưu kích thước bundle bằng cách import có chọn lọc từ các thư viện chart

## Các Task liên quan
- FE-DSH-001: Dashboard Page (parent task)
- FE-HRM-001: Employee List Page (liên kết khi click xem chi tiết)
- FE-HRM-002: Employee Detail Page (liên kết khi click vào tên nhân viên)

## Tiêu chí chấp nhận
1. Các widget HR metrics hiển thị đúng theo thiết kế trong MH-DSH-01.png
2. Dữ liệu được load thành công từ API endpoints và hiển thị chính xác
3. Biểu đồ trong các widget hiển thị đúng và có interactive elements (hover tooltips, click actions)
4. Navigation đến các trang chi tiết hoạt động chính xác với filter đã được áp dụng
5. Filter team/bộ phận trong widget W-HRM-03 hoạt động đúng
6. Hiển thị widgets theo phân quyền người dùng (chỉ hiển thị cho các vai trò phù hợp)
7. Các widget có trạng thái loading/empty/error được xử lý hợp lý
8. Performance tốt, không gây chậm dashboard khi load dữ liệu
9. Responsive design phù hợp với các kích thước màn hình khác nhau

## Resources
- [Thiết kế màn hình: MH-DSH-01 - Dashboard](../../../BD/ScreenDesign/ScreenBD.md#mh-dsh-01)
- [Screen Design Diagram: MH-DSH-01](../../../BD/ScreenDesign/diagram/MH-DSH-01.puml)
- [API Report Documentation](../../../BD/API/api_list.md#6-dashboard--báo-cáo)
- [API HRM Documentation](../../../BD/API/api_list.md#2-quản-lý-nhân-sự-hrm)
- [Chức năng liên quan: F-1.12, F-1.13 (Báo cáo tỷ lệ sử dụng nguồn lực, Cảnh báo hết dự án)](../../../BD/FunctionDesign/FunctionList.md) 