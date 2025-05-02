# Task Detail: FE-CORE-006

## Thông tin chung
- **ID**: FE-CORE-006
- **Tên task**: Xây dựng Notification components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 2 days
- **Tham chiếu**: Toàn bộ ứng dụng có sử dụng notifications

## Mô tả
Xây dựng hệ thống notification components toàn diện cho ứng dụng SDIMS, bao gồm toast notifications, alert components, và notification center UI. Các components này sẽ được sử dụng xuyên suốt ứng dụng để hiển thị thông báo, cảnh báo, lỗi, và các phản hồi khác từ hệ thống. Notification system sẽ giúp tăng cường trải nghiệm người dùng bằng cách cung cấp các phản hồi kịp thời và rõ ràng về các hành động của họ và trạng thái của hệ thống.

## Yêu cầu kỹ thuật
### Toast Notifications
- Hiển thị thông báo tạm thời trên màn hình
- Variants khác nhau: success, error, warning, info
- Auto-dismiss sau khoảng thời gian có thể tùy chỉnh
- Animation khi hiện/ẩn
- Stacking nhiều toasts
- Vị trí linh hoạt (top-right, top-center, etc.)
- Manual dismiss với close button

### Alert Components
- Static alert boxes trong UI
- Variants: success, error, warning, info
- Hỗ trợ icons và actions
- Dismissible options
- Ability to show/hide ở runtime

### Notification Center
- UI hiển thị tất cả notifications
- Group notifications by type
- Show notification status (read/unread)
- Pagination/infinite scroll cho nhiều notifications
- Mark as read functionality
- Clear all functionality

### Notification Management
- Global notification context/provider
- API để show/hide notifications từ bất kỳ component nào
- Centralized notification queue management
- Persistence cho important notifications

## Chi tiết các thành phần

### Notification Context
```tsx
// src/context/NotificationContext.tsx
import React, { createContext, useContext, useReducer, useCallback } from 'react';
import { v4 as uuidv4 } from 'uuid';

export type NotificationType = 'success' | 'error' | 'warning' | 'info';

export interface Notification {
  id: string;
  type: NotificationType;
  title: string;
  message: string;
  duration?: number; // in milliseconds, undefined = no auto-dismiss
  isPersistent?: boolean;
  createdAt: Date;
  onClose?: () => void;
}

interface NotificationState {
  toasts: Notification[];
  notifications: Notification[];
}

type NotificationAction =
  | { type: 'ADD_TOAST'; payload: Notification }
  | { type: 'REMOVE_TOAST'; payload: { id: string } }
  | { type: 'ADD_NOTIFICATION'; payload: Notification }
  | { type: 'REMOVE_NOTIFICATION'; payload: { id: string } }
  | { type: 'CLEAR_NOTIFICATIONS' };

interface NotificationContextType {
  toasts: Notification[];
  notifications: Notification[];
  showToast: (
    type: NotificationType,
    title: string,
    message: string,
    options?: { duration?: number; onClose?: () => void }
  ) => string;
  dismissToast: (id: string) => void;
  addNotification: (
    type: NotificationType,
    title: string,
    message: string,
    options?: { isPersistent?: boolean; onClose?: () => void }
  ) => string;
  removeNotification: (id: string) => void;
  clearNotifications: () => void;
}

const initialState: NotificationState = {
  toasts: [],
  notifications: [],
};

const notificationReducer = (state: NotificationState, action: NotificationAction): NotificationState => {
  switch (action.type) {
    case 'ADD_TOAST':
      return {
        ...state,
        toasts: [...state.toasts, action.payload],
      };
    case 'REMOVE_TOAST':
      return {
        ...state,
        toasts: state.toasts.filter(toast => toast.id !== action.payload.id),
      };
    case 'ADD_NOTIFICATION':
      return {
        ...state,
        notifications: [...state.notifications, action.payload],
      };
    case 'REMOVE_NOTIFICATION':
      return {
        ...state,
        notifications: state.notifications.filter(notification => notification.id !== action.payload.id),
      };
    case 'CLEAR_NOTIFICATIONS':
      return {
        ...state,
        notifications: state.notifications.filter(notification => notification.isPersistent),
      };
    default:
      return state;
  }
};

const NotificationContext = createContext<NotificationContextType | undefined>(undefined);

export const NotificationProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [state, dispatch] = useReducer(notificationReducer, initialState);

  const showToast = useCallback(
    (
      type: NotificationType,
      title: string,
      message: string,
      options?: { duration?: number; onClose?: () => void }
    ) => {
      const id = uuidv4();
      const notification: Notification = {
        id,
        type,
        title,
        message,
        duration: options?.duration ?? 5000,
        createdAt: new Date(),
        onClose: options?.onClose,
      };

      dispatch({ type: 'ADD_TOAST', payload: notification });

      // Auto-dismiss if duration is provided
      if (notification.duration) {
        setTimeout(() => {
          dispatch({ type: 'REMOVE_TOAST', payload: { id } });
          options?.onClose?.();
        }, notification.duration);
      }

      return id;
    },
    []
  );

  const dismissToast = useCallback((id: string) => {
    const toast = state.toasts.find(t => t.id === id);
    dispatch({ type: 'REMOVE_TOAST', payload: { id } });
    toast?.onClose?.();
  }, [state.toasts]);

  const addNotification = useCallback(
    (
      type: NotificationType,
      title: string,
      message: string,
      options?: { isPersistent?: boolean; onClose?: () => void }
    ) => {
      const id = uuidv4();
      const notification: Notification = {
        id,
        type,
        title,
        message,
        isPersistent: options?.isPersistent,
        createdAt: new Date(),
        onClose: options?.onClose,
      };

      dispatch({ type: 'ADD_NOTIFICATION', payload: notification });
      return id;
    },
    []
  );

  const removeNotification = useCallback((id: string) => {
    const notification = state.notifications.find(n => n.id === id);
    dispatch({ type: 'REMOVE_NOTIFICATION', payload: { id } });
    notification?.onClose?.();
  }, [state.notifications]);

  const clearNotifications = useCallback(() => {
    dispatch({ type: 'CLEAR_NOTIFICATIONS' });
  }, []);

  return (
    <NotificationContext.Provider
      value={{
        toasts: state.toasts,
        notifications: state.notifications,
        showToast,
        dismissToast,
        addNotification,
        removeNotification,
        clearNotifications,
      }}
    >
      {children}
    </NotificationContext.Provider>
  );
};

export const useNotifications = () => {
  const context = useContext(NotificationContext);
  if (!context) {
    throw new Error('useNotifications must be used within a NotificationProvider');
  }
  return context;
};
```

