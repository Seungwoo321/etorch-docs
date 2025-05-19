# E-Torch 세부 구현을 위한 상세 프롬프트

각 작업을 완벽하게 구현할 수 있도록 세부적인 프롬프트를 작성했습니다. 이를 사용하면 체계적으로 E-Torch를 개발할 수 있습니다.

## 1. 환경 설정 및 기반 구축

### 1.1 모노레포 구조 설정

```
E-Torch 프로젝트의 Turborepo 모노레포 구조를 설정해주세요. 다음 사항을 포함해야 합니다:

1. 프로젝트 초기화:
   - pnpm으로 워크스페이스 설정
   - Turborepo 구성 (turbo.json)
   - 루트 package.json 설정 (공통 스크립트 포함)

2. 패키지 구조:
   - apps/web (Next.js 15 앱)
   - apps/docs (문서화 앱)
   - packages/core (타입 정의, 상수, 기본 인터페이스)
   - packages/charts (차트 렌더링 및 편집 컴포넌트)
   - packages/dashboard (대시보드 그리드 및 관리 컴포넌트)
   - packages/data-sources (데이터 커넥터 및 변환 로직)
   - packages/ui (UI 컴포넌트 및 서버 래퍼)
   - packages/utils (포맷터, 유효성 검사, 헬퍼 함수)
   - packages/state (Zustand 스토어, Tanstack Query 훅)
   - packages/server-api (API 경로, 미들웨어, 서버 액션)

3. 패키지 의존성 설정:
   - architecture.md 문서에 정의된 의존성 구조 구현
   - 순환 의존성 방지를 위한 제약 설정
   - ESLint 9.27.0, React 19.1.0, Next 15.3.2, Tailwind 4.1.7, shadcn@canary 버전 사용
   - 코드 포맷 및 문법검사 스탠다그 규칙 플러그인 @seungwoo321/eslint-plugin-standard-js@^1.0.1, @seungwoo321/eslint-plugin-standard-jsx@^1.0.1 사용

4. 빌드 및 개발 환경:
   - TypeScript 구성 (tsconfig.json, tsconfig.base.json)
   - 개발 스크립트 구성 (dev, build, lint)

5. 각 패키지 기본 구조:
   - src 폴더 및 index.ts 파일
   - package.json 설정 (이름, 버전, main, types, scripts)
   - README.md 기본 문서

6. ESLint 9.27.0 + Standard 규칙 설정:
   ```js
   // eslint.config.mjs
   import { defineConfig } from 'eslint/config'
   import standardJs from '@seungwoo321/eslint-plugin-standard-js'
   import standardJsx from '@seungwoo321/eslint-plugin-standard-jsx'
   import tseslint from 'typescript-eslint'
   import nextPlugin from '@next/eslint-plugin-next'

   export default defineConfig([
      {
         ignores: ['app/components/ui/*']
      },
      {
         files: ['**/*.{js,mjs,cjs,ts,mts,jsx,tsx}'],
         extends: [
            ...standardJs.configs.recommended,
            ...standardJsx.configs.recommended,
            ...tseslint.configs.strict,
            ...tseslint.configs.stylistic

         ],
         plugins: {
            next: nextPlugin
         }
      }
   ])
   ```

패키지 간 의존성과 파일 구조를 명확히 보여주는 전체 코드를 제공해주세요.

```

### 1.2 디자인 시스템 구현

```

E-Torch 디자인 시스템을 구현해주세요. design-system.md 문서를 기반으로 다음 요소를 포함해야 합니다:

1. Tailwind CSS 설정 (tailwind.config.js):
   - 색상 체계 구현 (Primary: #0c1e3e, Secondary: #1a56db, Tertiary: #0284c7 등)
   - 타이포그래피 설정 (Inter, JetBrains Mono 폰트, 크기 스케일)
   - 차트 색상 팔레트 확장
   - 컨테이너 쿼리 및 반응형 설정

2. 기본 컴포넌트 구현 (packages/ui에 위치):
   - Shadcn/UI 기반 컴포넌트 커스터마이징
   - 버튼 컴포넌트 (Primary, Secondary, Outline, Ghost, Destructive 변형)
   - 카드 컴포넌트 (Default, Bordered, Elevated 변형)
   - 폼 컴포넌트 (Input, Checkbox, Toggle, DatePicker)
   - 서버 래퍼 컴포넌트 (ButtonServer, CardServer 등)

3. 유틸리티 클래스 및 컴포넌트:
   - 레이아웃 시스템 (Container, Grid, Flex 컴포넌트)
   - 스페이싱 유틸리티 (margin, padding 클래스)
   - 접근성 컴포넌트 (VisuallyHidden, SkipLink)

4. 테마 관리:
   - 라이트/다크 모드 토글 구현
   - 색상 변수 정의 (CSS 변수)
   - 테마 컨텍스트 및 훅 구현

5. 아이콘 시스템:
   - Lucide React 아이콘 활용
   - 커스텀 아이콘 컴포넌트 구현

6. 문서화 컴포넌트:
   - 컬러 팔레트 시각화
   - 타이포그래피 스케일 시각화
   - 컴포넌트 사용 예시

모든 파일의 정확한 경로와 각 컴포넌트의 완전한 구현 코드를 제공해주세요.

```

### 1.3 상태 관리 기본 구조

```

E-Torch 상태 관리 기본 구조를 Zustand와 Tanstack Query를 사용하여 구현해주세요. state-management.md 문서를 기반으로 다음 요소를 포함해야 합니다:

1. Zustand 스토어 설정 (packages/state에 위치):
   - 앱 전역 상태 스토어 (테마, 알림, 에러, 로딩 상태)
   - 대시보드 상태 스토어 (대시보드 목록, 선택된 대시보드, 레이아웃)
   - 차트 에디터 상태 스토어 (차트 설정, Undo/Redo 기능)
   - 미들웨어 구성 (immer, persist, devtools, subscribeWithSelector)
   - 정규화된 상태 구조 구현

2. Tanstack Query 설정:
   - 쿼리 클라이언트 구성
   - 계층적 쿼리 키 구조 (queryKeys 객체)
   - 지표 유형별 캐싱 전략 구현
   - React Query 개발 도구 통합

3. 사용자 정의 훅:
   - 대시보드 데이터 관리 훅
   - 차트 데이터 관리 훅
   - 낙관적 업데이트 패턴 구현
   - 데이터 변환 및 필터링 훅

4. 서버 상태와 클라이언트 상태 통합:
   - 서버 컴포넌트에서 페치한 데이터를 클라이언트 스토어에 통합
   - initialData 패턴 구현
   - hydration 전략

5. Undo/Redo 시스템:
   - 커맨드 패턴 기반 구현
   - 히스토리 관리자 구현
   - 트랜잭션 지원

6. 이벤트 버스 패턴:
   - 컴포넌트 간 통신을 위한 이벤트 시스템
   - 구독/발행 메커니즘
   - 이벤트 타입 정의

7. 상태 디버깅 도구:
   - 개발 모드 디버깅 기능
   - 액션 로깅
   - 상태 스냅샷

파일 구조, 타입 정의, 구현 코드를 상세히 제공해주세요.

```

