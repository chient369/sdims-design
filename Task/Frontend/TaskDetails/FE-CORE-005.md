# Task Detail: FE-CORE-005

## Thông tin chung
- **ID**: FE-CORE-005
- **Tên task**: Xây dựng Modal và Dialog components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 2 days
- **Tham chiếu**: Các màn hình có chức năng popup/modal/drawer trong toàn bộ hệ thống

## Mô tả
Xây dựng bộ Modal và Dialog components linh hoạt, tái sử dụng cao cho ứng dụng SDIMS, bao gồm các loại modal khác nhau từ các confirmation dialogs đơn giản đến modals phức tạp chứa forms và wizards. Task này cũng bao gồm xây dựng các Drawer components cho side panels. Các components này sẽ được sử dụng xuyên suốt ứng dụng cho việc xác nhận hành động, hiển thị thông tin chi tiết, chỉnh sửa dữ liệu mà không cần chuyển trang, và nhiều trường hợp khác.

## Yêu cầu kỹ thuật
### Core Modal Features
- Overlay hiển thị đúng và phù hợp với theming
- Focus trap trong modal khi đang mở
- ESC key để đóng modal
- Click outside để đóng (có thể tùy chỉnh)
- Transitions và animations mượt mà
- Stacking nhiều modals (nếu cần)
- Responsive trên các kích thước màn hình
- A11y compliance (keyboard navigation, ARIA roles)

### Modal Variants
- **Standard Modal**: Modal cơ bản với header, body, footer
- **Confirmation Dialog**: Modal đơn giản để xác nhận hành động
- **Form Modal**: Modal chứa form elements
- **Alert Dialog**: Modal hiển thị thông báo quan trọng
- **Full-screen Modal**: Modal chiếm toàn bộ màn hình
- **Drawer**: Side panel sliding từ các cạnh (left, right, bottom)

### Modal Utilities
- Modal Provider/Context cho quản lý central
- Imperative API (useModal hook)
- Dynamic content loading
- Blocking/non-blocking behaviors
- Cancel/confirm handling
- Size variants (sm, md, lg, xl, full)

## Chi tiết các thành phần

### Modal Context
```tsx
// src/components/modal/ModalContext.tsx
import React, { createContext, useContext, useState, useCallback } from 'react';

interface ModalContextType {
  openModal: (modalId: string) => void;
  closeModal: (modalId: string) => void;
  isOpen: (modalId: string) => boolean;
}

const ModalContext = createContext<ModalContextType | undefined>(undefined);

export const ModalProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [openModals, setOpenModals] = useState<Set<string>>(new Set());

  const openModal = useCallback((modalId: string) => {
    setOpenModals(prev => {
      const newSet = new Set(prev);
      newSet.add(modalId);
      return newSet;
    });
  }, []);

  const closeModal = useCallback((modalId: string) => {
    setOpenModals(prev => {
      const newSet = new Set(prev);
      newSet.delete(modalId);
      return newSet;
    });
  }, []);

  const isOpen = useCallback(
    (modalId: string) => openModals.has(modalId),
    [openModals]
  );

  return (
    <ModalContext.Provider value={{ openModal, closeModal, isOpen }}>
      {children}
    </ModalContext.Provider>
  );
};

export const useModalContext = () => {
  const context = useContext(ModalContext);
  if (!context) {
    throw new Error('useModalContext must be used within a ModalProvider');
  }
  return context;
};

export function useModal(modalId: string) {
  const { openModal, closeModal, isOpen } = useModalContext();

  return {
    open: () => openModal(modalId),
    close: () => closeModal(modalId),
    isOpen: isOpen(modalId),
  };
}
```

