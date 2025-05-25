# E-Torch 프론트엔드 아키텍처 설계 문서

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

### 2.2 Tailwind CSS 4 레이아웃 설정 (구체적 픽셀 수치)

```css
/* globals.css - E-Torch 레이아웃 시스템 */
@import "tailwindcss";

@theme {
  /* 반응형 헤더 높이 */
  --header-desktop: 80px;
  --header-tablet: 72px;
  --header-mobile: 64px;
  
  /* 반응형 툴바 높이 */
  --toolbar-desktop: 64px;
  --toolbar-tablet: 56px;
  
  /* 사이드바 */
  --sidebar-width: 200px;
  --sidebar-collapsed: 60px;
  
  /* 속성 패널 */
  --property-panel-width: 320px;
  --property-panel-min: 280px;
  --property-panel-max: 480px;
  
  /* 위젯 최소 크기 */
  --widget-min-desktop: 300px 200px;
  --widget-min-tablet: 250px 180px;
  
  /* 그리드 간격 */
  --grid-gap-desktop: 16px;
  --grid-gap-tablet: 12px;
  --grid-gap-mobile: 8px;
  
  /* 컨테이너 여백 */
  --container-desktop: 24px;
  --container-tablet: 16px;
  --container-mobile: 12px;
  
  /* 성능 최적화 */
  --debounce-resize: 300ms;
  --debounce-drag: 200ms;
  
  /* 터치 최적화 */
  --touch-target: 44px;
  --touch-spacing: 8px;
  
  /* E-Torch 브랜드 색상 (OKLCH) */
  --color-primary: oklch(0.2 0.15 240);
  --color-secondary: oklch(0.5 0.2 230);
  --color-tertiary: oklch(0.45 0.18 220);
}

/* HSL 폴백 (OKLCH 미지원 브라우저) */
@supports not (color: oklch(0 0 0)) {
  :root {
    --color-primary: hsl(225, 60%, 15%);
    --color-secondary: hsl(225, 80%, 50%);
    --color-tertiary: hsl(200, 80%, 45%);
  }
}
```

## 3. 아키텍처 계층 구조

### 3.1 서버/클라이언트 컴포넌트 분리

| 컴포넌트 유형 | 책임 | 구현 방식 |
|-------------|------|----------|
| **서버 컴포넌트** | 권한 확인, 메타데이터, 데이터 페칭 | App Router RSC |
| **클라이언트 컴포넌트** | 차트 렌더링, 편집, 상태 관리 | "use client" |
| **서버 액션** | 결제, 구독 관리, 데이터 변경 | "use server" |

### 3.2 데이터 소스 제약사항 구현

```typescript
// 데이터 소스별 제약사항 (기획서 정확 반영)
const DATA_SOURCE_CONSTRAINTS = {
  KOSIS: {
    supportedPeriods: ['M', 'Q', 'A'],        // 일간 데이터 없음
    maxHistory: 'unlimited',
    updateSchedule: 'official_announcement'
  },
  ECOS: {
    supportedPeriods: ['D', 'M', 'Q', 'A'],   // 일간 포함
    dailyLimit: '1year',                      // 일간은 최대 1년
    maxHistory: 'unlimited',
    updateSchedule: 'daily'
  }
} as const

// UI에서 주기 제한 적용
export const usePeriodConstraints = (dataSource: keyof typeof DATA_SOURCE_CONSTRAINTS) => {
  const constraints = DATA_SOURCE_CONSTRAINTS[dataSource]
  
  const validatePeriodSelection = (period: string, dateRange: DateRange) => {
    if (period === 'D' && dataSource === 'KOSIS') {
      throw new Error('KOSIS는 일간 데이터를 지원하지 않습니다')
    }
    
    if (period === 'D' && getDaysDiff(dateRange) > 365) {
      throw new Error('일간 데이터는 최대 1년까지만 조회 가능합니다')
    }
    
    return true
  }
  
  return { constraints, validatePeriodSelection }
}
```

### 3.3 7가지 위젯 시스템

