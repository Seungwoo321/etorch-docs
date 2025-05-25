# E-Torch 프론트엔드 아키텍처

## 1. 아키텍처 개요

### 1.1 E-Torch 특화 제약사항

| 도메인 특성 | 기술적 제약 | 구현 방법 |
|------------|------------|----------|
| **KOSIS/ECOS 통합** | 이기종 API + 주기 차이 | 어댑터 패턴 + UI 제한 |
| **1000+ 데이터 포인트** | 메모리 200MB 제한 | LTTB 다운샘플링 |
| **구독 모델** | 권한 검증 < 10ms | 클라이언트 캐시 5분 |
| **7가지 위젯 타입** | 차트 5개 + 텍스트 2개 | 팩토리 패턴 |
| **토스페이먼츠 빌링** | 자동 갱신 구독 | PaymentWidget SDK |
| **WCAG 2.1 AA** | 차트 접근성 | 데이터 테이블 + aria-label |

### 1.2 핵심 성능 목표

| 기능 | 목표값 | 측정 기준 |
|------|--------|----------|
| 차트 렌더링 | < 3초 | 1000포인트 시계열 |
| 편집 반응성 | 60fps | 드래그 200ms + 리사이즈 300ms |
| 메모리 사용량 | < 200MB | 대시보드당 |
| 권한 검증 | < 10ms | 플랜별 기능 제한 |
| 터치 타겟 | 44×44px | 모든 인터랙티브 요소 |

## 2. 기술 스택

### 2.1 핵심 기술 (정확한 버전)

| 영역 | 기술 | 버전 | E-Torch 특화 설정 |
|------|------|------|------------------|
| **모노레포** | Turborepo + pnpm | 2.5.3 + 10.11.0 | 9패키지 분할 |
| **프레임워크** | Next.js + React | 15.3.2 + 19.1.0 | App Router + useOptimistic |
| **UI** | Tailwind CSS + Shadcn/UI | 4.1.7 + latest | CSS-first, OKLCH 색상 |
| **상태관리** | Zustand + TanStack Query | 5.0.5 + 5.77.0 | 서버/클라이언트 분리 |
| **차트** | Recharts | 2.15.3 | LTTB + 7가지 위젯 |
| **레이아웃** | react-grid-layout | 1.5.1 | 300ms/200ms 디바운싱 |
| **인증** | Supabase Auth | v2 | SNS 3개 + 구독 모델 |
| **결제** | 토스페이먼츠 | latest | PaymentWidget + 빌링키 |

### 2.2 Tailwind CSS 4 레이아웃 설정

```css
@theme {
  /* 반응형 헤더 */
  --header-desktop: 80px; --header-tablet: 72px; --header-mobile: 64px;
  /* 툴바 */
  --toolbar-desktop: 64px; --toolbar-tablet: 56px;
  /* 사이드바 */
  --sidebar-width: 200px; --sidebar-collapsed: 60px;
  /* 속성 패널 */
  --property-panel-width: 320px; --property-panel-min: 280px;
  /* 위젯 최소 크기 */
  --widget-min-desktop: 300px 200px; --widget-min-tablet: 250px 180px;
  /* 성능 최적화 */
  --debounce-resize: 300ms; --debounce-drag: 200ms;
  /* 터치 최적화 */
  --touch-target: 44px; --touch-spacing: 8px;
  /* E-Torch 브랜드 색상 (OKLCH) */
  --color-primary: oklch(0.2 0.15 240);
  --color-secondary: oklch(0.5 0.2 230);
  --color-tertiary: oklch(0.45 0.18 220);
}
```

## 3. 아키텍처 계층 구조

### 3.1 서버/클라이언트 컴포넌트 분리

| 컴포넌트 유형 | 책임 | 구현 방식 |
|-------------|------|----------|
| **서버 컴포넌트** | 권한 확인, 메타데이터, 데이터 페칭 | App Router RSC |
| **클라이언트 컴포넌트** | 차트 렌더링, 편집, 상태 관리 | "use client" |
| **서버 액션** | 결제, 구독 관리, 데이터 변경 | "use server" |

### 3.2 데이터 소스 제약사항

| 데이터 소스 | 현재 상태 | 지원 주기 | 제한사항 | UI 표시 |
|------------|----------|----------|----------|--------|
| **KOSIS** | ✅ 완전 지원 | M, Q, A | 무제한 기간 | 기본 선택 |
| **ECOS** | ✅ 완전 지원 | D, M, Q, A | 일별은 1년만 | 주기별 제한 |
| **OECD** | 🚧 향후 확장 | 미정 | 현재 비활성화 | 비활성화 + 툴팁 |

