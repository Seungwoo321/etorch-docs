# E-Torch 세부 구현을 위한 개선된 프롬프트 가이드

10년차 프론트엔드 개발자로서 AI와 효과적으로 협업하기 위한 개선된 프롬프트 가이드입니다. 각 프롬프트는 E-Torch 문서를 정확히 반영하며 일관된 코드 생성을 위해 최적화되었습니다.

## 1. 환경 설정 및 기반 구축

### 1.1 모노레포 구조 설정

```
아래 요구사항에 따라 E-Torch 프로젝트의 Turborepo 모노레포 구조를 설정해주세요:

1. 기본 설정:
   - pnpm 워크스페이스 (pnpm-workspace.yaml)
   - Turborepo 구성 (turbo.json - 캐시 전략, 파이프라인 정의)
   - 루트 package.json (공통 스크립트 및 devDependencies)

2. 패키지 구조: architecture.md의 모노레포 패키지 구조 구현
   - apps: web(Next.js 15), docs
   - packages:
     - core: 타입 정의, 상수, 인터페이스
     - charts: 차트 컴포넌트 (Recharts 2.15.3+ 기반)
     - dashboard: 대시보드 그리드 (react-grid-layout 기반)
     - data-sources: 데이터 커넥터 및 변환 로직
     - ui: Shadcn/UI 기반 컴포넌트 및 서버 래퍼
     - utils: 포맷터, 헬퍼 함수
     - state: Zustand 5, Tanstack Query 5 훅
     - server-api: API 경로, 서버 액션

3. 의존성 관리:
   - architecture.md의 의존성 다이어그램 준수 (순환 의존성 방지)
   - 명시적 버전 지정: React 19, Next.js 15, Tailwind 4, TypeScript 5.5+
   - 코드 품질 도구: ESLint 9 (Standard 규칙), Typescript-ESLint

4. 각 패키지 기본 구조:
   - 일관된 내부 구조 (src 폴더, server 폴더 분리)
   - 명확한 진입점 (index.ts, server.ts)
   - public API 타입 정의

5. 빌드 최적화:
   - TypeScript 참조 프로젝트 설정
   - 증분 빌드 전략
   - 개발/빌드/린트/테스트 공통 스크립트

주의: architecture.md 문서의 의존성 구조와 일치해야 하며, ESLint 9의 플랫 구성 파일을 사용하세요. 완성된 구조는 확장 가능하고 일관된 개발 환경을 제공해야 합니다.
```

### 1.2 디자인 시스템 구현

```
design-system.md 문서를 참조하여 E-Torch의 디자인 시스템을 구현해주세요:

1. Tailwind CSS 4 구성:
   - globals.css에 OKLCH 색상 공간 기반 테마 변수 정의
   - 브랜드 색상: Primary (#0c1e3e), Secondary (#1a56db), Tertiary (#0284c7)
   - 차트 색상 팔레트 확장 (차트 유형별 일관된 색상)
   - Inter(UI) 및 JetBrains Mono(데이터) 폰트 설정
   - CSS 변수를 활용한 테마 시스템 (@theme inline 블록)

2. 컴포넌트 구현 (packages/ui):
   - 서버/클라이언트 컴포넌트 분리 구조 (src/components와 src/server-components)
   - 모든 컴포넌트에 일관된 Props 인터페이스 적용 (클래스명 확장 가능)
   - design-system.md의 타입 스케일 및 컴포넌트 패턴 준수
   - 클라이언트 컴포넌트에 'use client' 지시어 명시

3. 핵심 UI 컴포넌트:
   - Button (변형: default, destructive, outline, secondary, ghost, link)
   - Card, CardHeader, CardTitle, CardContent, CardFooter
   - Form 컴포넌트 (Input, Select, Checkbox, Switch)
   - Dialog, Dropdown, Tabs 등 상호작용 컴포넌트
   - 모든 컴포넌트에 대한 서버 래퍼 구현 (ButtonServer 등)

4. 접근성 컴포넌트:
   - SkipLink: 키보드 사용자를 위한 콘텐츠 바로가기
   - VisuallyHidden: 시각적으로 숨겨진 스크린리더 텍스트
   - WCAG 2.1 AA 준수를 위한 aria 속성 및 키보드 네비게이션

5. 테마 관리:
   - ThemeProvider: next-themes 기반 다크모드 지원
   - prefers-color-scheme 미디어 쿼리 및 CSS 변수 활용
   - 테마 전환 애니메이션 (@import "tw-animate-css" 활용)

핵심 요구사항: 서버/클라이언트 컴포넌트 분리 패턴을 준수하고, 모든 컴포넌트는 forwardRef를 사용하여 ref를 전달해야 합니다. 접근성을 최우선으로 고려하세요.
```

### 1.3 상태 관리 기본 구조

```
state-management.md 문서에 정의된 다중 계층 상태 아키텍처를 구현해주세요:

1. Zustand 스토어 설계 (packages/state/src/stores):
   - 정규화된 엔티티 상태 구조 구현 (byId/allIds 패턴)
   - 계층화된 스토어 구성:
     - app-store.ts: 전역 앱 상태 (테마, 알림, 에러)
     - dashboard-store.ts: 대시보드 CRUD 및 레이아웃 
     - chart-editor-store.ts: 차트 에디터 상태 및 Undo/Redo
   - 관심사 분리: 스토어는 단일 책임 원칙 준수
   - 미들웨어 조합: immer, persist, devtools, subscribeWithSelector

2. Tanstack Query 5 설정 (packages/state/src/query):
   - 계층적 쿼리 키 구조 (queryKeys 객체)
   - 데이터 유형별 캐싱 전략 (staleTime, gcTime 구성)
   - 지표 유형별 최적화된 refetchInterval 설정
   - hydration 및 initialData 패턴 구현

3. 사용자 정의 훅 (packages/state/src/hooks):
   - 데이터 관련 훅:
     - useDashboard, useChartData, useIndicators
     - 서버 컴포넌트에서 페치한 데이터 통합
   - 상태 제어 훅:
     - useTimeRangeSync: 차트 간 시간 범위 동기화
     - useActionMutation: 서버 액션 + 낙관적 업데이트
   - 상태 선택 최적화 훅 (선택적 구독 패턴)

4. 고급 상태 패턴:
   - Command 패턴 기반 Undo/Redo 시스템 구현
   - 이벤트 버스 패턴 (구독/발행 메커니즘)
   - 트랜잭션 지원 (복합 작업 원자성)
   - 선택적 상태 구독으로 리렌더링 최적화

5. 서버 상태 동기화:
   - 서버 컴포넌트에서 페치한 데이터를 클라이언트 스토어에 주입
   - 동적 라우팅 파라미터와 상태 연동
   - Next.js 캐시와 React Query 캐시 조화

구현 요구사항: 모든 스토어와 훅은 TypeScript 타입 안전성을 보장하고, 상태 업데이트 로직은 예측 가능하고 테스트 가능해야 합니다. 스토어 간 의존성은 최소화하세요.
```

### 1.4 라우팅 구조 설정

```
routing.md 문서를 기반으로 Next.js 15 App Router 라우팅 구조를 구현해주세요:

1. 라우트 그룹 구조 (app 디렉토리):
   - (auth): 인증 관련 경로
     - login/page.tsx: SNS 로그인 화면
     - callback/page.tsx: OAuth 콜백 처리
     - layout.tsx: 인증 레이아웃 (중앙 정렬 카드)
   - (dashboard): 대시보드 관련 경로
     - dashboard/page.tsx: 대시보드 목록
     - dashboard/[id]/page.tsx: 대시보드 상세
     - dashboard/new/page.tsx: 신규 생성
     - dashboard/[id]/edit/page.tsx: 편집
     - explore/page.tsx: 공유 대시보드 탐색
     - layout.tsx: 사이드바, 헤더 포함 레이아웃
   - (chart): 차트 관련 경로
     - chart-editor/[id]/page.tsx: 차트 에디터
     - chart/[id]/page.tsx: 차트 상세
   - (profile): 프로필 관련 경로
     - profile/settings/page.tsx: 사용자 설정
     - profile/subscription/page.tsx: 구독 관리
     - profile/notifications/page.tsx: 알림 설정

2. 레이아웃 컴포넌트:
   - RootLayout (app/layout.tsx):
     - 전역 메타데이터, 프로바이더 설정
     - 폰트 설정 (Inter, JetBrains_Mono)
     - 테마 컨텍스트 및 쿼리 클라이언트 제공
   - 그룹별 레이아웃: 각 역할에 맞는 UI 구조 제공
     - 모든 레이아웃은 일관된 디자인 시스템 적용

3. 접근 제어 및 보호 전략:
   - middleware.ts: 인증 기반 라우트 보호
     - 공개 경로와 보호된 경로 구분
     - JWT 토큰 유효성 검증
     - 인증되지 않은 접근 리디렉션
   - 서버 컴포넌트 인증 검증
   - 조건부 UI 렌더링 (클라이언트 측)

4. 페이지 메타데이터:
   - 정적 및 동적 메타데이터 설정
   - SEO 최적화 (OG 태그, 제목, 설명)
   - 동적 메타데이터 함수 구현

5. 네비게이션 최적화:
   - Link 컴포넌트 prefetch 활용
   - 인터셉트 라우트 (@modal 그룹)
   - 병렬 라우트 적용 (@탭/슬롯 구조)

구현 요구사항: routing.md의 계층적 라우팅 구조를 정확히 반영하되, 각 페이지 컴포넌트는 서버 컴포넌트로 구현하고 클라이언트 상호작용 부분만 클라이언트 컴포넌트로 분리하세요. 모든 동적 경로에는 generateMetadata 함수를 구현하세요.
```

## 2. 인증 시스템

### 2.1 Supabase 인증 연동

