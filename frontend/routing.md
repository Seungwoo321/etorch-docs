# E-Torch 라우팅 구조

## 1. 개요

E-Torch는 Next.js App Router를 활용하여 직관적이고 체계적인 라우팅 구조를 구현합니다. 이 문서는 E-Torch의 라우팅 아키텍처, 페이지 구성, 라우트 그룹, 레이아웃, 그리고 내비게이션 전략을 설명합니다.

## 2. Next.js 15 App Router 활용 전략

Next.js 15의 App Router 파일 시스템 기반 라우팅을 활용하여 다음과 같은 기능들을 구현합니다:

```mermaid
flowchart TD
    A[Next.js 15 App Router 활용] --> B[서버 컴포넌트 우선]
    A --> C[라우트 그룹]
    A --> D[중첩 레이아웃]
    A --> E[동적 라우트]
    A --> F[인터셉트 라우트]
    A --> G[병렬 라우트]
    
    B --> B1[SEO 최적화]
    B --> B2[초기 로딩 성능 향상]
    
    C --> C1[기능별 그룹화]
    C --> C2[코드 구조 명확화]
    
    D --> D1[일관된 UI 경험]
    D --> D2[중복 코드 제거]
    
    E --> E1[대시보드/차트 동적 ID]
    E --> E2[URL 파라미터 활용]
    
    F --> F1[모달/팝업 구현]
    F --> F2[전환 없는 데이터 로드]
    
    G --> G1[탭 인터페이스]
    G --> G2[동시 콘텐츠 로드]
```

## 3. 라우팅 구조 설계

E-Torch의 라우팅 구조는 `architecture.md`에 정의된 모노레포 패키지 구조와 통합되어 기능별로 그룹화됩니다:

```mermaid
graph TD
    Root[app/] --> Auth["(auth)/"]
    Root --> Dashboard["(dashboard)/"]
    Root --> Chart["(chart)/"]
    Root --> Profile["(profile)/"]
    Root --> API["api/"]
    Root --> Layout["layout.tsx"]
    Root --> Page["page.tsx"]
    
    Auth --> Auth1["login/"]
    Auth --> Auth2["callback/"]
    Auth --> AuthLayout["layout.tsx"]
    
    Dashboard --> Dash1["dashboard/"]
    Dashboard --> Dash2["dashboard/[id]/"]
    Dashboard --> Dash3["dashboard/new/"]
    Dashboard --> Dash4["dashboard/[id]/edit/"]
    Dashboard --> Dash5["explore/"]
    Dashboard --> DashLayout["layout.tsx"]
    
    Chart --> Chart1["chart-editor/[id]/"]
    Chart --> Chart2["chart/[id]/"]
    Chart --> ChartLayout["layout.tsx"]
    
    Profile --> Prof1["profile/settings/"]
    Profile --> Prof2["profile/subscription/"]
    Profile --> Prof3["profile/notifications/"]
    Profile --> ProfLayout["layout.tsx"]
    
    API --> API1["auth/"]
    API --> API2["charts/"]
    API --> API3["dashboards/"]
    API --> API4["data-sources/"]
```

## 4. 기본 및 확장 라우팅 구조

E-Torch의 라우팅 구조는 **기본 라우팅**과 **확장 라우팅**으로 구분됩니다. 이는 개발 단계와 기능 구현 우선순위에 따라 점진적으로 구현됩니다.

### 4.1 기본 라우팅 구조

기본 라우팅 구조는 제품의 핵심 기능을 제공하는 필수 경로로 구성됩니다. 이는 MVP(Minimum Viable Product) 단계에서 우선적으로 구현됩니다.

```
app/
├── (auth)/               # 인증 관련 라우트 그룹
│   ├── login/            # 로그인 페이지
│   ├── callback/         # 소셜 로그인 콜백 처리
│   └── layout.tsx        # 인증 레이아웃
│
├── (dashboard)/          # 대시보드 관련 라우트 그룹
│   ├── dashboard/        # 대시보드 목록 페이지
│   ├── dashboard/[id]/   # 개별 대시보드 상세 페이지
│   ├── dashboard/new/    # 새 대시보드 생성 페이지
│   ├── dashboard/[id]/edit/ # 대시보드 편집 페이지
│   ├── explore/          # 대시보드 탐색/발견 페이지
│   └── layout.tsx        # 대시보드 레이아웃
│
├── (chart)/              # 차트 관련 라우트 그룹
│   ├── chart-editor/[id]/ # 차트 에디터 페이지
│   ├── chart/[id]/       # 개별 차트 상세 페이지
│   └── layout.tsx        # 차트 레이아웃
│
├── (profile)/            # 사용자 프로필 관련 라우트 그룹
│   ├── profile/settings/ # 프로필 설정 페이지
│   ├── profile/subscription/ # 구독 관리 페이지
│   └── layout.tsx        # 프로필 레이아웃
│
├── layout.tsx            # 루트 레이아웃
└── page.tsx              # 홈페이지
```