### Toast Component
```tsx
// src/components/notifications/Toast.tsx
import React, { useEffect, useState } from 'react';
import { Transition } from '@headlessui/react';
import {
  CheckCircleIcon,
  XCircleIcon,
  ExclamationTriangleIcon,
  InformationCircleIcon,
  XMarkIcon,
} from '@heroicons/react/24/outline';
import { cn } from '../../utils/cn';
import { Notification, NotificationType } from '../../context/NotificationContext';

interface ToastProps {
  notification: Notification;
  onClose: (id: string) => void;
}

export const Toast: React.FC<ToastProps> = ({ notification, onClose }) => {
  const [show, setShow] = useState(true);
  
  useEffect(() => {
    setShow(true);
  }, [notification.id]);

  const handleClose = () => {
    setShow(false);
    setTimeout(() => {
      onClose(notification.id);
    }, 300); // match transition duration
  };

  const getIcon = (type: NotificationType) => {
    switch (type) {
      case 'success':
        return <CheckCircleIcon className="h-6 w-6 text-green-500" />;
      case 'error':
        return <XCircleIcon className="h-6 w-6 text-red-500" />;
      case 'warning':
        return <ExclamationTriangleIcon className="h-6 w-6 text-yellow-500" />;
      case 'info':
        return <InformationCircleIcon className="h-6 w-6 text-blue-500" />;
    }
  };

  const getBgColor = (type: NotificationType) => {
    switch (type) {
      case 'success':
        return 'bg-green-50 border-green-100';
      case 'error':
        return 'bg-red-50 border-red-100';
      case 'warning':
        return 'bg-yellow-50 border-yellow-100';
      case 'info':
        return 'bg-blue-50 border-blue-100';
    }
  };

  return (
    <Transition
      show={show}
      enter="transition-all ease-out duration-300"
      enterFrom="opacity-0 scale-95"
      enterTo="opacity-100 scale-100"
      leave="transition-all ease-in duration-200"
      leaveFrom="opacity-100 scale-100"
      leaveTo="opacity-0 scale-95"
    >
      <div
        className={cn(
          "max-w-md w-full shadow-lg rounded-lg pointer-events-auto border overflow-hidden",
          getBgColor(notification.type)
        )}
      >
        <div className="p-4">
          <div className="flex items-start">
            <div className="flex-shrink-0">{getIcon(notification.type)}</div>
            <div className="ml-3 w-0 flex-1">
              <p className="text-sm font-medium text-gray-900">{notification.title}</p>
              <p className="mt-1 text-sm text-gray-500">{notification.message}</p>
            </div>
            <div className="ml-4 flex-shrink-0 flex">
              <button
                className="bg-transparent rounded-md inline-flex text-gray-400 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-primary-500"
                onClick={handleClose}
              >
                <span className="sr-only">Close</span>
                <XMarkIcon className="h-5 w-5" aria-hidden="true" />
              </button>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  );
};
```