### 1.4 라우팅 구조 설정

```

E-Torch의 Next.js App Router 기반 라우팅 구조를 설정해주세요. routing.md 문서를 기반으로 다음 요소를 포함해야 합니다:

1. 기본 라우팅 구조 (app 디렉토리 내):
   - (auth) 그룹: 로그인 및 인증 관련 페이지
     - login/page.tsx
     - callback/page.tsx
     - layout.tsx (인증 레이아웃)
   - (dashboard) 그룹: 대시보드 관련 페이지
     - dashboard/page.tsx (대시보드 목록)
     - dashboard/[id]/page.tsx (개별 대시보드 상세)
     - dashboard/new/page.tsx (새 대시보드 생성)
     - dashboard/[id]/edit/page.tsx (대시보드 편집)
     - explore/page.tsx (대시보드 탐색/발견)
     - layout.tsx (대시보드 레이아웃)
   - (chart) 그룹: 차트 관련 페이지
     - chart-editor/[id]/page.tsx (차트 에디터)
     - chart/[id]/page.tsx (개별 차트 상세)
     - layout.tsx (차트 레이아웃)
   - (profile) 그룹: 사용자 프로필 관련 페이지
     - profile/settings/page.tsx (프로필 설정)
     - profile/subscription/page.tsx (구독 관리)
     - layout.tsx (프로필 레이아웃)
   - layout.tsx (루트 레이아웃)
   - page.tsx (홈페이지)

2. 확장 라우팅 구조:
   - @modal 인터셉트 라우트: 모달 다이얼로그 구현
   - 병렬 라우트: 대시보드 부가 정보 표시

3. 레이아웃 컴포넌트:
   - RootLayout: 공통 메타데이터, 프로바이더, 테마 설정
   - AuthLayout: 로그인 화면 스타일링
   - DashboardLayout: 사이드바, 헤더, 메인 콘텐츠 영역
   - ChartLayout: 전체 화면 콘텐츠
   - ProfileLayout: 사이드바, 설정 메뉴

4. 라우트 보호:
   - 미들웨어 설정 (middleware.ts)
   - 서버 컴포넌트 내 인증 검증
   - 클라이언트 컴포넌트 AuthGuard

5. 메타데이터 설정:
   - 정적 메타데이터
   - 동적 메타데이터 함수

6. 네비게이션 컴포넌트:
   - SideNavigation (사이드바)
   - HeaderNavigation (헤더)
   - BreadcrumbNavigation (위치 표시)

각 파일의 전체 구현 코드와 구조를 제공해주세요.

```

## 2. 인증 시스템

### 2.1 Supabase 인증 연동

```

E-Torch의 Supabase 기반 인증 시스템을 구현해주세요. 다음 요소를 포함해야 합니다:

1. Supabase 클라이언트 설정:
   - packages/server-api/supabase/client.ts: 클라이언트 사이드 설정
   - packages/server-api/supabase/server.ts: 서버 사이드 설정
   - 환경 변수 구성 (.env.local 예시)

2. 인증 프로바이더 구현:
   - apps/web/components/providers/supabase-auth-provider.tsx
   - Supabase Auth 상태와 React 상태 연동
   - 로그인, 로그아웃, 세션 관리 함수 제공

3. SNS 로그인 구현:
   - Google OAuth 연동
   - Naver OAuth 연동
   - Kakao OAuth 연동
   - OAuth 리다이렉션 설정

4. 콜백 핸들러:
   - app/(auth)/callback/page.tsx: OAuth 콜백 처리
   - 토큰 저장 및 세션 설정
   - 리다이렉션 로직

5. 로그인 페이지:
   - app/(auth)/login/page.tsx: 로그인 UI
   - 각 SNS 로그인 버튼 구현

6. 인증 상태 관리:
   - packages/state/src/auth.ts: Zustand 인증 스토어
   - 사용자 정보 저장 및 갱신
   - 토큰 리프레시 로직

7. 인증 훅:
   - packages/state/src/hooks/useAuth.ts
   - 현재 사용자 정보 제공
   - 로그인 상태 확인
   - 로그인/로그아웃 함수

전체 코드 구현과 Supabase 프로젝트 설정 방법을 자세히 제공해주세요.

```

### 2.2 인증 및 접근 제어

```

E-Torch의 인증 기반 접근 제어 시스템을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 미들웨어 기반 라우트 보호:
   - middleware.ts: 요청 경로 검사, 토큰 유효성 검증
   - 공개/보호된 라우트 정의
   - 인증되지 않은 사용자 리디렉션

2. 서버 컴포넌트 인증 검증:
   - 서버 컴포넌트 내에서 세션 확인
   - createServerClient 유틸리티
   - 인증 필요 시 리디렉션 또는 404 처리

3. AuthGuard 클라이언트 컴포넌트:
   - apps/web/components/auth/auth-guard.tsx
   - 클라이언트에서 인증 상태 확인
   - 로딩 상태 처리
   - 자식 컴포넌트 조건부 렌더링

4. 권한 관리 시스템:
   - 대시보드 소유권 확인
   - CRUD 작업별 권한 검사
   - 공유 대시보드 접근 권한

5. 인증 상태 확인 훅:
   - useRequireAuth: 인증 필요 페이지용
   - useOptionalAuth: 인증 선택적 페이지용

6. 프로필 권한:
   - 사용자 계정 설정 접근 제어
   - 구독 정보 접근 제어

7. 보안 헤더 및 CSRF 보호:
   - CSP(Content Security Policy) 설정
   - CSRF 토큰 구현
   - XSS 방지 헤더

세부 구현 코드와 함께 각 접근 제어 메커니즘의 작동 방식을 자세히 설명해주세요.

```

## 3. 데이터 소스 관리

### 3.1 API 클라이언트 구현

