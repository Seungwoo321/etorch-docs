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
| 차트 렌더링 | < 2초 | 1000포인트 시계열 |
| 편집 반응성 | 60fps | 드래그 200ms + 리사이즈 300ms |
| 메모리 사용량 | < 200MB | 대시보드당 |
| 권한 검증 | < 10ms | 플랜별 기능 제한 |
| 터치 타겟 | 44×44px | 모든 인터랙티브 요소 |

## 2. 기술 스택

### 2.1 핵심 기술

| 영역 | 기술 | 버전 | E-Torch 특화 설정 |
|------|------|------|------------------|
| **모노레포** | Turborepo + pnpm | 2.5.3 + 10.11.0 | 10패키지 분할 |
| **프레임워크** | Next.js + React | 15.3.2 + 19.1.0 | App Router + useOptimistic |
| **UI** | Tailwind CSS + Shadcn/UI | 4.1.7 + latest | CSS-first, OKLCH 색상 |
| **상태관리** | Zustand + TanStack Query | 5.0.5 + 5.77.0 | 서버/클라이언트 분리 |
| **차트** | Recharts | 2.15.3 | LTTB + 7가지 위젯 |
| **레이아웃** | react-grid-layout | 1.5.1 | 200ms/300ms 디바운싱 |
| **패널** | Shadcn ResizablePanel | latest | 위젯 에디터 핵심 |
| **인증** | Supabase Auth | v2 | SNS 3개 + 구독 모델 |
| **결제** | 토스페이먼츠 | latest | PaymentWidget + 빌링키 |

### 2.2 Tailwind CSS 4 레이아웃 설정

```css
@theme {
  /* E-Torch 브랜드 색상 (OKLCH) */
  --color-primary: oklch(0.2 0.15 240);     /* #0c1e3e */
  --color-secondary: oklch(0.5 0.2 230);    /* #1a56db */
  --color-tertiary: oklch(0.45 0.18 220);   /* #0284c7 */
  
  /* 반응형 헤더 높이 */
  --header-desktop: 80px; --header-tablet: 72px; --header-mobile: 64px;
  /* 편집 툴바 높이 */
  --toolbar-desktop: 64px; --toolbar-tablet: 56px;
  /* 사이드바 너비 */
  --sidebar-width: 200px; --sidebar-collapsed: 60px;
  
  /* 위젯 에디터 패널 시스템 */
  --preview-panel-min: 400px; --options-panel-min: 320px;
  --property-panel-width: 320px; --property-panel-range: 280px-480px;
  
  /* 위젯 최소 크기 */
  --widget-min-desktop: 300px 200px; --widget-min-tablet: 250px 180px;
  
  /* 성능 최적화 디바운싱 */
  --debounce-drag: 200ms; --debounce-resize: 300ms; --debounce-search: 300ms;
  
  /* 터치 최적화 */
  --touch-target: 44px; --touch-spacing: 8px;
  
  /* 그리드 시스템 */
  --grid-cols-desktop: 12; --grid-cols-tablet: 8; --grid-cols-mobile: 4;
  --grid-gap-desktop: 16px; --grid-gap-tablet: 12px; --grid-gap-mobile: 8px;
}
```

#### 5.2.1 편집 모드 상태 머신

```mermaid
stateDiagram-v2
    [*] --> ViewMode : 대시보드 조회
    
    ViewMode --> EditMode : 편집 버튼 클릭
    EditMode --> ViewMode : 저장/취소
    
    state EditMode {
        [*] --> Idle : 편집 모드 진입
        
        Idle --> DragStart : 위젯 드래그 시작
        Idle --> ResizeStart : 위젯 리사이즈 시작
        Idle --> WidgetEdit : 위젯 편집
        
        DragStart --> Dragging : 드래그 중
        Dragging --> DragEnd : 드래그 완료
        DragEnd --> Idle : 200ms 디바운싱 후
        
        ResizeStart --> Resizing : 리사이즈 중
        Resizing --> ResizeEnd : 리사이즈 완료
        ResizeEnd --> Idle : 300ms 디바운싱 후
        
        WidgetEdit --> WidgetEditor : 위젯 에디터 열기
        WidgetEditor --> Idle : 위젯 저장/취소
        
        state Dragging {
            [*] --> ChartDisabled : 차트 렌더링 비활성화
            ChartDisabled --> PositionUpdate : 위치 실시간 업데이트
            PositionUpdate --> ChartDisabled : 계속 드래그
        }
        
        state Resizing {
            [*] --> ChartDisabled : 차트 렌더링 비활성화
            ChartDisabled --> SizeUpdate : 크기 실시간 업데이트
            SizeUpdate --> ChartDisabled : 계속 리사이즈
        }
        
        state WidgetEditor {
            [*] --> PreviewPanel : 미리보기 패널
            PreviewPanel --> OptionsPanel : 옵션 패널
            OptionsPanel --> DataSourcePanel : 데이터 소스 패널
            DataSourcePanel --> PreviewPanel : 실시간 동기화
        }
    }
    
    note right of DragEnd : 200ms 디바운싱\n빠른 반응성 우선
    note right of ResizeEnd : 300ms 디바운싱\n정확성 우선
    note right of ChartDisabled : 60fps 유지를 위한\n성능 최적화
```

