# Task Detail: FE-CORE-002

## Thông tin chung
- **ID**: FE-CORE-002
- **Tên task**: Xây dựng Navigation components
- **Độ ưu tiên**: Cao
- **Estimate**: 2 days
- **Tham chiếu**: Các màn hình trong folder [Design/assets/frontend/img](../../../../Design/assets/frontend/img)

## Mô tả
Xây dựng các navigation components cốt lõi cho ứng dụng SDIMS, bao gồm Sidebar menu với phân quyền, Header với user profile và notifications, cùng với Breadcrumbs component. Các components này giúp người dùng điều hướng qua các phần khác nhau của ứng dụng, cung cấp trải nghiệm nhất quán và thuận tiện. Các navigation components được thiết kế với tính năng responsive, thể hiện đúng trạng thái active, và tích hợp với hệ thống phân quyền.

## Yêu cầu kỹ thuật
### Sidebar Component
- Menu động được cấu hình từ data structure
- Hiển thị menu items dựa trên user permissions
- Grouping menu items theo categories
- Active state highlighting cho current route
- Nested menu items với collapsible sections
- Icons cho tất cả menu items
- Collapse/expand functionality
- Mobile-friendly với responsive behavior

### Header Component
- User profile section với avatar, name, role
- Dropdown menu cho user actions (profile, settings, logout)
- Notifications icon với counter và dropdown
- Tích hợp với theme switcher (nếu có dark mode)
- Search bar (nếu cần)
- Responsive design cho tất cả kích thước màn hình

### Breadcrumbs Component
- Dynamic generation dựa trên route hierarchy
- Support cho custom labels và overrides
- Clickable navigation
- Icon support cho mỗi item
- Truncation cho paths quá dài
- Accessibility support (ARIA)
- Mobile-friendly với responsive behavior

## Chi tiết các thành phần

### Menu Configuration
Thiết lập cấu trúc dữ liệu cho dynamic menu:

```typescript
// src/config/menuItems.ts
import { IconType } from 'react-icons';
import { 
  HiOutlineHome, 
  HiOutlineUsers, 
  HiOutlineCurrencyDollar,
  HiOutlineDocumentText,
  HiOutlineLightningBolt,
  HiOutlineChart,
  HiOutlineCog
} from 'react-icons/hi';

export interface MenuItem {
  id: string;
  title: string;
  path: string;
  icon: IconType;
  permission?: string; // Permission required to see this item
  children?: MenuItem[]; // For nested menu items
  badge?: {
    text: string;
    color: string;
  };
}

export const menuItems: MenuItem[] = [
  {
    id: 'dashboard',
    title: 'Dashboard',
    path: '/dashboard',
    icon: HiOutlineHome,
  },
  {
    id: 'hrm',
    title: 'Human Resources',
    path: '/hrm',
    icon: HiOutlineUsers,
    permission: 'view:hrm',
    children: [
      {
        id: 'employees',
        title: 'Employees',
        path: '/hrm/employees',
        icon: HiOutlineUsers,
        permission: 'view:employees',
      },
      {
        id: 'skills',
        title: 'Skills Management',
        path: '/hrm/skills',
        icon: HiOutlineLightningBolt,
        permission: 'view:skills',
      },
    ],
  },
  {
    id: 'margin',
    title: 'Margin',
    path: '/margin',
    icon: HiOutlineCurrencyDollar,
    permission: 'view:margin',
  },
  {
    id: 'opportunities',
    title: 'Opportunities',
    path: '/opportunities',
    icon: HiOutlineLightningBolt,
    permission: 'view:opportunities',
  },
  {
    id: 'contracts',
    title: 'Contracts',
    path: '/contracts',
    icon: HiOutlineDocumentText,
    permission: 'view:contracts',
  },
  {
    id: 'reports',
    title: 'Reports',
    path: '/reports',
    icon: HiOutlineChart,
    permission: 'view:reports',
  },
  {
    id: 'settings',
    title: 'Settings',
    path: '/settings',
    icon: HiOutlineCog,
    permission: 'view:settings',
  },
];
```

