# E-Torch 디자인 시스템 가이드

## 1. 개요

E-Torch 디자인 시스템은 경제지표 대시보드 서비스의 일관되고 효율적인 UI 개발을 위한 가이드라인과 컴포넌트 라이브러리입니다. 본 문서는 개발팀이 일관된 코드베이스를 유지하기 위한 규약과 패턴을 제공합니다.

### 1.1 목적

- UI 컴포넌트의 일관된 구현 및 사용 방식 정의
- 디자인 토큰과 시각적 언어의 표준화
- 효율적인 개발 워크플로우 제공
- 접근성 및 성능 최적화 기준 설정

### 1.2 기술 스택

- **CSS 프레임워크**: Tailwind CSS 4
- **UI 컴포넌트 라이브러리**: Shadcn/UI
- **차트 라이브러리**: Recharts
- **아이콘**: Lucide React
- **폰트**: Inter (UI), JetBrains Mono (데이터/코드)
- **테마**: next-themes
- **유틸리티**: class-variance-authority, clsx, tailwind-merge

## 2. 프로젝트 구조

### 2.1 UI 패키지 구조

UI 패키지의 표준 파일 구조입니다:

```
packages/ui/
├── src/
│   ├── components/        # 클라이언트 컴포넌트
│   │   ├── a11y/          # 접근성 관련 컴포넌트
│   │   ├── chart/         # 차트 컴포넌트
│   │   ├── dashboard/     # 대시보드 컴포넌트
│   │   ├── data/          # 데이터 관련 컴포넌트
│   │   ├── layout/        # 레이아웃 컴포넌트
│   │   ├── ui/            # 기본 UI 컴포넌트 (Shadcn)
│   │   └── feedback/      # 알림, 토스트 등 피드백 컴포넌트
│   ├── server-components/ # 서버 컴포넌트 래퍼
│   ├── hooks/             # 패키지 내부 훅
│   ├── lib/               # 유틸리티 함수
│   └── styles/            # 전역 스타일 및 테마
├── index.ts               # 클라이언트 컴포넌트 엔트리 포인트
├── server.ts              # 서버 컴포넌트 엔트리 포인트
└── components.json        # Shadcn UI 컴포넌트 설정

```

### 2.2 문서화 앱 구조 (Storybook)

```
apps/docs/
├── .storybook/            # Storybook 설정
│   ├── main.ts            # 메인 설정
│   └── preview.ts         # 미리보기 설정
├── stories/
│   ├── ui/                # 기본 UI 컴포넌트 문서
│   ├── chart/             # 차트 컴포넌트 문서
│   ├── dashboard/         # 대시보드 컴포넌트 문서
│   └── intro.stories.mdx  # 소개 문서
└── package.json
```

### 2.3 필수 파일 목록

#### 컴포넌트 파일 (src/components/*)

**기본 UI 컴포넌트**

- `ui/accordion.tsx`
- `ui/alert-dialog.tsx`
- `ui/alert.tsx`
- `ui/avatar.tsx`
- `ui/badge.tsx`
- `ui/button.tsx`
- `ui/calendar.tsx`
- `ui/card.tsx`
- `ui/checkbox.tsx`
- `ui/color-picker.tsx`
- `ui/command.tsx`
- `ui/data-table.tsx`
- `ui/date-picker.tsx`
- `ui/dialog.tsx`
- `ui/dropdown-menu.tsx`
- `ui/form.tsx`
- `ui/input.tsx`
- `ui/label.tsx`
- `ui/popover.tsx`
- `ui/progress.tsx`
- `ui/radio-group.tsx`
- `ui/scroll-area.tsx`
- `ui/search-input.tsx`
- `ui/select.tsx`
- `ui/separator.tsx`
- `ui/sheet.tsx`
- `ui/skeleton.tsx`
- `ui/slider.tsx`
- `ui/switch.tsx`
- `ui/table.tsx`
- `ui/tabs.tsx`
- `ui/textarea.tsx`
- `ui/toast.tsx`
- `ui/toggle.tsx`
- `ui/tooltip.tsx`

**차트 컴포넌트**

- `chart/time-series-chart.tsx`
- `chart/bar-chart.tsx`
- `chart/scatter-chart.tsx`
- `chart/radar-chart.tsx`
- `chart/radial-bar-chart.tsx`
- `chart/chart-options-panel.tsx`
- `chart/chart-legend.tsx`
- `chart/accessible-chart-table.tsx`
- `chart/chart-tooltip.tsx`

