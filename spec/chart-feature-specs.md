# E-Torch: 차트 컴포넌트 기능 명세서

## 1. 공통 옵션 컴포넌트

### 1.1 Panel Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| PO-001 | Title | 컴포넌트 상단에 표시되는 제목 | 텍스트 | 빈 문자열 | 최대 100자 | 모든 컴포넌트 |
| PO-002 | Description | 제목 아래에 작은 글자로 표시되는 설명 텍스트 | 텍스트 | 빈 문자열 | 최대 200자 | 모든 컴포넌트 |
| PO-003 | 백그라운드 투명 여부 | 패널 배경의 투명도 설정 | 불리언 | false | true/false | 모든 컴포넌트 |

### 1.2 Tooltip Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| TO-001 | Tooltip Mode | 툴팁 표시 방식 | 선택 | default | default, active, hidden | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |
| TO-002 | Max Width | 툴팁의 최대 너비 | 숫자 | 160 | 60-500px | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |
| TO-003 | Cursor Style | 커서 스타일 설정 | 선택 | solid | solid, dash | Time Series |
| TO-004 | Dash Pattern | dash 선택 시 대시 패턴 | 문자열 | "2,2" | 형식: "숫자,숫자" | Time Series |
| TO-005 | Cursor Width | 커서의 두께 | 숫자 | 2 | 1-10px | Time Series |

### 1.3 Legend Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| LG-001 | Visibility | 범례 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |
| LG-002 | Layout | 범례 레이아웃 방향 | 선택 | horizontal | horizontal, vertical | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |
| LG-003 | Horizontal Align | 수평 정렬 방식 | 선택 | left | left, center, right | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |
| LG-004 | Vertical Align | 수직 정렬 방식 | 선택 | bottom | top, middle, bottom | Time Series, Bar Chart, Pie Chart, Scatter Chart, Radar Chart, Radial Bar Chart |

### 1.4 X-Axis Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| XA-001 | Visibility | X축 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| XA-002 | Data Key | X축에 표시할 데이터 필드 | 텍스트 | "date" | 데이터 소스의 필드명 | Time Series, Bar Chart, Scatter Chart |
| XA-003 | Type | X축 데이터 유형 | 선택 | category | category, number | Time Series, Bar Chart, Scatter Chart |
| XA-004 | Min Value | number 타입일 때 최소값 | 숫자 | 0 | 숫자 또는 'auto' | Time Series, Scatter Chart |
| XA-005 | Max Value | number 타입일 때 최대값 | 텍스트 | "auto" | 숫자 또는 'auto' | Time Series, Scatter Chart |
| XA-006 | Show Axis | 축 선 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| XA-007 | Tick Count | 눈금 개수 | 숫자 | 5 | 2-20 | Time Series, Bar Chart, Scatter Chart |
| XA-008 | Tick Angle | 눈금 레이블 회전 각도 | 숫자 | 0 | -90 ~ 90 | Time Series, Bar Chart, Scatter Chart |
| XA-009 | Show Tick | 눈금 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| XA-010 | Tick Size | 눈금 크기 | 숫자 | 6 | 0-20px | Time Series, Bar Chart, Scatter Chart |

