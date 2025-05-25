# E-Torch 프론트엔드 아키텍처 설계 문서

## 1. 아키텍처 개요

### 1.1 E-Torch 특화 제약사항

| 도메인 특성 | 기술적 제약 | 구현 방법 |
|------------|------------|----------|
| **KOSIS/ECOS 통합** | 이기종 API 응답 + 주기 차이 | 어댑터 패턴 + 주기별 UI 제한 |
| **1000+ 데이터 포인트** | 메모리 200MB 제한 | LTTB 다운샘플링 |
| **구독 모델** | 권한 검증 < 10ms | 클라이언트 캐시 5분 |
| **정기 발표 데이터** | 정확성 > 실시간성 | 24시간 캐시 |
| **7가지 위젯 타입** | 차트 5개 + 텍스트 2개 | 팩토리 패턴 |
| **WCAG 2.1 AA** | 차트 접근성 | 데이터 테이블 + aria-label |

### 1.2 핵심 성능 목표

| 기능 | 목표값 | 측정 기준 |
|------|--------|----------|
| 차트 렌더링 | < 3초 | 1000포인트 시계열 |
| 편집 반응성 | 60fps | 드래그(200ms) + 리사이즈(300ms) |
| 메모리 사용량 | < 200MB | 대시보드당 |
| 권한 검증 | < 10ms | 플랜별 기능 제한 |
| 터치 타겟 | 44x44px | 모든 인터랙티브 요소 |

## 2. 기술 스택

### 2.1 핵심 기술 (E-Torch 특화 설정)

| 영역 | 기술 | 버전 | E-Torch 특화 설정 |
|------|------|------|------------------|
| **모노레포** | Turborepo + pnpm | 2.5.3 + 10.11.0 | 9패키지 분할 |
| **프레임워크** | Next.js + React | 15.3.2 + 19.1.0 | App Router + useOptimistic |
| **UI** | Tailwind CSS + Shadcn/UI | 4.1.7 + latest | CSS-first, OKLCH |
| **상태관리** | Zustand + TanStack Query | 5.0.5 + 5.77.0 | 서버/클라이언트 분리 |
| **차트** | Recharts | 2.15.3 | LTTB + 7가지 위젯 |
| **레이아웃** | react-grid-layout | 1.5.1 | 300ms/200ms 디바운싱 |
| **인증** | Supabase Auth | v2 | SNS 3개 + 구독 모델 |
| **접근성** | WCAG 2.1 AA | - | axe-core + 스크린 리더 |

### 2.2 Tailwind CSS 4 설정 (CSS-first)

```css
/* globals.css */
@import "tailwindcss";

@theme {
  /* E-Torch 브랜드 색상 (OKLCH) */
  --color-primary: oklch(0.2 0.15 240);
  --color-secondary: oklch(0.5 0.2 230);
  --color-tertiary: oklch(0.45 0.18 220);
  
  /* 성능 최적화 값 */
  --debounce-resize: 300ms;
  --debounce-drag: 200ms;
  
  /* 터치 타겟 */
  --touch-target-min: 44px;
  --touch-spacing: 8px;
}

/* OKLCH 미지원 브라우저 HSL 폴백 */
@supports not (color: oklch(0 0 0)) {
  :root { --color-primary: hsl(225, 60%, 15%); }
}
```

## 3. 아키텍처 계층 구조

### 3.1 서버/클라이언트 컴포넌트 분리

| 컴포넌트 유형 | 책임 | 구현 방식 |
|-------------|------|----------|
| **서버 컴포넌트** | 데이터 페칭, 메타데이터, 권한 확인 | Next.js 15 App Router |
| **클라이언트 컴포넌트** | 인터랙션, 상태 관리, 차트 렌더링 | "use client" |
| **서버 액션** | 데이터 변경, 구독 관리 | "use server" |

### 3.2 데이터 흐름

```
KOSIS/ECOS API → 어댑터 패턴 → TanStack Query → Zustand → 7가지 위젯 → UI
```

### 3.3 7가지 위젯 아키텍처

