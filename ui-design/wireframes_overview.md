# E-Torch 와이어프레임 화면 설계

## 개요

E-Torch는 경제지표 데이터를 시각화하는 대시보드 서비스입니다. 본 문서는 모든 주요 화면의 와이어프레임을 정리하여 UI 설계의 전체적인 구조와 흐름을 한눈에 파악할 수 있도록 구성되었습니다.

## 목차

1. [기본 레이아웃](#1-기본-레이아웃)
2. [로그인 화면](#2-로그인-화면)
3. [대시보드 목록 화면](#3-대시보드-목록-화면)
4. [대시보드 상세 보기 화면](#4-대시보드-상세-보기-화면)
5. [대시보드 편집기 화면](#5-대시보드-편집기-화면)
6. [위젯 편집기 화면](#6-위젯-편집기-화면)
7. [옵션 컴포넌트](#7-옵션-컴포넌트)

---

## 1. 기본 레이아웃

기본 레이아웃은 모든 화면에서 공통으로 사용되는 구조를 정의합니다.

### 1.1 데스크톱 레이아웃 (1440×900)

**주요 구성 요소:**

- 상단 헤더 (로고, 브레드크럼, 사용자 프로필)
- 좌측 네비게이션 사이드바 (200px)
- 메인 콘텐츠 영역 (1240px × 670px)

![데스크톱 레이아웃](./wireframes.v2/0.%20기본%20레이아웃/desktop-layout-wireframe.svg)

### 1.2 태블릿 레이아웃 (1024×800)

**주요 구성 요소:**

- 상단 헤더 (로고, 브레드크럼, 사용자 프로필)
- 접힌 사이드바 (햄버거 메뉴로 토글)
- 메인 콘텐츠 영역 (1024px × 600px, 2열 그리드)

![태블릿 레이아웃](./wireframes.v2/0.%20기본%20레이아웃/tablet-layout-wireframe.svg)

### 1.3 모바일 레이아웃 (375×800)

**주요 구성 요소:**

- 상단 헤더 (로고, 햄버거 메뉴)
- 세로 스크롤 콘텐츠 영역 (375px × 520px)
- 하단 안전 영역 (모바일 제스처 고려)

![모바일 레이아웃](./wireframes.v2/0.%20기본%20레이아웃/mobile-layout-wireframe.svg)

---

## 2. 로그인 화면

SNS 로그인 기반의 간단한 인증 화면입니다.

### 2.1 데스크톱 로그인 (1440×900)

**주요 구성 요소:**

- 좌측: 로그인 폼 영역
- 우측: 배경 이미지 영역 (경제 차트/그래프)
- SNS 로그인 버튼 (Google, Naver, Kakao)

![데스크톱 로그인](./wireframes.v2/1.%20로그인%20화면/desktop-login-wireframe.svg)

### 2.2 태블릿 로그인 (1024×800)

**주요 구성 요소:**

- 중앙 정렬된 로그인 폼 컨테이너
- 상하단 배경 차트 영역
- 터치 최적화된 버튼 크기

![태블릿 로그인](./wireframes.v2/1.%20로그인%20화면/tablet-login-wireframe.svg)

### 2.3 모바일 로그인 (375×800)

**주요 구성 요소:**

- 세로 중심 레이아웃
- 터치 최적화된 큰 버튼 (56px 높이)
- 상하단 배경 차트 영역

![모바일 로그인](./wireframes.v2/1.%20로그인%20화면/mobile-login-wireframe.svg)

---

## 3. 대시보드 목록 화면

사용자의 대시보드를 관리하고 탐색하는 화면입니다.

### 3.1 데스크톱 대시보드 목록 (1440×900)

**주요 구성 요소:**

- 검색 및 필터 영역
- 대시보드 카드 그리드 (4×2 배치)
- 새 대시보드 생성 버튼
- 빠른 액션 버튼들

![데스크톱 대시보드 목록](./wireframes.v2/2.%20대시보드%20목록%20화면/desktop-dashboard_list-wireframe.svg)

### 3.2 태블릿 대시보드 목록 (1024×800)

**주요 구성 요소:**

- 간소화된 컨트롤 영역
- 대시보드 카드 그리드 (3×2 배치)
- 접힌 사이드바

![태블릿 대시보드 목록](./wireframes.v2/2.%20대시보드%20목록%20화면/tablet-dashboard_list-wireframe.svg)

### 3.3 모바일 대시보드 목록 (375×800)

**주요 구성 요소:**

- 탭 기반 네비게이션 (내 대시보드 / 대시보드 탐색)
- 세로 스크롤 카드 리스트
- 제스처 기반 인터랙션 (탭, 길게 누르기)

![모바일 대시보드 목록](./wireframes.v2/2.%20대시보드%20목록%20화면/mobile-dashboard_list-gesture_based-wireframe.svg)

---

## 4. 대시보드 상세 보기 화면

개별 대시보드를 조회하고 상호작용하는 화면입니다.

### 4.1 데스크톱 대시보드 상세 (1440×900)

**주요 구성 요소:**

- 대시보드 헤더 (제목, 설명, 메타데이터)
- 컨트롤 영역 (기간 설정, 새로고침, 공유/편집)
- 위젯 그리드 레이아웃 (3×2 배치)
- 상세한 경제지표 테이블

![데스크톱 대시보드 상세](./wireframes.v2/3.%20대시보드%20상세%20보기%20화면/desktop-dashboard_detail-wireframe.svg)

### 4.2 태블릿 대시보드 상세 (1024×800)

**주요 구성 요소:**

- 2열 그리드 레이아웃
- 터치 최적화된 컨트롤
- 접힌 사이드바

![태블릿 대시보드 상세](./wireframes.v2/3.%20대시보드%20상세%20보기%20화면/tablet-dashboard_detail-wireframe.svg)

### 4.3 모바일 대시보드 상세 (375×800)

**주요 구성 요소:**

- 미니멀한 컨트롤 영역
- 세로 스크롤 위젯 배치
- 플로팅 액션 버튼 (편집, 공유, 새로고침)

![모바일 대시보드 상세](./wireframes.v2/3.%20대시보드%20상세%20보기%20화면/mobile-dashboard_detail-wireframe.svg)

---

## 5. 대시보드 편집기 화면

대시보드를 생성하고 편집하는 화면입니다.

### 5.1 데스크톱 대시보드 편집기 (1440×900)

**주요 구성 요소:**

- 대시보드 헤더 (제목, 편집 모드 상태 표시)
- 컨트롤 영역 (위젯 추가, 기간 설정, 편집 도구, 저장/취소)
- 그리드 편집 모드 (3×3 배치, 드래그 앤 드롭)
- 우측 속성 패널 (선택된 위젯 정보 및 설정)

![데스크톱 대시보드 편집기](./wireframes.v2/4.%20대시보드%20편집기%20화면/desktop-dashboard_editor-wireframe.svg)

### 5.2 데스크톱 대시보드 편집기 - 위젯 추가 드롭다운 (1440×900)

**주요 구성 요소:**

- 위젯 추가 드롭다운 메뉴 (새 위젯 생성, 내 위젯에서 추가, 위젯 복사)
- 그리드 편집 모드 (배경 처리)
- 우측 속성 패널 (비활성화 상태)

![데스크톱 위젯 추가 드롭다운](./wireframes.v2/4.%20대시보드%20편집기%20화면/desktop-dashboard_editor-widget_add_dropdown-wireframe.svg)

### 5.3 데스크톱 대시보드 설정 모달 (1440×900)

**주요 구성 요소:**

- 모달 오버레이
- 기본 정보 설정 (제목, 설명)
- 공개 설정 및 메타데이터
- 저장/취소 버튼

![데스크톱 대시보드 설정 모달](./wireframes.v2/4.%20대시보드%20편집기%20화면/desktop-dashboard_editor-settings_modal-wireframe.svg)

### 5.4 태블릿 대시보드 편집기 (1024×800)

**주요 구성 요소:**

- 2열 그리드 편집 모드
- 터치 최적화된 위젯 조작
- 접힌 사이드바

![태블릿 대시보드 편집기](./wireframes.v2/4.%20대시보드%20편집기%20화면/tablet-dashboard_editor-wireframe.svg)

### 5.5 모바일 대시보드 편집기 (375×800)

**주요 구성 요소:**

- 세로 스크롤 편집 모드
- 터치 최적화된 위젯 컨트롤
- 드래그 핸들 및 터치 가이드

![모바일 대시보드 편집기](./wireframes.v2/4.%20대시보드%20편집기%20화면/mobile-dashboard_editor-wireframe.svg)

### 5.6 모바일 날짜 설정 - iOS 네이티브 (375×800)

**주요 구성 요소:**

- 하단 슬라이드업 날짜 선택기
- iOS 휠 피커 (년/월/일)
- 네이티브 스타일 인터페이스

![모바일 iOS 날짜 선택기](./wireframes.v2/4.%20대시보드%20편집기%20화면/mobile-dashboard_editor-date_settings-custom-ios_native_date_picker-wireframe.svg)

### 5.7 모바일 날짜 설정 - Android 네이티브 (375×800)

**주요 구성 요소:**

- 전체화면 모달 캘린더
- Material Design 스타일
- 월/년 탐색 + 날짜 그리드

![모바일 Android 날짜 선택기](./wireframes.v2/4.%20대시보드%20편집기%20화면/mobile-dashboard_editor-date_settings-custom-android_native_date_picker_wireframe.svg)

---

## 6. 위젯 편집기 화면

차트형 및 텍스트형 위젯을 생성하고 편집하는 화면입니다.

### 6.1 데스크톱 위젯 편집기 (1440×900)

**주요 구성 요소:**

- 좌측: 위젯 미리보기 + 데이터 소스 설정
- 우측: 옵션 패널 (아코디언 방식)
- 리사이즈 가능한 패널 구조

![데스크톱 위젯 편집기](./wireframes.v2/7.%20위젯%20편집기%20화면/desktop_widget_editor-wireframe.svg)

### 6.2 태블릿 위젯 편집기 (1024×800)

**주요 구성 요소:**

- 좌측: 위젯 미리보기 + 데이터 소스 설정
- 우측: 옵션 패널 (압축된 레이아웃)
- 접힌 사이드바

![태블릿 위젯 편집기](./wireframes.v2/7.%20위젯%20편집기%20화면/tablet_widget_editor-wireframe.svg)

### 6.3 모바일 위젯 편집기 - 데이터 탭 (375×800)

**주요 구성 요소:**

- 탭 기반 네비게이션 (데이터 설정 / 옵션 설정)
- 압축된 차트 미리보기
- 시리즈 카드 방식 데이터 설정

![모바일 위젯 편집기 - 데이터 탭](./wireframes.v2/7.%20위젯%20편집기%20화면/mobile_widget_editor-data_tab-wireframe.svg)

### 6.4 모바일 위젯 편집기 - 옵션 탭 (375×800)

**주요 구성 요소:**

- 세로 스크롤 옵션 패널
- 아코디언 방식 옵션 그룹
- 터치 최적화된 입력 컨트롤

![모바일 위젯 편집기 - 옵션 탭](./wireframes.v2/7.%20위젯%20편집기%20화면/mobile_widget_editor-options_tab-wireframe.svg)

---

## 7. 옵션 컴포넌트

위젯 편집기에서 사용되는 개별 옵션 컴포넌트들입니다.

### 7.1 Panel Options (380×250)

**주요 구성 요소:**

- 제목 설정
- 설명 설정
- 배경 투명도 설정

![Panel Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/panel-options-component.svg)

### 7.2 Tooltip Options (380×300)

**주요 구성 요소:**

- 툴팁 모드 설정
- 최대 너비 설정
- 커서 스타일 및 두께 설정

![Tooltip Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/tooltip_options_component.svg)

### 7.3 Legend Options (380×220)

**주요 구성 요소:**

- 범례 표시 여부
- 레이아웃 방향 설정
- 수평/수직 정렬 설정

![Legend Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/legend-options-component.svg)

### 7.4 X-Axis Options (380×510)

**주요 구성 요소:**

- X축 사용 여부 토글
- 데이터 키 및 축 유형 설정
- 눈금 관련 상세 설정

![X-Axis Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/x_axis_options_component.svg)

### 7.5 Y-Axis Options (380×580)

**주요 구성 요소:**

- Y축 사용 여부 토글
- 단위 및 축 유형 설정
- 눈금 및 레이블 위치 설정

![Y-Axis Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/y_axis_options_component.svg)

### 7.6 Y-Axis Secondary Options (380×600)

**주요 구성 요소:**

- 보조 Y축 사용 여부 토글
- 보조축 전용 설정 옵션
- 기본값 복원 기능

![Y-Axis Secondary Options](./wireframes.v2/7.%20위젯%20편집기%20화면/options/y_axis_secondary_options-component.svg)

### 7.7 Graph Styles (380×400)

**주요 구성 요소:**

- 시리즈별 스타일 매핑
- 전역 기본 설정
- 색상 팔레트 및 애니메이션 설정

![Graph Styles](./wireframes.v2/7.%20위젯%20편집기%20화면/options/graph_styles_component.svg)

---

## 와이어프레임 파일 목록

### 기본 레이아웃

- `wireframes.v2/0. 기본 레이아웃/desktop-layout-wireframe.svg`
- `wireframes.v2/0. 기본 레이아웃/tablet-layout-wireframe.svg`
- `wireframes.v2/0. 기본 레이아웃/mobile-layout-wireframe.svg`

### 로그인 화면

- `wireframes.v2/1. 로그인 화면/desktop-login-wireframe.svg`
- `wireframes.v2/1. 로그인 화면/tablet-login-wireframe.svg`
- `wireframes.v2/1. 로그인 화면/mobile-login-wireframe.svg`

### 대시보드 목록 화면

- `wireframes.v2/2. 대시보드 목록 화면/desktop-dashboard_list-wireframe.svg`
- `wireframes.v2/2. 대시보드 목록 화면/tablet-dashboard_list-wireframe.svg`
- `wireframes.v2/2. 대시보드 목록 화면/mobile-dashboard_list-gesture_based-wireframe.svg`

### 대시보드 상세 보기 화면

- `wireframes.v2/3. 대시보드 상세 보기 화면/desktop-dashboard_detail-wireframe.svg`
- `wireframes.v2/3. 대시보드 상세 보기 화면/tablet-dashboard_detail-wireframe.svg`
- `wireframes.v2/3. 대시보드 상세 보기 화면/mobile-dashboard_detail-wireframe.svg`

### 대시보드 편집기 화면

- `wireframes.v2/4. 대시보드 편집기 화면/desktop-dashboard_editor-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/desktop-dashboard_editor-widget_add_dropdown-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/desktop-dashboard_editor-settings_modal-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/tablet-dashboard_editor-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/mobile-dashboard_editor-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/mobile-dashboard_editor-date_settings-custom-ios_native_date_picker-wireframe.svg`
- `wireframes.v2/4. 대시보드 편집기 화면/mobile-dashboard_editor-date_settings-custom-android_native_date_picker_wireframe.svg`

### 위젯 편집기 화면

- `wireframes.v2/7. 위젯 편집기 화면/desktop_widget_editor-wireframe.svg`
- `wireframes.v2/7. 위젯 편집기 화면/tablet_widget_editor-wireframe.svg`
- `wireframes.v2/7. 위젯 편집기 화면/mobile_widget_editor-data_tab-wireframe.svg`
- `wireframes.v2/7. 위젯 편집기 화면/mobile_widget_editor-options_tab-wireframe.svg`

### 옵션 컴포넌트

- `wireframes.v2/7. 위젯 편집기 화면/options/panel-options-component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/tooltip_options_component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/legend-options-component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/x_axis_options_component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/y_axis_options_component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/y_axis_secondary_options-component.svg`
- `wireframes.v2/7. 위젯 편집기 화면/options/graph_styles_component.svg`