```

E-Torch API 클라이언트를 구현해주세요. data-flow.md 문서를 참고하여 다음 요소를 포함해야 합니다:

1. 기본 HTTP 클라이언트:
   - packages/data-sources/src/client/base-client.ts
   - fetch API 래핑
   - 타입 안전성 보장
   - 기본 옵션 설정

2. 인터셉터 시스템:
   - 요청 인터셉터 (헤더 추가, 인증 등)
   - 응답 인터셉터 (응답 변환, 오류 처리)
   - 인증 인터셉터 (JWT 토큰 관리)

3. 에러 핸들링:
   - API 에러 클래스 계층
   - HTTP 상태 코드별 처리
   - 401 오류 자동 리프레시 로직
   - 네트워크 오류 재시도 로직

4. 도메인별 클라이언트:
   - 경제지표 API 클라이언트 (KOSIS, ECOS, OECD)
   - 대시보드 API 클라이언트
   - 차트 API 클라이언트
   - 사용자 API 클라이언트

5. 요청 일괄 처리:
   - 여러 요청을 묶어서 처리하는 메커니즘
   - 성능 최적화 전략

6. Supabase 클라이언트 통합:
   - Supabase SDK 래핑
   - 타입 안전성 보장
   - 인증 통합

7. API 클라이언트 타입:
   - 응답 타입 정의
   - 요청 파라미터 타입 정의
   - 에러 타입 정의

각 클라이언트 및 유틸리티의 전체 구현 코드를 제공해주세요.

```

### 3.2 데이터 변환 파이프라인

```

E-Torch의 데이터 변환 파이프라인을 구현해주세요. data-flow.md 문서를 참고하여 다음 요소를 포함해야 합니다:

1. 데이터 모델 및 타입 정의:
   - packages/core/src/types/data-models.ts
   - 통합 데이터 모델 (NormalizedTimeSeriesData)
   - 지표 메타데이터 타입
   - 변환 타입 및 열거형

2. 데이터 변환 기능:
   - packages/data-sources/src/transforms/transformations.ts
   - 원본값 전달
   - 기간 대비 변화율 (MoM, QoQ, YoY)
   - 전년 동기 대비 변화율
   - 누적값 계산

3. 결측치 처리 알고리즘:
   - packages/data-sources/src/transforms/missing-values.ts
   - 선형 보간법 구현
   - 직전/다음 값 복제 방식
   - 0 또는 null 값 처리

4. 기간 동기화 전략:
   - packages/data-sources/src/transforms/synchronization.ts
   - 업샘플링 (낮은 주기→높은 주기)
   - 다운샘플링 (높은 주기→낮은 주기)
   - 집계 방법 (평균, 합계, 첫값, 마지막값 등)

5. 데이터 다운샘플링 알고리즘:
   - packages/data-sources/src/transforms/downsampling.ts
   - LTTB(Largest-Triangle-Three-Buckets) 구현
   - M4 알고리즘 구현
   - 균등 간격 리샘플링

6. 데이터 정규화:
   - 소스별 데이터 구조 정규화
   - 날짜 형식 표준화
   - 단위 변환 유틸리티

7. 통합 변환 파이프라인:
   - 파이프라인 단계 구성
   - 변환 순서 관리
   - 최적화 전략

각 알고리즘과 유틸리티의 전체 구현 코드를 제공해주세요.

```

### 3.3 데이터 캐싱 전략 구현

```

E-Torch의 데이터 캐싱 전략을 구현해주세요. state-management.md 문서를 참고하여 다음 요소를 포함해야 합니다:

1. TanStack Query 캐싱 설정:
   - packages/state/src/query/query-client.ts
   - 기본 쿼리 클라이언트 설정
   - staleTime, gcTime 설정
   - 캐시 키 구조 정의

2. 지표 유형별 캐싱 전략:
   - packages/state/src/query/caching-strategies.ts
   - 실시간 금융 지표 (짧은 staleTime)
   - 일일 경제지표 (중간 staleTime)
   - 월간/분기별/연간 지표 (긴 staleTime)
   - 지표 코드별 카테고리 매핑

3. 캐시 무효화 패턴:
   - 시간 기반 무효화
   - 이벤트 기반 무효화
   - 선택적 무효화
   - 선제적 리페치
   - 우선순위 기반 무효화

4. 영구 캐싱 전략:
   - localStorage/sessionStorage 활용
   - 캐시 만료 메커니즘
   - 저장소 용량 관리

5. 서버 컴포넌트 캐싱:
   - Next.js 캐시 활용
   - revalidatePath, revalidateTag 사용
   - 동적 vs 정적 렌더링

6. 캐시 상태 관리 훅:
   - useQueryWithCache
   - usePrefetchQuery
   - useInvalidateQueries

7. 가시성 기반 리페치:
   - IntersectionObserver 활용
   - 화면에 보이는 컴포넌트만 리페치

전체 구현 코드와 다양한 사용 예제를 제공해주세요.

```

## 4. 차트 컴포넌트

### 4.1 기본 차트 컴포넌트

```

E-Torch의 기본 차트 컴포넌트를 구현해주세요. core-components.md 문서를 참고하여 다음 요소를 포함해야 합니다:

1. 차트 컴포넌트 계층 구조:
   - packages/charts/src/components/ChartComponent.tsx (클라이언트 컴포넌트)
   - packages/charts/src/server/ChartServerWrapper.tsx (서버 컴포넌트)
   - packages/charts/src/components/ChartRenderer.tsx (차트 타입 분기)

2. 시계열 차트:
   - packages/charts/src/components/chart-types/TimeSeriesChart.tsx
   - 선, 영역, 막대 스타일 지원
   - 다중 Y축 지원
   - 확대/축소 기능

3. 막대 차트:
   - packages/charts/src/components/chart-types/BarChart.tsx
   - 수직/수평 막대 지원
   - 그룹/스택 막대 지원
   - 데이터 레이블 옵션

4. 산점도 차트:
   - packages/charts/src/components/chart-types/ScatterChart.tsx
   - 포인트 크기/모양 커스터마이징
   - 회귀선 옵션
   - 포인트 레이블 지원

5. 추가 차트 유형:
   - RadarChart.tsx (레이더 차트)
   - RadialBarChart.tsx (방사형 막대 차트)
   - PieChart.tsx (파이 차트)

6. 차트 컨트롤:
   - packages/charts/src/components/controls/ChartControls.tsx
   - 확대/축소 버튼
   - 다운로드 버튼
   - 전체 화면 전환

7. 차트 로딩 및 에러 컴포넌트:
   - packages/charts/src/components/ChartSkeleton.tsx
   - packages/charts/src/components/ChartError.tsx

각 차트 유형에 대한 전체 구현 코드와 필요한 타입 정의, 유틸리티 함수를 제공해주세요.

```

