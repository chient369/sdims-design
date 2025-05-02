# Task Detail: FE-CORE-007

## Thông tin chung
- **ID**: FE-CORE-007
- **Tên task**: Xây dựng Chart/Graph components
- **Độ ưu tiên**: Trung bình
- **Estimate**: 3 days
- **Tham chiếu**: MH-DSH-01 (Dashboard), MH-MGN-01 (Margin Management)

## Mô tả
Xây dựng các Chart/Graph components linh hoạt và tái sử dụng cao cho ứng dụng SDIMS để hiển thị dữ liệu dạng biểu đồ, thống kê và các chỉ số KPI. Task bao gồm việc tích hợp thư viện chart phù hợp, xây dựng các wrapper components cho các loại biểu đồ cơ bản (đường, cột, tròn, v.v.), và thiết kế các thành phần card hiển thị chỉ số KPI/thống kê. Các components này sẽ được sử dụng chủ yếu trong dashboard, màn hình quản lý margin, và các báo cáo.

## Yêu cầu kỹ thuật
### Chart Library Integration
- Tích hợp thư viện chart phù hợp (Chart.js, Recharts, ApexCharts, etc.)
- Styling theo theme của ứng dụng
- Responsive design cho các màn hình khác nhau
- Performance optimization (không re-render không cần thiết)
- Data loading & error handling
- Animation & interactions

### Chart Types
- **Line Chart**: Cho time series data, trends
- **Bar Chart**: So sánh các categories, horizontal và vertical
- **Pie/Donut Chart**: Hiển thị distribution, composition
- **Area Chart**: Trends và accumulated values
- **Gauge/Dial Chart**: Hiển thị progress, achievement
- **Stacked Bar/Area**: Composition over time
- **Heatmap**: Data intensity visualization (nếu cần)

### Chart Features
- Tooltips khi hover
- Legend với filter functionality
- Multiple series support
- Axis customization
- Grid lines
- Data labels
- Zoom/pan functionality (nếu cần)
- Custom formatters (dates, currency, percentages)

### KPI & Stat Cards
- KPI cards với indicators (up/down, percentage)
- Trend sparklines trong cards
- Color coding dựa trên target achievement
- Skeleton loaders cho loading state

### Chart Controls
- Date range selector
- Data granularity controls
- Category filters
- Series visibility toggles

## Chi tiết các thành phần

### Chart.js Integration
```typescript
// src/components/charts/Chart.tsx
import React, { useRef, useEffect } from 'react';
import { Chart as ChartJS, ChartOptions, ChartData, registerables } from 'chart.js';
import { cn } from '../../utils/cn';

// Register all Chart.js components
ChartJS.register(...registerables);

// Set defaults that match our theme
ChartJS.defaults.color = '#6B7280';
ChartJS.defaults.font.family = "'Inter', 'Helvetica', 'Arial', sans-serif";

interface ChartProps {
  type: 'line' | 'bar' | 'pie' | 'doughnut' | 'polarArea' | 'radar' | 'scatter' | 'bubble';
  data: ChartData;
  options?: ChartOptions;
  height?: number;
  width?: number;
  className?: string;
}

export const Chart: React.FC<ChartProps> = ({
  type,
  data,
  options,
  height = 300,
  width,
  className,
}) => {
  const chartRef = useRef<HTMLCanvasElement>(null);
  const chartInstance = useRef<ChartJS | null>(null);

  useEffect(() => {
    if (!chartRef.current) return;

    // Destroy existing chart
    if (chartInstance.current) {
      chartInstance.current.destroy();
    }

    // Create new chart
    const ctx = chartRef.current.getContext('2d');
    if (ctx) {
      chartInstance.current = new ChartJS(ctx, {
        type,
        data,
        options: {
          responsive: true,
          maintainAspectRatio: width !== undefined,
          ...options,
        },
      });
    }

    // Cleanup on unmount
    return () => {
      if (chartInstance.current) {
        chartInstance.current.destroy();
      }
    };
  }, [type, data, options, width]);

  return (
    <div className={cn("chart-container", className)} style={{ height, width }}>
      <canvas ref={chartRef} />
    </div>
  );
};
```

