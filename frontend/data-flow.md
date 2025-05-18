# E-Torch 데이터 흐름 및 API 연동

## 1. 개요

E-Torch 프로젝트의 데이터 흐름 및 API 연동 설계는 마이그레이션된 경제지표 데이터를 효율적으로 활용하고, 사용자에게 최적화된 시각화 환경을 제공하는 데 중점을 둡니다. 본 문서는 데이터의 흐름, 처리 과정, API 연동 전략을 설명합니다.

## 2. 데이터 아키텍처 개요

E-Torch의 데이터 아키텍처는 다음과 같은 주요 컴포넌트로 구성됩니다:

```mermaid
flowchart TD
    A[데이터 소스] --> B[데이터 페칭 레이어]
    B --> C[데이터 변환 레이어]
    C --> D[데이터 캐싱 레이어]
    D --> E[상태 관리 레이어]
    E --> F[UI 컴포넌트]
    F --> G[사용자 상호작용]
    G --> E
    E --> B
```

### 2.1 주요 데이터 컴포넌트

1. **데이터 소스**: 마이그레이션된 경제지표 데이터베이스, Supabase
2. **데이터 페칭 레이어**: API 클라이언트, 서버 액션, TanStack Query
3. **데이터 변환 레이어**: 데이터 정규화, 변환, 집계
4. **데이터 캐싱 레이어**: TanStack Query 캐시, 서버 컴포넌트 캐시
5. **상태 관리 레이어**: Zustand 스토어, React Context
6. **UI 컴포넌트**: 데이터 표시 및 사용자 상호작용
7. **사용자 상호작용**: 필터링, 정렬, 데이터 조작

## 3. 데이터 소스 구성

E-Torch는 마이그레이션된 경제지표 데이터와 Supabase를 사용하여 사용자 데이터 및 인증을 관리합니다.

### 3.1 주요 데이터 소스

| 데이터 소스 | 제공 데이터 유형 | 지원 주기 | API 유형 | 인증 방식 |
|------------|--------------|----------|---------|----------|
| **마이그레이션된 경제지표 DB** | KOSIS, ECOS, OECD 기반 경제지표 | D, M, Q, A | REST | Supabase JWT |
| **Supabase** | 사용자, 대시보드, 설정 등 | 실시간 | REST, Realtime | JWT |

### 3.2 인증 및 접근 제어

```mermaid
sequenceDiagram
    participant User
    participant Client
    participant Supabase
    participant EconomicDataDB
    
    User->>Client: 로그인 요청
    Client->>+Supabase: 인증 요청(SNS 계정)
    Supabase->>Supabase: 사용자 인증
    Supabase-->>-Client: JWT 토큰 발급
    Client->>Client: 토큰 저장(로컬 스토리지)
    
    Note over Client,EconomicDataDB: 경제지표 데이터 요청
    
    Client->>+Supabase: 요청 + Authorization 헤더(JWT)
    Supabase->>Supabase: JWT 검증 및 사용자 인증
    Supabase->>+EconomicDataDB: 인증된 사용자 요청 전달
    EconomicDataDB-->>-Supabase: 데이터 반환
    Supabase-->>-Client: 응답 데이터
```

E-Torch는 Supabase를 통한 단일 인증 시스템을 사용합니다:

1. **통합 인증 구조**:
   - 사용자는 Supabase를 통해 SNS 계정(Google, Naver, Kakao)으로 인증
   - 인증된 사용자만 마이그레이션된 경제지표 데이터베이스에 접근 가능
   - JWT 토큰으로 모든 데이터 접근 권한 관리

2. **권한 관리**:
   - Supabase의 Row Level Security(RLS)를 통한 데이터 접근 제어
   - 사용자 역할 및 구독 상태에 따른 데이터 접근 제한
   - JWT 클레임에 포함된 사용자 정보를 통한 권한 검증

### 3.3 데이터 통합 전략

E-Torch는 다음과 같은 접근 방식으로 다양한 데이터 소스를 통합합니다:

1. **통합 데이터 모델**: 다양한 경제지표 데이터를 통합된 형식으로 관리
2. **표준화된 메타데이터**: 모든 데이터에 대한 일관된 메타데이터 관리
3. **소스 투명성**: 데이터 출처를 항상 명시하여 사용자에게 정보 제공
4. **동적 소스 선택**: 사용자가 동일 지표에 대해 소스 선택 가능
5. **점진적 데이터 로딩**: 필요한 데이터만 필요한 시점에 로딩

## 4. API 클라이언트 설계

### 4.1 API 클라이언트 아키텍처

E-Torch의 API 클라이언트는 모듈화된 구조를 가지며, 다음과 같은 계층으로 구성됩니다:

```mermaid
flowchart TD
    A[API 클라이언트] --> B[기본 HTTP 클라이언트]
    A --> C[도메인별 클라이언트]
    
    B --> B1[인터셉터]
    B --> B2[에러 핸들링]
    B --> B3[재시도 로직]
    
    C --> C1[경제지표 API]
    C --> C2[대시보드 API]
    C --> C3[사용자 API]
    C --> C4[데이터 쿼리 API]
    
    B1 --> B1A[인증 인터셉터]
    B1A --> B1A1[JWT 토큰 관리]
    
    B2 --> B2A[401 오류 처리]
    B2 --> B2B[403 오류 처리]
    B2 --> B2C[네트워크 오류 처리]
```

### 4.2 인증 통합

모든 API 요청은 Supabase를 통한 인증 흐름을 따릅니다:

```mermaid
flowchart TD
    A[API 요청] --> B{인증 토큰 존재?}
    B -->|Yes| C[요청에 JWT 토큰 추가]
    B -->|No| D[인증 필요 여부 확인]
    D -->|필요| E[로그인 리디렉션]
    D -->|불필요| F[공개 API 요청]
    C --> G[Supabase 요청]
    F --> G
    G --> H{응답 코드?}
    H -->|200-299| I[응답 처리]
    H -->|401| J[토큰 갱신 시도]
    H -->|403| K[권한 오류 처리]
    H -->|기타| L[일반 오류 처리]
    J -->|성공| C
    J -->|실패| E
```

### 4.3 도메인별 API 클라이언트

사용자, 대시보드, 경제지표 등 도메인 영역별 API 클라이언트를 구현하여 관심사를 분리합니다:

1. **경제지표 API 클라이언트**: 지표 데이터 조회 및 분석
2. **대시보드 API 클라이언트**: 대시보드 생성, 조회, 수정, 삭제
3. **사용자 API 클라이언트**: 사용자 프로필 관리, 구독 정보
4. **데이터 쿼리 API 클라이언트**: 복잡한 데이터 쿼리 처리

## 5. 데이터 페칭 전략

E-Torch는 다양한 데이터 페칭 전략을 사용하여 성능과 사용자 경험을 최적화합니다.

### 5.1 서버 컴포넌트에서의 데이터 페칭

Next.js 서버 컴포넌트를 활용한 데이터 페칭:

1. **초기 데이터 로드**: 페이지 로드 시 서버에서 필요한 데이터 로드
2. **SEO 최적화**: 서버에서 메타데이터 생성 및 최적화
3. **클라이언트 번들 최소화**: 데이터를 서버에서 처리하여 클라이언트 번들 크기 감소
4. **인증 처리**: 서버 컴포넌트에서 Supabase 서버 SDK를 활용한 인증 처리

### 5.2 TanStack Query를 활용한 클라이언트 데이터 페칭

1. **초기 데이터 활용**: 서버에서 제공한 초기 데이터로 시작
2. **지연 로딩**: 필요한 데이터만 지연 로딩
3. **캐싱 전략**: 적절한 캐싱을 통한 네트워크 요청 최소화
4. **자동 리페치**: 데이터 신선도를 유지하기 위한 자동 리페치
5. **낙관적 업데이트**: 사용자 경험 향상을 위한 낙관적 업데이트
6. **인증 통합**: Supabase 클라이언트를 TanStack Query와 통합하여 인증 관리

### 5.3 서버 액션을 활용한 데이터 변경

1. **안전한 데이터 변경**: 서버 측에서 데이터 유효성 검증 및 변경
2. **캐시 무효화**: 관련 페이지의 캐시 자동 무효화
3. **트랜잭션 보장**: 여러 데이터 변경의 원자성 보장
4. **인증 컨텍스트**: 서버 액션에서 사용자 인증 상태 유지 및 확인

## 6. 데이터 변환 및 처리 파이프라인

E-Torch는 경제지표 데이터 처리를 위한 파이프라인을 구현합니다.

### 6.1 데이터 파이프라인 구조

```mermaid
flowchart LR
    A[원시 데이터 소스] --> B[데이터 추출]
    B --> C[데이터 정규화]
    C --> D[데이터 필터링]
    D --> E[데이터 변환]
    E --> F[데이터 집계]
    F --> G[시각화 데이터]
```

### 6.2 데이터 변환 유형

다양한 형태의 데이터 변환을 지원합니다:

1. **원본값**: 가공 없이 원본 데이터 사용
2. **기간 대비 변화율**: 직전 기간 대비 값의 변화율 계산
3. **전년 동기 대비 변화율**: 전년 동일 기간 대비 변화율 계산
4. **누적값**: 시간 경과에 따른 누적값 계산

### 6.3 데이터 변환기

### 6.4 소스별 데이터 통합 로직

