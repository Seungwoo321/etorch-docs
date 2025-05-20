# E-Torch 경제지표 대시보드 개발 프롬프트 가이드 (강화버전)

## 페이즈 1: 기반 구축

### 1.1.1 모노레포 구조 설정

```
설명: E-Torch의 모노레포 구조를 Turborepo와 pnpm을 사용하여 구축해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '4. 모노레포 패키지 구조 설계' 섹션
- 파일 목록: 'frontend/file-list.md'의 '루트 설정 파일' 섹션
- 패키지 구조: 'README.md'의 '프로젝트 구조' 섹션
- 로드맵: 'ROADMAP.md'의 '1.1.1 모노레포 구조 설정' 체크리스트

주요 설정 파일:
1. turbo.json - Turborepo 설정 (빌드 캐싱, 병렬 실행 설정)
2. package.json - 루트 패키지 설정 (공통 스크립트, 개발 의존성)
3. pnpm-workspace.yaml - PNPM 워크스페이스 설정 (패키지 경로 매핑)
4. eslint.config.mjs - ESLint 9 Flat Config 설정 (Standard JS/JSX 규칙)
5. tsconfig.json - 루트 TypeScript 설정 (공통 타입 설정, 경로 매핑)

필요한 packages 디렉토리 구조:
- eslint-config: 코드 스타일 및 품질 규칙
- core: 타입 정의, 상수 관리
- ui: Shadcn/UI 기반 UI 컴포넌트
- charts: 차트 관련 컴포넌트
- dashboard: 대시보드 관련 컴포넌트
- data-sources: 데이터 소스 연동
- utils: 유틸리티 함수
- state: 상태 관리
- server-api: API 클라이언트 및 서버 액션

필요한 apps 디렉토리 구조:
- web (Next.js 15 앱)
- storybook (컴포넌트 문서화)

기술 스택:
- React 19
- Next.js 15
- Tailwind CSS 4
- TypeScript 5.5+
```

### 1.1.2 디자인 시스템 구현

```
설명: Shadcn/UI와 Tailwind CSS 4를 기반으로 한 디자인 시스템을 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '4.3 UI 컴포넌트와 서버/클라이언트 통합 전략' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/ui' 섹션
- 기획서: 'product-spec.md'의 '6.1 디자인 시스템 개요' 섹션
- 디자인 가이드: 'ui-design/design-system.md' 전체 문서 참조
- 로드맵: 'ROADMAP.md'의 '1.1.2 디자인 시스템 구현' 체크리스트

핵심 컴포넌트 구현:
1. Button - 다양한 변형(primary, secondary, outline, ghost, link)과 크기(default, sm, lg, icon) 지원
2. Card - 대시보드 카드 컴포넌트 (CardHeader, CardTitle, CardContent, CardFooter)
3. Dialog - 모달 다이얼로그 (DashboardShareDialog 등에 활용)
4. 폼 요소 - Input, Select, Checkbox, Switch 등 (구독 설정 등에 활용)
5. Navigation - 메인 네비게이션 컴포넌트 (SideNavigation, KeyboardNavMenu)
6. 레이아웃 - Container, Grid 시스템 (DashboardGrid의 기반)
7. 접근성 컴포넌트 - SkipLink, VisuallyHidden, FocusTrap (WCAG 2.1 AA 준수)

OKLCH 색상 체계:
- Primary: oklch(0.205 0 0) / oklch(0.985 0 0) (라이트/다크 모드)
- Secondary: oklch(0.97 0 0) / oklch(0.269 0 0) (라이트/다크 모드)
- Accent: oklch(0.97 0 0) / oklch(0.269 0 0) (라이트/다크 모드)
- 차트 색상: --chart-1 ~ --chart-5 변수 정의 ('ui-design/design-system.md'의 globals.css 설정 참조)

타이포그래피:
- Inter: 기본 폰트 (UI 요소, 내비게이션, 헤더용)
- JetBrains Mono: 모노스페이스 폰트 (데이터, 코드, 수치값용)
- 타입 스케일: Display, H1~H4, Body, Small, XSmall, Data Value 등 정의

서버/클라이언트 컴포넌트 래퍼 패턴 구현 (Next.js 15 최적화):
- 서버 컴포넌트 래퍼 작성 (ButtonServer, CardServer 등)
- 클라이언트 컴포넌트 분리 ("use client" 지시어 사용)
- 'ui-design/design-system.md'의 '6.2 서버 컴포넌트 래퍼 패턴' 참조
```

### 1.1.3 상태 관리 기본 구조

```
설명: Zustand 5와 TanStack Query 5를 활용한 상태 관리 기본 구조를 구현해주세요.

참조:
- 상태 관리 문서: 'frontend/state-management.md' 전체 참조
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '5. 데이터 페칭 전략' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/state' 섹션
- 아키텍처 문서: 'frontend/architecture.md'의 '9. 데이터 흐름 통합 다이어그램' 섹션
- 로드맵: 'ROADMAP.md'의 '1.1.3 상태 관리 기본 구조' 체크리스트

구현 항목:
1. 앱 전역 상태 스토어 (packages/state/src/stores/app-store.ts)
   - 테마 상태 (라이트/다크 모드)
   - 전역 알림 상태 (토스트, 알림)
   - 전역 에러 상태 (오류 처리)
   - 로딩 상태 (스켈레톤, 스피너)

2. 대시보드 상태 스토어 (packages/state/src/stores/dashboard-store.ts)
   - 정규화된 상태 구조 ('frontend/state-management.md'의 '5. 정규화된 상태 구조' 참조)
   - 대시보드 CRUD 액션
   - 레이아웃 관리 액션
   - 시간 범위 및 주기 상태

3. 차트 에디터 상태 스토어 (packages/state/src/stores/chart-editor-store.ts)
   - 차트 속성 업데이트 액션
   - 언두/리두 시스템
   - 차트 유형 및 옵션 상태

4. TanStack Query 설정 (packages/state/src/providers/query-provider.tsx)
   - 기본 Query Client 설정
   - 쿼리 키 구조 설계 ('frontend/state-management.md'의 '7.3 쿼리 키 설계 전략' 참조)
   - 경제지표 데이터를 위한 캐싱 전략

5. Zustand 미들웨어 적용
   - immer: 상태 불변성 관리
   - persist: localStorage 저장
   - devtools: 개발 도구 연동
   - subscribeWithSelector: 성능 최적화

6. 서버/클라이언트 상태 통합
   - 서버 액션과 TanStack Query 통합 ('frontend/state-management.md'의 '11. 서버 액션 통합' 참조)
   - React 19의 useOptimistic 훅 활용 ('frontend/architecture.md'의 '12.3 React 19의 최적화 기능 활용' 참조)
```

### 1.1.4 라우팅 구조 설정

