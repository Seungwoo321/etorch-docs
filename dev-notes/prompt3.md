# E-Torch 프로젝트 완전 구현을 위한 포괄적 프롬프트 체계

기획서, 아키텍처 문서, 기능명세를 모두 충족하는 전체 E-Torch 서비스를 개발하기 위한 포괄적인 프롬프트 체계를 제시합니다. 이 프롬프트 세트는 모듈식으로 구성되어 있으며, 모든 필수 컴포넌트를 다룹니다.

## A. 기반 구조 개발 (Foundation)

### A1. 핵심 타입 정의

```
# 모듈 개발 요청: 핵심 타입 정의

파일: packages/core/src/types/index.ts
의존성: 없음

## 기능 요구사항
- 프로젝트 전체에서 사용할 핵심 타입 정의
- 차트, 대시보드, 데이터 모델 등 모든 도메인 타입 포함
- 확장 가능한 타입 구조 설계

## 입력/출력 인터페이스
- 타입스크립트 인터페이스 및 타입 정의 모듈

## 구현 지침
- 기획서의 데이터 모델 참조
- 차트 컴포넌트 타입(TimeSeries, BarChart, ScatterChart, RadarChart, RadialBarChart)
- 대시보드 및 위젯 관련 타입(Dashboard, DashboardItem, Layout)
- 데이터 소스 관련 타입(DataSource, Indicator, Transform)
- 사용자 및 인증 관련 타입(User, Subscription)
- 모든 API 요청/응답 타입
```

### A2. 패키지별 타입 내보내기

```
# 모듈 개발 요청: 패키지별 타입 내보내기

파일: packages/core/src/index.ts
의존성: ./types

## 기능 요구사항
- 모든 타입을 적절히 그룹화하여 내보내기
- 다른 패키지에서 쉽게 임포트할 수 있는 구조
- 타입 안전성 보장

## 구현 지침
- 차트 관련 타입(interfaces/charts.ts)
- 대시보드 관련 타입(interfaces/dashboard.ts)
- 데이터 소스 관련 타입(interfaces/data-sources.ts)
- 사용자 및 인증 관련 타입(interfaces/auth.ts)
- API 관련 타입(interfaces/api.ts)
- 유틸리티 타입(interfaces/utils.ts)
```

### A3. 전역 상수 및 설정

```
# 모듈 개발 요청: 전역 상수 및 설정

파일: packages/core/src/constants/index.ts
의존성: ../types

## 기능 요구사항
- 프로젝트 전체에서 사용할 상수 정의
- API 엔드포인트, 스토리지 키 등 중앙 관리
- 환경별 설정 지원(개발, 프로덕션)

## 구현 지침
- API 엔드포인트 상수
- 데이터 소스별 설정(KOSIS, ECOS, OECD)
- 로컬 스토리지 키
- 차트 기본 설정값
- 대시보드 기본 레이아웃 값
- 주기별 포맷 설정
```

## B. UI 패키지 컴포넌트 개발 (UI Components)

### B1. Tailwind CSS 설정

```
# 모듈 개발 요청: Tailwind CSS 설정

파일: packages/ui/src/styles/globals.css
의존성: tailwindcss

## 기능 요구사항
- E-Torch 디자인 시스템에 맞는 Tailwind CSS 4 설정
- 테마 변수 및 색상 설정
- 다크 모드 지원

## 구현 지침
- design-system.md의 3.1 globals.css 설정 참조
- OKLCH 색상 공간 사용
- 브랜드 색상, 중립 색상, 차트 색상 등 모든 색상 변수 정의
- 라이트/다크 모드 변수 설정
- 기본 스타일 및 테마 변수 설정
```

### B2. 기본 UI 컴포넌트 구현 (17개 컴포넌트)

```
# 모듈 개발 요청: 기본 UI 컴포넌트 - 버튼

파일: packages/ui/src/components/ui/button.tsx
의존성: @e-torch/core/types

## 기능 요구사항
- 다양한 스타일 변형을 지원하는 버튼 컴포넌트
- 접근성 고려한 구현
- Shadcn/UI 패턴 따르기

## 구현 지침
- design-system.md의 6.3 버튼 컴포넌트 예시 참조
- 'use client' 지시어 포함
- variant: default, destructive, outline, secondary, ghost, link
- size: default, sm, lg, icon
- React.forwardRef 사용
```

```
# 모듈 개발 요청: 기본 UI 컴포넌트 세트 (나머지 16개)

파일: packages/ui/src/components/ui/[component-name].tsx
의존성: @e-torch/core/types

## 기능 요구사항
- 프로젝트에 필요한 모든 기본 UI 컴포넌트 구현
- 일관된 디자인 시스템 적용
- 접근성 고려

## 구현 지침
- design-system.md 및 ui-requirements.md 참조
- 'use client' 지시어 포함
- 각 컴포넌트별 적절한 variant 및 size 구현
- React.forwardRef 사용
- 구현할 컴포넌트 목록:
  1. card.tsx (Card, CardHeader, CardTitle, CardContent, CardFooter)
  2. input.tsx
  3. select.tsx (Select, SelectTrigger, SelectValue, SelectContent, SelectItem)
  4. checkbox.tsx
  5. dialog.tsx (Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogFooter)
  6. dropdown-menu.tsx
  7. tabs.tsx (Tabs, TabsList, TabsTrigger, TabsContent)
  8. toast.tsx (Toast, ToastProvider, ToastViewport)
  9. tooltip.tsx
  10. progress.tsx
  11. badge.tsx
  12. avatar.tsx
  13. alert.tsx
  14. alert-dialog.tsx
  15. form.tsx (Form, FormField, FormItem, FormLabel, FormMessage)
  16. skeleton.tsx
```

### B3. 레이아웃 컴포넌트

```
# 모듈 개발 요청: 레이아웃 컴포넌트

파일: packages/ui/src/components/layout/[component-name].tsx
의존성: 
- @e-torch/core/types
- ../ui/*

## 기능 요구사항
- 애플리케이션 레이아웃을 위한 컴포넌트 구현
- 반응형 레이아웃 지원
- 다양한 화면 크기 대응

## 구현 지침
- 'use client' 지시어 포함
- 반응형 디자인 적용
- 접근성 고려
- 구현할 컴포넌트 목록:
  1. side-navigation.tsx - 사이드바 네비게이션
  2. header-navigation.tsx - 상단 헤더
  3. breadcrumb-navigation.tsx - 경로 표시
  4. container.tsx - 콘텐츠 컨테이너
  5. page-header.tsx - 페이지 헤더
  6. footer.tsx - 페이지 푸터
```

### B4. 피드백 컴포넌트

```
# 모듈 개발 요청: 피드백 컴포넌트

파일: packages/ui/src/components/feedback/[component-name].tsx
의존성: 
- @e-torch/core/types
- ../ui/*

## 기능 요구사항
- 사용자 피드백을 위한 컴포넌트 구현
- 다양한 알림 및 피드백 메커니즘 제공
- 접근성 고려

## 구현 지침
- 'use client' 지시어 포함
- 구현할 컴포넌트 목록:
  1. toast-wrapper.tsx - 토스트 알림 래퍼
  2. loading-indicator.tsx - 로딩 표시기
  3. error-display.tsx - 오류 표시
  4. success-message.tsx - 성공 메시지
  5. confirmation-dialog.tsx - 확인 대화상자
```

### B5. 접근성 컴포넌트

```
# 모듈 개발 요청: 접근성 컴포넌트

파일: packages/ui/src/components/a11y/[component-name].tsx
의존성: @e-torch/core/types

## 기능 요구사항
- 접근성 향상을 위한 컴포넌트 구현
- WCAG 2.1 AA 표준 준수
- 스크린 리더 및 키보드 사용자 지원

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 8 접근성 통합 컴포넌트 참조
- 구현할 컴포넌트 목록:
  1. skip-link.tsx - 콘텐츠 건너뛰기 링크
  2. visually-hidden.tsx - 시각적으로 숨겨진 텍스트
  3. focus-trap.tsx - 포커스 트랩
  4. keyboard-nav-menu.tsx - 키보드 네비게이션 메뉴
```

