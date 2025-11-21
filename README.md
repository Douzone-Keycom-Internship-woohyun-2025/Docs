<p align="center">
<img width="200" height="200" alt="TechLens로고" src="https://github.com/user-attachments/assets/3e8b41ac-733c-499a-b49b-bf32eee18ad8" />
</p>

# KNU x Douzone-Kicom Internship 2025

## 프로젝트 개요

데이터 분석, 기획, 개발, 시각화까지 전 과정을 경험하고  
실제 서비스 형태로 발전시키는 것을 목표로 합니다.  

본 프로젝트는 **더존ICT그룹 Kicom 개발본부**와  
**강원대학교 SW중심대학사업단**이 협력하여 진행한 인턴십 프로젝트입니다.

---

## 리포지토리 구조 (Organization)

본 프로젝트는 하나의 Organization 내 **3개의 리포지토리**로 구성되어 있습니다.

```
public-data-project/
├── Docs/       ← 기획서, 설계서, 분석 보고서 등 문서 저장소
├── Frontend/   ← React + TypeScript 기반 SPA
└── Backend/    ← Node.js + Express + PostgreSQL 기반 API 서버
```

> 담당: **심우현 (Fullstack)**  
> 기획 · 설계 · 프론트엔드 · 백엔드 전담

---

## 기술 스택 (Tech Stack)

| 구분 | 기술 스택 |
|------|------------|
| **Frontend** | React, TypeScript, TailwindCSS, Zustand, Chart.js |
| **Backend** | Node.js (Express), PostgreSQL, jwt, xml2js |
| **Database** | PostgreSQL 13+ |
| **Deployment** | Vercel (Frontend), Render (Backend) |
| **Data Source** | KIPRIS API (특허청 Open API, XML → JSON 변환 xml2js) |

---

## 일정

자세한 타임테이블은  
📄 **docs/timetable.md** 참고.

---

## 팀원 및 역할

| 이름 | 역할 | 담당 |
|------|-------|------|
| **심우현** | Fullstack | 전체 개발 (DB 설계 / API / UI / 배포) |
| **박효민 선임연구원** | Mentor | 프로젝트 및 백엔드 기술 멘토링 |
| **양태인 주임연구원** | Mentor | 개발 방향성 조언 |

---

# TechLens

> **특허청 KIPRIS API 기반 R&D 기술동향 분석 대시보드**  
> 기업의 기술집중도, 월별 출원 추이, 등록 현황 등을 시각화하는 분석 서비스

- 프로젝트 기간: **2025.10 – Douzone-Kicom Internship**
- 개발자: **심우현 (WooHyun Sim)** — 강원대학교 컴퓨터공학과

---

# 개요 (Overview)

**TechLens**는 특허청 **KIPRIS Open API**를 기반으로  
기업의 기술·연구개발 동향을 분석하고  
**IPC·월별 추이·등록현황 지표를 시각화**하는 특허 분석 플랫폼입니다.

---

# 프로젝트 배경

- 기술 경쟁력 확보가 기업 생존의 핵심
- 특허 데이터는 산업·기술 트렌드를 담은 핵심 지표
- KIPRIS 원본 데이터는 방대하고 복잡함 → 직접 분석 난이도 높음

➡ **TechLens는 특허 빅데이터 분석 장벽을 낮추는 것을 목표로 함**

---

# 문제 정의

| 문제 | 설명 |
|------|------|
| 방대한 특허 데이터 | 수작업으로는 분석 불가능 |
| 비효율적 데이터 접근 | KIPRIS XML 기반, 구조 파악 어려움 |
| 기술전략 리스크 | 실시간 분석 체계 부재 |

---

# 서비스 개요

| 항목 | 내용 |
|------|------|
| **서비스명** | TechLens |
| **형태** | SPA 기반 웹 서비스 |
| **핵심 기능** | IPC·출원추이·상태별 분석 / 검색 / 프리셋 / 관심특허 |
| **대상 사용자** | 기업, 분석가, 연구기획자 |

---

# 주요 기능 (Core Features)

### 1️⃣ IPC 기술 집중도 분석
- IPC 코드 기반 기술 분야 집중도 시각화  
- 상위 N개 기술 도메인 분석 가능

### 2️⃣ 월별 R&D 활동 추이 분석
- 연도·기간별 출원 건수 변화 시각화  
- 기술 투자 흐름 파악

### 3️⃣ 특허 상태(등록/거절/공개) 분석
- R&D 결과물의 효율성 파악  
- 등록 성공률 분석 가능

---

# 활용 데이터 (KIPRIS API)

| 항목 | 내용 |
|------|------|
| 제공 | 특허청(KIPO) |
| 형태 | XML → JSON(xml2js) |
| 주요 필드 | 출원번호, 출원일, 출원인, IPC, 초록, 등록상태 등 |
| 인증 | API Key 기반 |

---

# 시스템 아키텍처

현재 운영 중인 실제 아키텍처는 다음과 같다.

```
[ User ]
   ↓
[ Frontend (React + TS, Vercel) ]
   ↓ REST API
[ Backend (Node.js + Express, Render) ]
   ├─ xml2js 기반 KIPRIS API 연동
   ├─ 분석 로직 처리 (IPC 집계, 월별 집계 등)
   ↓
[ PostgreSQL 13+ ]
   ├─ Users
   ├─ Presets
   ├─ Favorite Patents
   ├─ IPC Subclass Map
   └─ Patent–IPC 매핑 테이블
```

---

# Docs 저장소 구성

Docs 저장소에는 **프로젝트 문서 전체**가 저장되어 있습니다.

```
Docs/
├── requirements/
│   └── TechLens_요구사항정의서V1.1.md
├── specs/
│   ├── TechLens_API명세서V1.1.md
│   └── TechLens_DB정의서V1.1.md
├── design/
│   └── TechLens기획설계서V1.0.pdf
└── timetable.md
```

### 포함 문서

| 문서 | 설명 |
|------|------|
| **요구사항정의서 V1.1** | 서비스 요구사항, 기능 정의 |
| **API 명세서 V1.1** | 전체 REST API 스펙 |
| **DB 정의서 V1.1 (PostgreSQL 기준)** | 테이블/컬럼/관계 정의 |
| **기획설계서** | 서비스 목적/배경/UI 흐름 등 |
| **timetable.md** | 프로젝트 일정 |

---

# 🧾 프로젝트 전체 구조 예시

```
TechLens/
├── Frontend/   ← React + TS
├── Backend/    ← Node + Express + PostgreSQL
└── Docs/       ← 모든 문서 (요구사항 / API / DB / 설계서)
```

---

# License

본 프로젝트는 **Douzone-Kicom Internship 2025** 교육 목적의 프로젝트입니다.  
KIPRIS API의 모든 권리는 **특허청(KIPO)**에 있습니다.

문서·설계·코드의 저작권은 **심우현 개인에게 귀속**되며,  
**Kicom 내부 사용 목적 범위에서만 활용이 허가**됩니다.

---

📎 참고 문서:  
[`/docs/TechLens기획설계서V1.0.pdf`](./docs/TechLens기획설계서V1.0.pdf)
