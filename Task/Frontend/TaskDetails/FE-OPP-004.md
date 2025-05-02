# Task Detail: FE-OPP-004

## Thông tin chung
- **ID**: FE-OPP-004
- **Tên task**: Xây dựng Hubspot Sync controls
- **Độ ưu tiên**: Thấp
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-OPP-01](../../../assets/frontend/img/MH-OPP-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-OPP-01.md](../../../DD/SCREEN/MH-OPP-01.md)
  - API: [API-OPP-005](../../../DD/API/API-OPP-005.md), [API-OPP-006](../../../DD/API/API-OPP-006.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-OPP-001: Xây dựng Opportunity List Page (MH-OPP-01)

## Mô tả
Xây dựng các thành phần giao diện người dùng (UI controls) để quản lý, theo dõi và kích hoạt quá trình đồng bộ dữ liệu cơ hội kinh doanh giữa hệ thống nội bộ và Hubspot CRM. Các thành phần này bao gồm nút kích hoạt đồng bộ thủ công, giao diện xem logs đồng bộ, hiển thị trạng thái đồng bộ, và các thông báo lỗi. Chỉ người dùng có quyền phù hợp (Admin, General Manager) mới có thể truy cập và sử dụng các tính năng này. Việc tích hợp Hubspot là quan trọng để đảm bảo dữ liệu cơ hội luôn được cập nhật đồng bộ giữa hai hệ thống.

## Yêu cầu kỹ thuật

### 1. Manual Sync Trigger

#### UI/UX
- Nút "Đồng bộ thủ công" nổi bật và dễ nhận biết trên màn hình MH-OPP-01
- Chỉ hiển thị cho người dùng có quyền (Admin, General Manager)
- Dialog xác nhận khi người dùng click vào nút để tránh trigger vô tình
- Animation loading hiển thị khi đang thực hiện đồng bộ
- Toast notification hiển thị kết quả đồng bộ (thành công/thất bại, số lượng records)
- Disabled state khi quá trình đồng bộ đang diễn ra

#### Functionality
- Kích hoạt API đồng bộ thủ công khi click
- Xử lý xác nhận người dùng trước khi thực hiện
- Hiển thị tiến trình đồng bộ (nếu có thể)
- Hiển thị kết quả đồng bộ (số lượng records thêm mới, cập nhật, lỗi)
- Phân quyền người dùng (chỉ Admin và General Manager được phép kích hoạt)
- Kiểm tra và xử lý các trường hợp đồng bộ đang diễn ra

### 2. Sync Status Indicator

#### UI/UX
- Indicator hiển thị thời gian đồng bộ gần nhất (VD: "Cập nhật lần cuối: 5 phút trước")
- Trạng thái hiện tại của đồng bộ (idle, in progress, error)
- Visual indicator cho các trạng thái khác nhau (màu sắc, icon)
- Tooltip hiển thị thông tin chi tiết hơn khi hover

#### Functionality
- Lấy thông tin thời gian đồng bộ gần nhất và trạng thái từ API
- Cập nhật trạng thái realtime khi có quá trình đồng bộ đang diễn ra
- Tính toán và hiển thị thời gian relative (VD: "5 phút trước", "1 giờ trước")
- Định kỳ kiểm tra trạng thái đồng bộ và cập nhật UI (polling)
- Hiển thị cảnh báo nếu đồng bộ tự động gặp lỗi hoặc không chạy trong một khoảng thời gian dài

### 3. Sync Logs Viewer

#### UI/UX
- Button/link "Xem logs đồng bộ" (chỉ hiển thị cho Admin)
- Modal/dialog hiển thị logs đồng bộ khi click
- Danh sách logs theo thứ tự thời gian (mới nhất trên cùng)
- Mỗi log entry hiển thị:
  - Thời gian đồng bộ
  - Loại đồng bộ (tự động, thủ công)
  - Người kích hoạt (nếu là thủ công)
  - Trạng thái (thành công, một phần, thất bại)
  - Tóm tắt kết quả (số lượng records thêm mới/cập nhật/lỗi)
- Filter logs theo loại, trạng thái, khoảng thời gian
- Pagination cho danh sách dài

#### Functionality
- Lấy dữ liệu logs từ API
- Hỗ trợ pagination và filtering
- Hiển thị chi tiết log khi click vào một mục
- Xem danh sách lỗi cụ thể (nếu có)
- Xuất logs ra file (CSV/Excel) nếu cần
- Phân quyền chặt chẽ (chỉ Admin được xem toàn bộ logs)

### 4. Error Reporting & Handling

#### UI/UX
- Alert banner hiển thị khi có lỗi đồng bộ gần đây
- Modal hiển thị danh sách các records gặp lỗi khi đồng bộ
- Chi tiết lỗi rõ ràng, dễ hiểu
- Tùy chọn "Thử lại" cho các records lỗi

#### Functionality
- Lấy và hiển thị danh sách lỗi đồng bộ từ API
- Phân loại và nhóm các lỗi theo loại
- Cung cấp hướng dẫn khắc phục cho các lỗi phổ biến
- Chức năng thử lại đồng bộ cho các records cụ thể
- Gửi thông báo cho Admin khi có lỗi nghiêm trọng
- Log đầy đủ thông tin lỗi để troubleshooting

### 5. Phân quyền

- Admin: Toàn quyền truy cập tất cả tính năng (manual sync, view logs, error handling)
- General Manager (課長): Có quyền kích hoạt đồng bộ thủ công, xem thông tin trạng thái
- Team Leader (部長): Chỉ xem thông tin trạng thái đồng bộ gần nhất
- Sales/Other: Chỉ xem thông tin trạng thái đồng bộ gần nhất

## Xử lý State và Performance

### State Management
- Sử dụng global state để quản lý trạng thái đồng bộ
- Lưu và quản lý trạng thái logs và filters
- Optimistic UI updates trong quá trình đồng bộ
- Nếu user đã tự đồng bộ, block các thao tác đồng bộ khác trong một khoảng thời gian

### API Integration
- Kết nối với `API-OPP-005` (`POST /api/v1/hubspot/sync/manual`) để kích hoạt đồng bộ thủ công
- Kết nối với `API-OPP-006` (`GET /api/v1/hubspot/sync/logs`) để lấy logs đồng bộ
- Implement polling để cập nhật trạng thái đồng bộ
- WebSocket hoặc Server-Sent Events (nếu cần) để cập nhật realtime cho quá trình đồng bộ dài
- Error handling và retry strategy

### Performance Considerations
- Tối ưu việc lấy và hiển thị logs (pagination, lazy loading)
- Tránh blocking UI trong quá trình đồng bộ
- Debounce cho các thao tác tìm kiếm/lọc logs
- Caching logs để tránh gọi API không cần thiết

## Các Task liên quan
- FE-OPP-001: Opportunity List Page (hiển thị dữ liệu đồng bộ từ Hubspot)
- FE-OPP-002: Opportunity Detail Page (hiển thị thông tin chi tiết từ Hubspot)
- FE-ADM-003: System Configuration Page (cấu hình kết nối Hubspot)

## Tiêu chí chấp nhận
1. Manual Sync Trigger:
   - Nút đồng bộ thủ công hiển thị đúng cho các vai trò được phép
   - Dialog xác nhận hiển thị rõ ràng trước khi thực hiện
   - Kích hoạt đồng bộ thành công và hiển thị kết quả
   - Hiển thị loading state trong quá trình đồng bộ

2. Sync Status Indicator:
   - Hiển thị chính xác thời gian đồng bộ gần nhất
   - Cập nhật trạng thái realtime khi có quá trình đồng bộ
   - Hiển thị visual indicators phù hợp cho các trạng thái
   - Tooltip hiển thị đầy đủ thông tin chi tiết

3. Sync Logs Viewer:
   - Modal hiển thị logs với đầy đủ thông tin
   - Pagination và filtering hoạt động chính xác
   - Hiển thị chi tiết log khi được chọn
   - Export logs hoạt động nếu được triển khai

4. Error Reporting:
   - Alert banner hiển thị khi có lỗi đồng bộ
   - Modal chi tiết lỗi hiển thị đầy đủ thông tin
   - Các tùy chọn xử lý lỗi hoạt động đúng
   - Hướng dẫn khắc phục lỗi rõ ràng

5. Phân quyền:
   - Các chức năng chỉ hiển thị cho người dùng có quyền
   - Admin có thể truy cập tất cả tính năng
   - General Manager chỉ có thể kích hoạt đồng bộ
   - Không lộ thông tin nhạy cảm cho người dùng không có quyền

6. Performance:
   - Thời gian phản hồi nhanh cho các thao tác
   - Không blocking UI khi đang đồng bộ
   - Hiệu suất tốt khi hiển thị logs số lượng lớn
   - Cập nhật trạng thái kịp thời

## Resources
- [Tài liệu thiết kế màn hình MH-OPP-01](../../../DD/SCREEN/MH-OPP-01.md)
- [Design mockup MH-OPP-01](../../../assets/frontend/img/MH-OPP-01.png)
- [Danh sách API - Opportunity Management](../../../BD/API/api_list.md#3-quản-lý-cơ-hội-kinh-doanh)
- [Chức năng liên quan: F-3.1, F-3.2 (Đồng bộ Hubspot)](../../../BD/FunctionDesign/FunctionList.md) 