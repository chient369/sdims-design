# Task Detail: FE-CORE-001

## Thông tin chung
- **ID**: FE-CORE-001
- **Tên task**: Xây dựng Layout components
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: Các màn hình trong folder [assets/frontend/img](../../../assets/frontend/img)

## Mô tả
Xây dựng các layout components cơ bản cho toàn bộ ứng dụng, bao gồm MainLayout với sidebar, header, content area cho các trang chính, AuthLayout cho các trang đăng nhập/đăng ký, và ErrorBoundary component để bắt lỗi. Các components này sẽ được sử dụng xuyên suốt trong ứng dụng, đảm bảo tính nhất quán và tái sử dụng cao.

## Yêu cầu kỹ thuật
### Layout & Structure
- Thiết kế responsive cho các breakpoints: mobile, tablet, desktop
- Sidebar có thể collapse/expand
- Fixed header ở trên cùng
- Main content area với scroll độc lập
- Breadcrumb navigation
- Footer area

### UI Components
- **MainLayout**: Layout chính cho các trang khi đã đăng nhập
  - Container chính: full-width, min-height 100vh
  - Sidebar: fixed position, có thể collapse
  - Header: fixed top, với user info và notifications
  - Content area: với padding phù hợp, scroll độc lập
  - Breadcrumb: hiển thị ở trên cùng của content area
  - Footer: ở cuối content area

- **AuthLayout**: Layout cho trang đăng nhập và đăng ký
  - Container chính: centered, card-based
  - Logo và branding
  - Background tùy chỉnh (gradient hoặc hình ảnh)
  - Support responsive

- **ErrorBoundary**: 
  - Wrapper component để bắt React errors
  - Fallback UI khi có lỗi
  - Logging và reporting

### Styling
- Sử dụng Tailwind CSS
- Áp dụng design tokens và biến cho colors, spacing, typography

### Functionality
- Responsive behavior cho tất cả layouts
- Sidebar toggle functionality
- User menu dropdown trong header
- Breadcrumb dynamic generation
- Error handling và logging
- Phân quyền hiển thị menu items trong sidebar

## Chi tiết các thành phần
### MainLayout Component

```jsx
// Pseudo-code structure
const MainLayout = ({ children }) => {
  const [sidebarCollapsed, setSidebarCollapsed] = useState(false);
  
  return (
    <div className="app-container">
      <Sidebar collapsed={sidebarCollapsed} onToggle={() => setSidebarCollapsed(!sidebarCollapsed)} />
      <div className="main-content">
        <Header />
        <Breadcrumb />
        <main>{children}</main>
        <Footer />
      </div>
    </div>
  );
};
```

#### Sidebar Component
- Logo app ở trên cùng
- Menu chính với icons và text
- Menu items được nhóm theo categories
- Active state cho current route
- Collapse/Expand button
- Footer với version info và links
- Menu items được render dựa trên user permissions

#### Header Component
- App title/breadcrumb ở bên trái
- User dropdown menu (avatar, name, role)
- Notifications icon với counter
- Action buttons khác

#### Breadcrumb Component
- Dynamic generation dựa trên current route
- Clickable links cho navigation
- Hỗ trợ custom override cho specific routes

#### Footer Component 
- Copyright info
- Links hữu ích
- Support contact

### AuthLayout Component
- Centered card với logo
- Slots cho form elements
- Background styling phù hợp
- Responsive behavior

### ErrorBoundary Component
- Sử dụng React Error Boundary pattern
- Graceful degradation UI
- Error logging/reporting
- Reset functionality

## Các dependencies
- React Router cho navigation
- Tailwind CSS cho styling
- HeadlessUI hoặc Radix UI cho accessible dropdown/menu components
- React Icons hoặc Heroicons cho icon set

## Tiêu chí chấp nhận
- Layouts hiển thị đúng trên tất cả kích thước màn hình (mobile, tablet, desktop)
- Sidebar collapse/expand hoạt động mượt mà với animations
- Breadcrumb hiển thị chính xác dựa trên current route
- ErrorBoundary bắt và hiển thị lỗi đúng cách
- Menu items trong sidebar hiển thị dựa trên user permissions
- Code đã được tổ chức thành các components tái sử dụng
- Styling nhất quán với design system
- Documentation đầy đủ cho việc sử dụng các components 