**대시보드 컴포넌트**

- `dashboard/dashboard-grid.tsx`
- `dashboard/chart-item.tsx`
- `dashboard/text-item.tsx`
- `dashboard/kpi-item.tsx`
- `dashboard/time-range-control.tsx`
- `dashboard/period-selector.tsx`
- `dashboard/dashboard-controls.tsx`

**데이터 컴포넌트**

- `data/data-query-builder.tsx`
- `data/source-selector.tsx`
- `data/indicator-selector.tsx`
- `data/transform-controls.tsx`
- `data/data-preview.tsx`

**레이아웃 컴포넌트**

- `layout/root-layout.tsx`
- `layout/header.tsx`
- `layout/sidebar.tsx`
- `layout/container.tsx`
- `layout/grid.tsx`

**접근성 컴포넌트**

- `a11y/skip-link.tsx`
- `a11y/visually-hidden.tsx`
- `a11y/error-boundary.tsx`
- `a11y/focus-trap.tsx`

**피드백 컴포넌트**

- `feedback/toast-provider.tsx`
- `feedback/loading-spinner.tsx`
- `feedback/error-message.tsx`

#### 서버 컴포넌트 래퍼 (src/server-components/*)

- `button.server.tsx`
- `card.server.tsx`
- `input.server.tsx`
- `select.server.tsx`
- `chart/time-series-chart.server.tsx`
- `dashboard/dashboard-grid.server.tsx`

#### 유틸리티 및 훅 (src/lib/, src/hooks/)

- `lib/utils.ts`
- `lib/format.ts`
- `lib/chart-utils.ts`
- `hooks/use-debounce.ts`
- `hooks/use-media-query.ts`
- `hooks/use-toast.ts`
- `hooks/use-click-outside.ts`
- `hooks/use-local-storage.ts`
- `hooks/use-mounted.ts`

#### 스타일 (src/styles/)

- `styles/globals.css`

#### 타입 정의 (src/types/)

- `types/chart.ts`
- `types/dashboard.ts`
- `types/data.ts`
- `types/theme.ts`

## 3. 디자인 원칙

### 3.1 컴포넌트 디자인 원칙

1. **명확성(Clarity)**: 복잡한 경제 데이터를 명확하게 전달하는 것이 최우선
2. **일관성(Consistency)**: 시각적 언어와 상호작용 패턴은 전체 애플리케이션에서 일관되게 적용
3. **효율성(Efficiency)**: 사용자가 최소한의 단계로 원하는 정보를 찾고 분석할 수 있도록 설계
4. **적응성(Adaptability)**: 전문가와 일반 사용자 모두에게 적합한 유연한 인터페이스 제공
5. **신뢰성(Reliability)**: 데이터 표현의 정확성과 일관성 보장

### 3.2 코드 작성 원칙

1. **타입 안전성**: 모든 컴포넌트와 함수는 TypeScript 타입 정의를 포함해야 함
2. **합성 가능성**: 컴포넌트는 작고 재사용 가능하게 설계되어야 함
3. **접근성**: WCAG 2.1 AA 기준을 충족하도록 구현
4. **성능 최적화**: 대시보드와 차트 컴포넌트는 메모이제이션 등의 최적화 기법 적용
5. **문서화**: 모든 컴포넌트는 JSDoc으로 문서화되어야 함

## 4. 컴포넌트 작성 가이드라인

### 4.1 기본 컴포넌트 템플릿

모든 클라이언트 컴포넌트는 다음 구조를 따라야 합니다:

```tsx
"use client" // 클라이언트 컴포넌트 지시어

import * as React from "react"
import { cn } from "@e-torch/ui/lib/utils"

// 타입은 core 패키지에서 임포트
import { ComponentNameProps } from "@e-torch/core"

export function ComponentName({
  children,
  className,
  ...props
}: ComponentNameProps) {
  // 구현
  return (
    <div className={cn("기본-클래스", className)} {...props}>
      {children}
    </div>
  );
}
```

### 4.2 서버 컴포넌트 래퍼 템플릿

서버 컴포넌트 래퍼는 다음 구조를 따라야 합니다:

```tsx
// "use client" 지시어 없음

import { ComponentName } from "../components/component-name";
import { cn } from "../lib/utils";

// 타입은 core 패키지에서 임포트
import { ComponentNameProps } from "@e-torch/core";

export function ComponentNameServer({
  children,
  className,
  ...props
}: ComponentNameProps) {
  return (
    <ComponentName className={className} {...props}>
      {children}
    </ComponentName>
  );
}
```

### 4.3 UI 컴포넌트 가져오기 (Shadcn)

기본 UI 컴포넌트는 Shadcn UI에서 CLI를 통해 가져와 사용합니다:

```bash
# 컴포넌트 추가 예시
npx shadcn-ui@latest add button
npx shadcn-ui@latest add card
npx shadcn-ui@latest add dropdown-menu
```

가져온 컴포넌트는 필요 시 다음과 같이 수정할 수 있습니다:

```tsx
// packages/ui/src/components/ui/button.tsx 

// 원본 Shadcn 컴포넌트 코드...

// 추가 변형 예시
const buttonVariants = cva(
  "inline-flex items-center justify-center...",
  {
    variants: {
      variant: {
        // 기존 변형...
        
        // 추가 변형
        success: "bg-success text-success-foreground hover:bg-success/90",
        warning: "bg-warning text-warning-foreground hover:bg-warning/90",
      },
      // 기타 variants...
    },
  }
)
```

### 4.4 Props 인터페이스 가이드라인

Props 인터페이스는 `packages/core`에 정의하며 다음 규칙을 따릅니다:

1. 모든 컴포넌트는 `className` prop을 포함해야 함
2. 필요한 경우 HTML 속성 확장 (예: `React.HTMLAttributes<HTMLElement>`)
3. Props 타입 이름은 `ComponentNameProps` 형식 사용
4. 선택적 속성은 `?`로 표시

```tsx
// packages/core/src/types/ui.ts
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: "default" | "destructive" | "outline" | "secondary" | "ghost" | "link" | "success" | "warning" | "info";
  size?: "default" | "sm" | "lg" | "icon";
  asChild?: boolean;
}

// packages/core/src/types/chart.ts
export interface ChartProps {
  data: Array<Record<string, any>>;
  xAxisKey: string;
  series: Array<ChartSeries>;
  height?: number | string;
  className?: string;
}

export interface ChartSeries {
  dataKey: string;
  name?: string;
  color?: string;
  type?: 'line' | 'area' | 'bar';
  yAxisId?: 'left' | 'right';
}
```

## 5. 스타일 가이드라인

### 5.1 Tailwind CSS 사용 패턴

1. **유틸리티 우선 접근 방식**: 인라인 클래스 우선 사용
2. **컴포넌트 클래스 추출**: 반복되는 패턴은 `cn()` 유틸리티로 추출
3. **명명된 그룹 사용**: 관련 유틸리티를 그룹화하여 가독성 향상

```tsx
// 좋은 예시
<div className={cn(
  "rounded-lg border bg-card p-4", // 카드 기본 스타일
  "transition-shadow hover:shadow-md", // 상호작용
  isActive && "ring-2 ring-primary", // 조건부 스타일
  className
)}>
  {children}
</div>
```

### 5.2 Tailwind CSS 전역 설정

`globals.css`에 정의된 기본 변수와 테마:

```css
:root {
  /* E-Torch 브랜드 색상 */
  --primary: hsl(221 83% 15%); /* #0c1e3e */
  --primary-foreground: hsl(210 40% 98%);
  --secondary: hsl(217 83% 45%); /* #1a56db */
  --secondary-foreground: hsl(210 40% 98%);
  --tertiary: hsl(200 97% 39%); /* #0284c7 */
  --tertiary-foreground: hsl(210 40% 98%);
  
  /* 차트 색상 */
  --chart-1: hsl(216 60% 49%); /* #1b3a6b */
  --chart-2: hsl(209 83% 47%); /* #1c7ed6 */
  --chart-3: hsl(164 80% 39%); /* #12b886 */
  --chart-4: hsl(258 94% 78%); /* #9775fa */
  --chart-5: hsl(27 98% 54%); /* #fd7e14 */
  --chart-6: hsl(142 52% 50%); /* #40c057 */
  --chart-7: hsl(339 84% 58%); /* #e64980 */
  
  /* 반경 */
  --radius: 0.6rem;
}

/* 다크 모드 색상 변수는 .dark 클래스에 정의 */
.dark {
  --primary: hsl(217.2 91.2% 59.8%); /* #4173e0 */
  --primary-foreground: hsl(222.2 47.4% 11.2%);
  /* ... 더 많은 다크 모드 변수 */
}
```