### Sidebar Component
```tsx
// src/components/navigation/Sidebar.tsx
import React, { useState } from 'react';
import { Link, useLocation } from 'react-router-dom';
import { menuItems, MenuItem } from '../../config/menuItems';
import { useAuth } from '../../hooks/useAuth';
import { cn } from '../../utils/cn';
import Logo from '../ui/Logo';

interface SidebarProps {
  collapsed: boolean;
  onToggle: () => void;
}

const Sidebar: React.FC<SidebarProps> = ({ collapsed, onToggle }) => {
  const location = useLocation();
  const { hasPermission } = useAuth();
  const [expandedGroups, setExpandedGroups] = useState<string[]>([]);
  
  const toggleGroup = (id: string) => {
    setExpandedGroups(prev => 
      prev.includes(id) 
        ? prev.filter(item => item !== id) 
        : [...prev, id]
    );
  };
  
  const isActive = (path: string) => {
    return location.pathname === path || location.pathname.startsWith(`${path}/`);
  };
  
  const renderMenuItem = (item: MenuItem) => {
    // Check permission first
    if (item.permission && !hasPermission(item.permission)) {
      return null;
    }
    
    const active = isActive(item.path);
    const hasChildren = item.children && item.children.length > 0;
    const isExpanded = expandedGroups.includes(item.id);
    
    return (
      <div key={item.id} className="mb-1">
        <div
          className={cn(
            "flex items-center px-4 py-2 rounded-md cursor-pointer transition-colors",
            active ? "bg-primary-100 text-primary-800" : "hover:bg-gray-100",
            collapsed && "justify-center"
          )}
          onClick={() => hasChildren ? toggleGroup(item.id) : null}
        >
          <Link 
            to={hasChildren ? "#" : item.path}
            className="flex items-center w-full" 
            onClick={(e) => hasChildren && e.preventDefault()}
          >
            <item.icon className={cn("w-5 h-5", active ? "text-primary-600" : "text-gray-500")} />
            
            {!collapsed && (
              <span className="ml-3 text-sm font-medium">{item.title}</span>
            )}
            
            {!collapsed && item.badge && (
              <span className={`ml-auto px-2 py-0.5 text-xs rounded-full bg-${item.badge.color}-100 text-${item.badge.color}-800`}>
                {item.badge.text}
              </span>
            )}
            
            {!collapsed && hasChildren && (
              <span className="ml-auto">
                <svg
                  className={`w-4 h-4 transition-transform ${isExpanded ? 'transform rotate-180' : ''}`}
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M19 9l-7 7-7-7" />
                </svg>
              </span>
            )}
          </Link>
        </div>
        
        {/* Children items */}
        {hasChildren && isExpanded && !collapsed && (
          <div className="pl-10 mt-1">
            {item.children!.map(child => {
              if (child.permission && !hasPermission(child.permission)) {
                return null;
              }
              
              const childActive = isActive(child.path);
              
              return (
                <Link
                  key={child.id}
                  to={child.path}
                  className={cn(
                    "flex items-center px-2 py-1.5 mb-1 text-sm rounded-md",
                    childActive ? "bg-primary-50 text-primary-700" : "hover:bg-gray-50"
                  )}
                >
                  <child.icon className={cn("w-4 h-4", childActive ? "text-primary-600" : "text-gray-500")} />
                  <span className="ml-2">{child.title}</span>
                </Link>
              );
            })}
          </div>
        )}
      </div>
    );
  };
  
  return (
    <aside 
      className={cn(
        "h-screen fixed left-0 top-0 z-20 flex flex-col transition-all duration-300 bg-white border-r",
        collapsed ? "w-16" : "w-64"
      )}
    >
      {/* Logo area */}
      <div className={cn(
        "h-16 flex items-center px-4 border-b",
        collapsed ? "justify-center" : "justify-between"
      )}>
        {collapsed ? (
          <Logo type="icon" className="w-8 h-8" />
        ) : (
          <Logo type="full" className="h-8" />
        )}
        
        {!collapsed && (
          <button 
            onClick={onToggle}
            className="p-1 rounded-md hover:bg-gray-100"
          >
            <svg
              className="w-5 h-5 text-gray-500"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M15 19l-7-7 7-7" />
            </svg>
          </button>
        )}
      </div>
      
      {/* Collapsed toggle button */}
      {collapsed && (
        <button 
          onClick={onToggle}
          className="absolute top-16 -right-3 p-1 bg-white rounded-full border shadow-sm"
        >
          <svg
            className="w-5 h-5 text-gray-500"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M9 5l7 7-7 7" />
          </svg>
        </button>
      )}
      
      {/* Menu items */}
      <div className="flex-1 px-2 py-4 overflow-y-auto">
        {menuItems.map(renderMenuItem)}
      </div>
      
      {/* Footer */}
      <div className={cn(
        "border-t px-4 py-3 text-xs text-gray-500",
        collapsed ? "text-center" : ""
      )}>
        {!collapsed && <div>SDIMS v1.0.0</div>}
      </div>
    </aside>
  );
};

export default Sidebar;
```

