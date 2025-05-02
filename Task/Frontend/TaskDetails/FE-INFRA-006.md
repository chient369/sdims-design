# Task Detail: FE-INFRA-006

## Thông tin chung
- **ID**: FE-INFRA-006
- **Tên task**: Thiết lập API client và interceptors
- **Độ ưu tiên**: Cao
- **Estimate**: 2 days
- **Tham chiếu**: 
  - N/A (Task cơ sở hạ tầng)

## Mô tả
Thiết lập và cấu hình hệ thống API client sử dụng Axios cho ứng dụng SDIMS frontend, bao gồm các interceptors để xử lý authentication, error handling và các tác vụ chung liên quan đến HTTP requests. Task này cũng bao gồm việc xây dựng các service modules có cấu trúc để tương tác với các endpoints API khác nhau, đồng thời tạo các utility functions để đơn giản hóa việc gọi API. Một hệ thống API client được thiết kế tốt sẽ giúp ứng dụng giao tiếp hiệu quả với backend, xử lý lỗi một cách nhất quán, và tối ưu hóa các tác vụ liên quan đến network.

## Yêu cầu kỹ thuật
### Cài đặt và cấu hình Axios
- Cài đặt Axios package
- Thiết lập instance Axios với các cấu hình mặc định
- Cấu hình baseURL từ biến môi trường
- Thiết lập các timeout và headers mặc định

### Xây dựng Interceptors
- Request interceptors:
  - Authentication token injection
  - Request logging (dev environment)
  - Request transformation (nếu cần)
- Response interceptors:
  - Response data transformation
  - Error handling
  - Authentication error handling (401/403)
  - Server error handling
  - Network error handling

### Xây dựng API Services
- Tạo base API service class/module
- Xây dựng domain-specific API services:
  - Auth service
  - Employee service
  - Margin service
  - Contract service
  - v.v.
- Typing cho request và response data

### Xây dựng Utilities
- Error mapping và error message extraction
- Response transformation helpers
- Query parameter builders
- File upload helpers
- Cache control utilities

## Chi tiết các thành phần

### Base Axios Client
Thiết lập Axios client cơ bản:

```typescript
// src/services/api/axios.ts
import axios, { AxiosError, AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';

// Types
export interface ApiError {
  status: number;
  message: string;
  errors?: Record<string, string[]>;
}

// Create axios instance
const apiClient: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000/api/v1',
  timeout: 15000,
  headers: {
    'Content-Type': 'application/json',
    Accept: 'application/json',
  },
});

// Request interceptor
apiClient.interceptors.request.use(
  (config) => {
    // Get token from localStorage or other state management
    const token = localStorage.getItem('token');
    
    // If token exists, add to headers
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    
    // Log requests in development
    if (import.meta.env.DEV) {
      console.log(`API Request: ${config.method?.toUpperCase()} ${config.url}`, config);
    }
    
    return config;
  },
  (error) => {
    // Handle request errors
    console.error('API Request Error:', error);
    return Promise.reject(error);
  }
);

// Response interceptor
apiClient.interceptors.response.use(
  (response: AxiosResponse) => {
    // Transform response data if needed
    return response.data;
  },
  (error: AxiosError) => {
    // Handle errors
    if (error.response) {
      // The request was made and the server responded with an error status
      const status = error.response.status;
      
      // Handle authentication errors
      if (status === 401) {
        // Clear auth data
        localStorage.removeItem('token');
        localStorage.removeItem('user');
        
        // Redirect to login (if not already there)
        if (window.location.pathname !== '/login') {
          window.location.href = '/login';
        }
      }
      
      // Format error for consistent handling
      const apiError: ApiError = {
        status,
        message: extractErrorMessage(error.response.data) || 'An error occurred',
        errors: extractValidationErrors(error.response.data),
      };
      
      return Promise.reject(apiError);
    }
    
    if (error.request) {
      // The request was made but no response was received
      const apiError: ApiError = {
        status: 0,
        message: 'No response from server. Please check your internet connection.',
      };
      return Promise.reject(apiError);
    }
    
    // Something happened in setting up the request
    const apiError: ApiError = {
      status: 0,
      message: error.message || 'An unexpected error occurred',
    };
    return Promise.reject(apiError);
  }
);

// Utility functions for error handling
function extractErrorMessage(data: any): string {
  if (typeof data === 'string') return data;
  if (data?.message) return data.message;
  if (data?.error) return data.error;
  return 'An unknown error occurred';
}

function extractValidationErrors(data: any): Record<string, string[]> | undefined {
  if (data?.errors && typeof data.errors === 'object') {
    return data.errors;
  }
  return undefined;
}

export default apiClient;
```