```
다음 요구사항에 따라 E-Torch의 Supabase 기반 인증 시스템을 구현해주세요:

1. Supabase 클라이언트 설정:
   - packages/server-api/supabase/client.ts: 클라이언트 사이드 인스턴스
     - 싱글톤 패턴 및 타입 안전성 보장
     - 환경 변수 기반 구성 (process.env.NEXT_PUBLIC_SUPABASE_URL 등)
   - packages/server-api/supabase/server.ts: 서버 사이드 인스턴스
     - createServerClient 유틸리티 (쿠키 기반)
     - 서버 컴포넌트에서 안전한 사용 패턴

2. SNS OAuth 인증 흐름:
   - packages/ui/src/components/auth/SNSLoginButtons.tsx
     - Google, Naver, Kakao 로그인 버튼
     - 리다이렉션 URL 및 브랜딩 준수
   - OAuth 콜백 처리:
     - app/(auth)/callback/page.tsx
     - 토큰 및 세션 설정
     - 성공/실패 처리 및 리다이렉션

3. 인증 컨텍스트 및 상태 관리:
   - packages/state/src/auth/auth-store.ts
     - Zustand 스토어로 인증 상태 관리
     - 사용자 정보, 토큰, 권한 상태
   - packages/state/src/hooks/useAuth.ts
     - 인증 상태 및 함수 제공
     - 로그인, 로그아웃, 세션 확인

4. 인증 보호 컴포넌트:
   - packages/ui/src/components/auth/AuthGuard.tsx
     - 인증이 필요한 페이지 보호
     - 로딩/인증 상태 처리
   - packages/ui/src/components/auth/RoleGuard.tsx
     - 역할 기반 접근 제어

5. 미들웨어 인증 통합:
   - middleware.ts
     - 쿠키 기반 세션 검증
     - 공개/보호 경로 관리
     - 인증 리다이렉션 처리

6. 토큰 관리:
   - 자동 토큰 리프레시
   - 안전한 토큰 저장 (httpOnly 쿠키)
   - 세션 지속성 관리

구현 요구사항: data-flow.md 문서의 '인증 및 접근 제어' 섹션에 설명된 흐름을 준수하고, 모든 클라이언트-서버 통신에 인증 토큰이 적용되어야 합니다. JWT 기반 인증을 사용하고, 토큰 만료 및 리프레시 로직을 포함하세요.
```

### 2.2 인증 및 접근 제어

```
E-Torch의 인증 기반 접근 제어 시스템을 다음 요구사항에 따라 구현해주세요:

1. 다층적 접근 제어 전략:
   - 미들웨어 레벨:
     - middleware.ts: 라우트 경로 보호
     - 공개 경로 배열 정의 (publicRoutes)
     - JWT 토큰 유효성 검증 및 파싱
     - 인증 실패 시 /login 리다이렉션 (returnTo 쿼리 파라미터 포함)
   
   - 서버 컴포넌트 레벨:
     - requireAuth.ts: 서버 컴포넌트에서 인증 검증
     - notFound() 또는 redirect() 활용
     - 데이터 접근 권한 검증
   
   - 클라이언트 컴포넌트 레벨:
     - AuthGuard.tsx: 조건부 컴포넌트 렌더링
     - 로딩 상태, 인증 오류 처리
     - 낙관적 UI 업데이트와 결합

2. Supabase Row Level Security (RLS) 정책:
   - 대시보드 소유권 정책:
     - 소유자만 수정 가능
     - 공개된 대시보드는 모든 사용자가 열람 가능
   - 구독 기반 접근 제어:
     - 유료 기능 접근 제한
     - 구독 상태에 따른 데이터 접근 제한

3. 사용자 정의 인증 훅:
   - useRequireAuth(): 인증 필수 페이지용
     - 로딩 상태 처리
     - 리다이렉션 로직 포함
   - useOptionalAuth(): 인증 선택적 페이지용
     - 인증 여부에 따른 UI 분기
   - useHasPermission(): 권한 기반 UI 렌더링

4. 리소스 접근 제어:
   - 대시보드 소유권 확인:
     - 편집, 삭제 권한 검증
     - 공유 설정 권한 확인
   - 위젯 수정 권한:
     - 대시보드 내 개별 위젯 권한
   - 구독 콘텐츠 접근:
     - 유료 기능 접근 검증

5. CSRF 보호 및 보안 헤더:
   - 상태 기반 CSRF 토큰
   - Content-Security-Policy 설정
   - X-Frame-Options, X-Content-Type-Options 헤더

구현 요구사항: data-flow.md의 인증 흐름을 정확히 구현하고, UI 상태와 서버 상태의 일관성을 유지해야 합니다. 권한 오류는 사용자 친화적 메시지로 처리하며, 모든 접근 제어 로직은 프론트엔드와 백엔드 모두에서 검증되어야 합니다.
```

## 3. 데이터 소스 관리

### 3.1 API 클라이언트 구현

```
data-flow.md 문서를 기반으로 E-Torch API 클라이언트를 구현해주세요:

1. 기본 HTTP 클라이언트:
   - packages/data-sources/src/client/base-client.ts
     - fetch API 기반 유틸리티 래퍼 (원시 해결사)
     - TypeScript 제네릭을 활용한 타입 안전성 보장
     - 기본 헤더 및 옵션 설정
     - 자동 HTTP 오류 처리 및 JSON 변환

2. 인터셉터 시스템:
   - packages/data-sources/src/client/interceptors.ts
     - 요청 인터셉터 (헤더 추가, 인증 토큰 주입)
     - 응답 인터셉터 (응답 변환, 오류 처리)
     - 플러그인 형태의 확장 가능한 구조
     - 컴포저블 인터셉터 체인

3. 에러 처리 시스템:
   - packages/data-sources/src/client/errors.ts
     - 계층화된 HTTP 에러 클래스
     - 401 인증 오류 특수 처리 (토큰 갱신)
     - 네트워크 오류 자동 재시도 로직
     - 사용자 친화적 오류 메시지 변환

4. 도메인별 API 클라이언트:
   - packages/data-sources/src/api/economic-data.ts
     - 경제지표 API (KOSIS, ECOS, OECD)
     - 지표 검색 및 데이터 조회 함수
   - packages/data-sources/src/api/dashboard.ts
     - 대시보드 CRUD 작업
   - packages/data-sources/src/api/chart.ts
     - 차트 관련 API
   - packages/data-sources/src/api/user.ts
     - 사용자 프로필 및 설정 API

5. 캐싱 및 최적화 전략:
   - packages/data-sources/src/client/caching.ts
     - 메모리 캐시 레이어
     - 지표 유형별 캐싱 전략
     - 요청 중복 방지 (request deduplication)

6. 배치 요청 처리:
   - packages/data-sources/src/client/batch.ts
     - 여러 요청을 단일 HTTP 요청으로 통합
     - 병렬 요청 최적화
     - 응답 매핑 및 오류 처리

7. Supabase 통합:
   - packages/data-sources/src/client/supabase-client.ts
     - Supabase 클라이언트 래핑
     - 인증 정보 활용
     - 타입 안전성 보장

구현 요구사항: 모든 API 클라이언트는 명확한 인터페이스와 상세한 타입 정의를 포함해야 합니다. 재시도 전략, 타임아웃 설정, 오류 처리 로직이 구현되어야 하며, 모든 네트워크 요청은 인증 상태와 연동되어야 합니다. data-flow.md의 API 클라이언트 아키텍처 섹션을 충실히 따르세요.
```

### 3.2 데이터 변환 파이프라인

```
data-flow.md 문서의 데이터 변환 및 처리 파이프라인 섹션을 참조하여 다음 기능을 구현해주세요:

1. 핵심 데이터 모델 및 타입 정의:
   - packages/core/src/types/data-models.ts
     - NormalizedTimeSeriesData 인터페이스 구현
     - 변환 유형 열거형 (TransformType)
     - 주기 타입 (Period: 'D'|'M'|'Q'|'A')
     - 메타데이터 타입 정의

2. 데이터 변환 함수 모듈:
   - packages/data-sources/src/transforms/index.ts
     - transformRawData: 원시 API 응답 정규화
     - calculateChange: 기간 대비 변화율 (MoM, QoQ, YoY)
     - calculateYoYChange: 전년 동기 대비 변화율
     - calculateCumulative: 누적값 계산
     - noTransform: 원본 데이터 유지

3. 결측치 처리 알고리즘:
   - packages/data-sources/src/transforms/missing-values.ts
     - handleMissingValues 함수 구현
     - 보간 방법: 선형, 직전값, 다음값, 0값, 무시
     - 결측 패턴 감지 및 최적 처리

4. 기간 동기화 전략:
   - packages/data-sources/src/transforms/synchronization.ts
     - synchronizeTimeSeries 함수 구현
     - 업샘플링: 낮은 주기→높은 주기 (연간→분기→월간→일간)
     - 다운샘플링: 높은 주기→낮은 주기 (일간→월간→분기→연간)
     - 집계 방법: 평균, 합계, 첫값, 마지막값, 최대, 최소

5. 데이터 다운샘플링 알고리즘:
   - packages/data-sources/src/transforms/downsampling.ts
     - LTTB 알고리즘 구현 (시각적 특성 보존)
     - M4 알고리즘 구현 (통계적 특성 보존)
     - 균등 간격 리샘플링

6. 통합 변환 파이프라인:
   - packages/data-sources/src/transforms/pipeline.ts
     - 다단계 파이프라인 구조 (추출→정규화→필터링→변환→집계)
     - 함수형 프로그래밍 접근법 (compose, pipe)
     - 중간 결과 캐싱 지원

7. 변환 유틸리티 훅:
   - packages/data-sources/src/hooks/useDataTransform.ts
     - 실시간 데이터 변환 지원
     - 변환 옵션 상태 관리
     - 메모이제이션 최적화

구현 요구사항: 모든 변환 함수는 순수 함수로 구현하고, 원본 데이터 불변성을 유지해야 합니다. 대용량 데이터 처리 성능을 고려하여 최적화된 알고리즘을 사용하세요. data-flow.md에 정의된 정규화된 데이터 구조를 정확히 준수해야 합니다.
```

### 3.3 데이터 캐싱 전략 구현

