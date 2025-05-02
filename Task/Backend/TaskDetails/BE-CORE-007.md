# Task BE-CORE-007: Xây dựng Seeder/Migrations Framework

## Thông tin chung
- **Độ ưu tiên**: Trung bình
- **Thời gian dự kiến**: 2 ngày
- **Người phụ trách**: TBD
- **Liên quan tới**: 
  - Core Modules:
    - [BE-CORE-001: Module Database Connection](BE-CORE-001.md)
  - Infrastructure Tasks:
    - [BE-INFRA-003: Thiết lập cơ sở dữ liệu DynamoDB](../BackendTasks.md)
  - Database Model Tasks:
    - Tất cả task BE-DB-001 đến BE-DB-007 (database models)
  - Yêu cầu phi chức năng:
    - NF-12, NF-13 (Khả năng bảo trì)
    - NF-15, NF-16 (Khả năng mở rộng)
    - NF-19 (Khả năng phục hồi)

## Mô tả
Xây dựng framework cho database migrations và seeding để quản lý các thay đổi schema database và tạo dữ liệu mẫu cho các môi trường development, testing và production.

## Chi tiết công việc
1. **Thiết lập Alembic cho database migrations**:
   - Cấu hình Alembic để quản lý database migrations.
   - Tạo cấu trúc thư mục và files cho migrations.
   - Tạo script khởi tạo để generate migration files từ SQLAlchemy models.
   - Triển khai CLI commands để tạo, chạy và rollback migrations.
   - Cấu hình để chạy migrations tự động trong quá trình deployment.

2. **Tạo seed data cho development/testing**:
   - Thiết kế framework để tạo và quản lý seed data.
   - Tạo các fixture modules cho các loại dữ liệu khác nhau (users, employees, configs).
   - Xây dựng CLI commands để chạy seeding cho các môi trường khác nhau.
   - Đảm bảo idempotency cho các lệnh seeding (có thể chạy nhiều lần mà không tạo ra dữ liệu trùng lặp).
   - Triển khai tính năng để xóa và tạo lại seed data khi cần.

3. **Triển khai schema versioning**:
   - Thiết lập cơ chế theo dõi version của database schema.
   - Tạo script để kiểm tra và validate phiên bản schema hiện tại.
   - Triển khai logic để ngăn chặn việc chạy ứng dụng với schema không tương thích.
   - Đảm bảo backward compatibility khi có thể.

4. **Tạo factory classes cho test data**:
   - Xây dựng các factory classes để tạo dữ liệu test một cách linh hoạt.
   - Hỗ trợ các scenarios khác nhau cho unit và integration testing.
   - Triển khai các helpers để setup và clean up test data.

5. **Tạo utilities cho data migration**:
   - Xây dựng các utilities để migrate dữ liệu khi schema thay đổi.
   - Hỗ trợ các tình huống phức tạp như splitting/merging tables, restructuring data.
   - Tạo logging và monitoring cho quá trình data migration.

## Đầu ra dự kiến
- Alembic được cấu hình và sẵn sàng sử dụng.
- Framework seeding hoàn chỉnh với các tính năng nêu trên.
- CLI commands cho việc quản lý migrations và seeding.
- Factory classes cho testing.
- Documentation và hướng dẫn sử dụng.

## Tài liệu tham khảo
- [Alembic Documentation](https://alembic.sqlalchemy.org/en/latest/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [Python Factory-Boy Documentation](https://factoryboy.readthedocs.io/en/stable/)
- [Pytest Fixtures Documentation](https://docs.pytest.org/en/6.2.x/fixture.html)
- [AWS DynamoDB Migrations Patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/dynamodb-data-modeling/modeling-best-practices.html)
- [Database Seeding Best Practices](https://martinfowler.com/articles/evodb.html)
- [Database Versioning Best Practices](https://schemahero.io/blog/2020/01/13/database-versioning-best-practices/) 