```
설명: Next.js 15 App Router 기반의 라우팅 구조를 설정해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md' 전체 참조
- 파일 목록: 'frontend/file-list.md'의 'apps/web' 섹션
- 와이어프레임: 'ui-design/wireframes' 폴더의 다양한 페이지 레이아웃 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3. 핵심 사용자 흐름' 섹션
- 로드맵: 'ROADMAP.md'의 '1.1.4 라우팅 구조 설정' 체크리스트

구현 항목:
1. 기본 라우트 그룹 설정 ('frontend/routing.md'의 '3. 라우팅 구조 설계' 참조)
   - (auth) - 인증 관련 페이지 (로그인, 콜백)
   - (dashboard) - 대시보드 관련 페이지 (목록, 상세, 편집, 탐색)
   - (chart) - 차트 관련 페이지 (에디터, 상세)
   - (profile) - 사용자 프로필 관련 페이지 (설정, 구독, 알림)

2. 레이아웃 컴포넌트 구현 ('frontend/routing.md'의 '6. 레이아웃 구조' 참조)
   - RootLayout: 전역 CSS, 메타데이터, 테마 제공자, Inter/JetBrains_Mono 폰트 설정
   - AuthLayout: 로그인 페이지용 레이아웃 ('ui-design/user-flows.md'의 '3.1 온보딩 및 로그인 흐름' 참조)
   - DashboardLayout: 대시보드 페이지용 레이아웃 (사이드바, 헤더 포함)
   - ChartLayout: 차트 에디터 페이지용 레이아웃
   - ProfileLayout: 프로필 페이지용 레이아웃

3. 동적 라우트 설정 ('frontend/routing.md'의 '7. 동적 라우팅 전략' 참조)
   - /dashboard/[id] - 대시보드 상세 페이지
   - /dashboard/[id]/edit - 대시보드 편집 페이지
   - /chart/[id] - 차트 상세 페이지
   - /chart-editor/[id] - 차트 에디터 페이지

4. 메타데이터 설정 ('frontend/routing.md'의 '10. 메타데이터 전략' 참조)
   - 기본 메타데이터 (타이틀, 설명, 오픈 그래프)
   - 동적 메타데이터 생성 (대시보드 제목 기반)
   - 공유 시 OG 이미지 생성

5. 인터셉트 라우트와 병렬 라우트 구현 ('frontend/file-list.md'의 'apps/web' 마지막 부분 참조)
   - @modal/dashboard/[id]/page.tsx - 대시보드 모달 (인터셉트 라우트)
   - @dashboard/info/page.tsx - 대시보드 정보 탭 (병렬 라우트)
   - @dashboard/share/page.tsx - 대시보드 공유 탭 (병렬 라우트)
```

### 1.2.1 Supabase 인증 연동

```
설명: Supabase를 활용한 SNS 로그인 인증 시스템을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '3.2 인증 및 접근 제어' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/lib/supabase.ts' 파일
- 기획서: 'product-spec.md'의 '3.1 사용자 관리 시스템' 섹션
- 와이어프레임: 'ui-design/wireframes/login-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.1 온보딩 및 로그인 흐름' 섹션
- 로드맵: 'ROADMAP.md'의 '1.2.1 Supabase 인증 연동' 체크리스트

구현 항목:
1. Supabase 클라이언트 설정 (apps/web/lib/supabase.ts)
   - 환경 변수 설정 (.env.local)
   - 서버 사이드 클라이언트
   - 클라이언트 사이드 클라이언트

2. 인증 프로바이더 구현 (packages/state/src/providers/auth-provider.tsx)
   - 인증 상태 관리
   - 인증 컨텍스트 제공
   - 'ui-design/user-flows.md'의 인증 흐름도 참고

3. SNS 로그인 연동
   - Google OAuth ('ui-design/user-flows.md'의 '3.1 온보딩 및 로그인 흐름' 참조)
   - Naver OAuth
   - Kakao OAuth
   - 인증 콜백 처리

4. 인증 페이지 구현 (apps/web/app/(auth)/login/page.tsx)
   - 로그인 UI ('ui-design/wireframes/login-wireframe.svg' 참조)
   - SNS 로그인 버튼
   - 접근성 고려 (키보드 내비게이션, 스크린 리더 지원)

5. 콜백 핸들러 구현 (apps/web/app/(auth)/callback/page.tsx)
   - 인증 코드 처리
   - 세션 저장 (apps/web/lib/session.ts)
   - 리디렉션 처리 (첫 방문자: 서비스 소개, 재방문자: 대시보드)

6. 인증 훅 구현
   - useAuth 훅 (packages/state/src/hooks/use-auth.ts)
   - useAuthStore 스토어 (packages/state/src/stores/auth-store.ts)
   - 인증 상태 접근 및 제어 (로그인, 로그아웃, 세션 확인)
```

### 1.2.2 인증 및 접근 제어

```
설명: 라우트 보호 및 권한 관리 시스템을 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '8. 네비게이션 및 라우트 보호' 섹션
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '3.2 인증 및 접근 제어' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/middleware.ts' 파일
- 아키텍처 문서: 'frontend/architecture.md'의 '6. 인증 아키텍처' 섹션
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.1 온보딩 및 로그인 흐름' 섹션
- 로드맵: 'ROADMAP.md'의 '1.2.2 인증 및 접근 제어' 체크리스트

구현 항목:
1. 미들웨어 구현 (apps/web/middleware.ts)
   - 공개/보호된 라우트 정의
   - 세션 검증 (JWT 토큰 확인)
   - 인증 리디렉션 (인증되지 않은 사용자 로그인 페이지로 리디렉트)
   - 'ui-design/user-flows.md'의 인증 상태 흐름도 참고

2. 서버 컴포넌트 인증 유틸리티
   - 세션 검증 (apps/web/lib/session.ts)
   - 권한 검증 (대시보드 소유권 확인 등)
   - 'frontend/architecture.md'의 '6. 인증 아키텍처' 섹션 활용

3. 클라이언트 인증 가드 컴포넌트
   - AuthGuard 컴포넌트 구현
   - 로딩 상태 처리 (인증 체크 중 스켈레톤 UI 표시)
   - 'ui-design/user-flows.md'의 'AuthGuard 검증' 부분 참조

4. 컨텍스트 기반 접근 제어
   - 대시보드 소유권 확인 (자신의 대시보드만 편집 가능)
   - CRUD 작업별 권한 확인
   - Supabase RLS(Row Level Security) 정책 연동

5. 인증 관련 훅
   - useRequireAuth 훅 (인증 필수 페이지용)
   - useOptionalAuth 훅 (인증 선택 페이지용)
   - 'frontend/architecture.md'의 인증 상태 관리 접근법 활용
```

## 페이즈 2: 핵심 기능

### 2.1.1 API 클라이언트 구현

```
설명: HTTP 클라이언트와 도메인별 API 클라이언트를 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '4. API 클라이언트 설계' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/server-api/src/client' 섹션
- 아키텍처 문서: 'frontend/architecture.md'의 '4.6 packages/server-api' 섹션
- 로드맵: 'ROADMAP.md'의 '2.1.1 API 클라이언트 구현' 체크리스트

구현 항목:
1. 기본 HTTP 클라이언트 (packages/server-api/src/client/api-client.ts)
   - Fetch API 래핑
   - 에러 핸들링 (네트워크 오류, API 오류 등)
   - 인터셉터 지원 (요청/응답 변환)
   - 'ROADMAP.md'의 '2.1.1 API 클라이언트 구현' 체크리스트 참조

2. 인증 인터셉터 (packages/server-api/src/client/auth-api-client.ts)
   - JWT 토큰 관리
   - 401 에러 자동 리프레시
   - 헤더 추가 (Authorization)
   - 'frontend/architecture.md'의 '6. 인증 아키텍처' 참조

3. 도메인별 API 클라이언트 구현
   - 대시보드 API 클라이언트 (packages/server-api/src/client/dashboard-api-client.ts)
   - 차트 API 클라이언트 (packages/server-api/src/client/chart-api-client.ts)
   - 데이터 API 클라이언트 (packages/server-api/src/client/data-api-client.ts)
   - 'frontend/file-list.md'의 각 API 클라이언트 파일 참조

4. API 에러 클래스 계층 구현
   - 기본 API 에러 클래스
   - 네트워크 에러 클래스
   - 인증 에러 클래스
   - 비즈니스 로직 에러 클래스
   - 'ROADMAP.md'의 '5. 오류 처리 및 예외 상황' 참조
```

