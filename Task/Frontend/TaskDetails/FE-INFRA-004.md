# Task Detail: FE-INFRA-004

## Thông tin chung
- **ID**: FE-INFRA-004
- **Tên task**: Xây dựng hệ thống theme và design system
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: 
  - N/A (Task cơ sở hạ tầng)

## Mô tả
Xây dựng hệ thống theme và design system cho ứng dụng SDIMS frontend để đảm bảo tính nhất quán và chuyên nghiệp trong toàn bộ giao diện người dùng. Task này bao gồm việc thiết lập và cấu hình Tailwind CSS, định nghĩa các giá trị thiết kế cơ bản (màu sắc, typography, spacing), và xây dựng các UI components cơ bản theo nguyên tắc design system. Hệ thống theme đảm bảo sự thống nhất trong toàn bộ ứng dụng, dễ dàng bảo trì và mở rộng, đồng thời cung cấp nền tảng vững chắc cho phát triển các tính năng UI/UX phức tạp hơn.

## Yêu cầu kỹ thuật
### Cài đặt và cấu hình Tailwind CSS
- Cài đặt Tailwind CSS và các dependencies cần thiết
- Tích hợp Tailwind với Vite
- Cấu hình PostCSS
- Thiết lập các plugins cần thiết (forms, typography, line-clamp, etc.)

### Thiết lập Theme
- Định nghĩa color palette:
  - Primary colors (brand colors)
  - Secondary colors
  - Neutral colors (grays)
  - Semantic colors (success, warning, error, info)
- Cấu hình typography:
  - Font families
  - Font sizes
  - Line heights
  - Font weights
- Thiết lập spacing và sizing scales
- Cấu hình breakpoints cho responsive design
- Thiết lập các giá trị border, shadow, opacity, transition

### Xây dựng UI Components cơ bản
Phát triển các components cơ bản với variant system:
- Button components
- Input components
- Card components
- Badge components
- Alert components
- Typography components (headings, text)

### Theme Configuration
- Thiết lập dark mode support (nếu cần)
- Cấu hình CSS variables cho theme values
- Thiết lập theme switching mechanism (nếu cần)
- Xử lý accessibility considerations (contrast, focus styles)

### Component Documentation
- Tạo các examples cho mỗi component
- Cung cấp guidelines về khi nào sử dụng component nào
- Thiết lập các conventions cho việc mở rộng components

## Chi tiết các thành phần

### Tailwind Configuration
Tạo file `tailwind.config.js` với các cấu hình custom:

```javascript
// tailwind.config.js
const colors = require('tailwindcss/colors');

module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eef2ff',
          100: '#e0e7ff',
          200: '#c7d2fe',
          300: '#a5b4fc',
          400: '#818cf8',
          500: '#6366f1', // Primary brand color
          600: '#4f46e5',
          700: '#4338ca',
          800: '#3730a3',
          900: '#312e81',
          950: '#1e1b4b',
        },
        secondary: {
          // Secondary color palette
        },
        success: colors.emerald,
        warning: colors.amber,
        error: colors.rose,
        info: colors.sky,
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Roboto Mono', 'monospace'],
      },
      fontSize: {
        'xs': ['0.75rem', { lineHeight: '1rem' }],
        'sm': ['0.875rem', { lineHeight: '1.25rem' }],
        'base': ['1rem', { lineHeight: '1.5rem' }],
        'lg': ['1.125rem', { lineHeight: '1.75rem' }],
        'xl': ['1.25rem', { lineHeight: '1.75rem' }],
        '2xl': ['1.5rem', { lineHeight: '2rem' }],
        '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
        '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
      },
      boxShadow: {
        'sm': '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
        'DEFAULT': '0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06)',
        'md': '0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)',
        'lg': '0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)',
        'xl': '0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)',
        '2xl': '0 25px 50px -12px rgba(0, 0, 0, 0.25)',
        'inner': 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.06)',
        'none': 'none',
      },
      borderRadius: {
        'none': '0',
        'sm': '0.125rem',
        'DEFAULT': '0.25rem',
        'md': '0.375rem',
        'lg': '0.5rem',
        'xl': '0.75rem',
        '2xl': '1rem',
        'full': '9999px',
      },
      spacing: {
        // Custom spacing if needed
      },
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
    require('@tailwindcss/aspect-ratio'),
    require('@tailwindcss/line-clamp'),
  ],
};
```

