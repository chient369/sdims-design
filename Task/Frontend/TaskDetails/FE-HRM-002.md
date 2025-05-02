# Task Detail: FE-HRM-002

## Thông tin chung
- **ID**: FE-HRM-002
- **Tên task**: Xây dựng Employee Detail Page (MH-HRM-02)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-02](../../../assets/frontend/img/MH-HRM-02.png), 
  [MH-HRM-02 - TAB Kĩ năng chuyên môn](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20%20K%C4%A9%20n%C4%83ng%20chuy%C3%AAn%20m%C3%B4n.png), 
  [MH-HRM-02 - TAB Lịch sử dự án](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20%20L%E1%BB%8Bch%20s%E1%BB%AD%20d%E1%BB%B1%20%C3%A1n.png), 
  [MH-HRM-02 - TAB Trạng thái & phân bổ](../../../assets/frontend/img/MH-HRM-02%20-%20TAB%20Tr%E1%BA%A1ng%20th%C3%A1i%20%26%20ph%C3%A2n%20b%E1%BB%95.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-02.md](../../../DD/SCREEN/MH-HRM-02.md)
  - API: [API-HRM-003](../../../DD/API/API-HRM-003.md), [API-HRM-017](../../../DD/API/API-HRM-017.md), [API-HRM-021](../../../DD/API/API-HRM-021.md)

## Mô tả
Xây dựng trang chi tiết nhân viên với nội dung đầy đủ về thông tin cá nhân, kỹ năng chuyên môn, lịch sử dự án và trạng thái phân bổ. Trang này cho phép xem thông tin chi tiết của một nhân viên cụ thể, thực hiện các hành động như cập nhật kỹ năng, phân bổ dự án, và truy cập lịch sử làm việc. Đây là nơi quản lý và Leader có thể xem chi tiết thông tin của nhân viên và thực hiện các thao tác quản lý.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với thông tin cơ bản và action buttons (Edit, Back)
- Tab navigation để chuyển đổi giữa các phần thông tin:
  - Thông tin cá nhân (mặc định)
  - Kỹ năng chuyên môn
  - Lịch sử dự án
  - Trạng thái & phân bổ
- Nội dung tab hiển thị thông tin tương ứng
- Content area có thể scroll nếu nội dung dài

### UI Components
- Sử dụng Tailwind CSS cho styling
- Header với avatar lớn, thông tin cơ bản và các action button
- Tab navigation với indicators cho tab đang active
- Card layouts cho từng section thông tin
- Form elements cho các trường có thể edit
- Tag components cho hiển thị skills
- Timeline components cho lịch sử dự án
- Status indicators cho trạng thái hiện tại
- Charts/visualizations (nếu cần) cho biểu diễn dữ liệu