```
state-management.md와 data-flow.md 문서를 참조하여 E-Torch의 다층적 데이터 캐싱 전략을 구현해주세요:

1. Tanstack Query 캐싱 설정:
   - packages/state/src/query/query-client.ts
     - QueryClient 설정 및 기본 옵션
     - 경제지표 데이터에 최적화된 staleTime, gcTime 설정
     - 전략적 리페치 설정
     - 오류 재시도 로직

2. 계층적 쿼리 키 구조:
   - packages/state/src/query/query-keys.ts
     - 일관된 쿼리 키 네이밍 및 구조
     - 계층적 쿼리 키 함수
     - 키 유형별 무효화 전략

3. 지표 유형별 캐싱 전략:
   - packages/state/src/query/caching-strategies.ts
     - 실시간 금융 지표: 짧은 staleTime (1분)
     - 일일 경제지표: 중간 staleTime (30분)
     - 월간/분기별/연간 지표: 긴 staleTime (3시간+)
     - 유형별 refetchInterval 설정

4. 캐시 무효화 패턴:
   - packages/state/src/query/invalidation.ts
     - 이벤트 기반 무효화 (대시보드 저장 후 등)
     - 부분/선택적 무효화 함수
     - 쿼리 키 기반 무효화 유틸리티

5. 최적화된 쿼리 훅:
   - packages/state/src/hooks/useQueryWithCache.ts
     - 캐싱 전략이 통합된 쿼리 훅
     - initialData 활용 최적화
     - 서버 컴포넌트 데이터 활용

6. 영구 캐싱 전략:
   - packages/state/src/storage/persistence.ts
     - localStorage/sessionStorage 활용
     - TTL 기반 캐시 관리
     - 스토리지 용량 제한 고려

7. 가시성 기반 리페치:
   - packages/state/src/hooks/useVisibilityRefetch.ts
     - IntersectionObserver 활용
     - 화면에 보이는 컴포넌트 우선 리페치
     - 리소스 최적화

8. 서버 컴포넌트 캐싱 연동:
   - packages/state/src/server/cache-integration.ts
     - Next.js 캐시와 Tanstack Query 통합
     - revalidatePath/revalidateTag 활용
     - 동적 vs 정적 렌더링 최적화

구현 요구사항: 모든 캐싱 전략은 경제지표 데이터의 특성(업데이트 주기, 중요도)에 맞게 최적화되어야 합니다. 클라이언트와 서버 캐시 간의 일관성을 유지하고, 메모리 사용량과 네트워크 요청을 최소화하는 전략을 구현하세요. data-flow.md의 캐싱 계층 구조를 충실히 반영해야 합니다.
```

## 4. 차트 컴포넌트

### 4.1 기본 차트 컴포넌트

```
core-components.md 문서를 참조하여 E-Torch의 기본 차트 컴포넌트를 구현해주세요:

1. 차트 컴포넌트 계층 구조:
   - packages/charts/src/server/ChartServerWrapper.tsx (서버 컴포넌트)
     - 데이터 페칭 및 초기 상태 준비
     - SSR-친화적 래퍼
   - packages/charts/src/components/ChartComponent.tsx (클라이언트 컴포넌트)
     - 차트 상태 관리, 이벤트 핸들링
     - 서버 데이터 하이드레이션
   - packages/charts/src/components/ChartRenderer.tsx
     - 차트 타입에 따른 렌더링 분기

2. 시계열 차트:
   - packages/charts/src/components/chart-types/TimeSeriesChart.tsx
     - Recharts 기반 구현
     - 다중 시리즈 지원
     - 스타일 변형: 선, 영역, 막대
     - 다중 Y축 지원
     - 확대/축소 기능
     - 레이지 로딩 최적화

3. 막대 차트:
   - packages/charts/src/components/chart-types/BarChart.tsx
     - 수직/수평 지원
     - 그룹화/스택 옵션
     - 정렬 기능
     - 반응형 레이아웃

4. 산점도 차트:
   - packages/charts/src/components/chart-types/ScatterChart.tsx
     - 상관관계 시각화
     - 포인트 크기/스타일 커스터마이징
     - 회귀선 옵션
     - 줌/패닝 지원

5. 추가 차트 유형:
   - RadarChart.tsx: 다차원 데이터 비교
   - RadialBarChart.tsx: 원형 진행 표시
   - PieChart.tsx: 비율 시각화

6. 공통 컨트롤 및 지원 컴포넌트:
   - packages/charts/src/components/controls/ChartControls.tsx
     - 차트 상호작용 UI
     - 데이터 다운로드 버튼
     - 전체화면 전환
   - packages/charts/src/components/ChartSkeleton.tsx
     - 로딩 상태 UI
   - packages/charts/src/components/ChartError.tsx
     - 에러 표시 및 재시도 옵션

7. 접근성 향상 컴포넌트:
   - packages/charts/src/components/a11y/ChartDataTable.tsx
     - 스크린 리더 접근성 보장
     - 차트 데이터의 테이블 표현

구현 요구사항: 모든 차트는 core-components.md의 차트 컴포넌트 계층 구조를 충실히 따라야 하며, ui-requirements.md의 상세 기능 명세를 준수해야 합니다. 서버 컴포넌트와 클라이언트 컴포넌트 분리 패턴을 명확히 구현하고, 초기 데이터 로딩 최적화를 고려하세요. 모든 차트는 WCAG 2.1 AA 접근성 표준을 준수해야 합니다.
```

### 4.2 차트 옵션 패널

```
ui-requirements.md의 3.1-3.2 섹션을 참조하여 E-Torch의 차트 옵션 패널을 구현해주세요:

1. 공통 옵션 컴포넌트:
   - packages/charts/src/components/options/PanelOptions.tsx
     - PO-001: 차트 제목 설정
     - PO-002: 차트 설명 설정
     - PO-003: 배경 투명도 설정
   - packages/charts/src/components/options/TooltipOptions.tsx
     - TO-001~005: 툴팁 표시 방식, 최대 너비, 커서 스타일 등
   - packages/charts/src/components/options/LegendOptions.tsx
     - LG-001~004: 범례 표시 여부, 레이아웃, 정렬 방향
   - packages/charts/src/components/options/XAxisOptions.tsx
     - XA-001~010: X축 표시, 데이터 키, 유형, 범위, 눈금 등
   - packages/charts/src/components/options/YAxisOptions.tsx
     - YA-001~011: Y축 표시, 단위, 유형, 범위, 눈금 등
   - packages/charts/src/components/options/YAxisSecondaryOptions.tsx
     - YAS-001~011: 보조 Y축 설정 (다중 축 차트용)

2. 차트 유형별 전용 옵션:
   - packages/charts/src/components/options/GraphStyles.tsx
     - GS-001~003: 시계열 차트 스타일 (선, 영역, 막대)
   - packages/charts/src/components/options/ScatterChartOptions.tsx
     - SC-001~006: 산점도 포인트 크기, 모양, 회귀선 등
   - packages/charts/src/components/options/RadarChartOptions.tsx
     - RC-001~006: 레이더 차트 그리드, 축, 영역 채우기 등
   - packages/charts/src/components/options/RadialBarOptions.tsx
     - RB-001~010: 각도, 반지름, 배경 설정 등
   - packages/charts/src/components/options/TextComponentOptions.tsx
     - TX-001~014: 텍스트 위젯 콘텐츠, 데이터 기반 설정 등

3. 옵션 패널 통합 컴포넌트:
   - packages/charts/src/components/OptionsPanel.tsx
     - 차트 유형별 관련 옵션 표시 로직
     - 아코디언/탭 형태의 옵션 그룹화
     - 컨텍스트 기반 조건부 렌더링

4. 옵션 적용 및 상태 관리:
   - packages/charts/src/hooks/useChartOptions.ts
     - 옵션 상태 관리 (Zustand 스토어 연결)
     - 옵션 변경 시 차트 실시간 업데이트
     - 기본값 및 유효성 검사

5. 옵션 관계 및 의존성 관리:
   - packages/charts/src/utils/option-dependencies.ts
     - 옵션 간 의존 관계 처리
     - 조건부 활성화/비활성화 로직
     - 옵션 충돌 해결

6. 사용자 친화적 폼 요소:
   - 색상 선택기 (컬러 피커)
   - 슬라이더 (값 범위 선택)
   - 드롭다운 (옵션 선택)
   - 토글 스위치 (불리언 옵션)
   - 숫자 입력 (범위 제한 포함)

구현 요구사항: ui-requirements.md에 정의된 모든 옵션 ID(예: PO-001, TO-001 등)에 대한 정확한 구현을 보장해야 합니다. 각 컴포넌트는 모듈화되고 재사용 가능해야 하며, 관련 차트 유형에만 표시되어야 합니다. 모든 옵션은 즉시 미리보기에 반영되어야 하며, 성능 최적화(디바운싱, 스로틀링)가 적용되어야 합니다.
```

### 4.3 차트 에디터

```
core-components.md의 4.2 섹션을 참조하여 E-Torch의 차트 에디터를 구현해주세요:

1. 차트 에디터 레이아웃:
   - packages/charts/src/editor/ChartEditor.tsx (메인 컴포넌트)
     - 리사이즈 가능한 3-영역 레이아웃:
       - 상단: 차트 미리보기 및 기본 설정
       - 우측: 옵션 패널
       - 하단: 데이터 소스 패널
     - Shadcn/UI Resizable 컴포넌트 활용
     - 응답형 레이아웃 지원

2. 차트 유형 선택:
   - packages/charts/src/editor/ChartTypeSelector.tsx
     - ui-requirements.md의 CM-001 구현
     - 시각적 차트 타입 갤러리
     - 차트 유형 간 변환 시 설정 보존
     - 유형별 설명 및 사용 가이드

3. 차트 미리보기:
   - packages/charts/src/editor/ChartPreview.tsx
     - 실시간 렌더링
     - 크기 조절 컨트롤
     - 인터랙티브 데모 모드
     - 접근성 검사 도구

4. 데이터 소스 패널:
   - packages/charts/src/editor/DataSourcePanel.tsx
     - 시리즈 관리 UI (최대 5개 시리즈 - ui-requirements.md AS-001)
     - packages/charts/src/editor/DataQueryCard.tsx
       - 개별 데이터 쿼리 설정
       - 데이터 소스 선택 (KOSIS, ECOS, OECD)
       - 지표 검색 및 선택
       - 데이터 변환 설정

5. 에디터 컨트롤:
   - packages/charts/src/editor/EditorControls.tsx
     - 저장/취소 버튼
     - 실행 취소/다시 실행 (state-management.md의 Undo/Redo 활용)
     - 미리보기 모드 전환

6. 에디터 상태 관리:
   - state-management.md 문서의 차트 에디터 스토어 활용
     - 편집 이력 관리
     - 임시 저장
     - 차트 메타데이터 관리

7. 서버 액션 통합:
   - packages/charts/src/server-actions/save-chart.ts
     - 차트 설정 저장
     - 저장 후 리다이렉션
     - 오류 처리 및 사용자 피드백

구현 요구사항: 차트 에디터는 improved-chart-editor-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 2.2 차트 관리 기능 명세를 준수해야 합니다. 모든 편집 기능은 실시간으로 미리보기에 반영되어야 하며, 복잡한 상태 변경은 state-management.md의 커맨드 패턴을 활용해 Undo/Redo를 지원해야 합니다.
```

### 4.4 차트 데이터 최적화

