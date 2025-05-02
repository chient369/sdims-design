# Task Detail: FE-HRM-007

## Thông tin chung
- **ID**: FE-HRM-007
- **Tên task**: Xây dựng Employee Skills Search
- **Độ ưu tiên**: Trung bình
- **Estimate**: 4 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-01](../../../assets/frontend/img/MH-HRM-01.png), [MH-HRM-02 - TAB Kĩ năng chuyên môn](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20%20Kĩ%20năng%20chuyên%20môn.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-01.md](../../../DD/SCREEN/MH-HRM-01.md)
  - API: [API-HRM-006](../../../DD/API/API-HRM-006.md), [API-HRM-001](../../../DD/API/API-HRM-001.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components
  - FE-HRM-001: Xây dựng Employee List Page (MH-HRM-01)
  - FE-HRM-004: Xây dựng Skills Management UI (MH-HRM-04)

## Mô tả
Xây dựng giao diện tìm kiếm nhân viên nâng cao dựa trên kỹ năng (Skills), bao gồm các tính năng tìm kiếm theo nhiều tiêu chí như loại kỹ năng, tên kỹ năng, mức độ thành thạo, số năm kinh nghiệm, và trạng thái sẵn sàng của nhân viên. Tính năng này cho phép người dùng dễ dàng tìm kiếm nhân viên phù hợp cho dự án mới hoặc khách hàng dựa trên yêu cầu kỹ năng cụ thể. Component này sẽ được tích hợp vào màn hình MH-HRM-01 (Employee List) dưới dạng bộ lọc nâng cao.

## Yêu cầu kỹ thuật

### 1. Advanced Search Interface

#### UI/UX
- Vùng tìm kiếm nâng cao có thể mở rộng/thu gọn trên màn hình danh sách nhân viên
- Giao diện tìm kiếm hiển thị các trường:
  - Loại kỹ năng (multi-select dropdown từ danh mục skill categories)
  - Tên kỹ năng (multi-select dropdown/autocomplete từ danh mục skills, lọc theo loại đã chọn)
  - Mức độ thành thạo (dropdown: Basic, Intermediate, Advanced) với tùy chọn "tối thiểu"
  - Số năm kinh nghiệm (range slider hoặc input fields với min/max)
  - Trạng thái sẵn sàng (multi-select: Available, Ending Soon, Allocated với % phân bổ)
- Visual styling rõ ràng cho các filter đang được áp dụng (active filters)
- Nút "Tìm kiếm" và "Reset" để thực hiện tìm kiếm hoặc xóa tất cả điều kiện

#### Functionality
- Hỗ trợ tìm kiếm AND/OR giữa các kỹ năng (VD: Java AND Spring, hoặc Java OR Kotlin)
- Lọc động danh sách kỹ năng dựa trên loại kỹ năng đã chọn
- Lưu và áp dụng các điều kiện tìm kiếm khi chuyển trang trong phân trang
- Kết hợp với các filter hiện có trên màn hình danh sách nhân viên
- Hỗ trợ lưu và tải lại các bộ filter thường dùng

### 2. Result Display and Ranking

#### UI/UX
- Kết quả tìm kiếm hiển thị trong bảng danh sách nhân viên với thông tin cơ bản
- Thêm cột "Độ phù hợp" hoặc "Match Score" hiển thị mức độ phù hợp với tiêu chí tìm kiếm
- Visual indicators (như badges hoặc stars) cho mỗi skill được tìm thấy
- Quick preview các kỹ năng phù hợp khi hover vào một nhân viên
- Hiển thị số lượng kết quả tìm thấy và tóm tắt điều kiện tìm kiếm

#### Functionality
- Sắp xếp kết quả theo độ phù hợp (best match first)
- Tính toán score dựa trên:
  - Số lượng kỹ năng khớp với yêu cầu
  - Mức độ thành thạo
  - Số năm kinh nghiệm
  - Trạng thái sẵn sàng
- Highlight các kỹ năng khớp với tiêu chí tìm kiếm
- Pagination và sorting cho kết quả tìm kiếm

### 3. Integration với Employee List

#### UI/UX
- Chuyển đổi mượt mà giữa tìm kiếm cơ bản và tìm kiếm nâng cao
- Consistent UI/UX với các filter hiện có
- Hiển thị rõ ràng số lượng filter đang áp dụng khi thu gọn search panel
- Option để lưu search query làm filter template

#### Functionality
- Kết hợp với các filter khác trên màn hình (team, trạng thái, etc.)
- URL parameters để lưu trạng thái tìm kiếm (deep linking)
- Cập nhật kết quả realtime khi thay đổi điều kiện tìm kiếm 
- Xử lý các trường hợp không có kết quả với gợi ý điều chỉnh filter

### 4. Gợi ý Nhân viên Phù hợp

#### UI/UX
- Widget "Gợi ý nhân viên phù hợp" có thể được sử dụng từ các màn hình khác
- Giao diện đơn giản cho phép nhập nhanh các kỹ năng cần thiết
- Hiển thị top N nhân viên phù hợp nhất với visual ranking

#### Functionality
- API endpoint riêng cho chức năng gợi ý
- Thuật toán matching tối ưu để đề xuất nhân viên phù hợp nhất
- Có thể được tích hợp vào màn hình Chi tiết Cơ hội hoặc tạo dự án mới
- Khả năng xuất danh sách đề xuất

### 5. Phân quyền Truy cập

- Admin: Toàn quyền sử dụng tính năng tìm kiếm nâng cao
- General Manager (課長): Tìm kiếm trong phạm vi bộ phận
- Team Leader (部長): Tìm kiếm trong team của họ và tìm kiếm nhân viên available/ending soon từ các team khác
- Employee: Tùy chỉnh quyền, có thể chỉ cho phép tìm kiếm cơ bản

## Xử lý State và Performance

### State Management
- Sử dụng global state để lưu trữ các điều kiện tìm kiếm
- Caching kết quả tìm kiếm để tránh gọi API không cần thiết
- Lưu trữ lịch sử tìm kiếm gần đây

### API Integration
- Kết nối với `API-HRM-006` (`GET /api/v1/employees/search/suggest`) để thực hiện tìm kiếm nâng cao
- Sử dụng `API-HRM-001` (`GET /api/v1/employees`) với các query params phức tạp cho filtering
- Implement debounce để tránh gọi API quá nhiều khi người dùng thay đổi điều kiện tìm kiếm
- Xử lý pagination ở cả client và server side

### Performance Considerations
- Lazy loading các components không cần thiết
- Optimize rendering của danh sách kết quả lớn
- Implement virtualization cho danh sách dài
- Caching danh mục kỹ năng và loại kỹ năng

## Các Task liên quan
- FE-HRM-001: Employee List Page (nơi tích hợp tìm kiếm nâng cao)
- FE-HRM-004: Skills Management UI (cung cấp danh mục kỹ năng)
- FE-OPP-002: Opportunity Detail Page (có thể tích hợp widget gợi ý nhân viên)

## Tiêu chí chấp nhận
1. UI tìm kiếm nâng cao:
   - Hiển thị đầy đủ các trường tìm kiếm theo yêu cầu
   - Dropdown và autocomplete hoạt động chính xác
   - Filter động của tên kỹ năng dựa trên loại đã chọn
   - Giao diện responsive và thân thiện người dùng

2. Chức năng tìm kiếm:
   - Tìm kiếm chính xác dựa trên các tiêu chí được chọn
   - Hỗ trợ cả tìm kiếm AND/OR giữa các kỹ năng
   - Lưu và áp dụng các điều kiện khi chuyển trang
   - Reset filters hoạt động đúng

3. Hiển thị kết quả:
   - Ranking hiển thị chính xác độ phù hợp
   - Highlight các kỹ năng khớp với yêu cầu
   - Hiển thị tóm tắt điều kiện tìm kiếm
   - Pagination hoạt động chính xác

4. Gợi ý nhân viên:
   - Widget gợi ý nhân viên có thể tích hợp vào các màn hình khác
   - Đề xuất nhân viên phù hợp dựa trên các kỹ năng yêu cầu
   - Ranking và display của kết quả gợi ý chính xác

5. Phân quyền:
   - Tính năng tìm kiếm nâng cao chỉ hiển thị cho người dùng có quyền
   - Kết quả phù hợp với phạm vi dữ liệu của mỗi vai trò
   - Không lộ thông tin nhạy cảm cho người dùng không có quyền

6. Performance:
   - Tìm kiếm hoạt động nhanh chóng, không gây lag giao diện
   - Debounce hoạt động đúng để tránh quá nhiều API calls
   - Caching kết quả hoạt động chính xác

## Resources
- [Thiết kế màn hình MH-HRM-01](../../../DD/SCREEN/MH-HRM-01.md)
- [Thiết kế màn hình MH-HRM-02](../../../DD/SCREEN/MH-HRM-02.md)
- [MH-HRM-02 - TAB Kĩ năng chuyên môn](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20%20Kĩ%20năng%20chuyên%20môn.png)
- [Danh sách API - Quản lý nhân sự](../../../BD/API/api_list.md#2-quản-lý-nhân-sự-hrm)
- [Chức năng liên quan: F-1.7, F-1.8 (Tìm kiếm Nhân sự theo Skills, Gợi ý Nhân sự phù hợp)](../../../BD/FunctionDesign/FunctionList.md) 