### B6. 서버 컴포넌트 래퍼 (17개)

```
# 모듈 개발 요청: 서버 컴포넌트 래퍼

파일: packages/ui/src/server-components/[component-name].server.tsx
의존성: 
- @e-torch/core/types
- ../components/ui/*

## 기능 요구사항
- UI 컴포넌트의 서버 컴포넌트 래퍼 구현
- 서버/클라이언트 컴포넌트 통합 지원
- 클라이언트 번들 크기 최적화

## 구현 지침
- core-components.md의 3.4 UI 컴포넌트 서버 래퍼 패턴 참조
- 'use client' 지시어 없이 구현
- 모든 UI 컴포넌트에 대한 서버 래퍼 구현
- 필요한 props만 전달하여 최적화
- 구현할 래퍼 컴포넌트 목록:
  button.server.tsx, card.server.tsx, input.server.tsx, select.server.tsx, 
  checkbox.server.tsx, dialog.server.tsx, dropdown-menu.server.tsx, 
  tabs.server.tsx, toast.server.tsx, tooltip.server.tsx, progress.server.tsx, 
  badge.server.tsx, avatar.server.tsx, alert.server.tsx, alert-dialog.server.tsx, 
  form.server.tsx, skeleton.server.tsx
```

### B7. UI 훅

```
# 모듈 개발 요청: UI 훅

파일: packages/ui/src/hooks/[hook-name].ts
의존성: 
- @e-torch/core/types
- ../components/*

## 기능 요구사항
- UI 컴포넌트 기능 확장을 위한 훅 구현
- 재사용 가능한 UI 로직 제공
- 성능 최적화 고려

## 구현 지침
- 'use client' 지시어 포함
- 구현할 훅 목록:
  1. use-toast.ts - 토스트 알림 관리
  2. use-dialog.ts - 다이얼로그 관리
  3. use-media-query.ts - 미디어 쿼리 관리
  4. use-keyboard.ts - 키보드 이벤트 관리
  5. use-clipboard.ts - 클립보드 기능
  6. use-disclosure.ts - 표시/숨김 상태 관리
  7. use-theme.ts - 테마 설정 관리
```

### B8. UI 패키지 엔트리 포인트

```
# 모듈 개발 요청: UI 패키지 엔트리 포인트

파일: packages/ui/index.ts
의존성: ./src/components/*

## 기능 요구사항
- 모든 UI 컴포넌트 내보내기
- 클라이언트 컴포넌트 엔트리 포인트 제공

## 구현 지침
- 모든 클라이언트 컴포넌트 내보내기
- 네임스페이스로 그룹화하여 내보내기
- 모든 UI 컴포넌트, 레이아웃 컴포넌트, 피드백 컴포넌트, 접근성 컴포넌트 포함
```

```
# 모듈 개발 요청: UI 서버 엔트리 포인트

파일: packages/ui/server.ts
의존성: ./src/server-components/*

## 기능 요구사항
- 모든 서버 컴포넌트 래퍼 내보내기
- 서버 컴포넌트 엔트리 포인트 제공

## 구현 지침
- 모든 서버 컴포넌트 래퍼 내보내기
- 네임스페이스로 그룹화하여 내보내기
```

## C. 유틸리티 개발 (Utilities)

### C1. 데이터 변환 유틸리티

```
# 모듈 개발 요청: 데이터 변환 유틸리티

파일: packages/utils/src/data-transform/[utility-name].ts
의존성: @e-torch/core/types

## 기능 요구사항
- 경제지표 데이터 변환 및 처리 유틸리티 구현
- 다양한 변환 및 계산 지원
- 성능 최적화 고려

## 구현 지침
- data-flow.md의 6.3 소스별 데이터 통합 로직 및 6.4 데이터 다운샘플링 알고리즘 참조
- 구현할 유틸리티 목록:
  1. transforms.ts - 데이터 변환 함수 (원본, 변화율, 누적값)
  2. downsampling.ts - 다운샘플링 알고리즘 (LTTB, M4, 리샘플링)
  3. interpolation.ts - 결측치 처리 알고리즘
  4. synchronization.ts - 기간 동기화 전략
  5. normalization.ts - 데이터 정규화 함수
```

### C2. 포맷팅 유틸리티

```
# 모듈 개발 요청: 포맷팅 유틸리티

파일: packages/utils/src/formatters/[utility-name].ts
의존성: @e-torch/core/types

## 기능 요구사항
- 데이터 표시를 위한 포맷팅 유틸리티 구현
- 다양한 형식 지원 (숫자, 날짜, 통화 등)
- 지역화 지원

## 구현 지침
- 구현할 유틸리티 목록:
  1. number-formatter.ts - 숫자 포맷팅 (단위, 소수점, 천 단위 구분)
  2. date-formatter.ts - 날짜 포맷팅 (주기별 포맷)
  3. currency-formatter.ts - 통화 포맷팅 
  4. percentage-formatter.ts - 백분율 포맷팅
  5. period-formatter.ts - 기간 포맷팅 (D, M, Q, A)
```

### C3. 검증 유틸리티

```
# 모듈 개발 요청: 검증 유틸리티

파일: packages/utils/src/validation/[utility-name].ts
의존성: @e-torch/core/types

## 기능 요구사항
- 데이터 및 입력 검증 유틸리티 구현
- 타입 안전성 보장
- 에러 메시지 지원

## 구현 지침
- 구현할 유틸리티 목록:
  1. input-validation.ts - 사용자 입력 검증
  2. chart-validation.ts - 차트 설정 검증
  3. dashboard-validation.ts - 대시보드 설정 검증
  4. date-validation.ts - 날짜 및 기간 검증
```

### C4. 헬퍼 유틸리티

```
# 모듈 개발 요청: 헬퍼 유틸리티

파일: packages/utils/src/helpers/[utility-name].ts
의존성: @e-torch/core/types

## 기능 요구사항
- 공통 헬퍼 유틸리티 구현
- 재사용 가능한 유틸리티 함수 제공
- 성능 최적화 고려

## 구현 지침
- 구현할 유틸리티 목록:
  1. array-helpers.ts - 배열 조작 헬퍼
  2. object-helpers.ts - 객체 조작 헬퍼
  3. string-helpers.ts - 문자열 조작 헬퍼
  4. date-helpers.ts - 날짜 조작 헬퍼
  5. url-helpers.ts - URL 조작 헬퍼
  6. color-helpers.ts - 색상 조작 헬퍼
  7. math-helpers.ts - 수학 계산 헬퍼
```

### C5. 유틸리티 패키지 엔트리 포인트

```
# 모듈 개발 요청: 유틸리티 패키지 엔트리 포인트

파일: packages/utils/index.ts
의존성: 
- ./src/data-transform/*
- ./src/formatters/*
- ./src/validation/*
- ./src/helpers/*

## 기능 요구사항
- 모든 유틸리티 함수 내보내기
- 카테고리별 그룹화

## 구현 지침
- 네임스페이스로 그룹화하여 내보내기
- 모든 유틸리티 함수 포함
```

## D. 상태 관리 개발 (State Management)

### D1. 앱 상태 스토어

```
# 모듈 개발 요청: 앱 상태 스토어

파일: packages/state/src/stores/app-store.ts
의존성: 
- @e-torch/core/types
- zustand
- zustand/middleware

## 기능 요구사항
- 애플리케이션 수준의 상태 관리
- 테마, 알림, 글로벌 오류, 로딩 상태 관리
- 상태 지속성 관리

## 구현 지침
- state-management.md의 4.2 패키지별 의존성 구조와의 통합 예시 참조
- Zustand 5와 immer 미들웨어 사용
- persist 미들웨어를 사용한 상태 지속성 구현
- 테마, 알림, 글로벌 오류, 로딩 상태 관리 기능 구현
```

### D2. 차트 에디터 스토어