```
data-flow.md의 데이터 최적화 섹션을 참조하여 다음 차트 데이터 최적화 기능을 구현해주세요:

1. LTTB 다운샘플링 알고리즘:
   - packages/data-sources/src/optimization/lttb.ts
     - Largest-Triangle-Three-Buckets 알고리즘 구현
     - 시각적 특성 보존 최적화
     - 화면 해상도에 맞춘 포인트 수 계산
     - 성능 최적화 (O(n) 시간 복잡도)

2. M4 다운샘플링 알고리즘:
   - packages/data-sources/src/optimization/m4.ts
     - Min, Max, First, Last 포인트 보존
     - 구간별 특징점 유지
     - 변동성 높은 데이터에 최적화
     - 이상값 보존 기능

3. 리샘플링 알고리즘:
   - packages/data-sources/src/optimization/resampling.ts
     - 균등 간격 포인트 생성
     - 선형/스플라인 보간법
     - 시간 기반 리샘플링 (일별→주별→월별)

4. 동적 해상도 관리:
   - packages/charts/src/hooks/useDynamicResolution.ts
     - 컴포넌트 크기에 따른 해상도 조정
     - 화면 크기 변경에 반응
     - 데이터 양에 따른 자동 최적화

5. 점진적 로딩 전략:
   - packages/charts/src/components/ProgressiveChart.tsx
     - 저해상도 데이터 먼저 표시
     - 고해상도 데이터 비동기 로드
     - 사용자 인터랙션에 따른 세부 데이터 로드

6. 메모이제이션 최적화:
   - packages/charts/src/utils/memoization.ts
     - 계산 집약적 차트 처리 캐싱
     - 의존성 추적 및 선택적 리렌더링
     - 깊은 비교 최적화

7. 렌더링 성능 개선:
   - packages/charts/src/utils/rendering-optimization.ts
     - 오프스크린 렌더링 최적화
     - 레이어 캐싱
     - 애니메이션 스로틀링

8. 통합 최적화 전략:
   - packages/charts/src/hooks/useOptimizedData.ts
     - 데이터셋 크기 기반 알고리즘 선택
     - 인터랙션 상태에 따른 최적화 수준 조정
     - 메트릭 수집 및 분석

구현 요구사항: 모든 최적화 알고리즘은 데이터의 통계적/시각적 특성을 보존하면서 렌더링 성능을 향상시켜야 합니다. 최적화는 점진적으로 적용되어 사용자가 인지하지 못하게 해야 하며, 대용량 시계열 데이터(10,000+ 포인트)에서도 60fps 이상의 렌더링 성능을 목표로 해야 합니다. 알고리즘 구현은 데이터의 무결성을 훼손하지 않아야 합니다.
```

## 5. 대시보드 시스템

### 5.1 대시보드 목록 페이지

```
dashboard-wireframe.svg와 core-components.md를 참조하여 E-Torch의 대시보드 목록 페이지를 구현해주세요:

1. 페이지 컴포넌트 구조:
   - apps/web/app/(dashboard)/dashboard/page.tsx (서버 컴포넌트)
     - 대시보드 목록 초기 데이터 페칭
     - 메타데이터 설정
     - 클라이언트 컴포넌트 래핑
   - packages/dashboard/src/components/DashboardList.tsx (클라이언트 컴포넌트)
     - 목록 UI 및 상호작용 로직
     - 필터링, 정렬, 검색 통합
     - 무한 스크롤 또는 페이지네이션

2. 대시보드 카드 컴포넌트:
   - packages/dashboard/src/components/DashboardCard.tsx
     - ui-requirements.md의 DL-004 (대시보드 미리보기) 구현
     - 타이틀, 설명, 최종 업데이트 일자 표시
     - 마우스 오버 효과 및 컨텍스트 메뉴
     - 자동 생성 썸네일 이미지

3. 검색 및 필터링:
   - packages/dashboard/src/components/DashboardSearch.tsx
     - ui-requirements.md의 DL-002 (필터링) 구현
     - 실시간 검색 기능
     - 검색어 하이라이팅
     - 고급 필터 옵션 (태그, 날짜 범위 등)

4. 정렬 옵션:
   - packages/dashboard/src/components/DashboardSort.tsx
     - ui-requirements.md의 DL-003 (정렬) 구현
     - 최신 수정순, 생성순, 이름순 정렬
     - 정렬 방향 전환 (오름차순/내림차순)
     - 정렬 상태 지속성

5. 새 대시보드 생성 버튼:
   - packages/dashboard/src/components/NewDashboardButton.tsx
     - ui-requirements.md의 DL-005 구현
     - 플로팅 액션 버튼 또는 헤더 버튼
     - 클릭 시 새 대시보드 페이지로 라우팅
     - 키보드 단축키 지원 (N)

6. 데이터 페칭 및 상태 관리:
   - packages/dashboard/src/server/fetchDashboards.ts
     - 페이지네이션 지원 (limit, offset)
     - 필터링 및 정렬 파라미터
     - 성능 최적화 (필요한 필드만 선택)
   - packages/state/src/hooks/useDashboards.ts
     - TanStack Query 활용
     - 검색/필터/정렬 상태 관리
     - 낙관적 UI 업데이트

7. 빈 상태 및 로딩 처리:
   - packages/dashboard/src/components/DashboardEmptyState.tsx
     - 대시보드가 없을 때 표시할 UI
     - 첫 대시보드 생성 가이드
   - packages/dashboard/src/components/DashboardSkeleton.tsx
     - 로딩 중 스켈레톤 UI
     - 점진적 로딩 효과

구현 요구사항: dashboard-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 2.1.1 대시보드 목록 및 탐색 기능을 모두 구현해야 합니다. 서버 컴포넌트와 클라이언트 컴포넌트를 적절히 분리하여 초기 로딩 성능을 최적화하고, 페이지네이션 또는 무한 스크롤을 통해 대량의 대시보드를 효율적으로 표시해야 합니다.
```

### 5.2 대시보드 상세 페이지

```
dashboard-view-wireframe.svg와 core-components.md를 참조하여 E-Torch의 대시보드 상세 페이지를 구현해주세요:

1. 페이지 컴포넌트 구조:
   - apps/web/app/(dashboard)/dashboard/[id]/page.tsx (서버 컴포넌트)
     - 특정 대시보드 ID 기반 초기 데이터 페칭
     - 동적 메타데이터 생성 (generateMetadata)
     - 존재하지 않는 대시보드 처리 (notFound)
   - packages/dashboard/src/server/DashboardServerWrapper.tsx
     - 서버 컴포넌트에서 데이터 준비
     - 권한 검증 및 접근 제어
   - packages/dashboard/src/components/DashboardDetail.tsx (클라이언트 컴포넌트)
     - 대시보드 렌더링 및 상호작용 로직

2. 대시보드 헤더:
   - packages/dashboard/src/components/DashboardHeader.tsx
     - 제목 및 설명 표시
     - 시간 범위 및 주기 선택 컨트롤
     - 액션 버튼 (편집, 공유, 내보내기)

3. 시간 범위 컨트롤:
   - packages/dashboard/src/components/TimeRangeControl.tsx
     - ui-requirements.md의 DV-001 구현
     - 사전 정의된 범위 (최근 1년, 3년 등)
     - 커스텀 날짜 범위 선택기
     - 전체 대시보드에 적용

4. 주기 선택기:
   - packages/dashboard/src/components/PeriodSelector.tsx
     - ui-requirements.md의 DV-002 구현
     - 일간(D), 월간(M), 분기(Q), 연간(A) 주기 선택
     - 현재 선택된 주기 표시
     - 주기 변경 시 데이터 리로드

5. 대시보드 그리드:
   - 5.3 대시보드 그리드 시스템에서 구현할 컴포넌트 활용
     - 읽기 전용 모드로 위젯 표시
     - 위젯 간 일관된 테마 적용
     - 위젯 상세 정보 표시 (툴팁 등)

6. 액션 컨트롤:
   - packages/dashboard/src/components/DashboardControls.tsx
     - ui-requirements.md의 DV-003~007 구현
     - 새로고침 버튼 및 자동 새로고침 설정
     - 공유 기능
     - 내보내기 옵션 (PNG, PDF)
     - 편집 모드 전환

7. 데이터 페칭 및 상태 관리:
   - packages/dashboard/src/server/fetchDashboardById.ts
     - 대시보드 메타데이터 및 위젯 데이터 로드
     - 관련 차트 데이터 최적화 로드
   - packages/state/src/hooks/useDashboard.ts
     - 대시보드 상태 관리
     - 시간 범위 및 주기 상태
     - 실시간 업데이트 처리

8. 오류 및 비어있는 상태 처리:
   - packages/dashboard/src/components/DashboardError.tsx
     - 대시보드 로드 실패 UI
     - 재시도 옵션
   - packages/dashboard/src/components/EmptyDashboard.tsx
     - 위젯이 없는 대시보드 UI
     - 위젯 추가 가이드

구현 요구사항: dashboard-view-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 2.1.2 대시보드 상세 보기 기능을 모두 구현해야 합니다. 서버에서 초기 데이터를 로드하여 첫 페인트 시간을 최적화하고, 인터랙티브 요소는 클라이언트 컴포넌트에서 처리해야 합니다. 시간 범위 및 주기 변경은 모든 차트에 일관되게 적용되어야 합니다.
```

### 5.3 대시보드 그리드 시스템

