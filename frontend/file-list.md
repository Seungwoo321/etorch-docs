# E-Torch 경제지표 대시보드 서비스 구현 파일 목록 (완전판)

E-Torch 프로젝트를 위한 모노레포 구조 파일 목록입니다. 최신 기술 스택과 완전한 구현을 위한 모든 파일을 포함합니다.

## 서비스 핵심 특징

- **데이터 소스**: KOSIS(12개 지표) + ECOS(28개 지표) = 총 40개 경제지표
- **구독 모델**: Basic(무료, 20개 지표) + Pro(유료, 40개 지표)
- **위젯 시스템**: 7가지 타입 (TimeSeries, BarChart, ScatterChart, RadarChart, RadialBarChart, TextCustom, TextData)
- **기술 스택**: React 19 + Next.js 15 + Turborepo + Tailwind CSS 4 + Shadcn/UI 최신
- **다국어 지원**: 한국어/영어 동시 지원 ([locale] 라우팅)
- **결제 시스템**: 토스페이먼츠 최신 SDK
- **접근성**: WCAG 2.1 AA 수준 완전 준수

## 루트 설정 파일

1. `turbo.json` - Turborepo v2.2+ 설정
2. `package.json` - 루트 패키지 설정 (pnpm workspace)
3. `pnpm-workspace.yaml` - PNPM 워크스페이스 설정
4. `eslint.config.mjs` - ESLint 9 Flat Config 설정
5. `.gitignore` - Git 무시 파일 설정
6. `tsconfig.json` - 루트 TypeScript 설정
7. `README.md` - 프로젝트 설명
8. `vitest.workspace.ts` - Vitest 워크스페이스 설정
9. `playwright.config.ts` - E2E 테스트 설정
10. `.env.example` - 환경 변수 예시
11. `.env.local.example` - 로컬 환경 변수 예시
12. `docker-compose.yml` - 로컬 개발용 Docker
13. `Dockerfile` - 프로덕션 컨테이너
14. `.lintstagedrc.js` - 린트 스테이지 설정
15. `.vscode/settings.json` - VSCode 설정
16. `.vscode/extensions.json` - VSCode 확장 추천
17. `.husky/pre-commit` - Git 훅 설정

## GitHub Actions CI/CD

18. `.github/workflows/ci.yml` - CI 워크플로우
19. `.github/workflows/deploy.yml` - 배포 워크플로우
20. `.github/workflows/test.yml` - 테스트 워크플로우
21. `.github/workflows/release.yml` - 릴리즈 워크플로우

## packages/eslint-config

1. `package.json`
2. `base.mjs` - Standard JS/JSX 규칙과 TypeScript ESLint 규칙
3. `next.mjs` - Next.js와 React Hooks 규칙
4. `a11y.mjs` - 접근성 규칙 (jsx-a11y)
5. `react.mjs` - React 19 전용 규칙

## packages/core

