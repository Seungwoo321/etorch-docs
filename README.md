# E-Torch: 경제지표 대시보드 서비스

E-Torch는 다양한 출처(KOSIS, ECOS, OECD)의 경제지표 데이터를 통합하여 시각화하는 대시보드 서비스입니다. 복잡한 경제 데이터를 누구나 쉽게 이해하고 활용할 수 있도록 직관적인 시각화를 제공합니다.

## 기술 스택

- **프레임워크**: React 19, Next.js 15
- **모노레포**: Turborepo, pnpm
- **상태 관리**: Zustand 5, TanStack Query 5
- **UI 프레임워크**: Shadcn/UI, Tailwind CSS 4
- **차트 시각화**: Recharts 2.15.3+
- **폼 관리**: React Hook Form 7, Zod 3
- **타입 관리**: TypeScript 5.5+
- **테스트**: Vitest, Testing Library, Playwright

## 프로젝트 구조

```bash
e-torch/
├── apps/                # 애플리케이션
│   ├── web/            # 웹 애플리케이션
│   └── storybook/      # 컴포넌트 문서화
│
└── packages/            # 공유 패키지
    ├── eslint-config/  # ESLint 설정
    ├── core/           # 타입, 상수, 인터페이스
    ├── ui/             # UI 컴포넌트
    ├── charts/         # 차트 컴포넌트
    ├── dashboard/      # 대시보드 컴포넌트
    ├── data-sources/   # 데이터 소스 연동
    ├── state/          # 상태 관리
    ├── utils/          # 유틸리티 함수
    └── server-api/     # API 경로 및 서버 액션
```

## 시작하기

### 개발 환경 설정

```bash
# 저장소 클론
git clone https://github.com/your-org/e-torch.git
cd e-torch

# 의존성 설치
pnpm install

# 개발 서버 실행
pnpm dev
```

### 빌드 및 배포

```bash
# 프로젝트 빌드
pnpm build

# 테스트 실행
pnpm test

# 린트 검사
pnpm lint
```

## 주요 명령어

```bash
# 새 패키지 생성
pnpm --filter @e-torch/cli create-package

# Storybook 실행
pnpm --filter @e-torch/ui storybook

# 특정 앱 빌드
pnpm --filter @e-torch/web build
```

## 문서 안내

### 기획 문서

- [서비스 기획서](./product-spec.md): 서비스 개요, 타겟 사용자, 핵심 기능

### UI/UX 문서

- [디자인 시스템](./ui-design/design-system.md): 색상, 타이포그래피, 컴포넌트 가이드라인
- [UI 요구사항](./ui-design/ui-requirements.md): UI 컴포넌트 상세 요구사항
- [사용자 흐름](./ui-design/user-flows.md): 주요 사용자 경험 흐름
- [와이어프레임](./ui-design/wireframes/README.md): UI 레이아웃 설계

### 프론트엔드 아키텍처 문서

- [아키텍처 개요](./frontend/architecture.md): 전체 아키텍처 설계
- [핵심 컴포넌트](./frontend/components/core-components.md): 주요 컴포넌트 설계
- [데이터 흐름](./frontend/data-flow.md): API 연동 및 데이터 처리
- [라우팅 구조](./frontend/routing.md): 페이지 구조 및 라우팅
- [상태 관리](./frontend/state-management.md): 상태 관리 전략
- [파일 목록](./frontend/file-list.md): 구현 파일 목록 및 구조

## 핵심 패키지 가이드

### @e-torch/ui

Shadcn/UI 기반의 재사용 가능한 UI 컴포넌트 라이브러리입니다.

```tsx
import { Button } from '@/e-torch/ui/components';
```

### @e-torch/charts

차트 렌더링 및 편집 기능을 제공합니다.

```tsx
import { TimeSeriesChart } from '@/e-torch/charts/components';
```

### @e-torch/dashboard

대시보드 그리드 및 관리 기능을 제공합니다.

```tsx
import { DashboardGrid } from '@/e-torch/dashboard/components';
```

### @e-torch/data-sources

다양한 경제 데이터 소스와의 통합 및 데이터 처리 로직을 제공합니다.

```tsx
import { useIndicatorData } from '@/e-torch/data-sources/hooks';
```

## 아키텍처 핵심 원칙

1. **모듈성**: 기능별 독립적 패키지화
2. **확장성**: 새 차트 유형, 데이터 소스 쉽게 추가 가능
3. **재사용성**: 공통 컴포넌트 추출
4. **성능**: 대량 데이터 처리 최적화
5. **접근성**: WCAG 표준 준수
6. **서버/클라이언트 분리**: Next.js App Router 아키텍처 활용

## 브랜치 전략

- `main`: 프로덕션 배포 브랜치
- `develop`: 개발 통합 브랜치
- `feature/*`: 기능 개발 브랜치
- `release/*`: 릴리즈 준비 브랜치
- `hotfix/*`: 긴급 수정 브랜치

## 로드맵

프로젝트 로드맵은 [여기](./ROADMAP.md)에서 확인할 수 있습니다.

### 주요 개발 계획

- [x] 기획: 완료
- [x] 설계: 완료
- [ ] 백엔드 개발: 모킹 서버 및 데이터 마이그레이션 초기단계 완료, API 기능 확장 및 안정화 진행 중
- [ ] 프론트엔드 개발: 진행 중
- [ ] 통합 테스트: 예정
- [ ] MVP 출시: 2025-06-20 목표

## 저작권 고지

© 2025 E-Torch. All Rights Reserved.
이 소프트웨어와 관련 문서는 개인 저작물로, 저작권자의 명시적 허가 없이 복제, 수정, 배포가 금지됩니다.
