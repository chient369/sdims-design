# Task Detail: FE-OPP-003

## Thông tin chung
- **ID**: FE-OPP-003
- **Tên task**: Xây dựng Note/Activity components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-OPP-02 - TAB Lịch sử tương tác](../../../assets/frontend/img/MH-OPP-02%20-%20TAB%20Lịch%20sử%20tương%20tác.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-OPP-02.md](../../../DD/SCREEN/MH-OPP-02.md)
  - API: [API-OPP-003](../../../DD/API/API-OPP-003.md), [API-OPP-004](../../../DD/API/API-OPP-004.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-OPP-002: Xây dựng Opportunity Detail Page (MH-OPP-02)

## Mô tả
Xây dựng bộ component để quản lý ghi chú (notes) và hoạt động (activities) cho cơ hội kinh doanh, tập trung vào tab "Lịch sử tương tác" trong màn hình chi tiết cơ hội (MH-OPP-02). Bộ component này bao gồm form nhập ghi chú mới với rich text editor, timeline hiển thị lịch sử hoạt động, và các công cụ lọc/tìm kiếm. Các component này sẽ giúp Sales, Leader và các bên liên quan dễ dàng ghi lại các tương tác với khách hàng, theo dõi lịch sử cập nhật, và đảm bảo không bỏ lỡ các follow-up quan trọng.

## Yêu cầu kỹ thuật

### 1. Note Input Form Component

#### UI/UX
- Form nhập ghi chú với layout rõ ràng, trực quan
- Các trường nhập liệu:
  - Loại ghi chú/hoạt động (dropdown: Call, Email, Meeting, Internal Note, etc.)
  - Nội dung chính (text area với hỗ trợ rich text)
  - Tùy chọn gắn tag/label (multi-select)
  - Checkbox để xác định "tính là tương tác" (cập nhật Last Interaction Date)
- Nút "Thêm ghi chú" nổi bật
- Hiển thị thông báo thành công/lỗi sau khi thêm
- Responsive design phù hợp với các kích thước màn hình
- Animation mượt mà cho việc hiển thị/ẩn form

#### Functionality
- Validation form với phản hồi trực quan
- Rich text editor cho phép định dạng văn bản cơ bản (bold, italic, bullet points)
- Kiểm tra quyền người dùng trước khi cho phép thêm ghi chú
- Gửi dữ liệu lên API và xử lý response
- Cập nhật Last Interaction Date của cơ hội nếu "tính là tương tác" được chọn
- Tự động thêm thông tin người tạo và timestamp
- Hỗ trợ lưu draft ghi chú khi người dùng rời khỏi trang

### 2. Activity Timeline Component

#### UI/UX
- Timeline hiển thị các ghi chú/hoạt động theo thời gian (mới nhất lên trên)
- Mỗi mục trong timeline hiển thị:
  - Icon/biểu tượng cho loại hoạt động
  - Thời gian tạo (định dạng thân thiện, VD: "2 giờ trước", "Hôm qua 15:30")
  - Tên người tạo với avatar
  - Nội dung ghi chú (có thể thu gọn/mở rộng nếu dài)
  - Tag/label (nếu có) với màu sắc phân biệt
- Phân trang cho danh sách dài
- Visual indicator cho các ghi chú mới chưa đọc
- Empty state khi chưa có ghi chú nào
- Loading state khi đang tải dữ liệu

#### Functionality
- Lấy dữ liệu lịch sử hoạt động từ API
- Sắp xếp theo thời gian (mới nhất trên cùng)
- Pagination và lazy loading khi scroll
- Render nội dung rich text đúng định dạng
- Hiển thị/ẩn chi tiết khi click vào mục
- Lọc theo loại hoạt động và người tạo
- Mark as read functionality cho các ghi chú mới

### 3. Advanced Filtering & Search

#### UI/UX
- Khu vực filter với giao diện collapsed/expanded
- Các bộ lọc:
  - Loại hoạt động (checkbox group)
  - Khoảng thời gian (date range picker)
  - Người tạo (dropdown/multi-select)
  - Tag/label (multi-select với màu sắc)
- Ô tìm kiếm theo nội dung ghi chú
- Hiển thị số lượng filter đang active
- Nút xóa tất cả bộ lọc

#### Functionality
- Áp dụng filter realtime hoặc khi click "Áp dụng"
- Lưu trạng thái filter trong session/local storage
- Tìm kiếm text trong nội dung ghi chú
- Kết hợp nhiều điều kiện lọc (AND/OR logic)
- Cập nhật URL parameters để có thể bookmark/share view đã lọc

### 4. Integration với Opportunity Detail Page

#### UI/UX
- Tab "Lịch sử tương tác" được highlight khi có ghi chú mới
- Counter hiển thị số lượng ghi chú chưa đọc
- "Quick add" button ở header của tab
- Consistent styling với các phần khác của opportunity detail

#### Functionality
- Update Last Interaction Date của cơ hội khi thêm ghi chú mới có tính là tương tác
- Tự động cập nhật Follow-up Status (Red/Yellow/Green) dựa trên Last Interaction Date
- Đồng bộ hiển thị thông tin Last Interaction Date với các phần khác của opportunity detail
- Notification cho các bên liên quan khi có ghi chú mới quan trọng
- Permission-based visibility và editing

## Xử lý State và Performance

### State Management
- Sử dụng global state để lưu trạng thái của timeline và filter
- Local state cho form input và validation
- Optimistic UI updates khi thêm ghi chú mới
- Caching dữ liệu để giảm số lần gọi API

### API Integration
- Kết nối với `API-OPP-003` (`GET /api/v1/opportunities/{id}/activities`) để lấy lịch sử
- Kết nối với `API-OPP-004` (`POST /api/v1/opportunities/{id}/activities`) để thêm ghi chú
- Implement pagination và filtering trên API calls
- Error handling và retry mechanism
- Debounce cho search functionality

### Performance Considerations
- Lazy loading cho danh sách dài
- Virtualized list cho hiệu năng tốt với nhiều ghi chú
- Tối ưu rich text rendering
- Implement read/unread tracking hiệu quả
- Caching và invalidation strategy phù hợp

## Các Task liên quan
- FE-OPP-002: Opportunity Detail Page (chứa các component này trong tab Lịch sử tương tác)
- FE-OPP-001: Opportunity List Page (hiển thị Last Interaction Date và Follow-up Status)
- FE-DSH-004: Opportunity widgets (sử dụng Follow-up Status được cập nhật từ activities)

## Tiêu chí chấp nhận
1. Note Input Form:
   - Form hiển thị và hoạt động chính xác với đầy đủ các trường
   - Rich text editor hoạt động đúng với các chức năng cơ bản
   - Validation hiệu quả và hiển thị lỗi rõ ràng
   - Thêm ghi chú thành công và hiển thị trong timeline

2. Activity Timeline:
   - Hiển thị chính xác danh sách ghi chú/hoạt động theo thứ tự thời gian
   - Render đúng nội dung rich text với định dạng
   - Pagination/infinite scroll hoạt động mượt mà
   - Hiển thị đầy đủ thông tin (người tạo, thời gian, loại, nội dung, tag)

3. Filtering & Search:
   - Các bộ lọc hoạt động chính xác và kết hợp được với nhau
   - Tìm kiếm text hoạt động trong nội dung ghi chú
   - Lưu và áp dụng lại được trạng thái filter
   - Clear filters hoạt động đúng

4. Integration:
   - Last Interaction Date được cập nhật đúng khi thêm ghi chú tính là tương tác
   - Follow-up Status được tính toán và cập nhật chính xác
   - Notifications được gửi khi có ghi chú mới quan trọng
   - Phân quyền được áp dụng đúng (ai có thể xem/thêm ghi chú)

5. Performance:
   - Thời gian tải ban đầu < 2 giây
   - Thêm ghi chú và hiển thị trong timeline < 1 giây
   - Không có hiện tượng lag khi scroll timeline dài
   - Rich text editor phản hồi nhanh khi nhập liệu

## Resources
- [Thiết kế màn hình MH-OPP-02](../../../DD/SCREEN/MH-OPP-02.md)
- [MH-OPP-02 - TAB Lịch sử tương tác](../../../assets/frontend/img/MH-OPP-02%20-%20TAB%20Lịch%20sử%20tương%20tác.png)
- [Danh sách API - Opportunity Management](../../../BD/API/api_list.md#3-quản-lý-cơ-hội-kinh-doanh)
- [Chức năng liên quan: F-3.4, F-3.5, F-3.6 (Ghi chú/Log và Follow-up Status)](../../../BD/FunctionDesign/FunctionList.md) 