### 5.3 컴포넌트별 스타일 가이드

#### 기본 컴포넌트

- 모든 기본 컴포넌트는 Shadcn/UI 스타일 가이드를 따름
- `cva()`를 사용하여 변형(variants) 정의
- 루트 엘리먼트는 항상 `className` prop을 받아야 함

#### 차트 컴포넌트

- 일관된 색상 팔레트 사용 (`--chart-1` ~ `--chart-7`)
- 챠트 크기는 컨테이너 크기에 반응형으로 적용
- 접근성을 위한 대체 텍스트 및 키보드 네비게이션 지원

#### 대시보드 컴포넌트

- 그리드 레이아웃 일관성 유지
- 카드 기반 UI 패턴 적용
- 반응형 설계로 모바일 지원

## 6. 색상 체계

### 6.1 브랜드 색상

| 이름 | 값 | CSS 변수 | 용도 |
|------|------|---------|------|
| Primary | `#0c1e3e` | `--primary` | 주요 강조, 헤더, 주 버튼 |
| Secondary | `#1a56db` | `--secondary` | 보조 강조, 호버 상태, 부 버튼 |
| Tertiary | `#0284c7` | `--tertiary` | 추가 강조, 링크, 특수 요소 |

### 6.2 중립 색상

| 이름 | 값 | CSS 변수 | 용도 |
|------|------|---------|------|
| Background | `#ffffff` | `--background` | 페이지 배경 |
| Surface | `#f8fafc` | `--card` | 카드, 모달 배경 |
| Surface Alt | `#f1f5f9` | `--accent` | 대체 표면, 셀렉션 |
| Border | `#e2e8f0` | `--border` | 경계선, 구분선 |
| Text Primary | `#0f172a` | `--foreground` | 주요 텍스트 |
| Text Secondary | `#334155` | `--muted-foreground` | 보조 텍스트 |

### 6.3 상태 색상

| 이름 | 값 | CSS 변수 | 용도 |
|------|------|---------|------|
| Success | `#059669` | `--success` | 긍정적 값, 증가 |
| Warning | `#d97706` | `--warning` | 주의 필요, 경고 |
| Danger | `#dc2626` | `--destructive` | 오류, 부정적 값, 감소 |
| Info | `#2563eb` | `--info` | 정보성 메시지 |

### 6.4 차트 색상

차트 시리즈를 위한 일관된 색상 팔레트:

| 이름 | 값 | CSS 변수 |
|------|------|---------|
| Chart 1 | `#1b3a6b` | `--chart-1` |
| Chart 2 | `#1c7ed6` | `--chart-2` |
| Chart 3 | `#12b886` | `--chart-3` |
| Chart 4 | `#9775fa` | `--chart-4` |
| Chart 5 | `#fd7e14` | `--chart-5` |
| Chart 6 | `#40c057` | `--chart-6` |
| Chart 7 | `#e64980` | `--chart-7` |

## 7. 타이포그래피

### 7.1 폰트 패밀리

- **기본 폰트**: Inter (UI 요소, 내비게이션, 헤더)
- **모노스페이스 폰트**: JetBrains Mono (데이터, 코드, 수치값)

### 7.2 타입 스케일

| 이름 | 크기 / 라인 높이 | 가중치 | CSS 클래스 | 용도 |
|------|-----------------|--------|-----------|------|
| Display | 36px / 1.2 | 700 | `text-4xl font-bold leading-tight` | 대시보드 제목 |
| H1 | 28px / 1.3 | 700 | `text-3xl font-bold leading-tight` | 섹션 제목 |
| H2 | 24px / 1.35 | 600 | `text-2xl font-semibold leading-tight` | 위젯 그룹 제목 |
| H3 | 20px / 1.4 | 600 | `text-xl font-semibold leading-snug` | 위젯 제목 |
| H4 | 18px / 1.45 | 600 | `text-lg font-semibold leading-snug` | 카드 제목 |
| Body | 16px / 1.5 | 400 | `text-base font-normal leading-normal` | 본문 텍스트 |
| Small | 14px / 1.5 | 400 | `text-sm font-normal leading-normal` | 보조 텍스트 |
| XSmall | 12px / 1.5 | 400 | `text-xs font-normal leading-normal` | 캡션, 도움말 |
| Data Value | 16px / 1.4 | 600 | `text-base font-semibold font-mono leading-tight` | 주요 지표값 |