### 4.2 확장 라우팅 구조

확장 라우팅 구조는 향상된 사용자 경험을 위한 고급 라우팅 패턴을 포함합니다. 이는 기본 기능 구현 후 점진적으로 추가됩니다.

```
# 기본 라우팅.0 구조에 다음과 같은 확장 패턴 추가

app/
├── @modal/               # 인터셉트 라우트 (모달용)
│   ├── dashboard/[id]/   # 대시보드 모달 표시
│   └── chart/[id]/       # 차트 모달 표시
│
├── (dashboard)/          
│   └── @dashboard/       # 병렬 라우트 (대시보드 부가 정보용)
│       ├── info/         # 대시보드 정보 탭
│       └── share/        # 대시보드 공유 탭
│
├── (chart)/              
│   └── preview/          # 차트 미리보기 페이지
│
└── (profile)/             
    └── notifications/    # 알림 설정 페이지
```

### 4.3 라우팅 유형 매핑 테이블

| 경로 | 기본/확장 | 라우팅 패턴 | 구현 우선순위 |
|-----|-----------|-----------|-------------|
| `/login`, `/callback` | 기본 | 일반 라우트 | 상 (MVP) |
| `/dashboard`, `/dashboard/[id]` | 기본 | 일반 라우트 | 상 (MVP) |
| `/dashboard/new`, `/dashboard/[id]/edit` | 기본 | 일반 라우트 | 상 (MVP) |
| `/explore` | 기본 | 일반 라우트 | 상 (MVP) |
| `/chart-editor/[id]`, `/chart/[id]` | 기본 | 일반 라우트 | 상 (MVP) |
| `/profile/*` | 기본 | 일반 라우트 | 중 |
| `@modal/dashboard/[id]` | 확장 | 인터셉트 라우트 | 중 |
| `@modal/chart/[id]` | 확장 | 인터셉트 라우트 | 중 |
| `@dashboard/info`, `@dashboard/share` | 확장 | 병렬 라우트 | 하 |
| `/chart/preview` | 확장 | 일반 라우트 | 하 |
| `/profile/notifications` | 확장 | 일반 라우트 | 하 |

## 5. 페이지별 라우트 설계

### 5.1 인증 관련 페이지

| 라우트 | 설명 | 권한 | 컴포넌트 타입 |
|-------|------|------|--------------|
| `/login` | 로그인 페이지 | Public | 서버 + 클라이언트 폼 |
| `/callback` | OAuth 콜백 처리 | Public | 서버 컴포넌트 |

### 5.2 대시보드 관련 페이지

| 라우트 | 설명 | 권한 | 컴포넌트 타입 |
|-------|------|------|--------------|
| `/dashboard` | 대시보드 목록 | Authenticated | 서버 + 클라이언트 기능 |
| `/dashboard/[id]` | 대시보드 상세 조회 | Authenticated | 서버 + 클라이언트 차트 |
| `/dashboard/new` | 새 대시보드 생성 | Authenticated | 서버 + 클라이언트 에디터 |
| `/dashboard/[id]/edit` | 대시보드 편집 | Owner | 서버 + 클라이언트 에디터 |
| `/explore` | 공유 대시보드 탐색 | Authenticated | 서버 + 클라이언트 필터링 |

### 5.3 차트 관련 페이지

| 라우트 | 설명 | 권한 | 컴포넌트 타입 |
|-------|------|------|--------------|
| `/chart-editor/[id]` | 차트 생성/편집 | Authenticated | 서버 + 클라이언트 에디터 |
| `/chart/[id]` | 개별 차트 상세 조회 | Authenticated | 서버 + 클라이언트 차트 |

### 5.4 프로필 관련 페이지

| 라우트 | 설명 | 권한 | 컴포넌트 타입 |
|-------|------|------|--------------|
| `/profile/settings` | 사용자 설정 | Authenticated | 서버 + 클라이언트 폼 |
| `/profile/subscription` | 구독 관리 | Authenticated | 서버 + 클라이언트 결제 |
| `/profile/notifications` | 알림 설정 | Authenticated | 서버 + 클라이언트 토글 |

## 6. 레이아웃 구조

E-Torch는 계층적 레이아웃 구조를 사용하여 일관된 사용자 경험을 제공합니다:

```mermaid
flowchart TD
    Root[RootLayout] --> Auth[AuthLayout]
    Root --> Dashboard[DashboardLayout]
    Root --> Chart[ChartLayout]
    Root --> Profile[ProfileLayout]
    
    Auth --> Login[로그인 페이지]
    Auth --> Callback[콜백 페이지]
    
    Dashboard --> DashboardList[대시보드 목록]
    Dashboard --> DashboardDetail[대시보드 상세]
    Dashboard --> DashboardEdit[대시보드 편집]
    Dashboard --> Explore[탐색 페이지]
    
    Chart --> ChartEditor[차트 에디터]
    Chart --> ChartDetail[차트 상세]
    
    Profile --> Settings[설정 페이지]
    Profile --> Subscription[구독 페이지]
    Profile --> Notifications[알림 페이지]
```

### 6.1 레이아웃 책임 분리

각 레이아웃은 명확한 책임을 갖는 구조로 설계되어 있습니다:

| 레이아웃 | 책임 |
|---------|-----|
| **RootLayout** | 전역 CSS/폰트(Inter, JetBrains_Mono), 테마 제공자, 메타데이터, 디자인 시스템 CSS 변수 적용 |
| **AuthLayout** | 최소 디자인, 로고 및 설명, 중앙 정렬 컨테이너 |
| **DashboardLayout** | 사이드 내비게이션, 상단 헤더, 메인 콘텐츠 영역 |
| **ChartLayout** | 상단 헤더, 전체 화면 콘텐츠, 백 버튼 |
| **ProfileLayout** | 사이드 내비게이션, 상단 헤더, 메인 콘텐츠 영역 |

## 7. 동적 라우팅 전략

### 7.1 대시보드 및 차트 ID 라우팅 패턴

동적 ID 기반 라우팅은 다음과 같은 패턴으로 구현됩니다:

```tsx
// app/(dashboard)/dashboard/[id]/page.tsx (서버 컴포넌트)
import { fetchDashboardById } from '@/e-torch/server-api/dashboard';
import { notFound } from 'next/navigation';
import { DashboardServerWrapper } from '@/e-torch/dashboard/server';

interface DashboardPageProps {
  params: { id: string };
}

export default async function DashboardPage({ params }: DashboardPageProps) {
  // 서버에서 대시보드 데이터 페칭
  const dashboard = await fetchDashboardById(params.id);
  
  if (!dashboard) {
    return notFound();
  }
  
  // 서버 래퍼 컴포넌트로 데이터 전달하여 렌더링
  return <DashboardServerWrapper dashboardId={params.id} initialData={dashboard} />;
}
```

### 7.2 동적 라우트 접근 제어 패턴

```mermaid
flowchart TD
    A[요청] --> B{ID 유효성 검증}
    B -->|유효함| C{권한 검증}
    B -->|유효하지 않음| D[404 페이지]
    C -->|권한 있음| E[페이지 렌더링]
    C -->|권한 없음| F[403 페이지/리디렉션]
```

## 8. 네비게이션 및 라우트 보호

### 8.1 네비게이션 컴포넌트 구조

E-Torch의 네비게이션 시스템은 다음과 같은 주요 컴포넌트로 구성됩니다:

- **SideNavigation**: 주요 메뉴 항목 및 네비게이션 링크 제공 (`packages/ui/src/components/layout/side-navigation.tsx`)
- **HeaderNavigation**: 현재 페이지 제목, 사용자 메뉴, 검색 바 등 (`packages/ui/src/components/layout/header-navigation.tsx`)
- **BreadcrumbNavigation**: 현재 위치 및 상위 카테고리 표시 (`packages/ui/src/components/layout/breadcrumb-navigation.tsx`)

### 8.2 라우트 보호 아키텍처

라우트 보호는 다층적 접근으로 구현됩니다:

1. **미들웨어 보호**:
   - Supabase JWT 토큰 유효성 검증
   - 인증 필요 시 리다이렉션

```tsx
// middleware.ts
import { createMiddlewareClient } from '@supabase/auth-helpers-nextjs';

export async function middleware(request: NextRequest) {
  const pathname = request.nextUrl.pathname;
  
  // 공개 라우트는 통과
  if (publicRoutes.some(route => pathname.startsWith(route))) {
    return NextResponse.next();
  }
  
  // Supabase 클라이언트 초기화
  const res = NextResponse.next();
  const supabase = createMiddlewareClient({ req: request, res });
  
  // 세션 검증
  const { data: { session } } = await supabase.auth.getSession();
  
  if (!session) {
    const url = new URL('/login', request.url);
    url.searchParams.set('redirectTo', pathname);
    return NextResponse.redirect(url);
  }
  
  return res;
}
```

