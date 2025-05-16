# 📘 UI/상태/컴포넌트 흐름 문서

## 주요 화면 흐름

1. 홈 → 사용자 대시보드 목록 → 대시보드 보기
2. 대시보드 보기 → 컴포넌트 클릭 → 차트 상세 편집
3. 대시보드 에디터 → 구성 저장 → 공유 또는 구독 설정

## 컴포넌트 맵

- `<DashboardGrid />`: 대시보드 UI 레이아웃 (react-grid-layout)
- `<ChartEditor />`: 차트 속성 편집기
- `<IndicatorSelector />`: 지표 선택 및 필터링

## Zustand 구조 요약

```json
{ dashboard: { layout: [], components: [], selectedChart: null, }, user: { info: {}, isAuthenticated: true } }

```