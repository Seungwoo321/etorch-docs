# E-Torch 디자인 시스템 가이드

## 1. 개요

E-Torch 디자인 시스템은 경제지표 대시보드 서비스의 일관되고 효율적인 UI 개발을 위한 가이드라인과 컴포넌트 라이브러리입니다. 본 문서는 개발팀이 일관된 코드베이스를 유지하기 위한 규약과 패턴을 제공합니다.

### 1.1 목적

이 디자인 시스템 문서의 주요 목적은 다음과 같습니다:

- **일관된 사용자 경험**: 모든 페이지와 기능에서 일관된 모양과 동작을 보장합니다
- **개발 효율성**: Shadcn/UI를 사용하여 컴포넌트를 재사용하고, 불필요한 중복 코드를 방지합니다
- **스타일 가이드**: 색상, 타이포그래피, 간격 등의 디자인 토큰을 표준화합니다
- **접근성 준수**: WCAG 2.1 AA 기준을 충족하는 접근성 높은 인터페이스를 보장합니다
- **확장 가능한 디자인**: 새로운 기능과 컴포넌트가 추가되어도 일관성을 유지할 수 있도록 합니다
- **테마 시스템**: 라이트 모드와 다크 모드를 지원하는 테마 시스템을 제공합니다

### 1.2 기술 스택

- **CSS 프레임워크**: Tailwind CSS 4
- **UI 컴포넌트 라이브러리**: Shadcn/UI
- **차트 라이브러리**: Recharts 2.15.3+
- **아이콘**: Lucide React 1.0.0+
- **폰트**: Inter (UI), JetBrains Mono (데이터/코드)
- **테마**: next-themes
- **유틸리티**: class-variance-authority, clsx, tailwind-merge

## 2. 파일 구조

```
packages/ui/
├── src/
│   ├── components/        # 클라이언트 컴포넌트
│   │   ├── a11y/          # 접근성 관련 컴포넌트
│   │   ├── feedback/      # 알림, 토스트 등 피드백 컴포넌트
│   │   ├── layout/        # 레이아웃 컴포넌트
│   │   └── ui/            # 기본 UI 컴포넌트 (Shadcn)
│   ├── server-components/ # 클라이언트 컴포넌트의 서버 래퍼
│   ├── hooks/             # 패키지 내부 훅
│   ├── lib/               # 유틸리티 함수
│   └── styles/            # 전역 스타일 및 테마
│       └── globals.css    # 전역 스타일 설정
├── index.ts               # 클라이언트 컴포넌트 엔트리 포인트
└── server.ts              # 서버 컴포넌트 엔트리 포인트
```

## 3. Tailwind CSS 4 설정

Tailwind CSS 4에서는 JS 설정 파일(`tailwind.config.js`)을 사용하지 않고, CSS 파일에서 모든 설정을 관리합니다.

### 3.1 globals.css 설정

`packages/ui/src/styles/globals.css` 파일을 다음과 같이 작성합니다:

```css
@import "tailwindcss";
@source "../../../apps/**/*.{ts,tsx}";
@source "../../../packages/**/*.{ts,tsx}";
@source "../**/*.{ts,tsx}";

@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

:root {
  /* E-Torch 브랜드 색상 - 라이트 모드 */
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --popover: oklch(1 0 0);
  --popover-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --secondary: oklch(0.97 0 0);
  --secondary-foreground: oklch(0.205 0 0);
  --muted: oklch(0.97 0 0);
  --muted-foreground: oklch(0.556 0 0);
  --accent: oklch(0.97 0 0);
  --accent-foreground: oklch(0.205 0 0);
  --destructive: oklch(0.577 0.245 27.325);
  --destructive-foreground: oklch(0.577 0.245 27.325);
  --border: oklch(0.922 0 0);
  --input: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
  
  /* 차트 색상 */
  --chart-1: oklch(0.646 0.222 41.116);
  --chart-2: oklch(0.6 0.118 184.704);
  --chart-3: oklch(0.398 0.07 227.392);
  --chart-4: oklch(0.828 0.189 84.429);
  --chart-5: oklch(0.769 0.188 70.08);
  
  /* 반경 */
  --radius: 0.625rem;
  
  /* 사이드바 색상 */
  --sidebar: oklch(0.985 0 0);
  --sidebar-foreground: oklch(0.145 0 0);
  --sidebar-primary: oklch(0.205 0 0);
  --sidebar-primary-foreground: oklch(0.985 0 0);
  --sidebar-accent: oklch(0.97 0 0);
  --sidebar-accent-foreground: oklch(0.205 0 0);
  --sidebar-border: oklch(0.922 0 0);
  --sidebar-ring: oklch(0.708 0 0);
}

.dark {
  /* E-Torch 브랜드 색상 - 다크 모드 */
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  --card: oklch(0.145 0 0);
  --card-foreground: oklch(0.985 0 0);
  --popover: oklch(0.145 0 0);
  --popover-foreground: oklch(0.985 0 0);
  --primary: oklch(0.985 0 0);
  --primary-foreground: oklch(0.205 0 0);
  --secondary: oklch(0.269 0 0);
  --secondary-foreground: oklch(0.985 0 0);
  --muted: oklch(0.269 0 0);
  --muted-foreground: oklch(0.708 0 0);
  --accent: oklch(0.269 0 0);
  --accent-foreground: oklch(0.985 0 0);
  --destructive: oklch(0.396 0.141 25.723);
  --destructive-foreground: oklch(0.637 0.237 25.331);
  --border: oklch(0.269 0 0);
  --input: oklch(0.269 0 0);
  --ring: oklch(0.556 0 0);
  
  /* 차트 색상 - 다크 모드 */
  --chart-1: oklch(0.488 0.243 264.376);
  --chart-2: oklch(0.696 0.17 162.48);
  --chart-3: oklch(0.769 0.188 70.08);
  --chart-4: oklch(0.627 0.265 303.9);
  --chart-5: oklch(0.645 0.246 16.439);
  
  /* 사이드바 색상 - 다크 모드 */
  --sidebar: oklch(0.205 0 0);
  --sidebar-foreground: oklch(0.985 0 0);
  --sidebar-primary: oklch(0.488 0.243 264.376);
  --sidebar-primary-foreground: oklch(0.985 0 0);
  --sidebar-accent: oklch(0.269 0 0);
  --sidebar-accent-foreground: oklch(0.985 0 0);
  --sidebar-border: oklch(0.269 0 0);
  --sidebar-ring: oklch(0.439 0 0);
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
  --color-chart-1: var(--chart-1);
  --color-chart-2: var(--chart-2);
  --color-chart-3: var(--chart-3);
  --color-chart-4: var(--chart-4);
  --color-chart-5: var(--chart-5);
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
  --color-sidebar: var(--sidebar);
  --color-sidebar-foreground: var(--sidebar-foreground);
  --color-sidebar-primary: var(--sidebar-primary);
  --color-sidebar-primary-foreground: var(--sidebar-primary-foreground);
  --color-sidebar-accent: var(--sidebar-accent);
  --color-sidebar-accent-foreground: var(--sidebar-accent-foreground);
  --color-sidebar-border: var(--sidebar-border);
  --color-sidebar-ring: var(--sidebar-ring);
}

@layer base {
  * {
    @apply border-border outline-ring/50;
  }
  body {
    @apply bg-background text-foreground;
  }
}
```

### 3.2 설정 설명

1. **임포트 및 소스 설정**
   - `@import "tailwindcss"`: Tailwind CSS의 기본 스타일을 가져옵니다.
   - `@source` 지시문: 컴포넌트 파일을 스캔하여 사용된 클래스를 추출합니다.
   - `@import "tw-animate-css"`: 애니메이션 관련 스타일을 가져옵니다.

2. **다크 모드 설정**
   - `@custom-variant dark (&:is(.dark *))`: `.dark` 클래스가 있는 요소 내부의 모든 요소에 다크 모드 스타일을 적용합니다.

3. **색상 변수 정의**
   - `:root` 선택자: 라이트 모드 변수를 정의합니다.
   - `.dark` 선택자: 다크 모드 변수를 정의합니다.
   - OKLCH 색상 공간을 사용하여 더 넓은 색 영역과 인지적으로 균일한 밝기를 제공합니다.

4. **테마 변수 매핑**
   - `@theme inline` 블록: CSS 변수를 Tailwind 테마 속성에 매핑합니다.

5. **기본 스타일 설정**
   - `@layer base` 블록: 모든 요소에 테두리 색상과 아웃라인을 적용하고, body 요소에 배경색과 텍스트 색상을 적용합니다.

## 4. 타이포그래피

E-Torch는 다음 두 가지 폰트 패밀리를 사용합니다:

1. **Inter**: UI 요소, 내비게이션, 헤더용 기본 폰트
2. **JetBrains Mono**: 데이터, 코드, 수치값을 위한 모노스페이스 폰트

### 4.1 타입 스케일

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

### 4.2 폰트 적용 방법