### 2.1.2 데이터 변환 파이프라인

```
설명: 경제지표 데이터 변환 및 처리 파이프라인을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '6. 데이터 변환 및 처리 파이프라인' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/data-sources/src/transforms' 섹션
- 기획서: 'product-spec.md'의 '7.4 데이터 변환 및 처리 파이프라인' 섹션
- 아키텍처 문서: 'frontend/components/core-components.md'의 '4.4 차트 데이터 흐름 통합 다이어그램' 섹션
- 로드맵: 'ROADMAP.md'의 '2.1.2 데이터 변환 파이프라인' 체크리스트

구현 항목:
1. 통합 데이터 모델 정의 (packages/core/src/types/data-source.ts)
   - NormalizedTimeSeriesData 인터페이스
   - 지표 메타데이터 타입
   - 'product-spec.md'의 '부록: 제공 경제지표 상세 목록' 참조

2. 데이터 변환 구현
   - 원본값 변환 (packages/data-sources/src/transforms/index.ts)
   - 변화율 변환 (packages/data-sources/src/transforms/change-rate.ts)
   - 누적값 계산 (packages/data-sources/src/transforms/cumulative.ts)
   - 'ui-design/user-flows.md'의 '3.4 데이터 소스 조회 및 분석 흐름' 참조

3. 결측치 처리 알고리즘 (packages/data-sources/src/transforms/interpolation.ts)
   - 선형 보간법
   - 직전/다음 값 복제
   - 'ROADMAP.md'의 '5. 오류 처리 및 예외 상황' 표 참조

4. 기간 동기화 (packages/data-sources/src/transforms/synchronization.ts)
   - 업샘플링 (낮은 주기→높은 주기)
   - 다운샘플링 (높은 주기→낮은 주기)
   - 'ui-design/user-flows.md'의 기간 및 주기 변경 관련 흐름 참조

5. 다운샘플링 알고리즘 (packages/data-sources/src/transforms/downsampling.ts)
   - LTTB(Largest-Triangle-Three-Buckets) 알고리즘
   - M4 알고리즘
   - 'frontend/components/core-components.md'의 '9.2 데이터 다운샘플링' 참조
```

### 2.1.3 데이터 캐싱 전략 구현

```
설명: TanStack Query를 활용한 다층적 데이터 캐싱 전략을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '7. 데이터 캐싱 전략' 섹션
- 상태 관리 문서: 'frontend/state-management.md'의 '7. 서버 상태 관리' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/state/src/providers/query-provider.tsx' 파일
- 로드맵: 'ROADMAP.md'의 '2.1.3 데이터 캐싱 전략 구현' 체크리스트

구현 항목:
1. TanStack Query 설정 (packages/state/src/providers/query-provider.tsx)
   - 기본 캐싱 설정
   - staleTime, gcTime 최적화
   - refetchOnWindowFocus 설정
   - 'ROADMAP.md'의 '2.1.3 데이터 캐싱 전략 구현' 체크리스트 참조

2. 지표 유형별 캐싱 설정
   - 금융 실시간 데이터 (1분)
   - 일별 금융 데이터 (30분)
   - 월간 경제지표 (3시간)
   - 분기/연간 데이터 (12시간)
   - 'product-spec.md'의 '7.2 데이터 업데이트 주기' 참조

3. 캐시 무효화 전략 구현
   - 시간 기반 무효화
   - 이벤트 기반 무효화
   - 선택적 무효화
   - 선제적 리페치
   - 'ROADMAP.md'의 '2.1.3 데이터 캐싱 전략 구현' 체크리스트 참조

4. 서버 상태 훅 구현
   - useChartData 훅 (packages/data-sources/src/hooks/useChartData.ts)
   - useIndicatorSearch 훅 (packages/data-sources/src/hooks/useIndicatorSearch.ts)
   - useDataTransform 훅 (packages/data-sources/src/hooks/useDataTransform.ts)
   - 'frontend/file-list.md'의 'packages/data-sources/src/hooks' 섹션 참조
```

### 2.2.1 기본 차트 컴포넌트

```
설명: Recharts를 활용한 기본 차트 컴포넌트를 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '7. 차트 컴포넌트 설계' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/charts/src/components' 섹션
- 와이어프레임: 'ui-design/wireframes/improved-chart-editor-wireframe.svg' 참조
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '4.1 차트 컴포넌트 계층 구조' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '3. 차트 컴포넌트 상세 기능 명세' 섹션
- 로드맵: 'ROADMAP.md'의 '2.2.1 기본 차트 컴포넌트' 체크리스트

구현 항목:
1. 서버/클라이언트 구조 설계
   - ChartServerWrapper 서버 컴포넌트 (packages/charts/src/server/ChartServerWrapper.tsx)
   - ChartDataLoader 서버 컴포넌트 (packages/charts/src/server/ChartDataLoader.tsx)
   - ChartComponent 클라이언트 컴포넌트 (packages/charts/src/components/ChartComponent.tsx)
   - 'frontend/components/core-components.md'의 '4.3 서버/클라이언트 통합 패턴' 참조

2. 렌더링 로직 분리
   - ChartRenderer 컴포넌트 (packages/charts/src/components/ChartRenderer.tsx)
   - 차트 유형별 분기 처리
   - 'ui-design/ui-requirements.md'의 '3.4 차트 컴포넌트별 활성화 옵션 영역' 참조

3. 차트 유형별 컴포넌트 구현
   - TimeSeriesChart (packages/charts/src/components/chart-types/TimeSeriesChart.tsx)
   - BarChart (packages/charts/src/components/chart-types/BarChart.tsx)
   - ScatterChart (packages/charts/src/components/chart-types/ScatterChart.tsx)
   - RadarChart (packages/charts/src/components/chart-types/RadarChart.tsx)
   - RadialBarChart (packages/charts/src/components/chart-types/RadialBarChart.tsx)
   - 'ui-design/ui-requirements.md'의 '3.1~3.3 공통 옵션 컴포넌트' 참조

4. 공통 컴포넌트 구현
   - ChartControls (packages/charts/src/components/ChartControls.tsx)
   - ChartLegend (packages/charts/src/components/ChartLegend.tsx)
   - ChartTooltip (packages/charts/src/components/ChartTooltip.tsx)
   - ChartAxisLabel (packages/charts/src/components/ChartAxisLabel.tsx)
   - 'ui-design/ui-requirements.md'의 '3.1.2~3.1.6 축, 범례, 툴팁 옵션' 참조

5. 접근성 지원
   - AccessibleChartTable (packages/charts/src/components/AccessibleChartTable.tsx)
   - 'ui-design/user-flows.md'의 '6. 접근성 고려사항' 참조
   - 'ui-design/design-system.md'의 '7. 접근성 가이드라인' 참조
```

### 2.2.2 차트 옵션 패널