#### 7.1.1 구독 모델 권한 검증 플로우

```mermaid
graph TD
    subgraph "사용자 액션"
        UserAction["사용자 액션<br/>(대시보드 생성, 위젯 추가, 지표 선택 등)"]
    end
    
    subgraph "캐시 확인 (5분)"
        CacheCheck{"캐시된 권한 정보<br/>존재하는가?"}
        CacheValid{"캐시가 유효한가?<br/>(5분 이내)"}
        CachedPlan["캐시된 플랜 정보<br/>(basic/pro)"]
    end
    
    subgraph "권한 검증 (< 10ms)"
        AuthCheck["Supabase Auth<br/>세션 확인"]
        PlanCheck["구독 플랜 확인<br/>(subscription_plan)"]
        DefaultPlan["기본 플랜 적용<br/>(basic)"]
    end
    
    subgraph "제한 사항 적용"
        DashboardLimit{"대시보드 수<br/>제한 확인"}
        WidgetLimit{"위젯 수<br/>제한 확인"}
        IndicatorLimit{"지표 접근<br/>권한 확인"}
        PeriodLimit{"데이터 기간<br/>제한 확인"}
    end
    
    subgraph "UI 상태 업데이트 (< 10ms)"
        EnableFeature["기능 활성화"]
        DisableFeature["기능 비활성화<br/>(회색 처리 + 툴팁)"]
        ShowUpgrade["업그레이드 안내<br/>(배너/모달)"]
        ApplyWatermark["워터마크 적용<br/>(Basic 플랜)"]
    end
    
    UserAction --> CacheCheck
    
    CacheCheck -->|Yes| CacheValid
    CacheCheck -->|No| AuthCheck
    
    CacheValid -->|Valid| CachedPlan
    CacheValid -->|Expired| AuthCheck
    
    AuthCheck --> PlanCheck
    PlanCheck -->|Authenticated| CachedPlan
    PlanCheck -->|Not Authenticated| DefaultPlan
    
    CachedPlan --> DashboardLimit
    DefaultPlan --> DashboardLimit
    
    DashboardLimit -->|Within Limit| WidgetLimit
    DashboardLimit -->|Exceeded| DisableFeature
    
    WidgetLimit -->|Within Limit| IndicatorLimit
    WidgetLimit -->|Exceeded| DisableFeature
    
    IndicatorLimit -->|Accessible| PeriodLimit
    IndicatorLimit -->|Restricted| DisableFeature
    
    PeriodLimit -->|Within Limit| EnableFeature
    PeriodLimit -->|Exceeded| DisableFeature
    
    EnableFeature --> ApplyWatermark
    DisableFeature --> ShowUpgrade
    
    classDef action fill:#e3f2fd
    classDef cache fill:#f3e5f5
    classDef auth fill:#e8f5e8
    classDef limit fill:#fff3e0
    classDef ui fill:#fce4ec
    
    class UserAction action
    class CacheCheck,CacheValid,CachedPlan cache
    class AuthCheck,PlanCheck,DefaultPlan auth
    class DashboardLimit,WidgetLimit,IndicatorLimit,PeriodLimit limit
    class EnableFeature,DisableFeature,ShowUpgrade,ApplyWatermark ui
```

#### 3.2.1 데이터 플로우 아키텍처