```tsx
// apps/web/app/layout.tsx
import { Inter, JetBrains_Mono } from 'next/font/google';
import '@e-torch/ui/styles/globals.css';

const inter = Inter({ 
  subsets: ['latin'],
  variable: '--font-inter'
});

const jetBrainsMono = JetBrains_Mono({
  subsets: ['latin'],
  variable: '--font-jetbrains-mono'
});

export default function RootLayout({ children }) {
  return (
    <html lang="ko" className={`${inter.variable} ${jetBrainsMono.variable}`}>
      <body>
        {children}
      </body>
    </html>
  );
}
```

## 5. 색상 체계

### 5.1 브랜드 색상

| 이름 | 라이트 모드 | 다크 모드 | 용도 |
|------|-----------|----------|------|
| Primary | `oklch(0.205 0 0)` | `oklch(0.985 0 0)` | 주요 강조, 헤더, 주 버튼 |
| Secondary | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 보조 강조, 호버 상태, 부 버튼 |
| Accent | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 추가 강조, 특수 요소 |

### 5.2 중립 색상

| 이름 | 라이트 모드 | 다크 모드 | 용도 |
|------|-----------|----------|------|
| Background | `oklch(1 0 0)` | `oklch(0.145 0 0)` | 페이지 배경 |
| Foreground | `oklch(0.145 0 0)` | `oklch(0.985 0 0)` | 주요 텍스트 |
| Card | `oklch(1 0 0)` | `oklch(0.145 0 0)` | 카드, 모달 배경 |
| Muted | `oklch(0.97 0 0)` | `oklch(0.269 0 0)` | 음소거된 배경 |
| Border | `oklch(0.922 0 0)` | `oklch(0.269 0 0)` | 경계선, 구분선 |

### 5.3 차트 색상

E-Torch는 차트에 일관된 색상 팔레트를 사용합니다:

| 이름 | 라이트 모드 | 다크 모드 |
|------|-----------|----------|
| Chart 1 | `oklch(0.646 0.222 41.116)` | `oklch(0.488 0.243 264.376)` |
| Chart 2 | `oklch(0.6 0.118 184.704)` | `oklch(0.696 0.17 162.48)` |
| Chart 3 | `oklch(0.398 0.07 227.392)` | `oklch(0.769 0.188 70.08)` |
| Chart 4 | `oklch(0.828 0.189 84.429)` | `oklch(0.627 0.265 303.9)` |
| Chart 5 | `oklch(0.769 0.188 70.08)` | `oklch(0.645 0.246 16.439)` |

## 6. 컴포넌트 패턴

### 6.1 기본 컴포넌트 패턴

모든 클라이언트 컴포넌트는 다음 구조를 따라야 합니다:

```tsx
"use client" // 클라이언트 컴포넌트 지시어

import * as React from "react"

// 타입은 core 패키지에서 임포트
import type { ComponentNameProps } from "@e-torch/core"

export function ComponentName({
  children,
  className,
  ...props
}: ComponentNameProps) {
  // 구현
  return (
    <div className={`기본-클래스 ${className}`} {...props}>
      {children}
    </div>
  );
}
```

### 6.2 서버 컴포넌트 래퍼 패턴

서버 컴포넌트 래퍼는 다음 구조를 따라야 합니다:

```tsx
// "use client" 지시어 없음

import { ComponentName } from "../components/component-name";

// 타입은 core 패키지에서 임포트
import type { ComponentNameProps } from "@e-torch/core";

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

### 6.3 버튼 컴포넌트 예시

```tsx
// packages/ui/src/components/ui/button.tsx
"use client"

import * as React from "react"

// 타입은 core 패키지에서 임포트
import type { ButtonProps } from "@e-torch/core"

const buttonVariants = {
  variants: {
    variant: {
      default: "bg-primary text-primary-foreground hover:bg-primary/90",
      destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
      outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
      secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/90",
      ghost: "hover:bg-accent hover:text-accent-foreground",
      link: "text-primary underline-offset-4 hover:underline",
    },
    size: {
      default: "h-10 px-4 py-2",
      sm: "h-9 rounded-md px-3",
      lg: "h-11 rounded-md px-8",
      icon: "h-10 w-10",
    },
  },
  defaultVariants: {
    variant: "default",
    size: "default",
  }
};

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant = "default", size = "default", asChild = false, ...props }, ref) => {
    return (
      <button
        className={`inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50 ${buttonVariants.variants.variant[variant]} ${buttonVariants.variants.size[size]} ${className || ""}`}
        ref={ref}
        {...props}
      />
    )
  }
)
Button.displayName = "Button"