```
설명: 차트 커스터마이징을 위한 옵션 패널 컴포넌트를 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '7.2 차트 에디터 컴포넌트' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/charts/src/editor/options' 섹션
- 와이어프레임: 'ui-design/wireframes/improved-chart-editor-wireframe.svg' 참조
- UI 요구사항: 'ui-design/ui-requirements.md'의 '3. 차트 컴포넌트 상세 기능 명세' 섹션
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.3 차트 생성 및 편집 흐름' 섹션
- 로드맵: 'ROADMAP.md'의 '2.2.2 차트 옵션 패널' 체크리스트

구현 항목:
1. 옵션 패널 메인 컴포넌트 (packages/charts/src/editor/OptionsPanel.tsx)
   - 아코디언 구조
   - 옵션 그룹 관리
   - 차트 유형별 활성화 옵션 관리
   - 'ui-design/ui-requirements.md'의 '3.4 차트 컴포넌트별 활성화 옵션 영역' 참조

2. 공통 옵션 컴포넌트
   - PanelOptions (packages/charts/src/editor/options/PanelOptions.tsx)
     - 제목, 설명, 배경 등 설정 (PO-001~003)
   - TooltipOptions (packages/charts/src/editor/options/TooltipOptions.tsx)
     - 툴팁 표시 방식, 커서 스타일 설정 (TO-001~005)
   - LegendOptions (packages/charts/src/editor/options/LegendOptions.tsx)
     - 범례 위치, 스타일 설정 (LG-001~004)
   - 'ui-design/ui-requirements.md'의 '3.1 공통 옵션 컴포넌트' 참조

3. 축 관련 옵션 컴포넌트
   - AxisOptions (packages/charts/src/editor/options/AxisOptions.tsx)
     - X축, Y축 설정 (XA-001~010, YA-001~011, YAS-001~011)
     - 그리드 라인 설정
     - 축 레이블 설정
   - 'ui-design/ui-requirements.md'의 '3.1.4~3.1.6 X축/Y축 옵션' 참조

4. 차트별 특화 옵션 컴포넌트
   - StyleOptions (packages/charts/src/editor/options/StyleOptions.tsx)
     - 색상, 선 스타일, 패턴 등 (GS-001~003)
   - ScatterOptions (packages/charts/src/editor/options/ScatterOptions.tsx)
     - 포인트 크기, 모양, 회귀선 등 (SC-001~006)
   - RadarOptions (packages/charts/src/editor/options/RadarOptions.tsx)
     - 그리드 모양, 축 선, 영역 채우기 등 (RC-001~006)
   - RadialBarOptions (packages/charts/src/editor/options/RadialBarOptions.tsx)
     - 각도, 반경, 배경 등 (RB-001~010)
   - 'ui-design/ui-requirements.md'의 '3.2 차트별 전용 옵션 컴포넌트' 참조

5. 옵션 상태 관리 훅
   - useChartOptions 훅 구현
   - 'frontend/components/core-components.md'의 '4.3 서버/클라이언트 통합 패턴' 활용
```

### 2.2.3 차트 에디터

```
설명: 차트 생성 및 편집을 위한 에디터 컴포넌트를 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '7.2 차트 에디터 컴포넌트' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/charts/src/editor' 섹션
- 와이어프레임: 'ui-design/wireframes/improved-chart-editor-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.3 차트 생성 및 편집 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '3. 차트 컴포넌트 상세 기능 명세' 섹션
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '4.2 차트 에디터 컴포넌트' 섹션
- 로드맵: 'ROADMAP.md'의 '2.2.3 차트 에디터' 체크리스트

구현 항목:
1. 에디터 레이아웃
   - ChartEditor 메인 컴포넌트 (packages/charts/src/editor/ChartEditor.tsx)
   - Resizable 컴포넌트 활용 (packages/ui/src/components/ui/resizable.tsx)
   - 'ui-design/user-flows.md'의 차트 에디터 흐름도 참조

2. 차트 미리보기
   - ChartPreview 컴포넌트 (packages/charts/src/editor/ChartPreview.tsx)
   - 실시간 옵션 반영
   - 데이터 미리보기 기능
   - 'ui-design/ui-requirements.md'의 데이터 미리보기 관련 요구사항 참조

3. 차트 유형 선택
   - ChartTypeSelector 컴포넌트 (packages/charts/src/editor/ChartTypeSelector.tsx)
   - 시계열, 바 차트, 산점도, 레이더, 방사형 바 차트 지원
   - 'ui-design/user-flows.md'의 차트 유형 선택 흐름 참조

4. 데이터 관련 컴포넌트
   - DataSourcePanel 컴포넌트 (packages/charts/src/editor/DataSourcePanel.tsx)
   - DataQueryCard 컴포넌트 (packages/charts/src/editor/DataQueryCard.tsx)
   - TimeRangeSelector 컴포넌트 (packages/charts/src/editor/TimeRangeSelector.tsx)
   - PeriodSelector 컴포넌트 (packages/charts/src/editor/PeriodSelector.tsx)
   - 'ui-design/ui-requirements.md'의 '3.3 데이터 소스 설정' 참조

5. 에디터 상태 관리
   - 상태 관리 연동 ('frontend/state-management.md'의 '4.3 차트 에디터 스토어 예시' 참조)
   - URL 쿼리 파라미터 활용
   - 에디터 상태 저장 및 복원
   - 'ui-design/user-flows.md'의 차트 에디터 상태 전이 다이어그램 참조
```

### 2.2.4 차트 데이터 최적화

```
설명: 차트 데이터 렌더링 최적화 전략을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '6.4 데이터 다운샘플링 알고리즘' 섹션
- 아키텍처 문서: 'frontend/architecture.md'의 '12. 컴포넌트 성능 최적화' 섹션
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '9. 컴포넌트 성능 최적화' 섹션
- 로드맵: 'ROADMAP.md'의 '2.2.4 차트 데이터 최적화' 체크리스트
- UI 요구사항: 'ui-design/ui-requirements.md'의 '5.3 데이터 처리 제약사항' 섹션

구현 항목:
1. 다운샘플링 알고리즘 통합
   - LTTB 알고리즘 활용 (packages/data-sources/src/transforms/downsampling.ts)
   - M4 알고리즘 활용
   - 'ui-design/ui-requirements.md'의 데이터 제약사항 L-DATA-001~004 준수

2. 메모이제이션 최적화
   - React.memo 활용
   - useMemo 최적화
   - useCallback 최적화
   - 'frontend/components/core-components.md'의 '4.3 차트 커스텀 렌더링 최적화' 패턴 활용

3. 코드 분할 및 지연 로딩
   - 차트 유형별 다이나믹 임포트 (Next.js dynamic import)
   - 차트 스켈레톤 로딩 UI
   - 'frontend/components/core-components.md'의 '9.1 코드 분할 및 지연 로딩' 예시 활용

4. 점진적 데이터 로딩
   - 점진적 로딩 구현 (ProgressiveChart)
   - 데이터 청크 단위 로딩
   - 'ui-design/user-flows.md'의 '4.1 데이터 로딩 상태 관리' 다이어그램 참조

5. 성능 측정 및 최적화
   - React DevTools Profiler 활용
   - 렌더링 성능 측정
   - 'ui-design/ui-requirements.md'의 '5.1 성능 요구사항' 목표값 달성
```

### 2.3.1 대시보드 목록 페이지

```
설명: 사용자의 대시보드 목록을 표시하는 페이지를 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.2 대시보드 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(dashboard)/dashboard/page.tsx' 파일
- 와이어프레임: 'ui-design/wireframes/dashboard-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.2 대시보드 관리 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1 대시보드 관리 기능' 섹션
- 로드맵: 'ROADMAP.md'의 '2.3.1 대시보드 목록 페이지' 체크리스트

구현 항목:
1. 대시보드 목록 페이지 (apps/web/app/(dashboard)/dashboard/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 초기 데이터 로딩
   - 'ui-design/ui-requirements.md'의 '2.1.1 대시보드 목록 및 탐색' 참조

2. 대시보드 목록 컴포넌트
   - DashboardList 컴포넌트 (packages/dashboard/src/components/DashboardList.tsx)
   - DashboardCard 컴포넌트 (packages/dashboard/src/components/DashboardCard.tsx)
   - 'ui-design/ui-requirements.md'의 DL-001~005 요구사항 준수

3. 검색 및 필터링
   - DashboardSearch 컴포넌트 (DL-002)
   - 정렬 옵션 (생성일, 수정일, 이름순) (DL-003)
   - 'ui-design/user-flows.md'의 대시보드 필터링 및 정렬 흐름 참조

4. 새 대시보드 생성
   - NewDashboardButton 컴포넌트 (DL-005)
   - 대시보드 생성 플로우 연결
   - 'ui-design/user-flows.md'의 '새 대시보드 생성' 흐름 참조

5. 빈 상태 및 로딩 처리
   - EmptyDashboard 컴포넌트 (packages/dashboard/src/components/EmptyDashboard.tsx)
   - DashboardSkeleton 컴포넌트 (packages/dashboard/src/components/DashboardSkeleton.tsx)
   - 'ui-design/user-flows.md'의 데이터 로딩 상태 관리 다이어그램 참조
```