```mermaid
graph LR
    subgraph "데이터 소스"
        KOSIS["KOSIS API<br/>(M,Q,A 주기)<br/>무제한 기간"]
        ECOS["ECOS API<br/>(D,M,Q,A 주기)<br/>일별 1년 제한"]
        OECD["OECD API<br/>(향후 확장)<br/>현재 비활성화"]
    end
    
    subgraph "어댑터 레이어"
        KOSISAdapter["KOSIS 어댑터<br/>응답 정규화"]
        ECOSAdapter["ECOS 어댑터<br/>응답 정규화"]
        OECDAdapter["OECD 어댑터<br/>(미구현)"]
    end
    
    subgraph "데이터 처리"
        Validator["데이터 검증<br/>(기간/주기 제한)"]
        Transformer["데이터 변환<br/>(원본값/변화율/누적값)"]
        LTTB["LTTB 다운샘플링<br/>(1000+ 포인트 임계값)"]
    end
    
    subgraph "캐싱 레이어"
        APICache["API 응답 캐시<br/>(15분)"]
        ComputeCache["계산 결과 캐시<br/>(30분)"]
        HistoryCache["과거 데이터 캐시<br/>(24시간)"]
    end
    
    subgraph "차트 렌더링"
        Recharts["Recharts<br/>(시각화)"]
        AccessibleChart["접근성 차트<br/>(WCAG 2.1 AA)"]
        DataTable["데이터 테이블<br/>(스크린 리더용)"]
    end
    
    subgraph "구독 모델 필터링"
        PlanFilter["플랜별 지표 필터<br/>(Basic: 20개, Pro: 40개)"]
        PeriodFilter["기간 제한 필터<br/>(Basic: 3년, Pro: 무제한)"]
    end
    
    KOSIS --> KOSISAdapter
    ECOS --> ECOSAdapter
    OECD --> OECDAdapter
    
    KOSISAdapter --> Validator
    ECOSAdapter --> Validator
    OECDAdapter --> Validator
    
    Validator --> PlanFilter
    PlanFilter --> PeriodFilter
    PeriodFilter --> Transformer
    
    Transformer --> LTTB
    LTTB --> APICache
    APICache --> ComputeCache
    ComputeCache --> HistoryCache
    
    HistoryCache --> Recharts
    Recharts --> AccessibleChart
    AccessibleChart --> DataTable
    
    classDef source fill:#e3f2fd
    classDef adapter fill:#f3e5f5
    classDef process fill:#e8f5e8
    classDef cache fill:#fff3e0
    classDef render fill:#fce4ec
    classDef filter fill:#f1f8e9
    
    class KOSIS,ECOS,OECD source
    class KOSISAdapter,ECOSAdapter,OECDAdapter adapter
    class Validator,Transformer,LTTB process
    class APICache,ComputeCache,HistoryCache cache
    class Recharts,AccessibleChart,DataTable render
    class PlanFilter,PeriodFilter filter
```

## 3. 아키텍처 계층 구조

### 3.1 서버/클라이언트 컴포넌트 분리

| 컴포넌트 유형 | 책임 | 구현 방식 |
|-------------|------|----------|
| **서버 컴포넌트** | 권한 확인, 메타데이터, 데이터 페칭 | App Router RSC |
| **클라이언트 컴포넌트** | 차트 렌더링, 편집, 상태 관리 | "use client" |
| **서버 액션** | 결제, 구독 관리, 데이터 변경 | "use server" |

### 3.2 데이터 소스 제약사항

| 데이터 소스 | 현재 상태 | 지원 주기 | 기간 제한 | UI 표시 |
|------------|----------|----------|----------|--------|
| **KOSIS** | ✅ 완전 지원 | M, Q, A | 무제한 | 기본 선택 |
| **ECOS** | ✅ 완전 지원 | D, M, Q, A | **일별만 1년 제한** | 주기별 제한 |
| **OECD** | 🚧 향후 확장 | 미정 | 현재 비활성화 | 비활성화 + 툴팁 |

```typescript
// 데이터 소스 설정 (기간 제한 포함)
export const DATA_SOURCE_CONFIG = {
  KOSIS: {
    id: 'kosis',
    name: '통계청 KOSIS',
    status: 'active',
    supportedPeriods: ['M', 'Q', 'A'] as const,
    maxHistoryYears: null, // 무제한
    indicatorCount: { basic: 12, pro: 12 }
  },
  ECOS: {
    id: 'ecos', 
    name: '한국은행 ECOS',
    status: 'active',
    supportedPeriods: ['D', 'M', 'Q', 'A'] as const,
    maxHistoryYears: { D: 1, M: null, Q: null, A: null }, // 일별만 1년 제한
    indicatorCount: { basic: 8, pro: 28 }
  },
  OECD: {
    id: 'oecd',
    name: 'OECD 통계',
    status: 'planned', // 현재 미지원
    supportedPeriods: [],
    maxHistoryYears: null,
    indicatorCount: { basic: 0, pro: 0 },
    plannedRelease: '2025-Q3'
  }
} as const

// ECOS 일별 데이터 1년 제한 검증
export const validateDataPeriod = (source: string, period: string, startDate: Date, endDate: Date) => {
  if (source === 'ecos' && period === 'D') {
    const oneYearAgo = new Date()
    oneYearAgo.setFullYear(oneYearAgo.getFullYear() - 1)
    
    if (startDate < oneYearAgo) {
      throw new Error('ECOS 일별 데이터는 최근 1년까지만 조회 가능합니다')
    }
  }
}
```

