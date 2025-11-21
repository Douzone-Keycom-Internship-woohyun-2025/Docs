# TechLens 데이터베이스 정의서 (V1.1)

본 문서는 **Kicom × KNU TechLens 프로젝트**의 데이터베이스 구조를 정의한 문서입니다.  
본 정의서는 **PostgreSQL 환경**에서 작성되었으며,  
KIPRIS Open API 기반의 특허 검색 및 분석 서비스를 위한 핵심 테이블 구조를 포함합니다.

---

## 목차
- [ERD 다이어그램](#erd-다이어그램)
- [1. 개요](#1-개요)
- [2. 테이블 목록](#2-테이블-목록)
- [3. 테이블 정의서](#3-테이블-정의서)
  - [3.1 USERS (사용자 계정 정보)](#31-users-사용자-계정-정보)
  - [3.2 REFRESH_TOKENS (리프레시 토큰 저장)](#32-refresh_tokens-리프레시-토큰-저장)
  - [3.3 PRESETS (프리셋 정보)](#33-presets-프리셋-정보)
  - [3.4 FAVORITE_PATENTS (관심특허 정보)](#34-favorite_patents-관심특허-정보)
  - [3.5 PATENT_IPC_SUBCLASS_MAP (특허–Subclass 매핑 정보)](#35-patent_ipc_subclass_map-특허–subclass-매핑-정보)
  - [3.6 IPC_SUBCLASS_MAP (IPC Subclass 사전)](#36-ipc_subclass_map-ipc-subclass-사전)
- [4. 설계 요약](#4-설계-요약)
- [5. 주요 변경사항 (V1.0 → V1.1)](#5-주요-변경사항-v10--v11)
- [6. PostgreSQL 생성 스크립트](#6-postgresql-생성-스크립트)
- [메타](#메타)

---

## ERD 다이어그램

<img width="1232" height="990" alt="BookStore (4)" src="https://github.com/user-attachments/assets/ffa9a21c-5320-40d0-9537-fcb15d906dd1" />

---

## 1. 개요

| 항목 | 내용 |
|------|------|
| 데이터베이스명 | TECHLENS_DB |
| DBMS | PostgreSQL 14 이상 |
| 문자 인코딩 | UTF8 |
| 정규화 수준 | 3NF (제3정규형) |
| 주요 기능 | 사용자, 프리셋, 관심특허, IPC 매핑, RefreshToken |
| 테이블 수 | 6개 |

---

## 2. 테이블 목록

| No | 테이블명 | 설명 |
|----|-----------|------|
| 1 | USERS | 사용자 계정 |
| 2 | REFRESH_TOKENS | 리프레시 토큰 저장 |
| 3 | PRESETS | 검색 프리셋 |
| 4 | FAVORITE_PATENTS | 관심특허 |
| 5 | PATENT_IPC_SUBCLASS_MAP | 특허–Subclass 매핑 |
| 6 | IPC_SUBCLASS_MAP | Subclass 사전 |

---

## 3. 테이블 정의서

---

# 3.1 USERS (사용자 계정 정보)

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| user_tblkey | SERIAL | PK | 사용자 ID |
| email | VARCHAR(255) | UNIQUE, NOT NULL | 로그인 이메일 |
| password_hash | VARCHAR(255) | NOT NULL | bcrypt 해시 |
| adddate | TIMESTAMP | DEFAULT NOW() | 생성일 |

---

# 3.2 REFRESH_TOKENS (리프레시 토큰 저장)

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| user_tblkey | INTEGER | PK, FK | 사용자 ID |
| refresh_token | TEXT | NOT NULL | RefreshToken |
| expires_at | TIMESTAMPTZ | NOT NULL | 만료 시각 |

---

# 3.3 PRESETS (프리셋 정보)

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| preset_tblkey | SERIAL | PK | 프리셋 ID |
| user_tblkey | INTEGER | FK | 사용자 |
| preset_name | VARCHAR(255) | NOT NULL | 프리셋 이름 |
| applicant | VARCHAR(255) | NOT NULL | 회사명 |
| start_date | VARCHAR(8) | NOT NULL | 시작일 |
| end_date | VARCHAR(8) | NOT NULL | 종료일 |
| description | VARCHAR(500) | NULL | 설명 |
| adddate | TIMESTAMP | DEFAULT NOW() | 생성일 |

---

# 3.4 FAVORITE_PATENTS (관심특허 정보 · 4개 컬럼 추가 반영)

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| patent_tblkey | SERIAL | PK | 관심특허 ID |
| user_tblkey | INTEGER | FK | 사용자 |
| application_number | VARCHAR(30) | NOT NULL | 출원번호 |
| invention_title | VARCHAR(500) | NOT NULL | 발명명 |
| applicant_name | VARCHAR(255) | NOT NULL | 출원인명 |
| abstract | TEXT | NULL | 초록 |
| application_date | VARCHAR(8) | NOT NULL | 출원일 |
| publication_date | VARCHAR(8) | NULL | 공개일 |
| register_date | VARCHAR(8) | NULL | 등록일 |
| register_status | VARCHAR(50) | NULL | 상태 |
| drawing_url | VARCHAR(500) | NULL | 도면 URL |
| open_number | VARCHAR(30) | NULL | 공개번호 |
| publication_number | VARCHAR(30) | NULL | 공고번호 |
| register_number | VARCHAR(30) | NULL | 등록번호 |
| main_ipc_code | VARCHAR(10) | NULL | 메인 IPC 코드 |
| adddate | TIMESTAMP | DEFAULT NOW() | 저장일 |

**제약조건**
- UNIQUE(user_tblkey, application_number)
- 사용자 삭제 시 CASCADE

---

# 3.5 PATENT_IPC_SUBCLASS_MAP

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| mapping_tblkey | SERIAL | PK | 매핑 ID |
| patent_tblkey | INTEGER | FK | 관심특허 |
| ipc_subclass | VARCHAR(10) | FK | Subclass 코드 |

---

# 3.6 IPC_SUBCLASS_MAP

| 컬럼 | 타입 | 조건 | 설명 |
|------|--------|--------|--------|
| ipc_subclass | VARCHAR(10) | PK | Subclass 코드 |
| kor_name | VARCHAR(255) | NOT NULL | 한글명 |

---

## 4. 설계 요약
- RefreshToken 저장 방식  
- FK + UNIQUE로 무결성 확보  
- 관심특허 ↔ Subclass 매핑 구조  
- 확장성 높은 구조

---

## 5. 주요 변경사항 (V1.0 → V1.1)

| 항목 | V1.0 | V1.1 |
|------|------|------|
| JWT_BLACKLIST | 사용 | 제거 |
| RefreshToken | 없음 | 추가 |
| FAVORITE_PATENTS | IPC 필드 없음 | **4개 IPC 관련 필드 추가** |

---

## 6. PostgreSQL 생성 스크립트

```sql
CREATE TABLE users (
  user_tblkey    SERIAL PRIMARY KEY,
  email          VARCHAR(255) UNIQUE NOT NULL,
  password_hash  VARCHAR(255) NOT NULL,
  adddate        TIMESTAMP DEFAULT NOW()
);

CREATE TABLE refresh_tokens (
  user_tblkey     INTEGER PRIMARY KEY,
  refresh_token   TEXT NOT NULL,
  expires_at      TIMESTAMPTZ NOT NULL,
  FOREIGN KEY (user_tblkey) REFERENCES users(user_tblkey) ON DELETE CASCADE
);

CREATE TABLE presets (
  preset_tblkey  SERIAL PRIMARY KEY,
  user_tblkey    INTEGER NOT NULL,
  preset_name    VARCHAR(255) NOT NULL,
  applicant      VARCHAR(255) NOT NULL,
  start_date     VARCHAR(8) NOT NULL,
  end_date       VARCHAR(8) NOT NULL,
  description    VARCHAR(500),
  adddate        TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY (user_tblkey) REFERENCES users(user_tblkey) ON DELETE CASCADE
);

CREATE TABLE favorite_patents (
  patent_tblkey       SERIAL PRIMARY KEY,
  user_tblkey         INTEGER NOT NULL,
  application_number  VARCHAR(30) NOT NULL,
  invention_title     VARCHAR(500) NOT NULL,
  applicant_name      VARCHAR(255) NOT NULL,
  abstract            TEXT,
  application_date    VARCHAR(8) NOT NULL,
  publication_date    VARCHAR(8),
  register_date       VARCHAR(8),
  register_status     VARCHAR(50),
  drawing_url         VARCHAR(500),
  open_number         VARCHAR(30),
  publication_number  VARCHAR(30),
  register_number     VARCHAR(30),
  main_ipc_code       VARCHAR(10),
  adddate             TIMESTAMP DEFAULT NOW(),
  FOREIGN KEY (user_tblkey) REFERENCES users(user_tblkey) ON DELETE CASCADE,
  UNIQUE (user_tblkey, application_number)
);

CREATE TABLE ipc_subclass_map (
  ipc_subclass  VARCHAR(10) PRIMARY KEY NOT NULL,
  kor_name      VARCHAR(255) NOT NULL
);

CREATE TABLE patent_ipc_subclass_map (
  mapping_tblkey  SERIAL PRIMARY KEY,
  patent_tblkey   INTEGER NOT NULL,
  ipc_subclass    VARCHAR(10) NOT NULL,
  FOREIGN KEY (patent_tblkey) REFERENCES favorite_patents(patent_tblkey) ON DELETE CASCADE,
  FOREIGN KEY (ipc_subclass) REFERENCES ipc_subclass_map(ipc_subclass) ON DELETE CASCADE,
  UNIQUE (patent_tblkey, ipc_subclass)
);
```

---

## 메타

```
Version: 1.1  
Updated: IPC 필드 4종 추가 반영  
작성자: 심우현 (KNU × Kicom Internship)

© 2025 TechLens Project. All rights reserved.
본 문서는 Kicom × KNU 인턴십 프로그램의 일부이며 무단 복제·배포를 금합니다.
```
