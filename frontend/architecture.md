# E-Torch 프론트엔드 아키텍처 설계 문서

## 1. 아키텍처 개요

### 1.1 E-Torch 특화 제약사항

| 도메인 특성 | 기술적 제약 | 구현 방법 |
|------------|------------|----------|
| **KOSIS/ECOS 통합** | 이기종 API 응답 차이 | 어댑터 패턴 |
| **1000+ 데이터 포인트** | 메모리 200MB 제한 | LTTB 다운샘플링 |
| **구독 모델** | 권한 검증 < 10ms | 클라이언트 캐시 5분 |
| **정기 발표 데이터** | 정확성 > 실시간성 | 24시간 캐시 |

### 1.2 핵심 성능 목표

| 기능 | 목표값 | 측정 기준 |
|------|--------|----------|
| 차트 렌더링 | < 3초 | 1000포인트 시계열 |
| 편집 반응성 | 60fps | 드래그 중 |
| 메모리 사용량 | < 200MB | 대시보드당 |
| 권한 검증 | < 10ms | 플랜별 기능 제한 |

## 2. 기술 스택

### 2.1 핵심 기술 (E-Torch 특화 설정)

| 영역 | 기술 | 버전 | E-Torch 특화 설정 |
|------|------|------|------------------|
| **모노레포** | Turborepo + pnpm | 2.5.3 + 10.11.0 | 9패키지 분할 |
| **프레임워크** | Next.js + React | 15.3.2 + 19.1.0 | App Router 전용 |
| **UI** | Tailwind CSS + Shadcn/UI | 4.1.7 + latest | CSS-first, OKLCH |
| **상태관리** | Zustand + TanStack Query | 5.0.5 + 5.77.0 | 서버/클라이언트 분리 |
| **차트** | Recharts | 2.15.3 | LTTB 다운샘플링 |
| **레이아웃** | react-grid-layout | 1.5.1 | 300ms 디바운싱 |
| **인증** | Supabase Auth | v2 | SNS 로그인 3개 |

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
| **서버 컴포넌트** | 데이터 페칭, 메타데이터 로드 | Next.js 15 App Router |
| **클라이언트 컴포넌트** | 인터랙션, 상태 관리 | "use client" |
| **서버 액션** | 데이터 변경, 검증 | "use server" |

### 3.2 데이터 흐름

```
KOSIS/ECOS API → 어댑터 패턴 → TanStack Query → Zustand → Charts → UI
```

### 3.3 성능 최적화 구현

```typescript
// LTTB 다운샘플링 (1000+ 포인트)
export const processChartData = (data: DataPoint[]) => {
  return data.length > 1000 ? applyLTTB(data, 1000) : data
}

// 편집 모드 디바운싱
const gridLayoutProps = {
  onDragStart: () => setChartRenderingEnabled(false),
  onDragStop: debounce(() => setChartRenderingEnabled(true), 200),
  onResizeStop: debounce(() => setChartRenderingEnabled(true), 300)
}
```

## 4. 모노레포 패키지 구조

### 4.1 패키지 의존성 (9개)

| 패키지 | 의존성 | 핵심 책임 |
|--------|--------|----------|
| **core** | 없음 | 타입, 상수 |
| **utils** | 없음 | LTTB, 포맷터 |
| **ui** | core, utils | Shadcn/UI |
| **data-sources** | core, utils | KOSIS/ECOS 어댑터 |
| **state** | core, utils, data-sources | 상태 관리 |
| **charts** | core, ui, utils, data-sources | 5가지 차트 + 2가지 텍스트 |
| **dashboard** | 모든 패키지 | react-grid-layout |
| **server-api** | core, utils | API 핸들러 |
| **eslint-config** | 없음 | Standard JS |

### 4.2 핵심 패키지 구현

```typescript
// Core: 구독 타입
export type Plan = 'basic' | 'pro'
export type PlanLimits = {
  basic: { dashboards: 3, widgets: 6, indicators: 20 }
  pro: { dashboards: -1, widgets: -1, indicators: 40 }
}

// Charts: LTTB 적용
export const ChartRenderer = memo(({ data, type, options }) => {
  const sampledData = useLTTBSampling(data, 1000)
  return <Chart data={sampledData} type={type} {...options} />
})

// Data-Sources: 어댑터 패턴
export class KOSISAdapter extends BaseAdapter {
  getSupportedPeriods() { return ['M', 'Q', 'A'] }
}
export class ECOSAdapter extends BaseAdapter {
  getSupportedPeriods() { return ['D', 'M', 'Q', 'A'] }
}
```

## 5. 성능 최적화

### 5.1 차트 렌더링 최적화

| 최적화 대상 | 임계값 | 최적화 방법 | 성능 목표 |
|------------|--------|------------|----------|
| 차트 렌더링 | 1000+ 포인트 | LTTB 다운샘플링 | < 3초 |
| 위젯 그리드 | 20개+ 위젯 | react-window 가상화 | 60fps |
| 편집 모드 | 드래그 중 | 차트 렌더링 비활성화 | < 16ms |
| 데이터 로딩 | 지연 로딩 | Intersection Observer | LCP < 2.5s |

### 5.2 메모리 관리

```typescript
// 편집 완료 시 메모리 정리
export const WidgetContainer = ({ widget }) => {
  useEffect(() => {
    return () => {
      cleanupWidgetListeners(widget.id)
      clearWidgetCache(widget.id)
    }
  }, [widget.id])
  
  return <Widget {...widget} />
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

## 6. 구독 모델 구현

### 6.1 권한 검증 (< 10ms)

```typescript
// 권한 게이트 시스템
const FEATURE_GATES = {
  basic: { indicators: 20, dashboards: 3, widgets: 6 },
  pro: { indicators: 40, dashboards: Infinity, widgets: Infinity }
} as const

export const useFeatureGate = (feature: string) => {
  const { plan } = useAuth()
  return FEATURE_GATES[plan][feature]
}

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
```

### 6.2 Supabase Auth + 토스페이먼츠

| 설정 | 값 | 용도 |
|------|-----|------|
| **OAuth 제공자** | Google, Naver, Kakao | SNS 로그인 전용 |
| **JWT 만료** | 1시간 | 자동 갱신 |
| **세션 유지** | 30일 | 브라우저 저장 |

```typescript
// 서버 액션: 구독 업그레이드
export async function upgradeSubscriptionAction(planType: 'pro') {
  const session = await getServerSession()
  if (!session) throw new Error('Unauthorized')
  
  const paymentSession = await createTossPayment({
    amount: 9900,
    orderId: generateOrderId(),
    customerEmail: session.user.email
  })
  
  return { success: true, data: paymentSession }
}
```

### 6.3 런타임 제한 적용

```typescript
// 위젯 생성 시 플랜 제한 확인
export async function createWidgetAction(dashboardId: string, widgetData: any) {
  const session = await getServerSession()
  const planLimits = await getUserPlanLimits(session.user.id)
  
  const currentWidgets = await getWidgetCount(dashboardId)
  if (planLimits.widgets !== -1 && currentWidgets >= planLimits.widgets) {
    throw new Error('Widget limit exceeded')
  }
  
  const widget = await createWidget(dashboardId, widgetData)
  return { success: true, data: widget }
}
```
