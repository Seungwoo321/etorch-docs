# E-Torch 경제지표 대시보드 서비스 구현 파일 목록

E-Torch 프로젝트를 위한 모노레포 구조 파일 목록입니다. Turborepo와 PNPM을 사용하며, Shadcn/UI, Supabase 인증, Recharts 기반 차트를 구현합니다.

## 루트 설정 파일

1. `turbo.json` - Turborepo 설정
2. `package.json` - 루트 패키지 설정
3. `pnpm-workspace.yaml` - PNPM 워크스페이스 설정
4. `eslint.config.mjs` - ESLint 9 Flat Config 설정
5. `.gitignore` - Git 무시 파일 설정
6. `tsconfig.json` - 루트 TypeScript 설정
7. `README.md` - 프로젝트 설명
8. `.vscode/settings.json` - VSCode 설정
9. `.vscode/extensions.json` - VSCode 확장 추천
10. `.husky/pre-commit` - Git 훅 설정

## eslint-config 패키지

1. `packages/eslint-config/package.json`
2. `packages/eslint-config/base.mjs` - Standard JS/JSX 규칙과 TypeScript ESLint 규칙 통합
3. `packages/eslint-config/next.mjs` - Next.js와 React Hooks 규칙 통합

## packages/core

1. `packages/core/package.json`
2. `packages/core/tsconfig.json`
3. `packages/core/src/index.ts` - 패키지 엔트리 포인트
4. `packages/core/src/types/index.ts` - 타입 인덱스
5. `packages/core/src/types/chart.ts` - 차트 관련 타입
6. `packages/core/src/types/dashboard.ts` - 대시보드 관련 타입
7. `packages/core/src/types/data-source.ts` - 데이터 소스 타입
8. `packages/core/src/types/command.ts` - Undo/Redo 커맨드 패턴 타입
9. `packages/core/src/types/user.ts` - 사용자 관련 타입
10. `packages/core/src/types/auth.ts` - 인증 관련 타입
11. `packages/core/src/types/subscription.ts` - 구독 관련 타입
12. `packages/core/src/constants/index.ts` - 상수 인덱스
13. `packages/core/src/constants/api-endpoints.ts`
14. `packages/core/src/constants/chart-types.ts`
15. `packages/core/src/constants/storage-keys.ts`
16. `packages/core/src/constants/data-sources.ts`
17. `packages/core/src/constants/time-periods.ts`
18. `packages/core/src/constants/error-messages.ts`

## packages/ui