**핵심 타입 정의 및 상수**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts` - 패키지 엔트리 포인트

### 타입 정의

5. `src/types/index.ts` - 타입 인덱스
6. `src/types/widget.ts` - 7가지 위젯 타입 정의
7. `src/types/dashboard.ts` - 대시보드 관련 타입
8. `src/types/data-source.ts` - KOSIS/ECOS 데이터 소스 타입
9. `src/types/subscription.ts` - Basic/Pro 구독 모델 타입
10. `src/types/auth.ts` - Supabase 인증 관련 타입
11. `src/types/chart.ts` - 차트 관련 타입
12. `src/types/command.ts` - Undo/Redo 커맨드 패턴 타입
13. `src/types/payment.ts` - 토스페이먼츠 결제 타입
14. `src/types/locale.ts` - 다국어 관련 타입

### 상수 정의

15. `src/constants/index.ts` - 상수 인덱스
16. `src/constants/subscription-plans.ts` - 구독 플랜별 제한사항
17. `src/constants/widget-types.ts` - 7가지 위젯 타입 상수
18. `src/constants/data-sources.ts` - KOSIS/ECOS 지표 목록 (40개)
19. `src/constants/chart-colors.ts` - 차트 색상 팔레트 (색맹 친화적)
20. `src/constants/locales.ts` - 다국어 지원 상수
21. `src/constants/api-endpoints.ts` - API 엔드포인트
22. `src/constants/performance-limits.ts` - 성능 제한 상수

## packages/ui

**Shadcn/UI 최신 버전 + Tailwind CSS 4 기반 UI 컴포넌트**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `index.ts` - 클라이언트 컴포넌트 엔트리
5. `server.ts` - 서버 컴포넌트 엔트리

### 스타일 시스템

6. `src/styles/globals.css` - Tailwind CSS 4 설정, OKLCH 색상 체계
7. `src/styles/themes.css` - 다크/라이트 모드 CSS 변수

### Shadcn/UI 기본 컴포넌트 (최신 버전)

8. `src/components/ui/button.tsx`
9. `src/components/ui/card.tsx`
10. `src/components/ui/dialog.tsx`
11. `src/components/ui/select.tsx`
12. `src/components/ui/tabs.tsx`
13. `src/components/ui/input.tsx`
14. `src/components/ui/checkbox.tsx`
15. `src/components/ui/radio-group.tsx`
16. `src/components/ui/dropdown-menu.tsx`
17. `src/components/ui/popover.tsx`
18. `src/components/ui/tooltip.tsx`
19. `src/components/ui/table.tsx`
20. `src/components/ui/skeleton.tsx`
21. `src/components/ui/switch.tsx`
22. `src/components/ui/badge.tsx`
23. `src/components/ui/progress.tsx`
24. `src/components/ui/slider.tsx`
25. `src/components/ui/resizable.tsx`

### Shadcn/UI 최신 추가 컴포넌트

26. `src/components/ui/breadcrumb.tsx`
27. `src/components/ui/pagination.tsx`
28. `src/components/ui/calendar.tsx`
29. `src/components/ui/date-picker.tsx`
30. `src/components/ui/command.tsx`
31. `src/components/ui/context-menu.tsx`
32. `src/components/ui/hover-card.tsx`
33. `src/components/ui/menubar.tsx`
34. `src/components/ui/navigation-menu.tsx`
35. `src/components/ui/scroll-area.tsx`
36. `src/components/ui/separator.tsx`
37. `src/components/ui/sheet.tsx`
38. `src/components/ui/toggle.tsx`
39. `src/components/ui/toggle-group.tsx`
40. `src/components/ui/form.tsx` - React Hook Form 통합

### 테마 시스템 (next-themes 사용)

41. `src/components/theme/theme-provider.tsx` - next-themes 래퍼
42. `src/components/theme/theme-toggle.tsx` - 다크/라이트 모드 토글
43. `src/components/theme/theme-customizer.tsx` - 테마 커스터마이징

### 접근성 컴포넌트 (WCAG 2.1 AA)

44. `src/components/a11y/skip-link.tsx`
45. `src/components/a11y/visually-hidden.tsx`
46. `src/components/a11y/focus-trap.tsx`
47. `src/components/a11y/keyboard-nav-menu.tsx`
48. `src/components/a11y/accessible-chart.tsx` - 차트 접근성 래퍼
49. `src/components/a11y/chart-data-table.tsx` - 차트 데이터 테이블
50. `src/components/a11y/skip-to-content.tsx` - 콘텐츠로 건너뛰기
51. `src/components/a11y/live-region.tsx` - 동적 콘텐츠 알림
52. `src/components/a11y/focus-manager.tsx` - 포커스 관리
53. `src/components/a11y/screen-reader-only.tsx` - 스크린 리더 전용
54. `src/components/a11y/high-contrast-mode.tsx` - 고대비 모드

### 피드백 컴포넌트

55. `src/components/feedback/toast.tsx`
56. `src/components/feedback/alert.tsx`
57. `src/components/feedback/loading-spinner.tsx`
58. `src/components/feedback/error-display.tsx`

### 레이아웃 컴포넌트

59. `src/components/layout/side-navigation.tsx`
60. `src/components/layout/header-navigation.tsx`
61. `src/components/layout/breadcrumb-navigation.tsx`

### 구독 모델 UI 컴포넌트

62. `src/components/subscription/subscription-badge.tsx` - Basic/Pro 플랜 배지
63. `src/components/subscription/limit-progress.tsx` - 사용량 진행바
64. `src/components/subscription/upgrade-prompt.tsx` - 업그레이드 안내
65. `src/components/subscription/plan-badge.tsx` - 플랜 배지
66. `src/components/subscription/feature-gate.tsx` - 기능 제한 게이트
67. `src/components/subscription/usage-chart.tsx` - 사용량 차트
68. `src/components/subscription/payment-history.tsx` - 결제 내역
69. `src/components/subscription/billing-cycle.tsx` - 결제 주기 관리
70. `src/components/subscription/cancellation-flow.tsx` - 해지 플로우

### 모바일 최적화 컴포넌트

71. `src/components/mobile/touch-optimized.tsx` - 터치 최적화
72. `src/components/mobile/swipe-gesture.tsx` - 스와이프 제스처

### 에러 바운더리 (React 19 기반)

73. `src/components/error/chart-error-boundary.tsx`
74. `src/components/error/widget-error-boundary.tsx`
75. `src/components/error/dashboard-error-boundary.tsx`
76. `src/components/error/root-error-boundary.tsx`

### 빈 상태 컴포넌트

77. `src/components/empty/empty-state.tsx` - 일반적인 빈 상태
78. `src/components/empty/empty-chart.tsx` - 차트 빈 상태
79. `src/components/empty/empty-dashboard.tsx` - 대시보드 빈 상태

### 가이드 시스템

80. `src/components/guide/tooltip.tsx` - 가이드 툴팁
81. `src/components/guide/spotlight.tsx` - 스포트라이트
82. `src/components/guide/progress-indicator.tsx` - 진행 표시

### 서버 컴포넌트

83. `src/server-components/button.server.tsx`
84. `src/server-components/card.server.tsx`
85. `src/server-components/tabs.server.tsx`

### 커스텀 훅

86. `src/hooks/useToast.ts`
87. `src/hooks/useMediaQuery.ts`
88. `src/hooks/useCopyToClipboard.ts`
89. `src/hooks/useMounted.ts`
90. `src/hooks/useInView.ts`
91. `src/hooks/useA11yAnnounce.ts` - 접근성 알림 훅
92. `src/hooks/index.ts`

## packages/charts

**7가지 위젯 타입 구현 + Recharts 최신 버전**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts` - 클라이언트 컴포넌트 엔트리
5. `src/server/index.ts` - 서버 컴포넌트 엔트리

