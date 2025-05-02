# Task Detail: FE-CORE-009

## Thông tin chung
- **ID**: FE-CORE-009
- **Tên task**: Xây dựng Authorization components
- **Độ ưu tiên**: Cao
- **Estimate**: 2 days
- **Tham chiếu**: Toàn bộ ứng dụng có các chức năng phân quyền

## Mô tả
Xây dựng bộ components và utilities để quản lý phân quyền/ủy quyền trong toàn bộ ứng dụng SDIMS. Task bao gồm việc tạo các components như PrivateRoute để bảo vệ routes, AuthGuard để kiểm tra quyền truy cập, và các hàm tiện ích để thực hiện permission-based rendering (hiển thị/ẩn các phần UI dựa trên quyền của người dùng). Các components này đảm bảo rằng người dùng chỉ có thể truy cập và tương tác với những tính năng mà họ được phép.

## Yêu cầu kỹ thuật
### Route Protection
- PrivateRoute component để kiểm tra authentication
- Role-based route guards
- Permission-based route guards
- Redirecting về login page hoặc unauthorized page khi cần
- Lưu intended destination để redirect sau khi login

### Authorization Guards
- Kiểm tra permissions cho components hoặc actions
- Role-based access control (RBAC) guards
- Conditional rendering dựa trên permissions
- Audit logging cho các hành động quan trọng (nếu cần thiết)
- Error handling khi access bị từ chối

### Permission Utilities
- Hook check permissions
- HOC (Higher Order Component) cho permission-based rendering
- Method check ownership (where applicable)
- Method check role hierarchy
- Method check feature-based permissions

### Authorization State Management
- Tích hợp với Auth Context
- Permission caching để tránh gọi lại API liên tục
- Permission synchronization

## Chi tiết các thành phần

### PrivateRoute Component
```tsx
// src/components/auth/PrivateRoute.tsx
import React from 'react';
import { Route, Navigate, useLocation, RouteProps } from 'react-router-dom';
import { useAuth } from '../../hooks/useAuth';
import { Spinner } from '../ui/Spinner';

interface PrivateRouteProps extends Omit<RouteProps, 'element'> {
  element: React.ReactNode;
  requiredPermissions?: string[];
  requiredRoles?: string[];
}

export const PrivateRoute: React.FC<PrivateRouteProps> = ({
  element,
  requiredPermissions = [],
  requiredRoles = [],
  ...rest
}) => {
  const { isAuthenticated, isLoading, hasPermissions, hasRoles } = useAuth();
  const location = useLocation();

  // If auth state is still loading, show a spinner
  if (isLoading) {
    return (
      <div className="flex items-center justify-center h-screen">
        <Spinner size="lg" />
      </div>
    );
  }

  // Check if user is authenticated
  if (!isAuthenticated) {
    // Save the intended destination
    return <Navigate to="/login" state={{ from: location.pathname }} replace />;
  }

  // Check permissions if required
  if (requiredPermissions.length > 0 && !hasPermissions(requiredPermissions)) {
    return <Navigate to="/unauthorized" replace />;
  }

  // Check roles if required
  if (requiredRoles.length > 0 && !hasRoles(requiredRoles)) {
    return <Navigate to="/unauthorized" replace />;
  }

  // Render the protected component
  return <>{element}</>;
};
```

### AuthGuard Component
```tsx
// src/components/auth/AuthGuard.tsx
import React from 'react';
import { useAuth } from '../../hooks/useAuth';

interface AuthGuardProps {
  permissions?: string[];
  roles?: string[];
  children: React.ReactNode;
  fallback?: React.ReactNode;
}

export const AuthGuard: React.FC<AuthGuardProps> = ({
  permissions = [],
  roles = [],
  children,
  fallback = null,
}) => {
  const { hasPermissions, hasRoles } = useAuth();

  const hasAccess = (
    (permissions.length === 0 || hasPermissions(permissions)) &&
    (roles.length === 0 || hasRoles(roles))
  );

  if (!hasAccess) {
    return <>{fallback}</>;
  }

  return <>{children}</>;
};
```