2. **서버 컴포넌트 보호**:
   - 세션 검증
   - 권한 검증
   - 리다이렉션/404 처리

3. **클라이언트 래퍼 보호**:
   - AuthGuard 컴포넌트
   - 세션 상태 검사
   - 로딩 상태 처리

## 9. 클라이언트 측 네비게이션 최적화

### 9.1 효율적인 네비게이션 패턴

```tsx
// 링크 컴포넌트 사용 예시 - design-system.md의 디자인 가이드라인 준수
import Link from 'next/link';

export function DashboardCard({ dashboard }) {
  return (
    <Link 
      href={`/dashboard/${dashboard.id}`}
      prefetch={true} // 자동 prefetch
      className="rounded-lg border bg-card shadow-sm hover:bg-muted/50 transition-colors"
    >
      <div className="p-4">
        <h3 className="text-xl font-semibold leading-snug">{dashboard.title}</h3>
        <p className="text-sm text-muted-foreground">{dashboard.description}</p>
      </div>
    </Link>
  );
}

// 프로그래매틱 네비게이션
import { useRouter } from 'next/navigation';

export function SaveButton({ dashboardId, onSave }) {
  const router = useRouter();
  
  const handleSave = async () => {
    const result = await onSave();
    if (result.success) {
      router.push(`/dashboard/${dashboardId}`);
    }
  };
  
  return (
    <button 
      onClick={handleSave} 
      className="inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors bg-primary text-primary-foreground hover:bg-primary/90 h-10 px-4 py-2"
    >
      저장
    </button>
  );
}
```

### 9.2 모달 라우팅 구조

모달 라우팅은 인터셉트 라우트를 사용하여 구현합니다:

```
app/
├── dashboard/[id]/
│   └── page.tsx         # 일반 대시보드 페이지
│
└── @modal/
    └── dashboard/[id]/
        └── page.tsx     # 모달로 표시되는 대시보드 페이지
```

```tsx
// app/@modal/dashboard/[id]/page.tsx
import { fetchDashboardById } from '@/e-torch/server-api/dashboard';
import { DashboardModalContent } from '@/e-torch/dashboard/components/dashboard-modal-content';

export default async function DashboardModal({ params }) {
  const dashboard = await fetchDashboardById(params.id);
  
  return (
    <div className="fixed inset-0 z-50 bg-background/80 backdrop-blur-sm">
      <div className="fixed left-1/2 top-1/2 z-50 max-h-[85vh] w-[90%] max-w-3xl -translate-x-1/2 -translate-y-1/2 rounded-lg border bg-card p-6 shadow-lg">
        <h2 className="text-xl font-semibold leading-snug">{dashboard.title}</h2>
        <DashboardModalContent dashboard={dashboard} />
      </div>
    </div>
  );
}
```

## 10. 메타데이터 전략

### 10.1 메타데이터 계층 구조

```tsx
// app/layout.tsx (기본 메타데이터)
import { Inter, JetBrains_Mono } from 'next/font/google';
import '@e-torch/ui/styles/globals.css';

const inter = Inter({ 
  subsets: ['latin'],
  variable: '--font-inter'
});

const jetBrainsMono = JetBrains_Mono({
  subsets: ['latin'],
  variable: '--font-jetbrains-mono'
});

export const metadata: Metadata = {
  title: {
    template: '%s | E-Torch',
    default: 'E-Torch - 경제지표 대시보드 서비스',
  },
  description: '다양한 출처의 경제지표 데이터를 시각화하는 대시보드 서비스',
  // ... 기타 메타데이터
};

export default function RootLayout({ children }) {
  return (
    <html lang="ko" className={`${inter.variable} ${jetBrainsMono.variable}`}>
      <body>
        {children}
      </body>
    </html>
  );
}

// app/(dashboard)/layout.tsx (섹션 메타데이터)
export const metadata: Metadata = {
  title: 'Dashboard',
  description: '대시보드를 관리하고 시각화하세요',
};

// app/(dashboard)/dashboard/[id]/page.tsx (동적 메타데이터)
export async function generateMetadata({ params }: { params: { id: string } }): Promise<Metadata> {
  const dashboard = await fetchDashboardById(params.id);
  
  if (!dashboard) {
    return {
      title: 'Dashboard Not Found',
    };
  }
  
  return {
    title: dashboard.title,
    description: dashboard.description || '대시보드 상세 정보',
    openGraph: {
      images: [dashboard.thumbnail || '/images/default-dashboard.png'],
    },
  };
}
```

## 11. 서버 액션 활용 전략