1. `packages/ui/package.json`
2. `packages/ui/tsconfig.json`
3. `packages/ui/index.ts` - 클라이언트 컴포넌트 엔트리 포인트
4. `packages/ui/server.ts` - 서버 컴포넌트 엔트리 포인트
5. `packages/ui/src/styles/globals.css` - Tailwind CSS 4 설정, OKLCH 색상 체계 및 테마 변수
6. `packages/ui/src/lib/utils.ts` - UI 유틸리티 함수
7. `packages/ui/src/components/ui/button.tsx`
8. `packages/ui/src/components/ui/card.tsx`
9. `packages/ui/src/components/ui/dialog.tsx`
10. `packages/ui/src/components/ui/select.tsx`
11. `packages/ui/src/components/ui/tabs.tsx`
12. `packages/ui/src/components/ui/input.tsx`
13. `packages/ui/src/components/ui/checkbox.tsx`
14. `packages/ui/src/components/ui/radio-group.tsx`
15. `packages/ui/src/components/ui/dropdown-menu.tsx`
16. `packages/ui/src/components/ui/popover.tsx`
17. `packages/ui/src/components/ui/tooltip.tsx`
18. `packages/ui/src/components/ui/table.tsx`
19. `packages/ui/src/components/ui/skeleton.tsx`
20. `packages/ui/src/components/ui/switch.tsx`
21. `packages/ui/src/components/ui/badge.tsx`
22. `packages/ui/src/components/ui/progress.tsx`
23. `packages/ui/src/components/ui/slider.tsx`
24. `packages/ui/src/components/a11y/skip-link.tsx`
25. `packages/ui/src/components/a11y/visually-hidden.tsx`
26. `packages/ui/src/components/a11y/focus-trap.tsx`
27. `packages/ui/src/components/a11y/keyboard-nav-menu.tsx`
28. `packages/ui/src/components/feedback/toast.tsx`
29. `packages/ui/src/components/feedback/alert.tsx`
30. `packages/ui/src/components/feedback/loading-spinner.tsx`
31. `packages/ui/src/components/feedback/error-display.tsx`
32. `packages/ui/src/components/layout/side-navigation.tsx`
33. `packages/ui/src/components/layout/header-navigation.tsx`
34. `packages/ui/src/components/layout/breadcrumb-navigation.tsx`
35. `packages/ui/src/server-components/button.server.tsx`
36. `packages/ui/src/server-components/card.server.tsx`
37. `packages/ui/src/server-components/tabs.server.tsx`
38. `packages/ui/src/server-components/toast.server.tsx`
39. `packages/ui/src/hooks/useToast.ts`
40. `packages/ui/src/hooks/useMediaQuery.ts`
41. `packages/ui/src/hooks/useCopyToClipboard.ts`
42. `packages/ui/src/hooks/useMounted.ts`
43. `packages/ui/src/hooks/useInView.ts`
44. `packages/ui/src/hooks/index.ts`
45. `packages/ui/src/components/ui/resizable.tsx` - Shadcn/UI 리사이즈 가능한 패널 컴포넌트

## packages/charts

1. `packages/charts/package.json`
2. `packages/charts/tsconfig.json`
3. `packages/charts/src/index.ts` - 클라이언트 컴포넌트 엔트리 포인트
4. `packages/charts/src/server/index.ts` - 서버 컴포넌트 엔트리 포인트
5. `packages/charts/src/components/ChartComponent.tsx`
6. `packages/charts/src/components/ChartRenderer.tsx`
7. `packages/charts/src/components/chart-types/TimeSeriesChart.tsx`
8. `packages/charts/src/components/chart-types/BarChart.tsx`
9. `packages/charts/src/components/chart-types/ScatterChart.tsx`
10. `packages/charts/src/components/chart-types/RadarChart.tsx`
11. `packages/charts/src/components/chart-types/RadialBarChart.tsx`
12. `packages/charts/src/components/chart-types/LineChart.tsx`
13. `packages/charts/src/components/chart-types/AreaChart.tsx`
14. `packages/charts/src/components/chart-types/PieChart.tsx`
15. `packages/charts/src/components/ChartControls.tsx`
16. `packages/charts/src/components/ChartLegend.tsx`
17. `packages/charts/src/components/ChartTooltip.tsx`
18. `packages/charts/src/components/ChartAxisLabel.tsx`
19. `packages/charts/src/components/AccessibleChartTable.tsx`
20. `packages/charts/src/components/ChartSkeleton.tsx`
21. `packages/charts/src/editor/ChartEditor.tsx`
22. `packages/charts/src/editor/ChartPreview.tsx`
23. `packages/charts/src/editor/OptionsPanel.tsx`
24. `packages/charts/src/editor/options/PanelOptions.tsx`
25. `packages/charts/src/editor/options/TooltipOptions.tsx`
26. `packages/charts/src/editor/options/LegendOptions.tsx`
27. `packages/charts/src/editor/options/AxisOptions.tsx`
28. `packages/charts/src/editor/options/StyleOptions.tsx`
29. `packages/charts/src/editor/options/ScatterOptions.tsx`
30. `packages/charts/src/editor/options/RadarOptions.tsx`
31. `packages/charts/src/editor/options/RadialBarOptions.tsx`
32. `packages/charts/src/editor/DataSourcePanel.tsx`
33. `packages/charts/src/editor/DataQueryCard.tsx`
34. `packages/charts/src/editor/TimeRangeSelector.tsx`
35. `packages/charts/src/editor/PeriodSelector.tsx`
36. `packages/charts/src/editor/ChartTypeSelector.tsx`
37. `packages/charts/src/utils/chartUtils.ts`
38. `packages/charts/src/utils/axisUtils.ts`
39. `packages/charts/src/commands/UpdateChartPropertyCommand.ts`
40. `packages/charts/src/commands/TransactionCommand.ts`
41. `packages/charts/src/commands/AddChartCommand.ts`
42. `packages/charts/src/commands/PositionChartCommand.ts`
43. `packages/charts/src/commands/StyleChartCommand.ts`
44. `packages/charts/src/server/ChartServerWrapper.tsx`
45. `packages/charts/src/server/ChartDataLoader.tsx`