### Base CSS Setup
Tạo file `src/index.css` với các base styles:

```css
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';

@layer base {
  html {
    font-family: Inter, system-ui, sans-serif;
  }
  
  h1 {
    @apply text-3xl font-bold text-gray-900;
  }
  
  h2 {
    @apply text-2xl font-bold text-gray-900;
  }
  
  h3 {
    @apply text-xl font-bold text-gray-900;
  }
  
  h4 {
    @apply text-lg font-medium text-gray-900;
  }
  
  p {
    @apply text-base text-gray-700;
  }
  
  /* Focus styles for better accessibility */
  :focus-visible {
    @apply outline-none ring-2 ring-primary-500 ring-offset-2;
  }
}

@layer components {
  /* Custom component classes can go here */
}

@layer utilities {
  /* Custom utility classes can go here */
}
```

### Button Component
Tạo component button với các variants:

```tsx
// src/components/ui/Button.tsx
import React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '../../utils/cn';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-primary-500 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-primary-600 text-white hover:bg-primary-700',
        secondary: 'bg-secondary-600 text-white hover:bg-secondary-700',
        outline: 'border border-gray-300 bg-transparent hover:bg-gray-50 text-gray-700',
        ghost: 'bg-transparent hover:bg-gray-100 text-gray-700',
        link: 'bg-transparent underline-offset-4 hover:underline text-primary-600 hover:text-primary-700',
        danger: 'bg-error-600 text-white hover:bg-error-700',
        success: 'bg-success-600 text-white hover:bg-success-700',
      },
      size: {
        xs: 'h-7 px-2 text-xs',
        sm: 'h-8 px-3',
        md: 'h-10 px-4',
        lg: 'h-12 px-6',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'md',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  isLoading?: boolean;
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, isLoading = false, children, ...props }, ref) => {
    return (
      <button
        className={cn(buttonVariants({ variant, size }), className)}
        ref={ref}
        disabled={isLoading || props.disabled}
        {...props}
      >
        {isLoading && (
          <svg className="mr-2 h-4 w-4 animate-spin" viewBox="0 0 24 24">
            {/* Spinner SVG */}
          </svg>
        )}
        {children}
      </button>
    );
  }
);

Button.displayName = 'Button';

export { Button, buttonVariants };
```

### Utility Functions
Tạo các utility functions cho styling:

```typescript
// src/utils/cn.ts
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

// Utility for merging tailwind classes safely
export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

### Theme Context (Optional for theme switching)
```tsx
// src/context/ThemeContext.tsx
import React, { createContext, useContext, useEffect, useState } from 'react';

type Theme = 'light' | 'dark';

type ThemeContextType = {
  theme: Theme;
  setTheme: (theme: Theme) => void;
};

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<Theme>('light');

  useEffect(() => {
    const savedTheme = localStorage.getItem('theme') as Theme;
    if (savedTheme) {
      setTheme(savedTheme);
    } else if (window.matchMedia('(prefers-color-scheme: dark)').matches) {
      setTheme('dark');
    }
  }, []);

  useEffect(() => {
    localStorage.setItem('theme', theme);
    document.documentElement.classList.remove('light', 'dark');
    document.documentElement.classList.add(theme);
  }, [theme]);

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-CORE-001: Layout Components (dependent)
- FE-CORE-003: Form Components (dependent)
- FE-CORE-004: Table Components (dependent)
- FE-CORE-005: Modal Components (dependent)

## Tiêu chí chấp nhận
- Tailwind CSS được cài đặt và cấu hình đúng
- Theme configuration hoạt động với các giá trị được xác định
- Color palette được định nghĩa đầy đủ và có tính thương hiệu
- Typography styles được áp dụng nhất quán
- Các UI components cơ bản được xây dựng với variants phù hợp
- Responsive design hoạt động trên các breakpoints chính
- Dark mode hoạt động đúng (nếu được triển khai)
- Các component styles nhất quán với thiết kế và có tính accessibility
- Có documentation về cách sử dụng theme và component system
- UI elements được tối ưu hóa cho tái sử dụng và mở rộng 