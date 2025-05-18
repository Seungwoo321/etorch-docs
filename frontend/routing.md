# E-Torch 라우팅 구조

## 1. 개요

E-Torch는 Next.js App Router를 활용하여 직관적이고 체계적인 라우팅 구조를 구현합니다. 이 문서는 E-Torch의 라우팅 아키텍처, 페이지 구성, 라우트 그룹, 레이아웃, 그리고 내비게이션 전략을 설명합니다.

## 2. Next.js App Router 활용 전략

Next.js App Router의 파일 시스템 기반 라우팅을 활용하여 다음과 같은 기능들을 구현합니다:

```mermaid
flowchart TD
    A[Next.js App Router 활용] --> B[서버 컴포넌트 우선]
    A --> C[라우트 그룹]
    A --> D[중첩 레이아웃]
    A --> E[동적 라우트]
    A --> F[파라렐 라우트]
    A --> G[인터셉트 라우트]
    A --> H[라우트 핸들러]
    
    B --> B1[SEO 최적화]
    B --> B2[초기 로딩 성능 향상]
    
    C --> C1[기능별 그룹화]
    C --> C2[코드 구조 명확화]
    
    D --> D1[일관된 UI 경험]
    D --> D2[중복 코드 제거]
    
    E --> E1[대시보드/차트 동적 ID]
    E --> E2[URL 파라미터 활용]
    
    F --> F1[동시 여러 컨텐츠 표시]
    F --> F2[독립적 상태 관리]
    
    G --> G1[모달/팝업 구현]
    G --> G2[전환 없는 데이터 로드]
    
    H --> H1[API 엔드포인트]
    H --> H2[백엔드 통신]
```

## 3. 라우팅 구조 설계

E-Torch의 라우팅 구조는 기능별로 그룹화되며, 다음과 같은 주요 섹션으로 구성됩니다:

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

```mermaid
graph TD
    Root[app/] --> Auth["(auth)/"]
    Root --> Dashboard["(dashboard)/"]
    Root --> Chart["(chart)/"]
    Root --> Profile["(profile)/"]
    
    Auth --> Auth1["login/"]
    Auth --> Auth2["callback/"]
    
    Dashboard --> Dash1["dashboard/"]
    Dashboard --> Dash2["dashboard/[id]/"]
    Dashboard --> Dash3["dashboard/new/"]
    Dashboard --> Dash4["dashboard/[id]/edit/"]
    Dashboard --> Dash5["explore/"]
    
    Chart --> Chart1["chart-editor/[id]/"]
    Chart --> Chart2["chart/[id]/"]
    
    Profile --> Prof1["profile/settings/"]
    Profile --> Prof2["profile/subscription/"]
```

### 4.2 확장 라우팅 구조

확장 라우팅 구조는 향상된 사용자 경험을 위한 고급 라우팅 패턴을 포함합니다. 이는 기본 기능 구현 후 점진적으로 추가됩니다.

```mermaid
graph TD
    Root[app/] --> Auth["(auth)/"]
    Root --> Dashboard["(dashboard)/"]
    Root --> Chart["(chart)/"]
    Root --> Profile["(profile)/"]
    Root --> Modal["@modal/"]
    
    Auth --> Auth1["login/"]
    Auth --> Auth2["callback/"]
    
    Dashboard --> Dash1["dashboard/"]
    Dashboard --> Dash2["dashboard/[id]/"]
    Dashboard --> Dash3["new/"]
    Dashboard --> Dash4["dashboard/[id]/edit/"]
    Dashboard --> Dash5["explore/"]
    Dashboard --> Para["@dashboard/"]
    
    Modal --> Modal1["dashboard/[id]/"]
    Modal --> Modal2["chart/[id]/"]
    
    Para --> Para1["info/"]
    Para --> Para2["share/"]
    
    Chart --> Chart1["chart-editor/[id]/"]
    Chart --> Chart2["chart/[id]/"]
    Chart --> Chart3["preview/"]
    
    Profile --> Prof1["profile/settings/"]
    Profile --> Prof2["profile/subscription/"]
    Profile --> Prof3["notifications/"]
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

이 구조를 통해 E-Torch는 핵심 기능을 우선적으로 구현하면서도, 향후 사용자 경험을 향상시키기 위한 고급 라우팅 기능을 점진적으로 도입할 수 있습니다.

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
    
    classDef layout fill:#ccffcc,stroke:#333,stroke-width:1px
    classDef page fill:#ffcccb,stroke:#333,stroke-width:1px
    
    class Root,Auth,Dashboard,Chart,Profile layout
    class Login,Callback,DashboardList,DashboardDetail,DashboardEdit,Explore,ChartEditor,ChartDetail,Settings,Subscription,Notifications page
```