## packages/dashboard

1. `packages/dashboard/package.json`
2. `packages/dashboard/tsconfig.json`
3. `packages/dashboard/src/index.ts` - 클라이언트 컴포넌트 엔트리 포인트
4. `packages/dashboard/src/server/index.ts` - 서버 컴포넌트 엔트리 포인트
5. `packages/dashboard/src/components/DashboardComponent.tsx`
6. `packages/dashboard/src/components/DashboardGrid.tsx` - react-grid-layout 구현
7. `packages/dashboard/src/components/GridItem.tsx`
8. `packages/dashboard/src/components/GridItemContent.tsx`
9. `packages/dashboard/src/components/GridItemHeader.tsx`
10. `packages/dashboard/src/components/ChartItem.tsx` - 차트 위젯
11. `packages/dashboard/src/components/TextItem.tsx` - 텍스트 위젯
12. `packages/dashboard/src/components/DashboardControls.tsx`
13. `packages/dashboard/src/components/TimeRangeControl.tsx`
14. `packages/dashboard/src/components/RefreshControl.tsx`
15. `packages/dashboard/src/components/ViewOptions.tsx`
16. `packages/dashboard/src/components/DashboardHeader.tsx`
17. `packages/dashboard/src/components/DashboardCard.tsx`
18. `packages/dashboard/src/components/DashboardList.tsx`
19. `packages/dashboard/src/components/EmptyDashboard.tsx`
20. `packages/dashboard/src/components/DashboardSkeleton.tsx`
21. `packages/dashboard/src/components/DashboardShareDialog.tsx`
22. `packages/dashboard/src/components/DashboardExportMenu.tsx`
23. `packages/dashboard/src/components/DateRangePicker.tsx` - 기간 선택기
24. `packages/dashboard/src/editor/DashboardEditor.tsx`
25. `packages/dashboard/src/editor/EditorToolbar.tsx`
26. `packages/dashboard/src/editor/SaveButton.tsx`
27. `packages/dashboard/src/editor/UndoRedoControls.tsx`
28. `packages/dashboard/src/editor/LayoutControls.tsx`
29. `packages/dashboard/src/editor/WidgetSelector.tsx`
30. `packages/dashboard/src/editor/AddChartButton.tsx`
31. `packages/dashboard/src/editor/AddTextButton.tsx`
32. `packages/dashboard/src/editor/DashboardSettingsForm.tsx`
33. `packages/dashboard/src/editor/TitleEditor.tsx`
34. `packages/dashboard/src/editor/DescriptionEditor.tsx`
35. `packages/dashboard/src/server/DashboardServerWrapper.tsx`
36. `packages/dashboard/src/server/DashboardLoader.tsx`
37. `packages/dashboard/src/utils/layoutUtils.ts`
38. `packages/dashboard/src/explore/ExploreList.tsx`
39. `packages/dashboard/src/explore/ExploreFilters.tsx`
40. `packages/dashboard/src/explore/ExploreCard.tsx`
41. `packages/dashboard/src/explore/SubscribeButton.tsx`

## packages/data-sources

