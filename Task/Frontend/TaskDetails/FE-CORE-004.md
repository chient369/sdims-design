# Task Detail: FE-CORE-004

## Thông tin chung
- **ID**: FE-CORE-004
- **Tên task**: Xây dựng Table components
- **Độ ưu tiên**: Cao
- **Estimate**: 3 days
- **Tham chiếu**: Các màn hình danh sách trong toàn bộ hệ thống (MH-HRM-01, MH-OPP-01, MH-CTR-01, etc.)

## Mô tả
Xây dựng bộ Table components toàn diện và linh hoạt cho ứng dụng SDIMS, dùng để hiển thị dữ liệu dạng bảng với các tính năng sorting, pagination, filtering và custom rendering. Các components này sẽ được sử dụng xuyên suốt ứng dụng cho tất cả màn hình danh sách như danh sách nhân viên, danh sách cơ hội, hợp đồng, v.v. Table components cần phải linh hoạt, hiệu năng tốt, hỗ trợ các tùy chỉnh về hiển thị và xử lý dữ liệu.

## Yêu cầu kỹ thuật
### Core Table Features
- Data grid với hỗ trợ pagination, sorting, filtering
- Fixed headers khi scroll content
- Resizable columns (nếu cần)
- Responsive behavior (horizontal scroll hoặc stacked layout)
- Row selection (single và multiple)
- Row hover states
- Row actions (inline và menu)
- Empty state handling
- Loading state và skeletons

### Column Features
- Custom cell renderers
- Auto-width và manual width
- Column alignment (left, center, right)
- Column visibility toggling
- Column reordering (optional)
- Sort indicators và multi-sort
- Tooltips cho content quá dài

### Table Utilities
- Bulk actions cho selected rows
- Export functionality (CSV, Excel)
- Quick search/filter
- Advanced filtering
- Pagination controls với page size selection
- Sticky eerste/last columns
- Row grouping (if needed)
- Row expand/collapse cho nested data

## Chi tiết các thành phần

