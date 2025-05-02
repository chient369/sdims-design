# Task Detail: FE-DSH-003

## Thông tin chung
- **ID**: FE-DSH-003
- **Tên task**: Xây dựng Revenue/Margin widgets
- **Độ ưu tiên**: Cao
- **Estimate**: 3 ngày
- **Tham chiếu**: 
  - Màn hình: [MH-DSH-01](../../../assets/frontend/img/MH-DSH-01.png)
  - Mô tả chi tiết: [Design/DD/SCREEN/MH-DSH-01.md](../../../DD/SCREEN/MH-DSH-01.md)
  - API: [API-MGN-002](../../../DD/API/API-MGN-002.md), [API-CTR-015](../../../DD/API/API-CTR-015.md), [API-RPT-006](../../../DD/API/API-RPT-006.md)
- **Prerequisites**:
  - FE-INFRA-001: Khởi tạo project React
  - FE-INFRA-005: Thiết lập state management
  - FE-INFRA-006: Thiết lập API client và interceptors
  - FE-CORE-007: Xây dựng Chart/Graph components
  - FE-DSH-001: Xây dựng Dashboard Page (MH-DSH-01)

## Mô tả
Xây dựng các widget liên quan đến tài chính trên Dashboard chính, bao gồm thông tin về margin theo team/bộ phận, so sánh doanh thu thực tế với KPI, và tình hình công nợ quá hạn. Các widget này sẽ cung cấp cái nhìn tổng quan về hiệu quả tài chính của doanh nghiệp, giúp quản lý cấp cao và lãnh đạo nhanh chóng nắm bắt tình hình và đưa ra các quyết định kinh doanh kịp thời.

## Yêu cầu kỹ thuật

### 1. Widget W-MGN-01 - Phân bổ Margin (Team/Bộ phận)

#### UI/UX
- Thiết kế dạng card với header, content area và footer
- Hiển thị thông tin tổng hợp về margin trung bình theo team
- Biểu đồ tròn (pie/donut chart) thể hiện tỷ lệ % nhân viên theo trạng thái Margin:
  - Red: Margin thấp hơn ngưỡng tối thiểu
  - Yellow: Margin ở mức cảnh báo
  - Green: Margin tốt
- Hiển thị số lượng cụ thể cho mỗi trạng thái
- Cung cấp link "Xem chi tiết" ở footer để chuyển đến màn hình bảng Margin (MH-MGN-01)
- Màu sắc tuân thủ theo thiết kế hệ thống (red, yellow, green cho các trạng thái margin)

#### Functionality
- Lấy dữ liệu margin từ API và phân loại theo ngưỡng cấu hình
- Tính toán số lượng nhân viên trong mỗi trạng thái margin và tỷ lệ phần trăm tương ứng
- Khi click vào một phần của biểu đồ, hiển thị popup với danh sách nhân viên thuộc trạng thái đó
- Click vào "Xem chi tiết" sẽ chuyển đến trang MH-MGN-01
- Tuân thủ phân quyền: chỉ hiển thị thông tin của team phù hợp với quyền của người dùng

#### API Integration
- `GET /api/v1/margins/summary` (API-MGN-002) với filter thời gian và team (nếu cần)
- Dữ liệu cần xử lý bao gồm:
  - Tổng số nhân viên
  - Số lượng và danh sách nhân viên theo từng trạng thái margin
  - Ngưỡng cấu hình cho các trạng thái (Red/Yellow/Green)

### 2. Widget W-CTR-02 - Doanh thu (vs KPI)

#### UI/UX
- Thiết kế dạng card với header và content area
- Hiển thị doanh thu thực tế và KPI doanh thu dưới dạng số
- Progress bar trực quan hiển thị % hoàn thành so với KPI
- Màu sắc progress bar thay đổi tùy theo mức độ hoàn thành (đỏ: thấp, vàng: trung bình, xanh: đạt/vượt)
- Hiển thị % hoàn thành bên cạnh progress bar
- Có thể kèm theo mini line/bar chart thể hiện xu hướng doanh thu theo thời gian (nếu không làm trang dashboard quá phức tạp)
- Dropdown filter cho phép chọn khoảng thời gian (năm, quý, tháng)

#### Functionality
- Lấy dữ liệu doanh thu thực tế và KPI từ API
- Tính toán tỷ lệ % hoàn thành (Actual/KPI * 100%)
- Cập nhật progress bar và thông tin hiển thị theo filter thời gian
- Nếu có thể, hiển thị breakdown theo team/sales person khi hover hoặc click vào các thành phần
- Click vào widget sẽ chuyển đến báo cáo doanh thu chi tiết