1. `packages/data-sources/package.json`
2. `packages/data-sources/tsconfig.json`
3. `packages/data-sources/src/index.ts` - 클라이언트 엔트리 포인트
4. `packages/data-sources/src/server/index.ts` - 서버 엔트리 포인트
5. `packages/data-sources/src/connectors/index.ts`
6. `packages/data-sources/src/connectors/kosis-connector.ts`
7. `packages/data-sources/src/connectors/ecos-connector.ts`
8. `packages/data-sources/src/connectors/oecd-connector.ts`
9. `packages/data-sources/src/connectors/base-connector.ts`
10. `packages/data-sources/src/transforms/index.ts`
11. `packages/data-sources/src/transforms/time-series-transform.ts`
12. `packages/data-sources/src/transforms/interpolation.ts` - 결측치 처리
13. `packages/data-sources/src/transforms/synchronization.ts` - 기간 동기화
14. `packages/data-sources/src/transforms/downsampling.ts` - LTTB 알고리즘
15. `packages/data-sources/src/transforms/change-rate.ts` - 변화율 계산
16. `packages/data-sources/src/transforms/cumulative.ts` - 누적값 계산
17. `packages/data-sources/src/hooks/useChartData.ts`
18. `packages/data-sources/src/hooks/useIndicatorSearch.ts`
19. `packages/data-sources/src/hooks/useDataTransform.ts`
20. `packages/data-sources/src/hooks/index.ts`
21. `packages/data-sources/src/components/DataQueryBuilder.tsx`
22. `packages/data-sources/src/components/SourceSelector.tsx`
23. `packages/data-sources/src/components/SourceList.tsx`
24. `packages/data-sources/src/components/SourceInfo.tsx`
25. `packages/data-sources/src/components/IndicatorSelector.tsx`
26. `packages/data-sources/src/components/SearchIndicator.tsx`
27. `packages/data-sources/src/components/IndicatorTree.tsx`
28. `packages/data-sources/src/components/RecentIndicators.tsx`
29. `packages/data-sources/src/components/TransformControls.tsx`
30. `packages/data-sources/src/components/DataPreview.tsx`
31. `packages/data-sources/src/components/TransformOptions.tsx`
32. `packages/data-sources/src/components/PeriodSelector.tsx`
33. `packages/data-sources/src/cache/cache-manager.ts`
34. `packages/data-sources/src/cache/storage-adapter.ts`
35. `packages/data-sources/src/cache/memory-cache.ts`
36. `packages/data-sources/src/server/fetchChartData.ts`
37. `packages/data-sources/src/server/searchIndicators.ts`
38. `packages/data-sources/src/server/normalizeData.ts`

## packages/utils

1. `packages/utils/package.json`
2. `packages/utils/tsconfig.json`
3. `packages/utils/src/index.ts`
4. `packages/utils/src/formatters/date-formatter.ts`
5. `packages/utils/src/formatters/number-formatter.ts`
6. `packages/utils/src/formatters/currency-formatter.ts`
7. `packages/utils/src/formatters/percentage-formatter.ts`
8. `packages/utils/src/formatters/index.ts`
9. `packages/utils/src/validation/data-validator.ts`
10. `packages/utils/src/validation/chart-validator.ts`
11. `packages/utils/src/validation/dashboard-validator.ts`
12. `packages/utils/src/validation/index.ts`
13. `packages/utils/src/helpers/chart-helpers.ts`
14. `packages/utils/src/helpers/dashboard-helpers.ts`
15. `packages/utils/src/helpers/array-helpers.ts`
16. `packages/utils/src/helpers/object-helpers.ts`
17. `packages/utils/src/helpers/string-helpers.ts`
18. `packages/utils/src/helpers/date-helpers.ts`
19. `packages/utils/src/helpers/math-helpers.ts`
20. `packages/utils/src/helpers/index.ts`

## packages/state