### ToastContainer Component
```tsx
// src/components/notifications/ToastContainer.tsx
import React from 'react';
import { Toast } from './Toast';
import { useNotifications } from '../../context/NotificationContext';
import { cn } from '../../utils/cn';

export type ToastPosition =
  | 'top-right'
  | 'top-center'
  | 'top-left'
  | 'bottom-right'
  | 'bottom-center'
  | 'bottom-left';

interface ToastContainerProps {
  position?: ToastPosition;
  className?: string;
}

export const ToastContainer: React.FC<ToastContainerProps> = ({
  position = 'top-right',
  className,
}) => {
  const { toasts, dismissToast } = useNotifications();

  const getPositionClasses = (pos: ToastPosition) => {
    switch (pos) {
      case 'top-right':
        return 'top-0 right-0';
      case 'top-center':
        return 'top-0 left-1/2 transform -translate-x-1/2';
      case 'top-left':
        return 'top-0 left-0';
      case 'bottom-right':
        return 'bottom-0 right-0';
      case 'bottom-center':
        return 'bottom-0 left-1/2 transform -translate-x-1/2';
      case 'bottom-left':
        return 'bottom-0 left-0';
    }
  };

  const isTop = position.startsWith('top');

  if (toasts.length === 0) return null;

  return (
    <div
      className={cn(
        "fixed z-50 p-4 space-y-4 w-full sm:max-w-md sm:w-auto pointer-events-none",
        getPositionClasses(position),
        className
      )}
    >
      {toasts
        .slice()
        // Top positions show newest toasts at the bottom, bottom positions show newest at the top
        .sort((a, b) => {
          if (isTop) {
            return a.createdAt.getTime() - b.createdAt.getTime();
          }
          return b.createdAt.getTime() - a.createdAt.getTime();
        })
        .map((toast) => (
          <div key={toast.id} className="pointer-events-auto">
            <Toast notification={toast} onClose={dismissToast} />
          </div>
        ))}
    </div>
  );
};
```