## 8. 접근성 가이드라인

### 8.1 기본 요구사항

- 모든 컴포넌트는 WCAG 2.1 AA 수준을 준수해야 함
- 키보드 네비게이션 지원
- 스크린 리더 호환성
- 충분한 색상 대비

### 8.2 차트 접근성

차트 컴포넌트는 다음 접근성 기능을 구현해야 합니다:

1. 데이터를 설명하는 대체 텍스트 제공
2. 스크린 리더를 위한 데이터 테이블 제공 (`AccessibleChartTable` 컴포넌트 사용)
3. 키보드로 데이터 포인트 탐색 가능
4. 색상만으로 정보를 구분하지 않음 (패턴, 라벨 등 함께 사용)

### 8.3 포커스 관리

- 모든 상호작용 요소는 키보드 포커스 표시기를 가져야 함
- 모달 및 다이얼로그는 포커스 트랩 구현
- 키보드 단축키는 접근성 지침을 준수해야 함

## 9. 성능 최적화 가이드라인

### 9.1 컴포넌트 최적화

- React.memo 적절히 사용
- 불필요한 리렌더링 방지
- 상태 지역화

```tsx
// 최적화된 컴포넌트 예시
import * as React from "react"
import { memo, useMemo } from "react"

// 메모이제이션 패턴 적용
export const OptimizedChart = memo(function OptimizedChart({
  data,
  ...props
}: ChartProps) {
  // 대용량 데이터 처리를 위한 메모이제이션
  const processedData = useMemo(() => {
    // 데이터 처리 로직
    return data
  }, [data])
  
  return (
    // 차트 렌더링...
  )
})
```

### 9.2 대용량 데이터 처리

- 데이터 가상화 (긴 목록)
- 데이터 다운샘플링 (시계열 차트)
- 지연 로딩 (필요한 경우만 데이터 로드)

### 9.3 이미지 최적화

- 적절한 이미지 형식 사용 (SVG 선호)
- 지연 로딩 적용
- 반응형 이미지 사용

## 10. 테스트 전략

### 10.1 컴포넌트 테스트

모든 컴포넌트는 다음 테스트를 포함해야 합니다:

1. 렌더링 테스트
2. 프롭 변경에 따른 동작 테스트
3. 이벤트 핸들러 테스트
4. 접근성 테스트

```tsx
// 테스트 예시 구조
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Button } from '../button'

describe('Button', () => {
  it('renders correctly', () => {
    // 렌더링 테스트
  })

  it('handles click events', async () => {
    // 이벤트 핸들러 테스트
  })

  it('supports different variants', () => {
    // 프롭 변경 테스트
  })

  it('is accessible', () => {
    // 접근성 테스트
  })
})
```

### 10.2 테스트 파일 구조

- 테스트 파일은 컴포넌트와 동일한 디렉토리에 위치
- 파일명은 `컴포넌트명.test.tsx` 형식
- 테스트는 디렉토리별로 그룹화 (`__tests__` 디렉토리 사용)

## 11. 코드 품질 기준

### 11.1 ESLint 규칙

모든 코드는 다음 ESLint 규칙을 준수해야 합니다:

- Next.js 기본 규칙
- TypeScript 엄격 규칙
- React Hooks 규칙
- 접근성 규칙

### 11.2 네이밍 컨벤션

- 컴포넌트: PascalCase (예: `Button`, `TimeSeriesChart`)
- 파일명: kebab-case (예: `button.tsx`, `time-series-chart.tsx`)
- 훅: camelCase, `use` 접두사 (예: `useMediaQuery`, `useDebounce`)
- 유틸리티 함수: camelCase (예: `formatNumber`, `clsx`)
- 타입/인터페이스: PascalCase (예: `ButtonProps`, `ChartOptions`)

### 11.3 코드 포맷팅

- ESLint 사용

