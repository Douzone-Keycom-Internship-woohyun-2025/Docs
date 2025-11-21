# TechLens 요구사항정의서 (V1.2)

## 1. 개요
| 항목 | 내용 |
| :--- | :--- |
| **프로젝트명** | TechLens (테크렌즈) |
| **버전** | 1.2 (수정) |
| **작성일자** | 2025-11-21 |
| **작성자** | 심우현 (KNU / Kicom Internship) |
| **DBMS** | PostgreSQL 13 이상 |
| **Frontend** | React + TypeScript + Zustand + TailwindCSS + Chart.js |
| **Backend** | Node.js (Express) + Raw Query(pg) + xml2js |
| **배포 환경** | Frontend(Vercel) / Backend(Render) |
| **API 연동** | KIPRIS Open API (getAdvancedSearch) |
| **문서 목적** | 개발·테스트·시연에 필요한 모든 요구사항 정의 |

---

## 2. 시스템 개요
TechLens는 **KIPRIS Open API의 특허 메타데이터(XML)** 기반으로  
경쟁사의 **R&D 기술 동향을 분석·시각화**하는 웹 대시보드 서비스입니다.

Frontend는 SPA 기반 UI·차트 렌더링을 담당하며, Backend는 XML 파싱, 통계 연산, 데이터 저장/조회 및 인증 관리를 수행합니다.

---

## 3. 주요 기능 요약
| 기능 구분 | 기능명 | 설명 |
| :--- | :--- | :--- |
| **핵심 분석 (3)** | IPC 기술 집중도 분석 | IPC 코드 기반 기술 비중 시각화 |
|  | R&D 추세 분석 | 월/분기별 출원량 시계열 분석 |
|  | R&D 결과 분석 | 등록/공개/거절 상태 분포 분석 |
| **검색/저장 (2)** | 프리셋 관리 | 검색 조건 저장·조회·수정·삭제 |
|  | 관심특허 관리 | 특정 특허 즐겨찾기 CRD |
| **지원 기능 (3)** | IPC 사전 | Subclass → 한글 기술명 제공 |
|  | 사용자 인증 | JWT Access + Refresh Token |
|  | **모바일 대응(Responsive UI)** | 모든 페이지 UI가 모바일에 자동 최적화 |

---

## 4. 시스템 구조
| 계층 | 구성 요소 | 역할 |
| :--- | :--- | :--- |
| **Frontend** | React SPA / TailwindCSS / Zustand / Chart.js | 화면·차트·상태관리 |
| **Backend** | Express + pg(raw) + xml2js | API, 인증, 데이터 분석 |
| **Database** | PostgreSQL | 사용자·프리셋·관심특허·IPC 사전 데이터 |
| **External API** | KIPRIS | 특허 XML 데이터 제공 |
| **Deployment** | Vercel / Render | CI/CD & 운영 |
| **Logging** | winston + morgan | 로그 저장 및 추적 |

---

## 5. 주요 요구사항 정의

### 5.1 기능 요구사항 (Functional Requirements)

| ID | 항목 | 요구사항 |
| :--- | :--- | :--- |
| **FR-01** | 회원가입 | 이메일·비밀번호 입력 → bcrypt 해시 저장 |
| **FR-02** | 로그인 | JWT Access/Refresh 발급 |
| **FR-03** | 토큰 재발급 | RefreshToken 검증 후 AccessToken 재발급 |
| **FR-04** | 프리셋 생성 | applicant, 기간, 설명 기반 저장 |
| **FR-05** | 프리셋 목록 조회 | 사용자별 프리셋 불러오기 |
| **FR-06** | 프리셋 상세 조회 | presetId 기반 단일 조회 |
| **FR-07** | 프리셋 수정 | 이름/설명/기간 수정 |
| **FR-08** | 프리셋 삭제 | presetId로 삭제 |
| **FR-09** | 관심특허 등록 | 출원번호 기준 중복 방지 후 저장 |
| **FR-10** | 관심특허 목록 | 특허 상세 정보 & 정렬/검색 |
| **FR-11** | 관심특허 삭제 | applicationNumber로 삭제 |
| **FR-12** | IPC 사전 조회 | subclass → kor_name 제공 |
| **FR-13** | 요약 분석(Summary) | 월별 출원량, 상태 비율, IPC 분포 계산·반환 |
| **FR-14** | KIPRIS XML 파싱 | xml2js로 JSON 변환 및 데이터 표준화 |
| **FR-15** | 에러 처리 | 공통 에러 핸들러 적용 |
| **FR-16** | **모바일 UI 대응** | PC·Tablet·Mobile 반응형 UI 지원 |