```typescript
// 위젯 팩토리 패턴
export const WIDGET_TYPES = {
  'time-series': TimeSeriesWidget,
  'bar-chart': BarChartWidget,
  'scatter-chart': ScatterChartWidget, 
  'radar-chart': RadarChartWidget,
  'radial-bar-chart': RadialBarChartWidget,
  'text-custom': TextCustomWidget,      // 사용자 정의 텍스트
  'text-data': TextDataWidget          // 데이터 기반 텍스트
} as const

// 데이터 소스 어댑터
export class KOSISAdapter extends BaseAdapter {
  getSupportedPeriods() { return ['M', 'Q', 'A'] }
}
export class ECOSAdapter extends BaseAdapter {
  getSupportedPeriods() { return ['D', 'M', 'Q', 'A'] }
}
```

## 4. 모노레포 패키지 구조

### 4.1 패키지 의존성 (9개)

| 패키지 | 의존성 | 핵심 책임 |
|--------|--------|----------|
| **core** | 없음 | 타입, 상수, 7가지 위젯 타입 |
| **utils** | 없음 | LTTB, 포맷터, 접근성 헬퍼 |
| **ui** | core, utils | Shadcn/UI + 워터마크 |
| **data-sources** | core, utils | KOSIS/ECOS 어댑터 + 주기 제한 |
| **state** | core, utils, data-sources | Zustand + TanStack Query |
| **charts** | core, ui, utils, data-sources | 7가지 위젯 + 접근성 |
| **dashboard** | 모든 패키지 | react-grid-layout + 편집 |
| **server-api** | core, utils | API 핸들러 + 구독 관리 |
| **eslint-config** | 없음 | Standard JS + jsx-a11y |

### 4.2 핵심 패키지 구현

```typescript
// Core: 구독 타입 + 위젯 타입
export type Plan = 'basic' | 'pro'
export type PlanLimits = {
  basic: { dashboards: 3, widgets: 6, indicators: 20, dataPeriod: '3years' }
  pro: { dashboards: -1, widgets: -1, indicators: 40, dataPeriod: 'all' }
}

export type WidgetType = 
  | 'time-series' | 'bar-chart' | 'scatter-chart' 
  | 'radar-chart' | 'radial-bar-chart'
  | 'text-custom' | 'text-data'

// Charts: 7가지 위젯 + LTTB
export const ChartRenderer = memo(({ data, type, options }) => {
  const sampledData = useLTTBSampling(data, 1000)
  return <AccessibleChart data={sampledData} type={type} {...options} />
})

// UI: 워터마크 시스템
export const WatermarkProvider = ({ plan, children }) => (
  <div className="relative">
    {children}
    {plan === 'basic' && (
      <div className="absolute bottom-2 right-2 text-xs text-gray-400 pointer-events-none">
        E-Torch로 제작됨
      </div>
    )}
  </div>
)
```

## 5. 성능 최적화

### 5.1 차트 렌더링 최적화 (정확한 수치)

| 최적화 대상 | 임계값 | 최적화 방법 | 디바운싱 | 성능 목표 |
|------------|--------|------------|----------|----------|
| 차트 렌더링 | 1000+ 포인트 | LTTB 다운샘플링 | - | < 3초 |
| 위젯 그리드 | 20개+ 위젯 | react-window 가상화 | - | 60fps |
| 리사이즈 편집 | 모든 위젯 | 차트 렌더링 비활성화 | **300ms** | < 16ms |
| 드래그 편집 | 모든 위젯 | 차트 렌더링 비활성화 | **200ms** | < 16ms |
| 데이터 로딩 | 지연 로딩 | Intersection Observer | - | LCP < 2.5s |

### 5.2 편집 모드 성능 (정확한 디바운싱)