### 6.1 레이아웃 책임 분리

각 레이아웃은 명확한 책임을 갖는 구조로 설계되어 있습니다:

```mermaid
flowchart TD
    subgraph "RootLayout"
        R1[전역 CSS/폰트]
        R2[서비스 프로바이더]
        R3[테마 설정]
        R4[기본 메타데이터]
    end
    
    subgraph "AuthLayout"
        A1[최소 디자인]
        A2[로고 및 설명]
        A3[중앙 정렬 컨테이너]
    end
    
    subgraph "DashboardLayout"
        D1[사이드 내비게이션]
        D2[상단 헤더]
        D3[메인 콘텐츠 영역]
    end
    
    subgraph "ChartLayout"
        C1[상단 헤더]
        C2[전체 화면 콘텐츠]
        C3[백 버튼]
    end
    
    subgraph "ProfileLayout"
        P1[사이드 내비게이션]
        P2[상단 헤더]
        P3[메인 콘텐츠 영역]
    end
```

## 7. 동적 라우팅 전략

### 7.1 대시보드 및 차트 ID 라우팅 패턴

```mermaid
flowchart LR
    subgraph "동적 라우트 처리"
        direction TB
        A["/dashboard/[id]"] --> B["params.id 추출"]
        B --> C["서버에서 데이터 페칭"]
        C --> D["메타데이터 생성"]
        C --> E["클라이언트 컴포넌트로 데이터 전달"]
    end
    
    subgraph "정적 생성 최적화"
        direction TB
        F["generateStaticParams"] --> G["빌드 시 유효한 ID 생성"]
        G --> H["데이터베이스 조회"]
        H --> I["정적 경로 생성"]
    end
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

```mermaid
flowchart TD
    subgraph "네비게이션 시스템"
        A[MainSidebar] --> A1[대시보드 섹션]
        A --> A2[탐색 섹션]
        A --> A3[설정 섹션]
        
        B[HeaderNav] --> B1[서비스 로고]
        B --> B2[현재 페이지 제목]
        B --> B3[사용자 메뉴]
        B --> B4[검색 바]
        
        C[Breadcrumbs] --> C1[현재 경로]
        C --> C2[상위 카테고리 링크]
    end
```

### 8.2 라우트 보호 아키텍처

```mermaid
flowchart TD
    subgraph "라우트 보호 계층"
        A[미들웨어 보호] --> A1[요청 경로 검사]
        A --> A2[토큰 유효성 검증]
        A --> A3[인증 필요 시 리디렉션]
        
        B[서버 컴포넌트 보호] --> B1[세션 검증]
        B --> B2[권한 검증]
        B --> B3[리디렉션/404 처리]
        
        C[클라이언트 래퍼 보호] --> C1[AuthGuard 컴포넌트]
        C --> C2[세션 상태 검사]
        C --> C3[로딩 상태 처리]
    end
    
    A -->|인증 확인| B
    B -->|렌더링| C
```

## 9. 클라이언트 측 네비게이션 최적화

### 9.1 효율적인 네비게이션 패턴

```mermaid
flowchart LR
    subgraph "Link 컴포넌트 전략"
        A[Link prefetch] --> A1[자동 프리페칭]
        A --> A2[데이터 미리 로딩]
    end
    
    subgraph "프로그래매틱 네비게이션"
        B[useRouter 활용] --> B1[폼 제출 후 라우팅]
        B --> B2[조건부 리디렉션]
        B --> B3[히스토리 관리]
    end
    
    subgraph "인터셉트 라우트"
        C[모달 구현] --> C1["@modal 디렉토리"]
        C --> C2[페이지 전환 없는 오버레이]
        C --> C3[URL 공유 가능성 유지]
    end