### Permission Hooks
```tsx
// src/hooks/usePermissions.tsx
import { useAuth } from './useAuth';

export function usePermissions() {
  const { user, permissions, roles, hasPermissions, hasRoles } = useAuth();

  const can = (permission: string) => {
    return hasPermissions([permission]);
  };

  const canAny = (requiredPermissions: string[]) => {
    return requiredPermissions.some(permission => hasPermissions([permission]));
  };

  const canAll = (requiredPermissions: string[]) => {
    return hasPermissions(requiredPermissions);
  };

  const is = (role: string) => {
    return hasRoles([role]);
  };

  const isAny = (requiredRoles: string[]) => {
    return requiredRoles.some(role => hasRoles([role]));
  };

  const isAllRoles = (requiredRoles: string[]) => {
    return hasRoles(requiredRoles);
  };

  return {
    can,
    canAny,
    canAll,
    is,
    isAny,
    isAllRoles,
    permissions,
    roles,
    user,
  };
}
```

### Permission HOC
```tsx
// src/components/auth/withPermissions.tsx
import React from 'react';
import { usePermissions } from '../../hooks/usePermissions';

interface WithPermissionsOptions {
  permissions?: string[];
  roles?: string[];
  all?: boolean; // If true, all permissions/roles are required; if false, any is enough
  fallback?: React.ReactNode;
}

export function withPermissions(options: WithPermissionsOptions = {}) {
  const {
    permissions = [],
    roles = [],
    all = true,
    fallback = null,
  } = options;

  return function <P extends object>(WrappedComponent: React.ComponentType<P>) {
    const WithPermissionsComponent: React.FC<P> = (props) => {
      const { canAll, canAny, isAllRoles, isAny } = usePermissions();

      const hasRequiredPermissions =
        permissions.length === 0 ||
        (all ? canAll(permissions) : canAny(permissions));

      const hasRequiredRoles =
        roles.length === 0 ||
        (all ? isAllRoles(roles) : isAny(roles));

      if (!hasRequiredPermissions || !hasRequiredRoles) {
        return <>{fallback}</>;
      }

      return <WrappedComponent {...props} />;
    };

    WithPermissionsComponent.displayName = `withPermissions(${WrappedComponent.displayName || WrappedComponent.name || 'Component'})`;

    return WithPermissionsComponent;
  };
}
```

### PermissionButton Component
```tsx
// src/components/auth/PermissionButton.tsx
import React from 'react';
import { Button, ButtonProps } from '../ui/Button';
import { usePermissions } from '../../hooks/usePermissions';

interface PermissionButtonProps extends ButtonProps {
  permissions?: string[];
  roles?: string[];
  all?: boolean;
  hideWhenUnauthorized?: boolean;
}

export const PermissionButton: React.FC<PermissionButtonProps> = ({
  permissions = [],
  roles = [],
  all = true,
  hideWhenUnauthorized = false,
  children,
  ...rest
}) => {
  const { canAll, canAny, isAllRoles, isAny } = usePermissions();

  const hasRequiredPermissions =
    permissions.length === 0 ||
    (all ? canAll(permissions) : canAny(permissions));

  const hasRequiredRoles =
    roles.length === 0 ||
    (all ? isAllRoles(roles) : isAny(roles));

  const hasAccess = hasRequiredPermissions && hasRequiredRoles;

  if (!hasAccess && hideWhenUnauthorized) {
    return null;
  }

  return (
    <Button
      {...rest}
      disabled={!hasAccess || rest.disabled}
      title={!hasAccess ? "You don't have permission to perform this action" : rest.title}
    >
      {children}
    </Button>
  );
};
```

