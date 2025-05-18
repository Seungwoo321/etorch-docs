# E-Torch 상태 관리 아키텍처

## 1. 개요

E-Torch 프로젝트의 상태 관리 아키텍처는 복잡한 경제지표 데이터와 사용자 인터페이스를 효율적으로 관리하기 위해 설계되었습니다. 이 문서는 프론트엔드 상태 관리 전략과 주요 개념을 설명합니다.

## 2. 상태 관리 설계 원칙

```mermaid
graph TD
    A[상태 관리 설계 원칙] --> B[계층적 분리]
    A --> C[관심사 분리]
    A --> D[성능 최적화]
    A --> E[확장성]
    A --> F[개발자 경험]
    
    B --> B1[전역/기능/지역 상태 분리]
    B --> B2[서버/클라이언트 상태 분리]
    
    C --> C1[도메인별 스토어 분리]
    C --> C2[상태와 로직의 분리]
    
    D --> D1[정규화된 상태 구조]
    D --> D2[선택적 구독 패턴]
    D --> D3[메모이제이션 활용]
    
    E --> E1[미들웨어 확장성]
    E --> E2[플러그인 아키텍처]
    
    F --> F1[디버깅 시스템]
    F --> F2[타입 안정성]
    F --> F3[테스트 용이성]
```

## 3. 상태 유형 분류

### 3.1 서버 상태 vs 클라이언트 상태

| 서버 상태 | 클라이언트 상태 |
|----------|----------------|
| 원격 서버에 저장된 데이터 | 로컬에서만 의미 있는 UI 상태 |
| API를 통해 CRUD 작업 | 사용자 상호작용에 의한 변경 |
| 여러 클라이언트에서 공유 | 단일 클라이언트에서만 사용 |
| 비동기 로직과 연관 | 동기적 작업 |
| 서버 상태 캐싱/동기화 필요 | 즉시 변경 반영 |

### 3.2 상태 지속성 및 범위

```mermaid
graph TD
    A[상태 지속성 및 범위] --> B[영구 상태]
    A --> C[애플리케이션 상태]
    A --> D[도메인 상태]
    A --> E[UI 상태]
    A --> F[서버 상태]
    
    B --> B1[앱 재시작 후에도 유지]
    B --> B2[사용자 설정, 테마, 최근 작업]
    B --> B3[Supabase + Zustand]
    
    C --> C1[세션 동안 유지]
    C --> C2[인증, 전역 알림, 에러]
    C --> C3[Zustand + Supabase Auth]
    
    D --> D1[기능별 컨텍스트 동안 유지]
    D --> D2[차트 에디터, 대시보드 에디터 상태]
    D --> D3[Zustand, Context API]
    
    E --> E1[컴포넌트 라이프사이클]
    E --> E2[모달, 드롭다운, 펼침 상태]
    E --> E3[useState, useReducer]
    
    F --> F1[캐시 정책에 따라 유지]
    F --> F2[원격 데이터, API 응답]
    F --> F3[Tanstack Query + Supabase]
```

## 4. 기술 스택 및 도구

### 4.1 상태 관리 도구 선정 및 이유

| 도구 | 사용 영역 | 선정 이유 |
|------|-----------|----------|
| **Zustand** | 클라이언트 상태 | 간결한 API, 유연한 미들웨어, 낮은 보일러플레이트, React와 통합 우수 |
| **Tanstack Query** | 서버 상태 | 캐싱, 재시도, 낙관적 업데이트, 자동 리페칭, devtools 지원 |
| **React Context** | 테마, 인증 등 | 깊은 컴포넌트 트리에서의 상태 공유, 간단한 전역 상태 |
| **React Hook Form** | 폼 상태 | 비제어 컴포넌트 최적화, 유효성 검사, 성능 우수 |
| **Supabase Auth** | 인증 상태 | SNS 로그인 통합, 세션 관리, 보안 강화 |
| **Supabase Database** | 영구 데이터 | 사용자 데이터, 대시보드 정보, 설정 저장 |

### 4.2 Zustand와 Supabase 통합 전략