### 4.2 차트 옵션 패널

```

E-Torch의 차트 옵션 패널을 구현해주세요. ui-requirements.md의 3.1-3.2 섹션을 참고하여 다음 요소를 포함해야 합니다:

1. 공통 옵션 컴포넌트:
   - packages/charts/src/components/options/PanelOptions.tsx (컴포넌트 제목, 설명 등)
   - packages/charts/src/components/options/TooltipOptions.tsx (툴팁 설정)
   - packages/charts/src/components/options/LegendOptions.tsx (범례 설정)
   - packages/charts/src/components/options/XAxisOptions.tsx (X축 설정)
   - packages/charts/src/components/options/YAxisOptions.tsx (Y축 설정)
   - packages/charts/src/components/options/YAxisSecondaryOptions.tsx (보조 Y축 설정)

2. 차트별 옵션 컴포넌트:
   - packages/charts/src/components/options/GraphStyles.tsx (시계열 차트 스타일)
   - packages/charts/src/components/options/ScatterChartOptions.tsx (산점도 옵션)
   - packages/charts/src/components/options/RadarChartOptions.tsx (레이더 차트 옵션)
   - packages/charts/src/components/options/RadialBarChartOptions.tsx (방사형 바 차트 옵션)
   - packages/charts/src/components/options/TextComponentOptions.tsx (텍스트 위젯 옵션)

3. 옵션 패널 통합 컴포넌트:
   - packages/charts/src/components/options/OptionsPanel.tsx (옵션 패널 컨테이너)
   - 아코디언 형태의 옵션 그룹핑
   - 차트 유형별 활성화 옵션 영역 관리

4. 옵션 연결 및 적용 시스템:
   - packages/charts/src/hooks/useChartOptions.ts (옵션 상태 관리 훅)
   - 옵션 변경 시 차트 업데이트 처리
   - 옵션 값 유효성 검사

5. 폼 요소:
   - 색상 선택기
   - 숫자 입력 (단위, 스텝 포함)
   - 선택 컴포넌트 (드롭다운)
   - 토글 스위치

6. 옵션 관계 및 의존성 관리:
   - 조건부 옵션 활성화/비활성화
   - 종속 옵션 자동 업데이트

7. 옵션 프리셋:
   - 빠른 옵션 적용을 위한 프리셋 시스템
   - 사용자 정의 프리셋 저장/로드

각 옵션 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 4.3 차트 에디터

```

E-Torch의 차트 에디터를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 차트 에디터 레이아웃:
   - packages/charts/src/editor/ChartEditor.tsx (메인 에디터 컴포넌트)
   - 에디터 레이아웃 구성 (Shadcn/UI Resizable 활용)
   - 상단 컨트롤 영역, 중앙 미리보기, 우측 옵션 패널, 하단 데이터 소스 영역

2. 차트 유형 선택:
   - packages/charts/src/editor/ChartTypeSelector.tsx
   - 시각적 차트 유형 선택 UI
   - 차트 유형 전환 시 데이터/옵션 호환성 관리

3. 차트 미리보기:
   - packages/charts/src/editor/ChartPreview.tsx
   - 실시간 미리보기 렌더링
   - 미리보기 크기 조절 옵션

4. 데이터 소스 패널:
   - packages/charts/src/editor/DataSourcePanel.tsx (데이터 소스 섹션)
   - packages/charts/src/editor/DataQueryCard.tsx (개별 쿼리 카드)
   - packages/data-sources/src/components/SourceSelector.tsx (소스 선택)
   - packages/data-sources/src/components/IndicatorSelector.tsx (지표 선택)
   - packages/data-sources/src/components/TransformControls.tsx (변환 옵션)

5. 차트 옵션 통합:
   - 차트 옵션 패널 통합 (4.2에서 구현한 컴포넌트 사용)
   - 옵션 변경 시 실시간 미리보기 업데이트

6. 저장 및 취소 로직:
   - packages/charts/src/editor/EditorControls.tsx
   - 차트 설정 저장 (서버 액션 연동)
   - 변경사항 폐기 및 경고 다이얼로그

7. URL 상태 관리:
   - URL 쿼리 파라미터를 통한 에디터 상태 유지
   - 새로고침 후에도 상태 복원

8. 에디터 상태 관리:
   - packages/state/src/stores/chart-editor.ts (차트 에디터 스토어)
   - Undo/Redo 기능 구현
   - 임시 저장 기능

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 4.4 차트 데이터 최적화

```

E-Torch 차트 데이터 최적화 기능을 구현해주세요. 다음 요소를 포함해야 합니다:

1. LTTB 다운샘플링 알고리즘:
   - packages/data-sources/src/optimization/lttb.ts
   - Largest-Triangle-Three-Buckets 알고리즘 구현
   - 시각적 특성 보존하면서 데이터 포인트 수 감소

2. M4 다운샘플링 알고리즘:
   - packages/data-sources/src/optimization/m4.ts
   - 구간별 최대, 최소, 첫값, 마지막값 유지
   - 트렌드와 이상값 보존 특화

3. 리샘플링 알고리즘:
   - packages/data-sources/src/optimization/resampling.ts
   - 균등 간격 데이터 포인트 생성
   - 보간법을 활용한 값 계산

4. 메모이제이션 최적화:
   - 차트 컴포넌트 내 useMemo, useCallback 활용
   - 핵심 계산 캐싱
   - 렌더링 최적화

5. 점진적 로딩:
   - packages/charts/src/components/ProgressiveChart.tsx
   - 낮은 해상도 데이터 먼저 표시
   - 고해상도 데이터 비동기 로드

6. 가상화 기법:
   - 대용량 데이터셋의 효율적 렌더링
   - 가시 영역 기반 데이터 필터링

7. 차트 렌더링 최적화:
   - React.memo를 활용한 컴포넌트 메모이제이션
   - 불필요한 리렌더링 방지
   - 상태 업데이트 일괄 처리

각 최적화 알고리즘과 전략의 전체 구현 코드를 제공해주세요.

```

## 5. 대시보드 시스템

### 5.1 대시보드 목록 페이지