### Specific Chart Types
```typescript
// src/components/charts/LineChart.tsx
import React from 'react';
import { Chart } from './Chart';
import { ChartData, ChartOptions } from 'chart.js';

interface LineChartProps {
  data: ChartData<'line'>;
  options?: ChartOptions<'line'>;
  height?: number;
  width?: number;
  className?: string;
}

export const LineChart: React.FC<LineChartProps> = ({
  data,
  options,
  height,
  width,
  className,
}) => {
  const defaultOptions: ChartOptions<'line'> = {
    plugins: {
      tooltip: {
        mode: 'index',
        intersect: false,
      },
    },
    scales: {
      x: {
        grid: {
          display: false,
        },
      },
      y: {
        beginAtZero: true,
        grid: {
          borderDash: [2, 4],
          color: '#E5E7EB',
        },
      },
    },
    interaction: {
      mode: 'nearest',
      axis: 'x',
      intersect: false,
    },
  };

  return (
    <Chart
      type="line"
      data={data}
      options={{ ...defaultOptions, ...options }}
      height={height}
      width={width}
      className={className}
    />
  );
};

// src/components/charts/BarChart.tsx
import React from 'react';
import { Chart } from './Chart';
import { ChartData, ChartOptions } from 'chart.js';

interface BarChartProps {
  data: ChartData<'bar'>;
  options?: ChartOptions<'bar'>;
  height?: number;
  width?: number;
  className?: string;
  horizontal?: boolean;
}

export const BarChart: React.FC<BarChartProps> = ({
  data,
  options,
  height,
  width,
  className,
  horizontal = false,
}) => {
  const defaultOptions: ChartOptions<'bar'> = {
    indexAxis: horizontal ? 'y' : 'x',
    plugins: {
      tooltip: {
        mode: 'index',
        intersect: false,
      },
    },
    scales: {
      x: {
        grid: {
          display: false,
        },
      },
      y: {
        beginAtZero: true,
        grid: {
          borderDash: [2, 4],
          color: '#E5E7EB',
        },
      },
    },
  };

  return (
    <Chart
      type="bar"
      data={data}
      options={{ ...defaultOptions, ...options }}
      height={height}
      width={width}
      className={className}
    />
  );
};

// src/components/charts/PieChart.tsx and DoughnutChart.tsx would be similar
```

### KPI Stat Card
```tsx
// src/components/charts/KpiCard.tsx
import React from 'react';
import { ArrowUpIcon, ArrowDownIcon } from '@heroicons/react/24/solid';
import { cn } from '../../utils/cn';

export interface KpiCardProps {
  title: string;
  value: string | number;
  previousValue?: string | number;
  percentageChange?: number;
  formatter?: (value: string | number) => string;
  chart?: React.ReactNode;
  loading?: boolean;
  className?: string;
}

export const KpiCard: React.FC<KpiCardProps> = ({
  title,
  value,
  previousValue,
  percentageChange,
  formatter = (val) => String(val),
  chart,
  loading = false,
  className,
}) => {
  const hasChange = percentageChange !== undefined;
  const isPositiveChange = hasChange && percentageChange > 0;
  const isNegativeChange = hasChange && percentageChange < 0;
  const absChange = hasChange ? Math.abs(percentageChange) : 0;

  return (
    <div
      className={cn(
        "bg-white rounded-lg border p-5 shadow-sm transition-all hover:shadow-md",
        className
      )}
    >
      {loading ? (
        <div className="animate-pulse">
          <div className="h-4 bg-gray-200 rounded w-3/4 mb-3"></div>
          <div className="h-8 bg-gray-200 rounded w-1/2 mb-3"></div>
          <div className="h-3 bg-gray-200 rounded w-1/4 mb-3"></div>
          {chart && <div className="h-16 bg-gray-200 rounded w-full mt-3"></div>}
        </div>
      ) : (
        <>
          <h3 className="text-sm font-medium text-gray-500">{title}</h3>
          <div className="mt-1 flex items-baseline">
            <p className="text-2xl font-semibold text-gray-900">
              {formatter(value)}
            </p>
            {hasChange && (
              <p
                className={cn(
                  "ml-2 flex items-baseline text-sm font-semibold",
                  isPositiveChange ? "text-green-600" : "",
                  isNegativeChange ? "text-red-600" : "",
                  !isPositiveChange && !isNegativeChange ? "text-gray-500" : ""
                )}
              >
                {isPositiveChange && <ArrowUpIcon className="h-3 w-3 flex-shrink-0 self-center" />}
                {isNegativeChange && <ArrowDownIcon className="h-3 w-3 flex-shrink-0 self-center" />}
                <span className="ml-1">{absChange.toFixed(1)}%</span>
              </p>
            )}
          </div>
          {previousValue !== undefined && (
            <p className="text-xs text-gray-500 mt-1">
              Previous: {formatter(previousValue)}
            </p>
          )}
          {chart && <div className="mt-3 h-16">{chart}</div>}
        </>
      )}
    </div>
  );
};
```