### 위젯 팩토리 시스템

6. `src/components/WidgetFactory.tsx` - 7가지 위젯 타입 팩토리
7. `src/components/WidgetRenderer.tsx` - 위젯 렌더링 로직

### 차트형 위젯 (5가지)

8. `src/components/widgets/TimeSeriesWidget.tsx`
9. `src/components/widgets/BarChartWidget.tsx`
10. `src/components/widgets/ScatterChartWidget.tsx`
11. `src/components/widgets/RadarChartWidget.tsx`
12. `src/components/widgets/RadialBarChartWidget.tsx`

### 텍스트형 위젯 (2가지)

13. `src/components/widgets/TextCustomWidget.tsx` - 사용자 정의 텍스트
14. `src/components/widgets/TextDataWidget.tsx` - 데이터 기반 텍스트

### 차트 공통 컴포넌트

15. `src/components/ChartControls.tsx`
16. `src/components/ChartLegend.tsx`
17. `src/components/ChartTooltip.tsx`
18. `src/components/ChartAxisLabel.tsx`
19. `src/components/ChartSkeleton.tsx`
20. `src/components/AccessibleRecharts.tsx` - Recharts 접근성 래퍼

### 위젯 에디터

21. `src/editor/WidgetEditor.tsx` - 통합 위젯 에디터
22. `src/editor/WidgetPreview.tsx`
23. `src/editor/OptionsPanel.tsx`

### 에디터 옵션 패널

24. `src/editor/options/PanelOptions.tsx` - 공통 패널 옵션
25. `src/editor/options/TooltipOptions.tsx`
26. `src/editor/options/LegendOptions.tsx`
27. `src/editor/options/AxisOptions.tsx`
28. `src/editor/options/StyleOptions.tsx`
29. `src/editor/options/TextOptions.tsx` - 텍스트 위젯 전용

### 데이터 소스 패널

30. `src/editor/DataSourcePanel.tsx` - KOSIS/ECOS 통합 패널
31. `src/editor/DataQueryCard.tsx`
32. `src/editor/TimeRangeSelector.tsx` - 구독 플랜별 기간 제한
33. `src/editor/PeriodSelector.tsx`
34. `src/editor/WidgetTypeSelector.tsx` - 7가지 타입 선택기

### 커맨드 패턴 (Undo/Redo)

35. `src/commands/UpdateWidgetPropertyCommand.ts`
36. `src/commands/TransactionCommand.ts`
37. `src/commands/AddWidgetCommand.ts`
38. `src/commands/PositionWidgetCommand.ts`
39. `src/commands/StyleWidgetCommand.ts`
40. `src/commands/CommandHistory.ts`