```

E-Torch의 대시보드 목록 페이지를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 목록 페이지:
   - apps/web/app/(dashboard)/dashboard/page.tsx (서버 컴포넌트)
   - packages/dashboard/src/components/DashboardList.tsx (클라이언트 컴포넌트)

2. 대시보드 카드:
   - packages/dashboard/src/components/DashboardCard.tsx
   - 타이틀, 설명, 썸네일, 최종 업데이트 정보 표시
   - 상호작용 및 컨텍스트 메뉴

3. 검색 및 필터링:
   - packages/dashboard/src/components/DashboardSearch.tsx
   - 제목 기반 검색
   - 태그 필터링
   - 생성일/수정일 필터링

4. 정렬 옵션:
   - packages/dashboard/src/components/DashboardSort.tsx
   - 최근 생성순
   - 최근 수정순
   - 이름순
   - 사용 빈도순

5. 새 대시보드 생성 버튼:
   - packages/dashboard/src/components/NewDashboardButton.tsx
   - 새 대시보드 생성 페이지로 이동

6. 데이터 페칭:
   - packages/dashboard/src/server/fetchDashboards.ts
   - Supabase 쿼리를 통한 대시보드 목록 조회
   - 필터링 및 정렬 적용

7. 페이지네이션:
   - packages/dashboard/src/components/DashboardPagination.tsx
   - 대시보드 목록 페이지네이션
   - 페이지 크기 조절

8. 상태 관리 통합:
   - packages/state/src/hooks/useDashboards.ts
   - Tanstack Query를 활용한 데이터 관리
   - 검색 및 필터 상태 관리

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 5.2 대시보드 상세 페이지

```

E-Torch의 대시보드 상세 페이지를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 상세 페이지:
   - apps/web/app/(dashboard)/dashboard/[id]/page.tsx (서버 컴포넌트)
   - packages/dashboard/src/server/DashboardServerWrapper.tsx (서버 래퍼)
   - packages/dashboard/src/components/DashboardDetail.tsx (클라이언트 컴포넌트)

2. 대시보드 헤더:
   - packages/dashboard/src/components/DashboardHeader.tsx
   - 대시보드 제목 및 설명
   - 편집, 공유, 내보내기 버튼
   - 시간 범위 및 주기 선택

3. 시간 범위 제어:
   - packages/dashboard/src/components/TimeRangeControl.tsx
   - 시간 범위 선택 (최근 1년, 최근 3년, 커스텀 범위 등)
   - 날짜 범위 선택기

4. 주기 선택:
   - packages/dashboard/src/components/PeriodSelector.tsx
   - 일간(D), 월간(M), 분기(Q), 연간(A) 주기 선택
   - 주기 변경 시 데이터 리로드

5. 새로고침 기능:
   - packages/dashboard/src/components/RefreshControl.tsx
   - 수동 새로고침 버튼
   - 자동 새로고침 간격 설정

6. 차트 렌더링:
   - 대시보드 그리드 내 차트 렌더링
   - 차트 컴포넌트 통합
   - 모든 차트의 시간 범위 및 주기 동기화

7. 대시보드 상세 데이터 페칭:
   - packages/dashboard/src/server/fetchDashboardById.ts
   - 대시보드 메타데이터 및 레이아웃 로드
   - 위젯 데이터 로드

8. 상태 관리 통합:
   - packages/state/src/hooks/useDashboard.ts
   - 대시보드 상태 관리
   - 시간 범위 및 주기 상태 관리

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 5.3 대시보드 그리드 시스템

```

E-Torch의 대시보드 그리드 시스템을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 그리드 컴포넌트:
   - packages/dashboard/src/components/DashboardGrid.tsx
   - react-grid-layout 기반 구현
   - 반응형 그리드 지원
   - 편집 모드와 조회 모드 전환

2. 그리드 아이템 컴포넌트:
   - packages/dashboard/src/components/GridItem.tsx
   - 그리드 내 개별 위젯 컨테이너
   - 위젯 헤더, 리사이즈 핸들, 제어 버튼

3. 위젯 컴포넌트:
   - packages/dashboard/src/components/widgets/ChartWidget.tsx (차트 위젯)
   - packages/dashboard/src/components/widgets/TextWidget.tsx (텍스트 위젯)
   - packages/dashboard/src/components/widgets/KpiWidget.tsx (KPI 위젯)

4. 레이아웃 관리:
   - packages/dashboard/src/utils/layoutUtils.ts
   - 레이아웃 변환 및 유효성 검사
   - 그리드 위치 계산

5. 드래그 앤 드롭 기능:
   - 위젯 이동 기능
   - 그리드 스냅 설정
   - 충돌 방지 로직

6. 리사이징 기능:
   - 위젯 크기 조절
   - 최소/최대 크기 제한
   - 비율 유지 옵션

7. 레이아웃 저장:
   - packages/dashboard/src/utils/saveLayout.ts
   - 레이아웃 변경 사항 저장
   - 낙관적 업데이트 및 에러 롤백

8. 스타일링 및 테마 통합:
   - 그리드 간격 및 여백 설정
   - 드래그 중 시각적 피드백
   - 다크/라이트 모드 지원

react-grid-layout 설정 및 전체 구현 코드를 제공해주세요.

```

### 5.4 대시보드 편집기

```

E-Torch의 대시보드 편집기를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 편집기 페이지:
   - apps/web/app/(dashboard)/dashboard/[id]/edit/page.tsx (서버 컴포넌트)
   - apps/web/app/(dashboard)/dashboard/new/page.tsx (새 대시보드 생성)
   - packages/dashboard/src/components/DashboardEditor.tsx (클라이언트 컴포넌트)

2. 편집 툴바:
   - packages/dashboard/src/components/editor/EditorToolbar.tsx
   - 위젯 추가, 배열, 이동, 저장 버튼
   - Undo/Redo 버튼

3. 위젯 추가 기능:
   - packages/dashboard/src/components/editor/WidgetSelector.tsx
   - 차트, 텍스트, KPI 등 다양한 위젯 유형 제공
   - 드래그 앤 드롭으로 대시보드에 추가

4. 대시보드 설정:
   - packages/dashboard/src/components/editor/DashboardSettings.tsx
   - 대시보드 제목, 설명, 태그 설정
   - 공개/비공개 설정

5. 레이아웃 제어:
   - packages/dashboard/src/components/editor/LayoutControls.tsx
   - 그리드 정렬
   - 위젯 간격 조절
   - 레이아웃 잠금/해제

6. Undo/Redo 시스템:
   - packages/state/src/stores/dashboard-editor.ts
   - 커맨드 패턴 기반 구현
   - 히스토리 관리
   - 트랜잭션 지원

7. 위젯 설정 패널:
   - packages/dashboard/src/components/editor/WidgetSettings.tsx
   - 선택한 위젯의 속성 편집
   - 위젯 유형별 설정 옵션

8. 저장 및 발행 기능:
   - packages/dashboard/src/server-actions/saveDashboard.ts
   - 서버 액션을 통한 대시보드 저장
   - 임시 저장 및 최종 발행

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 5.5 대시보드 공유 기능

```