```
# 모듈 개발 요청: 차트 에디터 스토어

파일: packages/state/src/stores/chart-editor-store.ts
의존성: 
- @e-torch/core/types
- zustand
- zustand/middleware

## 기능 요구사항
- 차트 에디터 상태 관리
- 차트 속성 편집 기능
- Undo/Redo 기능 지원
- 미리보기/편집 모드 전환

## 구현 지침
- state-management.md의 4.3 차트 에디터 스토어 예시 참조
- 정규화된 상태 구조 설계
- 커맨드 패턴을 활용한 Undo/Redo 구현
- devtools 미들웨어를 통한 디버깅 지원
```

### D3. 대시보드 스토어

```
# 모듈 개발 요청: 대시보드 스토어

파일: packages/state/src/stores/dashboard-store.ts
의존성: 
- @e-torch/core/types
- zustand
- zustand/middleware

## 기능 요구사항
- 대시보드 상태 관리
- 위젯 배치 및 레이아웃 관리
- 편집/뷰 모드 전환
- 저장 상태 관리

## 구현 지침
- state-management.md의 4.4 대시보드 스토어 예시 참조
- 정규화된 상태 구조 설계
- 대시보드, 위젯, 레이아웃 간의 관계 관리
- 저장 상태 관리 (idle, saving, success, error)
```

### D4. 쿼리 키 및 타입

```
# 모듈 개발 요청: 쿼리 키 및 타입

파일: packages/state/src/query-keys.ts
의존성: @e-torch/core/types

## 기능 요구사항
- TanStack Query의 쿼리 키 구조 정의
- 관련 쿼리 효율적 관리
- 타입 안전성 보장

## 구현 지침
- state-management.md의 7.3 쿼리 키 설계 전략 참조
- 계층적 쿼리 키 구조 설계
- 대시보드, 차트, 사용자 등 각 도메인별 쿼리 키 정의
```

### D5. 차트 데이터 쿼리 훅

```
# 모듈 개발 요청: 차트 데이터 쿼리 훅

파일: packages/state/src/hooks/use-chart-data.ts
의존성: 
- @e-torch/core/types
- @e-torch/data-sources
- @tanstack/react-query
- ../query-keys

## 기능 요구사항
- 차트 데이터 조회를 위한 TanStack Query 훅
- 캐싱 및 리페칭 전략 적용
- 서버 제공 초기 데이터 활용

## 구현 지침
- state-management.md의 7.1 TanStack Query 5 활용 전략 참조
- 적절한 캐싱 전략 적용
- 초기 데이터 활용
- 에러 핸들링 및 로딩 상태 관리
```

### D6. 대시보드 쿼리 훅

```
# 모듈 개발 요청: 대시보드 쿼리 훅

파일: packages/state/src/hooks/use-dashboard.ts
의존성: 
- @e-torch/core/types
- @e-torch/data-sources
- @tanstack/react-query
- ../query-keys

## 기능 요구사항
- 대시보드 데이터 조회를 위한 TanStack Query 훅
- 캐싱 및 리페칭 전략 적용
- 서버 제공 초기 데이터 활용

## 구현 지침
- 각 대시보드 유형에 맞는 데이터 정규화 및 변환
- 서버 컴포넌트에서 제공한 초기 데이터 활용
- 대시보드 ID를 기반으로 한 데이터 조회
```

### D7. 저장 변경 훅

```
# 모듈 개발 요청: 저장 변경 훅

파일: packages/state/src/hooks/use-save-dashboard.ts
의존성: 
- @e-torch/core/types
- @tanstack/react-query
- ../query-keys

## 기능 요구사항
- 대시보드 저장을 위한 TanStack Query 훅
- 낙관적 업데이트 지원
- 에러 핸들링 및 롤백

## 구현 지침
- state-management.md의 7.2 낙관적 업데이트 패턴 참조
- useMutation 활용
- 낙관적 업데이트 및 롤백 로직 구현
- 성공/실패 콜백 처리
```

### D8. 서버 액션 통합 훅

```
# 모듈 개발 요청: 서버 액션 통합 훅

파일: packages/state/src/hooks/use-action-mutation.ts
의존성: 
- @e-torch/core/types
- @tanstack/react-query
- react

## 기능 요구사항
- 서버 액션과 TanStack Query 통합
- useTransition을 활용한 낙관적 UI 업데이트
- 에러 핸들링 및 캐시 무효화

## 구현 지침
- state-management.md의 7.4 서버 액션과의 통합 참조
- useTransition 활용
- 관련 쿼리 자동 무효화
- 에러 핸들링 및 상태 관리
```

### D9. 시간 범위 동기화 훅

```
# 모듈 개발 요청: 시간 범위 동기화 훅

파일: packages/state/src/hooks/use-time-range-sync.ts
의존성: 
- @e-torch/core/types
- ../stores/dashboard-store
- ../services/event-bus

## 기능 요구사항
- 대시보드와 차트 간 시간 범위 동기화
- 개별 차트 설정 지원
- 이벤트 기반 동기화

## 구현 지침
- state-management.md의 9.2 대시보드-차트 컴포넌트 간 상태 동기화 참조
- 이벤트 버스 패턴 활용
- 공유 설정과 개별 설정 간의 우선순위 관리
```

### D10. 이벤트 버스 구현

```
# 모듈 개발 요청: 이벤트 버스

파일: packages/state/src/services/event-bus.ts
의존성: 
- @e-torch/core/types
- zustand

## 기능 요구사항
- 컴포넌트 간 느슨한 결합을 위한 이벤트 버스 구현
- 이벤트 발행/구독 패턴 지원
- 타입 안전성 보장

## 구현 지침
- state-management.md의 9.1 이벤트 버스 패턴 참조
- Zustand를 활용한 이벤트 버스 구현
- 이벤트 타입 및 페이로드 타입 정의
- 구독 취소 함수 반환
```

### D11. 히스토리 매니저

```
# 모듈 개발 요청: 히스토리 매니저

파일: packages/state/src/services/history-manager.ts
의존성: @e-torch/core/types

## 기능 요구사항
- Undo/Redo 기능을 위한 히스토리 관리
- 커맨드 패턴 기반 구현
- 명령 병합 지원

## 구현 지침
- state-management.md의 8.1 커맨드 패턴 기반 히스토리 관리 참조
- 패스트/퓨처 스택 관리
- 명령 실행, 취소, 재실행 구현
- 유사 명령 병합 지원
```

### D12. 디버그 스토어

```
# 모듈 개발 요청: 디버그 스토어

파일: packages/state/src/stores/debug-store.ts
의존성: 
- @e-torch/core/types
- zustand
- zustand/middleware

## 기능 요구사항
- 개발 모드에서의 디버깅 지원
- 액션 히스토리 기록
- 상태 스냅샷 저장
- 필터링 기능

## 구현 지침
- state-management.md의 10.1 Zustand 디버깅 도구 참조
- 개발 환경에서만 활성화
- 액션 히스토리 및 상태 스냅샷 관리
- 필터링 옵션 제공
```

### D13. 시간 여행 디버거

```
# 모듈 개발 요청: 시간 여행 디버거

파일: packages/state/src/services/time-travel-debugger.ts
의존성: 
- @e-torch/core/types
- ../stores/debug-store

## 기능 요구사항
- 개발 모드에서의 시간 여행 디버깅 지원
- 과거 상태로 돌아가기 기능
- 스냅샷 관리

## 구현 지침
- state-management.md의 10.2 시간 여행 디버깅 참조
- 스토어 상태 스냅샷 기록
- 시간 여행 기능 구현
- 원래 상태로 복원 기능
```

### D14. 상태 관리 패키지 엔트리 포인트

```
# 모듈 개발 요청: 상태 관리 패키지 엔트리 포인트

파일: packages/state/index.ts
의존성: 
- ./src/stores/*
- ./src/hooks/*
- ./src/services/*

## 기능 요구사항
- 모든 스토어, 훅, 서비스 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 상태 관리 스토어 내보내기
- 모든 훅 내보내기
- 모든 서비스 내보내기
- 개발 환경에서만 디버깅 도구 내보내기
```