```typescript
// react-grid-layout 최적화 (기획서 정확한 수치)
const gridLayoutProps = {
  onDragStart: () => setChartRenderingEnabled(false),
  onDragStop: debounce(() => setChartRenderingEnabled(true), 200),    // ✅ 200ms
  onResizeStart: () => setChartRenderingEnabled(false),
  onResizeStop: debounce(() => setChartRenderingEnabled(true), 300),  // ✅ 300ms
  
  // 터치 최적화
  margin: [16, 16],
  containerPadding: [12, 12], // 모바일 44x44px 터치 타겟 고려
}

// React 19 useOptimistic 활용
export const DashboardTitle = ({ dashboardId, title }) => {
  const [optimisticTitle, setOptimisticTitle] = useOptimistic(title)
  
  const updateTitle = async (formData: FormData) => {
    const newTitle = formData.get('title') as string
    setOptimisticTitle(newTitle) // 즉시 UI 업데이트
    await saveDashboardAction(dashboardId, { title: newTitle })
  }
  
  return <form action={updateTitle}>...</form>
}
```

### 5.3 메모리 관리

```typescript
// 편집 완료 시 메모리 정리
export const WidgetContainer = ({ widget }) => {
  useEffect(() => {
    return () => {
      cleanupWidgetListeners(widget.id)
      clearWidgetCache(widget.id)
    }
  }, [widget.id])
  
  return <AccessibleWidget {...widget} />
}
```

## 6. 구독 모델 + 접근성 구현

### 6.1 권한 검증 (< 10ms) + 세부 제한사항

```typescript
// 권한 게이트 시스템 (기획서 정확한 제한)
const FEATURE_GATES = {
  basic: { 
    indicators: 20,        // MVP 지표만
    dashboards: 3, 
    widgets: 6,           // 대시보드당
    dataPeriod: '3years', // 최근 3년만
    watermark: true,      // 워터마크 표시
    copy: false,          // 대시보드 복사 불가
    embed: false          // 임베드 코드 불가
  },
  pro: { 
    indicators: 40,       // 전체 지표
    dashboards: Infinity, 
    widgets: Infinity,
    dataPeriod: 'all',    // 전체 기간
    watermark: false,     // 워터마크 제거
    copy: true,           // 대시보드 복사 가능
    embed: true           // 임베드 코드 생성
  }
} as const

// 권한 훅 (5분 캐시)
export const useAuth = () => {
  const { data: session } = useQuery({
    queryKey: ['auth-session'],
    queryFn: () => supabase.auth.getSession(),
    staleTime: 5 * 60 * 1000 // 5분 캐시
  })
  
  const plan = session?.user?.user_metadata?.subscription_plan || 'basic'
  return { user: session?.user, plan, isAuthenticated: !!session?.user }
}

// 주기별 UI 제한 (KOSIS vs ECOS)
export const usePeriodOptions = (dataSource: 'KOSIS' | 'ECOS') => {
  const supportedPeriods = {
    KOSIS: ['M', 'Q', 'A'],      // 월, 분기, 연간
    ECOS: ['D', 'M', 'Q', 'A']   // 일, 월, 분기, 연간
  }
  
  return supportedPeriods[dataSource]
}
```

### 6.2 WCAG 2.1 AA 접근성 아키텍처

```typescript
// 차트 접근성 컴포넌트
export const AccessibleChart = ({ data, type, altText, ...props }) => {
  const summaryText = generateChartSummary(data) // "GDP 2020-2024, 최고 3.2%"
  
  return (
    <div role="img" aria-label={altText || summaryText}>
      <Chart data={data} type={type} {...props} />
      
      {/* 스크린 리더용 데이터 테이블 */}
      <table className="sr-only" aria-label="차트 데이터">
        <caption>{summaryText}</caption>
        <thead>
          <tr>
            <th>날짜</th>
            <th>값</th>
          </tr>
        </thead>
        <tbody>
          {data.map((item, index) => (
            <tr key={index}>
              <td>{item.date}</td>
              <td>{item.value}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}

// 터치 최적화 버튼 (44x44px)
export const TouchButton = ({ children, ...props }) => (
  <button 
    className="min-w-[44px] min-h-[44px] p-2 touch-manipulation"
    {...props}
  >
    {children}
  </button>
)

// 키보드 네비게이션
export const DashboardGrid = ({ widgets }) => {
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'ArrowRight') moveWidget('right')
    if (e.key === 'ArrowLeft') moveWidget('left')
    if (e.key === 'Delete') deleteWidget()
  }
  
  return (
    <div onKeyDown={handleKeyDown} role="grid" aria-label="대시보드 위젯">
      {widgets.map(widget => (
        <div key={widget.id} role="gridcell" tabIndex={0}>
          <AccessibleChart {...widget} />
        </div>
      ))}
    </div>
  )
}
```