### 3.3 7가지 위젯 시스템

| 위젯 타입 | 데이터 소스 필요 | 최대 시리즈 | 구현 클래스 |
|----------|----------------|------------|------------|
| time-series | ✅ | 5개 | TimeSeriesWidget |
| bar-chart | ✅ | 5개 | BarChartWidget |
| scatter-chart | ✅ | 5개 | ScatterChartWidget |
| radar-chart | ✅ | 5개 | RadarChartWidget |
| radial-bar-chart | ✅ | 5개 | RadialBarChartWidget |
| text-custom | ❌ | - | TextCustomWidget |
| text-data | ✅ | 1개 | TextDataWidget |

#### 3.3.1 위젯 시스템 아키텍처

```mermaid
graph TB
    subgraph "위젯 팩토리 패턴"
        WidgetFactory["WidgetFactory<br/>(팩토리 클래스)"]
        WidgetType["위젯 타입 결정<br/>(7가지 타입)"]
        WidgetFactory --> WidgetType
    end
    
    subgraph "차트형 위젯 (5가지)"
        TimeSeries["TimeSeries<br/>(Line/Area/Bar 혼합)"]
        BarChart["BarChart<br/>(수직/수평 바)"]
        ScatterChart["ScatterChart<br/>(산점도 + 회귀선)"]
        RadarChart["RadarChart<br/>(다각형/원형)"]
        RadialBarChart["RadialBarChart<br/>(방사형 바)"]
    end
    
    subgraph "텍스트형 위젯 (2가지)"
        TextCustom["TextCustom<br/>(사용자 정의)"]
        TextData["TextData<br/>(데이터 기반)"]
    end
    
    subgraph "공통 인터페이스"
        IWidget["IWidget Interface"]
        IChartWidget["IChartWidget Interface"]
        ITextWidget["ITextWidget Interface"]
    end
    
    subgraph "데이터 처리"
        DataSource["DataSource<br/>(KOSIS/ECOS)"]
        LTTB["LTTB 다운샘플링<br/>(1000+ 포인트)"]
        SeriesManager["SeriesManager<br/>(최대 5개)"]
    end
    
    WidgetType --> TimeSeries
    WidgetType --> BarChart
    WidgetType --> ScatterChart
    WidgetType --> RadarChart
    WidgetType --> RadialBarChart
    WidgetType --> TextCustom
    WidgetType --> TextData
    
    TimeSeries --> IChartWidget
    BarChart --> IChartWidget
    ScatterChart --> IChartWidget
    RadarChart --> IChartWidget
    RadialBarChart --> IChartWidget
    
    TextCustom --> ITextWidget
    TextData --> ITextWidget
    
    IChartWidget --> IWidget
    ITextWidget --> IWidget
    
    DataSource --> LTTB
    LTTB --> SeriesManager
    SeriesManager --> IChartWidget
    DataSource --> TextData
    
    classDef chartWidget fill:#e3f2fd
    classDef textWidget fill:#f3e5f5
    classDef interface fill:#e8f5e8
    classDef data fill:#fff3e0
    
    class TimeSeries,BarChart,ScatterChart,RadarChart,RadialBarChart chartWidget
    class TextCustom,TextData textWidget
    class IWidget,IChartWidget,ITextWidget interface
    class DataSource,LTTB,SeriesManager data
```

## 4. 모노레포 구조 (10패키지)

### 4.1 패키지 의존성 그래프

