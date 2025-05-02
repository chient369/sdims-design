# Task Detail: FE-INFRA-005

## Thông tin chung
- **ID**: FE-INFRA-005
- **Tên task**: Thiết lập state management
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - N/A (Task cơ sở hạ tầng)

## Mô tả
Thiết lập hệ thống quản lý state (state management) cho ứng dụng SDIMS frontend, đảm bảo việc quản lý và chia sẻ dữ liệu giữa các components được tổ chức tốt, hiệu quả và dễ bảo trì. Task này bao gồm việc cài đặt và cấu hình Context API kết hợp với Zustand (hoặc Redux Toolkit) để quản lý global state, thiết lập cấu trúc store hợp lý theo từng domain, và xây dựng các hooks, utilities cần thiết. Một hệ thống state management được thiết kế tốt sẽ giúp ứng dụng dễ dàng mở rộng, quản lý state logic tách biệt với UI, và tối ưu hóa hiệu năng re-render.

## Yêu cầu kỹ thuật
### Cài đặt và cấu hình state management libraries
- Cài đặt Context API (built-in với React)
- Cài đặt và cấu hình Zustand (hoặc Redux Toolkit)
- Cài đặt và cấu hình React Query cho server state management
- Thiết lập các devtools cần thiết

### Thiết kế cấu trúc state
- Phân chia state theo domains (auth, hrm, finance, etc.)
- Thiết kế các slice và cấu trúc store
- Định nghĩa các state selectors
- Thiết lập middleware (nếu cần)

### Xây dựng các Context providers
- Auth context cho quản lý xác thực
- Theme context cho quản lý theme
- Notification context cho hệ thống thông báo
- Modal/Dialog context cho quản lý modals

### Xây dựng các custom hooks
- Hooks để truy cập global state
- Hooks để kết nối và quản lý API calls
- Hooks cho form state management
- Utility hooks cho các logic phổ biến

### Xây dựng utilities cho state management
- Utilities cho immutable state updates
- Cache management utilities
- Type definitions cho state

## Chi tiết các thành phần

### Auth Context
Xây dựng Auth Context để quản lý trạng thái xác thực:

```tsx
// src/context/AuthContext.tsx
import React, { createContext, useContext, useReducer, useEffect } from 'react';
import { User } from '../types/user';
import { authApi } from '../services/authApi';

type AuthState = {
  user: User | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  token: string | null;
  error: string | null;
};

type AuthAction =
  | { type: 'LOGIN_REQUEST' }
  | { type: 'LOGIN_SUCCESS'; payload: { user: User; token: string } }
  | { type: 'LOGIN_FAILURE'; payload: string }
  | { type: 'LOGOUT' }
  | { type: 'RESTORE_AUTH' };

type AuthContextType = {
  state: AuthState;
  login: (username: string, password: string) => Promise<void>;
  logout: () => void;
  hasPermission: (permission: string) => boolean;
};

const initialState: AuthState = {
  user: null,
  isAuthenticated: false,
  isLoading: false,
  token: null,
  error: null,
};

const authReducer = (state: AuthState, action: AuthAction): AuthState => {
  switch (action.type) {
    case 'LOGIN_REQUEST':
      return { ...state, isLoading: true, error: null };
    case 'LOGIN_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isAuthenticated: true,
        user: action.payload.user,
        token: action.payload.token,
        error: null,
      };
    case 'LOGIN_FAILURE':
      return {
        ...state,
        isLoading: false,
        isAuthenticated: false,
        user: null,
        token: null,
        error: action.payload,
      };
    case 'LOGOUT':
      return initialState;
    case 'RESTORE_AUTH':
      // Logic to restore auth state from localStorage/sessionStorage
      return state;
    default:
      return state;
  }
};

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [state, dispatch] = useReducer(authReducer, initialState);

  useEffect(() => {
    // Check if user is already logged in (from localStorage/sessionStorage)
    const token = localStorage.getItem('token');
    const userData = localStorage.getItem('user');
    
    if (token && userData) {
      try {
        const user = JSON.parse(userData);
        dispatch({ 
          type: 'LOGIN_SUCCESS', 
          payload: { user, token } 
        });
      } catch (error) {
        localStorage.removeItem('token');
        localStorage.removeItem('user');
      }
    }
  }, []);

  const login = async (username: string, password: string) => {
    dispatch({ type: 'LOGIN_REQUEST' });
    try {
      const { user, token } = await authApi.login(username, password);
      
      // Save to localStorage
      localStorage.setItem('token', token);
      localStorage.setItem('user', JSON.stringify(user));
      
      dispatch({ type: 'LOGIN_SUCCESS', payload: { user, token } });
    } catch (error) {
      dispatch({ 
        type: 'LOGIN_FAILURE', 
        payload: error instanceof Error ? error.message : 'Login failed' 
      });
    }
  };

  const logout = () => {
    localStorage.removeItem('token');
    localStorage.removeItem('user');
    dispatch({ type: 'LOGOUT' });
  };

  const hasPermission = (permission: string) => {
    return state.user?.permissions?.includes(permission) || false;
  };

  return (
    <AuthContext.Provider value={{ state, login, logout, hasPermission }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (context === undefined) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
};
```

### Zustand Store Setup (Global State)
Thiết lập Zustand store cho global state:

```typescript
// src/store/index.ts
import { create } from 'zustand';
import { devtools, persist } from 'zustand/middleware';
import { createAuthSlice, AuthState, AuthActions } from './authSlice';
import { createUiSlice, UiState, UiActions } from './uiSlice';
import { createUserSlice, UserState, UserActions } from './userSlice';

// Define the store type
export type StoreState = AuthState & UiState & UserState;
export type StoreActions = AuthActions & UiActions & UserActions;

// Create the combined store
export const useStore = create<StoreState & StoreActions>()(
  devtools(
    persist(
      (...a) => ({
        ...createAuthSlice(...a),
        ...createUiSlice(...a),
        ...createUserSlice(...a),
      }),
      {
        name: 'sdims-store',
        partialize: (state) => ({
          // Only persist these state slices
          auth: {
            token: state.token,
          },
          ui: {
            theme: state.theme,
          },
        }),
      }
    )
  )
);

// Example of an auth slice
// src/store/authSlice.ts
export interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  isLoading: boolean;
}

export interface AuthActions {
  login: (username: string, password: string) => Promise<void>;
  logout: () => void;
  checkAuth: () => Promise<boolean>;
}

export const createAuthSlice = (set, get) => ({
  // Initial state
  user: null,
  token: null,
  isAuthenticated: false,
  isLoading: false,
  
  // Actions
  login: async (username, password) => {
    set({ isLoading: true });
    try {
      const { user, token } = await authApi.login(username, password);
      set({
        user,
        token,
        isAuthenticated: true,
        isLoading: false,
      });
      return true;
    } catch (error) {
      set({ isLoading: false });
      throw error;
    }
  },
  
  logout: () => {
    set({
      user: null,
      token: null,
      isAuthenticated: false,
    });
  },
  
  checkAuth: async () => {
    const { token } = get();
    if (!token) return false;
    
    try {
      const user = await authApi.getCurrentUser();
      set({
        user,
        isAuthenticated: true,
      });
      return true;
    } catch (error) {
      set({
        user: null,
        token: null,
        isAuthenticated: false,
      });
      return false;
    }
  },
});
```

### React Query Setup (Server State)
Cấu hình React Query cho quản lý server state:

```tsx
// src/services/queryClient.ts
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

// Create a client
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      refetchOnWindowFocus: false,
      retry: 1,
      staleTime: 5 * 60 * 1000, // 5 minutes
    },
  },
});

// Provider Component
export const QueryProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
      {process.env.NODE_ENV === 'development' && <ReactQueryDevtools initialIsOpen={false} />}
    </QueryClientProvider>
  );
};
```

### Custom Hooks
Xây dựng các custom hooks hữu ích:

```typescript
// src/hooks/useEmployees.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { employeeApi } from '../services/employeeApi';
import { Employee, EmployeeFilters } from '../types/employee';

export const useEmployees = (filters: EmployeeFilters) => {
  return useQuery({
    queryKey: ['employees', filters],
    queryFn: () => employeeApi.getEmployees(filters),
  });
};

export const useEmployee = (id: string) => {
  return useQuery({
    queryKey: ['employee', id],
    queryFn: () => employeeApi.getEmployee(id),
    enabled: !!id,
  });
};

export const useCreateEmployee = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (employee: Omit<Employee, 'id'>) => 
      employeeApi.createEmployee(employee),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['employees'] });
    },
  });
};

export const useUpdateEmployee = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (data: { id: string; employee: Partial<Employee> }) => 
      employeeApi.updateEmployee(data.id, data.employee),
    onSuccess: (_, variables) => {
      queryClient.invalidateQueries({ queryKey: ['employees'] });
      queryClient.invalidateQueries({ queryKey: ['employee', variables.id] });
    },
  });
};
```

### App Provider Wrapper
Tạo wrapper component để kết hợp tất cả providers:

```tsx
// src/providers/AppProviders.tsx
import React from 'react';
import { QueryProvider } from '../services/queryClient';
import { AuthProvider } from '../context/AuthContext';
import { ThemeProvider } from '../context/ThemeContext';
import { NotificationProvider } from '../context/NotificationContext';

export const AppProviders: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  return (
    <QueryProvider>
      <AuthProvider>
        <ThemeProvider>
          <NotificationProvider>
            {children}
          </NotificationProvider>
        </ThemeProvider>
      </AuthProvider>
    </QueryProvider>
  );
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-003: Router Setup (related)
- FE-INFRA-006: API Client (related)
- FE-AUTH-001: Login Page (dependent)
- FE-CORE-006: Notification Components (dependent)

## Tiêu chí chấp nhận
- Hệ thống state management được cài đặt và cấu hình đúng
- Context Providers hoạt động và có thể truy cập từ mọi nơi trong ứng dụng
- Zustand store được thiết lập với cấu trúc rõ ràng theo từng domain
- React Query được cấu hình đúng cho server state management
- Authentication flow hoạt động đúng (login, logout, restore session)
- Custom hooks dễ sử dụng và có typing đầy đủ
- DevTools được cấu hình để hỗ trợ phát triển
- State persisted đúng khi cần thiết (như auth token)
- Performance tốt, không có unnecessary re-renders
- Có documentation về cách sử dụng state management trong dự án 