E-Torch는 다음과 같이 Zustand와 Supabase를 통합하여 상태 관리를 강화합니다:

1. **Supabase Auth와 인증 상태 동기화**
   - 로그인/로그아웃 시 즉시 상태 업데이트
   - 세션 만료 처리 및 자동 갱신

2. **사용자 데이터 관리**
   - 프로필 정보 및 개인화 설정 저장
   - 구독 상태 및 결제 정보 관리

3. **대시보드 및 차트 영구 저장**
   - 사용자별 대시보드 데이터 저장 및 검색
   - 차트 설정 및 시각화 옵션 보존

4. **실시간 데이터 구독**
   - Supabase의 실시간 구독 기능을 활용한 데이터 업데이트
   - 공유 대시보드 변경사항 실시간 동기화

## 5. 다중 계층 상태 아키텍처

### 5.1 계층 구조

```mermaid
graph TD
    A[E-Torch 상태 계층] --> B[영구 상태 계층]
    A --> C[애플리케이션 상태 계층]
    A --> D[도메인 상태 계층]
    A --> E[UI 상태 계층]
    A --> F[세션 상태 계층]
    
    B --> B1[사용자 설정]
    B --> B2[테마 설정]
    B --> B3[최근 작업]
    
    C --> C1[인증 상태]
    C --> C2[전역 에러]
    C --> C3[전역 알림]
    
    D --> D1[차트 에디터 상태]
    D --> D2[대시보드 상태]
    D --> D3[데이터 소스 상태]
    
    E --> E1[모달 상태]
    E --> E2[사이드바 상태]
    E --> E3[툴팁 상태]
    
    F --> F1[현재 경로]
    F --> F2[검색 쿼리]
    F --> F3[필터 선택]
```

### 5.2 주요 스토어 설계

```mermaid
classDiagram
    class AuthState {
        +User user
        +boolean isLoading
        +string error
        +initialize()
        +loginWithSocial(provider)
        +logout()
        +refreshSession()
    }
    
    class DashboardState {
        +Record~string, Dashboard~ dashboards
        +string[] allDashboardIds
        +Record~string, DashboardItem~ items
        +string[] allItemIds
        +Record~string, string[]~ itemsByDashboard
        +string|null activeDashboardId
        +selectDashboard(id)
        +addWidget(dashboardId, widget)
        +removeWidget(dashboardId, widgetId)
        +updateLayout(dashboardId, layout)
        +saveDashboard(dashboard)
    }
    
    class ChartEditorState {
        +Record~string, ChartConfig~ charts
        +string[] allChartIds
        +string|null activeChartId
        +Command[] undoStack
        +Command[] redoStack
        +string viewMode
        +selectChart(id)
        +updateChartProperty(chartId, path, value)
        +createChart(type)
        +deleteChart(id)
        +undo()
        +redo()
    }
```

## 6. 정규화된 상태 구조

### 6.1 엔티티 정규화 패턴

```mermaid
graph TD
    subgraph "정규화 전 (중첩 구조)"
        A1[Dashboard] --> A2[items 배열]
    end
    
    subgraph "정규화 후 (플랫 구조)"
        B1[dashboards.byId] --> B1a["Dashboard1"]
        B1[dashboards.byId] --> B1b["Dashboard2"]
        B2[dashboards.allIds] --> B2a["id1, id2, ..."]
        B3[items.byId] --> B3a["Item1"]
        B3[items.byId] --> B3b["Item2"]
        B4[items.allIds] --> B4a["id1, id2, ..."]
        B5[itemsByDashboard] --> B5a["Dashboard1: [item1, item2]"]
        B5[itemsByDashboard] --> B5b["Dashboard2: [item3, item4]"]
    end
```

이 패턴은 다음과 같은 장점을 제공합니다:
- 데이터 중복 방지
- 특정 항목 업데이트 용이성
- 관계 관리 명확화
- 성능 최적화 용이성

## 7. Supabase 통합 상태 관리

### 7.1 인증 상태 관리