```mermaid
graph TB
    Core["@e-torch/core<br/>(타입, 상수, 구독 모델)"]
    Utils["@e-torch/utils<br/>(LTTB, 포맷터)"]
    UI["@e-torch/ui<br/>(Shadcn/UI, 워터마크)"]
    DataSources["@e-torch/data-sources<br/>(KOSIS/ECOS 어댑터)"]
    State["@e-torch/state<br/>(Zustand + TanStack Query)"]
    Charts["@e-torch/charts<br/>(7가지 위젯 + LTTB)"]
    Dashboard["@e-torch/dashboard<br/>(편집 + 레이아웃)"]
    Panels["@e-torch/panels<br/>(리사이즈 패널 시스템)"]
    ServerAPI["@e-torch/server-api<br/>(결제 + 인증)"]
    ESLint["@e-torch/eslint-config<br/>(접근성 + 성능 규칙)"]
    
    Core --> Utils
    Core --> UI
    Core --> DataSources
    Core --> State
    Core --> ServerAPI
    
    Utils --> Charts
    Utils --> Dashboard
    
    UI --> Charts
    UI --> Dashboard
    UI --> Panels
    
    DataSources --> State
    State --> Charts
    Charts --> Dashboard
    Dashboard --> Panels
    
    ServerAPI --> State
    
    ESLint --> Core
    
    classDef corePackage fill:#e1f5fe
    classDef uiPackage fill:#f3e5f5
    classDef dataPackage fill:#e8f5e8
    classDef serverPackage fill:#fff3e0
    
    class Core,Utils corePackage
    class UI,Charts,Dashboard,Panels uiPackage
    class DataSources,State dataPackage
    class ServerAPI serverPackage
```

### 4.2 패키지별 핵심 책임

| 패키지 | 주요 책임 | 핵심 export | 특화 기능 |
|--------|----------|-------------|----------|
| @e-torch/core | 타입, 상수, 구독 모델 | PLAN_LIMITS, 위젯 타입 | 구독 모델 제한사항 |
| @e-torch/utils | LTTB, 포맷터, 유틸리티 | useLTTBSampling | 1000+ 포인트 다운샘플링 |
| @e-torch/ui | Shadcn/UI, 워터마크, 접근성 | AccessibleChart | WCAG 2.1 AA 준수 |
| @e-torch/data-sources | KOSIS/ECOS 어댑터 | useDataSource | 일별 1년 제한 검증 |
| @e-torch/state | Zustand + TanStack Query | useGlobalState | 서버/클라이언트 분리 |
| @e-torch/charts | 7가지 위젯 + LTTB | WidgetFactory | 위젯 팩토리 패턴 |
| @e-torch/dashboard | 편집 + 레이아웃 | DashboardEditor | react-grid-layout |
| @e-torch/panels | 리사이즈 패널 시스템 | ResizableWidgetEditor | 위젯 에디터 전용 |
| @e-torch/server-api | 결제 + 인증 + 서버액션 | PaymentActions | 토스페이먼츠 연동 |
| @e-torch/eslint-config | 접근성 + 성능 규칙 | eslintConfig | jsx-a11y 규칙 |

### 4.3 리사이즈 패널 시스템 (@e-torch/panels)

```typescript
// 위젯 에디터 전용 패널 시스템
export const ResizableWidgetEditor = () => (
  <ResizablePanelGroup direction="horizontal" className="h-full">
    {/* 좌측: 미리보기 영역 */}
    <ResizablePanel 
      defaultSize={60} 
      minSize={40} 
      className="min-w-[400px]"
    >
      <div className="flex flex-col h-full">
        <PreviewPanel />
        {/* 하단: 데이터 소스 패널 (조건부 표시) */}
        <ResizablePanel defaultSize={30} minSize={20}>
          <DataSourcePanel />
        </ResizablePanel>
      </div>
    </ResizablePanel>
    
    <ResizableHandle />
    
    {/* 우측: 옵션 패널 */}
    <ResizablePanel 
      defaultSize={40} 
      minSize={30} 
      className="min-w-[320px]"
    >
      <OptionsPanel />
    </ResizablePanel>
  </ResizablePanelGroup>
)

// 패널 크기 상태 저장
export const usePanelSizes = () => {
  const [sizes, setSizes] = useLocalStorage('widget-editor-panel-sizes', {
    preview: 60,
    options: 40,
    dataSource: 30
  })
  return { sizes, setSizes }
}
```

## 5. 성능 최적화

### 5.1 핵심 성능 목표