### 1.5 Y-Axis Options (Primary)

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| YA-001 | Visibility | Y축 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| YA-002 | Unit | 축 레이블에 표시할 단위 텍스트 | 텍스트 | 빈 문자열 | 최대 10자 | Time Series, Bar Chart, Scatter Chart |
| YA-003 | Type | Y축 데이터 유형 | 선택 | number | category, number | Time Series, Bar Chart, Scatter Chart |
| YA-004 | Min Value | number 타입일 때 최소값 | 숫자 | 0 | 숫자 또는 'auto' | Time Series, Bar Chart, Scatter Chart |
| YA-005 | Max Value | number 타입일 때 최대값 | 텍스트 | "auto" | 숫자 또는 'auto' | Time Series, Bar Chart, Scatter Chart |
| YA-006 | Show Axis | 축 선 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| YA-007 | Tick Count | 눈금 개수 | 숫자 | 5 | 2-20 | Time Series, Bar Chart, Scatter Chart |
| YA-008 | Tick Angle | 눈금 레이블 회전 각도 | 숫자 | 0 | -90 ~ 90 | Time Series, Scatter Chart |
| YA-009 | Show Tick | 눈금 표시 여부 | 불리언 | true | true/false | Time Series, Bar Chart, Scatter Chart |
| YA-010 | Tick Size | 눈금 크기 | 숫자 | 6 | 0-20px | Time Series, Bar Chart, Scatter Chart |
| YA-011 | Label Position | 축 레이블 위치 | 선택 | insideLeft | insideLeft, insideTopLeft, insideBottomLeft | Time Series, Bar Chart, Scatter Chart |

### 1.6 Y-Axis Options (Secondary)

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| YAS-001 | Visibility | 보조 Y축 표시 여부 | 불리언 | false | true/false | Time Series |
| YAS-002 | Unit | 축 레이블에 표시할 단위 텍스트 | 텍스트 | 빈 문자열 | 최대 10자 | Time Series |
| YAS-003 | Type | 보조 Y축 데이터 유형 | 선택 | number | category, number | Time Series |
| YAS-004 | Min Value | number 타입일 때 최소값 | 숫자 | 0 | 숫자 또는 'auto' | Time Series |
| YAS-005 | Max Value | number 타입일 때 최대값 | 텍스트 | "auto" | 숫자 또는 'auto' | Time Series |
| YAS-006 | Show Axis | 축 선 표시 여부 | 불리언 | true | true/false | Time Series |
| YAS-007 | Tick Count | 눈금 개수 | 숫자 | 5 | 2-20 | Time Series |
| YAS-008 | Tick Angle | 눈금 레이블 회전 각도 | 숫자 | 0 | -90 ~ 90 | Time Series |
| YAS-009 | Show Tick | 눈금 표시 여부 | 불리언 | true | true/false | Time Series |
| YAS-010 | Tick Size | 눈금 크기 | 숫자 | 6 | 0-20px | Time Series |
| YAS-011 | Label Position | 축 레이블 위치 | 선택 | insideRight | insideRight, insideTopRight, insideBottomRight | Time Series |

## 2. 차트별 전용 옵션 컴포넌트

### 2.1 Graph Styles (Time Series)

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| GS-001 | Style | 그래프 스타일 | 선택 | Line | Line, Area, Bar | Time Series |
| GS-002 | Line Width | Line 스타일 시 선 두께 | 숫자 | 1 | 0.5-10px | Time Series (Line, Area) |
| GS-003 | Fill Opacity | 채우기 투명도 | 숫자 | 35 | 0-100% | Time Series (Line, Area) |

### 2.2 Scatter Chart Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| SC-001 | Point Size | 데이터 포인트 크기 | 숫자 | 5 | 1-20px | Scatter Chart |
| SC-002 | Point Shape | 데이터 포인트 모양 | 선택 | circle | circle, cross, diamond, square, star | Scatter Chart |
| SC-003 | Point Fill | 포인트 내부 채우기 여부 | 불리언 | true | true/false | Scatter Chart |
| SC-004 | Show Regression Line | 회귀선 표시 여부 | 불리언 | false | true/false | Scatter Chart |
| SC-005 | Regression Type | 회귀선 유형 | 선택 | linear | linear, polynomial, exponential | Scatter Chart |
| SC-006 | XY Label | XY 좌표 레이블 표시 | 불리언 | false | true/false | Scatter Chart |