### 접근성 지원

41. `src/a11y/ChartAnnouncer.tsx` - 차트 변경 안내
42. `src/a11y/KeyboardChartNav.tsx` - 차트 키보드 탐색

### 성능 최적화

43. `src/performance/ChartVirtualizer.tsx` - 차트 가상화
44. `src/performance/ChartMemoizer.tsx` - 차트 메모이제이션

### 유틸리티

45. `src/utils/chartUtils.ts`
46. `src/utils/widgetFactory.ts`
47. `src/utils/lttbSampling.ts` - LTTB 다운샘플링

### 서버 컴포넌트

48. `src/server/WidgetServerWrapper.tsx`
49. `src/server/ChartDataLoader.tsx`

## packages/dashboard

**react-grid-layout 기반 대시보드 시스템**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts` - 클라이언트 컴포넌트 엔트리
5. `src/server/index.ts` - 서버 컴포넌트 엔트리

### 대시보드 핵심 컴포넌트

6. `src/components/DashboardComponent.tsx`
7. `src/components/DashboardGrid.tsx` - react-grid-layout 구현
8. `src/components/GridItem.tsx`
9. `src/components/GridItemContent.tsx`
10. `src/components/GridItemHeader.tsx`

### 대시보드 컨트롤

11. `src/components/DashboardControls.tsx`
12. `src/components/TimeRangeControl.tsx` - 구독 플랜별 기간 제한
13. `src/components/RefreshControl.tsx`
14. `src/components/ViewOptions.tsx`

### 대시보드 관리

15. `src/components/DashboardHeader.tsx`
16. `src/components/DashboardCard.tsx`
17. `src/components/DashboardList.tsx`
18. `src/components/EmptyDashboard.tsx`
19. `src/components/DashboardSkeleton.tsx`

### 공유 및 내보내기

20. `src/components/DashboardShareDialog.tsx`
21. `src/components/DashboardExportMenu.tsx` - 구독 플랜별 차별화
22. `src/components/WatermarkOverlay.tsx` - Basic 플랜 워터마크

### 대시보드 에디터

23. `src/editor/DashboardEditor.tsx`
24. `src/editor/EditorToolbar.tsx`
25. `src/editor/SaveButton.tsx`
26. `src/editor/UndoRedoControls.tsx`
27. `src/editor/LayoutControls.tsx`
28. `src/editor/WidgetSelector.tsx` - 7가지 위젯 타입
29. `src/editor/DashboardSettingsForm.tsx`

### 대시보드 탐색

30. `src/explore/ExploreList.tsx`
31. `src/explore/ExploreFilters.tsx`
32. `src/explore/ExploreCard.tsx`
33. `src/explore/SubscribeButton.tsx` - Pro 플랜 전용

### 성능 최적화

34. `src/performance/GridVirtualizer.tsx` - 그리드 가상화

### 서버 컴포넌트

35. `src/server/DashboardServerWrapper.tsx`
36. `src/server/DashboardLoader.tsx`

### 유틸리티

37. `src/utils/layoutUtils.ts`
38. `src/utils/gridLayout.ts`

## packages/data-sources

**KOSIS/ECOS API 통합 및 구독 모델 필터링**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts` - 클라이언트 엔트리
5. `src/server/index.ts` - 서버 엔트리

### 데이터 커넥터

6. `src/connectors/index.ts`
7. `src/connectors/kosis-connector.ts` - 통계청 12개 지표
8. `src/connectors/ecos-connector.ts` - 한국은행 28개 지표
9. `src/connectors/base-connector.ts`

### 데이터 변환

10. `src/transforms/index.ts`
11. `src/transforms/time-series-transform.ts`
12. `src/transforms/interpolation.ts` - 결측치 처리
13. `src/transforms/synchronization.ts` - 기간 동기화
14. `src/transforms/downsampling.ts` - LTTB 알고리즘
15. `src/transforms/change-rate.ts` - YoY/MoM/QoQ 변화율
16. `src/transforms/cumulative.ts` - 누적값 계산

### 데이터 검증 및 정규화

17. `src/validators/kosis-validator.ts` - KOSIS 데이터 검증
18. `src/validators/ecos-validator.ts` - ECOS 데이터 검증
19. `src/validators/schema-validator.ts` - 스키마 검증
20. `src/normalizers/data-normalizer.ts` - 데이터 정규화