| 지표 | 목표값 | 측정 방법 | 구현 방법 |
|------|--------|----------|----------|
| **LCP** | < 2.5초 | Web Vitals API | 스켈레톤 UI + 지연 로딩 |
| **FID** | < 100ms | Web Vitals API | debounce 200ms/300ms |
| **CLS** | < 0.1 | Web Vitals API | 스켈레톤 UI 크기 고정 |
| **INP** | < 200ms | Web Vitals API | 편집 중 차트 렌더링 비활성화 |
| **차트 렌더링** | < 2초 | Performance API | LTTB 1000+ 임계값 |
| **대시보드 로딩** | < 2초 | 사용자 타이밍 | Suspense + 점진적 로딩 |
| **단일 지표 조회** | < 500ms | API 응답 모니터링 | SWR 캐싱 15분 |
| **메모리 사용량** | < 200MB | Performance API | 위젯 언마운트 시 정리 |

### 5.2 react-grid-layout 최적화

```typescript
// 정확한 디바운싱 시간 적용
const gridLayoutProps = {
  // 드래그: 빠른 반응성 우선 (200ms)
  onDragStop: debounce((layout) => {
    setChartRenderingEnabled(true)
    saveLayout(layout)
  }, 200),
  
  // 리사이즈: 정확성 우선 (300ms)  
  onResizeStop: debounce((layout) => {
    setChartRenderingEnabled(true)
    saveLayout(layout)
  }, 300),
  
  // 편집 중 성능 최적화
  onDragStart: () => setChartRenderingEnabled(false),
  onResizeStart: () => setChartRenderingEnabled(false),
  
  // 터치 최적화
  touchAction: 'manipulation',
  draggableHandle: '.drag-handle', // 44×44px 핸들
}
```

### 5.3 차트 렌더링 최적화

| 조건 | 임계값 | 최적화 방법 | 구현 위치 |
|------|--------|------------|----------|
| 데이터 포인트 | 1000+ | LTTB 다운샘플링 | @e-torch/charts |
| 위젯 개수 | 20+ | react-window 가상화 | @e-torch/dashboard |
| 편집 모드 | 드래그/리사이즈 중 | 차트 렌더링 비활성화 | 편집 컴포넌트 |
| 메모리 사용 | 200MB+ | 컴포넌트 언마운트 시 정리 | useEffect cleanup |

## 6. 속성 패널 반응형 시스템

### 6.1 화면 크기별 속성 패널 동작

| 화면 크기 | 패널 형태 | 크기 | 토글 방식 | 구현 컴포넌트 |
|----------|----------|------|----------|-------------|
| **데스크톱 1200px+** | 우측 고정 패널 | 320px (280-480px 조절) | 좌측 경계선 드래그 | ResizablePanel |
| **태블릿 768-1199px** | 하단 서랍 | 화면 높이의 60% | 하단 핸들 드래그 | Sheet |
| **모바일 ~767px** | 풀스크린 모달 | 전체 화면 | 플로팅 버튼 | Dialog |

```typescript
// 반응형 속성 패널 구현
const PropertyPanel = ({ children }: { children: React.ReactNode }) => {
  const isDesktop = useMediaQuery("(min-width: 1200px)")
  const isTablet = useMediaQuery("(min-width: 768px)")
  
  if (isDesktop) {
    return (
      <ResizablePanel 
        defaultSize={320} 
        minSize={280} 
        maxSize={480}
        className="border-l"
      >
        {children}
      </ResizablePanel>
    )
  }
  
  if (isTablet) {
    return (
      <Sheet>
        <SheetTrigger asChild>
          <Button variant="outline" className="fixed bottom-4 right-4">
            속성 편집
          </Button>
        </SheetTrigger>
        <SheetContent side="bottom" className="h-[60vh]">
          {children}
        </SheetContent>
      </Sheet>
    )
  }
  
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button 
          size="icon" 
          className="fixed bottom-4 right-4 w-12 h-12 rounded-full"
        >
          <Settings className="h-6 w-6" />
        </Button>
      </DialogTrigger>
      <DialogContent className="w-full h-full max-w-none p-0">
        {children}
      </DialogContent>
    </Dialog>
  )
}
```

## 7. 구독 모델 + 토스페이먼츠

### 7.1 권한 검증 (< 10ms 캐싱)

```typescript
// 5분 캐시로 권한 검증 최적화
export const useAuthWithCache = () => {
  const { data: session } = useQuery({
    queryKey: ['auth-session'],
    queryFn: () => supabase.auth.getSession(),
    staleTime: 5 * 60 * 1000, // 5분 캐시
    gcTime: 10 * 60 * 1000,   // 10분 가비지 컬렉션
  })
  return { 
    user: session?.user, 
    plan: session?.user?.subscription_plan || 'basic' 
  }
}
```

### 7.2 플랜별 제한