## E. 데이터 소스 개발 (Data Sources)

### E1. API 클라이언트 기본 구현

```
# 모듈 개발 요청: API 클라이언트 기본 구현

파일: packages/data-sources/src/api-client/index.ts
의존성: @e-torch/core/types

## 기능 요구사항
- 기본 HTTP 클라이언트 구현
- 인터셉터 및 에러 핸들링
- 재시도 로직

## 구현 지침
- data-flow.md의 4.1 API 클라이언트 아키텍처 참조
- 인증 토큰 관리 및 인터셉터 구현
- 에러 핸들링 및 재시도 로직 구현
- 타입 안전성 보장
```

### E2. 경제지표 API 클라이언트

```
# 모듈 개발 요청: 경제지표 API 클라이언트

파일: packages/data-sources/src/api-client/economic-indicators.ts
의존성: 
- @e-torch/core/types
- @e-torch/utils
- ./index

## 기능 요구사항
- 경제지표 데이터 조회 API 구현
- 지표 검색, 시계열 데이터 조회
- 데이터 변환 및 정규화

## 구현 지침
- data-flow.md의 4.3 도메인별 API 클라이언트 참조
- 소스별(KOSIS, ECOS, OECD) API 래퍼 구현
- 데이터 정규화 및 변환 로직 통합
- 데이터 캐싱 전략 적용
```

### E3. 대시보드 API 클라이언트

```
# 모듈 개발 요청: 대시보드 API 클라이언트

파일: packages/data-sources/src/api-client/dashboards.ts
의존성: 
- @e-torch/core/types
- @e-torch/utils
- ./index

## 기능 요구사항
- 대시보드 관련 API 구현
- 대시보드 생성, 조회, 수정, 삭제
- 대시보드 공유 및 탐색

## 구현 지침
- 대시보드 CRUD 작업 구현
- 위젯 및 레이아웃 관리 API 통합
- 대시보드 공유 및 권한 관리 API 구현
```

### E4. 사용자 API 클라이언트

```
# 모듈 개발 요청: 사용자 API 클라이언트

파일: packages/data-sources/src/api-client/users.ts
의존성: 
- @e-torch/core/types
- @e-torch/utils
- ./index

## 기능 요구사항
- 사용자 관련 API 구현
- 프로필 관리, 구독 정보
- 설정 관리

## 구현 지침
- 사용자 프로필 관리 API 구현
- 구독 정보 조회 및 관리 API 구현
- 사용자 설정 관리 API 구현
```

### E5. 데이터 소스 커넥터

```
# 모듈 개발 요청: 데이터 소스 커넥터

파일: packages/data-sources/src/connectors/[connector-name].ts
의존성: 
- @e-torch/core/types
- @e-torch/utils
- ../api-client/*

## 기능 요구사항
- 데이터 소스별 커넥터 구현
- 데이터 페칭 및 변환
- 캐싱 및 최적화

## 구현 지침
- 구현할 커넥터 목록:
  1. kosis-connector.ts - KOSIS 데이터 커넥터
  2. ecos-connector.ts - ECOS 데이터 커넥터
  3. oecd-connector.ts - OECD 데이터 커넥터
  4. supabase-connector.ts - Supabase 연동 커넥터
- 각 커넥터별 데이터 정규화 및 변환 로직 구현
```

### E6. 데이터 변환 서비스

```
# 모듈 개발 요청: 데이터 변환 서비스

파일: packages/data-sources/src/services/data-transformer.ts
의존성: 
- @e-torch/core/types
- @e-torch/utils

## 기능 요구사항
- 데이터 변환 파이프라인 구현
- 다양한 변환 방식 지원
- 성능 최적화

## 구현 지침
- data-flow.md의 6.1 데이터 파이프라인 구조 참조
- 변환 유형 구현: 원본값, 변화율(전기대비), 변화율(전년동기대비), 누적값
- 결측치 처리 및 기간 동기화 통합
- 성능 최적화를 위한 캐싱 및 메모이제이션
```

### E7. 데이터 쿼리 빌더

```
# 모듈 개발 요청: 데이터 쿼리 빌더

파일: packages/data-sources/src/services/query-builder.ts
의존성: 
- @e-torch/core/types
- @e-torch/utils
- ../connectors/*

## 기능 요구사항
- 데이터 쿼리 구성 서비스 구현
- 복잡한 쿼리 빌드 지원
- 소스 간 통합 쿼리 지원

## 구현 지침
- data-flow.md의 6.2 기간 동기화 전략 참조
- 쿼리 파라미터 빌드 및 검증
- 다중 소스 쿼리 통합
- 쿼리 최적화 및 캐싱
```

### E8. 데이터 소스 클라이언트 훅

```
# 모듈 개발 요청: 데이터 소스 클라이언트 훅

파일: packages/data-sources/src/hooks/[hook-name].ts
의존성: 
- @e-torch/core/types
- ../api-client/*
- ../services/*

## 기능 요구사항
- 데이터 조회를 위한 클라이언트 훅 구현
- API 클라이언트 래핑
- 에러 핸들링 및 데이터 변환

## 구현 지침
- 'use client' 지시어 포함
- 구현할 훅 목록:
  1. use-indicator-search.ts - 지표 검색 훅
  2. use-time-series-data.ts - 시계열 데이터 조회 훅
  3. use-indicator-metadata.ts - 지표 메타데이터 조회 훅
  4. use-data-transform.ts - 데이터 변환 훅
```

### E9. 서버 액션 데이터 페칭

```
# 모듈 개발 요청: 서버 액션 데이터 페칭

파일: packages/data-sources/server/index.ts
의존성: 
- @e-torch/core/types
- ../api-client/*

## 기능 요구사항
- 서버 컴포넌트에서 사용할 데이터 페칭 함수
- 서버 측 캐싱 활용
- 에러 핸들링

## 구현 지침
- 'use server' 지시어 없이 구현 (서버 컴포넌트에서만 사용)
- 서버 측 데이터 페칭 최적화
- 직접 API 호출 구현
- 서버 측 캐싱 활용
```

### E10. 데이터 소스 패키지 엔트리 포인트

```
# 모듈 개발 요청: 데이터 소스 패키지 엔트리 포인트

파일: packages/data-sources/index.ts
의존성: 
- ./src/api-client/*
- ./src/services/*
- ./src/hooks/*

## 기능 요구사항
- 클라이언트 측에서 사용할 모든 API 내보내기
- 타입 안전성 보장

## 구현 지침
- 'use client' 지시어 포함
- 모든 API 클라이언트 내보내기
- 모든 서비스 내보내기
- 모든 훅 내보내기
```

## F. 차트 컴포넌트 개발 (Charts)

### F1. 차트 렌더러 기반 컴포넌트

```
# 모듈 개발 요청: 차트 렌더러

파일: packages/charts/src/components/ChartRenderer.tsx
의존성: 
- @e-torch/core/types
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 다양한 차트 유형을 렌더링하는 기본 컴포넌트
- 차트 유형에 따른 조건부 렌더링
- 공통 차트 속성 관리

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 4.1 차트 컴포넌트 계층 구조 참조
- 차트 유형별 렌더링 로직 분기
- 공통 차트 속성 관리
```

### F2. 시계열 차트 컴포넌트

```
# 모듈 개발 요청: 시계열 차트 컴포넌트

파일: packages/charts/src/components/chart-types/TimeSeriesChart.tsx
의존성: 
- @e-torch/core/types
- @e-torch/utils
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 시계열 데이터 시각화 차트 구현
- 라인, 영역, 바 스타일 지원
- 커스터마이징 옵션 지원
- 접근성 고려

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 4.3 차트 커스텀 렌더링 최적화 참조
- 데이터 다운샘플링 적용
- 메모이제이션을 통한 성능 최적화
- 접근성 고려한 구현
```

### F3. 바 차트 컴포넌트