### 데이터 처리

21. `src/processors/batch-processor.ts` - 배치 처리
22. `src/transformers/data-transformer.ts` - 데이터 변환

### 구독 모델 필터링

23. `src/filters/subscription-filter.ts` - Basic/Pro 플랜별 제한
24. `src/filters/period-filter.ts` - 데이터 기간 제한
25. `src/filters/indicator-filter.ts` - 지표 접근 권한

### React 훅

26. `src/hooks/useChartData.ts`
27. `src/hooks/useIndicatorSearch.ts`
28. `src/hooks/useDataTransform.ts`
29. `src/hooks/useSubscriptionLimits.ts` - 구독 제한 확인
30. `src/hooks/index.ts`

### UI 컴포넌트

31. `src/components/DataQueryBuilder.tsx`
32. `src/components/SourceSelector.tsx` - KOSIS/ECOS 선택
33. `src/components/IndicatorSelector.tsx`
34. `src/components/SearchIndicator.tsx`
35. `src/components/IndicatorTree.tsx`
36. `src/components/RecentIndicators.tsx`
37. `src/components/TransformControls.tsx`
38. `src/components/DataPreview.tsx`
39. `src/components/PeriodSelector.tsx` - 구독 플랜별 제한 표시

### 캐싱 시스템

40. `src/cache/cache-manager.ts`
41. `src/cache/storage-adapter.ts`
42. `src/cache/memory-cache.ts`

### 서버 함수

43. `src/server/fetchChartData.ts`
44. `src/server/searchIndicators.ts`
45. `src/server/normalizeData.ts`

## packages/utils

**공통 유틸리티 함수 및 성능 최적화**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts`

### 포맷터

5. `src/formatters/date-formatter.ts` - 다국어 날짜 형식
6. `src/formatters/number-formatter.ts` - 한국어/영어 숫자 형식
7. `src/formatters/currency-formatter.ts` - 통화 형식
8. `src/formatters/percentage-formatter.ts` - 퍼센트 형식
9. `src/formatters/index.ts`

### 검증

10. `src/validation/data-validator.ts`
11. `src/validation/chart-validator.ts`
12. `src/validation/dashboard-validator.ts`
13. `src/validation/subscription-validator.ts` - 구독 플랜 검증
14. `src/validation/index.ts`

### 헬퍼 함수

15. `src/helpers/chart-helpers.ts`
16. `src/helpers/dashboard-helpers.ts`
17. `src/helpers/array-helpers.ts`
18. `src/helpers/object-helpers.ts`
19. `src/helpers/string-helpers.ts`
20. `src/helpers/date-helpers.ts`
21. `src/helpers/math-helpers.ts`
22. `src/helpers/subscription-helpers.ts` - 구독 관련 유틸리티
23. `src/helpers/index.ts`

### 성능 최적화

24. `src/performance/debounce.ts` - 200ms/300ms 디바운싱
25. `src/performance/throttle.ts` - 스로틀링
26. `src/performance/memory-manager.ts` - 메모리 관리

### LTTB 알고리즘

27. `src/algorithms/lttb.ts` - LTTB 알고리즘 구현
28. `src/algorithms/lttb-helpers.ts` - LTTB 헬퍼 함수

### Web Workers

29. `src/workers/lttb-worker.ts` - LTTB Web Worker
30. `src/workers/data-processor-worker.ts` - 데이터 처리 Worker

### 스트리밍

31. `src/streaming/data-stream.ts` - 데이터 스트리밍

### 에러 처리

32. `src/errors/error-recovery.ts` - 에러 복구 전략
33. `src/errors/fallback-data.ts` - 폴백 데이터

### 모니터링

34. `src/monitoring/performance-monitor.ts` - 성능 지표 수집
35. `src/monitoring/memory-monitor.ts` - 메모리 사용량 모니터링

## packages/state

**Zustand v5 + TanStack Query v5 상태 관리**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts`

### Zustand 스토어

5. `src/stores/app-store.ts` - 앱 전역 상태
6. `src/stores/dashboard-store.ts` - 대시보드 상태
7. `src/stores/widget-editor-store.ts` - 위젯 에디터 상태
8. `src/stores/auth-store.ts` - Supabase 인증 상태
9. `src/stores/subscription-store.ts` - 구독 플랜 상태
10. `src/stores/notification-store.ts` - 알림 상태
11. `src/stores/debug-store.ts` - 디버깅용 상태