export { Button, buttonVariants }
```

## 7. 접근성 가이드라인

### 7.1 기본 요구사항

- 모든 컴포넌트는 WCAG 2.1 AA 수준을 준수해야 함
- 키보드 네비게이션 지원
- 스크린 리더 호환성
- 충분한 색상 대비

### 7.2 접근성 컴포넌트

#### 7.2.1 스킵 링크

```tsx
// packages/ui/src/components/a11y/skip-link.tsx
"use client"

import * as React from "react"

export function SkipLink({ targetId }: { targetId: string }) {
  return (
    <a 
      href={`#${targetId}`} 
      className="sr-only focus:not-sr-only focus:absolute focus:top-0 focus:left-0 focus:z-50 focus:p-4 focus:bg-white focus:text-primary"
    >
      콘텐츠로 건너뛰기
    </a>
  );
}
```

#### 7.2.2 시각적으로 숨겨진 텍스트

```tsx
// packages/ui/src/components/a11y/visually-hidden.tsx
"use client"

import * as React from "react"

export function VisuallyHidden({ children }: { children: React.ReactNode }) {
  return (
    <span className="sr-only">
      {children}
    </span>
  );
}
```

#### 7.2.3 포커스 트랩

```tsx
// packages/ui/src/components/a11y/focus-trap.tsx
"use client"

import * as React from "react"
import { useRef, useEffect } from "react"

export function FocusTrap({ 
  children,
  active = true
}: { 
  children: React.ReactNode; 
  active?: boolean 
}) {
  const containerRef = useRef<HTMLDivElement>(null)
  
  useEffect(() => {
    if (!active) return
    
    const container = containerRef.current
    if (!container) return
    
    const focusableElements = container.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    )
    
    const firstElement = focusableElements[0] as HTMLElement
    const lastElement = focusableElements[focusableElements.length - 1] as HTMLElement
    
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return
      
      if (e.shiftKey) {
        if (document.activeElement === firstElement) {
          lastElement.focus()
          e.preventDefault()
        }
      } else {
        if (document.activeElement === lastElement) {
          firstElement.focus()
          e.preventDefault()
        }
      }
    }
    
    container.addEventListener('keydown', handleKeyDown)
    return () => container.removeEventListener('keydown', handleKeyDown)
  }, [active])
  
  return <div ref={containerRef}>{children}</div>
}
```

#### 7.2.4 키보드 내비게이션 메뉴

```tsx
// packages/ui/src/components/a11y/keyboard-nav-menu.tsx
"use client"

import * as React from "react"
import { useRef, useState, useEffect } from "react"

interface KeyboardNavMenuProps {
  children: React.ReactNode
  vertical?: boolean
}

export function KeyboardNavMenu({ 
  children, 
  vertical = true 
}: KeyboardNavMenuProps) {
  const menuRef = useRef<HTMLDivElement>(null)
  const [focusIndex, setFocusIndex] = useState<number>(-1)
  
  useEffect(() => {
    const menu = menuRef.current
    if (!menu) return
    
    const menuItems = Array.from(
      menu.querySelectorAll('[role="menuitem"]')
    ) as HTMLElement[]
    
    const handleKeyDown = (e: KeyboardEvent) => {
      const key = e.key
      
      if (
        (vertical && (key === 'ArrowUp' || key === 'ArrowDown')) ||
        (!vertical && (key === 'ArrowLeft' || key === 'ArrowRight'))
      ) {
        e.preventDefault()
        
        const direction = 
          key === 'ArrowDown' || key === 'ArrowRight' ? 1 : -1
        
        let newIndex = focusIndex + direction
        
        if (newIndex < 0) {
          newIndex = menuItems.length - 1
        } else if (newIndex >= menuItems.length) {
          newIndex = 0
        }
        
        setFocusIndex(newIndex)
        menuItems[newIndex]?.focus()
      }
    }
    
    menu.addEventListener('keydown', handleKeyDown)
    return () => menu.removeEventListener('keydown', handleKeyDown)
  }, [vertical, focusIndex])
  
  return (
    <div 
      ref={menuRef} 
      role="menu" 
      className={`outline-none ${vertical ? 'flex-col' : 'flex-row'}`}
    >
      {children}
    </div>
  )
}
```

## 8. 사용 예시

### 8.1 간단한 카드 컴포넌트

```tsx
// packages/ui/src/components/ui/card.tsx
"use client"

import * as React from "react"

const Card = React.forwardRef
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={`rounded-lg border bg-card p-4 shadow-sm ${className || ""}`}
    {...props}
  />
))
Card.displayName = "Card"

const CardHeader = React.forwardRef
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={`flex flex-col space-y-1.5 p-4 ${className || ""}`}
    {...props}
  />
))
CardHeader.displayName = "CardHeader"

