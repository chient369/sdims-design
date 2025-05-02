# Task BE-CORE-003: Xây dựng module Validation & Exception Handling

## Thông tin chung
- **Độ ưu tiên**: Cao
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Liên quan tới tất cả API endpoints (validation và exception handling là nền tảng cho mọi API)
  - Core Modules:
    - [BE-CORE-004: Module Logger](BE-CORE-004.md) (cho việc log exceptions)
  - Database Tasks:
    - Tất cả Database Model tasks (cho validation model)
  - Yêu cầu phi chức năng: 
    - NF-1, NF-2, NF-3 (Hiệu năng hệ thống - validation chuẩn giúp tránh lỗi runtime)
    - NF-8 (Phòng chống lỗ hổng bảo mật - input validation)
    - NF-14 (Hệ thống logging chi tiết)

## Mô tả
Xây dựng module Validation & Exception Handling để quản lý việc xác thực dữ liệu đầu vào, xử lý các ngoại lệ, và chuẩn hóa định dạng phản hồi API trong toàn bộ hệ thống.

## Chi tiết công việc
1. **Tạo decorator/middleware xử lý exception**:
   - Xây dựng decorator để bắt và xử lý các ngoại lệ trong Lambda handlers.
   - Phân loại các loại lỗi (validation errors, business errors, system errors).
   - Map các ngoại lệ sang HTTP status codes phù hợp.
   - Đảm bảo format thống nhất cho các phản hồi lỗi.
   - Tự động logging lỗi với các cấp độ phù hợp.

2. **Xây dựng validator (sử dụng Pydantic) cho request data**:
   - Tạo utility sử dụng Pydantic để xác thực dữ liệu đầu vào.
   - Xây dựng các model schemas cho mỗi API endpoint.
   - Triển khai custom validators cho các trường hợp phức tạp.
   - Tạo helper functions để chuyển đổi Pydantic validation errors sang response format chuẩn.
   - Tích hợp với API Gateway request parsing.

3. **Thiết lập response formatter chuẩn cho API**:
   - Xây dựng utility để định dạng phản hồi API theo một format thống nhất.
   - Tạo các wrappers cho success/error responses.
   - Hỗ trợ các trường hợp phản hồi đặc biệt (pagination, streaming data).
   - Đảm bảo tất cả API endpoints đều sử dụng format phản hồi giống nhau.

4. **Triển khai business logic validators**:
   - Xây dựng các validators cho các quy tắc nghiệp vụ phức tạp.
   - Tạo custom exceptions cho các trường hợp lỗi nghiệp vụ cụ thể.
   - Đảm bảo rằng các validation rules được áp dụng nhất quán trong toàn bộ hệ thống.

5. **Tích hợp với logging system**:
   - Đảm bảo các lỗi validation và exceptions được log đúng cách.
   - Cấu hình mức độ chi tiết của log phù hợp cho mỗi loại lỗi.
   - Tích hợp với CloudWatch hoặc các dịch vụ monitoring khác.

## Đầu ra dự kiến
- Module Validation & Exception Handling hoàn chỉnh với các tính năng nêu trên.
- Decorator/middleware exception handling có thể sử dụng cho tất cả Lambda handlers.
- Các validation schemas cho các API endpoints chính.
- Định dạng response chuẩn (JSON) được áp dụng trong toàn bộ hệ thống.
- Unit tests cho các chức năng chính.
- Documentation cho việc sử dụng module này.

## Tài liệu tham khảo
- [Pydantic Documentation](https://pydantic-docs.helpmanual.io/)
- [AWS API Gateway Response Formatting](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html)
- [Python Exception Handling Best Practices](https://docs.python.org/3/tutorial/errors.html)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [FastAPI Error Handling](https://fastapi.tiangolo.com/tutorial/handling-errors/) (pattern tham khảo)
- [JSON:API Specification](https://jsonapi.org/) (mẫu response format)
- [OWASP Input Validation Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html) 