### 2.3.2 대시보드 상세 페이지

```
설명: 개별 대시보드를 조회하는 상세 페이지를 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.2 대시보드 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(dashboard)/dashboard/[id]/page.tsx' 파일
- 와이어프레임: 'ui-design/wireframes/dashboard-view-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.2 대시보드 관리 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.2 대시보드 상세 보기' 섹션
- 로드맵: 'ROADMAP.md'의 '2.3.2 대시보드 상세 페이지' 체크리스트

구현 항목:
1. 대시보드 상세 페이지 (apps/web/app/(dashboard)/dashboard/[id]/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 대시보드 ID 기반 데이터 로딩
   - 404 처리
   - 'ui-design/ui-requirements.md'의 '2.1.2 대시보드 상세 보기' 참조

2. 대시보드 서버 래퍼
   - DashboardServerWrapper 컴포넌트 (packages/dashboard/src/server/DashboardServerWrapper.tsx)
   - DashboardLoader 컴포넌트 (packages/dashboard/src/server/DashboardLoader.tsx)
   - 'frontend/components/core-components.md'의 '5.3 서버 컴포넌트와 클라이언트 컴포넌트 통합 예시' 참조

3. 대시보드 헤더
   - DashboardHeader 컴포넌트 (packages/dashboard/src/components/DashboardHeader.tsx)
   - 제목, 설명, 메타 정보
   - 'ui-design/ui-requirements.md'의 DV-001~007 요구사항 준수

4. 대시보드 제어
   - TimeRangeControl 컴포넌트 (packages/dashboard/src/components/TimeRangeControl.tsx) (DV-001)
   - PeriodSelector 컴포넌트 (DV-002)
   - RefreshControl 컴포넌트 (packages/dashboard/src/components/RefreshControl.tsx) (DV-003, DV-004)
   - ViewOptions 컴포넌트 (packages/dashboard/src/components/ViewOptions.tsx)
   - 'ui-design/user-flows.md'의 대시보드 제어 흐름 참조

5. 기능 버튼
   - 편집 버튼 (DV-007)
   - 공유 버튼 (DV-005)
   - 내보내기 버튼 (DV-006)
   - 'ui-design/ui-requirements.md'의 대시보드 제어 관련 요구사항 준수
```

### 2.3.3 대시보드 그리드 시스템

```
설명: react-grid-layout을 활용한 대시보드 그리드 시스템을 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '8. 대시보드 컴포넌트 설계' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/dashboard/src/components' 섹션
- 와이어프레임: 'ui-design/wireframes/dashboard-view-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.2 대시보드 관리 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.2 대시보드 상세 보기' 섹션
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '5.1 대시보드 그리드 컴포넌트' 섹션
- 로드맵: 'ROADMAP.md'의 '2.3.3 대시보드 그리드 시스템' 체크리스트

구현 항목:
1. 그리드 레이아웃 설정
   - react-grid-layout 라이브러리 설정
   - 반응형 그리드 설정
   - 'ui-design/user-flows.md'의 '4.3 드래그 앤 드롭 인터랙션' 다이어그램 참조

2. 그리드 컴포넌트
   - DashboardGrid 컴포넌트 (packages/dashboard/src/components/DashboardGrid.tsx)
   - GridItem 컴포넌트 (packages/dashboard/src/components/GridItem.tsx)
   - GridItemHeader 컴포넌트 (packages/dashboard/src/components/GridItemHeader.tsx)
   - GridItemContent 컴포넌트 (packages/dashboard/src/components/GridItemContent.tsx)
   - 'ui-design/ui-requirements.md'의 DE-003~007 구현

3. 위젯 컴포넌트
   - ChartItem 컴포넌트 (packages/dashboard/src/components/ChartItem.tsx)
   - TextItem 컴포넌트 (packages/dashboard/src/components/TextItem.tsx)
   - 'ui-design/ui-requirements.md'의 위젯 관련 요구사항 준수

4. 그리드 상태 관리
   - 레이아웃 상태 관리
   - useTimeRangeSync 훅 구현 ('frontend/state-management.md'의 '9.2 대시보드-차트 컴포넌트 간 상태 동기화' 참조)
   - 'ui-design/user-flows.md'의 '4.2 편집 모드 전환 패턴' 다이어그램 참조

5. 그리드 유틸리티
   - layoutUtils 구현 (packages/dashboard/src/utils/layoutUtils.ts)
   - 그리드 위치 계산
   - 리사이징 제약 처리
   - 'ui-design/user-flows.md'의 드래그 앤 드롭 인터랙션 흐름 참조
```

### 2.3.4 대시보드 편집기

```
설명: 대시보드를 생성하고 편집하는 편집기를 구현해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '8.2 대시보드 편집기 컴포넌트' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/dashboard/src/editor' 섹션
- 와이어프레임: 'ui-design/wireframes/dashboard-editor-wireframe.svg' 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.2 대시보드 관리 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.3 대시보드 편집' 섹션
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '5.2 대시보드 편집기 컴포넌트' 섹션
- 로드맵: 'ROADMAP.md'의 '2.3.4 대시보드 편집기' 체크리스트

구현 항목:
1. 편집기 페이지
   - 새 대시보드 페이지 (apps/web/app/(dashboard)/dashboard/new/page.tsx)
   - 대시보드 편집 페이지 (apps/web/app/(dashboard)/dashboard/[id]/edit/page.tsx)
   - 'ui-design/ui-requirements.md'의 대시보드 편집 관련 요구사항 참조

2. 편집기 메인 컴포넌트
   - DashboardEditor 컴포넌트 (packages/dashboard/src/editor/DashboardEditor.tsx)
   - 'ui-design/user-flows.md'의 대시보드 편집 흐름도 참조

3. 편집 툴바
   - EditorToolbar 컴포넌트 (packages/dashboard/src/editor/EditorToolbar.tsx)
   - SaveButton 컴포넌트 (packages/dashboard/src/editor/SaveButton.tsx) (DE-007)
   - UndoRedoControls 컴포넌트 (packages/dashboard/src/editor/UndoRedoControls.tsx) (DE-009)
   - LayoutControls 컴포넌트 (packages/dashboard/src/editor/LayoutControls.tsx)
   - 'ui-design/ui-requirements.md'의 DE-001~009 요구사항 구현

4. 위젯 선택기
   - WidgetSelector 컴포넌트 (packages/dashboard/src/editor/WidgetSelector.tsx)
   - AddChartButton 컴포넌트 (packages/dashboard/src/editor/AddChartButton.tsx) (DE-003)
   - AddTextButton 컴포넌트 (packages/dashboard/src/editor/AddTextButton.tsx) (DE-003)
   - 'ui-design/user-flows.md'의 위젯 추가 흐름 참조

5. 대시보드 설정
   - DashboardSettingsForm 컴포넌트 (packages/dashboard/src/editor/DashboardSettingsForm.tsx)
   - TitleEditor 컴포넌트 (packages/dashboard/src/editor/TitleEditor.tsx) (DE-001)
   - DescriptionEditor 컴포넌트 (packages/dashboard/src/editor/DescriptionEditor.tsx) (DE-002)
   - 'ui-design/ui-requirements.md'의 대시보드 설정 관련 요구사항 준수

6. 서버 액션 연동
   - saveDashboard 서버 액션 (apps/web/app/actions/dashboard.ts)
   - React.useOptimistic 활용 (낙관적 UI 업데이트)
   - 'frontend/components/core-components.md'의 '9.3 React 19의 최적화 기능 활용' 예시 활용
```