```
core-components.md의 5.1 섹션을 참조하여 E-Torch의 대시보드 그리드 시스템을 구현해주세요:

1. 대시보드 그리드 컴포넌트:
   - packages/dashboard/src/components/DashboardGrid.tsx
     - react-grid-layout 기반 핵심 구현
     - 반응형 그리드 설정
     - 뷰 모드(조회)와 편집 모드 분기 처리
     - 레이아웃 저장 및 복원 로직

2. 그리드 설정 및 구성:
   - packages/dashboard/src/config/grid-config.ts
     - 기본 그리드 설정 (열 수, 행 높이, 여백)
     - 브레이크포인트별 반응형 설정
     - 위젯 최소/최대 크기 제한
     - 스냅 그리드 설정

3. 그리드 아이템 컴포넌트:
   - packages/dashboard/src/components/GridItem.tsx
     - 그리드 내 개별 위젯 래퍼
     - 위젯 헤더 (제목, 컨트롤)
     - 리사이즈 핸들 및 드래그 영역
     - 위젯 컨텍스트 메뉴

4. 위젯 컴포넌트:
   - packages/dashboard/src/components/widgets/ChartWidget.tsx
     - 차트 위젯 구현
     - 차트 컴포넌트 통합
     - 차트별 설정 적용
   - packages/dashboard/src/components/widgets/TextWidget.tsx
     - 텍스트/마크다운 위젯
     - 리치 텍스트 편집기 통합
   - packages/dashboard/src/components/widgets/KpiWidget.tsx
     - 주요 성과 지표 위젯
     - 숫자 포맷팅 및 강조 효과

5. 레이아웃 관리 유틸리티:
   - packages/dashboard/src/utils/layout-utils.ts
     - 레이아웃 변환 함수 (서버 <-> 클라이언트 포맷)
     - 그리드 위치 최적화 함수
     - 충돌 방지 및 자동 배치 로직
     - 레이아웃 유효성 검사

6. 드래그 앤 드롭 관리:
   - packages/dashboard/src/components/DragLayer.tsx
     - 드래그 중 프리뷰 및 가이드라인
     - 드래그 핸들 설정
     - 터치/마우스 이벤트 통합

7. 편집 모드 컨트롤:
   - packages/dashboard/src/components/GridControls.tsx
     - 그리드 정렬 버튼
     - 간격 조정 컨트롤
     - 레이아웃 잠금/해제 토글
     - 취소/저장 버튼

8. 레이아웃 저장 로직:
   - packages/dashboard/src/utils/save-layout.ts
     - 레이아웃 변경사항 API 저장
     - 낙관적 업데이트 적용
     - 저장 실패 시 롤백 처리

구현 요구사항: dashboard-editor-wireframe.svg의 디자인을 참조하고, ui-requirements.md의 2.1.3 대시보드 편집 기능을 충실히 구현해야 합니다. react-grid-layout을 활용하되, 고해상도 그리드(24열) 및 모바일 대응 설정을 포함해야 합니다. 드래그 앤 드롭과, 리사이징 상호작용은 매끄럽게 동작해야 하며, 레이아웃 변경 이력은 state-management.md의 Undo/Redo 시스템과 통합되어야 합니다.
```

### 5.4 대시보드 편집기

```
dashboard-editor-wireframe.svg와 core-components.md를 참조하여 E-Torch의 대시보드 편집기를 구현해주세요:

1. 편집기 페이지 컴포넌트:
   - apps/web/app/(dashboard)/dashboard/[id]/edit/page.tsx (기존 대시보드 편집)
     - 대시보드 데이터 로드 및 편집 모드 초기화
     - 권한 검증 (소유자 확인)
   - apps/web/app/(dashboard)/dashboard/new/page.tsx (새 대시보드 생성)
     - 빈 대시보드 템플릿 초기화
   - packages/dashboard/src/components/DashboardEditor.tsx (클라이언트 컴포넌트)
     - 편집 인터페이스 및 로직 구현

2. 편집 툴바:
   - packages/dashboard/src/components/editor/EditorToolbar.tsx
     - ui-requirements.md의 DE-003~006 구현
     - 위젯 추가 드롭다운
     - 레이아웃 제어 (배열, 정렬)
     - Undo/Redo 버튼
     - 저장/취소 버튼

3. 위젯 추가 기능:
   - packages/dashboard/src/components/editor/WidgetSelector.tsx
     - 위젯 유형 선택 UI (차트, 텍스트, KPI 등)
     - 드래그 앤 드롭으로 그리드에 추가
     - 위젯 템플릿 미리보기
   - packages/dashboard/src/components/editor/AddChartModal.tsx
     - 기존 차트 선택 또는 새 차트 생성
     - 차트 검색 및 필터링

4. 대시보드 설정 패널:
   - packages/dashboard/src/components/editor/DashboardSettings.tsx
     - ui-requirements.md의 DE-001~002 구현
     - 대시보드 제목 및 설명 편집
     - 태그 설정
     - 공개/비공개 설정
     - 자동 새로고침 옵션

5. 레이아웃 제어 도구:
   - packages/dashboard/src/components/editor/LayoutControls.tsx
     - 그리드 정렬 옵션 (좌, 중앙, 우)
     - 위젯 간격 조절
     - 레이아웃 잠금/해제
     - 그리드 스냅 토글

6. 위젯 설정 패널:
   - packages/dashboard/src/components/editor/WidgetSettings.tsx
     - 선택한 위젯의 속성 편집
     - 위젯 유형별 설정 컴포넌트 로드
     - 위젯 스타일링 옵션

7. Undo/Redo 기능:
   - packages/state/src/stores/dashboard-editor-store.ts
     - state-management.md의 커맨드 패턴 구현
     - 편집 이력 관리
     - 대시보드 상태 복원

8. 저장 및 발행 기능:
   - packages/dashboard/src/server-actions/save-dashboard.ts
     - 서버 액션을 통한 대시보드 저장
     - 자동 저장 기능 (DE-008)
     - 발행 전 유효성 검사
     - 저장 후 리다이렉션 또는 계속 편집

구현 요구사항: dashboard-editor-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 2.1.3 대시보드 편집 기능을 모두 구현해야 합니다. DashboardGrid 컴포넌트(5.3에서 구현)를 편집 모드로 통합하고, 완전한 WYSIWYG 편집 경험을 제공해야 합니다. 모든 편집 작업은 state-management.md의 Undo/Redo 시스템과 통합되어 실행 취소/다시 실행이 가능해야 합니다.
```

### 5.5 대시보드 공유 기능

```
data-flow.md의 10.2 섹션과 ui-requirements.md의 2.1.4 섹션을 참조하여 E-Torch의 대시보드 공유 기능을 구현해주세요:

1. 대시보드 공유 컴포넌트:
   - packages/dashboard/src/components/sharing/DashboardSharing.tsx
     - 공유 설정 모달 대화상자
     - 공개/비공개 토글 스위치
     - 권한 설정 인터페이스
     - 공유 옵션 관리

2. 공개 설정 및 공유 링크:
   - packages/dashboard/src/components/sharing/ShareLink.tsx
     - ui-requirements.md의 DS-001, DS-002 구현
     - 공개 URL 생성 및 표시
     - 복사 버튼 및 피드백
     - QR 코드 생성 옵션

3. 소셜 미디어 공유:
   - packages/dashboard/src/components/sharing/SocialShare.tsx
     - ui-requirements.md의 DS-003 구현
     - 주요 소셜 미디어 버튼 (Twitter, Facebook, LinkedIn)
     - 커스텀 공유 메시지 작성
     - 미리보기 이미지 활용

4. 임베드 코드 생성:
   - packages/dashboard/src/components/sharing/EmbedCode.tsx
     - 웹사이트 임베드용 iframe 코드 생성
     - 크기 및 테마 설정
     - 자동 새로고침 옵션
     - 미리보기 기능

5. 접근 권한 관리:
   - packages/dashboard/src/components/sharing/AccessControl.tsx
     - 사용자별 권한 설정 (보기, 댓글, 편집)
     - 이메일 초대 기능
     - 기존 권한 사용자 관리
     - 만료 시간 설정

6. 대시보드 구독 기능:
   - packages/dashboard/src/components/explore/SubscribeButton.tsx
     - ui-requirements.md의 DS-007 구현
     - 공유 대시보드 구독 버튼
     - 구독 상태 표시
     - 구독/구독 취소 토글

7. 공유 상태 표시:
   - packages/dashboard/src/components/DashboardStatusBadge.tsx
     - 대시보드 카드 및 상세 페이지에 공유 상태 표시
     - 공개/비공개 아이콘
     - 구독자 수 카운터
     - 툴팁 설명

8. 서버 액션 통합:
   - packages/dashboard/src/server-actions/update-sharing.ts
     - 공유 설정 저장 서버 액션
     - Supabase Row Level Security 연동
     - 권한 변경 알림
     - 공유 링크 생성/비활성화

구현 요구사항: ui-requirements.md의 2.1.4 대시보드 공유 및 탐색 기능을 충실히 구현해야 합니다. 공유 설정은 Supabase의 Row Level Security와 통합되어 권한 기반 접근 제어를 제공해야 합니다. 공유 링크는 짧고 사용자 친화적이어야 하며, 소셜 미디어 공유 시 적절한 OpenGraph 메타데이터가 포함되어야 합니다.
```

## 6. 사용자 관리

### 6.1 사용자 프로필 관리

```
subscription-wireframe.svg를 참조하여 E-Torch의 사용자 프로필 관리 페이지를 구현해주세요:

1. 프로필 설정 페이지:
   - apps/web/app/(profile)/profile/settings/page.tsx (서버 컴포넌트)
     - 사용자 데이터 로드
     - 인증 상태 확인
     - 메타데이터 설정
   - packages/ui/src/components/profile/ProfileSettings.tsx (클라이언트 컴포넌트)
     - 프로필 수정 인터페이스
     - 탭 기반 설정 레이아웃

2. 사용자 정보 폼:
   - packages/ui/src/components/profile/UserInfoForm.tsx
     - React Hook Form을 활용한 폼 관리
     - 표시 이름, 소개, 직업 정보 등 수정
     - 유효성 검사 및 에러 메시지
     - 저장 및 취소 버튼

3. 프로필 이미지 관리:
   - packages/ui/src/components/profile/ProfileImageUploader.tsx
     - 이미지 업로드 및 크롭 기능
     - 미리보기 지원
     - 기본 이미지 옵션
     - Supabase Storage 연동

4. SNS 계정 연결 관리:
   - packages/ui/src/components/profile/ConnectedAccounts.tsx
     - 연결된 소셜 계정 표시
     - 계정 연결/해제 기능
     - 기본 로그인 방법 설정
     - 계정 상태 표시

5. 테마 및 언어 설정:
   - packages/ui/src/components/profile/AppearanceSettings.tsx
     - 테마 선택 (라이트/다크/시스템)
     - 언어 설정 (한국어/영어)
     - UI 밀도 설정
     - 접근성 옵션

6. 계정 관리:
   - packages/ui/src/components/profile/AccountManagement.tsx
     - 계정 삭제 옵션
     - 데이터 내보내기 기능
     - 개인정보 설정
     - 로그아웃 버튼

7. 설정 레이아웃:
   - packages/ui/src/components/profile/SettingsLayout.tsx
     - 사이드 내비게이션 (프로필, 구독, 알림 탭)
     - 현재 선택된 탭 강조
     - 반응형 레이아웃 (모바일에서는 드롭다운)

8. 서버 액션 통합:
   - packages/server-api/src/profile/update-profile.ts
     - 프로필 업데이트 서버 액션
     - 이미지 업로드 처리
     - 유효성 검사 및 제한 적용
     - 성공/실패 피드백

구현 요구사항: subscription-wireframe.svg의 프로필 설정 부분을 참조하여 디자인을 구현하고, design-system.md의 디자인 토큰을 준수해야 합니다. 모든 폼은 React Hook Form을 사용하여 관리하고, 유효성 검사 및 오류 처리가 포함되어야 합니다. 설정 변경은 즉시 적용되어야 하며, 적절한 피드백(토스트 메시지 등)을 제공해야 합니다.
```