```
# 모듈 개발 요청: 바 차트 컴포넌트

파일: packages/charts/src/components/chart-types/BarChart.tsx
의존성: 
- @e-torch/core/types
- @e-torch/utils
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 바 차트 컴포넌트 구현
- 수직/수평 바 지원
- 그룹화/스택 바 지원
- 커스터마이징 옵션 지원

## 구현 지침
- 'use client' 지시어 포함
- 바 차트 특화 옵션 구현
- 메모이제이션을 통한 성능 최적화
- 접근성 고려한 구현
```

### F4. 산점도 차트 컴포넌트

```
# 모듈 개발 요청: 산점도 차트 컴포넌트

파일: packages/charts/src/components/chart-types/ScatterChart.tsx
의존성: 
- @e-torch/core/types
- @e-torch/utils
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 산점도 차트 구현
- 포인트 크기/모양 옵션
- 회귀선 옵션
- 축 설정 옵션

## 구현 지침
- 'use client' 지시어 포함
- ui-requirements.md의 3.2.2 Scatter Chart Options 참조
- 회귀선 계산 및 표시 구현
- 다양한 포인트 모양 및 크기 옵션 구현
```

### F5. 레이더 차트 컴포넌트

```
# 모듈 개발 요청: 레이더 차트 컴포넌트

파일: packages/charts/src/components/chart-types/RadarChart.tsx
의존성: 
- @e-torch/core/types
- @e-torch/utils
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 레이더 차트 구현
- 다차원 데이터 비교 시각화
- 그리드 모양 및 축 선 옵션
- 영역 채우기 옵션

## 구현 지침
- 'use client' 지시어 포함
- ui-requirements.md의 3.2.3 Radar Chart Options 참조
- 다양한 그리드 모양 옵션 구현
- 데이터 정규화 옵션 구현
```

### F6. 방사형 바 차트 컴포넌트

```
# 모듈 개발 요청: 방사형 바 차트 컴포넌트

파일: packages/charts/src/components/chart-types/RadialBarChart.tsx
의존성: 
- @e-torch/core/types
- @e-torch/utils
- @e-torch/ui
- recharts
- react

## 기능 요구사항
- 방사형 바 차트 구현
- 각도 및 반경 설정
- 배경 옵션
- 모서리 라운드 처리

## 구현 지침
- 'use client' 지시어 포함
- ui-requirements.md의 3.2.4 Radial Bar Chart Options 참조
- 다양한 시작/종료 각도 옵션 구현
- 그라데이션 및 배경 옵션 구현
```

### F7. 차트 컴포넌트

```
# 모듈 개발 요청: 차트 컴포넌트

파일: packages/charts/src/components/ChartComponent.tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- ./ChartRenderer
- react

## 기능 요구사항
- 차트 컴포넌트 통합
- 데이터 로딩/에러 상태 처리
- 차트 데이터 관리

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 3.3 서버/클라이언트 통합 패턴 참조
- 데이터 상태에 따른 UI 분기 처리
- 초기 데이터 활용 및 실시간 데이터 관리
```

### F8. 차트 에디터 컴포넌트

```
# 모듈 개발 요청: 차트 에디터 컴포넌트

파일: packages/charts/src/editor/ChartEditor.tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/data-sources
- ../components/ChartComponent
- ./options/*

## 기능 요구사항
- 차트 에디터 통합 컴포넌트
- 차트 미리보기, 옵션 패널, 데이터 소스 패널 통합
- 실시간 미리보기 제공

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 4.2 차트 에디터 컴포넌트 참조
- 에디터 레이아웃 및 상태 관리 구현
- 옵션 변경에 따른 실시간 미리보기 구현
```

### F9. 옵션 패널 컴포넌트

```
# 모듈 개발 요청: 옵션 패널 컴포넌트

파일: packages/charts/src/editor/options/[panel-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- react

## 기능 요구사항
- 차트 옵션 편집을 위한 패널 컴포넌트
- 각 옵션 유형별 편집 UI
- 실시간 업데이트 지원

## 구현 지침
- 'use client' 지시어 포함
- ui-requirements.md의 3.1 공통 옵션 컴포넌트 참조
- 구현할 패널 목록:
  1. PanelOptions.tsx - 기본 패널 옵션
  2. TooltipOptions.tsx - 툴팁 옵션
  3. LegendOptions.tsx - 범례 옵션
  4. AxisOptions.tsx - 축 옵션
  5. StyleOptions.tsx - 스타일 옵션
  6. ChartTypeOptions.tsx - 차트 유형별 옵션
```

### F10. 데이터 소스 패널 컴포넌트

```
# 모듈 개발 요청: 데이터 소스 패널 컴포넌트

파일: packages/charts/src/editor/data-source/[component-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/data-sources
- react

## 기능 요구사항
- 데이터 소스 설정 패널 컴포넌트
- 지표 검색 및 선택 UI
- 데이터 변환 설정 UI

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 6.1 데이터 쿼리 컴포넌트 및 6.2 데이터 변환 컴포넌트 참조
- 구현할 컴포넌트 목록:
  1. DataSourcePanel.tsx - 데이터 소스 패널 컴포넌트
  2. SourceSelector.tsx - 데이터 소스 선택 컴포넌트
  3. IndicatorSelector.tsx - 지표 선택 컴포넌트
  4. TransformControls.tsx - 데이터 변환 컴포넌트
  5. DataPreview.tsx - 데이터 미리보기 컴포넌트
```

### F11. 접근성 컴포넌트

```
# 모듈 개발 요청: 차트 접근성 컴포넌트

파일: packages/charts/src/components/accessibility/[component-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/ui
- react

## 기능 요구사항
- 차트의 접근성을 향상시키는 컴포넌트
- 스크린 리더 지원
- 키보드 접근성 지원

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 8.2 스크린 리더 지원 컴포넌트 및 8.3 색상 및 대비 컴포넌트 참조
- 구현할 컴포넌트 목록:
  1. AccessibleChartTable.tsx - 스크린 리더를 위한 테이블 변환
  2. AccessibleChartPatterns.tsx - 색맹 사용자를 위한 패턴
  3. KeyboardNavigableChart.tsx - 키보드 내비게이션 지원
```

### F12. 차트 서버 컴포넌트 래퍼

```
# 모듈 개발 요청: 차트 서버 컴포넌트 래퍼

파일: packages/charts/server/ChartServerWrapper.tsx
의존성: 
- @e-torch/core/types
- @e-torch/data-sources/server
- ../src/components/ChartComponent

## 기능 요구사항
- 서버 컴포넌트에서 차트 데이터 페칭
- 클라이언트 컴포넌트에 초기 데이터 전달
- 메타데이터 설정

## 구현 지침
- core-components.md의 3.3 서버/클라이언트 통합 패턴 참조
- 서버에서 데이터 페칭 최적화
- 클라이언트 hydration을 위한 데이터 직렬화
```

### F13. 차트 패키지 엔트리 포인트

```
# 모듈 개발 요청: 차트 패키지 엔트리 포인트

파일: packages/charts/index.ts
의존성: 
- ./src/components/*
- ./src/editor/*

## 기능 요구사항
- 모든 차트 컴포넌트 내보내기
- 에디터 컴포넌트 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 차트 컴포넌트 내보내기
- 에디터 컴포넌트 내보내기
- 네임스페이스로 그룹화하여 내보내기
```

```
# 모듈 개발 요청: 차트 서버 컴포넌트 엔트리 포인트

파일: packages/charts/server.ts
의존성: ./server/*

## 기능 요구사항
- 모든 차트 서버 컴포넌트 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 차트 서버 컴포넌트 내보내기
```

## G. 대시보드 컴포넌트 개발 (Dashboard)

### G1. 대시보드 그리드 컴포넌트

```
# 모듈 개발 요청: 대시보드 그리드 컴포넌트

파일: packages/dashboard/src/components/DashboardGrid.tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- react-grid-layout
- react

## 기능 요구사항
- 대시보드 그리드 레이아웃 구현
- 드래그 앤 드롭 위젯 배치
- 크기 조절 가능한 위젯
- 반응형 그리드

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 5.1 대시보드 그리드 컴포넌트 참조
- 드래그, 리사이징 이벤트 핸들러 구현
- 편집/뷰 모드 전환 지원
```