```typescript
// 데이터 소스 설정 (현재 + 향후)
export const DATA_SOURCE_CONFIG = {
  KOSIS: {
    id: 'kosis',
    name: '통계청 KOSIS',
    status: 'active',
    supportedPeriods: ['M', 'Q', 'A'] as const,
    maxHistoryYears: null, // 무제한
    apiEndpoint: '/api/kosis',
    indicatorCount: { basic: 12, pro: 12 }
  },
  ECOS: {
    id: 'ecos', 
    name: '한국은행 ECOS',
    status: 'active',
    supportedPeriods: ['D', 'M', 'Q', 'A'] as const,
    maxHistoryYears: { D: 1, M: null, Q: null, A: null }, // 일별만 1년 제한
    apiEndpoint: '/api/ecos',
    indicatorCount: { basic: 8, pro: 28 }
  },
  OECD: {
    id: 'oecd',
    name: 'OECD 통계',
    status: 'planned', // 현재 미지원
    supportedPeriods: [], // 향후 정의
    maxHistoryYears: null,
    apiEndpoint: '/api/oecd', // 준비만
    indicatorCount: { basic: 0, pro: 0 },
    plannedRelease: '2025-Q3' // 예상 출시일
  }
} as const

// UI에서 사용할 데이터 소스 필터링
export const getAvailableDataSources = () => {
  return Object.entries(DATA_SOURCE_CONFIG)
    .filter(([_, config]) => config.status === 'active')
    .map(([key, config]) => ({ key, ...config }))
}

// 지표 검색 시 소스별 필터링
export const getIndicatorsBySource = (source: keyof typeof DATA_SOURCE_CONFIG, plan: 'basic' | 'pro') => {
  const config = DATA_SOURCE_CONFIG[source]
  if (config.status !== 'active') return []
  
  const maxCount = config.indicatorCount[plan]
  return indicators.filter(indicator => 
    indicator.source === source
  ).slice(0, maxCount)
}
```

### 3.3 7가지 위젯 시스템

| 위젯 타입 | 데이터 소스 필요 | 구현 클래스 |
|----------|----------------|------------|
| time-series | ✅ | TimeSeriesWidget |
| bar-chart | ✅ | BarChartWidget |
| scatter-chart | ✅ | ScatterChartWidget |
| radar-chart | ✅ | RadarChartWidget |
| radial-bar-chart | ✅ | RadialBarChartWidget |
| text-custom | ❌ | TextCustomWidget |
| text-data | ✅ (단일) | TextDataWidget |

## 4. 모노레포 구조 (9패키지)

### 4.1 패키지 의존성

```
core (타입, 상수)
├── utils (LTTB, 포맷터)
├── ui (Shadcn/UI + 워터마크)
├── data-sources (KOSIS/ECOS 어댑터)
│   └── state (Zustand + TanStack Query)
│       └── charts (7가지 위젯)
│           └── dashboard (편집 + 레이아웃)
├── server-api (결제 + 인증)
└── eslint-config (접근성 규칙)
```

### 4.2 핵심 패키지 책임

| 패키지 | 주요 책임 | 핵심 export |
|--------|----------|-------------|
| @e-torch/core | 타입, 상수, 구독 모델 | PLAN_LIMITS, 위젯 타입 |
| @e-torch/charts | LTTB, 7가지 위젯 | useLTTBSampling |
| @e-torch/ui | 워터마크, 접근성 | AccessibleChart |
| @e-torch/data-sources | KOSIS/ECOS 어댑터 | useDataSource |

## 5. 성능 최적화

### 5.1 핵심 성능 목표

| 지표 | 목표값 | 측정 방법 | 구현 방법 |
|------|--------|----------|----------|
| **LCP** | < 2.5초 | Web Vitals API | 스켈레톤 UI + 지연 로딩 |
| **FID** | < 100ms | Web Vitals API | debounce 300ms/200ms |
| **CLS** | < 0.1 | Web Vitals API | 스켈레톤 UI 크기 고정 |
| **INP** | < 200ms | Web Vitals API | 편집 중 차트 렌더링 비활성화 |
| **차트 렌더링** | < 2초 | Performance API | LTTB 1000+ 임계값 |
| **대시보드 로딩** | < 2초 | 사용자 타이밍 | Suspense + 점진적 로딩 |
| **단일 지표 조회** | < 500ms | API 응답 모니터링 | SWR 캐싱 15분 |
| **메모리 사용량** | < 200MB | Performance API | 위젯 언마운트 시 정리 |

