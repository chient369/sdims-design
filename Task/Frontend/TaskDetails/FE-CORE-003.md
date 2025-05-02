# Task Detail: FE-CORE-003

## Thông tin chung
- **ID**: FE-CORE-003
- **Tên task**: Xây dựng Form components
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: Các màn hình input/form trong toàn bộ hệ thống

## Mô tả
Xây dựng bộ form components toàn diện và tái sử dụng cho ứng dụng SDIMS, bao gồm các input controls cơ bản, validation logic, và form helpers. Các components này sẽ được sử dụng xuyên suốt ứng dụng cho tất cả chức năng nhập liệu, chỉnh sửa dữ liệu, và search/filter controls. Form components được thiết kế tuân theo design system, có khả năng validation, accessibility support, và tối ưu cho trải nghiệm người dùng.

## Yêu cầu kỹ thuật
### Form Components
- Tích hợp với React Hook Form để quản lý form state
- Validation dựa trên schema với Zod/Yup
- Support cho các form layouts khác nhau (inline, stacked, etc.)
- Hiển thị validation errors nhất quán
- Responsive design
- Accessibility compliance (ARIA, keyboard navigation)
- Loading states và disabled states
- Support cho form navigation (multi-step forms)

### Input Components
- **Input**: Text, Number, Email, Password, với variants
- **Select**: Single-select, Multi-select, với search & filtering
- **Checkbox & Radio**: Support cho groups và standalone
- **DatePicker**: Calendar picker, range support, localization
- **TimePicker**: Hour/minute selection, 12/24h format
- **TextArea**: Support auto-resize, character count
- **FileInput**: Upload, preview, progress indicator
- **Toggle/Switch**: On/off boolean controls
- **Slider**: Range sliders với single/dual thumbs
- **AutoComplete**: Type-ahead search với options

### Form Layout & Helpers
- Form Group components
- Form Labels với required indicators
- Help text và tooltips
- Validation error display
- Form Section dividers
- Form Actions area (submit/cancel buttons)

## Chi tiết các thành phần

### Form Context and Provider
```tsx
// src/components/form/FormContext.tsx
import React, { createContext, useContext } from 'react';
import { 
  useForm, 
  FormProvider, 
  UseFormReturn, 
  FieldValues, 
  UseFormProps 
} from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import * as z from 'zod';

interface FormContextProps<TFieldValues extends FieldValues> {
  form: UseFormReturn<TFieldValues>;
}

const FormContext = createContext<FormContextProps<any> | undefined>(undefined);

export function useFormContext<TFieldValues extends FieldValues>() {
  const context = useContext(FormContext);
  if (!context) {
    throw new Error('useFormContext must be used within a FormProvider');
  }
  return context as FormContextProps<TFieldValues>;
}

interface FormProps<TFieldValues extends FieldValues, SchemaType extends z.ZodType<any, any>> {
  children: React.ReactNode;
  schema?: SchemaType;
  defaultValues?: UseFormProps<TFieldValues>['defaultValues'];
  onSubmit: (data: z.infer<SchemaType>) => void;
  mode?: UseFormProps<TFieldValues>['mode'];
}

export function Form<
  TFieldValues extends FieldValues = FieldValues,
  SchemaType extends z.ZodType<any, any> = z.ZodType<any, any>
>({
  children,
  schema,
  defaultValues,
  onSubmit,
  mode = 'onSubmit',
}: FormProps<TFieldValues, SchemaType>) {
  const form = useForm<TFieldValues>({
    resolver: schema ? zodResolver(schema) : undefined,
    defaultValues,
    mode,
  });

  return (
    <FormProvider {...form}>
      <FormContext.Provider value={{ form }}>
        <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
          {children}
        </form>
      </FormContext.Provider>
    </FormProvider>
  );
}
```

### Input Components
```tsx
// src/components/form/Input.tsx
import React, { forwardRef } from 'react';
import { useFormContext } from 'react-hook-form';
import { cn } from '../../utils/cn';

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  name: string;
  label?: string;
  helperText?: string;
  containerClassName?: string;
  labelClassName?: string;
  inputClassName?: string;
  errorClassName?: string;
  helperClassName?: string;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  (
    {
      name,
      label,
      helperText,
      containerClassName,
      labelClassName,
      inputClassName,
      errorClassName,
      helperClassName,
      ...props
    },
    ref
  ) => {
    const { register, formState: { errors } } = useFormContext();
    const error = errors[name];
    const errorMessage = error?.message as string | undefined;

    return (
      <div className={cn("flex flex-col space-y-1", containerClassName)}>
        {label && (
          <label
            htmlFor={name}
            className={cn(
              "text-sm font-medium text-gray-700",
              errorMessage && "text-red-500",
              labelClassName
            )}
          >
            {label}
            {props.required && <span className="text-red-500 ml-1">*</span>}
          </label>
        )}

        <input
          id={name}
          className={cn(
            "px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:ring-primary-500 focus:border-primary-500",
            errorMessage && "border-red-500 focus:ring-red-500 focus:border-red-500",
            inputClassName
          )}
          {...register(name)}
          {...props}
          ref={ref}
        />

        {(errorMessage || helperText) && (
          <div className="mt-1">
            {errorMessage && (
              <p className={cn("text-sm text-red-500", errorClassName)}>
                {errorMessage}
              </p>
            )}
            {!errorMessage && helperText && (
              <p className={cn("text-sm text-gray-500", helperClassName)}>
                {helperText}
              </p>
            )}
          </div>
        )}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### Select Component
```tsx
// src/components/form/Select.tsx
import React, { forwardRef } from 'react';
import { useFormContext } from 'react-hook-form';
import { cn } from '../../utils/cn';

