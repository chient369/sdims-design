# Task Detail: FE-HRM-005

## Thông tin chung
- **ID**: FE-HRM-005
- **Tên task**: Xây dựng Project Assignment UI
- **Độ ưu tiên**: Cao
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-02 - TAB Trạng thái & phân bổ](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20Trạng%20thái%20%26%20phân%20bổ.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-03.md](../../../DD/SCREEN/MH-HRM-03.md)
  - API: [API-HRM-020](../../../DD/API/API-HRM-020.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-HRM-002: Xây dựng Employee Detail Page (MH-HRM-02)
  - FE-HRM-003: Xây dựng Employee Form (MH-HRM-03)

## Mô tả
Xây dựng giao diện người dùng (UI) cho việc phân bổ nhân viên vào dự án, bao gồm các thành phần cho phép quản lý trạng thái nhân viên và tỷ lệ phân bổ thời gian vào các dự án. Component này sẽ được tích hợp trong màn hình MH-HRM-03 (Employee Form) và sẽ được hiển thị dưới dạng tab "Trạng thái & Phân bổ" trong MH-HRM-02 (Employee Detail). Chỉ người dùng có quyền phù hợp (Admin, Team Leader, General Manager) mới có thể cập nhật thông tin trong UI này.

## Yêu cầu kỹ thuật

### 1. UI cho Trạng thái Nhân viên

#### UI/UX
- Dropdown menu để chọn trạng thái nhân viên với các tùy chọn sau:
  - Allocated (Đã phân bổ vào dự án)
  - Available/Bench (Sẵn sàng cho dự án mới)
  - Ending Soon (Sắp kết thúc dự án)
  - On Leave (Nghỉ phép dài hạn)
  - Resigned (Đã nghỉ việc)
- Visual indicator cho trạng thái hiện tại (màu sắc, icon)
- Các trường bổ sung hiển thị dựa trên trạng thái được chọn

#### Functionality
- Khi trạng thái thay đổi, hiển thị/ẩn các trường thông tin liên quan:
  - Nếu chọn "Allocated" hoặc "Ending Soon": hiển thị fields cho dự án và phân bổ
  - Nếu chọn "Resigned": hiển thị field ngày nghỉ việc
  - Nếu chọn "On Leave": hiển thị fields ngày bắt đầu và kết thúc nghỉ
- Thay đổi trạng thái cần có xác nhận khi chuyển từ trạng thái đang có dự án sang trạng thái khác
- Cập nhật thông tin trạng thái lên API khi lưu

### 2. UI cho Phân bổ Dự án

#### UI/UX
- Section hiển thị danh sách dự án nhân viên đang tham gia (nếu có) với các thông tin:
  - Tên dự án (với autocomplete từ danh sách dự án)
  - Tỷ lệ phân bổ (%)
  - Ngày bắt đầu (date picker)
  - Ngày kết thúc dự án (date picker, bắt buộc nếu trạng thái là "Ending Soon")
  - Action buttons (Xóa phân bổ)
- Nút "Thêm dự án" để thêm một phân bổ dự án mới
- Hiển thị tổng tỷ lệ phân bổ với cảnh báo khi vượt quá 100%
- Sử dụng color-coded indicators cho tỷ lệ phân bổ

#### Functionality
- Cho phép thêm nhiều dự án cho một nhân viên (nếu tổng % không vượt quá 100%)
- Date range picker với validation logic (ngày kết thúc phải sau ngày bắt đầu)
- Autocomplete/dropdown cho tên dự án từ danh sách dự án trong hệ thống
- Validation tỷ lệ phân bổ (1-100%)
- Tính toán tổng tỷ lệ phân bổ và hiển thị cảnh báo nếu vượt quá 100%
- Cập nhật thông tin phân bổ lên API khi lưu

### 3. Integration với Employee Form

#### UI/UX
- Thiết kế nhất quán với các phần khác của form nhân viên
- Clear separation giữa khu vực trạng thái và khu vực phân bổ dự án
- Collapsed/Expanded views cho danh sách phân bổ nếu có nhiều dự án

#### Functionality
- Lưu trạng thái form khi chuyển tab để không mất dữ liệu đã nhập
- Hook vào hệ thống validation của form cha
- Xử lý submit như một phần của form cha
- Hiển thị lỗi validation tại component và báo lỗi lên form cha

### 4. Phân quyền Truy cập

- Admin: Có thể thay đổi tất cả thông tin trạng thái và phân bổ
- Team Leader (部長): Chỉ có thể thay đổi thông tin trạng thái và phân bổ cho nhân viên trong team của họ
- General Manager (課長): Có thể thay đổi thông tin trạng thái và phân bổ trong phạm vi bộ phận
- Employee: Chỉ có thể xem thông tin này, không được chỉnh sửa

## Xử lý State và Performance

### State Management
- Sử dụng local form state cho việc thay đổi trạng thái và cập nhật UI
- Sử dụng global state (Redux/Context) cho danh sách dự án được lấy từ API

### API Integration
- Kết nối với `API-HRM-020` (`PUT /api/v1/employees/{employeeId}/status`) để cập nhật trạng thái và phân bổ
- Lấy danh sách dự án từ API Contracts/Projects
- Implement validation logic đồng bộ với backend

## Các Task liên quan
- FE-HRM-002: Employee Detail Page (nơi hiển thị tab trạng thái & phân bổ)
- FE-HRM-003: Employee Form (nơi cập nhật trạng thái và phân bổ)
- FE-DSH-002: HR metrics widgets (sử dụng thông tin trạng thái để hiển thị metrics)

## Tiêu chí chấp nhận
1. UI cho project assignment được hiển thị đúng trong section 4 của MH-HRM-03 và tab tương ứng trong MH-HRM-02
2. Dropdown trạng thái hiển thị và hoạt động chính xác
3. Các trường thông tin liên quan được hiển thị/ẩn đúng theo trạng thái được chọn
4. Date range picker cho ngày bắt đầu/kết thúc dự án hoạt động với validation chính xác
5. Tính toán và hiển thị đúng tổng tỷ lệ phân bổ
6. Hiển thị cảnh báo khi tổng tỷ lệ phân bổ vượt quá 100%
7. Autocomplete/dropdown cho tên dự án hoạt động chính xác
8. Phân quyền được áp dụng đúng (Admin, Leader, General Manager có thể cập nhật, Employee chỉ xem)
9. Validation hoạt động chính xác cho tất cả các trường
10. Dữ liệu được cập nhật chính xác lên API khi lưu form

## Resources
- [MH-HRM-02 - TAB Trạng thái & phân bổ](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20Trạng%20thái%20%26%20phân%20bổ.png)
- [Mô tả chi tiết MH-HRM-03](../../../DD/SCREEN/MH-HRM-03.md)
- [Danh sách API - Quản lý nhân sự](../../../BD/API/api_list.md#2-quản-lý-nhân-sự-hrm)
- [Chức năng liên quan: F-1.9, F-1.10 (Quản lý Trạng thái Nhân sự, Quản lý Phân bổ Dự án)](../../../BD/FunctionDesign/FunctionList.md) 