# 프로젝트 개요

**B사 AS 접수 및 관리 시스템 (기업 연계 프로젝트)**

- **설명**: B사 비즈니스 요구사항을 기반으로 AS 접수 및 주문 처리, 권한별 페이지 접근 제한 등 핵심 업무 흐름을 검증하는 **프론트엔드 단독 웹 애플리케이션**입니다. 백엔드 서버 없이 `Context API`와 브라우저 저장소(`LocalStorage`, `SessionStorage`)를 활용하여 역할 간 데이터 동기화와 데이터 영속성을 구현했습니다.
- **종류**: 웹 애플리케이션 (Client-side / 기업 보안 정책상 README 명세만 공개)
- **작업 기간**: 5일 (2026.07.20 ~ 2026.07.24)
- **팀원 구성**: 3인 (디자이너 1명, 퍼블리셔 1명, 프론트엔드 개발자 1명)
- **핵심 목표**: 고객 AS 접수 ➡️ 기사 배정 및 수락 ➡️ 고객 화면 상태 연동 흐름 기술 검증

---

## 🔄 애플리케이션 동작 흐름

### 1. 사용자 인증 및 접근 제어

```text
Login
   │
   ▼
SessionStorage (로그인 세션 유지)
   │
   ▼
UserContext (사용자 권한 상태 관리)
   │
   ▼
ProtectedRoute (접근 제어 라우트)
   ├── USER (일반 고객)
   ├── ENGINEER (정비기사)
   ├── MANUFACTURER (제조사)
   └── ADMIN (본사 관리자)
```

### 2. 역할 간 데이터 흐름 (Data Flow)

```text
[일반고객 USER] AS 접수 신청 (주소 API 연동 / 이미지 첨부)
      │
      ▼ (OrderContext 전역 상태 추가)
LocalStorage (브라우저 데이터 저장)
      │
      ▼ (담당 지역 기준 배정)
[정비기사 ENGINEER] 배정 대기 목록 조회 및 수락
      │
      ▼ (OrderContext 상태 업데이트: REQUESTED ➡️ ACCEPTED)
[일반고객 USER] 마이페이지 접수 내역에서 상태 변경 확인
```

---

## 🛠 기술 스택

- **Environment**: Node.js v18 이상 (로컬 구동 환경)
- **Framework**: React 19
- **Build Tool**: Vite 8
- **Routing**: React Router DOM 7
- **State Management**: React Context API
- **Storage**: LocalStorage, SessionStorage
- **Styling**: Vanilla CSS

---

## 👨‍💻 담당 역할 (Frontend Developer)

- **사용자 인증 및 접근 제어**: SessionStorage 기반 로그인 세션 유지 및 ProtectedRoute 구현으로 권한별 라우팅 처리
- **전역 상태 및 데이터 영속화**: Context API와 LocalStorage를 연동하여 AS 접수 신청, 기사 수락 등 비즈니스 데이터의 CRUD 및 새로고침 시 데이터 유지 구현
- **역할 간 데이터 연동**: 일반 고객의 AS 신청 건이 정비기사의 담당 구역에 맞춰 노출되고, 기사 수락 시 고객 마이페이지에 실시간 반영되는 데이터 흐름 구현
- **외부 API 및 파일 처리**: Daum 우편번호 서비스 연동 및 FileReader를 통한 업로드 이미지 Base64 변환 저장 처리
- **검색 및 필터링 기능**: 본사 대시보드(임직원 이름 검색 및 소속 필터) 및 제조사 대시보드(자재명 검색 및 제품군 필터) 구현
- **UI 컴포넌트 구조화**: 퍼블리셔의 HTML/CSS 산출물을 React 컴포넌트로 구조화하고 스타일 코드 통합

---

## 🔑 구현 기능 명세

**실제 동작하는 동적 기능**과 **데모용 정적 UI**를 구분하여 명시합니다.

### 🟢 실제 동작 기능 (Interactive Features)