```mermaid
graph TD
    A[Supabase 인증 통합] --> B[인증 초기화]
    A --> C[소셜 로그인 처리]
    A --> D[세션 관리]
    A --> E[권한 관리]
    
    B --> B1["앱 실행 시 세션 확인
    자동 로그인 및 상태 복원"]
    
    C --> C1["Google/Naver/Kakao 
    OAuth 통합"]
    
    D --> D1["세션 유효성 검사
    토큰 갱신 및 만료 처리"]
    
    E --> E1["사용자 역할 기반 
    기능 접근 제어"]
```

### 7.2 대시보드 및 차트 데이터 관리

```mermaid
graph TD
    A[Supabase 데이터 관리] --> B[데이터 구조]
    A --> C[CRUD 작업]
    A --> D[데이터 동기화]
    A --> E[권한 관리]
    
    B --> B1["표 설계:
    - dashboards
    - dashboard_items
    - charts
    - user_settings"]
    
    C --> C1["CRUD 함수:
    - createDashboard
    - fetchDashboards
    - updateDashboard
    - deleteDashboard"]
    
    D --> D1["상태 동기화:
    - RLS 적용
    - 실시간 구독
    - 낙관적 업데이트"]
    
    E --> E1["Row Level Security:
    - 사용자별 접근 제어
    - 공유 설정"]
```

## 8. 성능 최적화 전략

### 8.1 선택적 구독 패턴

```mermaid
graph LR
    A[상태 구독 최적화] --> B[전체 상태 구독]
    A --> C[선택적 구독]
    
    B --> B1["dashboard 전체 구독:
    모든 상태 변경 시 리렌더링"]
    
    C --> C1["대시보드 제목만 구독:
    제목 변경 시에만 리렌더링"]
    
    C --> C2["차트 수만 구독:
    차트 추가/제거 시에만 리렌더링"]
```

### 8.2 메모이제이션 전략

```mermaid
graph TD
    A[메모이제이션 전략] --> B[컴포넌트 메모이제이션]
    A --> C[계산 결과 메모이제이션] 
    A --> D[콜백 메모이제이션]
    
    B --> B1["React.memo()
    리렌더링 조건 제어"]
    
    C --> C1["useMemo()
    고비용 계산 결과 캐싱"]
    
    D --> D1["useCallback()
    함수 참조 안정성 확보"]
```

### 8.3 상태 업데이트 일괄 처리

```mermaid
graph TD
    A[상태 업데이트 최적화] --> B[개별 업데이트]
    A --> C[일괄 업데이트]
    
    B --> B1["여러 상태 개별 변경
    → 여러 번 리렌더링 발생"]
    
    C --> C1["batch 함수로 여러 상태 일괄 변경
    → 한 번만 리렌더링 발생"]
```

## 9. 서버 상태 관리

### 9.1 Tanstack Query와 Supabase 통합

```mermaid
graph TD
    A[Tanstack Query + Supabase] --> B[쿼리 키 설계]
    A --> C[Supabase 쿼리 통합]
    A --> D[실시간 구독 연동]
    A --> E[캐싱 전략]
    
    B --> B1["계층적 쿼리 키:
    ['dashboard', userId, id]
    ['chart', chartId, timeRange]"]
    
    C --> C1["Supabase 쿼리 래핑:
    useSupabaseQuery()
    useDashboardQuery()"]
    
    D --> D1["실시간 업데이트:
    - 쿼리 자동 무효화
    - 캐시 업데이트"]
    
    E --> E1["데이터 유형별 캐싱:
    - 사용자 데이터: 긴 캐시
    - 경제 지표: 중간 캐시
    - 실시간 데이터: 짧은 캐시"]
```

### 9.2 데이터 유형별 캐싱 전략

| 데이터 유형 | staleTime | gcTime | 리페치 전략 | 무효화 조건 |
|------------|-----------|--------|------------|------------|
| 사용자 대시보드 | 5분 | 1시간 | 윈도우 포커스 시 | 대시보드 변경 후 |
| 공유 대시보드 | 10분 | 3시간 | 수동 또는 주기적 | 댓글 작성, 별점 변경 |
| 경제지표 데이터 | 1시간 | 12시간 | 수동 또는 주기적 | 시간 범위 변경 |
| 실시간 지표 | 1분 | 10분 | 주기적 (1분) | 자동 만료 |
| 사용자 설정 | 1일 | 7일 | 앱 시작 시 | 설정 변경 시 |