E-Torch는 다양한 데이터 소스(KOSIS, ECOS, OECD)의 데이터를 통합하여 일관된 형식으로 처리합니다. 각 소스별 데이터 구조와 정규화 프로세스는 다음과 같습니다:

#### 6.4.1 소스별 데이터 구조 및 정규화

##### KOSIS 데이터 구조

```typescript
// KOSIS 원본 데이터 구조
interface KosisRawData {
  StatisticsName: string;
  StatCode: string;
  PeriodsOnHeader: boolean;
  Period: string; // "M", "Q", "A"
  Items: Array<{
    Category: string;
    Item: string;
    ItemCode: string;
    Values: Array<{
      Period: string; // "2024-01", "2024-Q1", "2024"
      Value: string;
    }>;
  }>;
}

// 정규화된 데이터 구조
interface NormalizedTimeSeriesData {
  source: "KOSIS" | "ECOS" | "OECD";
  indicatorCode: string;
  indicatorName: string;
  period: "D" | "M" | "Q" | "A";
  unit: string;
  data: Array<{
    date: string; // ISO 형식의 날짜 문자열
    value: number;
    formattedDate?: string; // 표시용 날짜 포맷
    metadata?: Record<string, any>; // 추가 메타데이터
  }>;
}
```

**KOSIS 정규화 로직**:

```typescript
function normalizeKosisData(data: KosisRawData): NormalizedTimeSeriesData {
  // 날짜 변환 유틸리티
  const convertPeriodToDate = (period: string): string => {
    // 월간(M) 데이터: "2024-01" -> "2024-01-15" (월 중간일)
    if (period.match(/^\d{4}-\d{2}$/)) {
      return `${period}-15`;
    }
    // 분기(Q) 데이터: "2024-Q1" -> "2024-02-15" (분기 중간월)
    else if (period.match(/^\d{4}-Q\d$/)) {
      const year = period.substring(0, 4);
      const quarter = parseInt(period.substring(6, 7));
      const month = (quarter - 1) * 3 + 2; // Q1->2월, Q2->5월, Q3->8월, Q4->11월
      return `${year}-${month.toString().padStart(2, '0')}-15`;
    }
    // 연간(A) 데이터: "2024" -> "2024-07-01" (연도 중간)
    else if (period.match(/^\d{4}$/)) {
      return `${period}-07-01`;
    }
    return period;
  };

  // 데이터 변환 및 정규화
  return {
    source: "KOSIS",
    indicatorCode: data.StatCode,
    indicatorName: data.StatisticsName,
    period: data.Period as "M" | "Q" | "A",
    unit: "", // KOSIS에서 별도로 추출 필요
    data: data.Items.flatMap(item => 
      item.Values.map(valueItem => ({
        date: convertPeriodToDate(valueItem.Period),
        value: parseFloat(valueItem.Value),
        formattedDate: valueItem.Period,
        metadata: {
          category: item.Category,
          item: item.Item,
          itemCode: item.ItemCode
        }
      }))
    )
  };
}
```

##### ECOS 데이터 구조

ECOS(한국은행 경제통계시스템)는 더 다양한 주기(일별, 월별, 분기별, 연간)를 지원하며 다음과 같은 구조로 데이터를 제공합니다:

```typescript
// ECOS 원본 데이터 구조
interface EcosRawData {
  STAT_NAME: string;
  STAT_CODE: string;
  ITEM_NAME1: string;
  ITEM_CODE1: string;
  ITEM_NAME2?: string;
  ITEM_CODE2?: string;
  UNIT_NAME: string;
  TIME: Array<{
    DATA_VALUE: string;
    TIME: string; // "20240101", "202401", "2024Q1", "2024"
  }>;
}

// ECOS 정규화 로직
function normalizeEcosData(data: EcosRawData): NormalizedTimeSeriesData {
  // ECOS 날짜 변환 유틸리티 (각 주기별 변환)
  const convertEcosTimeToDate = (time: string): string => {
    // 일별(D) 데이터: "20240101" -> "2024-01-01"
    if (time.length === 8) {
      return `${time.substring(0, 4)}-${time.substring(4, 6)}-${time.substring(6, 8)}`;
    }
    // 월간(M) 데이터: "202401" -> "2024-01-15"
    else if (time.length === 6) {
      return `${time.substring(0, 4)}-${time.substring(4, 6)}-15`;
    }
    // 분기(Q) 데이터: "2024Q1" -> "2024-02-15"
    else if (time.includes('Q')) {
      const year = time.substring(0, 4);
      const quarter = parseInt(time.substring(5, 6));
      const month = (quarter - 1) * 3 + 2;
      return `${year}-${month.toString().padStart(2, '0')}-15`;
    }
    // 연간(A) 데이터: "2024" -> "2024-07-01"
    else if (time.length === 4) {
      return `${time}-07-01`;
    }
    return time;
  };

  // 주기 판별
  const detectPeriod = (times: Array<{TIME: string}>): "D" | "M" | "Q" | "A" => {
    const sampleTime = times[0]?.TIME;
    if (!sampleTime) return "M"; // 기본값
    
    if (sampleTime.length === 8) return "D";
    if (sampleTime.length === 6) return "M";
    if (sampleTime.includes('Q')) return "Q";
    return "A";
  };

  return {
    source: "ECOS",
    indicatorCode: data.STAT_CODE,
    indicatorName: data.STAT_NAME,
    period: detectPeriod(data.TIME),
    unit: data.UNIT_NAME,
    data: data.TIME.map(item => ({
      date: convertEcosTimeToDate(item.TIME),
      value: parseFloat(item.DATA_VALUE),
      formattedDate: item.TIME,
      metadata: {
        item1: data.ITEM_NAME1,
        item1Code: data.ITEM_CODE1,
        item2: data.ITEM_NAME2,
        item2Code: data.ITEM_CODE2
      }
    }))
  };
}
```