---

### 5.2 비기능 요구사항 (Non-Functional Requirements)

| ID | 항목 | 요구사항 |
| :--- | :--- | :--- |
| **NFR-01** | 보안 | 비밀번호 해시 / JWT 만료 / RefreshToken 저장 |
| **NFR-02** | 성능 | KIPRIS 호출 포함 평균 3초 이내 응답 |
| **NFR-03** | 운영 안정성 | Render 재시작에도 자동 복구 |
| **NFR-04** | 확장성 | 분석 모듈 확장 및 IPC 확장 고려 |
| **NFR-05** | 유지보수성 | Raw SQL 기반 구조로 쿼리 제어 용이 |
| **NFR-06** | 로그 관리 | winston 기반 Log Rotation |
| **NFR-07** | 캐싱 | summary 일부 메모리 캐싱(5분) |
| **NFR-08** | 무결성 | FK/UNIQUE 제약 준수 |
| **NFR-09** | 호환성 | Node 20+, PostgreSQL 13+ |
| **NFR-10** | **반응형 웹** | Tailwind 기반 반응형 레이아웃 적용 |

---

## 6. 데이터 흐름 요약

### 🔹 Summary 분석 흐름
1. React → Axios로 /summary 요청  
2. presetId 유무 판단 후 검색 조건 결정  
3. Backend → KIPRIS API 호출(XML)  
4. xml2js로 JSON 파싱  
5. 출원일 기준 월별 정규화  
6. 등록/거절/공개 상태값 정규화  
7. IPC 코드 집계  
8. FE에서 바로 Chart.js로 사용 가능한 형태로 반환  

### 🔹 관심특허 흐름
- 중복 검사 후 저장  
- subclass 배열 기반 IPC 매핑 테이블 저장  
- 삭제 시 CASCADE 적용  

---

## 7. 시스템 아키텍처

### ✔ Frontend (Vercel)
- Zustand로 전역 상태관리  
- Axios 인터셉터 기반 JWT 처리  
- Tailwind 반응형 UI  
- Chart.js 기반 시각화  

### ✔ Backend (Render)
- Express 계층 구조 (Router → Controller → Service → Repository)  
- xml2js 기반 XML 파싱  
- Raw SQL(pg) 기반 DB 접근  
- RefreshToken 저장 및 재발급 인증 구조  

### ✔ Database (PostgreSQL)
- USERS  
- REFRESH_TOKENS  
- PRESETS  
- FAVORITE_PATENTS  
- PATENT_IPC_SUBCLASS_MAP  
- IPC_SUBCLASS_MAP  

---

## 8. 제약사항 및 고려사항
- KIPRIS API는 XML-only → 반드시 JSON 변환 필요  
- API 호출 제한(초당 5회) 존재  
- Render 무료 서버 특성상 콜드스타트 가능  
- IPC 매핑은 Subclass까지만 제공  
- Redis 등 외부 캐싱은 사용하지 않음  

---

## 9. 향후 확장 계획
| 항목 | 설명 |
| :--- | :--- |
| 경쟁사 비교 페이지 | IPC·트렌드 비교 분석 |
| 국가별 분석 | 출원 국가별 기술 집중도 |
| AI IPC 자동 분류 | NLP 기반 IPC 예측 |
| 대시보드 공유 | 프리셋 및 분석 공유 기능 |
| KIPRIS 추가 API 연동 | 인용·피인용·키워드 분석 |

---

**Version:** 1.2  
**Date:** 2025-11-21  
**Author:** 심우현 (KNU / Kicom Internship)