E-Torch의 대시보드 공유 기능을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 공유 컴포넌트:
   - packages/dashboard/src/components/sharing/DashboardSharing.tsx
   - 공유 설정 모달
   - 공개/비공개 전환

2. 공유 링크 생성:
   - packages/dashboard/src/components/sharing/ShareLink.tsx
   - 고유 공유 링크 생성
   - 링크 복사 기능

3. 소셜 공유:
   - packages/dashboard/src/components/sharing/SocialShare.tsx
   - Twitter, Facebook, LinkedIn 등 소셜 미디어 공유 버튼
   - OG 메타데이터 설정

4. 접근 권한 관리:
   - packages/dashboard/src/components/sharing/AccessControl.tsx
   - 사용자별 권한 설정 (읽기, 댓글, 편집)
   - 이메일 초대 기능

5. 임베드 코드 생성:
   - packages/dashboard/src/components/sharing/EmbedCode.tsx
   - 웹사이트에 임베드 가능한 코드 생성
   - 크기 및 옵션 설정

6. 대시보드 구독:
   - packages/dashboard/src/components/sharing/SubscribeButton.tsx
   - 공유 대시보드 구독 기능
   - 업데이트 알림 설정

7. 공유 상태 표시:
   - packages/dashboard/src/components/DashboardStatusBadge.tsx
   - 공개/비공개 상태 표시
   - 구독자 수 표시

8. 공유 설정 저장:
   - packages/dashboard/src/server-actions/updateSharingSettings.ts
   - 서버 액션을 통한 공유 설정 저장
   - Supabase RLS 정책 연동

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

## 6. 사용자 관리

### 6.1 사용자 프로필 관리

```

E-Torch의 사용자 프로필 관리 페이지를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 프로필 설정 페이지:
   - apps/web/app/(profile)/profile/settings/page.tsx (서버 컴포넌트)
   - packages/ui/src/components/profile/ProfileSettings.tsx (클라이언트 컴포넌트)

2. 사용자 정보 폼:
   - packages/ui/src/components/profile/UserInfoForm.tsx
   - 표시 이름, 프로필 이미지, 소개 수정
   - React Hook Form을 활용한 유효성 검사

3. 계정 연결 관리:
   - packages/ui/src/components/profile/ConnectedAccounts.tsx
   - 연결된 SNS 계정 목록
   - 계정 연결/해제 기능

4. 테마 설정:
   - packages/ui/src/components/profile/ThemeSettings.tsx
   - 라이트/다크/시스템 테마 선택
   - 커스텀 색상 설정

5. 프로필 이미지 업로드:
   - packages/ui/src/components/profile/ProfileImageUploader.tsx
   - 이미지 업로드 및 크롭 기능
   - Supabase Storage 연동

6. 계정 삭제:
   - packages/ui/src/components/profile/DeleteAccount.tsx
   - 계정 삭제 확인 절차
   - 데이터 처리 옵션

7. 프로필 데이터 관리:
   - packages/server-api/src/profile/updateProfile.ts (서버 액션)
   - 프로필 정보 업데이트
   - 변경 내역 저장

8. 설정 레이아웃:
   - packages/ui/src/components/profile/SettingsLayout.tsx
   - 탭 기반 내비게이션
   - 반응형 레이아웃

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 6.2 구독 관리 시스템

```

E-Torch의 구독 관리 시스템을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 구독 관리 페이지:
   - apps/web/app/(profile)/profile/subscription/page.tsx (서버 컴포넌트)
   - packages/ui/src/components/subscription/SubscriptionManagement.tsx (클라이언트 컴포넌트)

2. 구독 정보 표시:
   - packages/ui/src/components/subscription/CurrentPlanInfo.tsx
   - 현재 구독 플랜, 상태, 다음 결제일, 이용료 표시
   - 구독 기간 및 갱신 정보

3. 구독 플랜 선택:
   - packages/ui/src/components/subscription/PlanSelector.tsx
   - 무료/월간/연간 플랜 카드
   - 플랜 비교 정보

4. 토스페이먼츠 결제 연동:
   - packages/ui/src/components/subscription/PaymentForm.tsx
   - 토스페이먼츠 결제 위젯 통합
   - 결제 프로세스 처리

5. 결제 내역:
   - packages/ui/src/components/subscription/PaymentHistory.tsx
   - 과거 결제 내역 표시
   - 인보이스 다운로드

6. 구독 취소:
   - packages/ui/src/components/subscription/CancelSubscription.tsx
   - 구독 취소 확인 및 처리
   - 환불 정책 안내

7. 결제 방법 관리:
   - packages/ui/src/components/subscription/PaymentMethods.tsx
   - 결제 카드 추가/변경/삭제
   - 기본 결제 수단 설정

8. 구독 API 통합:
   - packages/server-api/src/subscription/manageSubscription.ts
   - 구독 생성, 업데이트, 취소 처리
   - 토스페이먼츠 API 연동

9. 구독 상태 관리:
   - packages/state/src/hooks/useSubscription.ts
   - 현재 구독 상태 관리
   - 구독 변경 처리

각 컴포넌트의 전체 구현 코드와 토스페이먼츠 연동 방법을 제공해주세요.

```

### 6.3 알림 설정 페이지

```

E-Torch의 알림 설정 페이지를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 알림 설정 페이지:
   - apps/web/app/(profile)/profile/notifications/page.tsx (서버 컴포넌트)
   - packages/ui/src/components/notifications/NotificationSettings.tsx (클라이언트 컴포넌트)

2. 알림 유형 설정:
   - packages/ui/src/components/notifications/NotificationTypesForm.tsx
   - 대시보드 업데이트, 댓글, 멘션, 구독 콘텐츠 등 알림 유형
   - 유형별 활성화/비활성화 토글

3. 알림 채널 설정:
   - packages/ui/src/components/notifications/NotificationChannelsForm.tsx
   - 이메일, 웹 알림 등 채널별 설정
   - 채널별 활성화/비활성화 토글

4. 알림 주기 설정:
   - packages/ui/src/components/notifications/NotificationFrequencyForm.tsx
   - 즉시, 일간, 주간 등 알림 주기 선택
   - 다이제스트 이메일 설정

5. 알림 목록 조회:
   - packages/ui/src/components/notifications/NotificationsList.tsx
   - 받은 알림 목록 표시
   - 읽음/안 읽음 상태 관리

