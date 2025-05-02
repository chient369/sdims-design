# Task Detail: FE-HRM-006

## Thông tin chung
- **ID**: FE-HRM-006
- **Tên task**: Xây dựng Import/Export functionality
- **Độ ưu tiên**: Trung bình
- **Estimate**: 4 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-HRM-01](../../../assets/frontend/img/MH-HRM-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-HRM-01.md](../../../DD/SCREEN/MH-HRM-01.md)
  - API: [API-HRM-007](../../../DD/API/API-HRM-007.md), [API-HRM-008](../../../DD/API/API-HRM-008.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-HRM-001: Xây dựng Employee List Page (MH-HRM-01)

## Mô tả
Xây dựng chức năng Import/Export dữ liệu nhân viên, cho phép người dùng nhập dữ liệu hàng loạt từ file Excel/CSV và xuất danh sách nhân viên ra file. Các chức năng này sẽ được tích hợp vào màn hình MH-HRM-01 (Employee List) và chỉ được phép sử dụng bởi người dùng có quyền phù hợp (Admin, General Manager). Tính năng này giúp quản lý dữ liệu nhân sự hiệu quả hơn, đặc biệt khi cần nhập một lượng lớn dữ liệu ban đầu hoặc cần xuất dữ liệu để phân tích bên ngoài hệ thống.

## Yêu cầu kỹ thuật

### 1. Chức năng Export Danh sách Nhân viên

#### UI/UX
- Nút "Xuất Excel" hoặc "Export" trên màn hình danh sách nhân viên (MH-HRM-01)
- Dropdown menu cho phép chọn format xuất (Excel/CSV)
- Hiển thị dialog xác nhận với các tùy chọn xuất (tất cả dữ liệu hoặc chỉ dữ liệu đã lọc)
- Thông báo tiến trình xuất khi xử lý file lớn
- Tự động download file sau khi xuất thành công

#### Functionality
- Xuất dữ liệu theo các filter đang được áp dụng trên màn hình danh sách
- Hỗ trợ xuất ra file Excel (.xlsx) và CSV (.csv)
- Áp dụng định dạng chuẩn cho file xuất (header, data types, styling cho Excel)
- Xử lý các trường đặc biệt (ngày tháng, enum values) theo format phù hợp
- Xử lý các kịch bản lỗi và hiển thị thông báo phù hợp
- Đảm bảo hiệu suất khi xuất lượng dữ liệu lớn

### 2. Chức năng Import Danh sách Nhân viên

#### UI/UX
- Nút "Nhập Excel" hoặc "Import" trên màn hình danh sách nhân viên (MH-HRM-01)
- Upload area cho phép drag-and-drop file hoặc chọn file từ hệ thống
- Màn hình preview dữ liệu trước khi import chính thức
- Progress bar hiển thị tiến trình xử lý
- Báo cáo lỗi chi tiết khi có vấn đề với dữ liệu import
- Tùy chọn xử lý trùng lặp (bỏ qua, cập nhật, báo lỗi)

#### Functionality
- Hỗ trợ import từ file Excel (.xlsx, .xls) và CSV (.csv)
- Validation dữ liệu nhập (kiểm tra định dạng, dữ liệu bắt buộc, tham chiếu đến các bảng khác)
- Xử lý và hiển thị lỗi validation theo dòng/cột
- Hỗ trợ cả import nhân viên mới và cập nhật nhân viên hiện có
- Template download cho người dùng sử dụng khi tạo file import
- Xử lý batch để đảm bảo hiệu suất và khả năng phục hồi khi gặp lỗi

### 3. File Template và Validation

#### UI/UX
- Nút "Tải template" để người dùng có thể download mẫu file import
- Clear labels và instructions trong template file
- Màn hình hiển thị validation errors trực quan, cho phép người dùng dễ dàng hiểu và sửa lỗi

#### Functionality
- Tạo template file với các cột bắt buộc được đánh dấu rõ ràng
- Thêm sheet hướng dẫn trong template Excel
- Validation rules được áp dụng trong template (nếu có thể)
- Hỗ trợ các loại dữ liệu phức tạp (skills list, project assignments) thông qua format chuẩn

### 4. Xử lý Lỗi và Báo cáo

#### UI/UX
- Hiển thị summary của kết quả import (số lượng thành công, số lượng lỗi)
- Chi tiết lỗi theo từng dòng/cột có vấn đề
- Tùy chọn xuất báo cáo lỗi ra file
- Visual indicators cho các dòng có vấn đề trong preview

#### Functionality
- Phân loại và hiển thị lỗi theo mức độ (critical, warning)
- Xử lý batch cho phép tiếp tục import các dòng không lỗi
- Log đầy đủ quá trình import để troubleshooting
- Rollback capability khi gặp lỗi nghiêm trọng

### 5. Phân quyền Truy cập

- Admin: Có quyền import và export đầy đủ
- General Manager (課長): Có quyền export và có thể được cấp quyền import cho phạm vi bộ phận
- Team Leader (部長): Chỉ có quyền export dữ liệu team của họ
- Employee: Không có quyền truy cập các chức năng này

## Xử lý State và Performance

### State Management
- Sử dụng global state để lưu trạng thái quá trình import/export
- Lưu trữ và hiển thị lỗi validation từ server
- Quản lý trạng thái upload file và progress

### API Integration
- Kết nối với `API-HRM-007` (`POST /api/v1/employees/import`) để import dữ liệu
- Kết nối với `API-HRM-008` (`GET /api/v1/employees/export`) để export dữ liệu
- Xử lý multipart/form-data cho file upload
- Handling của response streams cho file download

### Performance Considerations
- Xử lý bất đồng bộ và pagination khi export dataset lớn
- Chunking large files khi upload
- Hiển thị progress indicator cho người dùng
- Caching của template files

## Các Task liên quan
- FE-HRM-001: Employee List Page (nơi tích hợp các chức năng import/export)
- FE-HRM-004: Skills Management UI (liên quan đến validation skills trong file import)

## Tiêu chí chấp nhận
1. Chức năng Export hoạt động chính xác:
   - Export được dữ liệu theo filter đang áp dụng
   - Hỗ trợ các format file được yêu cầu (Excel, CSV)
   - Format dữ liệu xuất dễ đọc, có header và styling phù hợp
   - Download file hoạt động trên các trình duyệt chính

2. Chức năng Import hoạt động chính xác:
   - Upload file hoạt động với drag-drop và file picker
   - Preview dữ liệu trước khi import
   - Validation đầy đủ và hiển thị lỗi rõ ràng
   - Import thành công dữ liệu hợp lệ

3. Phân quyền được áp dụng đúng:
   - Chỉ Admin và General Manager có thể thấy và sử dụng chức năng Import
   - Export được giới hạn theo phạm vi dữ liệu của từng vai trò

4. File Template:
   - Template download hoạt động
   - Template có hướng dẫn và format rõ ràng
   - Template bao gồm tất cả trường cần thiết được đánh dấu

5. Báo cáo Lỗi:
   - Hiển thị đầy đủ và rõ ràng các lỗi validation
   - Cho phép export báo cáo lỗi
   - Xử lý batch cho phép tiếp tục với các dòng không lỗi

6. Xử lý Performance:
   - Không bị treo/crash khi xử lý file lớn
   - Progress indicator hiển thị chính xác tiến trình
   - Cơ chế retry/resume khi gặp lỗi network

## Resources
- [Tài liệu thiết kế màn hình MH-HRM-01](../../../DD/SCREEN/MH-HRM-01.md)
- [Danh sách API - HRM](../../../BD/API/api_list.md#2-quản-lý-nhân-sự-hrm)
- [Chức năng liên quan: F-1.4 (Import/Export Danh sách Nhân sự)](../../../BD/FunctionDesign/FunctionList.md) 