```

### 9.2 모달 라우팅 구조

```mermaid
flowchart TD
    A["/dashboard"] --> A1["page.tsx: 대시보드 목록"]
    A --> B["[id]/"]
    A --> C["@modal/"]
    
    B --> B1["page.tsx: 대시보드 상세"]
    C --> C1["[id]/"]
    C1 --> C2["page.tsx: 모달 형태의 대시보드 상세"]
    
    A1 -.-> |"인터셉트"| C2
    B1 -.-> |"대체"| C2
```

## 10. 메타데이터 전략

### 10.1 메타데이터 계층 구조

```mermaid
flowchart TD
    subgraph "메타데이터 계층"
        A[기본 메타데이터] --> A1[사이트명 및 설명]
        A --> A2[기본 OG 이미지]
        A --> A3[파비콘 설정]
        
        B[레이아웃 메타데이터] --> B1[섹션별 제목]
        B --> B2[섹션 설명]
        
        C[페이지 메타데이터] --> C1[동적 생성 제목]
        C --> C2[페이지별 설명]
        C --> C3[OG 이미지 커스터마이징]
    end
    
    A -->|"상속"| B -->|"상속"| C
```

### 10.2 동적 메타데이터 생성 전략

```mermaid
flowchart LR
    subgraph "정적 메타데이터"
        A[export const metadata]
    end
    
    subgraph "동적 메타데이터"
        B[generateMetadata] --> B1[params 활용]
        B1 --> B2[데이터 페칭]
        B2 --> B3[동적 메타 생성]
    end
    
    subgraph "파일 기반 메타데이터"
        C[metadata 파일] --> C1[opengraph-image.tsx]
        C --> C2[icon.tsx]
        C --> C3[robots.txt]
    end
```

## 11. 라우트 핸들러 (API 라우트)

### 11.1 API 라우트 구조

```mermaid
flowchart TD
    subgraph "API 라우트 구성"
        A["/api"] --> A1["/auth"]
        A --> A2["/charts"]
        A --> A3["/dashboards"]
        A --> A4["/data-sources"]
        
        A1 --> A11["/login"]
        A1 --> A12["/callback"]
        A1 --> A13["/logout"]
        
        A2 --> A21["/[id]"]
        A2 --> A22["/data"]
        
        A3 --> A31["/[id]"]
        A3 --> A32["/share"]
        
        A4 --> A41["/kosis"]
        A4 --> A42["/ecos"]
        A4 --> A43["/oecd"]
    end
```

### 11.2 라우트 핸들러 응답 패턴

```mermaid
flowchart TD
    subgraph "HTTP 메서드 핸들러"
        A[GET/POST/PUT/DELETE] --> B{요청 검증}
        B -->|유효함| C[데이터 처리]
        B -->|유효하지 않음| D[에러 응답]
        C --> E[성공 응답]
        C -->|에러 발생| D
    end
```

## 12. 서버 액션 활용 전략

### 12.1 서버 액션 워크플로우

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

### 12.2 주요 서버 액션 분류

```mermaid
flowchart TD
    subgraph "서버 액션 유형"
        A[데이터 변경 액션] --> A1[생성 액션]
        A --> A2[수정 액션]
        A --> A3[삭제 액션]
        
        B[인증 관련 액션] --> B1[로그인/로그아웃]
        B --> B2[프로필 업데이트]
        
        C[파일 처리 액션] --> C1[업로드 액션]
        C --> C2[내보내기 액션]
    end
```

## 13. 결론

E-Torch의 라우팅 구조는 Next.js App Router의 최신 기능을 활용하여 사용자 중심의 직관적인 인터페이스를 제공합니다. 주요 특징은 다음과 같습니다:

- **계층적 레이아웃**: 페이지 간 일관된 사용자 경험 제공
- **기능별 라우트 그룹화**: 코드 구조의 명확한 조직화
- **서버/클라이언트 분리**: 성능과 사용자 경험 최적화
- **동적 라우팅**: 대시보드와 차트에 대한 유연한 접근
- **안전한 라우트 보호**: 다층적 인증 및 권한 검증
- **메타데이터 최적화**: SEO 및 소셜 공유 최적화
- **서버 액션**: 클라이언트-서버 통신 간소화

이 구조는 E-Torch의 복잡한 기능을 직관적으로 접근 가능하게 만들며, 향후 기능 추가 시에도 확장 가능한 견고한 기반을 제공합니다.
