# Task Detail: FE-INFRA-003

## Thông tin chung
- **ID**: FE-INFRA-003
- **Tên task**: Thiết lập router và cấu trúc điều hướng
- **Độ ưu tiên**: Cao
- **Estimate**: 2 days
- **Tham chiếu**: 
  - N/A (Task cơ sở hạ tầng)

## Mô tả
Thiết lập hệ thống định tuyến (routing) cho ứng dụng SDIMS frontend sử dụng React Router. Task này bao gồm việc cấu hình các routes cho toàn bộ ứng dụng, thiết lập cấu trúc điều hướng theo luồng đã được thiết kế, và xây dựng các thành phần bảo vệ routes dựa trên phân quyền người dùng. Hệ thống routing là phần quan trọng trong cấu trúc ứng dụng, cho phép người dùng điều hướng giữa các trang và module khác nhau một cách mượt mà, đồng thời đảm bảo chỉ những người dùng có quyền mới có thể truy cập các trang được bảo vệ.

## Yêu cầu kỹ thuật
### Cài đặt và cấu hình React Router
- Cài đặt react-router-dom phiên bản mới nhất
- Thiết lập root router component (BrowserRouter)
- Cấu hình history mode (default là browserHistory)
- Tích hợp với suspense/lazy loading cho code splitting

### Cấu trúc route
- Thiết kế cấu trúc route phân cấp rõ ràng:
  - Root routes (/, /login, /dashboard)
  - Module-based routes (/hrm/*, /margin/*, /contracts/*, etc.)
  - Nested routes cho sub-views và tabs
- Thiết lập default routes và fallback (404) route
- Xử lý redirects cho các routes đã thay đổi hoặc legacy

### Route Protection
- Xây dựng PrivateRoute component để bảo vệ routes dựa trên trạng thái đăng nhập
- Thiết lập Route Guards dựa trên user roles và permissions
- Xử lý điều hướng sau đăng nhập/đăng xuất
- Lưu trữ và khôi phục đường dẫn sau khi đăng nhập (redirect after login)

### Navigation Components
- Xây dựng các helpers cho programmatic navigation
- Thiết lập utilities cho URL generation
- Cấu hình scroll behavior khi chuyển trang

### SEO và Metadata
- Tích hợp với helmet hoặc tương tự để quản lý document head
- Thiết lập dynamic page titles và meta tags

## Chi tiết các thành phần

### Main Router Setup
Tạo cấu trúc router chính:
```jsx
// src/routes/index.tsx
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { Suspense, lazy } from 'react';
import PrivateRoute from './PrivateRoute';
import PublicRoute from './PublicRoute';
import LoadingFallback from '../components/ui/LoadingFallback';
import Layout from '../components/layout/MainLayout';
import AuthLayout from '../components/layout/AuthLayout';

// Lazy-loaded components
const Login = lazy(() => import('../features/auth/Login'));
const Dashboard = lazy(() => import('../features/dashboard/Dashboard'));
const NotFound = lazy(() => import('../features/errors/NotFound'));
// More lazy-loaded components for other pages...

const AppRouter = () => {
  return (
    <BrowserRouter>
      <Suspense fallback={<LoadingFallback />}>
        <Routes>
          {/* Public routes */}
          <Route element={<AuthLayout />}>
            <Route path="/login" element={<PublicRoute component={Login} />} />
            {/* Other public routes */}
          </Route>

          {/* Protected routes */}
          <Route element={<Layout />}>
            <Route path="/" element={<Navigate to="/dashboard" replace />} />
            <Route path="/dashboard" element={<PrivateRoute component={Dashboard} />} />
            {/* Other protected routes */}
            
            {/* Module routes */}
            <Route path="/hrm/*" element={<PrivateRoute component={HRMRoutes} />} />
            <Route path="/margins/*" element={<PrivateRoute component={MarginRoutes} />} />
            <Route path="/contracts/*" element={<PrivateRoute component={ContractRoutes} />} />
            {/* More module routes */}
            
            {/* 404 route */}
            <Route path="*" element={<NotFound />} />
          </Route>
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
};

export default AppRouter;
```

### Module Routes
Xây dựng các file định tuyến riêng cho mỗi module lớn:
- `src/routes/HRMRoutes.tsx`
- `src/routes/MarginRoutes.tsx`
- `src/routes/ContractRoutes.tsx`
- `src/routes/OpportunityRoutes.tsx`
- v.v.

### Route Guard Components
Xây dựng các components để bảo vệ routes:

```jsx
// src/routes/PrivateRoute.tsx
import { Navigate, useLocation } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

interface PrivateRouteProps {
  component: React.ComponentType;
  requiredPermissions?: string[];
}

const PrivateRoute: React.FC<PrivateRouteProps> = ({ 
  component: Component, 
  requiredPermissions = [] 
}) => {
  const { isAuthenticated, hasPermissions } = useAuth();
  const location = useLocation();
  
  // Check if user is authenticated
  if (!isAuthenticated) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }
  
  // Check if user has required permissions
  if (requiredPermissions.length > 0 && !hasPermissions(requiredPermissions)) {
    return <Navigate to="/unauthorized" replace />;
  }
  
  // Render the protected component
  return <Component />;
};

export default PrivateRoute;
```

### Navigation Utilities
Xây dựng các utilities cho việc điều hướng:

```typescript
// src/utils/navigation.ts
import { useNavigate } from 'react-router-dom';

export const routePaths = {
  home: '/',
  login: '/login',
  dashboard: '/dashboard',
  hrm: {
    base: '/hrm',
    employees: '/hrm/employees',
    employeeDetail: (id: string) => `/hrm/employees/${id}`,
    newEmployee: '/hrm/employees/new',
  },
  margin: {
    base: '/margins',
    list: '/margins/list',
    costs: '/margins/costs',
  },
  // More route paths
};

export const useAppNavigation = () => {
  const navigate = useNavigate();
  
  return {
    goToHome: () => navigate(routePaths.home),
    goToDashboard: () => navigate(routePaths.dashboard),
    goToEmployeeDetail: (id: string) => navigate(routePaths.hrm.employeeDetail(id)),
    goBack: () => navigate(-1),
    // More navigation helpers
  };
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-005: State Management (related)
- FE-AUTH-001: Login Page (dependent)
- FE-CORE-001: Layout Components (dependent)
- FE-CORE-002: Navigation Components (dependent)

## Tiêu chí chấp nhận
- React Router được cài đặt và cấu hình đúng
- Tất cả các routes được định nghĩa theo cấu trúc phân cấp rõ ràng
- PrivateRoute hoạt động đúng, chuyển hướng người dùng chưa đăng nhập đến trang login
- Route guards kiểm tra permissions/roles một cách chính xác
- Code splitting với lazy loading hoạt động mượt mà
- Điều hướng programmatic hoạt động đúng (goBack, navigate to route, etc.)
- URL params và query params được xử lý đúng
- Scroll behavior được cấu hình phù hợp (scroll to top khi chuyển trang)
- Nested routes hoạt động đúng (cho tabs và sub-views)
- Dynamic page titles hoạt động cho mỗi route 