#### API Integration
- `GET /api/v1/sales-kpis` (API-CTR-015) với filter thời gian và team
- Xử lý dữ liệu để tính toán tỷ lệ hoàn thành và hiển thị trực quan

### 3. Widget W-CTR-03 - Công nợ Quá hạn

#### UI/UX
- Thiết kế dạng card với header và content area
- Hiển thị tổng giá trị các khoản thanh toán quá hạn dưới dạng số lớn
- Có thể kèm theo mini pie chart phân loại công nợ theo mức độ quá hạn:
  - 1-30 ngày
  - 31-60 ngày
  - Sau 60 ngày
- Hiển thị số lượng hợp đồng có khoản thanh toán quá hạn
- Cung cấp link "Xem chi tiết" để chuyển đến báo cáo công nợ chi tiết
- Sử dụng màu cảnh báo (đỏ) cho số liệu chính

#### Functionality
- Lấy dữ liệu về các khoản thanh toán quá hạn từ API
- Tính toán tổng giá trị và phân loại theo thời gian quá hạn
- Click vào chart hoặc "Xem chi tiết" sẽ chuyển đến báo cáo công nợ với filter tương ứng
- Cập nhật số liệu theo khoảng thời gian được chọn trên dashboard

#### API Integration
- `GET /api/v1/reports/payment-status` (API-RPT-006) với filter status=overdue
- Xử lý dữ liệu trả về để hiển thị tổng giá trị, số lượng hợp đồng, và phân loại theo thời gian quá hạn

### Phân quyền
- Widget W-MGN-01 hiển thị cho: Admin, Trưởng bộ phận, Team Leader (chỉ dữ liệu team của họ)
- Widget W-CTR-02 hiển thị cho: Admin, Trưởng bộ phận, Leader, Sales
- Widget W-CTR-03 hiển thị cho: Admin, Trưởng bộ phận, Leader, Kế toán
- Sales chỉ thấy dữ liệu KPI và doanh thu của chính họ
- Nhân viên thông thường không thấy các widget này

## Xử lý State và Performance

### State Management
- Sử dụng React Query hoặc Redux để quản lý state và cache API data
- Đồng bộ các filter thời gian và team giữa các widget để tránh inconsistency
- Implement loading, error, và empty states cho mỗi widget

### Performance Optimization
- Sử dụng memo, useMemo, useCallback để tối ưu re-rendering
- Tính toán các metrics phức tạp ở server side nếu có thể
- Lazy load các components và thư viện chart
- Áp dụng debounce cho các filter thay đổi thường xuyên
- Cache dữ liệu API trong một khoảng thời gian hợp lý

## Các Task liên quan
- FE-DSH-001: Dashboard Page (parent task)
- FE-MGN-001: Margin List Page (liên kết từ widget W-MGN-01)
- FE-CTR-001, FE-CTR-002: Contract List/Detail Pages (liên kết từ widget W-CTR-03)

## Tiêu chí chấp nhận
1. Các widget Revenue/Margin hiển thị đúng theo thiết kế trong MH-DSH-01.png
2. Dữ liệu được load thành công từ API endpoints và hiển thị chính xác
3. Biểu đồ trong các widget hiển thị đúng và có interactive elements (hover tooltips, click actions)
4. Navigation đến các trang chi tiết hoạt động chính xác
5. Phân quyền được áp dụng đúng, mỗi vai trò chỉ thấy dữ liệu được phép
6. Các widget cập nhật khi filter dashboard thay đổi (khoảng thời gian, team)
7. Trạng thái loading/empty/error được xử lý hợp lý
8. Số liệu và trạng thái margin (red/yellow/green) hiển thị chính xác theo ngưỡng cấu hình
9. Responsive design phù hợp với các kích thước màn hình khác nhau

## Resources
- [Thiết kế màn hình: MH-DSH-01 - Dashboard](../../../BD/ScreenDesign/ScreenBD.md#mh-dsh-01)
- [Thiết kế màn hình: MH-MGN-01 - Bảng Margin Nhân sự](../../../BD/ScreenDesign/ScreenBD.md#mh-mgn-01)
- [Screen Design Diagram: MH-DSH-01](../../../BD/ScreenDesign/diagram/MH-DSH-01.puml)
- [API Margin Documentation](../../../BD/API/api_list.md#3-quản-lý-hiệu-suất--margin)
- [API Contract & Revenue Documentation](../../../BD/API/api_list.md#5-quản-lý-hợp-đồng--doanh-thu)
- [Chức năng liên quan: F-2.3, F-2.4, F-2.5, F-2.6 (Margin), F-4.10, F-4.11 (KPI Doanh thu)](../../../BD/FunctionDesign/FunctionList.md) 