### Alert Component
```tsx
// src/components/notifications/Alert.tsx
import React, { useState } from 'react';
import {
  CheckCircleIcon,
  XCircleIcon,
  ExclamationTriangleIcon,
  InformationCircleIcon,
  XMarkIcon,
} from '@heroicons/react/24/outline';
import { cn } from '../../utils/cn';
import { NotificationType } from '../../context/NotificationContext';

interface AlertProps {
  type: NotificationType;
  title?: string;
  message: string;
  icon?: boolean;
  dismissible?: boolean;
  onClose?: () => void;
  action?: React.ReactNode;
  className?: string;
}

export const Alert: React.FC<AlertProps> = ({
  type,
  title,
  message,
  icon = true,
  dismissible = false,
  onClose,
  action,
  className,
}) => {
  const [visible, setVisible] = useState(true);

  if (!visible) return null;

  const getIcon = (type: NotificationType) => {
    switch (type) {
      case 'success':
        return <CheckCircleIcon className="h-5 w-5 text-green-500" />;
      case 'error':
        return <XCircleIcon className="h-5 w-5 text-red-500" />;
      case 'warning':
        return <ExclamationTriangleIcon className="h-5 w-5 text-yellow-500" />;
      case 'info':
        return <InformationCircleIcon className="h-5 w-5 text-blue-500" />;
    }
  };

  const getAlertStyles = (type: NotificationType) => {
    switch (type) {
      case 'success':
        return 'bg-green-50 border-green-200 text-green-800';
      case 'error':
        return 'bg-red-50 border-red-200 text-red-800';
      case 'warning':
        return 'bg-yellow-50 border-yellow-200 text-yellow-800';
      case 'info':
        return 'bg-blue-50 border-blue-200 text-blue-800';
    }
  };

  const handleClose = () => {
    setVisible(false);
    onClose?.();
  };

  return (
    <div
      className={cn(
        "rounded-md border p-4",
        getAlertStyles(type),
        className
      )}
    >
      <div className="flex">
        {icon && (
          <div className="flex-shrink-0">
            {getIcon(type)}
          </div>
        )}
        <div className={cn("flex-1", icon && "ml-3")}>
          {title && (
            <h3 className="text-sm font-medium">{title}</h3>
          )}
          <div className={cn("text-sm", title && "mt-1")}>
            {message}
          </div>
          {action && (
            <div className="mt-4">
              {action}
            </div>
          )}
        </div>
        {dismissible && (
          <div className="ml-auto pl-3">
            <div className="-mx-1.5 -my-1.5">
              <button
                type="button"
                className={cn(
                  "inline-flex rounded-md p-1.5 focus:outline-none focus:ring-2 focus:ring-offset-2",
                  type === 'success' && "text-green-500 hover:bg-green-100 focus:ring-green-600",
                  type === 'error' && "text-red-500 hover:bg-red-100 focus:ring-red-600",
                  type === 'warning' && "text-yellow-500 hover:bg-yellow-100 focus:ring-yellow-600",
                  type === 'info' && "text-blue-500 hover:bg-blue-100 focus:ring-blue-600"
                )}
                onClick={handleClose}
              >
                <span className="sr-only">Dismiss</span>
                <XMarkIcon className="h-5 w-5" aria-hidden="true" />
              </button>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};
```

### NotificationCenter Component
```tsx
// src/components/notifications/NotificationCenter.tsx
import React, { useState } from 'react';
import { Transition } from '@headlessui/react';
import {
  BellIcon,
  CheckCircleIcon,
  XCircleIcon,
  ExclamationTriangleIcon,
  InformationCircleIcon,
} from '@heroicons/react/24/outline';
import { cn } from '../../utils/cn';
import { useNotifications, NotificationType } from '../../context/NotificationContext';
import { formatDistanceToNow } from 'date-fns';

interface NotificationCenterProps {
  maxHeight?: string;
  className?: string;
}

export const NotificationCenter: React.FC<NotificationCenterProps> = ({
  maxHeight = '24rem',
  className,
}) => {
  const { notifications, removeNotification, clearNotifications } = useNotifications();
  const [isOpen, setIsOpen] = useState(false);

  const getIcon = (type: NotificationType) => {
    switch (type) {
      case 'success':
        return <CheckCircleIcon className="h-5 w-5 text-green-500" />;
      case 'error':
        return <XCircleIcon className="h-5 w-5 text-red-500" />;
      case 'warning':
        return <ExclamationTriangleIcon className="h-5 w-5 text-yellow-500" />;
      case 'info':
        return <InformationCircleIcon className="h-5 w-5 text-blue-500" />;
    }
  };

  const unreadCount = notifications.length;

  return (
    <div className={cn("relative inline-block text-left", className)}>
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="relative rounded-full p-1 text-gray-500 hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-primary-500"
      >
        <span className="sr-only">View notifications</span>
        <BellIcon className="h-6 w-6" aria-hidden="true" />
        {unreadCount > 0 && (
          <span className="absolute top-0 right-0 -mt-1 -mr-1 flex h-4 w-4 items-center justify-center rounded-full bg-red-500 text-xs font-bold text-white">
            {unreadCount > 9 ? '9+' : unreadCount}
          </span>
        )}
      </button>

      <Transition
        show={isOpen}
        enter="transition ease-out duration-200"
        enterFrom="opacity-0 scale-95"
        enterTo="opacity-100 scale-100"
        leave="transition ease-in duration-150"
        leaveFrom="opacity-100 scale-100"
        leaveTo="opacity-0 scale-95"
      >
        <div
          className="absolute right-0 mt-2 w-80 origin-top-right divide-y divide-gray-100 rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
          style={{ zIndex: 50 }}
        >
          <div className="flex items-center justify-between px-4 py-3">
            <h3 className="text-sm font-medium">Notifications</h3>
            {notifications.length > 0 && (
              <button
                onClick={clearNotifications}
                className="text-xs text-primary-600 hover:text-primary-800"
              >
                Clear all
              </button>
            )}
          </div>

          <div
            className="overflow-y-auto"
            style={{ maxHeight }}
          >
            {notifications.length === 0 ? (
              <div className="px-4 py-8 text-center text-sm text-gray-500">
                No notifications
              </div>
            ) : (
              <ul className="divide-y divide-gray-100">
                {notifications.map((notification) => (
                  <li
                    key={notification.id}
                    className="hover:bg-gray-50 transition-colors"
                  >
                    <div className="flex px-4 py-3">
                      <div className="flex-shrink-0 mt-0.5">
                        {getIcon(notification.type)}
                      </div>
                      <div className="ml-3 flex-1">
                        <div className="flex items-start justify-between">
                          <p className="text-sm font-medium text-gray-900">
                            {notification.title}
                          </p>
                          <p className="ml-2 text-xs text-gray-500">
                            {formatDistanceToNow(notification.createdAt, { addSuffix: true })}
                          </p>
                        </div>
                        <p className="text-sm text-gray-500">{notification.message}</p>
                        <button
                          onClick={() => removeNotification(notification.id)}
                          className="mt-1 text-xs text-primary-600 hover:text-primary-800"
                        >
                          Dismiss
                        </button>
                      </div>
                    </div>
                  </li>
                ))}
              </ul>
            )}
          </div>
        </div>
      </Transition>
    </div>
  );
};
```