### 2.3 Radar Chart Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| RC-001 | Grid Shape | 그리드 모양 | 선택 | polygon | polygon, circle | Radar Chart |
| RC-002 | Axis Lines | 축 선 표시 여부 | 불리언 | true | true/false | Radar Chart |
| RC-003 | Fill Area | 영역 채우기 | 불리언 | true | true/false | Radar Chart |
| RC-004 | Fill Opacity | 영역 투명도 | 숫자 | 50 | 0-100% | Radar Chart |
| RC-005 | Show Data Points | 데이터 포인트 표시 | 불리언 | true | true/false | Radar Chart |
| RC-006 | Normalize Data | 데이터 정규화 | 불리언 | true | true/false | Radar Chart |

### 2.4 Radial Bar Chart Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| RB-001 | Start Angle | 시작 각도 | 숫자 | 0 | 0-360° | Radial Bar Chart |
| RB-002 | End Angle | 종료 각도 | 숫자 | 360 | 0-360° | Radial Bar Chart |
| RB-003 | Bar Size | 바 크기 | 숫자 | 10 | 1-50px | Radial Bar Chart |
| RB-004 | Inner Radius | 내부 반지름 비율 | 숫자 | 30 | 0-100% | Radial Bar Chart |
| RB-005 | Outer Radius | 외부 반지름 비율 | 숫자 | 100 | 0-100% | Radial Bar Chart |
| RB-006 | Corner Radius | 모서리 라운드 처리 | 숫자 | 0 | 0-10px | Radial Bar Chart |
| RB-007 | Show Background | 배경 표시 여부 | 불리언 | true | true/false | Radial Bar Chart |
| RB-008 | Background Color | 배경 색상 | 색상 선택기 | "#f5f5f5" | 유효한 색상 코드 | Radial Bar Chart |
| RB-009 | Use Gradient | 그라데이션 사용 여부 | 불리언 | false | true/false | Radial Bar Chart |
| RB-010 | Gradient Colors | 그라데이션 색상 범위 | 색상 배열 | ["#8884d8", "#82ca9d"] | 유효한 색상 코드 | Radial Bar Chart |

### 2.5 Text Component Options

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| TX-001 | Content Type | 텍스트 콘텐츠 유형 | 선택 | 사용자 정의 | 사용자 정의, 데이터 기반 | Text |
| TX-002 | Custom Content | 표시할 텍스트 내용 (사용자 정의 모드) | 텍스트 에디터 | 빈 문자열 | 최대 10000자 | Text |
| TX-003 | Data Source | 데이터 소스 설정 (데이터 기반 모드) | 데이터 소스 선택 | - | 유효한 데이터 소스 | Text |
| TX-004 | Data Operation | 데이터 처리 방식 | 선택 | 최신값 | 최신값, 최소값, 최대값, 평균, 합계, 개수 | Text |
| TX-005 | Format | 숫자 포맷 | 텍스트 | "0,0.00" | 유효한 형식 문자열 | Text |
| TX-006 | Prefix | 값 앞에 표시할 텍스트 | 텍스트 | 빈 문자열 | 최대 20자 | Text |
| TX-007 | Suffix | 값 뒤에 표시할 텍스트 | 텍스트 | 빈 문자열 | 최대 20자 | Text |
| TX-008 | Mode | 텍스트 편집 모드 | 선택 | markdown | markdown, html | Text |
| TX-009 | Font Size | 기본 폰트 크기 | 숫자 | 14 | 8-72px | Text |
| TX-010 | Text Align | 텍스트 정렬 | 선택 | left | left, center, right, justify | Text |
| TX-011 | Text Color | 텍스트 색상 | 색상 선택기 | "#000000" | 유효한 색상 코드 | Text |
| TX-012 | Link Target | 링크 타겟 설정 | 선택 | _blank | _blank, _self | Text |
| TX-013 | Conditional Format | 조건부 서식 사용 | 불리언 | false | true/false | Text |
| TX-014 | Conditions | 조건부 서식 규칙 | 복합 | [] | 조건, 색상, 아이콘 등 정의 | Text |

