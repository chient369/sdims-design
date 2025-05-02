# Task Detail: FE-INFRA-001

## Thông tin chung
- **ID**: FE-INFRA-001
- **Tên task**: Khởi tạo project React với Vite
- **Độ ưu tiên**: Cao
- **Estimate**: 2 days
- **Tham chiếu**: 
  - N/A (Task cơ sở hạ tầng)

## Mô tả
Khởi tạo và cấu hình dự án React frontend cho hệ thống SDIMS (Internal Management System) sử dụng Vite làm công cụ build. Task này bao gồm thiết lập cấu trúc thư mục theo các best practices, cấu hình các công cụ kiểm tra code chất lượng (linting, formatting), và thiết lập các file cấu hình cần thiết. Đây là task nền tảng, tạo cơ sở cho tất cả các task frontend khác.

## Yêu cầu kỹ thuật
### Khởi tạo dự án
- Sử dụng Vite để khởi tạo dự án React với TypeScript
- Cấu hình package.json với các script cần thiết (dev, build, preview, lint)
- Thiết lập .gitignore phù hợp 
- Cấu hình các file môi trường (.env, .env.development, .env.production)

### Cấu trúc thư mục
Thiết lập cấu trúc thư mục tuân theo các best practices:
```
src/
  assets/           # Static assets (images, fonts, etc.)
  components/       # Shared components
    ui/             # Base UI components
    layout/         # Layout components
    forms/          # Form-related components
    tables/         # Table-related components
    modals/         # Modal-related components
  features/         # Feature-based modules
    auth/           # Authentication related
    dashboard/      # Dashboard related
    hrm/            # Human Resource Management
    margin/         # Margin Management
    contracts/      # Contract Management
    opportunities/  # Opportunity Management
  hooks/            # Custom hooks
  context/          # React context definitions
  services/         # API services
  utils/            # Utility functions
  types/            # TypeScript type definitions
  config/           # Application configuration
  routes/           # Routing definitions
```

### Cấu hình Linting và Formatting
- Cài đặt và cấu hình ESLint với các rules phù hợp
  - Tích hợp plugin React và TypeScript
  - Thêm các rules quan trọng cho maintainability
- Cài đặt và cấu hình Prettier cho code formatting
  - Thiết lập file .prettierrc với các quy tắc thống nhất
- Tích hợp ESLint và Prettier (eslint-config-prettier)
- Thiết lập pre-commit hooks với husky và lint-staged

### Cấu hình TypeScript
- Thiết lập tsconfig.json với các options phù hợp
- Cấu hình path aliases để dễ dàng import
- Đảm bảo strict mode được bật
- Thiết lập các compiler options cần thiết

### Dependencies
Cài đặt các dependencies cốt lõi:
- **UI Framework**: Tailwind CSS
- **HTTP Client**: Axios
- **Forms**: React Hook Form
- **State Management**: Zustand/Redux Toolkit/React Query (tùy theo quyết định)
- **Routing**: React Router
- **Date handling**: date-fns
- **Validation**: Zod
- **Type checking**: TypeScript

## Chi tiết các thành phần

### Cấu hình Vite
- Tạo và cấu hình vite.config.ts
  - Thiết lập path aliases
  - Cấu hình plugins cần thiết
  - Tối ưu hóa build process

### Tailwind CSS Setup
- Cài đặt và cấu hình Tailwind CSS
- Tạo tailwind.config.js với theme customization:
  - Color palette dựa trên thiết kế hệ thống
  - Typography settings
  - Screen breakpoints
  - Các plugin mở rộng cần thiết

### Cấu hình API Client
- Tạo lớp base API client với Axios
- Thiết lập interceptors cơ bản
- Cấu hình các thông số mặc định (timeout, baseURL, v.v.)

### Testing Setup
- Cài đặt và cấu hình Vitest
- Thiết lập testing utilities
- Cấu hình test coverage reporting

### Documentation Setup
- Tạo README.md với hướng dẫn cơ bản và mô tả về cấu trúc dự án
- Thiết lập JSDoc hoặc tương tự cho code documentation

## Các Task liên quan
- FE-INFRA-002: CI/CD Pipeline (dependent)
- FE-INFRA-003: Router Setup (dependent)
- FE-INFRA-004: Theme System (dependent)
- FE-INFRA-005: State Management (dependent)

## Tiêu chí chấp nhận
- Dự án khởi tạo thành công với Vite và chạy được trên local
- Cấu trúc thư mục được thiết lập đầy đủ và rõ ràng
- ESLint và Prettier được cấu hình và hoạt động đúng
- TypeScript được cấu hình với strict mode
- Tailwind CSS được cài đặt và hoạt động chính xác
- Các scripts trong package.json hoạt động đúng (dev, build, lint)
- Pre-commit hooks được thiết lập và hoạt động chính xác
- README với hướng dẫn cơ bản được tạo
- Developer có thể chạy dự án lần đầu tiên với ít hoặc không gặp vấn đề gì 