1. `packages/state/package.json`
2. `packages/state/tsconfig.json`
3. `packages/state/src/index.ts`
4. `packages/state/src/stores/app-store.ts` - 앱 전역 상태
5. `packages/state/src/stores/dashboard-store.ts` - 대시보드 상태
6. `packages/state/src/stores/chart-editor-store.ts` - 차트 에디터 상태
7. `packages/state/src/stores/debug-store.ts` - 디버깅용 상태
8. `packages/state/src/stores/auth-store.ts` - Supabase 인증 상태
9. `packages/state/src/stores/subscription-store.ts` - 구독 상태
10. `packages/state/src/stores/notification-store.ts` - 알림 상태
11. `packages/state/src/hooks/use-dashboard.ts`
12. `packages/state/src/hooks/use-save-dashboard.ts`
13. `packages/state/src/hooks/use-time-range-sync.ts`
14. `packages/state/src/hooks/use-action-mutation.ts`
15. `packages/state/src/hooks/use-server-action.ts`
16. `packages/state/src/hooks/use-chart.ts`
17. `packages/state/src/hooks/use-auth.ts` - Supabase 인증 훅
18. `packages/state/src/hooks/use-subscription.ts`
19. `packages/state/src/hooks/index.ts`
20. `packages/state/src/services/event-bus.ts` - 이벤트 버스
21. `packages/state/src/services/history-manager.ts` - 히스토리 관리
22. `packages/state/src/services/time-travel-debugger.ts` - 시간 여행 디버거
23. `packages/state/src/services/auth-service.ts` - Supabase 인증 서비스
24. `packages/state/src/services/subscription-service.ts`
25. `packages/state/src/providers/query-provider.tsx` - TanStack Query
26. `packages/state/src/providers/zustand-provider.tsx` - Zustand
27. `packages/state/src/providers/auth-provider.tsx` - Supabase 인증
28. `packages/state/src/query-keys.ts` - TanStack Query 키

## packages/server-api

1. `packages/server-api/package.json`
2. `packages/server-api/tsconfig.json`
3. `packages/server-api/src/index.ts`
4. `packages/server-api/src/dashboard.ts`
5. `packages/server-api/src/chart.ts`
6. `packages/server-api/src/data-sources.ts`
7. `packages/server-api/src/auth.ts` - Supabase 인증 API
8. `packages/server-api/src/user.ts`
9. `packages/server-api/src/subscription.ts`
10. `packages/server-api/src/indicators.ts`
11. `packages/server-api/src/client/api-client.ts`
12. `packages/server-api/src/client/auth-api-client.ts` - Supabase 인증 클라이언트
13. `packages/server-api/src/client/dashboard-api-client.ts`
14. `packages/server-api/src/client/chart-api-client.ts`
15. `packages/server-api/src/client/data-api-client.ts`
16. `packages/server-api/src/middlewares/auth-middleware.ts` - Supabase 인증 미들웨어
17. `packages/server-api/src/middlewares/error-middleware.ts`

## apps/web