### Base Modal Component
```tsx
// src/components/modal/Modal.tsx
import React, { useEffect, useRef } from 'react';
import { createPortal } from 'react-dom';
import { Transition } from '@headlessui/react';
import { XMarkIcon } from '@heroicons/react/24/outline';
import { cn } from '../../utils/cn';
import { useClickOutside } from '../../hooks/useClickOutside';
import { useEscapeKey } from '../../hooks/useEscapeKey';
import FocusTrap from 'focus-trap-react';

export type ModalSize = 'sm' | 'md' | 'lg' | 'xl' | 'full';

export interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
  size?: ModalSize;
  showCloseButton?: boolean;
  closeOnClickOutside?: boolean;
  closeOnEsc?: boolean;
  preventScroll?: boolean;
  className?: string;
  overlayClassName?: string;
}

export const Modal: React.FC<ModalProps> = ({
  isOpen,
  onClose,
  children,
  size = 'md',
  showCloseButton = true,
  closeOnClickOutside = true,
  closeOnEsc = true,
  preventScroll = true,
  className,
  overlayClassName,
}) => {
  const modalRef = useRef<HTMLDivElement>(null);

  // Handle click outside
  useClickOutside(modalRef, () => {
    if (closeOnClickOutside) {
      onClose();
    }
  });

  // Handle ESC key
  useEscapeKey(() => {
    if (closeOnEsc) {
      onClose();
    }
  }, isOpen);

  // Prevent scroll on body when modal is open
  useEffect(() => {
    if (preventScroll) {
      if (isOpen) {
        document.body.style.overflow = 'hidden';
      } else {
        document.body.style.overflow = '';
      }
    }

    return () => {
      document.body.style.overflow = '';
    };
  }, [isOpen, preventScroll]);

  // Calculate max width based on size
  const sizeClasses = {
    sm: 'max-w-sm',
    md: 'max-w-md',
    lg: 'max-w-lg',
    xl: 'max-w-xl',
    full: 'max-w-full h-full m-0 rounded-none',
  };

  // Only render if open
  if (!isOpen) return null;

  // Use createPortal to render outside of parent hierarchy
  return createPortal(
    <Transition
      show={isOpen}
      enter="transition-opacity ease-out duration-200"
      enterFrom="opacity-0"
      enterTo="opacity-100"
      leave="transition-opacity ease-in duration-150"
      leaveFrom="opacity-100"
      leaveTo="opacity-0"
      className="fixed inset-0 z-50 overflow-y-auto"
    >
      <div className="flex min-h-screen items-center justify-center px-4 py-8 text-center">
        {/* Overlay */}
        <div
          className={cn(
            "fixed inset-0 bg-black bg-opacity-50 transition-opacity",
            overlayClassName
          )}
          aria-hidden="true"
        />

        {/* Modal */}
        <FocusTrap>
          <Transition.Child
            enter="transition ease-out duration-300"
            enterFrom="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            enterTo="opacity-100 translate-y-0 sm:scale-100"
            leave="transition ease-in duration-200"
            leaveFrom="opacity-100 translate-y-0 sm:scale-100"
            leaveTo="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            className={cn(
              "relative transform overflow-hidden rounded-lg bg-white text-left shadow-xl transition-all w-full",
              sizeClasses[size],
              className
            )}
            ref={modalRef}
          >
            {showCloseButton && (
              <button
                type="button"
                className="absolute right-4 top-4 z-10 rounded-md p-1 text-gray-400 hover:bg-gray-100 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-primary-500"
                onClick={onClose}
              >
                <span className="sr-only">Close</span>
                <XMarkIcon className="h-5 w-5" aria-hidden="true" />
              </button>
            )}
            {children}
          </Transition.Child>
        </FocusTrap>
      </div>
    </Transition>,
    document.body
  );
};
```