```typescript
// 성능 임계값 상수
export const PERFORMANCE_THRESHOLDS = {
  LCP: 2500,     // ms
  FID: 100,      // ms (deprecated 2024+)
  INP: 200,      // ms (FID 대체 지표 2024+)
  CLS: 0.1,      // score
  CHART_RENDER: 2000, // ms
  DASHBOARD_LOAD: 2000, // ms
  API_RESPONSE: 500,    // ms
  MEMORY_LIMIT: 200,    // MB
} as const

// 디바운싱 시간
export const DEBOUNCE_TIMES = {
  DRAG: 200,    // ms - 빠른 반응성
  RESIZE: 300,  // ms - 정확성 우선
  SEARCH: 300,  // ms - 검색 입력
} as const
```

### 5.2 react-grid-layout 최적화

| 설정 | 값 | 목적 |
|------|----|----|
| onDragStop | debounce 200ms | 빠른 반응성 |
| onResizeStop | debounce 300ms | 정확성 우선 |
| 터치 타겟 | 44×44px | 모바일 최적화 |
| 그리드 컬럼 | 12/8/4 (데스크톱/태블릿/모바일) | 반응형 |

### 5.3 차트 렌더링 최적화

| 조건 | 임계값 | 최적화 방법 |
|------|--------|------------|
| 데이터 포인트 | 1000+ | LTTB 다운샘플링 |
| 위젯 개수 | 20+ | react-window 가상화 |
| 편집 모드 | 드래그/리사이즈 중 | 차트 렌더링 비활성화 |
| 메모리 사용 | 200MB+ | 컴포넌트 언마운트 시 정리 |

## 6. 구독 모델 + 토스페이먼츠

### 6.1 권한 검증 (< 10ms)

```typescript
// 5분 캐시로 권한 검증 최적화
export const useAuthWithCache = () => {
  const { data: session } = useQuery({
    queryKey: ['auth-session'],
    queryFn: () => supabase.auth.getSession(),
    staleTime: 5 * 60 * 1000, // 5분 캐시
  })
  return { user: session?.user, plan: session?.user?.subscription_plan || 'basic' }
}
```

### 6.2 플랜별 제한

| 기능 | Basic (무료) | Pro (유료) | 기술 구현 |
|------|-------------|-----------|----------|
| **대시보드 수** | 3개 | 무제한 | DB 제약 + UI 진행바 |
| **위젯 수** | 6개/대시보드 | 무제한 | react-grid-layout 제한 |
| **경제지표** | 20개 (MVP) | 40개 (전체) | API 권한 검증 |
| **데이터 기간** | 최근 3년 | 전체 기간 | DatePicker 비활성화 |
| **워터마크** | "E-Torch로 제작됨" | 제거 가능 | CSS 오버레이 |
| **대시보드 복사** | 불가 | 가능 | 버튼 비활성화 |
| **임베드 코드** | 불가 | 가능 | Pro 전용 기능 |
| **내보내기 품질** | 워터마크 포함 | 고해상도 | Canvas 렌더링 설정 |

```typescript
// 권한 검증 상수
export const PLAN_LIMITS = {
  basic: {
    dashboards: 3,
    widgets: 6,
    indicators: 20,
    dataYears: 3,
    watermark: true,
    copyDashboard: false,
    embedCode: false
  },
  pro: {
    dashboards: Infinity,
    widgets: Infinity,
    indicators: 40,
    dataYears: Infinity,
    watermark: false,
    copyDashboard: true,
    embedCode: true
  }
} as const
```

### 6.3 토스페이먼츠 결제

```typescript
// PaymentWidget 기본 설정
const paymentWidget = PaymentWidget(clientKey, user.id)
paymentWidget.renderPaymentMethods('#payment-methods', {
  value: 9900 // Pro 월간 9,900원
})
```

## 7. 워터마크 + 접근성

### 7.1 워터마크 시스템

| 플랜 | 워터마크 표시 | 내보내기 | 구현 위치 |
|------|--------------|----------|----------|
| **Basic** | "E-Torch로 제작됨" 우하단 고정 | 포함 | CSS 오버레이 |
| **Pro** | 제거 가능 옵션 | 제거 가능 | 조건부 렌더링 |