1. `apps/web/package.json`
2. `apps/web/tsconfig.json`
3. `apps/web/next.config.js`
4. `apps/web/app/layout.tsx`
5. `apps/web/app/page.tsx` - 홈페이지
6. `apps/web/app/(auth)/layout.tsx` - 인증 레이아웃
7. `apps/web/app/(auth)/login/page.tsx` - Supabase 로그인 페이지
8. `apps/web/app/(auth)/callback/page.tsx` - Supabase 콜백 페이지
9. `apps/web/app/(dashboard)/layout.tsx`
10. `apps/web/app/(dashboard)/dashboard/page.tsx` - 대시보드 목록
11. `apps/web/app/(dashboard)/dashboard/[id]/page.tsx` - 대시보드 상세
12. `apps/web/app/(dashboard)/dashboard/new/page.tsx` - 새 대시보드
13. `apps/web/app/(dashboard)/dashboard/[id]/edit/page.tsx` - 대시보드 편집
14. `apps/web/app/(dashboard)/explore/page.tsx` - 대시보드 탐색
15. `apps/web/app/(chart)/layout.tsx`
16. `apps/web/app/(chart)/chart-editor/[id]/page.tsx` - 차트 에디터
17. `apps/web/app/(chart)/chart/[id]/page.tsx` - 차트 상세
18. `apps/web/app/(profile)/layout.tsx`
19. `apps/web/app/(profile)/profile/settings/page.tsx` - 프로필 설정
20. `apps/web/app/(profile)/profile/subscription/page.tsx` - 구독 관리
21. `apps/web/app/(profile)/profile/notifications/page.tsx` - 알림 설정
22. `apps/web/app/actions/dashboard.ts` - 대시보드 서버 액션
23. `apps/web/app/actions/chart.ts` - 차트 서버 액션
24. `apps/web/app/actions/auth.ts` - Supabase 인증 서버 액션
25. `apps/web/app/actions/data-source.ts` - 데이터 소스 서버 액션
26. `apps/web/app/actions/subscription.ts` - 구독 서버 액션
27. `apps/web/app/api/charts/route.ts` - 차트 API 라우트
28. `apps/web/app/api/charts/[id]/route.ts`
29. `apps/web/app/api/dashboards/route.ts` - 대시보드 API 라우트
30. `apps/web/app/api/dashboards/[id]/route.ts`
31. `apps/web/app/api/data-sources/route.ts` - 데이터 소스 API
32. `apps/web/app/api/data-sources/[source]/[indicator]/route.ts`
33. `apps/web/app/api/subscriptions/route.ts` - 구독 API
34. `apps/web/app/api/users/[id]/route.ts` - 사용자 API
35. `apps/web/app/api/webhook/route.ts` - Supabase 웹훅
36. `apps/web/app/middleware.ts` - Next.js 미들웨어 (인증 및 권한 검증)
37. `apps/web/lib/supabase.ts` - Supabase 클라이언트 (V2)
38. `apps/web/lib/session.ts` - Supabase 세션 관리
39. `apps/web/components/landing/HeroSection.tsx`
40. `apps/web/components/landing/FeatureSection.tsx`
41. `apps/web/components/landing/PricingSection.tsx`
42. `apps/web/components/landing/TestimonialSection.tsx`
43. `apps/web/app/@modal/dashboard/[id]/page.tsx` - 대시보드 모달 (인터셉트 라우트)
44. `apps/web/app/@modal/chart/[id]/page.tsx` - 차트 모달 (인터셉트 라우트)
45. `apps/web/app/(dashboard)/@dashboard/info/page.tsx` - 대시보드 정보 탭 (병렬 라우트)
46. `apps/web/app/(dashboard)/@dashboard/share/page.tsx` - 대시보드 공유 탭 (병렬 라우트)

## Storybook 문서

1. `apps/storybook/package.json`
2. `apps/storybook/tsconfig.json`
3. `apps/storybook/.storybook/main.ts`
4. `apps/storybook/.storybook/preview.ts`
5. `apps/storybook/.storybook/theme.ts` - 커스텀 테마
6. `apps/storybook/stories/Introduction.stories.mdx`
7. `apps/storybook/stories/ui/Button.stories.tsx`
8. `apps/storybook/stories/ui/Card.stories.tsx`
9. `apps/storybook/stories/ui/Dialog.stories.tsx`
10. `apps/storybook/stories/ui/Select.stories.tsx`
11. `apps/storybook/stories/charts/TimeSeriesChart.stories.tsx`
12. `apps/storybook/stories/charts/BarChart.stories.tsx`
13. `apps/storybook/stories/dashboard/DashboardGrid.stories.tsx`
14. `apps/storybook/stories/dashboard/DashboardControls.stories.tsx`
15. `apps/storybook/stories/data-sources/DataQueryBuilder.stories.tsx`

이 파일 목록은 E-Torch 프로젝트의 모든 핵심 구성 요소를 포함하고 있으며, 프론트엔드 개발 경험을 바탕으로 실제 구현에 필요한 구조를 제시합니다.