### 2.3.5 대시보드 공유 기능

```
설명: 대시보드 공유 및 접근 권한 관리 기능을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '10. 데이터 내보내기 및 공유' 섹션
- 기획서: 'product-spec.md'의 '3.5 공유 대시보드 시스템' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/dashboard/src/components/DashboardShareDialog.tsx' 파일
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.6 대시보드 공유 및 구독 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.4 대시보드 공유 및 탐색' 섹션
- 로드맵: 'ROADMAP.md'의 '2.3.5 대시보드 공유 기능' 체크리스트

구현 항목:
1. 공유 다이얼로그
   - DashboardShareDialog 컴포넌트 (packages/dashboard/src/components/DashboardShareDialog.tsx)
   - 'ui-design/ui-requirements.md'의 DS-001~003 요구사항 구현
   - 'ui-design/user-flows.md'의 공유 옵션 흐름도 참조

2. 공유 링크 생성
   - 공유 URL 생성 (DS-002)
   - 링크 복사 기능 (useCopyToClipboard 훅 활용)
   - 'ui-design/user-flows.md'의 공유 링크 생성 흐름 참조

3. 소셜 미디어 공유
   - SNS 공유 버튼 (DS-003)
   - 메타데이터 설정 (OG 이미지, 설명)
   - 'ui-design/ui-requirements.md'의 소셜 공유 관련 요구사항 구현

4. 접근 권한 관리
   - 공개/비공개 설정 (DS-001)
   - 사용자별 접근 권한
   - Supabase RLS 정책 연동
   - 'ui-design/user-flows.md'의 isPublic 상태 확인 흐름 참조

5. 대시보드 구독
   - SubscribeButton 컴포넌트 (packages/dashboard/src/explore/SubscribeButton.tsx) (DS-007)
   - 'ui-design/user-flows.md'의 구독 버튼 클릭 흐름 참조
   - 'ui-design/ui-requirements.md'의 구독 관련 요구사항 구현

6. 내보내기 메뉴
   - DashboardExportMenu 컴포넌트 (packages/dashboard/src/components/DashboardExportMenu.tsx)
   - PNG/PDF/CSV 내보내기 기능 (DV-006)
   - 'ui-design/ui-requirements.md'의 내보내기 관련 요구사항 구현
```

## 페이즈 3: 사용자 기능

### 3.1.1 사용자 프로필 관리

```
설명: 사용자 프로필 설정 및 관리 페이지를 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.4 프로필 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(profile)/profile/settings/page.tsx' 파일
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.3.1 인증 및 프로필' 섹션
- 로드맵: 'ROADMAP.md'의 '3.1.1 사용자 프로필 관리' 체크리스트

구현 항목:
1. 프로필 설정 페이지 (apps/web/app/(profile)/profile/settings/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 사용자 데이터 로딩
   - 'ui-design/ui-requirements.md'의 UP-001~006 요구사항 구현

2. 프로필 폼
   - UserInfoForm 컴포넌트
   - React Hook Form + Zod 유효성 검사
   - 프로필 정보 설정 (UP-003)
   - 'frontend/file-list.md'의 폼 컴포넌트 구조 참조

3. 계정 연결 관리
   - ConnectedAccounts 컴포넌트
   - SNS 계정 연동 관리 (UP-005)
   - Google, Naver, Kakao 계정 연결/해제

4. 테마 설정
   - ThemeSettings 컴포넌트
   - 다크/라이트 모드 전환
   - 'ui-design/design-system.md'의 테마 전환 방식 활용

5. 계정 관리
   - DeleteAccount 컴포넌트
   - 계정 삭제 경고 및 확인 (UP-006)
   - 'ui-design/ui-requirements.md'의 계정 관리 요구사항 구현

6. 서버 액션 연동
   - updateProfile 서버 액션 (apps/web/app/actions/auth.ts)
   - React.useOptimistic 활용 (낙관적 UI 업데이트)
   - 'frontend/components/core-components.md'의 "useOptimistic 훅 활용" 예시 참조
```

### 3.1.2 구독 관리 시스템

```
설명: 구독 플랜 관리 및 결제 시스템을 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.4 프로필 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(profile)/profile/subscription/page.tsx' 파일
- 와이어프레임: 'ui-design/wireframes/subscription-wireframe.svg' 참조
- 기획서: 'product-spec.md'의 '3.6 구독 및 결제 시스템' 섹션
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.7 구독 및 결제 관리 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.3.2 구독 관리' 섹션
- 로드맵: 'ROADMAP.md'의 '3.1.2 구독 관리 시스템' 체크리스트

구현 항목:
1. 구독 관리 페이지 (apps/web/app/(profile)/profile/subscription/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 구독 정보 로딩
   - 'ui-design/ui-requirements.md'의 SM-001~006 요구사항 구현

2. 현재 구독 정보
   - CurrentPlanInfo 컴포넌트 (SM-001)
   - 구독 상태, 다음 결제일, 요금 표시
   - 'ui-design/user-flows.md'의 구독 관리 옵션 흐름도 참조

3. 플랜 선택
   - PlanSelector 컴포넌트 (SM-002)
   - 월간/연간 플랜 비교
   - 무료/월간(₩9,900)/연간(₩94,900) 옵션
   - 'ui-design/user-flows.md'의 플랜 선택 흐름 참조

4. 결제 관련
   - PaymentForm 컴포넌트
   - 토스페이먼츠 결제 위젯 통합 (SM-003)
   - PaymentHistory 컴포넌트 (SM-005)
   - PaymentMethods 컴포넌트 (SM-006)
   - 'ui-design/user-flows.md'의 결제 프로세스 흐름 참조

5. 구독 취소
   - CancelSubscription 컴포넌트 (SM-004)
   - 취소 확인 다이얼로그
   - 취소 후 무료 플랜으로 전환
   - 'ui-design/user-flows.md'의 구독 취소 처리 흐름 참조

6. 서버 액션 연동
   - manageSubscription 서버 액션 (apps/web/app/actions/subscription.ts)
   - React.useOptimistic 활용 (낙관적 UI 업데이트)
   - 'frontend/architecture.md'의 "서버 액션 통합 패턴" 예시 활용
```

### 3.1.3 알림 설정 페이지