## 3. 데이터 소스 설정

### 3.1 전체 차트 공통 설정 (상단 배치)

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| CS-001 | 조회 기간 설정 | 차트에 표시할 데이터의 기간 범위 | 날짜 범위 | 최근 1년 | 유효한 날짜 범위 | 모든 차트 컴포넌트 |
| CS-002 | 조회 주기 선택 | 데이터 주기 | 선택 | 월간(M) | 일간(D), 월간(M), 분기(Q), 연간(A) | 모든 차트 컴포넌트 |

### 3.2 지표별 데이터 소스 설정 (하단 카드형 UI)

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| DS-001 | 데이터 출처 | 데이터 제공 출처 | 선택 | KOSIS | KOSIS, ECOS, OECD | 모든 차트 컴포넌트 |
| DS-002 | 지표 코드 | 조회할 지표 코드 | 텍스트 | 빈 문자열 | 유효한 지표 코드 | 모든 차트 컴포넌트 |
| DS-003 | 지표 검색 | 지표명/코드로 검색 | 검색 입력 | 빈 문자열 | - | 모든 차트 컴포넌트 |
| DS-004 | 시리즈 이름 | 차트에 표시될 시리즈 이름 | 텍스트 | 지표명 | 최대 30자 | 모든 차트 컴포넌트 |
| DS-005 | 시리즈 색상 | 차트에 표시될 시리즈 색상 | 색상 선택기 | 자동 할당 | 유효한 색상 코드 | 모든 차트 컴포넌트 |
| DS-006 | Y축 선택 | 시리즈가 표시될 Y축 (주/보조) | 선택 | Primary | Primary, Secondary | Time Series |
| DS-007 | 시리즈 스타일 | 개별 시리즈의 그래프 스타일 | 선택 | Line | Line, Area, Bar | Time Series |
| DS-008 | 데이터 변환 | 원본 데이터 변환 방식 | 선택 | 원본값 | 원본값, 변화율(전기대비), 변화율(전년동기대비), 누적값 | 모든 차트 컴포넌트 |
| DS-009 | 시리즈 제거 | 현재 시리즈 제거 | 버튼 | - | - | 모든 차트 컴포넌트 |
| DS-010 | 데이터 시차 | 시계열 데이터 시차 적용 | 숫자 | 0 | -12 ~ 12 (개월/분기/년) | Scatter Chart, Time Series |

### 3.3 시리즈 추가 기능

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| AS-001 | 시리즈 추가 | 새로운 데이터 시리즈 추가 | 버튼 | - | 최대 5개 시리즈 | 모든 차트 컴포넌트 |

### 3.4 기간별 비교 설정

| 기능 ID | 기능명 | 설명 | 입력 유형 | 기본값 | 제약조건 | 적용 가능 컴포넌트 |
|---------|--------|------|-----------|--------|----------|-------------------|
| CP-001 | 비교 활성화 | 기간 비교 기능 활성화 | 불리언 | false | true/false | Time Series, Bar Chart |
| CP-002 | 비교 기간 유형 | 비교할 기간 유형 | 선택 | 전년동기 | 전년동기, 이전기간, 사용자정의 | Time Series, Bar Chart |
| CP-003 | 사용자 정의 기간 | 사용자 정의 비교 기간 | 날짜 범위 | - | 유효한 날짜 범위 | Time Series, Bar Chart |
| CP-004 | 비교 시각화 방식 | 비교 결과 표시 방법 | 선택 | 중첩 | 중첩, 나란히, 차이값 | Time Series, Bar Chart |
| CP-005 | 기준기간 스타일 | 기준기간 시각화 스타일 | 복합 | { color: "#1f77b4", lineStyle: "solid" } | 색상, 선 스타일 등 | Time Series |
| CP-006 | 비교기간 스타일 | 비교기간 시각화 스타일 | 복합 | { color: "#ff7f0e", lineStyle: "dashed" } | 색상, 선 스타일 등 | Time Series |