### Base API Service
Xây dựng một base API service để các service cụ thể kế thừa:

```typescript
// src/services/api/baseApi.ts
import apiClient from './axios';
import { AxiosRequestConfig } from 'axios';

export class BaseApiService {
  protected endpoint: string;
  
  constructor(endpoint: string) {
    this.endpoint = endpoint;
  }
  
  // GET all resources
  async getAll<T>(config?: AxiosRequestConfig): Promise<T> {
    return apiClient.get<T, T>(this.endpoint, config);
  }
  
  // GET a single resource
  async getById<T>(id: string | number, config?: AxiosRequestConfig): Promise<T> {
    return apiClient.get<T, T>(`${this.endpoint}/${id}`, config);
  }
  
  // POST a new resource
  async create<T, D>(data: D, config?: AxiosRequestConfig): Promise<T> {
    return apiClient.post<T, T>(this.endpoint, data, config);
  }
  
  // PUT/PATCH to update a resource
  async update<T, D>(id: string | number, data: D, config?: AxiosRequestConfig): Promise<T> {
    return apiClient.put<T, T>(`${this.endpoint}/${id}`, data, config);
  }
  
  // DELETE a resource
  async delete<T>(id: string | number, config?: AxiosRequestConfig): Promise<T> {
    return apiClient.delete<T, T>(`${this.endpoint}/${id}`, config);
  }
  
  // Custom request method for more complex scenarios
  async request<T>(config: AxiosRequestConfig): Promise<T> {
    return apiClient.request<T, T>(config);
  }
}
```

### Domain-specific API Services
Xây dựng các service cụ thể cho từng domain:

```typescript
// src/services/api/authApi.ts
import { BaseApiService } from './baseApi';
import { User } from '../../types/user';

interface LoginRequest {
  username: string;
  password: string;
}

interface LoginResponse {
  user: User;
  token: string;
}

class AuthApiService extends BaseApiService {
  constructor() {
    super('/auth');
  }
  
  // Login
  async login(credentials: LoginRequest): Promise<LoginResponse> {
    return this.create<LoginResponse, LoginRequest>(credentials);
  }
  
  // Get current user profile
  async getCurrentUser(): Promise<User> {
    return this.request<User>({ url: `${this.endpoint}/me`, method: 'GET' });
  }
  
  // Logout
  async logout(): Promise<void> {
    return this.request<void>({ url: `${this.endpoint}/logout`, method: 'POST' });
  }
  
  // Refresh token
  async refreshToken(): Promise<{ token: string }> {
    return this.request<{ token: string }>({ 
      url: `${this.endpoint}/refresh-token`, 
      method: 'POST' 
    });
  }
}

export const authApi = new AuthApiService();
```