### G2. 위젯 컴포넌트

```
# 모듈 개발 요청: 위젯 컴포넌트

파일: packages/dashboard/src/components/widgets/[widget-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/charts
- react

## 기능 요구사항
- 대시보드 위젯 구현
- 차트 위젯, 텍스트 위젯 등 다양한 위젯 지원
- 위젯 설정 및 상호작용

## 구현 지침
- 'use client' 지시어 포함
- 구현할 위젯 목록:
  1. ChartWidget.tsx - 차트 위젯
  2. TextWidget.tsx - 텍스트 위젯
  3. MetricWidget.tsx - 지표 위젯
  4. TableWidget.tsx - 테이블 위젯
  5. EmptyWidget.tsx - 빈 위젯 (드래그 앤 드롭용)
```

### G3. 대시보드 컨트롤 컴포넌트

```
# 모듈 개발 요청: 대시보드 컨트롤 컴포넌트

파일: packages/dashboard/src/components/controls/[control-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- react

## 기능 요구사항
- 대시보드 컨트롤 구현
- 시간 범위, 새로고침, 뷰 옵션 등 컨트롤 제공
- 상호작용 및 상태 관리

## 구현 지침
- 'use client' 지시어 포함
- 구현할 컨트롤 목록:
  1. TimeRangeControl.tsx - 시간 범위 컨트롤
  2. RefreshControl.tsx - 새로고침 컨트롤
  3. ViewOptionsControl.tsx - 뷰 옵션 컨트롤
  4. ExportControl.tsx - 내보내기 컨트롤
  5. ShareControl.tsx - 공유 컨트롤
```

### G4. 대시보드 에디터 컴포넌트

```
# 모듈 개발 요청: 대시보드 에디터 컴포넌트

파일: packages/dashboard/src/editor/DashboardEditor.tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/charts
- ../components/*
- react

## 기능 요구사항
- 대시보드 에디터 통합 컴포넌트
- 에디터 툴바, 위젯 선택기 등 통합
- 대시보드 편집 상태 관리

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 5.2 대시보드 편집기 컴포넌트 참조
- 에디터 툴바, 위젯 선택기, 레이아웃 컨트롤 통합
- 저장, 취소, 레이아웃 변경 핸들러 구현
```

### G5. 대시보드 컴포넌트

```
# 모듈 개발 요청: 대시보드 컴포넌트

파일: packages/dashboard/src/components/DashboardComponent.tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- ./DashboardGrid
- ./controls/*
- react

## 기능 요구사항
- 대시보드 통합 컴포넌트
- 대시보드 컨트롤 및 그리드 통합
- 대시보드 상태 관리

## 구현 지침
- 'use client' 지시어 포함
- core-components.md의 5.3 서버/클라이언트 통합 예시 참조
- 대시보드 상태 초기화 및 관리
- 편집/뷰 모드 전환 지원
```

### G6. 대시보드 탐색 컴포넌트

```
# 모듈 개발 요청: 대시보드 탐색 컴포넌트

파일: packages/dashboard/src/components/explore/[component-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/data-sources
- react

## 기능 요구사항
- 대시보드 탐색 및 검색 컴포넌트
- 대시보드 카드, 검색 필터, 카테고리 필터 등
- 구독 및 미리보기 기능

## 구현 지침
- 'use client' 지시어 포함
- 구현할 컴포넌트 목록:
  1. DashboardExplore.tsx - 대시보드 탐색 컴포넌트
  2. DashboardSearchFilter.tsx - 검색 필터 컴포넌트
  3. DashboardCard.tsx - 대시보드 카드 컴포넌트
  4. CategoryFilter.tsx - 카테고리 필터 컴포넌트
  5. DashboardPreview.tsx - 대시보드 미리보기 컴포넌트
```

### G7. 대시보드 공유 컴포넌트

```
# 모듈 개발 요청: 대시보드 공유 컴포넌트

파일: packages/dashboard/src/components/sharing/[component-name].tsx
의존성: 
- @e-torch/core/types
- @e-torch/state
- @e-torch/ui
- @e-torch/data-sources
- react

## 기능 요구사항
- 대시보드 공유 컴포넌트
- 링크 생성, 소셜 공유, 내보내기 등
- 권한 설정

## 구현 지침
- 'use client' 지시어 포함
- 구현할 컴포넌트 목록:
  1. ShareModal.tsx - 공유 모달 컴포넌트
  2. LinkGenerator.tsx - 링크 생성 컴포넌트
  3. SocialShare.tsx - 소셜 공유 컴포넌트
  4. ExportOptions.tsx - 내보내기 옵션 컴포넌트
  5. PermissionSettings.tsx - 권한 설정 컴포넌트
```

### G8. 대시보드 서버 컴포넌트 래퍼

```
# 모듈 개발 요청: 대시보드 서버 컴포넌트 래퍼

파일: packages/dashboard/server/DashboardServerWrapper.tsx
의존성: 
- @e-torch/core/types
- @e-torch/data-sources/server
- ../src/components/DashboardComponent

## 기능 요구사항
- 서버 컴포넌트에서 대시보드 데이터 페칭
- 클라이언트 컴포넌트에 초기 데이터 전달
- 메타데이터 설정

## 구현 지침
- core-components.md의 5.3 서버/클라이언트 통합 예시 참조
- 서버에서 대시보드 및 위젯 데이터 페칭
- 클라이언트 hydration을 위한 데이터 직렬화
```

### G9. 대시보드 패키지 엔트리 포인트

```
# 모듈 개발 요청: 대시보드 패키지 엔트리 포인트

파일: packages/dashboard/index.ts
의존성: 
- ./src/components/*
- ./src/editor/*

## 기능 요구사항
- 모든 대시보드 컴포넌트 내보내기
- 에디터 컴포넌트 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 대시보드 컴포넌트 내보내기
- 에디터 컴포넌트 내보내기
- 네임스페이스로 그룹화하여 내보내기
```

```
# 모듈 개발 요청: 대시보드 서버 컴포넌트 엔트리 포인트

파일: packages/dashboard/server.ts
의존성: ./server/*

## 기능 요구사항
- 모든 대시보드 서버 컴포넌트 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 대시보드 서버 컴포넌트 내보내기
```

## H. 서버 API 개발 (Server API)

### H1. API 핸들러 기반 구현

```
# 모듈 개발 요청: API 핸들러 기본 구현

파일: packages/server-api/src/handlers/index.ts
의존성: @e-torch/core/types

## 기능 요구사항
- API 핸들러 기본 구현
- 요청 검증 및 응답 포맷팅
- 오류 처리

## 구현 지침
- API 요청/응답 검증 로직 구현
- 표준화된 오류 처리 및 응답 포맷 구현
- 성공/실패 응답 래퍼 함수 구현
```

### H2. 대시보드 API 핸들러

```
# 모듈 개발 요청: 대시보드 API 핸들러

파일: packages/server-api/src/handlers/dashboard.ts
의존성: 
- @e-torch/core/types
- ./index

## 기능 요구사항
- 대시보드 API 핸들러 구현
- CRUD 작업 지원
- 인증 및 권한 검증

## 구현 지침
- 대시보드 조회, 생성, 수정, 삭제 핸들러 구현
- 인증 및 권한 검증 로직 통합
- 데이터 검증 및 오류 처리
```

### H3. 데이터 소스 API 핸들러

```
# 모듈 개발 요청: 데이터 소스 API 핸들러

파일: packages/server-api/src/handlers/data-source.ts
의존성: 
- @e-torch/core/types
- ./index

## 기능 요구사항
- 데이터 소스 API 핸들러 구현
- 지표 검색 및 데이터 조회 지원
- 캐싱 및 최적화

## 구현 지침
- 지표 검색, 목록, 상세 조회 핸들러 구현
- 시계열 데이터 조회 핸들러 구현
- 캐싱 전략 적용
```