## 4. 차트 컴포넌트별 활성화 옵션 영역

### 4.1 Time Series

- **활성화 옵션 영역**:
  - Panel Options
  - Tooltip Options
  - Legend Options
  - X-Axis Options
  - Y-Axis Options (Primary)
  - Y-Axis Options (Secondary)
  - Graph Styles (Time Series)
  - 데이터 소스 설정

### 4.2 Bar Chart

- **활성화 옵션 영역**:
  - Panel Options
  - Tooltip Options
  - Legend Options
  - X-Axis Options
  - Y-Axis Options (Primary)
  - 데이터 소스 설정

### 4.3 Text

- **활성화 옵션 영역**:
  - Panel Options
  - Text Component Options
  - 데이터 소스 설정 (데이터 기반 모드 선택 시)

### 4.4 Scatter Chart

- **활성화 옵션 영역**:
  - Panel Options
  - Tooltip Options
  - Legend Options
  - X-Axis Options
  - Y-Axis Options (Primary)
  - Scatter Chart Options
  - 데이터 소스 설정

### 4.5 Radar Chart

- **활성화 옵션 영역**:
  - Panel Options
  - Tooltip Options
  - Legend Options
  - Radar Chart Options
  - 데이터 소스 설정

### 4.6 Radial Bar Chart

- **활성화 옵션 영역**:
  - Panel Options
  - Tooltip Options
  - Legend Options
  - Radial Bar Chart Options
  - 데이터 소스 설정

## 5. 옵션 관계 및 의존성 규칙

### 5.1 컴포넌트 유형에 따른 옵션 활성화 규칙

| 컴포넌트 유형 | 규칙 ID | 규칙 설명 |
|--------------|---------|----------|
| 전체 | R-001 | Panel Options는 모든 컴포넌트에서 항상 활성화됨 |
| Time Series | R-TS-001 | Y-Axis (Secondary) 옵션은 YAS-001 (Visibility)가 true일 때만 나머지 필드가 활성화됨 |
| Time Series | R-TS-002 | Graph Style (GS-001)이 'Line'일 때만 Line 관련 옵션(GS-002) 활성화 |
| Time Series | R-TS-003 | Graph Style (GS-001)이 'Area'일 때만 Area 관련 옵션(GS-003) 활성화 |
| Time Series | R-TS-004 | 시리즈의 Y축 선택(DS-006)이 'Secondary'일 때는 해당 시리즈만 Secondary Y축에 표시됨 |
| Time Series | R-TS-005 | 데이터 시리즈의 개별 스타일(DS-007)은 해당 시리즈에만 적용됨 |

### 5.2 데이터 소스 의존성 규칙

| 규칙 ID | 규칙 설명 |
|---------|----------|
| R-DS-001 | 지표 코드(DS-002)는 선택된 데이터 출처(DS-001)에 따라 유효한 코드만 선택 가능 |
| R-DS-002 | 주기 선택(CS-002)은 선택된 모든 지표 코드가 지원하는 주기만 활성화 |
| R-DS-003 | KOSIS 출처 지표는 M, Q, A 주기만 지원, ECOS는 모든 주기(D, M, Q, A) 지원 |
| R-DS-004 | 일간(D) 데이터는 최대 1년 기간만 조회 가능, 다른 주기는 더 긴 기간 지원 |
| R-DS-005 | 조회 주기(CS-002)와 기간(CS-001)은 차트의 모든 시리즈에 공통 적용됨 |
| R-DS-006 | 시리즈 추가 시 상단의 공통 설정(조회 기간, 조회 주기)은 모든 시리즈에 동일하게 적용됨 |

## 6. UI 레이아웃 구성

### 6.1 UI 레이아웃

차트 컴포넌트 편집 화면은 다음과 같은 구조로 구성되며, shadcn/ui의 resizable 컴포넌트를 활용하여 각 영역이 사용자에 의해 리사이즈 가능합니다:

1. **상단 영역 (공통 설정)**
   - 차트 미리보기
   - 조회 기간 설정 (CS-001)
   - 조회 주기 선택 (CS-002)
   - 수직 방향으로 리사이즈 가능

2. **우측 사이드바 (옵션 패널)**
   - 모든 옵션 컴포넌트 그룹화
   - 컴포넌트 유형별 활성화된 옵션 영역만 표시
   - 수평 방향으로 리사이즈 가능

3. **하단 영역 (데이터 소스 설정)**
   - 지표별 카드 UI
   - 각 카드에는 데이터 출처, 지표 코드, 시리즈 이름, 시리즈 색상 등의 설정 포함
   - '+Add Query' 버튼으로 추가 지표 카드 생성 가능
   - 수직 방향으로 리사이즈 가능

### 6.2 시리즈 관리
- 각 지표(시리즈)는 하단 영역에서 카드 단위로 관리
- 최대 5개 시리즈까지 추가 가능
- 시리즈별로 색상, 이름, Y축 선택, 데이터 변환 방식 등 개별 설정 가능
- 시리즈의 추가/제거는 하단 영역에서 관리

## 7. 성능 및 제약사항

### 7.1 데이터 처리 제약사항

| 제약사항 ID | 설명 |
|------------|------|
| L-DATA-001 | Time Series/Bar Chart: 단일 차트에 최대 10,000개 데이터 포인트 처리 가능 |
| L-DATA-002 | Pie Chart: 최대 20개 조각 표시 가능, 초과 시 기본적으로 그룹화 |
| L-DATA-003 | Table: 단일 페이지에 최대 100행까지 표시, 초과 시 자동 페이지네이션 |
| L-DATA-004 | 다중 시리즈: 차트당 최대 5개 시리즈 지원 |

### 7.2 브라우저 호환성

| 호환성 ID | 설명 |
|-----------|------|
| C-BROWSER-001 | 지원 브라우저: Chrome 80+, Firefox 70+, Safari 13+, Edge 80+ |
| C-BROWSER-002 | IE 11 미지원 |
| C-BROWSER-003 | 모바일 브라우저 지원: iOS Safari 13+, Android Chrome 80+ |
| C-BROWSER-004 | 최소 화면 너비: 320px(모바일), 권장 화면 너비: 768px 이상 |

## 8. 구현 참고사항

### 8.1 기술 스택 참고사항

- shadcn/ui 컴포넌트를 기반으로 UI 구성
- recharts 라이브러리를 활용한 차트 구현
- 현재 recharts에서 지원하는 차트 유형으로 구현 범위 제한:
  - Time Series (LineChart, AreaChart, BarChart)
  - Bar Chart
  - Scatter Chart
  - Radar Chart
  - Radial Bar Chart

- 차트 유형 지원 관련 검토 사항:
  1. recharts에서 지원되지 않아 추후 별도 검토 필요:
     - Gauge
     - Bar Gauge
     - Heatmap
  
  2. recharts에서 지원되지만 현재 제공 예정인 데이터(기획서 부록 참조)로는 활용하기 어려워 별도 검토 필요:
     - Funnel Chart
     - Treemap
     - Sankey Chart

  3. 필요에 따라 다음 방향으로 확장 가능:
     - 기존 recharts 커스터마이징을 통한 기능 확장
     - 특수 목적 차트를 위한 보조 라이브러리(nivo, visx 등) 부분 도입

### 8.2 데이터 소스 관리

- 데이터 소스 설정은 차트 컴포넌트 전체에 대한 공통 설정(기간, 주기)과 시리즈별 개별 설정(출처, 지표)으로 분리
- 시리즈 추가/제거 시 차트 구성에 즉시 반영
- 동일한 차트 내 시리즈의 조회 기간과 주기는 항상 동일하게 유지