```
설명: 사용자별, 알림 유형별 알림 설정 페이지를 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.4 프로필 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(profile)/profile/notifications/page.tsx' 파일
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.3.3 알림 설정' 섹션
- 로드맵: 'ROADMAP.md'의 '3.1.3 알림 설정 페이지' 체크리스트

구현 항목:
1. 알림 설정 페이지 (apps/web/app/(profile)/profile/notifications/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 알림 설정 로딩
   - 'ui-design/ui-requirements.md'의 NS-001~006 요구사항 구현

2. 알림 유형 설정
   - NotificationTypesForm 컴포넌트
   - 시스템, 업데이트, 구독 등 알림 유형 설정 (NS-002)
   - 알림 유형별 토글 스위치

3. 알림 채널 설정
   - NotificationChannelsForm 컴포넌트
   - 이메일 알림 설정 (NS-001)
   - 앱 내 알림 설정
   - 채널별 토글 스위치

4. 알림 주기 설정
   - NotificationFrequencyForm 컴포넌트
   - 알림 주기 설정 (NS-003)
   - 즉시, 일일 요약, 주간 요약 등 옵션

5. 알림 목록
   - NotificationsList 컴포넌트 (NS-004)
   - 알림 읽음 표시 (NS-005)
   - 알림 전체 삭제 (NS-006)
   - 알림 필터링 및 정렬

6. 서버 액션 연동
   - updateNotificationSettings 서버 액션
   - React.useOptimistic 활용 (낙관적 UI 업데이트)
   - 'frontend/architecture.md'의 "서버 액션 통합 패턴" 예시 활용
```

## 페이즈 4: 완성 및 배포

### 4.1.1 대시보드 탐색 페이지

```
설명: 공유된 대시보드를 검색하고 탐색하는 페이지를 구현해주세요.

참조:
- 라우팅 문서: 'frontend/routing.md'의 '5.2 대시보드 관련 페이지' 섹션
- 파일 목록: 'frontend/file-list.md'의 'apps/web/app/(dashboard)/explore/page.tsx' 파일
- 와이어프레임: 'ui-design/wireframes/dashboard-explore-wireframe.svg' 참조
- 기획서: 'product-spec.md'의 '3.5 공유 대시보드 시스템' 섹션
- 사용자 흐름: 'ui-design/user-flows.md'의 '3.6 대시보드 공유 및 구독 흐름' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.4 대시보드 공유 및 탐색' 섹션
- 로드맵: 'ROADMAP.md'의 '4.1.1 대시보드 탐색 페이지' 체크리스트

구현 항목:
1. 탐색 페이지 (apps/web/app/(dashboard)/explore/page.tsx)
   - 서버 컴포넌트 기반 구현
   - 초기 데이터 로딩
   - 'ui-design/ui-requirements.md'의 DS-004~008 요구사항 구현

2. 탐색 컴포넌트
   - ExploreList 컴포넌트 (packages/dashboard/src/explore/ExploreList.tsx) (DS-004)
   - ExploreCard 컴포넌트 (packages/dashboard/src/explore/ExploreCard.tsx)
   - 'ui-design/user-flows.md'의 DashboardExplore 페이지 흐름 참조

3. 검색 및 필터링
   - ExploreFilters 컴포넌트 (packages/dashboard/src/explore/ExploreFilters.tsx)
   - 대시보드 검색 (DS-005)
   - 카테고리 필터 (DS-006)
   - 정렬 옵션 (인기순, 최신순 등)
   - 'ui-design/user-flows.md'의 DashboardSearchFilter 흐름 참조

4. 대시보드 미리보기
   - DashboardPreview 컴포넌트 (DS-008)
   - 미리보기 모달 또는 확장 카드
   - 'ui-design/user-flows.md'의 미리보기 흐름 참조

5. 구독 기능
   - SubscribeButton 컴포넌트 (packages/dashboard/src/explore/SubscribeButton.tsx) (DS-007)
   - 구독/구독취소 토글
   - 'ui-design/user-flows.md'의 구독 버튼 클릭 흐름 참조
```

### 4.1.2 데이터 내보내기 기능

```
설명: 차트와 대시보드를 다양한 형식으로 내보내는 기능을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '10. 데이터 내보내기 및 공유' 섹션
- 기획서: 'product-spec.md'의 '3.5 공유 대시보드 시스템' 섹션
- 파일 목록: 'frontend/file-list.md'의 'packages/dashboard/src/components/DashboardExportMenu.tsx' 파일
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.2 대시보드 상세 보기' 섹션 (DV-006)
- 로드맵: 'ROADMAP.md'의 '4.1.2 데이터 내보내기 기능' 체크리스트

구현 항목:
1. 차트 내보내기
   - ChartExport 컴포넌트
   - PNG/SVG/CSV 내보내기 기능 (CM-007)
   - Watermark 컴포넌트 (E-Torch 워터마크 추가)
   - 'ui-design/ui-requirements.md'의 차트 내보내기 관련 요구사항 구현

2. 대시보드 내보내기
   - DashboardExport 컴포넌트
   - PNG/PDF 내보내기 기능 (DV-006)
   - 데이터 내보내기 (CSV/Excel)
   - 'ui-design/ui-requirements.md'의 대시보드 내보내기 관련 요구사항 구현

3. 내보내기 옵션
   - ExportControls 컴포넌트
   - 해상도, 형식, 포함 요소 설정
   - 'ROADMAP.md'의 '4.1.2 데이터 내보내기 기능' 체크리스트 참조

4. 내보내기 유틸리티
   - exportToPng 유틸리티
   - exportToSvg 유틸리티
   - exportToCsv 유틸리티
   - exportToPdf 유틸리티
   - 'frontend/file-list.md'의 유틸리티 함수 구조 참조

5. 공유 기능 연동
   - 소셜 미디어 공유
   - 임베드 코드 생성
   - 'ui-design/ui-requirements.md'의 공유 관련 요구사항 구현
```

### 4.1.3 실시간 데이터 업데이트

```
설명: 실시간 또는 주기적으로 데이터를 업데이트하는 기능을 구현해주세요.

참조:
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '9. 실시간 데이터 업데이트' 섹션
- 상태 관리 문서: 'frontend/state-management.md'의 '9. 이벤트 버스 패턴 및 컴포넌트 간 상태 동기화' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '2.1.2 대시보드 상세 보기' 섹션 (DV-003, DV-004)
- 로드맵: 'ROADMAP.md'의 '4.1.3 실시간 데이터 업데이트' 체크리스트

구현 항목:
1. 데이터 폴링
   - usePolling 훅 구현
   - 자동 새로고침 설정 (10초~5분) (DV-004)
   - 새로고침 버튼 (DV-003)
   - 'ui-design/ui-requirements.md'의 자동 새로고침 관련 요구사항 구현

2. Supabase Realtime 연동
   - useRealtimeData 훅 구현
   - 실시간 업데이트 구독
   - 'ROADMAP.md'의 '4.1.3 실시간 데이터 업데이트' 체크리스트 참조

3. 협업 기능
   - CollaborationPresence 컴포넌트
   - 실시간 사용자 상태 표시
   - 'ROADMAP.md'의 '4.1.3 실시간 데이터 업데이트' 체크리스트 참조

4. 실시간 알림
   - RealtimeNotifications 컴포넌트
   - 중요 업데이트 알림
   - 'ui-design/ui-requirements.md'의 알림 관련 요구사항 구현

5. 데이터 동기화
   - SyncManager 구현
   - 오프라인/온라인 동기화
   - 충돌 해결 전략
   - 'ROADMAP.md'의 '5. 오류 처리 및 예외 상황' 참조
```

### 4.1.4 성능 최적화