### Example Usage
```tsx
// Example usage of notification components
import { useNotifications } from '../context/NotificationContext';
import { Alert } from '../components/notifications/Alert';
import { Button } from '../components/ui/Button';

function ExampleComponent() {
  const { showToast, addNotification } = useNotifications();

  const handleSuccessClick = () => {
    showToast('success', 'Success!', 'Your changes have been saved successfully.');
  };

  const handleErrorClick = () => {
    showToast('error', 'Error!', 'Something went wrong. Please try again.', { duration: 8000 });
  };

  const handleAddNotification = () => {
    addNotification('info', 'New message', 'You have received a new message from Admin.');
  };

  return (
    <div className="space-y-6 p-4">
      <h2 className="text-lg font-medium">Notification Examples</h2>

      <div className="space-y-4">
        <h3 className="text-md font-medium">Toast Notifications</h3>
        <div className="flex space-x-3">
          <Button onClick={handleSuccessClick}>Show Success Toast</Button>
          <Button onClick={handleErrorClick}>Show Error Toast</Button>
          <Button onClick={handleAddNotification}>Add to Notification Center</Button>
        </div>
      </div>

      <div className="space-y-4">
        <h3 className="text-md font-medium">Alert Components</h3>
        <div className="space-y-4">
          <Alert
            type="success"
            title="Success Alert"
            message="Your profile has been updated successfully."
            dismissible
          />
          <Alert
            type="error"
            title="Error Alert"
            message="There was a problem with your request."
            dismissible
          />
          <Alert
            type="warning"
            title="Warning Alert"
            message="Your account is about to expire."
            action={<Button size="sm">Renew Now</Button>}
            dismissible
          />
          <Alert
            type="info"
            message="Scheduled maintenance will occur this weekend."
            dismissible
          />
        </div>
      </div>
    </div>
  );
}
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-INFRA-005: State Management (related)
- FE-CORE-001: Layout Components (dependent)
- FE-CORE-002: Navigation Components (dependent)

## Tiêu chí chấp nhận
- Toast notifications hiển thị đúng khi được trigger
- Toasts tự động đóng sau khoảng thời gian định trước
- Notifications stacking đúng cách khi có nhiều notifications
- Animations mượt mà cho hiệu ứng hiện/ẩn
- Alert components hiển thị đúng với tất cả variants
- Alert dismissible hoạt động đúng
- NotificationCenter hiển thị đúng số lượng thông báo
- Clear all và dismiss individual notification hoạt động đúng
- API (hooks, context) dễ sử dụng từ bất kỳ component nào
- Notification system không gây ảnh hưởng đến hiệu năng ứng dụng
- Components responsive trên tất cả kích thước màn hình
- Bố cục và styling nhất quán với design system
- Documentation đầy đủ về cách sử dụng notification system 