## 10. Undo/Redo 기능 구현

### 10.1 커맨드 패턴 기반 히스토리 관리

```mermaid
classDiagram
    class Command {
        +execute()
        +undo()
        +redo()
        +merge?(command) boolean
    }
    
    class HistoryManager {
        +Command[] past
        +Command[] future
        +execute(command)
        +undo()
        +redo()
        +canUndo() boolean
        +canRedo() boolean
        +clear()
    }
    
    class UpdateChartPropertyCommand {
        +string chartId
        +string propertyPath
        +any oldValue
        +any newValue
        +ChartEditorStore store
        +execute()
        +undo()
        +redo()
        +merge(command) boolean
    }
    
    class TransactionCommand {
        +Command[] commands
        +string name
        +execute()
        +undo()
        +redo()
    }
    
    Command <|-- UpdateChartPropertyCommand
    Command <|-- TransactionCommand
    HistoryManager o-- Command
```

## 11. 이벤트 버스 패턴

```mermaid
graph TD
    A[이벤트 버스] --> B[이벤트 정의]
    A --> C[이벤트 발행]
    A --> D[이벤트 구독]
    
    B --> B1["EventType 정의
    'dashboard:timeRangeChanged'
    'chart:dataUpdated'"]
    
    C --> C1["publish 메서드
    type과 payload 전달"]
    
    D --> D1["subscribe 메서드
    특정 이벤트와 콜백 등록"]
    
    C1 --> E[이벤트 처리 흐름]
    D1 --> E
    
    E --> E1[컴포넌트 간 느슨한 결합]
    E --> E2[크로스커팅 관심사 해결]
```

## 12. Supabase 인증 및 권한 관리

### 12.1 SNS 로그인 흐름

```mermaid
sequenceDiagram
    participant U as 사용자
    participant C as 클라이언트
    participant A as Supabase Auth
    participant S as SNS 제공자
    participant DB as Supabase DB
    
    U->>C: 로그인 버튼 클릭
    C->>A: 소셜 로그인 요청
    A->>S: 리디렉션
    S->>U: 인증 요청
    U->>S: 인증 승인
    S->>A: 인증 코드 반환
    A->>A: 토큰 생성
    A->>C: 세션 정보 반환
    C->>C: 인증 상태 업데이트
    C->>DB: 사용자 정보 요청
    DB->>C: 사용자 프로필 및 설정
    C->>U: 로그인 완료 및 리디렉션
```

### 12.2 RLS(Row Level Security) 정책

```mermaid
graph TD
    A[RLS 정책] --> B[소유자 정책]
    A --> C[공유 정책]
    A --> D[구독 정책]
    
    B --> B1["dashboards:
    사용자 ID = auth.uid()"]
    
    C --> C1["shared_dashboards:
    is_public = true OR 
    auth.uid() IN shared_users"]
    
    D --> D1["subscription_check:
    subscription_active = true AND
    feature_access = 'premium'"]
```

## 13. 결론

E-Torch의 상태 관리 아키텍처는 Zustand와 Tanstack Query를 중심으로 효율적인 클라이언트 및 서버 상태 관리 전략을 구현하며, Supabase를 통해 SNS 로그인 인증 및 사용자 데이터 관리를 통합적으로 처리합니다.

정규화된 상태 구조, 선택적 구독 패턴, 메모이제이션 등의 최적화 기법을 통해 대량의 경제 데이터를 처리하면서도 우수한 성능을 제공합니다. Supabase의 실시간 구독 기능과 RLS 정책을 활용하여 데이터 동기화 및 보안을 강화하였습니다.

이벤트 버스 패턴과 커맨드 패턴을 통해 컴포넌트 간 느슨한 결합을 유지하면서도 Undo/Redo와 같은 고급 기능을 구현할 수 있습니다. 이러한 아키텍처는 E-Torch가 복잡한 경제지표 대시보드 서비스로서 확장성과 유지보수성을 갖추도록 설계되었습니다.
