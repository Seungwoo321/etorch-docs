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

```typescript
const DATA_SOURCE_CONSTRAINTS = {
  KOSIS: { supportedPeriods: ['M', 'Q', 'A'], maxHistory: 'unlimited' },
  ECOS: { supportedPeriods: ['D', 'M', 'Q', 'A'], dailyLimit: '1year' }
} as const
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

### 5.1 react-grid-layout 최적화

| 설정 | 값 | 목적 |
|------|----|----|
| onDragStop | debounce 200ms | 빠른 반응성 |
| onResizeStop | debounce 300ms | 정확성 우선 |
| 터치 타겟 | 44×44px | 모바일 최적화 |
| 그리드 컬럼 | 12/8/4 (데스크톱/태블릿/모바일) | 반응형 |

### 5.2 차트 렌더링 최적화

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

| 기능 | Basic | Pro |
|------|-------|-----|
| 대시보드 | 3개 | 무제한 |
| 위젯 | 6개/대시보드 | 무제한 |
| 지표 | 20개 (MVP) | 40개 (전체) |
| 데이터 기간 | 최근 3년 | 전체 기간 |
| 워터마크 | 표시 | 제거 가능 |
| 대시보드 복사 | 불가 | 가능 |

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

| 플랜 | 워터마크 | 내보내기 품질 |
|------|---------|-------------|
| Basic | "E-Torch로 제작됨" 우하단 | 0.8 품질 |
| Pro | 제거 가능 | 1.0 품질 고해상도 |

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