### Modal Components
```tsx
// src/components/modal/ModalHeader.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface ModalHeaderProps {
  children: React.ReactNode;
  className?: string;
}

export const ModalHeader: React.FC<ModalHeaderProps> = ({ children, className }) => (
  <div className={cn("px-6 py-4 border-b", className)}>
    <h3 className="text-lg font-medium text-gray-900">{children}</h3>
  </div>
);

// src/components/modal/ModalBody.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface ModalBodyProps {
  children: React.ReactNode;
  className?: string;
}

export const ModalBody: React.FC<ModalBodyProps> = ({ children, className }) => (
  <div className={cn("p-6", className)}>
    {children}
  </div>
);

// src/components/modal/ModalFooter.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface ModalFooterProps {
  children: React.ReactNode;
  className?: string;
}

export const ModalFooter: React.FC<ModalFooterProps> = ({ children, className }) => (
  <div className={cn("px-6 py-4 border-t flex items-center justify-end space-x-3", className)}>
    {children}
  </div>
);
```

### Confirmation Dialog
```tsx
// src/components/modal/ConfirmationDialog.tsx
import React from 'react';
import { Modal, ModalProps } from './Modal';
import { ModalHeader } from './ModalHeader';
import { ModalBody } from './ModalBody';
import { ModalFooter } from './ModalFooter';
import { Button } from '../ui/Button';

interface ConfirmationDialogProps extends Omit<ModalProps, 'children'> {
  title: string;
  message: React.ReactNode;
  confirmLabel?: string;
  cancelLabel?: string;
  confirmVariant?: 'primary' | 'danger';
  onConfirm: () => void;
}

export const ConfirmationDialog: React.FC<ConfirmationDialogProps> = ({
  title,
  message,
  confirmLabel = 'Confirm',
  cancelLabel = 'Cancel',
  confirmVariant = 'primary',
  onConfirm,
  onClose,
  ...modalProps
}) => {
  return (
    <Modal {...modalProps} onClose={onClose} size="sm">
      <ModalHeader>{title}</ModalHeader>
      <ModalBody>{message}</ModalBody>
      <ModalFooter>
        <Button
          variant="outline"
          onClick={onClose}
        >
          {cancelLabel}
        </Button>
        <Button
          variant={confirmVariant}
          onClick={() => {
            onConfirm();
            onClose();
          }}
        >
          {confirmLabel}
        </Button>
      </ModalFooter>
    </Modal>
  );
};
```