### UnauthorizedPage Component
```tsx
// src/components/auth/UnauthorizedPage.tsx
import React from 'react';
import { Link } from 'react-router-dom';
import { ShieldExclamationIcon } from '@heroicons/react/outline';
import { Button } from '../ui/Button';

interface UnauthorizedPageProps {
  title?: string;
  message?: string;
}

export const UnauthorizedPage: React.FC<UnauthorizedPageProps> = ({
  title = 'Access Denied',
  message = 'You do not have permission to access this page.',
}) => {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
      <div className="max-w-md w-full text-center">
        <ShieldExclamationIcon className="mx-auto h-16 w-16 text-red-500" />
        <h2 className="mt-6 text-2xl font-bold text-gray-900">{title}</h2>
        <p className="mt-2 text-sm text-gray-600">{message}</p>
        <div className="mt-6 space-x-2">
          <Button as={Link} to="/dashboard" variant="outline">
            Go to Dashboard
          </Button>
          <Button as={Link} to="/">
            Go to Home
          </Button>
        </div>
      </div>
    </div>
  );
};
```

### Routes Configuration with Auth
```tsx
// Example of routes configuration with auth
// src/routes/index.tsx
import React from 'react';
import { Routes, Route, Navigate } from 'react-router-dom';
import { PrivateRoute } from '../components/auth/PrivateRoute';
import { UnauthorizedPage } from '../components/auth/UnauthorizedPage';
import { MainLayout } from '../components/layout/MainLayout';
import { AuthLayout } from '../components/layout/AuthLayout';
import { Dashboard } from '../pages/Dashboard';
import { LoginPage } from '../pages/auth/LoginPage';
import { EmployeeList } from '../pages/hrm/EmployeeList';
import { EmployeeDetails } from '../pages/hrm/EmployeeDetails';
import { MarginList } from '../pages/margin/MarginList';
import { OpportunityList } from '../pages/opportunities/OpportunityList';
import { NotFoundPage } from '../pages/NotFoundPage';

export const AppRoutes: React.FC = () => {
  return (
    <Routes>
      {/* Public routes */}
      <Route element={<AuthLayout />}>
        <Route path="/login" element={<LoginPage />} />
      </Route>

      {/* Auth error routes */}
      <Route path="/unauthorized" element={<UnauthorizedPage />} />

      {/* Protected routes */}
      <Route element={<MainLayout />}>
        <Route path="/" element={<Navigate to="/dashboard" replace />} />
        
        <Route
          path="/dashboard"
          element={
            <PrivateRoute
              element={<Dashboard />}
              requiredPermissions={['view:dashboard']}
            />
          }
        />
        
        <Route
          path="/hrm/employees"
          element={
            <PrivateRoute
              element={<EmployeeList />}
              requiredPermissions={['view:employees']}
            />
          }
        />
        
        <Route
          path="/hrm/employees/:id"
          element={
            <PrivateRoute
              element={<EmployeeDetails />}
              requiredPermissions={['view:employee_details']}
            />
          }
        />
        
        <Route
          path="/margin"
          element={
            <PrivateRoute
              element={<MarginList />}
              requiredPermissions={['view:margin']}
              requiredRoles={['manager', 'admin']}
            />
          }
        />
        
        <Route
          path="/opportunities"
          element={
            <PrivateRoute
              element={<OpportunityList />}
              requiredPermissions={['view:opportunities']}
            />
          }
        />
        
        {/* Fallback for unknown routes */}
        <Route path="*" element={<NotFoundPage />} />
      </Route>
    </Routes>
  );
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-003: Router Setup (prerequisite)
- FE-INFRA-005: State Management (related - for Auth state)
- FE-CORE-001: Layout Components (dependent)
- FE-AUTH-001: Login Page (related)

## Tiêu chí chấp nhận
- PrivateRoute component redirects unauthenticated users to login page
- Route guards kiểm tra permissions/roles đúng cách
- AuthGuard component chỉ hiển thị nội dung khi user có permission/role cần thiết
- Hooks (usePermissions) để dễ dàng kiểm tra permissions/roles
- HOC withPermissions hoạt động đúng với các components
- PermissionButton chỉ enable khi user có permissions cần thiết
- UnauthorizedPage hiển thị đúng khi user không có quyền truy cập
- Routes được cấu hình với các permission/role requirements đúng
- Các components kiểm tra cả permissions lẫn roles khi cần
- Documentation đầy đủ về cách sử dụng các authorization components 