```typescript
// 위젯 팩토리 패턴 (정확한 7가지)
export const WIDGET_TYPES = {
  'time-series': TimeSeriesWidget,
  'bar-chart': BarChartWidget,
  'scatter-chart': ScatterChartWidget,
  'radar-chart': RadarChartWidget,
  'radial-bar-chart': RadialBarChartWidget,
  'text-custom': TextCustomWidget,      // 사용자 정의 마크다운/HTML
  'text-data': TextDataWidget          // 데이터 기반 자동 생성
} as const

// 데이터 소스 필요 여부
export const WIDGET_DATA_REQUIREMENTS = {
  'time-series': true,
  'bar-chart': true, 
  'scatter-chart': true,
  'radar-chart': true,
  'radial-bar-chart': true,
  'text-custom': false,                 // 데이터 소스 불필요
  'text-data': true                     // 단일 데이터 소스만
} as const
```

## 4. 모노레포 구조 (9패키지)

### 4.1 패키지 의존성 그래프

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

### 4.2 핵심 패키지 구현

```typescript
// @e-torch/core - 구독 모델 타입
export const PLAN_LIMITS = {
  basic: {
    dashboards: 3,        // 최대 3개
    widgets: 6,           // 대시보드당 6개
    indicators: 20,       // MVP 20개 지표
    dataPeriod: '3years', // 최근 3년만
    watermark: true,      // 워터마크 표시
    copy: false,          // 대시보드 복사 불가
    embed: false          // 임베드 코드 불가
  },
  pro: {
    dashboards: -1,       // 무제한
    widgets: -1,          // 무제한
    indicators: 40,       // 전체 40개 지표
    dataPeriod: 'all',    // 전체 기간
    watermark: false,     // 워터마크 제거
    copy: true,           // 대시보드 복사 가능
    embed: true           // 임베드 코드 생성
  }
} as const

// @e-torch/charts - LTTB 다운샘플링
export const useLTTBSampling = (data: DataPoint[], targetPoints = 1000) => {
  return useMemo(() => {
    if (data.length <= targetPoints) return data
    return applyLTTB(data, targetPoints)
  }, [data, targetPoints])
}
```

## 5. 성능 최적화 (구체적 수치)

### 5.1 react-grid-layout 최적화

```typescript
// 편집 모드 디바운싱 (정확한 수치)
const gridLayoutProps = {
  // 드래그: 빠른 반응성 중시 (200ms)
  onDragStart: () => setChartRenderingEnabled(false),
  onDragStop: debounce(() => setChartRenderingEnabled(true), 200),
  
  // 리사이즈: 정확성 중시 (300ms)
  onResizeStart: () => setChartRenderingEnabled(false), 
  onResizeStop: debounce(() => setChartRenderingEnabled(true), 300),
  
  // 반응형 설정
  cols: { lg: 12, md: 8, sm: 4 },
  breakpoints: { lg: 1200, md: 768, sm: 0 },
  margin: [16, 16],          // 데스크톱
  containerPadding: [24, 24] // 데스크톱
}

// 터치 최적화 (모바일)
const mobileGridProps = {
  ...gridLayoutProps,
  margin: [8, 8],            // 모바일 간격
  containerPadding: [12, 12], // 44px 터치 타겟 고려
  minH: 3,                   // 최소 높이로 44px 보장
  minW: 2                    // 최소 너비로 44px 보장
}
```

### 5.2 차트 렌더링 최적화

```typescript
// 1000포인트 임계값 적용
export const OptimizedChart = memo(({ data, type, ...props }) => {
  const sampledData = useLTTBSampling(data, 1000)
  const isEditMode = useEditMode()
  const chartRenderingEnabled = useChartRendering()
  
  // 편집 중에는 차트 렌더링 비활성화
  if (isEditMode && !chartRenderingEnabled) {
    return <ChartPlaceholder />
  }
  
  return <AccessibleChart data={sampledData} type={type} {...props} />
})
```

## 6. 구독 모델 + 토스페이먼츠 구현

### 6.1 권한 검증 시스템 (< 10ms)