### H4. 사용자 API 핸들러

```
# 모듈 개발 요청: 사용자 API 핸들러

파일: packages/server-api/src/handlers/user.ts
의존성: 
- @e-torch/core/types
- ./index

## 기능 요구사항
- 사용자 API 핸들러 구현
- 프로필 관리, 구독 정보 지원
- 인증 및 권한 검증

## 구현 지침
- 사용자 프로필 조회 및 수정 핸들러 구현
- 구독 정보 관리 핸들러 구현
- 인증 및 권한 검증 로직 통합
```

### H5. 서버 액션 구현

```
# 모듈 개발 요청: 대시보드 서버 액션

파일: packages/server-api/src/actions/dashboard.ts
의존성: 
- @e-torch/core/types
- ../handlers/dashboard

## 기능 요구사항
- 대시보드 관련 서버 액션 구현
- 대시보드 저장, 삭제 등 작업 지원
- 캐시 무효화 및 리다이렉션

## 구현 지침
- 'use server' 지시어 포함
- 대시보드 저장, 삭제 등 서버 액션 구현
- revalidatePath를 통한 캐시 무효화
- 적절한 오류 처리 및 결과 반환
```

```
# 모듈 개발 요청: 차트 서버 액션

파일: packages/server-api/src/actions/chart.ts
의존성: 
- @e-torch/core/types
- ../handlers/data-source

## 기능 요구사항
- 차트 관련 서버 액션 구현
- 차트 데이터 조회 및 저장 지원
- 캐시 무효화

## 구현 지침
- 'use server' 지시어 포함
- 차트 데이터 조회 및 저장 서버 액션 구현
- 차트 설정 관리 서버 액션 구현
- 적절한 오류 처리 및 결과 반환
```

### H6. 미들웨어 구현

```
# 모듈 개발 요청: 인증 미들웨어

파일: packages/server-api/src/middleware/auth.ts
의존성: @e-torch/core/types

## 기능 요구사항
- 인증 미들웨어 구현
- JWT 토큰 검증
- 권한 검증

## 구현 지침
- Next.js 미들웨어 패턴 사용
- JWT 토큰 검증 로직 구현
- 권한 검증 및 리다이렉션 처리
```

### H7. 서버 API 패키지 엔트리 포인트

```
# 모듈 개발 요청: 서버 API 패키지 엔트리 포인트

파일: packages/server-api/index.ts
의존성: 
- ./src/handlers/*
- ./src/actions/*

## 기능 요구사항
- 모든 API 핸들러 및 서버 액션 내보내기
- 타입 안전성 보장

## 구현 지침
- 모든 API 핸들러 내보내기
- 'use server' 지시어가 포함된 서버 액션은 별도로 내보내기
- 네임스페이스로 그룹화하여 내보내기
```

## I. 앱 페이지 개발 (App Pages)

### I1. 루트 레이아웃

```
# 모듈 개발 요청: 루트 레이아웃

파일: apps/web/app/layout.tsx
의존성: 
- @e-torch/ui
- @e-torch/state
- next/font/google

## 기능 요구사항
- 앱 루트 레이아웃 구현
- 폰트 및 전역 스타일 적용
- 상태 제공자 통합

## 구현 지침
- routing.md의 6 레이아웃 구조 참조
- 서버 컴포넌트로 구현
- Inter 및 JetBrains Mono 폰트 설정
- QueryClientProvider, ThemeProvider 등 상태 제공자 통합
```

### I2. 인증 관련 페이지

```
# 모듈 개발 요청: 로그인 페이지

파일: apps/web/app/(auth)/login/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/core/types

## 기능 요구사항
- 로그인 페이지 구현
- SNS 로그인 버튼 제공
- 간결한 UI 구성

## 구현 지침
- 서버 컴포넌트로 구현
- SNS 로그인 버튼(Google, Naver, Kakao) 제공
- 로그인 관련 메타데이터 설정
```

```
# 모듈 개발 요청: OAuth 콜백 페이지

파일: apps/web/app/(auth)/callback/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/core/types
- next/navigation

## 기능 요구사항
- OAuth 콜백 처리 페이지 구현
- 인증 토큰 저장
- 리다이렉션 처리

## 구현 지침
- 서버 컴포넌트로 구현
- URL 파라미터에서 토큰 추출
- 인증 성공 시 대시보드로 리다이렉션
```

### I3. 대시보드 관련 페이지

```
# 모듈 개발 요청: 대시보드 목록 페이지

파일: apps/web/app/(dashboard)/dashboard/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/dashboard/server
- @e-torch/server-api

## 기능 요구사항
- 대시보드 목록 페이지 구현
- 사용자의 대시보드 목록 표시
- 새 대시보드 생성 버튼 제공

## 구현 지침
- 서버 컴포넌트로 구현
- 서버에서 대시보드 목록 데이터 페칭
- 대시보드 카드 그리드 레이아웃 구성
```

```
# 모듈 개발 요청: 대시보드 상세 페이지

파일: apps/web/app/(dashboard)/dashboard/[id]/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/dashboard/server
- @e-torch/server-api
- next/navigation

## 기능 요구사항
- 대시보드 상세 페이지 구현
- 특정 대시보드 조회 및 표시
- 동적 라우트 파라미터 처리

## 구현 지침
- 서버 컴포넌트로 구현
- 동적 ID 파라미터 처리
- 서버에서 대시보드 데이터 페칭
- 존재하지 않는 대시보드 처리 (404)
```

```
# 모듈 개발 요청: 대시보드 편집 페이지

파일: apps/web/app/(dashboard)/dashboard/[id]/edit/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/dashboard/server
- @e-torch/server-api
- next/navigation

## 기능 요구사항
- 대시보드 편집 페이지 구현
- 기존 대시보드 편집 기능
- 동적 라우트 파라미터 처리

## 구현 지침
- 서버 컴포넌트로 구현
- 동적 ID 파라미터 처리
- 서버에서 대시보드 데이터 페칭
- 편집 권한 검증
```

```
# 모듈 개발 요청: 새 대시보드 페이지

파일: apps/web/app/(dashboard)/dashboard/new/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/dashboard/server
- @e-torch/server-api

## 기능 요구사항
- 새 대시보드 생성 페이지 구현
- 빈 대시보드 템플릿 제공
- 저장 기능

## 구현 지침
- 서버 컴포넌트로 구현
- 기본 템플릿 초기화
- 메타데이터 설정
```

```
# 모듈 개발 요청: 대시보드 탐색 페이지

파일: apps/web/app/(dashboard)/explore/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/dashboard/server
- @e-torch/server-api

## 기능 요구사항
- 대시보드 탐색 페이지 구현
- 공유 대시보드 목록 표시
- 검색 및 필터링 기능

## 구현 지침
- 서버 컴포넌트로 구현
- 서버에서 공유 대시보드 데이터 페칭
- 검색 및 필터링 컴포넌트 통합
```

### I4. 차트 관련 페이지

```
# 모듈 개발 요청: 차트 에디터 페이지

파일: apps/web/app/(chart)/chart-editor/[id]/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/charts/server
- @e-torch/server-api
- next/navigation

## 기능 요구사항
- 차트 에디터 페이지 구현
- 특정 차트 편집 기능
- 동적 라우트 파라미터 처리

## 구현 지침
- 서버 컴포넌트로 구현
- 동적 ID 파라미터 처리
- 서버에서 차트 데이터 페칭
- 편집 권한 검증
```

```
# 모듈 개발 요청: 차트 상세 페이지

파일: apps/web/app/(chart)/chart/[id]/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/charts/server
- @e-torch/server-api
- next/navigation

## 기능 요구사항
- 차트 상세 페이지 구현
- 특정 차트 조회 및 표시
- 동적 라우트 파라미터 처리

## 구현 지침
- 서버 컴포넌트로 구현
- 동적 ID 파라미터 처리
- 서버에서 차트 데이터 페칭
- 존재하지 않는 차트 처리 (404)
```