### 6.2 구독 관리 시스템

```
subscription-wireframe.svg를 참조하여 E-Torch의 구독 관리 시스템을 구현해주세요:

1. 구독 관리 페이지:
   - apps/web/app/(profile)/profile/subscription/page.tsx (서버 컴포넌트)
     - 현재 구독 정보 로드
     - 결제 내역 초기 데이터 페칭
     - 인증 및 구독 상태 확인
   - packages/ui/src/components/subscription/SubscriptionManagement.tsx (클라이언트 컴포넌트)
     - 구독 관리 인터페이스
     - 플랜 선택 및 결제 흐름

2. 현재 구독 정보 카드:
   - packages/ui/src/components/subscription/CurrentPlanInfo.tsx
     - ui-requirements.md의 SM-001 구현
     - 현재 구독 플랜 표시 (무료/월간/연간)
     - 상태 표시 (활성화, 만료 예정 등)
     - 다음 결제일 및 금액 정보
     - 구독 취소 버튼

3. 구독 플랜 선택:
   - packages/ui/src/components/subscription/PlanSelector.tsx
     - ui-requirements.md의 SM-002 구현
     - 카드 형태의 플랜 비교 표시
     - 플랜별 기능 및 가격 정보
     - 현재 구독 중인 플랜 강조
     - 플랜 변경 버튼

4. 토스페이먼츠 결제 통합:
   - packages/ui/src/components/subscription/PaymentForm.tsx
     - ui-requirements.md의 SM-003 구현
     - 토스페이먼츠 결제 위젯 통합
     - 카드 정보 입력 양식
     - 결제 프로세스 진행
     - 결제 성공/실패 처리

5. 구독 취소 기능:
   - packages/ui/src/components/subscription/CancelSubscription.tsx
     - ui-requirements.md의 SM-004 구현
     - 취소 확인 대화상자
     - 취소 사유 수집
     - 혜택 유지 기간 안내
     - 취소 후 피드백

6. 결제 내역:
   - packages/ui/src/components/subscription/PaymentHistory.tsx
     - ui-requirements.md의 SM-005 구현
     - 과거 결제 내역 테이블
     - 결제일, 금액, 상태 표시
     - 인보이스 다운로드 링크
     - 페이지네이션 지원

7. 결제 방법 관리:
   - packages/ui/src/components/subscription/PaymentMethods.tsx
     - ui-requirements.md의 SM-006 구현
     - 등록된 결제 수단 목록
     - 새 결제 수단 추가
     - 기본 결제 수단 설정
     - 결제 수단 삭제

8. 서버 통합:
   - packages/server-api/src/subscription/manage-subscription.ts
     - 구독 생성/변경/취소 서버 액션
     - 토스페이먼츠 API 연동
     - 결제 처리 및 응답 핸들링
     - 구독 상태 업데이트

구현 요구사항: subscription-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 3.3.2 구독 관리 기능을 모두 구현해야 합니다. 토스페이먼츠 결제 시스템과 통합하여 실제 결제 흐름을 구현하고, 구독 상태에 따른 접근 제어를 적용해야 합니다. 모든 결제 관련 에러는 사용자 친화적인 메시지로 처리되어야 합니다.
```

### 6.3 알림 설정 페이지

```
E-Torch의 알림 설정 페이지를 다음 요구사항에 따라 구현해주세요:

1. 알림 설정 페이지:
   - apps/web/app/(profile)/profile/notifications/page.tsx (서버 컴포넌트)
     - 현재 알림 설정 로드
     - 사용자 인증 확인
     - 메타데이터 설정
   - packages/ui/src/components/notifications/NotificationSettings.tsx (클라이언트 컴포넌트)
     - 알림 설정 인터페이스
     - 탭 기반 레이아웃

2. 알림 유형 설정:
   - packages/ui/src/components/notifications/NotificationTypesForm.tsx
     - ui-requirements.md의 NS-002 구현
     - 알림 유형별 활성화/비활성화 토글
     - 알림 유형:
       - 대시보드 업데이트
       - 댓글 및 멘션
       - 구독 콘텐츠 업데이트
       - 시스템 알림
     - 그룹화 및 카테고리 구분

3. 알림 채널 설정:
   - packages/ui/src/components/notifications/NotificationChannelsForm.tsx
     - ui-requirements.md의 NS-001 구현
     - 채널별 활성화/비활성화 설정
     - 지원 채널:
       - 이메일
       - 웹 알림
     - 채널별 우선순위 설정

4. 알림 주기 설정:
   - packages/ui/src/components/notifications/NotificationFrequencyForm.tsx
     - ui-requirements.md의 NS-003 구현
     - 알림 주기 선택:
       - 즉시
       - 일간 요약
       - 주간 요약
     - 요약 전송 시간 설정
     - 시간대 설정

5. 알림 목록 컴포넌트:
   - packages/ui/src/components/notifications/NotificationsList.tsx
     - ui-requirements.md의 NS-004 구현
     - 수신한 알림 목록 표시
     - 읽음/안 읽음 상태 표시
     - 알림 유형별 아이콘 및 스타일
     - 알림 관련 액션 링크

6. 알림 제어 컴포넌트:
   - packages/ui/src/components/notifications/NotificationActions.tsx
     - ui-requirements.md의 NS-005, NS-006 구현
     - 개별 알림 읽음 표시
     - 알림 삭제 버튼
     - 모든 알림 읽음 표시 버튼
     - 모든 알림 삭제 버튼

7. 알림 설정 저장:
   - packages/server-api/src/notifications/update-notification-settings.ts
     - 알림 설정 업데이트 서버 액션
     - 설정 유효성 검사
     - 저장 성공/실패 피드백
     - 설정 즉시 적용

8. 실시간 알림 컴포넌트:
   - packages/ui/src/components/notifications/NotificationListener.tsx
     - Supabase Realtime 또는 웹소켓 연동
     - 실시간 알림 수신 및 표시
     - 브라우저 알림 통합
     - 알림 카운터 관리

구현 요구사항: design-system.md의 디자인 토큰과 ui-requirements.md의 3.3.3 알림 설정 기능을 준수해야 합니다. 모든 설정은 Zustand 스토어에 저장되고 서버와 동기화되어야 합니다. 알림 설정 변경은 즉시 효과가 적용되어야 하며, 적절한 사용자 피드백을 제공해야 합니다. 실시간 알림은 Supabase Realtime 또는 웹소켓을 통해 구현하고, 브라우저 알림 API와 통합해야 합니다.
```

## 7. 고급 기능 및 최적화

### 7.1 대시보드 탐색 페이지

```
dashboard-explore-wireframe.svg와 core-components.md를 참조하여 E-Torch의 대시보드 탐색 페이지를 구현해주세요:

1. 탐색 페이지 구조:
   - apps/web/app/(dashboard)/explore/page.tsx (서버 컴포넌트)
     - 공개 대시보드 초기 데이터 로드
     - 필터 및 정렬 기본값 설정
     - 메타데이터 구성
   - packages/dashboard/src/components/explore/ExploreView.tsx (클라이언트 컴포넌트)
     - 탐색 UI 및 상호작용 로직
     - 검색 및 필터링 통합
     - 무한 스크롤 또는 페이지네이션

2. 검색 및 필터링:
   - packages/dashboard/src/components/explore/ExploreSearch.tsx
     - ui-requirements.md의 DS-005 구현
     - 검색 필드 및 자동 완성
     - 검색어 하이라이팅
     - 검색 히스토리
   - packages/dashboard/src/components/explore/FilterPanel.tsx
     - 고급 필터 옵션 패널
     - 기간, 데이터 소스, 인기도 등 필터
     - 필터 토글 및 리셋

3. 카테고리 필터:
   - packages/dashboard/src/components/explore/CategoryFilter.tsx
     - ui-requirements.md의 DS-006 구현
     - 대시보드 카테고리 필터링
     - 카테고리 칩/태그 디자인
     - 다중 선택 지원
     - 카테고리 카운트 표시

4. 정렬 옵션:
   - packages/dashboard/src/components/explore/SortOptions.tsx
     - 정렬 기준 선택 (인기순, 최신순, 구독자순)
     - 정렬 방향 (오름차순/내림차순)
     - 현재 정렬 상태 표시
     - 모바일 최적화 드롭다운

5. 대시보드 카드 리스트:
   - packages/dashboard/src/components/explore/ExploreList.tsx
     - 탐색 결과 리스트 표시
     - 카드 형태의 대시보드 항목
     - 작성자, 구독자 수, 미리보기 표시
     - 상세 정보 및 액션 버튼

6. 대시보드 미리보기:
   - packages/dashboard/src/components/explore/DashboardPreview.tsx
     - ui-requirements.md의 DS-008 구현
     - 미리보기 모달 또는 인라인 확장
     - 주요 차트 및 정보 표시
     - 상세 페이지 연결 링크

7. 구독 기능:
   - packages/dashboard/src/components/explore/SubscribeButton.tsx
     - ui-requirements.md의 DS-007 구현
     - 대시보드 구독 버튼
     - 구독 상태 표시 및 토글
     - 구독 확인 대화상자
     - 구독자 수 카운터

8. 데이터 페칭 및 상태 관리:
   - packages/dashboard/src/server/fetchExploreDashboards.ts
     - 필터링 및 정렬 파라미터 처리
     - 페이지네이션 지원
     - 성능 최적화
   - packages/state/src/hooks/useExploreDashboards.ts
     - 필터, 검색, 정렬 상태 관리
     - 무한 스크롤 또는 페이지네이션 로직
     - URL 쿼리 파라미터 동기화

구현 요구사항: dashboard-explore-wireframe.svg의 디자인을 충실히 반영하고, ui-requirements.md의 2.1.4 대시보드 공유 및 탐색 기능을 모두 구현해야 합니다. 탐색 페이지는 대량의 대시보드를 효율적으로 필터링하고 정렬할 수 있어야 하며, 우수한 검색 경험을 제공해야 합니다. 무한 스크롤 또는 페이지네이션을 통해 대량의 결과를 효율적으로 처리해야 합니다.
```