Next.js 서버 액션을 활용하여 클라이언트-서버 통신을 간소화합니다. 이 섹션에서는 서버 액션의 라우팅 관점에서의 활용에 초점을 맞춥니다. 컴포넌트 통합 관점의 서버 액션 패턴은 [`core-components.md`](./components/core-components.md) 문서를 참조하십시오.

### 11.1 서버 액션 워크플로우

```mermaid
flowchart LR
    subgraph "클라이언트"
        A[폼 컴포넌트] --> B[서버 액션 호출]
        F[결과 처리] --> G[UI 업데이트]
    end
    
    subgraph "서버"
        C[서버 액션] --> D[데이터 처리]
        D --> E[캐시 무효화/리디렉션]
    end
    
    B --> C
    E --> F
```

### 11.2 라우팅 관련 서버 액션 패턴

- 폼 제출 처리: 사용자 입력 검증 및 데이터베이스 저장 (`/dashboard/new`, `/dashboard/edit`)
- 캐시 무효화: 관련 페이지의 캐시 자동 무효화 (`revalidatePath`)
- 리디렉션: 액션 완료 후 적절한 페이지로 이동 (`redirect`)
- 데이터 프리페칭: 페이지 전환 전 데이터 미리 로드 (`prefetch`)

```tsx
// app/actions/dashboard.ts
'use server';

import { revalidatePath } from 'next/cache';
import { saveDashboard } from '@/e-torch/server-api/dashboard';
import { getCurrentUser } from '@/e-torch/server-api/auth';
import { redirect } from 'next/navigation';

export async function saveDashboardAction(
  formData: FormData | Record<string, any>
) {
  // 현재 사용자 가져오기
  const user = await getCurrentUser();
  if (!user) {
    return { success: false, error: '인증되지 않은 사용자' };
  }
  
  try {
    // FormData를 객체로 변환 (필요시)
    const dashboardData = formData instanceof FormData 
      ? Object.fromEntries(formData.entries())
      : formData;
    
    // 소유자 정보 추가
    dashboardData.userId = user.id;
    dashboardData.updatedAt = new Date().toISOString();
    
    // 저장 처리
    const result = await saveDashboard(dashboardData);
    
    // 캐시 무효화
    revalidatePath(`/dashboard/${result.id}`);
    revalidatePath('/dashboard');
    
    return { success: true, data: result };
  } catch (error) {
    return { 
      success: false, 
      error: error instanceof Error ? error.message : '저장 실패'
    };
  }
}
```

```tsx
// packages/dashboard/components/save-button.tsx
'use client';

import { useActionMutation } from '@/e-torch/state';
import { saveDashboardAction } from '@/app/actions/dashboard';
import { Button } from '@/e-torch/ui';
import { queryKeys } from '@/e-torch/state/query-keys';

export function SaveButton({ dashboardId, formData }) {
  const { mutate, isPending } = useActionMutation({
    action: saveDashboardAction,
    invalidateQueries: [
      { queryKey: queryKeys.dashboards.detail(dashboardId) },
      { queryKey: queryKeys.dashboards.lists() }
    ],
    onSuccess: () => {
      toast({
        title: "저장 완료",
        description: "대시보드가 성공적으로 저장되었습니다.",
        variant: "default",
      });
    },
    onError: (error) => {
      toast({
        title: "저장 실패",
        description: error.message,
        variant: "destructive",
      });
    }
  });
  
  return (
    <Button 
      onClick={() => mutate(formData)}
      disabled={isPending}
    >
      {isPending ? '저장 중...' : '저장'}
    </Button>
  );
}
```

## 12. 결론

E-Torch의 라우팅 구조는 Next.js 15 App Router의 최신 기능을 활용하여 사용자 중심의 직관적인 인터페이스를 제공합니다. 주요 특징은 다음과 같습니다:

- **계층적 레이아웃**: 페이지 간 일관된 사용자 경험 제공
- **기능별 라우트 그룹화**: 코드 구조의 명확한 조직화
- **서버/클라이언트 분리**: 성능과 사용자 경험 최적화
- **동적 라우팅**: 대시보드와 차트에 대한 유연한 접근
- **안전한 라우트 보호**: 다층적 인증 및 권한 검증
- **메타데이터 최적화**: SEO 및 소셜 공유 최적화
- **서버 액션**: 클라이언트-서버 통신 간소화
- **디자인 시스템 통합**: OKLCH 색상 체계와 Tailwind CSS 4 클래스 활용

이 구조는 E-Torch의 복잡한 기능을 직관적으로 접근 가능하게 만들며, 향후 기능 추가 시에도 확장 가능한 견고한 기반을 제공합니다.