### React 훅 (React 19 기능 활용)

12. `src/hooks/use-dashboard.ts`
13. `src/hooks/use-save-dashboard.ts`
14. `src/hooks/use-time-range-sync.ts`
15. `src/hooks/use-widget.ts`
16. `src/hooks/use-auth.ts` - Supabase 인증 훅
17. `src/hooks/use-subscription.ts` - 구독 관리 훅
18. `src/hooks/use-server-action.ts`
19. `src/hooks/use-optimistic-updates.ts` - useOptimistic 활용
20. `src/hooks/use-action-state.ts` - useActionState 활용
21. `src/hooks/index.ts`

### 서비스

22. `src/services/event-bus.ts` - 이벤트 버스
23. `src/services/history-manager.ts` - Undo/Redo 히스토리
24. `src/services/time-travel-debugger.ts` - 시간 여행 디버거
25. `src/services/auth-service.ts` - Supabase 인증 서비스
26. `src/services/subscription-service.ts` - 구독 관리 서비스

### 프로바이더

27. `src/providers/query-provider.tsx` - TanStack Query v5
28. `src/providers/zustand-provider.tsx` - Zustand
29. `src/providers/auth-provider.tsx` - Supabase 인증
30. `src/providers/subscription-provider.tsx` - 구독 상태

### 캐시 관리

31. `src/cache/query-cache.ts` - TanStack Query 캐시 설정
32. `src/cache/subscription-cache.ts` - 구독 상태 캐싱

### 쿼리 키

33. `src/query-keys.ts` - TanStack Query 키 관리

## packages/server-api

**서버 API 및 토스페이먼츠 최신 SDK 통합**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `src/index.ts`

### API 함수

5. `src/dashboard.ts` - 대시보드 CRUD
6. `src/widget.ts` - 위젯 CRUD
7. `src/data-sources.ts` - KOSIS/ECOS 데이터
8. `src/auth.ts` - Supabase 인증 API
9. `src/user.ts` - 사용자 관리
10. `src/subscription.ts` - 구독 관리
11. `src/payment.ts` - 토스페이먼츠 결제
12. `src/indicators.ts` - 경제지표 메타데이터

### API 클라이언트

13. `src/client/api-client.ts` - 기본 API 클라이언트
14. `src/client/auth-api-client.ts` - Supabase 인증 클라이언트
15. `src/client/dashboard-api-client.ts`
16. `src/client/widget-api-client.ts`
17. `src/client/data-api-client.ts`
18. `src/client/payment-api-client.ts` - 토스페이먼츠 클라이언트

### 미들웨어

19. `src/middlewares/auth-middleware.ts` - Supabase 인증 미들웨어
20. `src/middlewares/subscription-middleware.ts` - 구독 권한 검증
21. `src/middlewares/error-middleware.ts`

## apps/web

**Next.js 15 App Router 메인 애플리케이션**

1. `package.json`
2. `tsconfig.json`
3. `vitest.config.ts`
4. `next.config.js` - React Compiler 설정 포함
5. `middleware.ts` - 언어/인증/구독 검증 미들웨어
6. `.env.example` - Next.js 환경 변수 예시

### 루트 레이아웃

7. `app/layout.tsx` - 루트 레이아웃
8. `app/page.tsx` - 언어 리디렉션 페이지
9. `app/not-found.tsx` - 404 페이지
10. `app/global-error.tsx` - 전역 에러 페이지

### 다국어 라우팅 ([locale])

11. `app/[locale]/layout.tsx` - 언어별 레이아웃 (next-intl)
12. `app/[locale]/page.tsx` - 홈페이지
13. `app/[locale]/not-found.tsx` - 언어별 404

### 인증 라우트 그룹 (auth)

14. `app/[locale]/(auth)/layout.tsx` - 인증 레이아웃
15. `app/[locale]/(auth)/login/page.tsx` - SNS 로그인 페이지
16. `app/[locale]/(auth)/callback/page.tsx` - OAuth 콜백

### 대시보드 라우트 그룹 (dashboard)

