# Task Detail: FE-MGN-001

## Thông tin chung
- **ID**: FE-MGN-001
- **Tên task**: Xây dựng Margin List Page (MH-MGN-01)
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - Màn hình: [MH-MNG-01](../../../assets/frontend/img/MH-MGN-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-MGN-01.md](../../../DD/SCREEN/MH-MGN-01.md)
  - API: [API-MGN-001](../../../DD/API/API-MGN-001.md), [API-MGN-002](../../../DD/API/API-MGN-002.md)

## Mô tả
Xây dựng trang hiển thị và quản lý margin của nhân viên với các chỉ số về chi phí, doanh thu và tỷ lệ margin. Đây là trang quan trọng dành cho Leader và Trưởng bộ phận (General Manager) để theo dõi hiệu quả tài chính từ nguồn lực nhân sự. Trang này có tính bảo mật cao, chỉ hiển thị thông tin margin theo quyền hạn của người dùng đăng nhập. Giao diện cung cấp các bộ lọc để xem dữ liệu theo team, thời gian và bao gồm các chỉ báo màu sắc để nhanh chóng nhận biết trạng thái margin của từng nhân viên.

## Yêu cầu kỹ thuật
### Layout & Structure
- Sử dụng MainLayout chung của hệ thống (từ FE-CORE-001)
- Header section với tiêu đề và controls
- Filter section với các bộ lọc (team, thời gian)
- Bảng dữ liệu chính hiển thị thông tin margin
- Summary section ở cuối bảng hiển thị các giá trị tổng hợp

### UI Components
- Sử dụng Tailwind CSS cho styling
- Filter controls:
  - Team selector (dropdown/multi-select)
  - Date range picker (tháng/quý/năm)
  - Quick filter chips (Red margins, Yellow margins, Green margins)
- DataTable với các columns:
  - Thông tin nhân viên (Tên, Mã NV)
  - Team/Bộ phận
  - Chi phí (Cost)
  - Doanh thu (Revenue)
  - Margin (giá trị và %)
  - Trạng thái Margin (với color indicators)
- Color indicators theo ngưỡng cấu hình:
  - Red: Margin < 30%
  - Yellow: 30% <= Margin < 50%
  - Green: Margin >= 50%
- Tooltips hiển thị thông tin chi tiết khi hover
- Summary footer với tổng số nhân viên, tổng chi phí, tổng doanh thu, margin trung bình

### Functionality
- Lọc dữ liệu theo team và khoảng thời gian
- Quick filters cho các trạng thái margin
- Sắp xếp theo các cột (sortable columns)
- Phân trang với cấu hình số records/trang
- Hiển thị chi tiết margin khi click vào dòng (optional: modal/drawer)
- Export dữ liệu ra Excel/CSV (tùy quyền)
- Tính toán và hiển thị trạng thái margin dựa trên ngưỡng cấu hình
- Hiển thị tổng hợp (summary) theo kết quả lọc hiện tại

### API Integration
- Tích hợp với Backend API endpoints:
  - `GET /api/v1/margins/employee` (API-MGN-001) - Lấy dữ liệu margin của nhân viên (với query params cho filter)
  - `GET /api/v1/margins/summary` (API-MGN-002) - Lấy dữ liệu margin tổng hợp (sử dụng cho summary section)

## Chi tiết các thành phần

### Header Section
- Tiêu đề "Quản lý Margin"
- Text hiển thị khoảng thời gian đang xem
- Button "Export" (secondary button) nếu có quyền

### Filter Section
- Team filter: Dropdown cho phép chọn team/bộ phận (dựa trên quyền)
- Date range filter: Chọn khoảng thời gian (tháng, quý, năm)
- Quick filter chips: "Tất cả", "Red margin (<30%)", "Yellow margin (30-50%)", "Green margin (>50%)"
- Nút "Áp dụng" và "Xóa bộ lọc"

### DataTable Margin
- Column Nhân viên:
  - Tên nhân viên (in đậm)
  - Mã nhân viên (text nhỏ)
- Column Team/Bộ phận
- Column Chi phí (Cost):
  - Hiển thị giá trị tiền tệ với định dạng đúng
  - Tooltip hiển thị breakdown chi phí (nếu có)
- Column Doanh thu (Revenue):
  - Hiển thị giá trị tiền tệ với định dạng đúng
  - Tooltip hiển thị breakdown doanh thu theo dự án (nếu có)
- Column Margin:
  - Hiển thị giá trị tiền tệ
  - Hiển thị phần trăm margin tính toán
- Column Trạng thái:
  - Badge màu tương ứng với trạng thái margin:
    - Red: Margin < 30%
    - Yellow: 30% <= Margin < 50%
    - Green: Margin >= 50%
  - Tooltip hiển thị thông tin ngưỡng

### Summary Section
- Hiển thị ở dưới bảng dữ liệu
- Tổng số nhân viên trong kết quả hiện tại
- Tổng chi phí (Cost)
- Tổng doanh thu (Revenue)
- Margin trung bình (%)
- Phân bố nhân viên theo trạng thái margin (X nhân viên Red, Y nhân viên Yellow, Z nhân viên Green)

## Phân quyền truy cập
- **Admin**: Xem toàn bộ dữ liệu margin của tất cả nhân viên, đầy đủ quyền export
- **General Manager (課長)**: Xem dữ liệu margin của nhân viên thuộc các bộ phận quản lý, quyền export
- **Team Leader (部長)**: Chỉ xem dữ liệu margin của nhân viên trong team mình quản lý, quyền export giới hạn
- **Nhân viên**: Không có quyền truy cập
- **Kế toán**: Có thể được cấp quyền xem toàn bộ hoặc giới hạn

## Các Task liên quan
- FE-CORE-001: Layout components (dependency)
- FE-CORE-004: Table components (dependency)
- FE-CORE-007: Chart components (optional, nếu có visualization)
- FE-MGN-002: Cost Input Form (related)
- FE-MGN-004: Margin Chart components (related)

## Tiêu chí chấp nhận
- Bảng margin hiển thị đúng theo mô tả MH-MGN-01.md
- Trạng thái margin (Red/Yellow/Green) hiển thị chính xác theo ngưỡng cấu hình
- Lọc dữ liệu hoạt động chính xác (team, thời gian, margin status)
- Tính toán và hiển thị summary chính xác
- Export dữ liệu thành công (với quyền phù hợp)
- Phân quyền áp dụng chặt chẽ (mỗi role chỉ thấy dữ liệu được phép)
- UI responsive từ desktop đến tablet (>= 768px)
- Performance: dữ liệu load trong < 3 giây kể cả với bộ dữ liệu lớn
- Tooltips hiển thị chính xác thông tin chi tiết
- Định dạng tiền tệ và phần trăm hiển thị nhất quán 