```typescript
// 워터마크 컴포넌트
interface WatermarkProps {
  show: boolean
  onExport?: boolean // 내보내기 시에만 표시
}

const Watermark = ({ show, onExport = false }: WatermarkProps) => {
  if (!show) return null
  
  return (
    <div 
      className={cn(
        "absolute bottom-2 right-2 z-50",
        "bg-black/10 px-2 py-1 rounded text-xs text-gray-600",
        onExport && "print:block", // 내보내기 시에만 출력
        !onExport && "print:hidden" // 일반 화면에서는 숨김
      )}
    >
      E-Torch로 제작됨
    </div>
  )
}

// 사용 예시
const DashboardView = () => {
  const { plan } = useSubscription()
  const showWatermark = plan === 'basic'
  
  return (
    <div className="relative">
      {/* 대시보드 콘텐츠 */}
      <DashboardGrid />
      
      {/* 워터마크 */}
      <Watermark show={showWatermark} />
    </div>
  )
}

// 내보내기 시 워터마크 설정
export const exportDashboard = async (format: 'png' | 'pdf') => {
  const { plan } = useSubscription()
  const includeWatermark = plan === 'basic'
  
  const canvas = await html2canvas(dashboardRef.current, {
    // Basic 플랜: 워터마크 포함, 0.8 품질
    // Pro 플랜: 워터마크 제거 가능, 1.0 품질
    scale: plan === 'pro' ? 2 : 1.6, // 고해상도
    useCORS: true,
    backgroundColor: '#ffffff'
  })
  
  // Pro 플랜에서 워터마크 제거 옵션 제공
  if (plan === 'pro' && !includeWatermark) {
    // 워터마크 없는 버전으로 내보내기
  }
}
```

### 7.2 WCAG 2.1 AA 접근성

| 요구사항 | 구현 방법 | 검증 도구 |
|----------|----------|----------|
| 차트 대체 텍스트 | aria-label + 데이터 테이블 | axe-core |
| 키보드 네비게이션 | tabindex, Arrow키 지원 | 수동 테스트 |
| 색상 대비 4.5:1 | OKLCH 기반 자동 검증 | Colour Contrast Analyser |
| 터치 타겟 44×44px | min-w/h-[44px] 클래스 | 시각적 확인 |

## 8. 모바일 최적화

### 8.1 터치 인터페이스

| 설정 | 값 | 적용 범위 |
|------|----|---------|
| 터치 타겟 | 44×44px | 모든 버튼, 링크 |
| 터치 간격 | 8px | 인접 요소 |
| 스와이프 감지 | 100px 이동 | 대시보드 네비게이션 |
| 편집 제한 | 드래그만 지원 | 리사이즈는 속성 패널 |

### 8.2 반응형 레이아웃

| 화면 크기 | 그리드 | 위젯 크기 | 편집 기능 |
|----------|-------|---------|---------|
| 데스크톱 1200px+ | 12컬럼 | 300×200px | 드래그 + 리사이즈 |
| 태블릿 768-1199px | 8컬럼 | 250×180px | 드래그 + 리사이즈 |
| 모바일 ~767px | 4컬럼 | 100% 너비 | 드래그만 |

### 8.3 모바일 편집 제한사항

| 화면 크기 | 드래그 | 리사이즈 | 구현 방법 |
|----------|-------|---------|----------|
| **데스크톱 1200px+** | ✅ 지원 | ✅ 핸들 | react-grid-layout 기본 |
| **태블릿 768-1199px** | ✅ 지원 | ✅ 핸들 | 터치 최적화 핸들 |
| **모바일 ~767px** | ✅ 지원 | ❌ 속성 패널만 | 풀스크린 모달 |

```typescript
// 모바일 편집 제한 구현
const useResponsiveGridProps = () => {
  const isMobile = useMediaQuery("(max-width: 767px)")
  
  return {
    isDraggable: true, // 모든 화면에서 드래그 지원
    isResizable: !isMobile, // 모바일에서만 리사이즈 비활성화
    
    // 모바일용 대체 리사이즈 (속성 패널)
    ...(isMobile && {
      onLayoutChange: (layout) => {
        // 모바일에서는 드래그로만 위치 변경
        // 크기는 속성 패널의 프리셋에서만 변경
      }
    })
  }
}

// 모바일 속성 패널 - 크기 조절 프리셋
const MOBILE_WIDGET_SIZES = {
  small: { w: 4, h: 2 },   // 4컬럼 전체 너비
  medium: { w: 4, h: 3 },  // 높이만 증가
  large: { w: 4, h: 4 },   // 정사각형
} as const
```

## 9. 개발 도구 설정

### 9.1 ESLint 접근성

```json
{
  "extends": ["plugin:jsx-a11y/recommended"],
  "rules": {
    "jsx-a11y/alt-text": "error",
    "jsx-a11y/aria-label": "error"
  }
}
```

### 9.2 성능 모니터링 목표

| 지표 | 목표값 | 임계값 초과 시 |
|------|--------|-------------|
| LCP | < 2.5초 | 경고 로그 |
| FID | < 100ms | 경고 로그 |
| CLS | < 0.1 | 경고 로그 |