### Header Component
```tsx
// src/components/navigation/Header.tsx
import React, { useState } from 'react';
import { useAuth } from '../../hooks/useAuth';
import { Menu, Transition } from '@headlessui/react';
import { 
  HiOutlineBell, 
  HiOutlineSearch,
  HiOutlineLogout,
  HiOutlineUser,
  HiOutlineCog
} from 'react-icons/hi';
import { Link } from 'react-router-dom';

interface HeaderProps {
  showSearchBar?: boolean;
}

const Header: React.FC<HeaderProps> = ({ showSearchBar = true }) => {
  const { user, logout } = useAuth();
  const [notificationsOpen, setNotificationsOpen] = useState(false);
  
  // Dummy notifications for demo
  const notifications = [
    { id: 1, title: 'New employee added', time: '30 min ago', read: false },
    { id: 2, title: 'Meeting reminder', time: '1 hour ago', read: false },
    { id: 3, title: 'Task completed', time: '2 hours ago', read: true },
  ];
  
  const unreadCount = notifications.filter(n => !n.read).length;
  
  return (
    <header className="h-16 px-4 flex items-center justify-between bg-white border-b z-10">
      {/* Left side */}
      <div className="flex items-center">
        {showSearchBar && (
          <div className="relative max-w-xs hidden md:block">
            <div className="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
              <HiOutlineSearch className="w-5 h-5 text-gray-400" />
            </div>
            <input
              type="text"
              className="block w-full py-2 pl-10 pr-3 text-sm border border-gray-300 rounded-md focus:ring-primary-500 focus:border-primary-500"
              placeholder="Search..."
            />
          </div>
        )}
      </div>
      
      {/* Right side */}
      <div className="flex items-center space-x-4">
        {/* Notifications */}
        <div className="relative">
          <button
            className="relative p-2 text-gray-600 rounded-full hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-primary-500"
            onClick={() => setNotificationsOpen(!notificationsOpen)}
          >
            <HiOutlineBell className="w-5 h-5" />
            {unreadCount > 0 && (
              <span className="absolute top-1 right-1 inline-flex items-center justify-center w-4 h-4 text-xs font-bold text-white bg-red-500 rounded-full">
                {unreadCount}
              </span>
            )}
          </button>
          
          {/* Notifications dropdown */}
          <Transition
            show={notificationsOpen}
            enter="transition ease-out duration-100"
            enterFrom="transform opacity-0 scale-95"
            enterTo="transform opacity-100 scale-100"
            leave="transition ease-in duration-75"
            leaveFrom="transform opacity-100 scale-100"
            leaveTo="transform opacity-0 scale-95"
          >
            <div className="absolute right-0 mt-2 w-80 bg-white rounded-md shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none z-50">
              <div className="px-4 py-3 border-b">
                <h3 className="text-sm font-medium">Notifications</h3>
              </div>
              <div className="divide-y divide-gray-100 max-h-96 overflow-y-auto">
                {notifications.length > 0 ? (
                  notifications.map((notification) => (
                    <div 
                      key={notification.id} 
                      className={`px-4 py-3 hover:bg-gray-50 cursor-pointer ${!notification.read ? 'bg-primary-50' : ''}`}
                    >
                      <div className="flex justify-between">
                        <p className="text-sm font-medium text-gray-900">{notification.title}</p>
                        <p className="text-xs text-gray-500">{notification.time}</p>
                      </div>
                    </div>
                  ))
                ) : (
                  <div className="px-4 py-6 text-center text-gray-500">
                    No notifications
                  </div>
                )}
              </div>
              <div className="px-4 py-2 border-t">
                <Link to="/notifications" className="text-xs text-primary-600 hover:text-primary-800">
                  View all notifications
                </Link>
              </div>
            </div>
          </Transition>
        </div>
        
        {/* User menu */}
        <Menu as="div" className="relative">
          <Menu.Button className="flex items-center space-x-3 focus:outline-none">
            <div className="w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center overflow-hidden">
              {user?.avatar ? (
                <img src={user.avatar} alt={user.name} className="w-full h-full object-cover" />
              ) : (
                <span className="text-gray-700 font-medium">{user?.name?.charAt(0)}</span>
              )}
            </div>
            <div className="hidden md:block text-left">
              <div className="text-sm font-medium text-gray-900">{user?.name}</div>
              <div className="text-xs text-gray-500">{user?.role}</div>
            </div>
          </Menu.Button>
          
          <Transition
            enter="transition ease-out duration-100"
            enterFrom="transform opacity-0 scale-95"
            enterTo="transform opacity-100 scale-100"
            leave="transition ease-in duration-75"
            leaveFrom="transform opacity-100 scale-100"
            leaveTo="transform opacity-0 scale-95"
          >
            <Menu.Items className="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none py-1 z-50">
              <Menu.Item>
                {({ active }) => (
                  <Link
                    to="/profile"
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex items-center px-4 py-2 text-sm text-gray-700`}
                  >
                    <HiOutlineUser className="mr-3 h-5 w-5 text-gray-400" aria-hidden="true" />
                    Your Profile
                  </Link>
                )}
              </Menu.Item>
              <Menu.Item>
                {({ active }) => (
                  <Link
                    to="/settings"
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex items-center px-4 py-2 text-sm text-gray-700`}
                  >
                    <HiOutlineCog className="mr-3 h-5 w-5 text-gray-400" aria-hidden="true" />
                    Settings
                  </Link>
                )}
              </Menu.Item>
              <Menu.Item>
                {({ active }) => (
                  <button
                    onClick={logout}
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex items-center px-4 py-2 text-sm text-gray-700 w-full text-left`}
                  >
                    <HiOutlineLogout className="mr-3 h-5 w-5 text-gray-400" aria-hidden="true" />
                    Sign out
                  </button>
                )}
              </Menu.Item>
            </Menu.Items>
          </Transition>
        </Menu>
      </div>
    </header>
  );
};