### I5. 프로필 관련 페이지

```
# 모듈 개발 요청: 프로필 설정 페이지

파일: apps/web/app/(profile)/profile/settings/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/server-api

## 기능 요구사항
- 프로필 설정 페이지 구현
- 사용자 설정 관리
- 프로필 정보 편집

## 구현 지침
- 서버 컴포넌트로 구현
- 서버에서 사용자 프로필 데이터 페칭
- 프로필 편집 폼 통합
```

```
# 모듈 개발 요청: 구독 관리 페이지

파일: apps/web/app/(profile)/profile/subscription/page.tsx
의존성: 
- @e-torch/ui/server
- @e-torch/server-api

## 기능 요구사항
- 구독 관리 페이지 구현
- 구독 정보 표시
- 플랜 변경 및 결제 관리

## 구현 지침
- 서버 컴포넌트로 구현
- 서버에서 구독 정보 데이터 페칭
- 결제 관리 컴포넌트 통합
```

### I6. API 라우트 구현

```
# 모듈 개발 요청: 대시보드 API 라우트

파일: apps/web/app/api/dashboards/[...path]/route.ts
의존성: 
- @e-torch/server-api
- next/server

## 기능 요구사항
- 대시보드 API 라우트 구현
- RESTful API 엔드포인트 제공
- 인증 및 권한 검증

## 구현 지침
- Next.js 14 Route Handlers 구현
- GET, POST, PUT, DELETE 메소드 구현
- 인증 미들웨어 통합
- 적절한 응답 및 오류 처리
```

```
# 모듈 개발 요청: 차트 API 라우트

파일: apps/web/app/api/charts/[...path]/route.ts
의존성: 
- @e-torch/server-api
- next/server

## 기능 요구사항
- 차트 API 라우트 구현
- RESTful API 엔드포인트 제공
- 인증 및 권한 검증

## 구현 지침
- Next.js 14 Route Handlers 구현
- GET, POST, PUT, DELETE 메소드 구현
- 인증 미들웨어 통합
- 적절한 응답 및 오류 처리
```

```
# 모듈 개발 요청: 데이터 소스 API 라우트

파일: apps/web/app/api/data-sources/[...path]/route.ts
의존성: 
- @e-torch/server-api
- next/server

## 기능 요구사항
- 데이터 소스 API 라우트 구현
- 지표 검색 및 데이터 조회 엔드포인트 제공
- 캐싱 및 최적화

## 구현 지침
- Next.js 14 Route Handlers 구현
- GET 메소드 구현
- 적절한 캐싱 헤더 설정
- 인증 미들웨어 통합
```

```
# 모듈 개발 요청: 사용자 API 라우트

파일: apps/web/app/api/users/[...path]/route.ts
의존성: 
- @e-torch/server-api
- next/server

## 기능 요구사항
- 사용자 API 라우트 구현
- 프로필 및 구독 관리 엔드포인트 제공
- 인증 및 권한 검증

## 구현 지침
- Next.js 14 Route Handlers 구현
- GET, PUT 메소드 구현
- 인증 미들웨어 통합
- 적절한 응답 및 오류 처리
```

### I7. 미들웨어 구현

```
# 모듈 개발 요청: 애플리케이션 미들웨어

파일: apps/web/middleware.ts
의존성: 
- @e-torch/core/types
- next/server

## 기능 요구사항
- 애플리케이션 레벨 미들웨어 구현
- 인증 상태 검증
- 라우트 보호

## 구현 지침
- routing.md의 8.2 라우트 보호 아키텍처 참조
- Next.js 미들웨어 구현
- 인증 상태 검증 및 리다이렉션 처리
- 공개/보호된 라우트 정의
```

### I8. 서버 액션 구현

```
# 모듈 개발 요청: 애플리케이션 서버 액션

파일: apps/web/app/actions/[action-name].ts
의존성: 
- @e-torch/server-api
- next/cache
- next/navigation

## 기능 요구사항
- 애플리케이션 레벨 서버 액션 구현
- 비즈니스 로직 처리
- 캐시 무효화 및 리다이렉션

## 구현 지침
- 'use server' 지시어 포함
- 구현할 서버 액션 목록:
  1. dashboard.ts - 대시보드 관련 서버 액션
  2. chart.ts - 차트 관련 서버 액션
  3. auth.ts - 인증 관련 서버 액션
  4. user.ts - 사용자 관련 서버 액션
- 각 액션별 적절한 오류 처리 및 결과 반환
```

## J. 테스트 및 배포 (Testing & Deployment)

### J1. 유닛 테스트

```
# 모듈 개발 요청: 유틸리티 유닛 테스트

파일: packages/utils/src/__tests__/[test-name].test.ts
의존성: 
- @e-torch/utils
- vitest
- @testing-library/react

## 기능 요구사항
- 유틸리티 함수 유닛 테스트 구현
- 다양한 테스트 케이스 작성
- 코드 커버리지 확보

## 구현 지침
- describe/it 패턴 사용
- 구현할 테스트 파일 목록:
  1. data-transform.test.ts - 데이터 변환 유틸리티 테스트
  2. formatters.test.ts - 포맷팅 유틸리티 테스트
  3. validation.test.ts - 검증 유틸리티 테스트
  4. helpers.test.ts - 헬퍼 유틸리티 테스트
```

### J2. 컴포넌트 테스트

```
# 모듈 개발 요청: 차트 컴포넌트 테스트

파일: packages/charts/src/__tests__/[test-name].test.tsx
의존성: 
- @e-torch/charts
- vitest
- @testing-library/react

## 기능 요구사항
- 차트 컴포넌트 단위 테스트 구현
- 다양한 상태 및 상호작용 테스트
- 접근성 테스트

## 구현 지침
- describe/it 패턴 사용
- 구현할 테스트 파일 목록:
  1. ChartComponent.test.tsx - 차트 컴포넌트 테스트
  2. TimeSeriesChart.test.tsx - 시계열 차트 테스트
  3. ChartRenderer.test.tsx - 차트 렌더러 테스트
  4. AccessibilityComponents.test.tsx - 접근성 컴포넌트 테스트
```

### J3. 통합 테스트

```
# 모듈 개발 요청: 대시보드 통합 테스트

파일: apps/web/e2e/[test-name].spec.ts
의존성: 
- @playwright/test

## 기능 요구사항
- 대시보드 기능 통합 테스트 구현
- 주요 사용자 시나리오 테스트
- 성능 및 동작 검증

## 구현 지침
- Playwright 테스트 패턴 사용
- 구현할 테스트 파일 목록:
  1. dashboard-creation.spec.ts - 대시보드 생성 테스트
  2. dashboard-editing.spec.ts - 대시보드 편집 테스트
  3. chart-integration.spec.ts - 차트 통합 테스트
  4. user-flows.spec.ts - 주요 사용자 흐름 테스트
```

### J4. 배포 설정

```
# 모듈 개발 요청: Vercel 배포 설정

파일: apps/web/vercel.json
의존성: 없음

## 기능 요구사항
- Vercel 배포 설정 구현
- 환경별 설정 관리
- 캐싱 및 성능 최적화

## 구현 지침
- Vercel 설정 파일 구현
- 환경별 설정 구분 (개발, 스테이징, 프로덕션)
- 적절한 캐싱 설정
- 성능 최적화 설정
```

이 프롬프트 체계는 E-Torch 프로젝트의 기획서, 아키텍처 문서, 기능명세서에서 요구하는 모든 핵심 기능을 구현합니다. 각 패키지와 모듈이 체계적으로 구성되어 있으며, 의존성 관계를 고려하여 순차적 개발이 가능하도록 설계되었습니다. 시작은 핵심 타입 정의부터 하여 점진적으로 UI 컴포넌트, 유틸리티, 상태 관리, 데이터 소스, 차트, 대시보드, 서버 API 순으로 구현한 후 최종적으로 앱 페이지를 완성하는 흐름으로 진행됩니다.