17. `app/[locale]/(dashboard)/layout.tsx` - 대시보드 레이아웃
18. `app/[locale]/(dashboard)/dashboard/page.tsx` - 대시보드 목록
19. `app/[locale]/(dashboard)/dashboard/[id]/page.tsx` - 대시보드 상세
20. `app/[locale]/(dashboard)/dashboard/new/page.tsx` - 새 대시보드
21. `app/[locale]/(dashboard)/dashboard/[id]/edit/page.tsx` - 대시보드 편집
22. `app/[locale]/(dashboard)/explore/page.tsx` - 대시보드 탐색

### 병렬/인터셉트 라우트

23. `app/[locale]/(dashboard)/@info/page.tsx` - 대시보드 정보 탭
24. `app/[locale]/(dashboard)/@share/page.tsx` - 대시보드 공유 탭
25. `app/[locale]/@modal/(.)dashboard/[id]/page.tsx` - 대시보드 모달
26. `app/[locale]/@modal/(.)widget/[id]/page.tsx` - 위젯 모달

### 위젯 라우트 그룹 (widget)

27. `app/[locale]/(widget)/layout.tsx` - 위젯 레이아웃
28. `app/[locale]/(widget)/widget-editor/[id]/page.tsx` - 위젯 에디터
29. `app/[locale]/(widget)/widget/[id]/page.tsx` - 위젯 상세

### 구독 라우트 그룹 (subscription)

30. `app/[locale]/(subscription)/layout.tsx` - 구독 레이아웃
31. `app/[locale]/(subscription)/subscription/upgrade/page.tsx` - Pro 업그레이드
32. `app/[locale]/(subscription)/subscription/billing/page.tsx` - 결제 관리
33. `app/[locale]/(subscription)/subscription/trial/page.tsx` - 7일 무료 체험
34. `app/[locale]/(subscription)/subscription/payment/success/page.tsx` - 결제 성공
35. `app/[locale]/(subscription)/subscription/payment/failure/page.tsx` - 결제 실패
36. `app/[locale]/(subscription)/subscription/payment/cancel/page.tsx` - 결제 취소

### 제한 도달 안내 페이지

37. `app/[locale]/(subscription)/subscription/limit-reached/dashboard/page.tsx` - 대시보드 한도
38. `app/[locale]/(subscription)/subscription/limit-reached/widget/page.tsx` - 위젯 한도
39. `app/[locale]/(subscription)/subscription/limit-reached/data-period/page.tsx` - 기간 제한

### 프로필 라우트 그룹 (profile)

40. `app/[locale]/(profile)/layout.tsx` - 프로필 레이아웃
41. `app/[locale]/(profile)/profile/settings/page.tsx` - 프로필 설정
42. `app/[locale]/(profile)/profile/notifications/page.tsx` - 알림 설정

### 접근성 페이지

43. `app/[locale]/accessibility/page.tsx` - 접근성 가이드

### 서버 액션 (React 19 기능 활용)

44. `app/actions/dashboard.ts` - 대시보드 서버 액션
45. `app/actions/widget.ts` - 위젯 서버 액션
46. `app/actions/auth.ts` - Supabase 인증 서버 액션
47. `app/actions/subscription.ts` - 구독 서버 액션
48. `app/actions/payment.ts` - 토스페이먼츠 서버 액션
49. `app/actions/optimistic-actions.ts` - 낙관적 업데이트 액션

### API 라우트

50. `app/api/widgets/route.ts` - 위젯 API
51. `app/api/widgets/[id]/route.ts`
52. `app/api/dashboards/route.ts` - 대시보드 API
53. `app/api/dashboards/[id]/route.ts`
54. `app/api/data-sources/route.ts` - 데이터 소스 API
55. `app/api/data-sources/[source]/[indicator]/route.ts`
56. `app/api/subscriptions/route.ts` - 구독 API
57. `app/api/payments/route.ts` - 토스페이먼츠 API
58. `app/api/payments/webhook/route.ts` - 결제 웹훅
59. `app/api/payments/billing-webhook/route.ts` - 빌링 웹훅
60. `app/api/users/[id]/route.ts` - 사용자 API

### 라이브러리 및 설정

61. `lib/supabase/client.ts` - Supabase 클라이언트 (SSR 패키지)
62. `lib/supabase/server.ts` - Supabase 서버
63. `lib/supabase/middleware.ts` - Supabase 미들웨어
64. `lib/payments/toss-client.ts` - 토스페이먼츠 클라이언트
65. `lib/payments/toss-server.ts` - 토스페이먼츠 서버
66. `lib/payments/billing-webhook.ts` - 빌링 웹훅
67. `lib/session.ts` - Supabase 세션 관리
68. `lib/auth-helpers.ts` - 인증 헬퍼 함수