- **권한별 라우팅 및 세션 관리**: SessionStorage를 활용한 로그인 세션 유지 및 권한별 네비게이션 헤더 분기 처리
- **AS 신청 및 데이터 영속화 (고객)**: Daum 우편번호 API 연동 주소 입력, 이미지 파일 Base64 변환 첨부, LocalStorage 저장 및 데이터 유지
- **배정 주문 수락 및 상태 연동 (기사)**: 정비기사 담당 지역에 접수된 주문 조회 및 수락 시 상태 변경 (`REQUESTED` ➡️ `ACCEPTED`) 연동
- **임직원 및 자재 검색/필터링**: 본사 관리자 페이지의 임직원 검색 및 소속 필터, 제조사 페이지의 자재명 검색 및 제품군 필터

### 🟡 데모 전용 기능 (Static UI Only)

- **정비기사 대시보드**: 오늘 완료 건수, 이번 달 수익 지표 등 KPI 데이터
- **제조사 대시보드**: 본사/센터별 재고 현황 및 자재 수량 데이터 시각화

---

## 📁 디렉토리 구조

```text
client/
├── public/                      # 로고, 아이콘 등 정적 리소스 파일
└── src/
    ├── components/
    │   ├── Header.jsx           # 사용자 권한(Role)에 맞춰 하위 헤더 컴포넌트를 분기 렌더링하는 메인 헤더
    │   ├── UserHeader.jsx       # 일반 고객용 네비게이션 헤더
    │   ├── AdminHeader.jsx      # 본사 관리자용 네비게이션 헤더
    │   ├── EngineerHeader.jsx   # 정비기사용 네비게이션 헤더
    │   ├── ManufacturerHeader.jsx # 제조사용 네비게이션 헤더
    │   └── Footer.jsx           # 공통 레이아웃 하단 푸터 UI
    ├── context/
    │   ├── userContext.jsx      # SessionStorage 연동, 로그인/로그아웃 및 권한 상태 분기 처리
    │   └── orderContext.jsx     # LocalStorage 연동, AS 접수 내역의 전역 C.R.U.D 처리 로직
    ├── data/
    │   ├── DUMMY_USERS.js       # 권한별(ADMIN, USER, ENGINEER, MANUFACTURER) 모의 계정 데이터
    │   ├── DUMMY_ORDERS.js      # 초기 AS 접수 내역 모의 데이터
    │   └── DUMMY_MATERIALS.js   # 제조사 대시보드용 모의 자재/재고 데이터
    ├── pages/
    │   ├── Index.jsx            # 서비스 랜딩 및 고객 전용 메인 페이지
    │   ├── Login.jsx            # 로그인 처리 폼 컴포넌트
    │   ├── Order.jsx            # AS 서비스 신청 접수 양식 폼 (입력값 검증 및 전역 상태 반영)
    │   ├── UserMyPage.jsx       # 일반 고객 마이페이지 (AS 신청 내역 조회 및 상태 업데이트)
    │   ├── AdminDashboard.jsx   # 본사 관리자 대시보드 (임직원 동적 검색/필터링)
    │   ├── EngineerMyPage.jsx   # 정비기사 마이페이지 (배정된 주문 수락 및 상태 업데이트)
    │   └── ManufacturerDashboard.jsx # 제조사 대시보드 (자재 관리 및 자재 마스터 검색/필터링)
    └── styles/
        └── common.css           # 전역 공통 스타일시트
```

---

## 🔑 테스트 계정 정보

| 구분 (권한)               | 아이디 (이메일)            | 비밀번호       | 시연 가능한 주요 기능                                        |
| :------------------------ | :------------------------- | :------------- | :----------------------------------------------------------- |
| **일반고객 (User)**       | `user@example.com`         | `user`         | AS 접수 신청(주소 API, 이미지 첨부), 신청 내역 조회/취소     |
| **정비기사 (Engineer)**   | `engineer1@example.com`    | `engineer1`    | 담당 구역 AS 대기 주문 조회, 주문 수락 (고객 화면 상태 연동) |
| **본사 (Admin)**          | `admin@example.com`        | `admin`        | 임직원/기사/제조사 목록 조회, 소속별 필터링 및 이름 검색     |
| **제조사 (Manufacturer)** | `manufacturer@example.com` | `manufacturer` | 본사/센터별 재고 현황 필터 및 자재명 검색                    |

---

## 🚀 Quick Start

```bash
cd client
npm install
npm run dev
```

(서버 구동 후 웹 브라우저에서 `http://localhost:5173` 접속)