| 기능 | Basic (무료) | Pro (유료) | 기술 구현 |
|------|-------------|-----------|----------|
| **대시보드 수** | 3개 | 무제한 | DB 제약 + UI 진행바 |
| **위젯 수** | 6개/대시보드 | 무제한 | react-grid-layout 제한 |
| **경제지표** | 20개 (MVP) | 40개 | API 권한 검증 |
| **데이터 기간** | 최근 3년 | 전체 기간 | DatePicker 비활성화 |
| **ECOS 일별** | 최근 1년 | 최근 1년 | 소스별 제한 (플랜 무관) |
| **워터마크** | "E-Torch로 제작됨" | 제거 가능 | CSS 오버레이 |
| **대시보드 복사** | 불가 | 가능 | 버튼 비활성화 |
| **임베드 코드** | 불가 | 가능 | Pro 전용 기능 |
| **내보내기 품질** | 워터마크 + 1.6배율 | 고해상도 2배율 | Canvas 렌더링 설정 |

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
    embedCode: false,
    exportScale: 1.6
  },
  pro: {
    dashboards: Infinity,
    widgets: Infinity,
    indicators: 40,
    dataYears: Infinity,
    watermark: false, // 제거 가능
    copyDashboard: true,
    embedCode: true,
    exportScale: 2.0
  }
} as const

// ECOS 일별 제한은 플랜 무관 (소스 자체 제한)
export const DATA_SOURCE_LIMITS = {
  ecos: {
    daily: { maxYears: 1 }, // 모든 플랜 공통
    monthly: { maxYears: null },
    quarterly: { maxYears: null },
    annual: { maxYears: null }
  }
} as const
```

## 8. 워터마크 + 접근성

### 8.1 워터마크 시스템

```typescript
interface WatermarkProps {
  show: boolean
  onExport?: boolean
  position?: 'bottom-right' | 'bottom-left'
  opacity?: number
}

const Watermark = ({ 
  show, 
  onExport = false, 
  position = 'bottom-right',
  opacity = 0.7
}: WatermarkProps) => {
  if (!show) return null
  
  return (
    <div 
      className={cn(
        "absolute bottom-2 z-50 pointer-events-none",
        position === 'bottom-right' ? "right-2" : "left-2",
        "bg-black/10 px-2 py-1 rounded text-xs text-gray-600",
        onExport ? "print:block" : "print:hidden"
      )}
      style={{ opacity }}
      aria-hidden="true"
    >
      E-Torch로 제작됨
    </div>
  )
}