### 6.3 Supabase Auth + 토스페이먼츠

```typescript
// 서버 액션: 구독 업그레이드
export async function upgradeSubscriptionAction(planType: 'pro') {
  const session = await getServerSession()
  if (!session) throw new Error('Unauthorized')
  
  const paymentSession = await createTossPayment({
    amount: planType === 'pro' ? 9900 : 0,
    orderId: generateOrderId(),
    customerEmail: session.user.email
  })
  
  return { success: true, data: paymentSession }
}

// 런타임 제한 적용
export async function createWidgetAction(dashboardId: string, widgetData: any) {
  const session = await getServerSession()
  const planLimits = await getUserPlanLimits(session.user.id)
  
  const currentWidgets = await getWidgetCount(dashboardId)
  if (planLimits.widgets !== -1 && currentWidgets >= planLimits.widgets) {
    throw new Error('Widget limit exceeded')
  }
  
  // 워터마크 적용
  const widget = await createWidget(dashboardId, {
    ...widgetData,
    showWatermark: planLimits.watermark
  })
  
  return { success: true, data: widget }
}
```

## 7. 모바일 + 접근성 통합

### 7.1 터치 인터페이스 최적화

```typescript
// 44x44px 터치 타겟 보장
export const MobileOptimizedGrid = () => (
  <GridLayout
    {...gridLayoutProps}
    // 모바일 터치 최적화
    margin={[12, 12]}
    containerPadding={[16, 16]}
    minH={3} // 최소 높이로 44px 이상 보장
    minW={2} // 최소 너비로 44px 이상 보장
  />
)

// 스와이프 제스처
export const useSwipeGesture = () => {
  const [touchStart, setTouchStart] = useState<number | null>(null)
  const [touchEnd, setTouchEnd] = useState<number | null>(null)

  const onTouchStart = (e: TouchEvent) => {
    setTouchEnd(null)
    setTouchStart(e.targetTouches[0].clientX)
  }

  const onTouchMove = (e: TouchEvent) => {
    setTouchEnd(e.targetTouches[0].clientX)
  }

  const onTouchEnd = () => {
    if (!touchStart || !touchEnd) return
    const distance = touchStart - touchEnd
    const isLeftSwipe = distance > 50
    const isRightSwipe = distance < -50

    if (isLeftSwipe) navigateNext()
    if (isRightSwipe) navigatePrev()
  }

  return { onTouchStart, onTouchMove, onTouchEnd }
}
```

### 7.2 스크린 리더 최적화

```typescript
// 동적 콘텐츠 알림
export const useAriaLiveAnnouncements = () => {
  const [announcement, setAnnouncement] = useState('')
  
  const announce = (message: string) => {
    setAnnouncement(message)
    setTimeout(() => setAnnouncement(''), 1000)
  }
  
  return {
    announcement,
    announce,
    AriaLiveRegion: () => (
      <div aria-live="polite" aria-atomic="true" className="sr-only">
        {announcement}
      </div>
    )
  }
}

// 포커스 관리
export const useFocusManagement = () => {
  const trapFocus = (containerRef: RefObject<HTMLElement>) => {
    const focusableElements = containerRef.current?.querySelectorAll(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    )
    
    const firstElement = focusableElements?.[0] as HTMLElement
    const lastElement = focusableElements?.[focusableElements.length - 1] as HTMLElement
    
    const handleTabKey = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return
      
      if (e.shiftKey) {
        if (document.activeElement === firstElement) {
          lastElement?.focus()
          e.preventDefault()
        }
      } else {
        if (document.activeElement === lastElement) {
          firstElement?.focus()
          e.preventDefault()
        }
      }
    }
    
    containerRef.current?.addEventListener('keydown', handleTabKey)
    return () => containerRef.current?.removeEventListener('keydown', handleTabKey)
  }
  
  return { trapFocus }
}
```
