# Task BE-CORE-005: Xây dựng module S3 Storage

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 3 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Infrastructure Tasks:
    - [BE-INFRA-005: Thiết lập AWS S3 cho lưu trữ tập tin](../BackendTasks.md)
  - Contract File APIs:
    - [API-CTR-009: Get Contract Files](../../../DD/API/API-CTR-009.md)
    - [API-CTR-010: Upload Contract File](../../../DD/API/API-CTR-010.md)
    - [API-CTR-011: Delete Contract File](../../../DD/API/API-CTR-011.md)
  - Yêu cầu chức năng:
    - [F-4.3: Upload/Đính kèm File Hợp đồng](../../../BD/FunctionDesign/FunctionList.md)
  - Yêu cầu phi chức năng:
    - NF-5, NF-6 (Bảo mật - file security)
    - NF-16 (Khả năng mở rộng - file storage)

## Mô tả
Xây dựng module S3 Storage để quản lý việc lưu trữ, truy xuất và xử lý tập tin trong hệ thống, bao gồm hợp đồng, ảnh nhân viên và các tài liệu đính kèm khác.

## Chi tiết công việc
1. **Tạo utility cho upload/download file từ S3**:
   - Xây dựng class `S3Storage` để quản lý các thao tác với S3.
   - Tạo các methods cho upload, download, delete, và list files.
   - Triển khai presigned URL cho việc upload/download trực tiếp từ client.
   - Xử lý concurrent uploads và chunked uploads cho file lớn.
   - Cấu hình retry mechanism cho các thao tác không thành công.

2. **Quản lý file metadata và định danh**:
   - Thiết kế cấu trúc thư mục hợp lý trên S3 (organization by module/type/date).
   - Tạo hệ thống đặt tên file để tránh trùng lặp và dễ quản lý.
   - Lưu trữ và quản lý metadata của file (original filename, type, size, upload date, owner).
   - Hỗ trợ versioning cho các file quan trọng.

3. **Xử lý file validation và security**:
   - Triển khai validation cho các loại file được phép (MIME types, file extensions).
   - Kiểm tra kích thước file và giới hạn phù hợp.
   - Scan file uploads cho malware/virus (tùy chọn).
   - Đảm bảo file permissions được áp dụng đúng đắn.

4. **Triển khai image processing**:
   - Tạo các utilities để resize, crop và xử lý ảnh trước khi lưu trữ.
   - Hỗ trợ tạo thumbnails cho hình ảnh.
   - Xử lý metadata của ảnh (EXIF data).
   - Tối ưu hóa file ảnh để giảm kích thước lưu trữ.

5. **Tích hợp với các module khác**:
   - Tích hợp với Contract Management để lưu trữ hợp đồng và tài liệu đính kèm.
   - Tích hợp với Employee Management để lưu trữ hồ sơ và ảnh nhân viên.
   - Cấu hình permissions cho việc truy cập file dựa trên vai trò người dùng.

## Đầu ra dự kiến
- Module S3 Storage hoàn chỉnh với các tính năng nêu trên.
- API interfaces cho upload/download/management file.
- Cấu trúc thư mục và naming conventions được thiết lập.
- Unit tests cho các chức năng chính.
- Documentation cho việc sử dụng module này.

## Tài liệu tham khảo
- [AWS S3 Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- [S3 Security Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
- [Python Boto3 S3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html)
- [AWS S3 Transfer Manager](https://github.com/boto/s3transfer)
- [Pillow Documentation](https://pillow.readthedocs.io/en/stable/) (cho image processing)
- [AWS Macie](https://docs.aws.amazon.com/macie/latest/user/what-is-macie.html) (cho sensitive data detection trong files)
- [ClamAV for AWS Lambda](https://github.com/awslabs/cdk-serverless-clamscan) (virus scanning - optional) 