### 다국어 지원 (next-intl v3)

69. `lib/i18n/config.ts` - next-intl 설정
70. `lib/i18n/request.ts` - 요청별 로케일
71. `lib/i18n/a11y-messages.ts` - 접근성 메시지 번역
72. `messages/ko.json` - 한국어 번역
73. `messages/en.json` - 영어 번역

### 랜딩 페이지 컴포넌트

74. `components/landing/HeroSection.tsx`
75. `components/landing/FeatureSection.tsx`
76. `components/landing/PricingSection.tsx` - Basic/Pro 플랜 비교
77. `components/landing/TestimonialSection.tsx`

### 온보딩 시스템

78. `components/onboarding/WelcomeFlow.tsx` - 환영 플로우
79. `components/onboarding/TutorialStep.tsx` - 튜토리얼 단계
80. `components/onboarding/InterestSelector.tsx` - 관심 분야 선택
81. `components/onboarding/TemplateRecommendation.tsx` - 템플릿 추천

### 구독 관련 컴포넌트

82. `components/subscription/PlanComparison.tsx` - Basic vs Pro 비교
83. `components/subscription/UsageProgress.tsx` - 사용량 진행바
84. `components/subscription/UpgradePrompt.tsx` - 업그레이드 안내
85. `components/subscription/PaymentForm.tsx` - 토스페이먼츠 폼
86. `components/subscription/TrialBanner.tsx` - 체험판 배너
87. `components/subscription/UpgradeModal.tsx` - 업그레이드 모달

## apps/storybook

**컴포넌트 문서화 (Storybook 최신 버전)**

1. `package.json`
2. `tsconfig.json`
3. `.storybook/main.ts`
4. `.storybook/preview.ts`
5. `.storybook/theme.ts` - E-Torch 커스텀 테마

### 도입부

6. `stories/Introduction.stories.mdx` - 서비스 소개

### UI 컴포넌트 스토리

7. `stories/ui/Button.stories.tsx`
8. `stories/ui/Card.stories.tsx`
9. `stories/ui/Dialog.stories.tsx`
10. `stories/ui/Select.stories.tsx`
11. `stories/ui/Form.stories.tsx` - React Hook Form 통합
12. `stories/ui/DatePicker.stories.tsx`
13. `stories/ui/Command.stories.tsx`

### 테마 시스템 스토리

14. `stories/theme/ThemeToggle.stories.tsx`
15. `stories/theme/ColorPalette.stories.tsx`

### 위젯 스토리

16. `stories/widgets/TimeSeriesWidget.stories.tsx`
17. `stories/widgets/BarChartWidget.stories.tsx`
18. `stories/widgets/ScatterChartWidget.stories.tsx`
19. `stories/widgets/RadarChartWidget.stories.tsx`
20. `stories/widgets/RadialBarChartWidget.stories.tsx`
21. `stories/widgets/TextCustomWidget.stories.tsx`
22. `stories/widgets/TextDataWidget.stories.tsx`

### 대시보드 스토리

23. `stories/dashboard/DashboardGrid.stories.tsx`
24. `stories/dashboard/DashboardControls.stories.tsx`

### 데이터 소스 스토리

25. `stories/data-sources/DataQueryBuilder.stories.tsx`

### 구독 모델 스토리

26. `stories/subscription/PlanComparison.stories.tsx`
27. `stories/subscription/UsageProgress.stories.tsx`

### 접근성 스토리

28. `stories/a11y/AccessibilityGuide.stories.tsx`
29. `stories/a11y/KeyboardNavigation.stories.tsx`

## 테스트 파일 구조

### 각 패키지별 테스트 (예시: packages/ui)

- `__tests__/components/ui/Button.test.tsx`
- `__tests__/components/theme/ThemeProvider.test.tsx`
- `__tests__/hooks/useToast.test.ts`

### E2E 테스트 (apps/web)

- `e2e/auth.spec.ts` - 로그인/로그아웃 플로우
- `e2e/dashboard.spec.ts` - 대시보드 생성/편집
- `e2e/subscription.spec.ts` - 구독/결제 플로우
- `e2e/accessibility.spec.ts` - 접근성 테스트

이 파일 목록은 E-Torch 프로젝트의 **완전한 구현**을 위한 모든 파일을 포함하고 있으며, 최신 기술 스택과 실제 운영 요구사항을 모두 반영합니다.