```typescript
// 5분 캐시로 권한 검증 최적화
export const useAuthWithCache = () => {
  const { data: session } = useQuery({
    queryKey: ['auth-session'],
    queryFn: () => supabase.auth.getSession(),
    staleTime: 5 * 60 * 1000, // 5분 캐시
    cacheTime: 10 * 60 * 1000 // 10분 보관
  })
  
  const plan = session?.user?.user_metadata?.subscription_plan || 'basic'
  const limits = PLAN_LIMITS[plan]
  
  return { user: session?.user, plan, limits, isAuth: !!session?.user }
}

// 기능별 권한 게이트
export const FeatureGate = ({ feature, children, fallback }) => {
  const { limits } = useAuthWithCache()
  
  if (!limits[feature]) {
    return fallback || <UpgradePrompt feature={feature} />
  }
  
  return children
}
```

### 6.2 토스페이먼츠 결제 시스템

```typescript
// 토스페이먼츠 PaymentWidget 통합
export const SubscriptionPayment = ({ planType }) => {
  const clientKey = process.env.NEXT_PUBLIC_TOSS_CLIENT_KEY
  const { user } = useAuthWithCache()
  
  useEffect(() => {
    const paymentWidget = PaymentWidget(clientKey, user.id)
    
    // 결제 UI 렌더링
    paymentWidget.renderPaymentMethods('#payment-methods', {
      value: planType === 'pro' ? 9900 : 0 // 월간 9,900원
    })
    
    paymentWidget.renderAgreement('#agreement')
    
    return () => paymentWidget.destroy()
  }, [clientKey, user.id, planType])
  
  const handlePayment = async () => {
    const orderId = `etorch_${Date.now()}_${user.id}`
    
    await paymentWidget.requestPayment({
      orderId,
      orderName: `E-Torch Pro 플랜 (월간)`,
      successUrl: `${window.location.origin}/payment/success`,
      failUrl: `${window.location.origin}/payment/fail`,
      customerEmail: user.email,
      customerName: user.user_metadata?.name
    })
  }
  
  return (
    <div className="payment-container">
      <div id="payment-methods" />
      <div id="agreement" />
      <button onClick={handlePayment}>결제하기</button>
    </div>
  )
}

// 서버 액션: 결제 성공 처리
export async function handlePaymentSuccess(paymentKey: string, orderId: string, amount: number) {
  const session = await getServerSession()
  
  // 토스페이먼츠 결제 승인
  const response = await fetch('https://api.tosspayments.com/v1/payments/confirm', {
    method: 'POST',
    headers: {
      'Authorization': `Basic ${Buffer.from(process.env.TOSS_SECRET_KEY + ':').toString('base64')}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ paymentKey, orderId, amount })
  })
  
  if (response.ok) {
    // 구독 상태 업데이트
    await supabase
      .from('subscriptions')
      .upsert({
        user_id: session.user.id,
        plan: 'pro',
        status: 'active',
        billing_key: paymentKey, // 자동 갱신용
        next_billing_date: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000)
      })
    
    return { success: true }
  }
  
  throw new Error('Payment confirmation failed')
}
```

## 7. 워터마크 + 접근성 구현

### 7.1 워터마크 시스템

```typescript
// Basic 플랜 워터마크 적용
export const WatermarkProvider = ({ children }) => {
  const { plan } = useAuthWithCache()
  
  return (
    <div className="relative">
      {children}
      {plan === 'basic' && (
        <div className="absolute bottom-2 right-2 text-xs text-gray-400 pointer-events-none opacity-60 select-none">
          E-Torch로 제작됨
        </div>
      )}
    </div>
  )
}