### Sparkline Component
```tsx
// src/components/charts/Sparkline.tsx
import React from 'react';
import { Line } from 'react-chartjs-2';
import { cn } from '../../utils/cn';

interface SparklineProps {
  data: number[];
  labels?: string[];
  color?: string;
  width?: number;
  height?: number;
  className?: string;
}

export const Sparkline: React.FC<SparklineProps> = ({
  data,
  labels,
  color = '#4F46E5',
  width,
  height = 30,
  className,
}) => {
  const chartData = {
    labels: labels || data.map((_, i) => i.toString()),
    datasets: [
      {
        data,
        borderColor: color,
        backgroundColor: 'rgba(0, 0, 0, 0)',
        borderWidth: 1.5,
        pointRadius: 0,
        tension: 0.3,
      },
    ],
  };

  const options = {
    maintainAspectRatio: false,
    plugins: {
      legend: {
        display: false,
      },
      tooltip: {
        enabled: false,
      },
    },
    scales: {
      x: {
        display: false,
      },
      y: {
        display: false,
        min: Math.min(...data) * 0.9,
        max: Math.max(...data) * 1.1,
      },
    },
    elements: {
      line: {
        cubicInterpolationMode: 'monotone',
      },
    },
  };

  return (
    <div className={cn("sparkline", className)} style={{ width, height }}>
      <Line data={chartData} options={options} />
    </div>
  );
};
```

### Chart DateRange Selector
```tsx
// src/components/charts/DateRangeSelector.tsx
import React from 'react';
import { cn } from '../../utils/cn';

export type DateRange = 'day' | 'week' | 'month' | 'quarter' | 'year' | 'custom';

interface DateRangeSelectorProps {
  value: DateRange;
  onChange: (range: DateRange) => void;
  allowCustom?: boolean;
  className?: string;
  buttonClassName?: string;
}

export const DateRangeSelector: React.FC<DateRangeSelectorProps> = ({
  value,
  onChange,
  allowCustom = true,
  className,
  buttonClassName,
}) => {
  const ranges: { label: string; value: DateRange }[] = [
    { label: 'Day', value: 'day' },
    { label: 'Week', value: 'week' },
    { label: 'Month', value: 'month' },
    { label: 'Quarter', value: 'quarter' },
    { label: 'Year', value: 'year' },
  ];

  if (allowCustom) {
    ranges.push({ label: 'Custom', value: 'custom' });
  }

  return (
    <div className={cn("inline-flex rounded-md shadow-sm", className)}>
      {ranges.map((range) => (
        <button
          key={range.value}
          type="button"
          className={cn(
            "px-3 py-1.5 text-sm font-medium border",
            range.value === value
              ? "bg-primary-100 text-primary-700 border-primary-300 z-10"
              : "bg-white text-gray-700 border-gray-300 hover:bg-gray-50",
            ranges.indexOf(range) === 0 && "rounded-l-md",
            ranges.indexOf(range) === ranges.length - 1 && "rounded-r-md",
            ranges.indexOf(range) !== 0 && "-ml-px",
            buttonClassName
          )}
          onClick={() => onChange(range.value)}
        >
          {range.label}
        </button>
      ))}
    </div>
  );
};
```