interface Option {
  value: string | number;
  label: string;
  disabled?: boolean;
}

interface SelectProps extends Omit<React.SelectHTMLAttributes<HTMLSelectElement>, 'size'> {
  name: string;
  label?: string;
  helperText?: string;
  options: Option[];
  size?: 'sm' | 'md' | 'lg';
  containerClassName?: string;
  labelClassName?: string;
  selectClassName?: string;
  errorClassName?: string;
  helperClassName?: string;
}

export const Select = forwardRef<HTMLSelectElement, SelectProps>(
  (
    {
      name,
      label,
      helperText,
      options,
      size = 'md',
      containerClassName,
      labelClassName,
      selectClassName,
      errorClassName,
      helperClassName,
      ...props
    },
    ref
  ) => {
    const { register, formState: { errors } } = useFormContext();
    const error = errors[name];
    const errorMessage = error?.message as string | undefined;

    const sizeClasses = {
      sm: 'py-1 text-sm',
      md: 'py-2 text-base',
      lg: 'py-3 text-lg',
    };

    return (
      <div className={cn("flex flex-col space-y-1", containerClassName)}>
        {label && (
          <label
            htmlFor={name}
            className={cn(
              "text-sm font-medium text-gray-700",
              errorMessage && "text-red-500",
              labelClassName
            )}
          >
            {label}
            {props.required && <span className="text-red-500 ml-1">*</span>}
          </label>
        )}

        <select
          id={name}
          className={cn(
            "px-3 border border-gray-300 rounded-md shadow-sm focus:ring-primary-500 focus:border-primary-500",
            sizeClasses[size],
            errorMessage && "border-red-500 focus:ring-red-500 focus:border-red-500",
            selectClassName
          )}
          {...register(name)}
          {...props}
          ref={ref}
        >
          {props.placeholder && (
            <option value="" disabled>
              {props.placeholder}
            </option>
          )}
          {options.map((option) => (
            <option 
              key={option.value} 
              value={option.value}
              disabled={option.disabled}
            >
              {option.label}
            </option>
          ))}
        </select>

        {(errorMessage || helperText) && (
          <div className="mt-1">
            {errorMessage && (
              <p className={cn("text-sm text-red-500", errorClassName)}>
                {errorMessage}
              </p>
            )}
            {!errorMessage && helperText && (
              <p className={cn("text-sm text-gray-500", helperClassName)}>
                {helperText}
              </p>
            )}
          </div>
        )}
      </div>
    );
  }
);

Select.displayName = 'Select';
```

### Form Layout Components
```tsx
// src/components/form/FormSection.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface FormSectionProps {
  title?: string;
  description?: string;
  children: React.ReactNode;
  className?: string;
}

export const FormSection: React.FC<FormSectionProps> = ({
  title,
  description,
  children,
  className,
}) => {
  return (
    <div className={cn("py-4", className)}>
      {(title || description) && (
        <div className="mb-4">
          {title && <h3 className="text-lg font-medium text-gray-900">{title}</h3>}
          {description && <p className="mt-1 text-sm text-gray-500">{description}</p>}
        </div>
      )}
      <div className="space-y-4">{children}</div>
    </div>
  );
};

// src/components/form/FormRow.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface FormRowProps {
  children: React.ReactNode;
  className?: string;
}

export const FormRow: React.FC<FormRowProps> = ({ children, className }) => {
  return (
    <div className={cn("grid grid-cols-1 gap-4 md:grid-cols-2", className)}>
      {children}
    </div>
  );
};

// src/components/form/FormFooter.tsx
import React from 'react';
import { cn } from '../../utils/cn';
import { Button } from '../ui/Button';

interface FormFooterProps {
  submitLabel?: string;
  cancelLabel?: string;
  onCancel?: () => void;
  isSubmitting?: boolean;
  className?: string;
}

export const FormFooter: React.FC<FormFooterProps> = ({
  submitLabel = 'Submit',
  cancelLabel = 'Cancel',
  onCancel,
  isSubmitting = false,
  className,
}) => {
  return (
    <div className={cn("flex justify-end space-x-3 border-t pt-5 mt-5", className)}>
      {onCancel && (
        <Button 
          type="button" 
          variant="outline" 
          onClick={onCancel}
        >
          {cancelLabel}
        </Button>
      )}
      <Button 
        type="submit" 
        isLoading={isSubmitting}
      >
        {submitLabel}
      </Button>
    </div>
  );
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-CORE-001: Layout Components (related)
- FE-HRM-003: Employee Form (dependent)
- FE-CTR-003: Contract Form (dependent)

## Tiêu chí chấp nhận
- Tất cả form components đều thống nhất với design system
- React Hook Form tích hợp đúng với tất cả components
- Validation hoạt động chính xác với Zod/Yup schema
- Error states hiển thị rõ ràng và nhất quán
- Components responsive trên tất cả kích thước màn hình
- Focus states và keyboard navigation hoạt động tốt
- Disabled và loading states được xử lý đúng
- Form helpers (labels, error messages, tooltips) hiển thị đúng
- Multi-select components hoạt động hiệu quả với data sets lớn
- DatePicker có đầy đủ chức năng cần thiết
- Documentation đầy đủ về cách sử dụng form components 