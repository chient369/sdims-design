# Task Detail: FE-MGN-003

## Thông tin chung
- **ID**: FE-MGN-003
- **Tên task**: Xây dựng Cost Import UI
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-MGN-02](../../../assets/frontend/img/MH-MGN-02.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-MGN-02.md](../../../DD/SCREEN/MH-MGN-02.md)
  - API: [API-MGN-002](../../../DD/API/API-MGN-002.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-003: Xây dựng Form components
  - FE-CORE-004: Xây dựng Table components
  - FE-MGN-001: Xây dựng Margin List Page (MH-MGN-01)
  - FE-MGN-002: Xây dựng Cost Input Form (MH-MGN-02)

## Mô tả
Xây dựng giao diện người dùng (UI) để import dữ liệu chi phí nhân sự hàng loạt từ file Excel/CSV, bao gồm các chức năng tải template, upload file, validation dữ liệu và hiển thị báo cáo kết quả/lỗi. Tính năng này là một phần của màn hình MH-MGN-02 và chỉ được phép sử dụng bởi người dùng có quyền Leader (部長) và General Manager (課長). Chức năng import này giúp người quản lý cập nhật nhanh chi phí cho nhiều nhân viên cùng lúc, đặc biệt hữu ích khi nhập dữ liệu định kỳ hàng tháng/quý.

## Yêu cầu kỹ thuật

### 1. UI cho Import Chi phí

#### UI/UX
- Tab "Import từ File" trong màn hình MH-MGN-02
- Khu vực "Hướng dẫn & Template" với nội dung hướng dẫn rõ ràng về cách thức sử dụng
- Nút "Tải file mẫu (Template)" nổi bật
- Khu vực upload với hỗ trợ drag-and-drop hoặc chọn file
- Hiển thị tên file đã chọn và trạng thái
- Progress bar khi đang upload/xử lý file lớn
- Khu vực phản hồi kết quả sau khi import (thành công/thất bại, số lượng)

#### Functionality
- Tạo và tải file template Excel/CSV dựa trên kỳ và team đã chọn
- Tạo template với các cột bắt buộc (Mã Nhân viên, Họ và Tên, Chi phí)
- Upload file lên server
- Validation toàn diện (định dạng file, cấu trúc cột, dữ liệu, quyền hạn)
- Hiển thị lỗi validation chi tiết (dòng, cột, lý do)
- Xử lý và import dữ liệu hợp lệ vào database
- Hiển thị summary kết quả import (số lượng thành công, số lượng lỗi)
- Log thông tin import để troubleshooting

### 2. Template Generation

#### UI/UX
- Quá trình tạo template phải tính đến kỳ và team đã chọn
- Template phải dễ hiểu và có hướng dẫn rõ ràng
- Nên bao gồm sheet hướng dẫn trong file Excel

#### Functionality
- Tạo template động với danh sách nhân viên thuộc phạm vi quản lý của người dùng
- Tự động điền sẵn các trường Mã Nhân viên và Họ Tên
- Đánh dấu rõ ràng các trường bắt buộc 
- Bao gồm các thông tin meta (tên kỳ, team, ngày tạo)
- Định dạng ô nhập liệu chi phí để nhận giá trị số
- Bổ sung sheet hướng dẫn cách sử dụng template

### 3. Validation & Error Handling

#### UI/UX
- Hiển thị lỗi rõ ràng, chi tiết và dễ hiểu
- Phân loại lỗi (định dạng file, lỗi cấu trúc, lỗi dữ liệu)
- Hiển thị lỗi theo dòng/cột để người dùng dễ dàng sửa chữa

#### Functionality
- Validation định dạng file (chỉ cho phép Excel, CSV)
- Validation cấu trúc cột (đảm bảo đúng các cột của template)
- Validation dữ liệu:
  - Mã nhân viên phải tồn tại trong hệ thống và thuộc phạm vi quản lý
  - Chi phí phải là số, không âm, và nằm trong ngưỡng hợp lý
  - Các trường bắt buộc không được trống
- Báo cáo lỗi chi tiết (dòng, cột, giá trị, lý do)
- Xử lý các kịch bản đặc biệt (sửa dữ liệu đã có, trùng lặp...)

### 4. Xử lý Ghi đè & Cảnh báo

#### UI/UX
- Cảnh báo người dùng về việc ghi đè dữ liệu
- Hiển thị dialog xác nhận khi cần

#### Functionality
- Xử lý logic ghi đè dữ liệu chi phí đã tồn tại
- Cảnh báo người dùng trước khi ghi đè
- Cung cấp tùy chọn cho việc xử lý dữ liệu trùng lặp (bỏ qua, ghi đè, báo lỗi)
- Log lại các thay đổi/ghi đè để tracking

### 5. Phân quyền

- Admin: Có quyền import dữ liệu (hỗ trợ)
- General Manager (課長): Có quyền import cho phạm vi bộ phận
- Team Leader (部長): Chỉ có quyền import cho team của họ
- Employee: Không có quyền truy cập chức năng này

## Xử lý State và Performance

### State Management
- Sử dụng global state để lưu trạng thái upload/import
- Quản lý trạng thái validation và hiển thị lỗi
- Lưu log import để troubleshooting

### API Integration
- Kết nối với `API-MGN-002` (`POST /api/v1/margins/cost/import`) để import dữ liệu
- Kết nối với API tạo template để tải file mẫu
- Xử lý multipart/form-data cho upload file
- Xử lý response chi tiết từ server (validation errors, results)

### Performance Considerations
- Tối ưu cho file lớn (chunking, progress tracking)
- Hiển thị progress indicator cho quá trình upload/xử lý
- Xử lý timeout/retry cho file lớn hoặc mạng chậm
- Caching template để tải nhanh hơn các lần sau

## Các Task liên quan
- FE-MGN-001: Margin List Page (nơi xem kết quả sau khi import)
- FE-MGN-002: Cost Input Form (tab khác của cùng màn hình)
- FE-MGN-004: Margin Chart components (sử dụng dữ liệu chi phí đã import)

## Tiêu chí chấp nhận
1. Template Generation:
   - Tạo được template chứa danh sách nhân viên chính xác theo phạm vi quản lý
   - File template có định dạng rõ ràng và hướng dẫn sử dụng
   - Các trường bắt buộc được đánh dấu

2. File Upload:
   - Upload thành công các file Excel/CSV
   - Hiển thị progress khi cần
   - Xử lý lỗi upload phù hợp

3. Validation:
   - Phát hiện và báo cáo đầy đủ các lỗi trong file
   - Hiển thị thông báo lỗi chi tiết, rõ ràng
   - Xử lý hợp lý các trường hợp đặc biệt

4. Import Data:
   - Import thành công dữ liệu hợp lệ vào database
   - Áp dụng đúng logic ghi đè
   - Hiển thị summary kết quả import

5. Phân quyền:
   - Chỉ hiển thị chức năng import cho người dùng có quyền
   - Chỉ cho phép import dữ liệu trong phạm vi quản lý

6. Hiệu năng:
   - Xử lý tốt file với nhiều bản ghi (ít nhất 200-300 nhân viên)
   - Thời gian xử lý hợp lý
   - Không freeze UI khi xử lý file lớn

## Resources
- [Tài liệu thiết kế màn hình MH-MGN-02](../../../DD/SCREEN/MH-MGN-02.md)
- [Design mockup MH-MGN-02](../../../assets/frontend/img/MH-MGN-02.png)
- [Danh sách API - Margin Management](../../../BD/API/api_list.md#2-quản-lý-hiệu-suất--margin)
- [Chức năng liên quan: F-2.1 (Nhập/Import Chi phí Nhân sự)](../../../BD/FunctionDesign/FunctionList.md) 