### Example Dashboard Chart
```tsx
// Example usage of chart components
import React, { useState } from 'react';
import { LineChart } from '../../components/charts/LineChart';
import { BarChart } from '../../components/charts/BarChart';
import { KpiCard } from '../../components/charts/KpiCard';
import { Sparkline } from '../../components/charts/Sparkline';
import { DateRangeSelector, DateRange } from '../../components/charts/DateRangeSelector';

const DashboardChartsExample: React.FC = () => {
  const [dateRange, setDateRange] = useState<DateRange>('month');
  
  // Example data for revenue chart
  const revenueData = {
    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
    datasets: [
      {
        label: 'Revenue',
        data: [125000, 167000, 145000, 195000, 210000, 250000],
        borderColor: '#4F46E5',
        backgroundColor: 'rgba(79, 70, 229, 0.1)',
        fill: true,
      },
      {
        label: 'Target',
        data: [150000, 150000, 150000, 200000, 200000, 200000],
        borderColor: '#94A3B8',
        borderDash: [5, 5],
        fill: false,
        pointRadius: 0,
      },
    ],
  };
  
  // Example data for department distribution
  const departmentData = {
    labels: ['IT', 'Marketing', 'Sales', 'HR', 'Finance'],
    datasets: [
      {
        label: 'Headcount',
        data: [65, 40, 25, 15, 10],
        backgroundColor: [
          '#4F46E5', // Primary
          '#7C3AED', // Purple
          '#0EA5E9', // Sky
          '#10B981', // Emerald
          '#F59E0B', // Amber
        ],
      },
    ],
  };
  
  // Example formatting function for currency
  const formatCurrency = (value: string | number): string => {
    return new Intl.NumberFormat('en-US', {
      style: 'currency',
      currency: 'USD',
      maximumFractionDigits: 0,
    }).format(Number(value));
  };

  return (
    <div className="p-6 space-y-6">
      <div className="flex justify-between items-center">
        <h2 className="text-xl font-bold text-gray-900">Dashboard Analytics</h2>
        <DateRangeSelector value={dateRange} onChange={setDateRange} />
      </div>
      
      {/* KPI Cards */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        <KpiCard
          title="Total Revenue"
          value={980000}
          previousValue={850000}
          percentageChange={15.3}
          formatter={formatCurrency}
          chart={<Sparkline data={[150000, 167000, 180000, 195000, 210000, 250000]} color="#4F46E5" />}
        />
        <KpiCard
          title="Employees"
          value={155}
          previousValue={140}
          percentageChange={10.7}
          chart={<Sparkline data={[120, 125, 130, 140, 145, 155]} color="#10B981" />}
        />
        <KpiCard
          title="Active Projects"
          value={24}
          previousValue={28}
          percentageChange={-14.3}
          chart={<Sparkline data={[30, 28, 25, 26, 25, 24]} color="#F43F5E" />}
        />
        <KpiCard
          title="Average Margin"
          value={41.2}
          previousValue={39.8}
          percentageChange={3.5}
          formatter={(value) => `${value}%`}
          chart={<Sparkline data={[38, 39, 40, 39.5, 40.3, 41.2]} color="#F59E0B" />}
        />
      </div>
      
      {/* Line Chart */}
      <div className="bg-white p-6 rounded-lg border shadow-sm">
        <h3 className="text-lg font-medium text-gray-900 mb-4">Revenue Performance</h3>
        <LineChart
          data={revenueData}
          height={350}
          options={{
            plugins: {
              tooltip: {
                callbacks: {
                  label: function(context) {
                    let label = context.dataset.label || '';
                    if (label) {
                      label += ': ';
                    }
                    label += formatCurrency(context.parsed.y);
                    return label;
                  }
                }
              }
            }
          }}
        />
      </div>
      
      {/* Chart Grid */}
      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <div className="bg-white p-6 rounded-lg border shadow-sm">
          <h3 className="text-lg font-medium text-gray-900 mb-4">Department Distribution</h3>
          <div className="flex justify-center items-center h-80">
            <Chart 
              type="doughnut" 
              data={departmentData}
              options={{
                plugins: {
                  legend: {
                    position: 'right',
                  }
                },
                cutout: '60%',
              }}
            />
          </div>
        </div>
        
        <div className="bg-white p-6 rounded-lg border shadow-sm">
          <h3 className="text-lg font-medium text-gray-900 mb-4">Top Projects by Revenue</h3>
          <BarChart
            data={{
              labels: ['Project A', 'Project B', 'Project C', 'Project D', 'Project E'],
              datasets: [
                {
                  label: 'Revenue',
                  data: [85000, 72000, 68000, 54000, 48000],
                  backgroundColor: '#4F46E5',
                },
              ],
            }}
            height={350}
            horizontal={true}
            options={{
              indexAxis: 'y',
              plugins: {
                legend: {
                  display: false,
                },
                tooltip: {
                  callbacks: {
                    label: function(context) {
                      return formatCurrency(context.parsed.x);
                    }
                  }
                }
              },
            }}
          />
        </div>
      </div>
    </div>
  );
};
```

## Các Task liên quan
- FE-INFRA-001: Project Setup (prerequisite)
- FE-INFRA-004: Theme System (prerequisite)
- FE-DSH-001: Dashboard Page (dependent)
- FE-DSH-002: HR metrics widgets (dependent)
- FE-DSH-003: Revenue/Margin widgets (dependent)
- FE-MGN-004: Margin Chart components (dependent)

## Tiêu chí chấp nhận
- Chart.js (hoặc thư viện tương đương) được tích hợp đúng cách
- Line, Bar, Pie/Donut và Area charts hoạt động với dữ liệu test
- Chart styling phù hợp với design system
- Charts responsive trên các kích thước màn hình khác nhau
- KPI cards hiển thị đúng các metrics, trends, và phần trăm thay đổi
- Sparklines trong KPI cards render đúng
- Chart controls (date range, filters) hoạt động đúng
- Loading states và error handling được xử lý phù hợp
- Performance tốt, không gây lag khi hiển thị nhiều charts
- Documentation đầy đủ về cách sử dụng và tùy chỉnh chart components
- Unit tests cho các chart components 