export default Header;
```

### Breadcrumbs Component
```tsx
// src/components/navigation/Breadcrumbs.tsx
import React from 'react';
import { Link, useLocation } from 'react-router-dom';
import { HiChevronRight, HiHome } from 'react-icons/hi';

interface BreadcrumbMapping {
  [key: string]: {
    label: string;
    icon?: React.ReactNode;
  };
}

// Configuration for route to label mapping
const routeMappings: BreadcrumbMapping = {
  '': { label: 'Home', icon: <HiHome className="w-4 h-4" /> },
  'dashboard': { label: 'Dashboard' },
  'hrm': { label: 'Human Resources' },
  'employees': { label: 'Employees' },
  'employee': { label: 'Employee Details' },
  'skills': { label: 'Skills' },
  'margin': { label: 'Margin' },
  'opportunities': { label: 'Opportunities' },
  'contracts': { label: 'Contracts' },
  'reports': { label: 'Reports' },
  'settings': { label: 'Settings' },
  // Add more mappings as needed
};

interface BreadcrumbsProps {
  overrides?: {
    [key: string]: string;
  };
}

const Breadcrumbs: React.FC<BreadcrumbsProps> = ({ overrides = {} }) => {
  const location = useLocation();
  const pathnames = location.pathname.split('/').filter(x => x);
  
  // If we're at the root path, don't show breadcrumbs
  if (pathnames.length === 0) {
    return null;
  }
  
  return (
    <nav className="px-4 py-3 text-sm">
      <ol className="flex items-center flex-wrap">
        {/* Home breadcrumb */}
        <li className="flex items-center">
          <Link
            to="/"
            className="text-gray-500 hover:text-primary-600 flex items-center"
          >
            {routeMappings[''].icon}
            <span className="ml-1 hidden md:inline">Home</span>
          </Link>
        </li>
        
        {/* Path-based breadcrumbs */}
        {pathnames.map((name, index) => {
          // Try to get a numeric ID (for detail pages)
          const isId = /^\d+$/.test(name);
          
          // Get the path until this point
          const routeTo = `/${pathnames.slice(0, index + 1).join('/')}`;
          
          // Check for override
          const displayName = overrides[routeTo] || (
            isId ? `ID: ${name}` : (
              routeMappings[name]?.label || name.charAt(0).toUpperCase() + name.slice(1)
            )
          );
          
          // Icon for this breadcrumb
          const icon = routeMappings[name]?.icon;
          
          const isLast = index === pathnames.length - 1;
          
          return (
            <li key={routeTo} className="flex items-center">
              <HiChevronRight className="mx-2 text-gray-400" />
              {isLast ? (
                <span className="font-medium text-gray-800 flex items-center">
                  {icon && <span className="mr-1">{icon}</span>}
                  {displayName}
                </span>
              ) : (
                <Link
                  to={routeTo}
                  className="text-gray-500 hover:text-primary-600 flex items-center"
                >
                  {icon && <span className="mr-1">{icon}</span>}
                  {displayName}
                </Link>
              )}
            </li>
          );
        })}
      </ol>
    </nav>
  );
};

export default Breadcrumbs;
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-003: Router Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-CORE-001: Layout Components (related)
- FE-AUTH-001: Login Page (dependent)

## Tiêu chí chấp nhận
- Sidebar hiển thị menu items chính xác theo cấu hình
- Menu items được filter dựa trên user permissions
- Active state được highlight đúng trên current route
- Nested menu items hoạt động với expand/collapse
- Header hiển thị user info và notifications đúng
- Notifications dropdown hiển thị danh sách thông báo
- User dropdown menu hoạt động và có các actions cần thiết
- Breadcrumbs tự động generate dựa trên current route
- Tất cả components đều responsive trên mobile, tablet, desktop
- Components phù hợp với design system và theme
- Accessible navigation với keyboard support
- Hiệu ứng và transitions mượt mà 