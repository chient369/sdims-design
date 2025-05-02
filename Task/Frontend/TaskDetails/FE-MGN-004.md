# Task Detail: FE-MGN-004

## Thông tin chung
- **ID**: FE-MGN-004
- **Tên task**: Xây dựng Margin Chart components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 4 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-MGN-01](../../../assets/frontend/img/MH-MGN-01.png), [MH-DSH-01](../../../assets/frontend/img/MH-DSH-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-MGN-01.md](../../../DD/SCREEN/MH-MGN-01.md)
  - API: [API-MGN-003](../../../DD/API/API-MGN-003.md), [API-MGN-004](../../../DD/API/API-MGN-004.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-MGN-001: Xây dựng Margin List Page (MH-MGN-01)

## Mô tả
Xây dựng các thành phần biểu đồ (chart components) để hiển thị dữ liệu margin theo nhiều góc nhìn khác nhau, bao gồm biểu đồ xu hướng (trend chart) theo thời gian, biểu đồ phân tích (breakdown chart) theo team/bộ phận và các widget tổng hợp cho dashboard. Các biểu đồ này sẽ được tích hợp vào màn hình MH-MGN-01 và MH-DSH-01 giúp người dùng có cái nhìn trực quan về hiệu suất tài chính của đội ngũ nhân sự. Chỉ người dùng có quyền phù hợp (Team Leader, General Manager) mới có thể xem các biểu đồ này theo phạm vi quản lý tương ứng.

## Yêu cầu kỹ thuật

### 1. Margin Trend Chart

#### UI/UX
- Biểu đồ đường (line chart) hiển thị xu hướng margin theo thời gian
- Hỗ trợ hiển thị nhiều đường (multiple lines) để so sánh giữa các team hoặc so với target
- Trục X hiển thị thời gian (tháng, quý, năm tùy theo bộ lọc)
- Trục Y hiển thị tỷ lệ margin (%)
- Tooltips hiển thị thông tin chi tiết khi hover
- Tùy chọn để ẩn/hiện các đường trên biểu đồ
- Legend rõ ràng với màu sắc phân biệt cho mỗi đường
- Hiển thị đường target/baseline (nếu có) để so sánh

#### Functionality
- Lấy dữ liệu margin theo thời gian từ API
- Tính toán giá trị trung bình cho mỗi khoảng thời gian
- Khi click vào một điểm trên biểu đồ, hiển thị chi tiết (drill down) 
- Hỗ trợ zoom in/out trên trục thời gian
- Export dữ liệu biểu đồ ra dạng CSV/Excel
- Lưu trạng thái hiển thị (các đường đang hiển thị/ẩn)

### 2. Margin Breakdown Chart

#### UI/UX
- Biểu đồ cột/thanh (bar chart) hoặc biểu đồ tròn (pie/donut chart) hiển thị phân bố margin
- Phân chia rõ ràng theo team hoặc bộ phận
- Sử dụng màu sắc nhất quán với trạng thái margin (Red/Yellow/Green)
- Hiển thị giá trị cụ thể (%) trên mỗi phần của biểu đồ
- Tooltips với thông tin chi tiết (tổng nhân viên, avg margin, tổng revenue, tổng cost)
- Animation mượt mà khi chuyển đổi dữ liệu

#### Functionality
- Lấy dữ liệu phân bố margin từ API
- Tính toán tỷ lệ phần trăm cho mỗi phân đoạn
- Khi click vào một phân đoạn, hiển thị chi tiết (drill down) hoặc chuyển đến danh sách đã lọc
- Tùy chọn chuyển đổi giữa các loại biểu đồ (pie/bar) nếu phù hợp
- Sắp xếp dữ liệu theo nhiều tiêu chí (margin cao nhất, thấp nhất)
- Export dữ liệu biểu đồ ra dạng CSV/Excel

### 3. Margin Status Distribution Widget

#### UI/UX
- Widget hiển thị phân bố nhân viên theo trạng thái margin (Red/Yellow/Green)
- Dạng đồ thị tròn (pie/donut chart) hoặc stacked bar chart
- Hiển thị số lượng và tỷ lệ % cho mỗi trạng thái
- Màu sắc theo chuẩn của trạng thái (Red/Yellow/Green)
- Clickable để chuyển đến danh sách đã lọc theo trạng thái

#### Functionality
- Lấy dữ liệu phân bố trạng thái margin từ API
- Tính toán tỷ lệ phần trăm cho mỗi trạng thái
- Chuyển đến màn hình danh sách với filter tương ứng khi click
- Tự động cập nhật khi thay đổi bộ lọc (kỳ, team)
- Hiển thị số lượng nhân viên cho mỗi trạng thái

### 4. Integration với Dashboard và Margin List Page

#### UI/UX
- Thiết kế nhất quán với các phần khác của hệ thống
- Responsive design để hiển thị tốt trên các kích thước màn hình
- Animated transitions khi dữ liệu thay đổi
- Loading state với skeleton hoặc spinner
- Error state với thông báo phù hợp và retry option

#### Functionality
- Tích hợp với bộ lọc chung (kỳ, team) của các màn hình cha
- Đồng bộ trạng thái giữa các biểu đồ (nếu cần)
- Chia sẻ dữ liệu giữa các biểu đồ để tránh gọi API trùng lặp
- Lazy loading khi cần thiết để tối ưu hiệu suất
- Caching dữ liệu để giảm số lần gọi API

### 5. Phân quyền

- General Manager (課長): Xem được tất cả biểu đồ trong phạm vi bộ phận
- Team Leader (部長): Chỉ xem được biểu đồ cho team của mình
- Employee: Không có quyền xem các biểu đồ margin
- Admin: Có thể xem tất cả biểu đồ (cho mục đích hỗ trợ)

## Xử lý State và Performance

### State Management
- Sử dụng global state để lưu và chia sẻ dữ liệu giữa các chart components
- Quản lý state cho việc filter và chọn lựa hiển thị
- Caching query results để tránh gọi API lặp lại

### API Integration
- Kết nối với `API-MGN-003` (`GET /api/v1/margins/trends`) để lấy dữ liệu xu hướng
- Kết nối với `API-MGN-004` (`GET /api/v1/margins/breakdown`) để lấy dữ liệu phân tích
- Implement strategical polling hoặc invalidation để dữ liệu luôn mới nhất
- Xử lý cancel/abort các request khi component unmount hoặc params thay đổi

### Performance Considerations
- Tối ưu rendering và re-rendering của các chart components
- Lazy loading cho code của thư viện chart (nếu cần)
- Phân tích dataset lớn ở backend thay vì frontend
- Aggregation data ở server-side
- Sử dụng Web Workers cho các tính toán phức tạp (nếu cần)
- Sử dụng rendering throttling cho animation và transitions

## Các Task liên quan
- FE-MGN-001: Margin List Page (nơi tích hợp các biểu đồ)
- FE-DSH-003: Revenue/Margin widgets (sử dụng một số chart components được tạo)
- FE-MGN-002: Cost Input Form (nguồn dữ liệu chi phí cho biểu đồ)
- FE-MGN-003: Cost Import UI (nguồn dữ liệu chi phí cho biểu đồ)

## Tiêu chí chấp nhận
1. Margin Trend Chart:
   - Hiển thị chính xác dữ liệu xu hướng margin theo thời gian
   - Hỗ trợ đầy đủ các tùy chọn hiển thị (multiple lines, zoom, tooltips)
   - Phản hồi mượt mà khi tương tác
   - Drill-down functionality hoạt động chính xác

2. Margin Breakdown Chart:
   - Hiển thị chính xác dữ liệu phân bố margin
   - Màu sắc và trực quan phù hợp với trạng thái margin
   - Tooltips hiển thị đầy đủ thông tin chi tiết
   - Chuyển chính xác đến view chi tiết khi click

3. Margin Status Distribution Widget:
   - Hiển thị chính xác phân bố nhân viên theo trạng thái margin
   - Màu sắc theo đúng chuẩn trạng thái
   - Click để chuyển đến danh sách đã lọc hoạt động chính xác
   - Tự động cập nhật khi thay đổi bộ lọc

4. Integration với Dashboard và Margin List:
   - Các biểu đồ tích hợp mượt mà vào các màn hình cha
   - Responsive design hoạt động trên các kích thước màn hình
   - Loading và error states được xử lý phù hợp
   - Đồng bộ trạng thái giữa các biểu đồ

5. Phân quyền:
   - Chỉ hiển thị dữ liệu cho người dùng có quyền
   - Leader chỉ thấy dữ liệu team của họ
   - General Manager thấy dữ liệu bộ phận/toàn công ty

6. Performance:
   - Thời gian tải và render biểu đồ < 3 giây
   - Interaction và animation mượt mà
   - Không có memory leaks khi unmount components
   - Tối ưu số lần gọi API

## Resources
- [Tài liệu thiết kế màn hình MH-MGN-01](../../../DD/SCREEN/MH-MGN-01.md)
- [Tài liệu thiết kế màn hình MH-DSH-01](../../../DD/SCREEN/MH-DSH-01.md)
- [Design mockup MH-MGN-01](../../../assets/frontend/img/MH-MGN-01.png)
- [Danh sách API - Margin Management](../../../BD/API/api_list.md#2-quản-lý-hiệu-suất--margin)
- [Chức năng liên quan: F-2.3, F-2.4, F-2.5, F-2.6 (Margin)](../../../BD/FunctionDesign/FunctionList.md) 