### 7.2 데이터 내보내기 기능

```
data-flow.md의 10장을 참조하여 E-Torch의 데이터 내보내기 기능을 구현해주세요:

1. 차트 내보내기 컴포넌트:
   - packages/charts/src/components/export/ChartExport.tsx
     - 내보내기 드롭다운 메뉴
     - 형식 선택 (PNG, SVG, CSV)
     - 옵션 설정 UI
     - 내보내기 진행 상태 표시

2. 이미지 내보내기 유틸리티:
   - packages/charts/src/utils/export/exportToPng.ts
     - 차트 DOM 요소를 PNG로 변환
     - 해상도 및 크기 옵션
     - 배경색 설정
     - 워터마크 적용
   - packages/charts/src/utils/export/exportToSvg.ts
     - 차트 SVG 추출 및 정리
     - 스타일 인라인화
     - 글꼴 임베딩
     - 메타데이터 추가

3. 데이터 내보내기 유틸리티:
   - packages/charts/src/utils/export/exportToCsv.ts
     - 차트 데이터를 CSV 형식으로 변환
     - 열 헤더 및 포맷 옵션
     - 다운로드 처리
     - 유니코드 지원
   - packages/charts/src/utils/export/exportToExcel.ts
     - 차트 데이터를 Excel 형식으로 변환
     - 다중 시트 지원
     - 서식 및 스타일 적용
     - 차트 포함 옵션

4. 대시보드 내보내기 컴포넌트:
   - packages/dashboard/src/components/export/DashboardExport.tsx
     - 대시보드 내보내기 드롭다운
     - 형식 선택 (PDF, PNG, 데이터)
     - 옵션 설정 패널
     - 내보내기 진행 상태

5. PDF 내보내기 유틸리티:
   - packages/dashboard/src/utils/export/exportToPdf.ts
     - 대시보드를 PDF 문서로 변환
     - 페이지 레이아웃 및 여백 설정
     - 헤더/푸터 포함
     - 차트 및 텍스트 포함
     - 워터마크 적용

6. 워터마크 처리:
   - packages/ui/src/components/export/Watermark.tsx
     - E-Torch 워터마크 컴포넌트
     - 위치 및 불투명도 설정
     - 반응형 크기 조정
     - 커스텀 텍스트 옵션

7. 내보내기 컨트롤:
   - packages/ui/src/components/export/ExportControls.tsx
     - 내보내기 형식 선택 UI
     - 고급 옵션 패널
     - 내보내기 버튼
     - 미리보기 기능

8. 대용량 데이터 처리:
   - packages/data-sources/src/export/large-data-export.ts
     - 대용량 데이터셋 내보내기 최적화
     - 청크 단위 처리
     - 백그라운드 작업 관리
     - 진행률 표시

구현 요구사항: 내보내기 기능은 data-flow.md의 10장에 설명된 데이터 내보내기 및 공유 요구사항을 충족해야 합니다. 모든 형식 변환은 클라이언트 측에서 수행되어야 하며, 대용량 데이터셋에 대해서도 효율적으로 작동해야 합니다. 모든 내보내기 결과물에는 E-Torch 워터마크가 포함되어야 하며, 내보내기 중 발생하는 오류는 사용자 친화적으로 처리되어야 합니다.
```

### 7.3 실시간 데이터 업데이트

```
data-flow.md의 9장을 참조하여 E-Torch의 실시간 데이터 업데이트 기능을 구현해주세요:

1. 주기적 폴링 시스템:
   - packages/data-sources/src/polling/usePolling.ts
     - 설정 가능한 간격의 폴링 메커니즘
     - 탭 가시성에 따른 폴링 조절
     - 네트워크 상태에 따른 폴링 조절
     - 백그라운드/포그라운드 모드 지원

2. 자동 새로고침 컴포넌트:
   - packages/dashboard/src/components/AutoRefreshSettings.tsx
     - ui-requirements.md의 DV-004 구현
     - 새로고침 간격 선택 UI (비활성화, 30초, 1분, 5분 등)
     - 활성화/비활성화 토글
     - 마지막 업데이트 시간 표시
     - 수동 새로고침 버튼

3. Supabase Realtime 통합:
   - packages/data-sources/src/realtime/useRealtimeData.ts
     - Supabase Realtime 구독 설정
     - 테이블 변경 이벤트 수신
     - 이벤트 유형별 처리 (INSERT, UPDATE, DELETE)
     - 상태 업데이트 및 캐시 무효화

4. 협업 기능:
   - packages/dashboard/src/components/collaboration/CollaborationPresence.tsx
     - 현재 대시보드 조회/편집 중인 사용자 표시
     - 실시간 사용자 상태 업데이트
     - 사용자 포인터 위치 공유
     - 충돌 감지 및 해결

5. 실시간 알림 시스템:
   - packages/ui/src/components/notifications/RealtimeNotifications.tsx
     - 데이터 변경 이벤트에 대한 실시간 알림
     - 토스트 메시지 또는 인앱 알림
     - 알림 우선순위 및 그룹화
     - 알림 클릭 시 관련 콘텐츠로 이동

6. 실시간 댓글 시스템:
   - packages/dashboard/src/components/comments/CommentSection.tsx
     - 대시보드에 대한 실시간 댓글 기능
     - 댓글 작성, 수정, 삭제
     - 댓글에 대한 답글
     - 멘션 및 알림

7. 가시성 기반 업데이트:
   - packages/ui/src/hooks/useVisibilityControl.ts
     - IntersectionObserver를 활용한 가시성 감지
     - 화면에 보이는 컴포넌트만 실시간 업데이트
     - 리소스 사용 최적화
     - 우선순위 기반 업데이트

8. 데이터 동기화 관리:
   - packages/state/src/sync/SyncManager.ts
     - 클라이언트-서버 데이터 동기화
     - 오프라인 모드 지원
     - 충돌 해결 전략
     - 안정적인 네트워크 복구

구현 요구사항: data-flow.md의 9장에 설명된 실시간 데이터 업데이트 패턴을 충실히 구현해야 합니다. 자동 새로고침은 ui-requirements.md의 DV-004 기능 명세를 준수해야 하며, 사용자 친화적인 인터페이스를 제공해야 합니다. Supabase Realtime을 활용한 실시간 기능은 성능과 배터리 사용량을 고려하여 최적화되어야 하며, 네트워크 상태 변화에 강건하게 대응해야 합니다. 실시간 공동 작업 기능은 직관적인 UI로 현재 접속 중인 사용자와 그들의 활동을 표시해야 합니다.
```

### 7.4 성능 최적화

```
core-components.md의 9장과 architecture.md를 참조하여 E-Torch의 성능 최적화를 구현해주세요:

1. 코드 분할 및 지연 로딩:
   - 적용 대상: 대시보드 편집기, 차트 에디터, 설정 페이지
   - 구현 방법:
     - Next.js의 dynamic import 활용
     - 차트 유형별 동적 임포트
     - 초기 로드 경로 최적화
     - 사용자 인터랙션 기반 지연 로딩

2. 이미지 최적화:
   - 적용 대상: 대시보드 썸네일, 프로필 이미지
   - 구현 방법:
     - Next.js Image 컴포넌트 활용
     - 이미지 크기 및 품질 최적화
     - WebP/AVIF 포맷 활용
     - 이미지 CDN 연동

3. 메모이제이션 최적화:
   - 적용 대상: 차트 렌더링, 복잡한 리스트 렌더링
   - 구현 방법:
     - React.memo 전략적 활용
     - useMemo/useCallback 최적화
     - 상태 선택자 최적화 (Zustand 선택적 구독)
     - 리렌더링 최소화 전략

4. 서버/클라이언트 컴포넌트 분리:
   - 적용 대상: 모든 페이지 및 주요 컴포넌트
   - 구현 방법:
     - 서버 컴포넌트 최대 활용
     - 클라이언트 번들 크기 최소화
     - 하이드레이션 최적화
     - 스트리밍 SSR 활용

5. 상태 업데이트 최적화:
   - 적용 대상: 차트 에디터, 대시보드 에디터
   - 구현 방법:
     - 상태 업데이트 일괄 처리
     - 디바운싱/스로틀링 적용
     - 지역 상태와 전역 상태 분리
     - 트랜잭션 기반 상태 업데이트

6. 데이터 요청 최적화:
   - 적용 대상: 대시보드 및 차트 데이터 로딩
   - 구현 방법:
     - 요청 일괄 처리 (Batching)
     - 데이터 프리페칭
     - 선택적 데이터 로딩
     - 캐싱 전략 적용

7. 렌더링 성능 개선:
   - 적용 대상: 대시보드 그리드, 차트 컴포넌트
   - 구현 방법:
     - 가상화 기법 적용 (react-window/react-virtualized)
     - 렌더링 스로틀링
     - 애니메이션 최적화
     - 레이아웃 스래싱 방지

8. 웹 바이탈 최적화:
   - 적용 대상: 전체 애플리케이션
   - 구현 방법:
     - LCP, FID, CLS 최적화
     - 성능 메트릭 측정 및 분석
     - 크리티컬 CSS 최적화
     - 리소스 우선순위 설정

9. 개발자 도구 통합:
   - 적용 대상: 개발 환경
   - 구현 방법:
     - React 개발자 도구 설정
     - 성능 프로파일링 도구
     - 렌더링 디버깅 유틸리티
     - 번들 분석 도구

구현 요구사항: core-components.md의 9장에 설명된 성능 최적화 전략을 충실히 구현해야 합니다. 모든 최적화는 측정 가능한 성능 향상을 목표로 해야 하며, 사용자 경험을 저해하지 않아야 합니다. 대시보드와 차트는 대량의 데이터(10,000+ 데이터 포인트)에서도 60fps 이상의 렌더링 성능을 유지해야 하며, 초기 로드 시간은 2초 이내를 목표로 해야 합니다. 개발 모드에서는 성능 문제를 쉽게 식별하고 디버깅할 수 있는 도구를 제공해야 합니다.
```

## 8. 테스트 및 배포

### 8.1 테스트 환경 설정