const CardTitle = React.forwardRef
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLHeadingElement>
>(({ className, ...props }, ref) => (
  <h3
    ref={ref}
    className={`text-xl font-semibold leading-snug tracking-tight ${className || ""}`}
    {...props}
  />
))
CardTitle.displayName = "CardTitle"

const CardContent = React.forwardRef
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div ref={ref} className={`p-4 pt-0 ${className || ""}`} {...props} />
))
CardContent.displayName = "CardContent"

const CardFooter = React.forwardRef
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={`flex items-center p-4 pt-0 ${className || ""}`}
    {...props}
  />
))
CardFooter.displayName = "CardFooter"

export { Card, CardHeader, CardFooter, CardTitle, CardContent }
```

### 8.2 대시보드 카드 사용 예시

```tsx
// apps/web/app/(dashboard)/dashboard/page.tsx
import { Card, CardHeader, CardTitle, CardContent } from "@e-torch/ui"
import { Button } from "@e-torch/ui"

export default function DashboardPage() {
  return (
    <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
      <Card>
        <CardHeader>
          <CardTitle>GDP 성장률</CardTitle>
        </CardHeader>
        <CardContent>
          <p className="text-2xl font-semibold text-chart-1">2.6%</p>
          <p className="text-xs text-muted-foreground">전년 대비</p>
        </CardContent>
      </Card>
      
      <Card>
        <CardHeader>
          <CardTitle>소비자물가 등락률</CardTitle>
        </CardHeader>
        <CardContent>
          <p className="text-2xl font-semibold text-chart-2">3.2%</p>
          <p className="text-xs text-muted-foreground">전년 동월 대비</p>
        </CardContent>
      </Card>
      
      <Card>
        <CardHeader>
          <CardTitle>기준금리</CardTitle>
        </CardHeader>
        <CardContent>
          <p className="text-2xl font-semibold text-chart-3">3.5%</p>
          <p className="text-xs text-muted-foreground">한국은행</p>
        </CardContent>
      </Card>
    </div>
  )
}
```

### 8.3 접근성 차트 테이블 적용 예시

```tsx
// packages/charts/src/components/AccessibleChartTable.tsx
"use client"

import * as React from "react"

export interface AccessibleChartTableProps {
  data: Array<Record<string, any>>;
  columns: Array<{
    key: string;
    label: string;
  }>;
  summary: string;
}

export function AccessibleChartTable({ 
  data, 
  columns, 
  summary 
}: AccessibleChartTableProps) {
  return (
    <div className="sr-only">
      <table>
        <caption>{summary}</caption>
        <thead>
          <tr>
            {columns.map(column => (
              <th key={column.key} scope="col">{column.label}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {data.map((row, i) => (
            <tr key={i}>
              {columns.map(column => (
                <td key={column.key}>{row[column.key]}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}
```

## 9. 코드 스타일 가이드

E-Torch 프로젝트는 ESLint 9 버전의 Standard 규칙을 사용하여 코드 스타일을 관리합니다:

### 9.1 ESLint 설정

프로젝트 루트의 설정을 따르며, 다음과 같은 규칙을 적용합니다:

- StandardJS 및 StandardJSX 규칙 준수
- TypeScript ESLint 권장 규칙 준수
- React Hooks 규칙 준수

### 9.2 코드 포맷팅 가이드

- 들여쓰기: 2칸 공백
- 세미콜론: 사용하지 않음
- 따옴표: 작은따옴표 사용
- 중괄호 스타일: 같은 줄에 열고 닫기
- 최대 줄 길이: 100자
- 컴포넌트 이름: PascalCase
- 함수 이름: camelCase
- 상수 이름: UPPER_SNAKE_CASE

ESLint 9 버전에서는 코드 포맷 관련 설정도 포함되므로 별도의 Prettier 설정은 필요하지 않습니다.

## 10. 결론

E-Torch 디자인 시스템은 Tailwind CSS 4를 기반으로 경제지표 대시보드 서비스에 필요한 모든 UI 컴포넌트를 일관되고 접근성이 높은 방식으로 제공합니다. 이 가이드를 따라 개발하면 일관된 사용자 경험과 효율적인 개발 프로세스를 달성할 수 있습니다.

주요 특징:

- Tailwind CSS 4의 CSS 기반 설정을 활용한 테마 관리
- 서버/클라이언트 컴포넌트 분리 패턴
- 접근성을 고려한 컴포넌트 설계
- OKLCH 색상 공간을 활용한 향상된 색상 시스템
- 일관된 컴포넌트 패턴