### Base Table Component
```tsx
// src/components/table/Table.tsx
import React, { useState, useMemo } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getPaginationRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  flexRender,
  ColumnDef,
  SortingState,
  ColumnFiltersState,
  PaginationState,
} from '@tanstack/react-table';
import { cn } from '../../utils/cn';
import { TablePagination } from './TablePagination';
import { TableToolbar } from './TableToolbar';
import { Spinner } from '../ui/Spinner';

export interface TableProps<TData> {
  data: TData[];
  columns: ColumnDef<TData, any>[];
  isLoading?: boolean;
  enableSorting?: boolean;
  enableFiltering?: boolean;
  enablePagination?: boolean;
  enableRowSelection?: boolean;
  enableBulkActions?: boolean;
  onRowClick?: (row: TData) => void;
  rowClassName?: (row: TData) => string;
  toolbarContent?: React.ReactNode;
  bulkActions?: React.ReactNode;
  initialSorting?: SortingState;
  initialFilters?: ColumnFiltersState;
  getRowId?: (row: TData) => string;
  className?: string;
}

export function Table<TData>({
  data,
  columns,
  isLoading = false,
  enableSorting = true,
  enableFiltering = true,
  enablePagination = true,
  enableRowSelection = false,
  enableBulkActions = false,
  onRowClick,
  rowClassName,
  toolbarContent,
  bulkActions,
  initialSorting = [],
  initialFilters = [],
  getRowId,
  className,
}: TableProps<TData>) {
  const [sorting, setSorting] = useState<SortingState>(initialSorting);
  const [columnFilters, setColumnFilters] = useState<ColumnFiltersState>(initialFilters);
  const [pagination, setPagination] = useState<PaginationState>({
    pageIndex: 0,
    pageSize: 10,
  });
  const [rowSelection, setRowSelection] = useState({});

  const table = useReactTable({
    data,
    columns,
    state: {
      sorting,
      columnFilters,
      pagination: enablePagination ? pagination : undefined,
      rowSelection: enableRowSelection ? rowSelection : undefined,
    },
    enableRowSelection,
    enableMultiRowSelection: enableRowSelection,
    onRowSelectionChange: setRowSelection,
    onSortingChange: setSorting,
    onColumnFiltersChange: setColumnFilters,
    onPaginationChange: setPagination,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: enableSorting ? getSortedRowModel() : undefined,
    getFilteredRowModel: enableFiltering ? getFilteredRowModel() : undefined,
    getPaginationRowModel: enablePagination ? getPaginationRowModel() : undefined,
    getRowId,
  });

  const showToolbar = toolbarContent || (enableBulkActions && bulkActions && Object.keys(rowSelection).length > 0);
  const selectedRows = useMemo(() => 
    Object.keys(rowSelection).length, 
    [rowSelection]
  );
  
  return (
    <div className="flex flex-col w-full">
      {showToolbar && (
        <TableToolbar
          selectedCount={selectedRows}
          bulkActions={bulkActions}
          toolbarContent={toolbarContent}
        />
      )}
      
      <div className={cn("relative overflow-x-auto border rounded-md", className)}>
        {isLoading && (
          <div className="absolute inset-0 flex items-center justify-center bg-white bg-opacity-70 z-10">
            <Spinner size="md" />
          </div>
        )}
        
        <table className="w-full text-sm text-left">
          <thead className="text-xs uppercase text-gray-700 bg-gray-50 border-b">
            {table.getHeaderGroups().map(headerGroup => (
              <tr key={headerGroup.id}>
                {headerGroup.headers.map(header => (
                  <th
                    key={header.id}
                    className={cn(
                      "px-4 py-3 font-medium",
                      header.column.getCanSort() ? "cursor-pointer select-none" : "",
                      header.column.getIsSorted() && "bg-gray-100"
                    )}
                    onClick={header.column.getCanSort() ? header.column.getToggleSortingHandler() : undefined}
                    style={{ width: header.column.getSize() }}
                  >
                    <div className="flex items-center justify-between">
                      {flexRender(header.column.columnDef.header, header.getContext())}
                      
                      {header.column.getCanSort() && (
                        <span className="ml-2">
                          {{ asc: '↑', desc: '↓' }[header.column.getIsSorted() as string] ?? '↕'}
                        </span>
                      )}
                    </div>
                  </th>
                ))}
              </tr>
            ))}
          </thead>
          
          <tbody>
            {table.getRowModel().rows.length ? (
              table.getRowModel().rows.map(row => (
                <tr 
                  key={row.id}
                  className={cn(
                    "border-b hover:bg-gray-50 transition-colors",
                    row.getIsSelected() && "bg-primary-50",
                    onRowClick && "cursor-pointer",
                    rowClassName && rowClassName(row.original)
                  )}
                  onClick={onRowClick ? () => onRowClick(row.original) : undefined}
                >
                  {row.getVisibleCells().map(cell => (
                    <td key={cell.id} className="px-4 py-3">
                      {flexRender(cell.column.columnDef.cell, cell.getContext())}
                    </td>
                  ))}
                </tr>
              ))
            ) : (
              <tr>
                <td 
                  colSpan={columns.length} 
                  className="px-4 py-10 text-center text-gray-500"
                >
                  {isLoading ? 'Loading...' : 'No data available'}
                </td>
              </tr>
            )}
          </tbody>
        </table>
        
        {enablePagination && table.getRowModel().rows.length > 0 && (
          <TablePagination table={table} />
        )}
      </div>
    </div>
  );
}
```

### Table Pagination Component
```tsx
// src/components/table/TablePagination.tsx
import React from 'react';
import { Table } from '@tanstack/react-table';
import { Button } from '../ui/Button';
import {
  HiChevronDoubleLeft,
  HiChevronLeft,
  HiChevronRight,
  HiChevronDoubleRight,
} from 'react-icons/hi';

interface TablePaginationProps<TData> {
  table: Table<TData>;
}

export function TablePagination<TData>({ table }: TablePaginationProps<TData>) {
  return (
    <div className="flex items-center justify-between px-4 py-3 border-t">
      <div className="flex items-center gap-2">
        <span className="text-sm text-gray-700">Rows per page:</span>
        <select
          value={table.getState().pagination.pageSize}
          onChange={e => {
            table.setPageSize(Number(e.target.value));
          }}
          className="block text-sm border-gray-300 rounded-md shadow-sm focus:ring-primary-500 focus:border-primary-500"
        >
          {[10, 20, 30, 40, 50].map(pageSize => (
            <option key={pageSize} value={pageSize}>
              {pageSize}
            </option>
          ))}
        </select>
      </div>
      
      <div className="flex items-center gap-1 md:gap-2">
        <span className="text-sm text-gray-700">
          Page {table.getState().pagination.pageIndex + 1} of{' '}
          {table.getPageCount()}
        </span>
        
        <div className="flex items-center gap-1">
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.setPageIndex(0)}
            disabled={!table.getCanPreviousPage()}
            className="px-2"
          >
            <HiChevronDoubleLeft className="h-4 w-4" />
          </Button>
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.previousPage()}
            disabled={!table.getCanPreviousPage()}
            className="px-2"
          >
            <HiChevronLeft className="h-4 w-4" />
          </Button>
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.nextPage()}
            disabled={!table.getCanNextPage()}
            className="px-2"
          >
            <HiChevronRight className="h-4 w-4" />
          </Button>
          <Button
            variant="outline"
            size="sm"
            onClick={() => table.setPageIndex(table.getPageCount() - 1)}
            disabled={!table.getCanNextPage()}
            className="px-2"
          >
            <HiChevronDoubleRight className="h-4 w-4" />
          </Button>
        </div>
      </div>
    </div>
  );
}
```