```
설명: 전체 애플리케이션의 성능을 최적화해주세요.

참조:
- 아키텍처 문서: 'frontend/architecture.md'의 '12. 컴포넌트 성능 최적화' 섹션
- 데이터 흐름 문서: 'frontend/data-flow.md'의 '8. 클라이언트-서버 통신 최적화' 섹션
- 컴포넌트 설계: 'frontend/components/core-components.md'의 '9. 컴포넌트 성능 최적화' 섹션
- UI 요구사항: 'ui-design/ui-requirements.md'의 '5.1 성능 요구사항' 섹션
- 로드맵: 'ROADMAP.md'의 '4.1.4 성능 최적화' 체크리스트

구현 항목:
1. 코드 분할 및 지연 로딩
   - Next.js dynamic import 활용 ('frontend/architecture.md'의 '12.1 코드 분할 및 지연 로딩' 예시 참조)
   - 로딩 상태 처리 (Skeleton UI 구현)
   - 'ui-design/ui-requirements.md'의 PR-006 목표값 (초기 JS 번들 크기 < 200KB) 달성

2. 이미지 최적화
   - Next.js Image 컴포넌트 활용
   - 이미지 포맷 최적화 (WebP, AVIF)
   - 'ROADMAP.md'의 '4.1.4 성능 최적화' 체크리스트 참조

3. 메모이제이션 최적화
   - React.memo, useMemo, useCallback 일관된 활용
   - 선택적 구독 패턴 ('frontend/state-management.md'의 '6.1 선택적 구독 패턴' 참조)
   - 'frontend/components/core-components.md'의 '4.3 차트 커스텀 렌더링 최적화' 패턴 활용

4. 상태 업데이트 최적화
   - 일괄 처리
   - React 19 최적화 기능 활용
   - 'frontend/components/core-components.md'의 '9.3 React 19의 최적화 기능 활용' 예시 참조

5. 네트워크 최적화
   - 요청 일괄 처리
   - 데이터 프리페칭
   - 점진적 로딩
   - 'ui-design/ui-requirements.md'의 PR-001~003 목표값 달성

6. 성능 측정 및 개선
   - Core Web Vitals 최적화
   - 번들 분석 및 최적화
   - 'ui-design/ui-requirements.md'의 PR-001~007 목표값 달성
```

### 4.2.1 테스트 환경 설정

```
설명: 단위/통합/E2E 테스트 환경을 설정해주세요.

참조:
- 로드맵 문서: 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' 체크리스트
- 아키텍처 문서: 'frontend/architecture.md'의 '2. 기술 스택' 테스트 섹션
- 파일 목록: 'frontend/file-list.md'의 Vitest 및 Playwright 관련 설정

구현 항목:
1. Vitest 설정
   - vitest.config.ts 구성
   - vitest-setup.ts 구현
   - 패키지별 테스트 설정
   - 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' 체크리스트 참조

2. 단위 테스트
   - 차트 컴포넌트 테스트
   - 데이터 변환 유틸리티 테스트
   - 스토어 테스트
   - 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' > 차트 컴포넌트 단위 테스트 구현 등 참조

3. 통합 테스트
   - 페이지 통합 테스트
   - 서버/클라이언트 통합 테스트
   - 데이터 흐름 테스트
   - 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' > 서버/클라이언트 통합 테스트 구현 등 참조

4. E2E 테스트
   - Playwright 설정
   - 핵심 사용자 흐름 테스트
   - 접근성 테스트
   - 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' > E2E 테스트 구현 등 참조

5. API 모킹
   - MSW 설정
   - API 응답 모킹
   - 'ROADMAP.md'의 '4.2.1 테스트 환경 설정' > MSW 설정 및 API 모킹 구현 참조
```

### 4.2.2 CI/CD 파이프라인

```
설명: GitHub Actions와 Vercel을 활용한 CI/CD 파이프라인을 구축해주세요.

참조:
- 로드맵 문서: 'ROADMAP.md'의 '4.2.2 CI/CD 파이프라인' 체크리스트
- README.md의 '브랜치 전략' 섹션

구현 항목:
1. GitHub Actions 워크플로우
   - CI 워크플로우 (.github/workflows/ci.yml)
   - CD 워크플로우 (.github/workflows/cd.yml)
   - PR 미리보기 워크플로우 (.github/workflows/preview.yml)
   - 'ROADMAP.md'의 '4.2.2 CI/CD 파이프라인' 체크리스트 참조

2. 빌드 최적화
   - 의존성 설치 및 캐싱
   - 빌드 시간 최적화
   - 병렬 작업 설정
   - 'ROADMAP.md'의 '4.2.2 CI/CD 파이프라인' > 의존성 설치 및 캐싱 설정 등 참조

3. 테스트 자동화
   - 린트 및 타입 체크
   - 단위/통합 테스트 실행
   - 코드 커버리지 보고서
   - 'ROADMAP.md'의 '4.2.2 CI/CD 파이프라인' > 테스트 자동화 설정 등 참조

4. 배포 설정
   - 환경별 배포 설정 (개발, 스테이징, 프로덕션)
   - 롤백 메커니즘
   - 배포 알림
   - README.md의 '브랜치 전략' 섹션 활용

5. Vercel 설정
   - vercel.json 구성
   - 환경 변수 관리
   - 다중 환경 설정
   - 'ROADMAP.md'의 '4.2.2 CI/CD 파이프라인' > Vercel 설정 파일 구성 등 참조
```

### 4.2.3 사용자 가이드 문서

```
설명: 사용자를 위한 가이드 문서를 작성해주세요.

참조:
- 로드맵 문서: 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' 체크리스트
- 기획서: 'product-spec.md' 전체 참조
- 사용자 흐름: 'ui-design/user-flows.md'의 '3. 핵심 사용자 흐름' 섹션

구현 항목:
1. 시작하기 가이드
   - 서비스 개요 및 목적 ('product-spec.md'의 '1. 서비스 개요' 참조)
   - 계정 생성 및 로그인 가이드 ('ui-design/user-flows.md'의 '3.1 온보딩 및 로그인 흐름' 참조)
   - 사용자 인터페이스 소개
   - 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' 체크리스트 참조

2. 대시보드 가이드
   - 대시보드 생성 방법 ('ui-design/user-flows.md'의 '3.2 대시보드 관리 흐름' 참조)
   - 위젯 추가 및 관리
   - 레이아웃 조정
   - 대시보드 공유 ('ui-design/user-flows.md'의 '3.6 대시보드 공유 및 구독 흐름' 참조)
   - 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' > 대시보드 가이드 작성 등 참조

3. 차트 사용법
   - 차트 유형 선택 ('ui-design/user-flows.md'의 '3.3 차트 생성 및 편집 흐름' 참조)
   - 데이터 소스 설정 ('ui-design/user-flows.md'의 '3.4 데이터 소스 조회 및 분석 흐름' 참조)
   - 차트 옵션 설정
   - 차트 내보내기
   - 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' > 차트 사용법 가이드 작성 등 참조

4. 고급 기능 가이드
   - 데이터 변환 사용법 ('ui-design/user-flows.md'의 '3.4 데이터 소스 조회 및 분석 흐름' 참조)
   - 커스텀 시각화
   - API 연동
   - 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' > 고급 기능 가이드 작성 등 참조

5. 문제 해결 가이드
   - 자주 묻는 질문 (FAQ)
   - 일반적인 문제 해결 방법
   - 지원 요청 방법
   - 'ROADMAP.md'의 '4.2.3 사용자 가이드 문서' > 문제 해결 가이드 작성 등 참조
```

이 강화된 프롬프트 가이드는 새로 추가된 문서의 내용을 충분히 반영하여 E-Torch 개발 과정에서 더 구체적인 지침을 제공합니다. 각 단계에서 참조할 수 있는 문서들이 명확하게 표시되어 있으며, UI 요구사항과 사용자 흐름을 바탕으로 한 구현 방향성도 보강되었습니다. 10년차 프론트엔드 개발자로서의 경험을 살릴 수 있도록 각 컴포넌트와 기능의 구현 방식에 대한 구체적인 지침과 참조가 포함되어 있습니다.