## 12. 컴포넌트 문서화 (Storybook)

### 12.1 Storybook 문서화 규칙

모든 컴포넌트는 `apps/docs` 내의 Storybook을 통해 문서화합니다:

```tsx
// apps/docs/stories/chart/time-series-chart.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { TimeSeriesChart } from '@e-torch/ui';

// 메타데이터 정의
const meta: Meta<typeof TimeSeriesChart> = {
  title: 'Chart/TimeSeriesChart',
  component: TimeSeriesChart,
  tags: ['autodocs'],
  parameters: {
    layout: 'centered',
  },
  argTypes: {
    data: { control: 'object' },
    xAxisKey: { control: 'text' },
    series: { control: 'object' },
    height: { control: 'text' },
  },
};

export default meta;
type Story = StoryObj<typeof TimeSeriesChart>;

// 기본 사용 예시
export const Default: Story = {
  args: {
    data: [
      { date: '2023-01', value: 100 },
      { date: '2023-02', value: 120 },
      { date: '2023-03', value: 90 },
      // ...
    ],
    xAxisKey: 'date',
    series: [
      {
        dataKey: 'value',
        name: 'GDP 성장률',
        color: 'var(--chart-1)'
      }
    ],
    height: 300,
  },
};

// 변형 예시
export const WithMultipleSeries: Story = {
  args: {
    // 다중 시리즈 데이터
  },
};

export const WithSecondaryAxis: Story = {
  args: {
    // 보조 축 예시
  },
};
```

### 12.2 문서화 템플릿

각 컴포넌트 스토리는 다음 섹션을 포함해야 합니다:

1. **기본 예시**: 가장 일반적인 사용 방법
2. **Props 변형**: 다양한 props 조합 예시
3. **반응형 동작**: 다양한 화면 크기에서의 동작
4. **사용 가이드라인**: 컴포넌트의 권장 사용 방법과 제약사항

### 12.3 문서화 템플릿 (MDX)

복잡한 컴포넌트는 MDX 문서로 추가 설명을 제공할 수 있습니다:

```mdx
// apps/docs/stories/chart/time-series-chart.mdx
import { Canvas, Meta, Story } from '@storybook/blocks';
import * as TimeSeriesChartStories from './time-series-chart.stories';

<Meta of={TimeSeriesChartStories} />

# 타임 시리즈 차트

타임 시리즈 데이터를 시각화하기 위한 차트 컴포넌트입니다.

## 기본 사용법

<Canvas of={TimeSeriesChartStories.Default} />

## 다중 시리즈

<Canvas of={TimeSeriesChartStories.WithMultipleSeries} />

## 사용 가이드라인

- 시간 경과에 따른 데이터 트렌드를 보여줄 때 사용합니다.
- 최대 5개 이하의 시리즈를 사용하는 것이 권장됩니다.
- 큰 데이터 세트의 경우 자동 다운샘플링이 적용됩니다.

## 접근성 고려사항

- 차트는 시각적 정보를 대체하는 텍스트 설명을 제공합니다.
- 키보드 사용자도 데이터 포인트를 탐색할 수 있습니다.
```

## 13. 버전 관리 및 릴리스

### 13.1 버전 관리 체계

시맨틱 버저닝을 따릅니다:

- **Major (X.0.0)**: 호환성이 깨지는 큰 변경
- **Minor (0.X.0)**: 기능 추가, 호환성 유지
- **Patch (0.0.X)**: 버그 수정, 사소한 변경

### 13.2 Changelog 관리

모든 변경사항은 CHANGELOG.md에 다음 형식으로 기록합니다:

```md
# 변경 기록

## [1.1.0] - 2025-05-20
### 추가
- 레이더 차트 컴포넌트 추가

### 변경
- 버튼 컴포넌트 스타일 개선

### 수정
- 모바일 디바이스에서 대시보드 그리드 레이아웃 문제 수정
```

## 부록: 참고 자료

- [Tailwind CSS 4 문서](https://tailwindcss.com/)
- [Shadcn/UI 컴포넌트 문서](https://ui.shadcn.com/)
- [Recharts 문서](https://recharts.org/en-US/)
- [WCAG 2.1 AA 지침](https://www.w3.org/WAI/WCAG21/quickref/?currentsidebar=%23col_customize&levels=aaa)