6. 알림 제어:
   - packages/ui/src/components/notifications/NotificationActions.tsx
   - 개별 알림 읽음 표시
   - 알림 삭제
   - 알림 전체 삭제

7. 알림 설정 저장:
   - packages/server-api/src/notifications/updateNotificationSettings.ts
   - 서버 액션을 통한 알림 설정 저장
   - 설정 변경 시 즉시 적용

8. 실시간 알림 수신:
   - packages/ui/src/components/notifications/NotificationListener.tsx
   - Supabase Realtime 연동
   - 실시간 알림 수신 및 표시

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

## 7. 고급 기능 및 최적화

### 7.1 대시보드 탐색 페이지

```

E-Torch의 대시보드 탐색 페이지를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 대시보드 탐색 페이지:
   - apps/web/app/(dashboard)/explore/page.tsx (서버 컴포넌트)
   - packages/dashboard/src/components/explore/ExploreView.tsx (클라이언트 컴포넌트)

2. 검색 및 필터링:
   - packages/dashboard/src/components/explore/ExploreSearch.tsx
   - 키워드 검색
   - 고급 필터링 옵션

3. 카테고리 필터:
   - packages/dashboard/src/components/explore/CategoryFilter.tsx
   - 대시보드 카테고리별 필터링
   - 다중 선택 지원

4. 정렬 옵션:
   - packages/dashboard/src/components/explore/SortOptions.tsx
   - 인기순, 최신순, 구독자순 등 정렬
   - 정렬 기준 변경

5. 대시보드 카드 목록:
   - packages/dashboard/src/components/explore/ExploreList.tsx
   - 탐색 결과 리스트 표시
   - 무한 스크롤 또는 페이지네이션

6. 대시보드 미리보기:
   - packages/dashboard/src/components/explore/DashboardPreview.tsx
   - 대시보드 상세 미리보기
   - 주요 차트 및 정보 표시

7. 구독 기능:
   - packages/dashboard/src/components/explore/SubscribeButton.tsx
   - 대시보드 구독 버튼
   - 구독 상태 표시

8. 탐색 데이터 페칭:
   - packages/dashboard/src/server/fetchExploreDashboards.ts
   - 필터링 및 정렬이 적용된 공개 대시보드 조회
   - 인기 대시보드 및 추천 대시보드 조회

9. 상태 관리:
   - packages/state/src/hooks/useExploreDashboards.ts
   - 탐색 상태 관리
   - 필터 및 검색 상태 유지

각 컴포넌트의 전체 구현 코드를 제공해주세요.

```

### 7.2 데이터 내보내기 기능

```

E-Torch의 데이터 내보내기 기능을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 차트 내보내기 기능:
   - packages/charts/src/components/export/ChartExport.tsx
   - 차트를 PNG, SVG, CSV 형식으로 내보내기
   - 워터마크 적용 옵션

2. PNG 내보내기:
   - packages/charts/src/utils/export/exportToPng.ts
   - 차트를 PNG 이미지로 변환
   - 해상도 및 크기 옵션

3. SVG 내보내기:
   - packages/charts/src/utils/export/exportToSvg.ts
   - 차트 SVG 추출 및 처리
   - 스타일 인라인화

4. CSV 내보내기:
   - packages/charts/src/utils/export/exportToCsv.ts
   - 차트 데이터를 CSV로 변환
   - 데이터 포맷팅 옵션

5. 대시보드 내보내기:
   - packages/dashboard/src/components/export/DashboardExport.tsx
   - 전체 대시보드를 PDF 또는 이미지로 내보내기
   - 모든 차트 포함

6. PDF 내보내기:
   - packages/dashboard/src/utils/export/exportToPdf.ts
   - 대시보드를 PDF 문서로 변환
   - 페이지 설정 및 레이아웃 옵션

7. 워터마크 처리:
   - packages/ui/src/components/export/Watermark.tsx
   - E-Torch 워터마크 추가
   - 워터마크 위치 및 투명도 설정

8. 내보내기 컨트롤:
   - packages/ui/src/components/export/ExportControls.tsx
   - 내보내기 형식 선택
   - 옵션 설정 UI

각 기능의 전체 구현 코드를 제공해주세요.

```

### 7.3 실시간 데이터 업데이트

```

E-Torch의 실시간 데이터 업데이트 기능을 구현해주세요. 다음 요소를 포함해야 합니다:

1. 주기적 폴링 시스템:
   - packages/data-sources/src/polling/usePolling.ts
   - 설정된 간격으로 데이터 자동 갱신
   - 폴링 상태 및 제어 기능

2. 자동 새로고침 설정:
   - packages/dashboard/src/components/AutoRefreshSettings.tsx
   - 새로고침 간격 설정 UI
   - 활성화/비활성화 토글

3. Supabase Realtime 연동:
   - packages/data-sources/src/realtime/useRealtimeData.ts
   - Supabase 테이블 변경 이벤트 구독
   - 실시간 데이터 업데이트 처리

4. 대시보드 협업 기능:
   - packages/dashboard/src/components/collaboration/CollaborationPresence.tsx
   - 현재 대시보드를 보고 있는 사용자 표시
   - 실시간 사용자 상태 업데이트

5. 실시간 알림:
   - packages/ui/src/components/notifications/RealtimeNotifications.tsx
   - 데이터 변경 또는 이벤트 발생 시 실시간 알림
   - 알림 표시 및 관리

6. 실시간 댓글 시스템:
   - packages/dashboard/src/components/comments/CommentSection.tsx
   - 대시보드에 대한 실시간 댓글 및 피드백
   - 댓글 작성, 수정, 삭제 기능

7. 가시성 기반 업데이트:
   - packages/ui/src/hooks/useVisibilityControl.ts
   - 화면에 보이는 컴포넌트만 업데이트
   - IntersectionObserver 활용

8. 데이터 동기화 관리:
   - packages/state/src/sync/SyncManager.ts
   - 클라이언트-서버 데이터 동기화
   - 충돌 해결 전략

각 기능의 전체 구현 코드를 제공해주세요.

```

### 7.4 성능 최적화