### Table Toolbar Component
```tsx
// src/components/table/TableToolbar.tsx
import React from 'react';
import { cn } from '../../utils/cn';

interface TableToolbarProps {
  selectedCount: number;
  bulkActions?: React.ReactNode;
  toolbarContent?: React.ReactNode;
}

export function TableToolbar({
  selectedCount,
  bulkActions,
  toolbarContent,
}: TableToolbarProps) {
  const showBulkActions = selectedCount > 0 && bulkActions;
  
  if (!showBulkActions && !toolbarContent) {
    return null;
  }
  
  return (
    <div className={cn(
      "flex items-center p-2 mb-2 rounded-md",
      showBulkActions ? "bg-gray-100" : ""
    )}>
      {showBulkActions ? (
        <div className="flex items-center w-full justify-between">
          <span className="text-sm font-medium text-gray-700">
            {selectedCount} {selectedCount === 1 ? 'item' : 'items'} selected
          </span>
          <div className="flex items-center space-x-2">
            {bulkActions}
          </div>
        </div>
      ) : (
        toolbarContent
      )}
    </div>
  );
}
```

### Cell Renderers
```tsx
// src/components/table/cellRenderers.tsx
import React from 'react';
import { ColumnDef } from '@tanstack/react-table';
import { HiDotsVertical, HiPencil, HiTrash, HiEye } from 'react-icons/hi';
import { Button } from '../ui/Button';
import { Menu, Transition } from '@headlessui/react';
import { format } from 'date-fns';
import { Badge } from '../ui/Badge';

// Action cell with dropdown menu
export function ActionCell<TData>({
  row,
  onView,
  onEdit,
  onDelete,
}: {
  row: TData;
  onView?: (row: TData) => void;
  onEdit?: (row: TData) => void;
  onDelete?: (row: TData) => void;
}) {
  return (
    <Menu as="div" className="relative inline-block text-left">
      <Menu.Button as={React.Fragment}>
        <Button
          variant="ghost"
          size="sm"
          className="p-1"
        >
          <HiDotsVertical className="h-5 w-5" />
        </Button>
      </Menu.Button>
      <Transition
        enter="transition ease-out duration-100"
        enterFrom="transform opacity-0 scale-95"
        enterTo="transform opacity-100 scale-100"
        leave="transition ease-in duration-75"
        leaveFrom="transform opacity-100 scale-100"
        leaveTo="transform opacity-0 scale-95"
      >
        <Menu.Items className="absolute right-0 z-10 mt-2 w-48 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none">
          <div className="py-1">
            {onView && (
              <Menu.Item>
                {({ active }) => (
                  <button
                    onClick={() => onView(row)}
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex w-full items-center px-4 py-2 text-sm text-gray-700`}
                  >
                    <HiEye className="mr-3 h-5 w-5 text-gray-400" />
                    View Details
                  </button>
                )}
              </Menu.Item>
            )}
            {onEdit && (
              <Menu.Item>
                {({ active }) => (
                  <button
                    onClick={() => onEdit(row)}
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex w-full items-center px-4 py-2 text-sm text-gray-700`}
                  >
                    <HiPencil className="mr-3 h-5 w-5 text-gray-400" />
                    Edit
                  </button>
                )}
              </Menu.Item>
            )}
            {onDelete && (
              <Menu.Item>
                {({ active }) => (
                  <button
                    onClick={() => onDelete(row)}
                    className={`${
                      active ? 'bg-gray-100' : ''
                    } flex w-full items-center px-4 py-2 text-sm text-red-600`}
                  >
                    <HiTrash className="mr-3 h-5 w-5 text-red-400" />
                    Delete
                  </button>
                )}
              </Menu.Item>
            )}
          </div>
        </Menu.Items>
      </Transition>
    </Menu>
  );
}

// Date cell renderer
export function DateCell({ value }: { value: Date | string | null | undefined }) {
  if (!value) return <span className="text-gray-400">-</span>;
  
  const date = typeof value === 'string' ? new Date(value) : value;
  return <span>{format(date, 'dd/MM/yyyy')}</span>;
}

// Status cell with badge
export function StatusCell({
  value,
  mapping = {},
}: {
  value: string;
  mapping?: Record<string, { label: string; color: string }>;
}) {
  const status = mapping[value] || { label: value, color: 'gray' };
  
  return (
    <Badge color={status.color as any}>
      {status.label}
    </Badge>
  );
}

// Boolean cell with icon
export function BooleanCell({ value }: { value: boolean }) {
  return value ? (
    <div className="flex justify-center">
      <div className="h-5 w-5 rounded-full bg-green-100 flex items-center justify-center">
        <svg className="h-3.5 w-3.5 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M5 13l4 4L19 7" />
        </svg>
      </div>
    </div>
  ) : (
    <div className="flex justify-center">
      <div className="h-5 w-5 rounded-full bg-red-100 flex items-center justify-center">
        <svg className="h-3.5 w-3.5 text-red-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M6 18L18 6M6 6l12 12" />
        </svg>
      </div>
    </div>
  );
}
```

### Example Usage
```tsx
// Example usage of the Table component
import { Table } from '../../components/table/Table';
import { ActionCell, DateCell, StatusCell } from '../../components/table/cellRenderers';
import { createColumnHelper } from '@tanstack/react-table';

interface Employee {
  id: string;
  name: string;
  email: string;
  role: string;
  status: string;
  joinDate: string;
}

const columnHelper = createColumnHelper<Employee>();

const columns = [
  columnHelper.accessor('name', {
    header: 'Name',
    cell: info => info.getValue(),
  }),
  columnHelper.accessor('email', {
    header: 'Email',
    cell: info => info.getValue(),
  }),
  columnHelper.accessor('role', {
    header: 'Role',
    cell: info => info.getValue(),
  }),
  columnHelper.accessor('status', {
    header: 'Status',
    cell: info => (
      <StatusCell 
        value={info.getValue()} 
        mapping={{
          active: { label: 'Active', color: 'green' },
          bench: { label: 'On Bench', color: 'blue' },
          leave: { label: 'On Leave', color: 'yellow' },
          resigned: { label: 'Resigned', color: 'red' },
        }}
      />
    ),
  }),
  columnHelper.accessor('joinDate', {
    header: 'Join Date',
    cell: info => <DateCell value={info.getValue()} />,
  }),
  columnHelper.display({
    id: 'actions',
    header: 'Actions',
    cell: info => (
      <ActionCell
        row={info.row.original}
        onView={() => handleView(info.row.original)}
        onEdit={() => handleEdit(info.row.original)}
        onDelete={() => handleDelete(info.row.original)}
      />
    ),
  }),
];

function EmployeeList() {
  const [data, setData] = useState<Employee[]>([]);
  const [isLoading, setIsLoading] = useState(false);
  
  // Function implementations for handling actions...
  
  return (
    <div className="p-4">
      <Table
        data={data}
        columns={columns}
        isLoading={isLoading}
        enableRowSelection
        bulkActions={
          <>
            <Button variant="outline" size="sm">Export</Button>
            <Button variant="danger" size="sm">Delete Selected</Button>
          </>
        }
        toolbarContent={
          <div className="w-full flex justify-between items-center">
            <input
              type="text"
              placeholder="Search employees..."
              className="px-3 py-2 border border-gray-300 rounded-md"
            />
            <Button>Add Employee</Button>
          </div>
        }
        onRowClick={row => handleView(row)}
      />
    </div>
  );
}
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-CORE-003: Form Components (related - for filters)
- FE-HRM-001: Employee List Page (dependent)
- FE-OPP-001: Opportunity List Page (dependent)
- FE-CTR-001: Contract List Page (dependent)

## Tiêu chí chấp nhận
- Table component hiển thị dữ liệu chính xác với các column được cấu hình
- Sorting, filtering, và pagination hoạt động mượt mà
- Row selection và bulk actions hoạt động đúng cách
- Responsive design cho tất cả kích thước màn hình
- Custom cell renderers cho các loại dữ liệu khác nhau
- Loading state và empty state được hiển thị đúng
- Table toolbar với filtering options và bulk actions
- Hiệu năng tốt với tập dữ liệu lớn
- Bố cục và styling nhất quán với design system
- Accessibility được đảm bảo (keyboard navigation, ARIA)
- Documentation đầy đủ về cách sử dụng và customize table 