```
architecture.md와 product-spec.md를 참조하여 E-Torch의 테스트 환경을 설정해주세요:

1. Vitest 기본 설정:
   - vitest.config.ts (루트 설정)
     - 전역 테스트 환경 설정
     - 모듈 별칭 및 경로 매핑
     - Turborepo 통합
     - 공통 플러그인 설정
   - vitest-setup.ts (글로벌 설정)
     - Testing Library 설정
     - 모킹 유틸리티 설정
     - 글로벌 테스트 헬퍼

2. 단위 테스트 환경:
   - packages/*/vitest.config.ts (패키지별 설정)
     - 패키지별 테스트 설정 구성
     - 커버리지 설정
     - 환경 변수 및 종속성 설정
   - 주요 테스트 패턴:
     - 컴포넌트 단위 테스트
     - 유틸리티 함수 테스트
     - 상태 로직 테스트

3. 통합 테스트 환경:
   - apps/web/test/integration/ (통합 테스트 디렉토리)
     - 페이지 통합 테스트
     - API 통합 테스트
     - 상태 관리 통합 테스트
   - MSW (Mock Service Worker) 설정
     - API 모킹 핸들러
     - 응답 템플릿

4. E2E 테스트 환경 (Playwright):
   - playwright.config.ts
     - 브라우저 설정 (Chromium, Firefox, WebKit)
     - 스크린샷 및 비디오 설정
     - 병렬 실행 설정
     - 환경 변수 설정
   - apps/web/e2e/
     - 사용자 흐름 테스트
     - 권한 및 인증 테스트
     - 성능 테스트

5. 테스트 유틸리티:
   - packages/test-utils/src/
     - 테스트 렌더링 헬퍼
     - 테스트 데이터 생성기
     - 모킹 유틸리티
     - 단언 헬퍼

6. 테스트 데이터 및 픽스처:
   - packages/test-utils/fixtures/
     - 모의 차트 데이터
     - 모의 대시보드 데이터
     - 모의 사용자 데이터
     - API 응답 템플릿

7. 코드 커버리지 설정:
   - 커버리지 레포트 생성
     - 인라인 소스맵 활성화
     - HTML/JSON/LCOV 리포트 생성
   - 커버리지 임계값 설정
     - 라인: 80%
     - 문장: 80% 
     - 함수: 85%
     - 브랜치: 75%

8. 주요 컴포넌트 테스트 예제:
   - 차트 컴포넌트 테스트
   - 대시보드 그리드 테스트
   - 인증 흐름 테스트
   - 상태 관리 테스트

구현 요구사항: 모든 테스트 설정은 Turborepo 모노레포 구조와 통합되어야 하며, 패키지 간 종속성을 고려해야 합니다. Vitest와 Testing Library를 기본 도구로 활용하고, E2E 테스트는 Playwright를 사용해야 합니다. 각 패키지는 독립적인 테스트 스크립트를 가져야 하며, 전체 테스트 실행과 개별 패키지 테스트 실행이 모두 가능해야 합니다. 코드 커버리지 리포트는 전체 프로젝트와 개별 패키지 수준에서 모두 생성될 수 있어야 합니다.
```

### 8.2 CI/CD 파이프라인

```
product-spec.md의 9장을 참조하여 E-Torch의 CI/CD 파이프라인을 구성해주세요:

1. GitHub Actions 워크플로우:
   - .github/workflows/ci.yml
     - 풀 리퀘스트 및 main 브랜치 push 트리거
     - Node.js 및 pnpm 설정
     - 의존성 캐싱 및 설치
     - 병렬 작업 설정
     - 성능 최적화 전략
   - .github/workflows/cd.yml
     - main 브랜치 merge 트리거
     - 스테이징/프로덕션 환경 배포
     - 롤백 메커니즘
     - 배포 성공/실패 알림
   - .github/workflows/preview.yml
     - PR 환경 미리보기 배포
     - 코멘트 자동화
     - 브랜치 단위 격리된 환경

2. CI 단계 구성:
   - Lint 및 타입 체크
     - ESLint를 통한 코드 품질 검증
     - TypeScript 타입 검사
     - 코드 스타일 검증
   - 테스트 실행
     - 단위 테스트 (Vitest)
     - 통합 테스트
     - E2E 테스트 (Playwright)
   - 빌드 검증
     - Next.js 빌드 테스트
     - 번들 크기 검사
     - 의존성 감사

3. CD 단계 구성:
   - 환경별 배포
     - 개발(dev): PR 기반 자동 배포
     - 스테이징(staging): main 브랜치 자동 배포
     - 프로덕션(prod): 수동 승인 후 배포
   - 배포 전략
     - Atomic 배포
     - 블루/그린 배포
     - 점진적 롤아웃
   - 배포 후 검증
     - Smoke 테스트
     - 성능 메트릭 수집
     - 오류 모니터링

4. Vercel 배포 설정:
   - vercel.json
     - 환경 구성
     - 빌드 커맨드 및 출력 디렉토리
     - 리다이렉트 및 리라이트 규칙
     - 정적 파일 처리
   - 프로젝트 연동
     - GitHub 저장소 연결
     - 배포 브랜치 설정
     - 환경 변수 설정
     - 도메인 구성

5. 환경 변수 관리:
   - GitHub Secrets 활용
     - 비밀 환경 변수 저장
     - API 키 및 토큰 관리
   - 환경별 변수 분리
     - .env.development
     - .env.staging
     - .env.production
   - 환경 변수 유효성 검사
     - 필수 변수 체크
     - 형식 검증

6. 모니터링 및 알림 통합:
   - 오류 추적 (Sentry)
     - JavaScript 오류 캡처
     - 사용자 컨텍스트 연결
     - 릴리스 추적
   - 성능 모니터링
     - 웹 바이탈 지표 추적
     - API 응답 시간 모니터링
     - 사용자 경험 메트릭
   - 알림 시스템
     - Slack 통합
     - 이메일 알림
     - 온콜 로테이션

7. 자동화된 테스트:
   - 회귀 테스트 자동화
     - 주요 사용자 흐름 테스트
     - 시각적 회귀 테스트
     - 성능 회귀 테스트
   - 보안 검사
     - 의존성 취약점 스캔
     - OWASP 테스트
     - 인증 테스트

구현 요구사항: 모든 CI/CD 파이프라인은 product-spec.md의 9장에 언급된 개발 일정과 출시 전략을 지원해야 합니다. GitHub Actions 워크플로우는 빠르고 안정적으로 실행되어야 하며, 의존성 캐싱과 병렬 작업을 활용하여 성능을 최적화해야 합니다. Vercel 배포는 환경별로 구성되어야 하며, 롤백 메커니즘과 점진적 롤아웃을 지원해야 합니다. 모니터링 및 알림 시스템은 배포 후 문제를 신속하게 감지하고 대응할 수 있도록 설정되어야 합니다.
```

### 8.3 사용자 가이드 문서

```
product-spec.md를 기반으로 E-Torch의 사용자 가이드 문서를 작성해주세요:

1. 시작하기 가이드:
   - 서비스 개요 및 목적
     - E-Torch 소개 및 기본 컨셉
     - 주요 기능 및 사용 사례
     - 타겟 사용자별 가치 제안
   - 계정 생성 및 로그인
     - SNS 로그인 프로세스 (Google, Naver, Kakao)
     - 첫 방문 온보딩 흐름
     - 개인정보 처리방침 및 이용약관
   - 사용자 인터페이스 소개
     - 주요 화면 레이아웃 설명
     - 내비게이션 구조
     - 모바일/데스크톱 인터페이스 차이점

2. 대시보드 가이드:
   - 대시보드 목록 탐색
     - 목록 보기 및 정렬/필터링
     - 대시보드 카드 정보 이해
     - 검색 기능 활용
   - 대시보드 생성 및 편집
     - 새 대시보드 생성 프로세스
     - 레이아웃 편집 방법
     - 저장 및 공유 설정
   - 위젯 추가 및 배치
     - 차트 위젯 추가
     - 텍스트 위젯 활용
     - 위젯 크기 조정 및 배치
   - 시간 범위 및 주기 설정
     - 시간 필터 사용법
     - 데이터 주기 변경 방법
     - 자동 새로고침 설정

3. 차트 사용법:
   - 차트 유형별 특징 및 사용 사례
     - 시계열 차트: 추세 분석
     - 막대 차트: 비교 분석
     - 산점도: 상관관계 분석
     - 기타 차트 유형 가이드
   - 차트 옵션 설정
     - 기본 설정 (제목, 설명, 축)
     - 스타일 설정 (색상, 폰트, 레이아웃)
     - 고급 설정 (상호작용, 툴팁)
   - 차트 데이터 변환
     - 원본 데이터 vs 변환 데이터
     - 변화율 계산 옵션
     - 누적값 및 이동평균
   - 데이터 시각화 모범 사례
     - 차트 유형 선택 가이드
     - 효과적인 데이터 표현 방법
     - 오해를 방지하는 시각화 팁

4. 데이터 소스 가이드:
   - 지원되는 데이터 소스
     - KOSIS (국가통계포털)
     - ECOS (한국은행 경제통계시스템)
     - OECD 데이터
   - 데이터 조회 및 필터링
     - 지표 검색 방법
     - 데이터 필터링 옵션
     - 기간 및 주기 설정
   - 데이터 변환 옵션
     - 원본값 사용
     - 변화율 계산 (전기대비, 전년동기대비)
     - 누적값 계산
   - 데이터 해석 가이드
     - 주요 경제지표 이해
     - 상관관계 vs 인과관계
     - 통계적 해석 주의사항

5. 공유 및 협업:
   - 대시보드 공유 방법
     - 공개/비공개 설정
     - 링크 공유 기능
     - 소셜 미디어 공유
   - 권한 설정
     - 접근 권한 관리
     - 보기/편집 권한 구분
     - 팀 협업 설정
   - 구독 기능 활용
     - 대시보드 구독 방법
     - 업데이트 알림 설정
     - 구독 관리
   - 댓글 및 피드백
     - 대시보드 댓글 작성
     - 특정 차트에 대한 피드백
     - 멘션 및 알림

6. 계정 관리:
   - 프로필 설정
     - 개인 정보 관리
     - 프로필 이미지 변경
     - 소셜 계정 연결/해제
   - 구독 관리
     - 구독 플랜 정보
     - 결제 관리
     - 구독 변경/취소
   - 알림 설정
     - 알림 유형 및 채널 설정
     - 알림 주기 관리
     - 알림 내역 확인
   - 보안 설정
     - 계정 보안 강화 방법
     - 연결된 기기 관리
     - 로그인 내역 확인

7. 고급 기능:
   - 대