### Drawer Component
```tsx
// src/components/modal/Drawer.tsx
import React, { useEffect, useRef } from 'react';
import { createPortal } from 'react-dom';
import { Transition } from '@headlessui/react';
import { XMarkIcon } from '@heroicons/react/24/outline';
import { cn } from '../../utils/cn';
import { useEscapeKey } from '../../hooks/useEscapeKey';
import FocusTrap from 'focus-trap-react';

export type DrawerPlacement = 'left' | 'right' | 'top' | 'bottom';
export type DrawerSize = 'xs' | 'sm' | 'md' | 'lg' | 'xl' | 'full';

interface DrawerProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
  placement?: DrawerPlacement;
  size?: DrawerSize;
  showCloseButton?: boolean;
  preventScroll?: boolean;
  className?: string;
  overlayClassName?: string;
}

export const Drawer: React.FC<DrawerProps> = ({
  isOpen,
  onClose,
  children,
  placement = 'right',
  size = 'md',
  showCloseButton = true,
  preventScroll = true,
  className,
  overlayClassName,
}) => {
  const drawerRef = useRef<HTMLDivElement>(null);

  // Handle ESC key
  useEscapeKey(() => {
    onClose();
  }, isOpen);

  // Prevent scroll on body when drawer is open
  useEffect(() => {
    if (preventScroll && isOpen) {
      document.body.style.overflow = 'hidden';
    } else {
      document.body.style.overflow = '';
    }

    return () => {
      document.body.style.overflow = '';
    };
  }, [isOpen, preventScroll]);

  // Calculate size based on placement and size prop
  const getSizeClass = () => {
    const horizontalSizes = {
      xs: 'w-64',
      sm: 'w-80',
      md: 'w-96',
      lg: 'w-1/3',
      xl: 'w-1/2',
      full: 'w-screen',
    };

    const verticalSizes = {
      xs: 'h-1/4',
      sm: 'h-1/3',
      md: 'h-1/2',
      lg: 'h-2/3',
      xl: 'h-3/4',
      full: 'h-screen',
    };

    return placement === 'left' || placement === 'right'
      ? horizontalSizes[size]
      : verticalSizes[size];
  };

  // Get position and transform classes based on placement
  const getPositionClasses = () => {
    const positions = {
      left: 'inset-y-0 left-0',
      right: 'inset-y-0 right-0',
      top: 'inset-x-0 top-0',
      bottom: 'inset-x-0 bottom-0',
    };
    return positions[placement];
  };

  const getTransformClasses = () => {
    const transforms = {
      left: 'translate-x-[-100%]',
      right: 'translate-x-[100%]',
      top: 'translate-y-[-100%]',
      bottom: 'translate-y-[100%]',
    };
    return transforms[placement];
  };

  if (!isOpen) return null;

  return createPortal(
    <Transition
      show={isOpen}
      appear
      enter="transition-opacity ease-out duration-300"
      enterFrom="opacity-0"
      enterTo="opacity-100"
      leave="transition-opacity ease-in duration-200"
      leaveFrom="opacity-100"
      leaveTo="opacity-0"
      className="fixed inset-0 z-50 overflow-hidden"
    >
      <div className="flex h-full">
        {/* Overlay */}
        <div
          className={cn(
            "fixed inset-0 bg-black bg-opacity-50 transition-opacity",
            overlayClassName
          )}
          aria-hidden="true"
          onClick={onClose}
        />

        {/* Drawer */}
        <FocusTrap>
          <Transition.Child
            enter="transform transition ease-in-out duration-300"
            enterFrom={getTransformClasses()}
            enterTo="translate-x-0 translate-y-0"
            leave="transform transition ease-in-out duration-200"
            leaveFrom="translate-x-0 translate-y-0"
            leaveTo={getTransformClasses()}
            className={cn(
              "fixed bg-white shadow-xl",
              getPositionClasses(),
              getSizeClass(),
              className
            )}
            ref={drawerRef}
          >
            {showCloseButton && (
              <button
                type="button"
                className="absolute right-4 top-4 z-10 rounded-md p-1 text-gray-400 hover:bg-gray-100 hover:text-gray-500 focus:outline-none focus:ring-2 focus:ring-primary-500"
                onClick={onClose}
              >
                <span className="sr-only">Close</span>
                <XMarkIcon className="h-5 w-5" aria-hidden="true" />
              </button>
            )}
            <div className="h-full overflow-y-auto">{children}</div>
          </Transition.Child>
        </FocusTrap>
      </div>
    </Transition>,
    document.body
  );
};
```

### useModal Hook
```tsx
// src/hooks/useModal.tsx
import { useState, useCallback } from 'react';

export function useModalState(initialState = false) {
  const [isOpen, setIsOpen] = useState(initialState);

  const open = useCallback(() => setIsOpen(true), []);
  const close = useCallback(() => setIsOpen(false), []);
  const toggle = useCallback(() => setIsOpen(prev => !prev), []);

  return { isOpen, open, close, toggle };
}
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-CORE-003: Form Components (related - used inside modals)
- FE-HRM-003: Employee Form (dependent - uses modal)
- FE-OPP-003: Note/Activity Components (dependent - uses modal)

## Tiêu chí chấp nhận
- Tất cả modal variants hoạt động đúng (standard, confirmation, form, alert, full-screen)
- Drawer component hoạt động với tất cả placements (left, right, top, bottom)
- Animation/transitions mượt mà và chuyên nghiệp
- Focus trap hoạt động trong modal và không cho phép tabbing ra ngoài
- ESC key và click outside đóng modal đúng cách
- Modal có thể được nested mà không bị confict
- Modal responsive trên tất cả kích thước màn hình
- Keyboard navigation được hỗ trợ đầy đủ
- Accessibility compliant với ARIA roles/labels
- Modal provider và useModal hook hoạt động đúng cách
- Documentation đầy đủ về cách sử dụng modal components 