// 내보내기 시 워터마크 처리
export const exportDashboard = async (format: 'png' | 'pdf') => {
  const { plan } = useAuthWithCache()
  
  const options = {
    quality: plan === 'pro' ? 1.0 : 0.8,        // Pro: 고화질
    watermark: plan === 'basic',                 // Basic: 워터마크
    resolution: plan === 'pro' ? '2x' : '1x'     // Pro: 고해상도
  }
  
  return await generateExport(format, options)
}
```

### 7.2 WCAG 2.1 AA 접근성

```typescript
// 차트 접근성 컴포넌트
export const AccessibleChart = ({ data, type, title, ...props }) => {
  const summaryText = useMemo(() => 
    generateChartSummary(data), // "GDP 2020-2024, 최고 3.2%, 최저 -1.1%"
    [data]
  )
  
  return (
    <div role="img" aria-label={title || summaryText}>
      <Chart data={data} type={type} {...props} />
      
      {/* 스크린 리더용 데이터 테이블 */}
      <table className="sr-only" aria-label={`${title} 데이터`}>
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
              <td>{formatDate(item.date)}</td>
              <td>{formatNumber(item.value)}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}

// 터치 최적화 버튼 (44×44px 보장)
export const TouchButton = ({ children, className, ...props }) => (
  <button 
    className={cn(
      "min-w-[44px] min-h-[44px] p-2 touch-manipulation",
      "focus-visible:ring-2 focus-visible:ring-primary focus-visible:ring-offset-2",
      className
    )}
    {...props}
  >
    {children}
  </button>
)

// 키보드 네비게이션 (편집 모드)
export const useKeyboardNavigation = (widgets: Widget[]) => {
  const [selectedWidget, setSelectedWidget] = useState<string | null>(null)
  
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (!selectedWidget) return
      
      switch (e.key) {
        case 'ArrowRight':
          moveWidget(selectedWidget, 'right')
          break
        case 'ArrowLeft': 
          moveWidget(selectedWidget, 'left')
          break
        case 'ArrowUp':
          moveWidget(selectedWidget, 'up')
          break
        case 'ArrowDown':
          moveWidget(selectedWidget, 'down')
          break
        case 'Delete':
          deleteWidget(selectedWidget)
          break
        case 'Escape':
          setSelectedWidget(null)
          break
      }
    }
    
    document.addEventListener('keydown', handleKeyDown)
    return () => document.removeEventListener('keydown', handleKeyDown)
  }, [selectedWidget])
  
  return { selectedWidget, setSelectedWidget }
}
```

## 8. 모바일 최적화

### 8.1 터치 인터페이스

```typescript
// 44×44px 터치 타겟 보장
export const MobileDashboard = () => {
  const isMobile = useMediaQuery('(max-width: 767px)')
  
  if (isMobile) {
    return (
      <GridLayout
        {...mobileGridProps}
        isDraggable={true}         // 드래그만 지원
        isResizable={false}        // 리사이즈는 속성 패널에서
      />
    )
  }
  
  return <DesktopDashboard />
}

// 스와이프 제스처
export const useSwipeGesture = () => {
  const [touchStart, setTouchStart] = useState<number | null>(null)
  
  const handleTouchStart = (e: TouchEvent) => {
    setTouchStart(e.targetTouches[0].clientX)
  }
  
  const handleTouchEnd = (e: TouchEvent) => {
    if (!touchStart) return
    
    const touchEnd = e.changedTouches[0].clientX
    const distance = touchStart - touchEnd
    
    if (Math.abs(distance) > 100) { // 최소 100px 이동
      if (distance > 0) navigateNext()
      else navigatePrev()
    }
  }
  
  return { handleTouchStart, handleTouchEnd }
}
```

## 9. 개발 도구 설정

### 9.1 ESLint 접근성 설정

```json
// .eslintrc.json
{
  "extends": [
    "@e-torch/eslint-config",
    "plugin:jsx-a11y/recommended"
  ],
  "rules": {
    "jsx-a11y/alt-text": "error",
    "jsx-a11y/aria-label": "error", 
    "jsx-a11y/click-events-have-key-events": "error",
    "jsx-a11y/no-static-element-interactions": "error"
  }
}
```

### 9.2 성능 모니터링

```typescript
// Web Vitals 모니터링
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'

export function initWebVitals() {
  getCLS(sendToAnalytics)
  getFID(sendToAnalytics) 
  getFCP(sendToAnalytics)
  getLCP(sendToAnalytics)
  getTTFB(sendToAnalytics)
}

function sendToAnalytics({ name, value, id }) {
  // 목표값 확인
  const thresholds = {
    LCP: 2500,   // 2.5초
    FID: 100,    // 100ms
    CLS: 0.1     // 0.1
  }
  
  if (value > thresholds[name]) {
    console.warn(`${name} threshold exceeded: ${value}`)
  }
}
```