// 내보내기 시 워터마크 + 품질 설정
export const exportDashboard = async (format: 'png' | 'pdf') => {
  const { plan } = useSubscription()
  const scale = PLAN_LIMITS[plan].exportScale
  const includeWatermark = PLAN_LIMITS[plan].watermark
  
  const canvas = await html2canvas(dashboardRef.current, {
    scale,
    useCORS: true,
    backgroundColor: '#ffffff',
    // Pro 플랜: 고품질, Basic: 워터마크 포함
    ignoreElements: (element) => {
      return !includeWatermark && element.classList.contains('watermark')
    }
  })
  
  return canvas.toDataURL('image/png', plan === 'pro' ? 1.0 : 0.8)
}
```

### 8.2 WCAG 2.1 AA 접근성

| 요구사항 | 구현 방법 | 검증 도구 | 패키지 위치 |
|----------|----------|----------|------------|
| 차트 대체 텍스트 | aria-label + 데이터 테이블 | axe-core | @e-torch/ui |
| 키보드 네비게이션 | tabindex, Arrow키 지원 | 수동 테스트 | 모든 컴포넌트 |
| 색상 대비 4.5:1 | OKLCH 기반 자동 검증 | Colour Contrast Analyser | @e-torch/ui |
| 터치 타겟 44×44px | min-w/h-[44px] 클래스 | 시각적 확인 | @e-torch/ui |

```typescript
// 접근성 컴포넌트 (@e-torch/ui)
export const AccessibleChart = ({ 
  data, 
  chartType, 
  ariaLabel 
}: AccessibleChartProps) => {
  const summaryText = generateDataSummary(data)
  
  return (
    <div role="img" aria-label={ariaLabel}>
      {/* 시각적 차트 */}
      <ChartComponent data={data} type={chartType} />
      
      {/* 스크린 리더용 데이터 테이블 (숨김) */}
      <table className="sr-only" aria-label={`${ariaLabel} 데이터 테이블`}>
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
```

## 9. 모바일 최적화

### 9.1 편집 기능 제한사항

| 화면 크기 | 드래그 | 리사이즈 | 크기 조절 방법 | 구현 방식 |
|----------|-------|---------|-------------|----------|
| **데스크톱 1200px+** | ✅ 지원 | ✅ 핸들 | 모서리 드래그 | react-grid-layout 기본 |
| **태블릿 768-1199px** | ✅ 지원 | ✅ 확대 핸들 | 터치 최적화 핸들 (48×48px) | 터치 이벤트 |
| **모바일 ~767px** | ✅ 지원 | ❌ 비활성화 | 속성 패널 프리셋만 | 풀스크린 모달 |

```typescript
// 모바일 편집 제한 구현
const useResponsiveGridProps = () => {
  const isMobile = useMediaQuery("(max-width: 767px)")
  const isTablet = useMediaQuery("(min-width: 768px) and (max-width: 1199px)")
  
  return {
    isDraggable: true, // 모든 화면에서 드래그 지원
    isResizable: !isMobile, // 모바일에서만 리사이즈 비활성화
    
    // 터치 타겟 크기 조정
    ...(isTablet && {
      resizeHandles: ['se'], // 우하단만
      resizeHandle: <div className="w-12 h-12 absolute -bottom-2 -right-2" />
    }),
    
    // 모바일 대체 리사이즈
    ...(isMobile && {
      onLayoutChange: (layout) => {
        // 드래그로만 위치 변경, 크기는 속성 패널에서
      }
    })
  }
}

// 모바일 위젯 크기 프리셋
const MOBILE_WIDGET_PRESETS = {
  small: { w: 4, h: 2, label: '작게 (2행)' },
  medium: { w: 4, h: 3, label: '중간 (3행)' },
  large: { w: 4, h: 4, label: '크게 (4행)' },
  extra: { w: 4, h: 6, label: '매우 크게 (6행)' }
} as const

// 모바일 속성 패널에서 크기 선택
const MobileWidgetSizeSelector = ({ widgetId, currentSize, onChange }) => (
  <div className="space-y-2">
    <Label>위젯 크기</Label>
    <RadioGroup 
      value={`${currentSize.w}x${currentSize.h}`}
      onValueChange={(value) => {
        const preset = Object.values(MOBILE_WIDGET_PRESETS)
          .find(p => `${p.w}x${p.h}` === value)
        if (preset) onChange(preset)
      }}
    >
      {Object.entries(MOBILE_WIDGET_PRESETS).map(([key, preset]) => (
        <div key={key} className="flex items-center space-x-2">
          <RadioGroupItem 
            value={`${preset.w}x${preset.h}`} 
            id={key}
            className="min-w-[44px] min-h-[44px]" // 터치 타겟
          />
          <Label htmlFor={key} className="min-h-[44px] flex items-center">
            {preset.label}
          </Label>
        </div>
      ))}
    </RadioGroup>
  </div>
)
```

### 9.2 터치 인터페이스 최적화

| 설정 | 값 | 적용 범위 | CSS 구현 |
|------|----|---------|---------|
| 터치 타겟 | 44×44px | 모든 버튼, 링크 | min-w-[44px] min-h-[44px] |
| 터치 간격 | 8px | 인접 요소 | space-x-2 space-y-2 |
| 드래그 핸들 | 48×48px | 위젯 이동 핸들 | w-12 h-12 |
| 스와이프 감지 | 100px 이동 | 대시보드 네비게이션 | 터치 이벤트 |

## 10. 개발 도구 설정

### 10.1 ESLint 접근성 + 성능 규칙

```json
{
  "extends": [
    "plugin:jsx-a11y/recommended",
    "plugin:react-hooks/recommended"
  ],
  "rules": {
    "jsx-a11y/alt-text": "error",
    "jsx-a11y/aria-label": "error",
    "jsx-a11y/click-events-have-key-events": "error",
    "react-hooks/exhaustive-deps": "warn",
    "@typescript-eslint/no-unused-vars": "error"
  }
}
```

### 10.2 성능 모니터링

| 지표 | 목표값 | 경고 임계값 | 에러 임계값 | 자동 대응 |
|------|--------|------------|-----------|----------|
| LCP | < 2.5초 | > 3초 | > 5초 | 콘솔 경고 |
| FID/INP | < 200ms | > 300ms | > 500ms | 디바운싱 강화 |
| CLS | < 0.1 | > 0.2 | > 0.3 | 스켈레톤 UI 강화 |
| 메모리 | < 200MB | > 250MB | > 300MB | 가비지 컬렉션 강제 |