##### OECD 데이터 구조

OECD 데이터는 국제 기준을 따르며 다음과 같은 구조로 제공됩니다:

```typescript
// OECD 원본 데이터 구조
interface OecdRawData {
  indicator: string;
  indicator_id: string;
  subject: string;
  measure: string;
  frequency: string; // "A", "Q", "M"
  unit: string;
  observations: Array<{
    time: string; // "2024", "2024-Q1", "2024-01"
    value: number;
  }>;
}

// OECD 정규화 로직
function normalizeOecdData(data: OecdRawData): NormalizedTimeSeriesData {
  // OECD 날짜 변환 유틸리티
  const convertOecdTimeToDate = (time: string, frequency: string): string => {
    // 월간(M) 데이터: "2024-01" -> "2024-01-15"
    if (frequency === "M") {
      return `${time}-15`;
    }
    // 분기(Q) 데이터: "2024-Q1" -> "2024-02-15"
    else if (frequency === "Q") {
      const year = time.substring(0, 4);
      const quarter = parseInt(time.substring(6, 7));
      const month = (quarter - 1) * 3 + 2;
      return `${year}-${month.toString().padStart(2, '0')}-15`;
    }
    // 연간(A) 데이터: "2024" -> "2024-07-01"
    else if (frequency === "A") {
      return `${time}-07-01`;
    }
    return time;
  };

  // 주기 매핑
  const frequencyMap: Record<string, "D" | "M" | "Q" | "A"> = {
    "D": "D",
    "M": "M",
    "Q": "Q",
    "A": "A"
  };

  return {
    source: "OECD",
    indicatorCode: data.indicator_id,
    indicatorName: data.indicator,
    period: frequencyMap[data.frequency] || "A",
    unit: data.unit,
    data: data.observations.map(item => ({
      date: convertOecdTimeToDate(item.time, data.frequency),
      value: item.value,
      formattedDate: item.time,
      metadata: {
        subject: data.subject,
        measure: data.measure
      }
    }))
  };
}
```

#### 6.4.2 결측치 처리 알고리즘

시계열 데이터에서 결측치는 일반적으로 다음 방법으로 처리합니다:

```typescript
enum InterpolationMethod {
  LINEAR = "linear",
  PREVIOUS = "previous",
  NEXT = "next",
  ZERO = "zero",
  NONE = "none"
}

function handleMissingValues(
  data: NormalizedTimeSeriesData,
  method: InterpolationMethod = InterpolationMethod.LINEAR
): NormalizedTimeSeriesData {
  if (method === InterpolationMethod.NONE || data.data.length <= 1) {
    return data;
  }

  // 날짜 정렬
  const sortedData = [...data.data].sort((a, b) => 
    new Date(a.date).getTime() - new Date(b.date).getTime()
  );

  // 결측치 처리
  for (let i = 1; i < sortedData.length; i++) {
    const prevDate = new Date(sortedData[i-1].date);
    const currDate = new Date(sortedData[i].date);
    
    // 주기 간격 확인 (예: 월간 데이터는 대략 30일 간격)
    let expectedGap: number;
    switch (data.period) {
      case "D": expectedGap = 1; break;
      case "M": expectedGap = 30; break;
      case "Q": expectedGap = 90; break;
      case "A": expectedGap = 365; break;
      default: expectedGap = 30;
    }
    
    const daysDiff = (currDate.getTime() - prevDate.getTime()) / (1000 * 60 * 60 * 24);
    
    // 예상 간격의 1.5배 이상이면 결측치로 간주
    if (daysDiff > expectedGap * 1.5) {
      // 결측치 개수 계산 (반올림)
      const missingCount = Math.round(daysDiff / expectedGap) - 1;
      
      // 보간법에 따른 값 계산
      for (let j = 1; j <= missingCount; j++) {
        const missingDate = new Date(prevDate);
        missingDate.setDate(prevDate.getDate() + Math.round(j * daysDiff / (missingCount + 1)));
        
        let missingValue: number;
        switch (method) {
          case InterpolationMethod.LINEAR:
            // 선형 보간
            missingValue = sortedData[i-1].value + 
              (sortedData[i].value - sortedData[i-1].value) * (j / (missingCount + 1));
            break;
          case InterpolationMethod.PREVIOUS:
            // 이전 값 사용
            missingValue = sortedData[i-1].value;
            break;
          case InterpolationMethod.NEXT:
            // 다음 값 사용
            missingValue = sortedData[i].value;
            break;
          case InterpolationMethod.ZERO:
            // 0 사용
            missingValue = 0;
            break;
          default:
            missingValue = sortedData[i-1].value;
        }
        
        // 결측치 데이터 삽입
        sortedData.splice(i, 0, {
          date: missingDate.toISOString().split('T')[0],
          value: missingValue,
          formattedDate: formatDateByPeriod(missingDate, data.period),
          metadata: { interpolated: true, method }
        });
        
        i++; // 인덱스 조정
      }
    }
  }
  
  return {
    ...data,
    data: sortedData
  };
}

// 주기별 날짜 포맷팅
function formatDateByPeriod(date: Date, period: "D" | "M" | "Q" | "A"): string {
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  
  switch (period) {
    case "D":
      return `${year}-${month.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`;
    case "M":
      return `${year}-${month.toString().padStart(2, '0')}`;
    case "Q":
      const quarter = Math.ceil(month / 3);
      return `${year}-Q${quarter}`;
    case "A":
      return `${year}`;
    default:
      return `${year}-${month.toString().padStart(2, '0')}-${day.toString().padStart(2, '0')}`;
  }
}
```

#### 6.4.3 기간 동기화 전략

서로 다른 주기를 가진 데이터를 비교하거나 통합할 때의 기간 동기화 전략입니다:

```typescript
enum SynchronizationMethod {
  UPSAMPLE = "upsample", // 낮은 주기를 높은 주기로 (연간→분기→월간→일간)
  DOWNSAMPLE = "downsample" // 높은 주기를 낮은 주기로 (일간→월간→분기→연간)
}

enum AggregationMethod {
  AVERAGE = "average",
  SUM = "sum",
  FIRST = "first",
  LAST = "last",
  MAX = "max",
  MIN = "min"
}

function synchronizeTimeSeries(
  seriesArray: NormalizedTimeSeriesData[],
  targetPeriod: "D" | "M" | "Q" | "A",
  method: SynchronizationMethod = SynchronizationMethod.DOWNSAMPLE,
  aggregation: AggregationMethod = AggregationMethod.AVERAGE
): NormalizedTimeSeriesData[] {
  return seriesArray.map(series => {
    // 이미 타겟 주기와 같으면 변환 불필요
    if (series.period === targetPeriod) {
      return series;
    }
    
    // 업샘플링 (더 세분화된 주기로 변환: A→Q→M→D)
    if (method === SynchronizationMethod.UPSAMPLE) {
      return upsampleTimeSeries(series, targetPeriod);
    }
    
    // 다운샘플링 (더 넓은 주기로 변환: D→M→Q→A)
    return downsampleTimeSeries(series, targetPeriod, aggregation);
  });
}

// 다운샘플링 구현 (높은 주기 → 낮은 주기)
function downsampleTimeSeries(
  series: NormalizedTimeSeriesData,
  targetPeriod: "D" | "M" | "Q" | "A",
  aggregation: AggregationMethod
): NormalizedTimeSeriesData {
  // 주기 우선순위: D > M > Q > A
  const periodPriority: Record<string, number> = { "D": 3, "M": 2, "Q": 1, "A": 0 };
  
  // 현재 주기가 타겟 주기보다 낮거나 같으면 다운샘플링 불필요
  if (periodPriority[series.period] <= periodPriority[targetPeriod]) {
    return series;
  }
  
  // 각 데이터 포인트를 타겟 주기로 그룹화
  const groupedData: Record<string, number[]> = {};
  
  for (const point of series.data) {
    const date = new Date(point.date);
    let groupKey: string;
    
    switch (targetPeriod) {
      case "M":
        groupKey = `${date.getFullYear()}-${(date.getMonth() + 1).toString().padStart(2, '0')}`;
        break;
      case "Q":
        const quarter = Math.ceil((date.getMonth() + 1) / 3);
        groupKey = `${date.getFullYear()}-Q${quarter}`;
        break;
      case "A":
        groupKey = `${date.getFullYear()}`;
        break;
      default:
        groupKey = point.date;
    }
    
    if (!groupedData[groupKey]) {
      groupedData[groupKey] = [];
    }
    
    groupedData[groupKey].push(point.value);
  }
  
  // 집계 함수로 각 그룹의 값 계산
  const aggregateFunction = (values: number[]): number => {
    if (values.length === 0) return 0;
    
    switch (aggregation) {
      case AggregationMethod.AVERAGE:
        return values.reduce((sum, val) => sum + val, 0) / values.length;
      case AggregationMethod.SUM:
        return values.reduce((sum, val) => sum + val, 0);
      case AggregationMethod.FIRST:
        return values[0];
      case AggregationMethod.LAST:
        return values[values.length - 1];
      case AggregationMethod.MAX:
        return Math.max(...values);
      case AggregationMethod.MIN:
        return Math.min(...values);
      default:
        return values.reduce((sum, val) => sum + val, 0) / values.length;
    }
  };
  
  // 결과 데이터 생성
  const resultData = Object.entries(groupedData).map(([key, values]) => {
    let date: string;
    
    // 타겟 주기에 맞는 날짜 생성
    switch (targetPeriod) {
      case "M":
        date = `${key}-15`; // 월 중간일
        break;
      case "Q":
        const [year, quarter] = key.split('-Q');
        const month = (parseInt(quarter) - 1) * 3 + 2; // Q1->2월, Q2->5월, Q3->8월, Q4->11월
        date = `${year}-${month.toString().padStart(2, '0')}-15`;
        break;
      case "A":
        date = `${key}-07-01`; // 연도 중간
        break;
      default:
        date = key;
    }
    
    return {
      date,
      value: aggregateFunction(values),
      formattedDate: key,
      metadata: {
        aggregated: true,
        method: aggregation,
        count: values.length
      }
    };
  });
  
  // 날짜 기준 정렬
  resultData.sort((a, b) => new Date(a.date).getTime() - new Date(b.date).getTime());
  
  return {
    ...series,
    period: targetPeriod,
    data: resultData
  };
}

// 업샘플링 구현 (낮은 주기 → 높은 주기)
function upsampleTimeSeries(
  series: NormalizedTimeSeriesData,
  targetPeriod: "D" | "M" | "Q" | "A"
): NormalizedTimeSeriesData {
  // 주기 우선순위: D > M > Q > A
  const periodPriority: Record<string, number> = { "D": 3, "M": 2, "Q": 1, "A": 0 };
  
  // 현재 주기가 타겟 주기보다 높거나 같으면 업샘플링 불필요
  if (periodPriority[series.period] >= periodPriority[targetPeriod]) {
    return series;
  }
  
  // 업샘플링은 기본적으로 값을 복제하는 방식으로 구현
  // (더 정교한 보간법을 적용할 수도 있음)
  
  const resultData: Array<{
    date: string;
    value: number;
    formattedDate: string;
    metadata?: Record<string, any>;
  }> = [];
  
  // 데이터 정렬
  const sortedData = [...series.data].sort(
    (a, b) => new Date(a.date).getTime() - new Date(b.date).getTime()
  );
  
  for (let i = 0; i < sortedData.length - 1; i++) {
    const currentPoint = sortedData[i];
    const nextPoint = sortedData[i + 1];
    
    const currentDate = new Date(currentPoint.date);
    const nextDate = new Date(nextPoint.date);
    
    // 현재 포인트 추가
    resultData.push({
      ...currentPoint,
      metadata: {
        ...currentPoint.metadata,
        upsampled: false
      }
    });
    
    // 중간 포인트 생성
    const dates: Date[] = [];
    
    if (targetPeriod === "D") {
      // 일별 데이터로 업샘플링
      const daysDiff = Math.floor((nextDate.getTime() - currentDate.getTime()) / (1000 * 60 * 60 * 24));
      
      for (let day = 1; day < daysDiff; day++) {
        const newDate = new Date(currentDate);
        newDate.setDate(currentDate.getDate() + day);
        dates.push(newDate);
      }
    } else if (targetPeriod === "M" && (series.period === "Q" || series.period === "A")) {
      // 월별 데이터로 업샘플링
      let month = currentDate.getMonth();
      const endMonth = nextDate.getMonth() + (nextDate.getFullYear() - currentDate.getFullYear()) * 12;
      
      for (let m = month + 1; m < endMonth; m++) {
        const newDate = new Date(currentDate);
        newDate.setMonth(m % 12);
        newDate.setFullYear(currentDate.getFullYear() + Math.floor(m / 12));
        dates.push(newDate);
      }
    } else if (targetPeriod === "Q" && series.period === "A") {
      // 분기별 데이터로 업샘플링
      const currentYear = currentDate.getFullYear();
      const nextYear = nextDate.getFullYear();
      
      // 현재 연도의 남은 분기
      for (let q = Math.ceil((currentDate.getMonth() + 1) / 3) + 1; q <= 4; q++) {
        const newDate = new Date(currentYear, (q - 1) * 3 + 1, 15);
        dates.push(newDate);
      }
      
      // 중간 연도의 모든 분기
      for (let y = currentYear + 1; y < nextYear; y++) {
        for (let q = 1; q <= 4; q++) {
          const newDate = new Date(y, (q - 1) * 3 + 1, 15);
          dates.push(newDate);
        }
      }
      
      // 다음 연도의 이전 분기
      for (let q = 1; q < Math.ceil((nextDate.getMonth() + 1) / 3); q++) {
        const newDate = new Date(nextYear, (q - 1) * 3 + 1, 15);
        dates.push(newDate);
      }
    }
    
    // 선형 보간으로 중간 값 계산
    for (const date of dates) {
      const ratio = (date.getTime() - currentDate.getTime()) / (nextDate.getTime() - currentDate.getTime());
      const interpolatedValue = currentPoint.value + (nextPoint.value - currentPoint.value) * ratio;
      
      resultData.push({
        date: date.toISOString().split('T')[0],
        value: interpolatedValue,
        formattedDate: formatDateByPeriod(date, targetPeriod),
        metadata: {
          upsampled: true,
          interpolated: true,
          originalPeriod: series.period
        }
      });
    }
  }
  
  // 마지막 포인트 추가
  resultData.push({
    ...sortedData[sortedData.length - 1],
    metadata: {
      ...sortedData[sortedData.length - 1].metadata,
      upsampled: false
    }
  });
  
  // 날짜 기준 정렬
  resultData.sort((a, b) => new Date(a.date).getTime() - new Date(b.date).getTime());
  
  return {
    ...series,
    period: targetPeriod,
    data: resultData
  };
}
```

이러한 데이터 통합 및 정규화 로직을 통해 다양한 소스에서 수집된 경제지표 데이터를 일관된 형식으로 변환하고, 결측치 처리 및 기간 동기화를 수행하여 의미 있는 시각화 및 분석이 가능하도록 합니다.

## 7. 데이터 캐싱 전략

E-Torch는 다층적 캐싱 전략을 통해 성능을 최적화합니다.

### 7.1 TanStack Query 캐싱 설정

```mermaid
flowchart TD
    A[API 요청] --> B[캐시 확인]
    B -->|캐시 있음 & 신선함| C[캐시된 데이터 사용]
    B -->|캐시 있음 & 신선하지 않음| D[백그라운드 리페치]
    B -->|캐시 없음| E[새 데이터 페칭]
    D --> F[UI 업데이트]
    E --> G[캐시 저장]
    G --> F
```

### 7.2 데이터 유형별 캐싱 전략

| 데이터 유형 | staleTime | gcTime | 리페치 전략 | 무효화 조건 |
|------------|-----------|--------|------------|------------|
| 사용자 대시보드 | 5분 | 1시간 | 윈도우 포커스 시 | 대시보드 변경 후 |
| 공유 대시보드 | 10분 | 3시간 | 수동 또는 주기적 | 댓글 작성, 별점 변경 |
| 경제지표 데이터 | 1시간 | 12시간 | 수동 또는 주기적 | 시간 범위 변경 |
| 실시간 지표 | 1분 | 10분 | 주기적 (1분) | 자동 만료 |
| 사용자 구독 정보 | 30분 | 1시간 | 윈도우 포커스 시 | 구독 변경 후 |

### 7.3 다중 계층 캐싱

```mermaid
flowchart TD
    A[데이터 요청] --> B[브라우저 메모리 캐시]
    B -->|히트| C[메모리 캐시 데이터 반환]
    B -->|미스| D[영구 캐시 확인]
    D -->|히트| E[영구 캐시 데이터 반환]
    D -->|미스| F[서버 요청]
    F --> G[서버 캐시 확인]
    G -->|히트| H[서버 캐시 데이터 반환]
    G -->|미스| I[데이터베이스 쿼리]
    I --> J[응답 캐싱 및 반환]
```

## 8. 클라이언트-서버 통신 최적화

### 8.1 요청 일괄 처리 (Request Batching)

여러 데이터 요청을 한 번의 네트워크 호출로 처리하는 전략으로, 특히 대시보드 데이터 로딩 시 활용합니다.

### 8.2 점진적 로딩 (Progressive Loading)

대시보드와 같은 복잡한 화면에서 중요한 데이터를 먼저 로드하고, 덜 중요한 데이터는 나중에 로드하는 전략을 사용합니다.

### 8.3 낙관적 UI 업데이트 (Optimistic UI)

사용자 액션 즉시 UI를 업데이트하고, 서버 응답을 기다리는 대신 성공을 가정하여 사용자 경험을 향상시킵니다.

## 9. 실시간 데이터 업데이트

E-Torch는 필요에 따라 실시간 또는 주기적 데이터 업데이트를 지원합니다.

### 9.1 주기적 데이터 폴링

```mermaid
sequenceDiagram
    Client->>+Server: 초기 데이터 요청
    Server-->>-Client: 데이터 응답
    Note over Client,Server: 설정된 간격(10초~5분)으로 반복
    loop 주기적 폴링
        Client->>+Server: 데이터 업데이트 확인
        Server-->>-Client: 변경된 데이터 또는 304 Not Modified
    end
```

### 9.2 Supabase Realtime을 활용한 실시간 업데이트

Supabase의 Realtime 기능을 활용하여 협업 시나리오를 지원합니다:

1. **대시보드 공동 편집**: 여러 사용자가 동시에 대시보드 편집
2. **실시간 댓글 및 피드백**: 대시보드에 대한 실시간 댓글 및 피드백
3. **알림**: 데이터 변경 또는 이벤트 발생 시 실시간 알림

```mermaid
flowchart TD
    A[Supabase Realtime] --> B[채널 구독]
    B --> C{이벤트 유형}
    C -->|INSERT| D[새 항목 추가]
    C -->|UPDATE| E[항목 업데이트]
    C -->|DELETE| F[항목 삭제]
    C -->|Presence| G[사용자 상태 변경]
    D --> H[UI 업데이트]
    E --> H
    F --> H
    G --> I[협업 상태 표시]
```

## 10. 데이터 내보내기 및 공유

E-Torch는 데이터 및 시각화 결과를 다양한 형식으로 내보내고 공유하는 기능을 제공합니다.

### 10.1 차트 내보내기

사용자는 차트를 다양한 형식(PNG, SVG, CSV)으로 내보낼 수 있으며, 모든 내보내기에는 E-Torch 워터마크가 포함됩니다.

### 10.2 대시보드 공유

```mermaid
flowchart TD
    A[대시보드 공유] --> B{공개 여부}
    B -->|비공개| C[특정 사용자에게만 공유]
    B -->|공개| D[링크로 공유]
    C --> E[이메일 초대]
    C --> F[권한 관리]
    D --> G[소셜 미디어 공유]
    D --> H[임베드 코드 생성]
```

## 11. 오류 처리 및 복원 전략

E-Torch는 네트워크 오류, 서버 오류 등에 대한 강건한 오류 처리 전략을 구현합니다.

### 11.1 오류 처리 계층

```mermaid
flowchart TD
    A[사용자 요청] --> B[요청 유효성 검증]
    B -->|유효하지 않음| C[클라이언트 측 오류 표시]
    B -->|유효함| D[API 요청]
    D --> E{요청 성공?}
    E -->|실패| F[오류 분류]
    E -->|성공| G[데이터 처리 및 표시]
    F --> H[네트워크 오류]
    F --> I[서버 오류]
    F --> J[인증 오류]
    F --> K[권한 오류]
    H --> L[자동 재시도]
    I --> M[오류 로깅 및 사용자 메시지]
    J --> N[로그인 페이지로 리다이렉트]
    K --> O[접근 제한 메시지]
```

### 11.2 복원 전략

1. **자동 재연결**: 네트워크 연결 복원 시 자동 데이터 동기화
2. **로컬 캐싱**: 오프라인 상태에서도 최근 데이터 접근 가능
3. **변경사항 큐**: 오프라인 상태에서 변경사항을 큐에 저장 후 연결 복원 시 적용
4. **점진적 성능 저하**: 일부 기능만 제한하여 핵심 기능 유지

## 12. 결론

E-Torch의 데이터 흐름 및 API 연동 아키텍처는 마이그레이션된 경제지표 데이터를 효율적으로 활용하고, Supabase를 통한 단일 인증 시스템으로 모든 데이터 접근을 관리합니다. 계층화된 데이터 아키텍처, 모듈화된 API 클라이언트, 최적화된 데이터 페칭 전략, 강력한 캐싱 메커니즘 등이 이를 가능하게 합니다.

특히 데이터 변환 및 처리 파이프라인은 다양한 경제지표 데이터를 일관된 형태로 변환하여 차트와 대시보드 컴포넌트에서 쉽게 사용할 수 있도록 합니다. 또한 낙관적 UI 업데이트, 요청 일괄 처리, 점진적 로딩 등의 최적화 기법은 대량 데이터 처리 시에도 우수한 사용자 경험을 보장합니다.

Supabase의 실시간 기능과 결합된 실시간 데이터 업데이트, 데이터 내보내기 및 공유, 오류 처리 및 복원 전략 등은 E-Torch가 실무적인 요구사항을 충족하는 강력한 경제지표 대시보드 서비스로 기능할 수 있게 합니다.