### Functionality
- Chuyển đổi giữa các tab mà không reload trang
- Hiển thị các action button tùy theo quyền của người dùng
- Kích hoạt form edit khi click nút Edit (hoặc chuyển trang)
- Load và hiển thị danh sách kỹ năng theo từng nhóm
- Hiển thị lịch sử dự án dạng timeline có thể filter
- Hiển thị thông tin trạng thái và phân bổ hiện tại
- Hỗ trợ export thông tin nhân viên (nếu có quyền)

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/employees/{employeeId}` (API-HRM-003) - Lấy thông tin chi tiết nhân viên
  - `GET /api/v1/employees/{employeeId}/skills` (API-HRM-017) - Lấy danh sách kỹ năng của nhân viên
  - `GET /api/v1/employees/{employeeId}/project-history` (API-HRM-021) - Lấy lịch sử dự án

## Chi tiết các thành phần
Dựa trên thiết kế [MH-HRM-02.png](../../../assets/frontend/img/MH-HRM-02.png) và các hình ảnh tab chi tiết:

### Header Section
- Avatar lớn của nhân viên (hoặc placeholder nếu không có)
- Tên đầy đủ của nhân viên (in đậm, cỡ lớn)
- Thông tin cơ bản: Mã nhân viên, Email, Vị trí, Team/Bộ phận
- Badge hiển thị trạng thái hiện tại (Available, Allocated, Ending Soon)
- Buttons:
  - "Back" - Quay lại trang Danh sách nhân viên
  - "Edit" - Chuyển đến form sửa thông tin (MH-HRM-03) hoặc kích hoạt edit mode
  - "Export Profile" (Optional) - Xuất thông tin nhân viên

### Tab Navigation
- Tab "Thông tin cá nhân" (mặc định selected)
- Tab "Kỹ năng chuyên môn"
- Tab "Lịch sử dự án"
- Tab "Trạng thái & phân bổ"

### Tab Content: Thông tin cá nhân
- Card thông tin liên hệ:
  - Số điện thoại
  - Email công ty & cá nhân
  - Địa chỉ
- Card thông tin làm việc:
  - Ngày bắt đầu làm việc
  - Vị trí công việc
  - Team/Bộ phận trực thuộc
  - Leader trực tiếp
- Card thông tin khác (tùy vào yêu cầu cụ thể)

### Tab Content: Kỹ năng chuyên môn
- Hiển thị danh sách kỹ năng được nhóm theo loại (Programing Languages, Frameworks, Database...)
- Mỗi kỹ năng hiển thị dưới dạng Tag với thông tin:
  - Tên kỹ năng
  - Mức độ thành thạo (biểu diễn bằng màu sắc hoặc số sao/rating)
  - Số năm kinh nghiệm (nếu có)
- Button "Cập nhật" hoặc "Thêm kỹ năng" nếu có quyền

### Tab Content: Lịch sử dự án
- Timeline hiển thị các dự án mà nhân viên đã tham gia
- Mỗi mục trong timeline chứa:
  - Tên dự án
  - Thời gian tham gia (từ ngày - đến ngày)
  - Vai trò trong dự án
  - Mô tả công việc (nếu có)
  - % tham gia (allocation)
- Filter để lọc lịch sử theo thời gian, loại dự án...

### Tab Content: Trạng thái & phân bổ
- Hiển thị trạng thái hiện tại của nhân viên:
  - Badge lớn thể hiện trạng thái (Available, Allocated, Ending Soon, On Leave)
  - Thời gian kể từ khi ở trạng thái hiện tại
- Thông tin phân bổ hiện tại:
  - Dự án đang tham gia
  - Thời gian bắt đầu - kết thúc dự kiến
  - % phân bổ
  - Vai trò trong dự án
- Chart thể hiện tiến độ dự án / thời gian còn lại
- Button "Cập nhật trạng thái" hoặc "Phân bổ" nếu có quyền

## Phân quyền truy cập
- **Admin**: Xem tất cả thông tin, có quyền edit thông tin cá nhân, skills, trạng thái và phân bổ
- **General Manager (課長)**: Xem tất cả thông tin, có quyền edit thông tin cá nhân, skills, trạng thái và phân bổ
- **Team Leader (部長)**: Chỉ xem thông tin chi tiết nhân viên thuộc team mình, có quyền edit skills, trạng thái và phân bổ
- **Nhân viên**: Chỉ xem thông tin của bản thân, có quyền đề xuất cập nhật skills (tùy cấu hình)
- Áp dụng các quyền chi tiết theo định nghĩa trong file permissions_definition.md

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-002: Tab components (dependency)
- FE-CORE-003: Form components (dependency)
- FE-HRM-001: Employee List Page (related)
- FE-HRM-003: Employee Form Page (related)
- FE-HRM-004: Skills Management UI (related)
- FE-HRM-005: Project Assignment UI (related)

## Tiêu chí chấp nhận
- Trang chi tiết nhân viên hiển thị đúng theo thiết kế MH-HRM-02.png và các thiết kế tab
- Tất cả các tab hiển thị đầy đủ và chính xác thông tin
- API calls được thực hiện đúng và hiệu quả (tránh gọi không cần thiết khi chuyển tab)
- UI responsive từ desktop đến tablet (>= 768px)
- Tab navigation hoạt động mượt mà, không reload trang
- Các action (edit, export) hiển thị dựa trên quyền người dùng
- Timeline lịch sử dự án hiển thị đúng theo thứ tự thời gian
- Trạng thái nhân viên hiển thị với màu sắc và thông tin chính xác
- RBAC áp dụng đúng (chỉ thấy người dùng được phép, chỉ thấy nút action khi có quyền) 