```typescript
// src/services/api/employeeApi.ts
import { BaseApiService } from './baseApi';
import { Employee, EmployeeFilters, PaginatedResponse } from '../../types/employee';

class EmployeeApiService extends BaseApiService {
  constructor() {
    super('/employees');
  }
  
  // Get employees with filtering, pagination
  async getEmployees(filters: EmployeeFilters): Promise<PaginatedResponse<Employee>> {
    return this.request<PaginatedResponse<Employee>>({
      url: this.endpoint,
      method: 'GET',
      params: filters,
    });
  }
  
  // Get employee details
  async getEmployee(id: string): Promise<Employee> {
    return this.getById<Employee>(id);
  }
  
  // Create new employee
  async createEmployee(employee: Omit<Employee, 'id'>): Promise<Employee> {
    return this.create<Employee, Omit<Employee, 'id'>>(employee);
  }
  
  // Update employee
  async updateEmployee(id: string, employee: Partial<Employee>): Promise<Employee> {
    return this.update<Employee, Partial<Employee>>(id, employee);
  }
  
  // Delete employee
  async deleteEmployee(id: string): Promise<void> {
    return this.delete<void>(id);
  }
  
  // Get employee skills
  async getEmployeeSkills(employeeId: string): Promise<any[]> {
    return this.request<any[]>({
      url: `${this.endpoint}/${employeeId}/skills`,
      method: 'GET',
    });
  }
  
  // Add/update employee skill
  async updateEmployeeSkill(employeeId: string, skillData: any): Promise<any> {
    return this.request<any>({
      url: `${this.endpoint}/${employeeId}/skills`,
      method: 'POST',
      data: skillData,
    });
  }
}

export const employeeApi = new EmployeeApiService();
```

### Utilities for API Calls
```typescript
// src/services/api/utils.ts
import { AxiosRequestConfig } from 'axios';

// Build query params for filtering, sorting, pagination
export function buildQueryParams(params: Record<string, any>): Record<string, string> {
  const result: Record<string, string> = {};
  
  Object.entries(params).forEach(([key, value]) => {
    if (value !== undefined && value !== null) {
      if (Array.isArray(value)) {
        // Handle arrays
        result[key] = value.join(',');
      } else if (typeof value === 'object') {
        // Handle objects (convert to JSON)
        result[key] = JSON.stringify(value);
      } else {
        // Handle primitives
        result[key] = String(value);
      }
    }
  });
  
  return result;
}

// Helper for file uploads
export function createFileUploadConfig(file: File, additionalData?: Record<string, any>): AxiosRequestConfig {
  const formData = new FormData();
  formData.append('file', file);
  
  // Add any additional data
  if (additionalData) {
    Object.entries(additionalData).forEach(([key, value]) => {
      if (value !== undefined && value !== null) {
        formData.append(key, String(value));
      }
    });
  }
  
  return {
    headers: {
      'Content-Type': 'multipart/form-data',
    },
    data: formData,
  };
}

// API error helper
export function getErrorMessage(error: any): string {
  if (typeof error === 'string') return error;
  if (error?.message) return error.message;
  return 'An unknown error occurred';
}

// Request abort controller helper
export function createAbortController(timeoutMs = 30000): { 
  controller: AbortController; 
  timeoutId: number;
  getConfig: () => AxiosRequestConfig;
} {
  const controller = new AbortController();
  const timeoutId = window.setTimeout(() => controller.abort(), timeoutMs);
  
  return {
    controller,
    timeoutId,
    getConfig: () => ({
      signal: controller.signal,
    }),
  };
}
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-005: State Management (related)
- FE-AUTH-001: Login Page (dependent)
- FE-HRM-001: Employee List Page (dependent)
- FE-MGN-001: Margin List Page (dependent)

## Tiêu chí chấp nhận
- Axios được cài đặt và cấu hình đúng
- Request interceptors xử lý authentication và request headers một cách chính xác
- Response interceptors xử lý lỗi một cách nhất quán
- 401 errors tự động redirect người dùng về trang login
- API services được xây dựng với typing đầy đủ cho các request/response
- Có các utilities để hỗ trợ việc gọi API phức tạp
- File uploads được xử lý đúng cách
- Request/response logging trong development environment
- Tất cả API services tuân theo cấu trúc chung
- Error handling nhất quán và thân thiện với người dùng 