```

E-Torch의 성능 최적화를 구현해주세요. 다음 요소를 포함해야 합니다:

1. 코드 분할 및 지연 로딩:
   - apps/web/app/layout.tsx 및 페이지 컴포넌트에 적용
   - dynamic import 활용
   - Next.js 내장 최적화 활용

2. 이미지 최적화:
   - Next.js Image 컴포넌트 활용
   - 이미지 포맷 및 크기 최적화
   - 레이지 로딩 구현

3. 메모이제이션 최적화:
   - packages/ui/src/utils/memoization-helpers.ts
   - React.memo, useMemo, useCallback 활용 가이드
   - 메모이제이션 훅 구현

4. 서버/클라이언트 컴포넌트 분리:
   - server-components.md의 분리 전략 구현
   - 서버 컴포넌트 최적화
   - 클라이언트 번들 크기 최소화

5. 상태 업데이트 최적화:
   - packages/state/src/utils/batch-updates.ts
   - 상태 업데이트 일괄 처리
   - 리렌더링 최소화 전략

6. 데이터 요청 최적화:
   - packages/data-sources/src/optimization/request-optimization.ts
   - 요청 일괄 처리
   - 데이터 프리페칭

7. 렌더링 성능:
   - 가상화 기법 (긴 목록)
   - 애니메이션 최적화
   - 레이아웃 스래싱 방지

8. 웹 바이탈 최적화:
   - 코어 웹 바이탈 측정 및 개선
   - LCP, FID, CLS 최적화
   - 퍼포먼스 모니터링

9. 빌드 최적화:
   - 번들 분석 및 최적화
   - 트리 쉐이킹 활용
   - 불필요한 의존성 제거

각 최적화 전략의 구현 코드와 성능 측정 방법을 제공해주세요.

```

## 8. 테스트 및 배포

### 8.1 테스트 환경 설정

```

E-Torch의 테스트 환경을 설정해주세요. 다음 요소를 포함해야 합니다:

1. Vitest 설정:
   - vitest.config.ts: Vitest 기본 설정
   - vitest-setup.ts: 전역 설정 및 모킹
   - packages/*/vitest.config.ts: 패키지별 설정

2. 단위 테스트 환경:
   - packages/charts/src/__tests__/unit/: 차트 컴포넌트 테스트
   - packages/dashboard/src/__tests__/unit/: 대시보드 컴포넌트 테스트
   - packages/data-sources/src/__tests__/unit/: 데이터 변환 테스트

3. 통합 테스트:
   - apps/web/test/integration/: 페이지 통합 테스트
   - 서버/클라이언트 컴포넌트 통합 테스트
   - 데이터 흐름 테스트

4. E2E 테스트 (Playwright):
   - playwright.config.ts: Playwright 설정
   - apps/web/e2e/: E2E 테스트 파일
   - 주요 사용자 흐름 테스트

5. 테스트 유틸리티:
   - packages/test-utils/src/: 테스트 헬퍼 함수
   - 테스트 데이터 생성기
   - 모킹 유틸리티

6. 테스트 데이터:
   - packages/test-utils/fixtures/: 테스트 데이터 파일
   - 모의 API 응답
   - 샘플 대시보드 데이터

7. 코드 커버리지 설정:
   - Vitest 커버리지 설정
   - 커버리지 임계값 설정
   - 커버리지 리포트 생성

8. 주요 컴포넌트 테스트 예제:
   - Chart 컴포넌트 테스트
   - 상태 관리 테스트
   - 데이터 변환 테스트

9. 모킹 전략:
   - MSW(Mock Service Worker) 설정
   - API 모킹
   - 브라우저 API 모킹

각 설정 파일과 테스트 코드 예제를 제공해주세요.

```

### 8.2 CI/CD 파이프라인

```

E-Torch의 CI/CD 파이프라인을 구성해주세요. 다음 요소를 포함해야 합니다:

1. GitHub Actions 워크플로우:
   - .github/workflows/ci.yml: CI 워크플로우
   - .github/workflows/cd.yml: CD 워크플로우
   - .github/workflows/preview.yml: PR 미리보기 워크플로우

2. CI 단계:
   - 의존성 설치 및 캐싱
   - 빌드 검증
   - 린트 및 타입 체크
   - 단위 테스트
   - 통합 테스트
   - E2E 테스트
   - 코드 커버리지 보고서

3. CD 단계:
   - 환경별 배포 (개발, 스테이징, 프로덕션)
   - 롤백 메커니즘
   - 배포 알림

4. Vercel 배포 설정:
   - vercel.json: Vercel 설정 파일
   - 환경별 프로젝트 설정
   - 도메인 설정

5. 환경 변수 관리:
   - GitHub Secrets 활용
   - 환경별 변수 분리
   - 민감 정보 관리

6. 배포 전략:
   - Atomic 배포
   - 블루/그린 배포
   - Canary 배포

7. 모니터링 통합:
   - 에러 트래킹 설정 (Sentry 등)
   - 성능 모니터링
   - 알림 설정

8. 자동화된 테스트:
   - PR 검증 자동화
   - 정기적인 테스트 실행
   - 회귀 테스트

각 설정 파일과 워크플로우 정의를 제공해주세요.

```

### 8.3 사용자 가이드 문서

```

E-Torch의 사용자 가이드 문서를 작성해주세요. 다음 요소를 포함해야 합니다:

1. 시작하기 가이드:
   - 서비스 개요 및 목적
   - 계정 생성 및 로그인
   - 사용자 인터페이스 소개

2. 대시보드 가이드:
   - 대시보드 목록 탐색
   - 대시보드 생성 및 편집
   - 위젯 추가 및 배치
   - 시간 범위 및 주기 설정

3. 차트 사용법:
   - 차트 유형별 특징 및 사용 사례
   - 차트 옵션 설정
   - 차트 데이터 변환
   - 데이터 시각화 모범 사례

4. 데이터 소스 가이드:
   - 지원되는 데이터 소스 (KOSIS, ECOS, OECD)
   - 데이터 조회 및 필터링
   - 데이터 변환 옵션
   - 데이터 해석 가이드

5. 공유 및 협업:
   - 대시보드 공유 방법
   - 권한 설정
   - 구독 기능 활용
   - 댓글 및 피드백

6. 계정 관리:
   - 프로필 설정
   - 구독 관리
   - 알림 설정
   - 보안 설정

7. 고급 기능:
   - 대시보드 템플릿 활용
   - 데이터 내보내기
   - API 연동 (고급 사용자용)
   - 사용자 정의 위젯

8. 문제 해결 가이드:
   - 일반적인 문제 및 해결 방법
   - 오류 메시지 설명
   - 지원 요청 방법
   - 자주 묻는 질문 (FAQ)

각 섹션에 대한 상세 내용과 스크린샷, 예제를 포함해주세요.

```

이 28개의 상세 프롬프트를 순서대로 활용하면 실수 없이 E-Torch 프로젝트를 체계적으로 구현할 수 있습니다. 각 프롬프트는 해당 작업을 완벽하게 수행하는 데 필요한 모든 정보와 파일 구조, 구현 세부사항을 포함하고 있습니다.
