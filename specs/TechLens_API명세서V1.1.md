<p align="center">
<img width="200" height="200" alt="TechLens로고" src="https://github.com/user-attachments/assets/3e8b41ac-733c-499a-b49b-bf32eee18ad8" />
</p>

# TechLens API 명세서 · v1.2.0

Base URL: `https://techlens-backend-develop.onrender.com`  
Auth: **JWT Bearer**  
응답 형식: **JSON**

---

## 목차

- [1. 개요](#1-개요)
- [2. 인증](#2-인증)
  - [2.1 Authorization 헤더](#21-authorization-헤더)
  - [2.2 토큰 획득](#22-토큰-획득)
  - [2.3 AccessToken / RefreshToken 정책](#23-accesstoken--refreshtoken-정책)
  - [2.4 로그아웃 정책](#24-로그아웃-정책)
- [3. API 엔드포인트](#3-api-엔드포인트)
  - [3.1 Users (사용자 인증)](#31-users-사용자-인증)
  - [3.2 Presets (프리셋 관리)](#32-presets-프리셋-관리)
  - [3.3 Patents (특허 검색)](#33-patents-특허-검색)
  - [3.4 Summary (요약 분석)](#34-summary-요약-분석)
  - [3.5 Favorites (관심특허 관리)](#35-favorites-관심특허-관리)
  - [3.6 Health Check](#36-health-check)
- [4. 데이터 타입](#4-데이터-타입)
- [5. HTTP 상태 코드](#5-http-상태-코드)
- [6. 설계 원칙](#6-설계-원칙)
- [7. 메타](#7-메타)

## 🔎 API 엔드포인트 개요 (Quick Navigation)

아래 표에서 API 종류별 주요 기능을 한눈에 확인하고,  
각 항목을 클릭하여 상세 스펙으로 바로 이동할 수 있습니다.

### 1) Users (사용자 인증)
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|-------|
| 회원가입 | 신규 사용자 등록 | `POST /users/signup` | [바로가기](#311-회원가입) |
| 로그인 | Access / Refresh 발급 | `POST /users/login` | [바로가기](#312-로그인) |
| 로그아웃 | Refresh 제거 | `POST /users/logout` | [바로가기](#313-로그아웃) |
| 토큰 재발급 | AccessToken 갱신 | `POST /users/refresh` | [바로가기](#314-토큰재발급) |

---

### 2) Presets (프리셋 관리)
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|------|
| 프리셋 생성 | 검색 조건 저장 | `POST /presets` | [바로가기](#321-프리셋-생성) |
| 프리셋 목록 조회 | 저장된 목록 반환 | `GET /presets` | [바로가기](#322-프리셋-목록-조회) |
| 프리셋 상세 조회 | 프리셋 단건 정보 | `GET /presets/{id}` | [바로가기](#323-프리셋-상세-조회) |
| 프리셋 수정 | 부분 업데이트 지원 | `PATCH /presets/{id}` | [바로가기](#324-프리셋-수정) |
| 프리셋 삭제 | 프리셋 삭제 | `DELETE /presets/{id}` | [바로가기](#325-프리셋-삭제) |

---

### 3) Patents (특허 검색)
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|------|
| 기본 검색 | 출원인 + 날짜 기반 검색 | `POST /patents/search/basic` | [바로가기](#331-기본-검색) |
| 상세 검색 | 특허명·상태·기간 복합 검색 | `POST /patents/search/advanced` | [바로가기](#332-상세-검색) |
| 특허 상세 조회 | 출원번호 기반 상세 조회 | `GET /patents/{applicationNumber}` | [바로가기](#333-특허-상세-조회) |

---

### 4) Summary (요약 분석)
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|-------|
| 요약 분석 | 기간 전체 특허 통계 분석 | `GET /summary` | [바로가기](#41-요약-분석) |

---

### 5) Favorites (관심특허)
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|--------|
| 관심특허 추가 | 즐겨찾기 저장 | `POST /favorites` | [바로가기](#351-관심특허-추가) |
| 관심특허 목록 조회 | 전체 목록 반환 | `GET /favorites` | [바로가기](#352-관심특허-목록-조회) |
| 관심특허 단건 조회 | 특정 특허 즐겨찾기 상태 조회 | `GET /favorites/{applicationNumber}` | [바로가기](#353-관심특허-상세-조회) |
| 관심특허 삭제 | 즐겨찾기 해제 | `DELETE /favorites/{applicationNumber}` | [바로가기](#354-관심특허-제거) |

---

### 6) Health Check
| 기능 | 설명 | 엔드포인트 | 이동 |
|------|------|-------------|--------|
| 서버 상태 확인 | 백엔드 & DB 연결 체크 | `GET /health` | [바로가기](#36-health-check) |

---

## 1. 개요

| 항목           | 값                                                         |
|----------------|------------------------------------------------------------|
| Base URL       | `https://techlens-backend-develop.onrender.com`           |
| 인증 방식      | JWT Bearer Token                                           |
| API 버전       | 2.1.0                                                      |
| DB             | PostgreSQL 14+                                             |
| 주요 연동      | KIPRIS Open API (`xml2js` 파싱)                             |
| 총 엔드포인트  | 17개 (Users, Presets, Patents, Summary, Favorites, Health) |

> 현재 배포는 URL 버전 프리픽스(`/api/v1`) 없이 **루트 경로**에 라우팅됩니다.  
> 예) `/users/login`, `/presets`, `/patents/search/basic`, `/summary`, `/favorites`

---

## 2. 인증

### 2.1 Authorization 헤더

모든 보호된(Protected) API는 다음과 같이 AccessToken을 요구합니다.

    Authorization: Bearer <ACCESS_TOKEN>

---

### 2.2 토큰 획득

1. `POST /users/signup`  
2. `POST /users/login`  

응답의 `data.accessToken`, `data.refreshToken` 을 추출하여 사용합니다.

예시 응답 구조:

    {
      "status": "success",
      "message": "로그인 성공",
      "data": {
        "user": {
          "user_tblkey": 1,
          "email": "user@techlens.net",
          "adddate": "2025-11-11T23:08:29.608Z"
        },
        "accessToken": "xxx.yyy.zzz",
        "refreshToken": "rrr.sss.ttt"
      }
    }

---

### 2.3 AccessToken / RefreshToken 정책

- 로그인/회원가입 성공 시:
  - `accessToken` (만료: 1시간)
  - `refreshToken` (만료: 7일)
- `refreshToken`:
  - 서버 DB(`refresh_tokens` 테이블)에 저장
  - 만료 시 또는 로그아웃 시 삭제
- AccessToken 만료 후:
  - `POST /users/refresh` 로 새로운 AccessToken 발급
  - 이 때, 요청 바디에 `refreshToken` 필수

예시 요청:

    POST /users/refresh
    Content-Type: application/json

    {
      "refreshToken": "rrr.sss.ttt"
    }

예시 응답:

    {
      "status": "success",
      "message": "토큰 재발급 성공",
      "data": {
        "accessToken": "새로운AccessToken"
      }
    }

---

### 2.4 로그아웃 정책

- `POST /users/logout` 호출 시:
  - 요청 바디의 `refreshToken`을 기준으로 DB에서 해당 토큰 삭제
  - 더 이상 해당 `refreshToken`으로 AccessToken 재발급 불가
  - 기존 AccessToken은 만료 시점까지만 유효

오류 예시:

    {
      "status": "fail",
      "message": "refreshToken 필요"
    }

또는 저장되지 않은 토큰 사용 시:

    {
      "status": "fail",
      "message": "refresh token invalid"
    }

---

## 3. API 엔드포인트

### 공통 응답 포맷

모든 엔드포인트는 아래 형태 중 하나로 응답합니다.

성공:

    {
      "status": "success",
      "message": "설명 메시지 (선택)",
      "data": { ... }             // 필요 시 포함
    }

실패:

    {
      "status": "fail",
      "message": "에러 원인 메시지"
    }

서버 에러(예시):

    {
      "status": "error",
      "message": "서버 에러 메시지"
    }

---

### 3.1 Users (사용자 인증)

#### 3.1.1 회원가입

- 메서드/경로: **POST** `/users/signup`
- 인증: 불필요
- 응답: **201 Created**

Request Body:

    {
      "email": "user@techlens.net",
      "password": "abcd1234!"
    }

Response:

    {
      "status": "success",
      "message": "회원가입 성공",
      "data": {
        "user": {
          "user_tblkey": 2,
          "email": "user@techlens.net",
          "adddate": "2025-11-12T21:36:16.430Z"
        },
        "accessToken": "xxx.yyy.zzz",
        "refreshToken": "rrr.sss.ttt"
      }
    }

에러 케이스 예시:
- 비밀번호 8자 미만 → `"비밀번호는 최소 8자 이상"`
- 기존 이메일 존재 → `"이미 가입된 이메일입니다."`

---

#### 3.1.2 로그인

- 메서드/경로: **POST** `/users/login`
- 인증: 불필요
- 응답: **200 OK**

Request Body:

    {
      "email": "user@techlens.net",
      "password": "abcd1234!"
    }

Response:

    {
      "status": "success",
      "message": "로그인 성공",
      "data": {
        "user": {
          "user_tblkey": 1,
          "email": "user@techlens.net",
          "adddate": "2025-11-11T23:08:29.608Z"
        },
        "accessToken": "xxx.yyy.zzz",
        "refreshToken": "rrr.sss.ttt"
      }
    }

에러 케이스 예시:
- 이메일/비밀번호 불일치 → `"이메일 또는 비밀번호 오류"`

---

#### 3.1.3 로그아웃

- 메서드/경로: **POST** `/users/logout`
- 인증: AccessToken 필수 (헤더)
- 응답: **200 OK**

Request Body:

    {
      "refreshToken": "rrr.sss.ttt"
    }

Response:

    {
      "status": "success",
      "message": "로그아웃 완료"
    }

---

#### 3.1.4 토큰재발급

- 메서드/경로: **POST** `/users/refresh`
- 인증: 불필요 (RefreshToken으로 인증)
- 응답: **200 OK**

Request Body:

    {
      "refreshToken": "rrr.sss.ttt"
    }

Response:

    {
      "status": "success",
      "message": "토큰 재발급 성공",
      "data": {
        "accessToken": "새로운AccessToken"
      }
    }

---

### 3.2 Presets (프리셋 관리)

프리셋은 검색 조건 템플릿입니다.  
목록 조회는 요약 정보(설명 제외), 단건 조회는 상세 정보(설명 포함)를 제공합니다.

#### 3.2.1 프리셋 생성

- 메서드/경로: **POST** `/presets`
- 인증: AccessToken 필수
- 응답: **201 Created**

Request Body:

    {
      "presetName": "삼성 2024년 분석",
      "applicant": "삼성전자",
      "startDate": "20240101",
      "endDate": "20241231",
      "description": "삼성의 2024년 특허 분석"
    }

Response:

    {
      "status": "success",
      "message": "프리셋이 생성되었습니다.",
      "data": {
        "id": 1,
        "presetName": "삼성 2024년 분석",
        "applicant": "삼성전자",
        "startDate": "20240101",
        "endDate": "20241231",
        "description": "삼성의 2024년 특허 분석",
        "createdAt": "2025-11-03T10:30:00Z"
      }
    }

---

#### 3.2.2 프리셋 목록 조회

- 메서드/경로: **GET** `/presets?skip=0&limit=10`
- 인증: AccessToken 필수
- 응답: **200 OK**

Query 파라미터:

- `skip` (선택, 기본 0)
- `limit` (선택, 기본 10, 최대 100)

Response (description 제외):

    {
      "status": "success",
      "message": "프리셋 목록 조회 성공",
      "data": {
        "total": 5,
        "page": 1,
        "pageSize": 10,
        "totalPages": 1,
        "presets": [
          {
            "id": 1,
            "presetName": "삼성 2024년 분석",
            "applicant": "삼성전자",
            "startDate": "20240101",
            "endDate": "20241231",
            "createdAt": "2025-11-03T10:30:00Z"
          },
          {
            "id": 2,
            "presetName": "LG 2023년 분석",
            "applicant": "LG전자",
            "startDate": "20230101",
            "endDate": "20231231",
            "createdAt": "2025-11-02T14:15:00Z"
          }
        ]
      }
    }

---

#### 3.2.3 프리셋 상세 조회

- 메서드/경로: **GET** `/presets/{presetId}`
- 인증: AccessToken 필수
- 응답: **200 OK**

Response:

    {
      "status": "success",
      "data": {
        "id": 1,
        "presetName": "삼성 2024년 분석",
        "applicant": "삼성전자",
        "startDate": "20240101",
        "endDate": "20241231",
        "description": "삼성의 2024년 특허 분석",
        "createdAt": "2025-11-03T10:30:00Z"
      }
    }

존재하지 않는 프리셋 ID일 경우:

    {
      "status": "fail",
      "message": "프리셋을 찾을 수 없습니다."
    }

---

#### 3.2.4 프리셋 수정

- 메서드/경로: **PATCH** `/presets/{presetId}`
- 인증: AccessToken 필수
- 응답: **200 OK**

Request Body (부분 업데이트 가능):

    {
      "presetName": "삼성 2024년 최종",
      "description": "수정된 설명"
    }

Response:

    {
      "status": "success",
      "message": "프리셋이 수정되었습니다."
    }

---

#### 3.2.5 프리셋 삭제

- 메서드/경로: **DELETE** `/presets/{presetId}`
- 인증: AccessToken 필수
- 응답: **204 No Content**

실패(없는 ID) 시:

    {
      "status": "fail",
      "message": "프리셋을 찾을 수 없습니다."
    }

---

### 3.3 Patents (특허 검색)

KIPRIS Open API의 `getAdvancedSearch` 엔드포인트를 사용하여 특허를 검색합니다.  
페이지당 기본 20건, 정렬 및 즐겨찾기 정보가 포함됩니다.

#### 3.3.1 기본 검색

- 메서드/경로: **POST** `/patents/search/basic`
- 인증: AccessToken 필수
- 응답: **200 OK**

Request Body:

    {
      "applicant": "삼성전자",
      "startDate": "20240101",
      "endDate": "20241231",
      "page": 1,
      "sort": "desc"
    }

- `sort`: `"desc"` (기본) 또는 `"asc"`
- `page`: 1 이상의 정수

Response (요약):

    {
      "status": "success",
      "message": "특허 검색 성공",
      "data": {
        "total": 635,
        "page": 1,
        "totalPages": 32,
        "patents": [
          {
            "applicationNumber": "1020250146315",
            "applicantName": "삼성전자주식회사",
            "applicationDate": "20251010",
            "ipcNumber": "G06F 3/06|G06F 11/10|G06F 9/451",
            "registerStatus": "공개",
            "inventionTitle": "개선된 SSD 신뢰성",
            "astrtCont": "요약 내용...",
            "bigDrawing": "http://plus.kipris.or.kr/...",
            "drawing": "http://plus.kipris.or.kr/...",
            "mainIpcCode": "G06F",
            "ipcKorName": "알 수 없음 또는 사전 매핑된 한글명",
            "isFavorite": false
          }
        ]
      }
    }

- `mainIpcCode`: IPC 메인 코드(예: G06F)
- `ipcKorName`: IPC 코드에 대한 한글명 (내부 사전 매핑 기반)
- `isFavorite`: 현재 로그인 사용자의 관심특허 여부

---

#### 3.3.2 상세 검색

- 메서드/경로: **POST** `/patents/search/advanced`
- 인증: AccessToken 필수
- 응답: **200 OK**

Request Body:

    {
      "inventionTitle": "AI 기반",
      "applicant": "삼성전자",
      "startDate": "20240101",
      "endDate": "20241231",
      "registerStatus": "공개",
      "page": 1,
      "sort": "desc"
    }

- `registerStatus`: 한글 상태값 (`공개`, `등록`, `취하`, `소멸`, `포기`, `무효`, `거절`)  
  내부에서 `A/C/F/G/I/J/R` 코드로 매핑되어 KIPRIS에 전달됩니다.

Response 구조는 기본 검색과 동일합니다.

---

#### 3.3.3 특허 상세 조회

- 메서드/경로: **GET** `/patents/{applicationNumber}`
- 인증: AccessToken 필수
- 응답: **200 OK**

Response (예시):

    {
      "status": "success",
      "message": "특허 상세 조회 성공",
      "data": {
        "applicationNumber": "1020250146315",
        "inventionTitle": "개선된 SSD 신뢰성",
        "applicantName": "삼성전자주식회사",
        "applicationDate": "20251010",
        "openDate": "20251021",
        "openNumber": "1020250151330",
        "publicationDate": null,
        "publicationNumber": null,
        "registerDate": null,
        "registerNumber": null,
        "ipcNumber": "G06F 3/06|G06F 11/10|G06F 9/451",
        "registerStatus": "공개",
        "astrtCont": "솔리드 스테이트 드라이브(SSD)의 신뢰성을 향상시키는 방법...",
        "bigDrawing": "http://plus.kipris.or.kr/...",
        "drawing": "http://plus.kipris.or.kr/...",
        "mainIpcCode": "G06F",
        "ipcKorName": "알 수 없음 또는 사전 매핑된 한글명",
        "isFavorite": true
      }
    }

존재하지 않는 출원번호일 경우:

    {
      "status": "fail",
      "message": "특허 정보를 찾을 수 없습니다."
    }

---

### 3.4 Summary (요약 분석)

요약 분석은 지정된 기간 동안 한 출원인(applicant)의 특허를 모두 수집하여 통계를 산출합니다.

- 내부적으로 KIPRIS `getAdvancedSearch`를 **페이지 단위로 모두 순회**하여 수천 건 이상 데이터를 수집할 수 있음
- 상태 분포, 월별 출원 추이, IPC 상위 Top5, 최근 특허 3건 등 다양한 지표 제공

#### 3.4.1 요약 분석 조회

- 메서드/경로: **GET** `/summary`
- 인증: AccessToken 필수
- 응답: **200 OK**

두 가지 방식 중 하나로 호출:

1) 프리셋 기반:

    GET /summary?presetId=1
    Authorization: Bearer <ACCESS_TOKEN>

2) 직접 조건 기반:

    GET /summary?applicant=카카오&startDate=20250501&endDate=20251114
    Authorization: Bearer <ACCESS_TOKEN>

- `presetId` 를 제공하면 해당 프리셋의 `applicant`, `startDate`, `endDate`를 사용
- 프리셋 없이 직접 조회 시 `applicant`, `startDate`, `endDate` 모두 필수

조건 부족 시:

    {
      "status": "fail",
      "message": "presetId 또는 applicant + startDate + endDate 를 모두 입력해야 합니다."
    }

Response:

    {
      "status": "success",
      "message": "요약 분석 완료",
      "data": {
        "applicant": "카카오",
        "period": {
          "startDate": "20250501",
          "endDate": "20251114"
        },
        "totalCount": 123,
        "statusCount": {
          "공개": 50,
          "등록": 30,
          "기타": 43
        },
        "statusPercent": {
          "공개": 40.65,
          "등록": 24.39,
          "기타": 34.96
        },
        "monthlyTrend": [
          { "month": "2025-05", "count": 10 },
          { "month": "2025-06", "count": 15 }
        ],
        "topIPC": [
          { "code": "G06F", "count": 15 },
          { "code": "H04L", "count": 12 }
        ],
        "avgMonthlyCount": 15.50,
        "recentPatents": [
          {
            "applicationNumber": "1020250000001",
            "title": "AI 기반 검색 시스템",
            "date": "20251110",
            "ipcMain": "G06F",
            "status": "공개"
          },
          {
            "applicationNumber": "1020250000002",
            "title": "챗봇 응답 최적화 장치",
            "date": "20251109",
            "ipcMain": "G06N",
            "status": "등록"
          }
        ]
      }
    }

---

### 3.5 Favorites (관심특허 관리)

특허 상세/검색 결과에서 관심 특허를 저장하고, 별도 목록에서 관리합니다.  
출원번호(`applicationNumber`) 기준으로 중복을 차단합니다.

#### 3.5.1 관심특허 추가

- 메서드/경로: **POST** `/favorites`
- 인증: AccessToken 필수
- 응답: **201 Created**

Request Body (요약 형태):

    {
      "applicationNumber": "1020250146315",
      "inventionTitle": "개선된 SSD 신뢰성",
      "applicantName": "삼성전자주식회사",
      "abstract": "요약 내용...",
      "applicationDate": "20251010",
      "openNumber": "1020250151330",
      "publicationNumber": null,
      "publicationDate": null,
      "registerNumber": null,
      "registerDate": null,
      "registerStatus": "공개",
      "drawingUrl": "http://plus.kipris.or.kr/...",
      "mainIpcCode": "G06F",
      "ipcNumber": "G06F 3/06|G06F 11/10|G06F 9/451"
    }

Response:

    {
      "status": "success",
      "message": "즐겨찾기가 추가되었습니다.",
      "data": {
        "id": 10,
        "applicationNumber": "1020250146315",
        "inventionTitle": "개선된 SSD 신뢰성",
        "applicantName": "삼성전자주식회사",
        "abstract": "요약 내용...",
        "applicationDate": "20251010",
        "openNumber": "1020250151330",
        "publicationNumber": null,
        "publicationDate": null,
        "registerNumber": null,
        "registerDate": null,
        "registerStatus": "공개",
        "drawingUrl": "http://plus.kipris.or.kr/...",
        "mainIpcCode": "G06F",
        "createdAt": "2025-11-21T12:00:00.000Z"
      }
    }

에러 케이스 예시:
- 필수 필드 누락 → `"출원번호는 필수입니다."`, `"발명의 명칭은 필수입니다."`, `"출원인명은 필수입니다."`, `"출원일은 필수입니다."`
- 중복 추가 시 → `"이미 즐겨찾기에 추가된 특허입니다."`

---

#### 3.5.2 관심특허 목록 조회

- 메서드/경로: **GET** `/favorites`
- 인증: AccessToken 필수
- 응답: **200 OK**

(현재 구현상 별도 페이지네이션 없이 전체 반환)

Response:

    {
      "status": "success",
      "message": "즐겨찾기 목록 조회 성공",
      "data": {
        "total": 3,
        "favorites": [
          {
            "id": 10,
            "applicationNumber": "1020250146315",
            "inventionTitle": "개선된 SSD 신뢰성",
            "applicantName": "삼성전자주식회사",
            "abstract": "요약 내용...",
            "applicationDate": "20251010",
            "openNumber": "1020250151330",
            "publicationNumber": null,
            "publicationDate": null,
            "registerNumber": null,
            "registerDate": null,
            "registerStatus": "공개",
            "drawingUrl": "http://plus.kipris.or.kr/...",
            "mainIpcCode": "G06F",
            "createdAt": "2025-11-21T12:00:00.000Z"
          }
        ]
      }
    }

---

#### 3.5.3 관심특허 상세 조회

- 메서드/경로: **GET** `/favorites/{applicationNumber}`
- 인증: AccessToken 필수
- 응답: **200 OK**

Response:

    {
      "status": "success",
      "data": {
        "id": 10,
        "applicationNumber": "1020250146315",
        "inventionTitle": "개선된 SSD 신뢰성",
        "applicantName": "삼성전자주식회사",
        "abstract": "요약 내용...",
        "applicationDate": "20251010",
        "openNumber": "1020250151330",
        "publicationNumber": null,
        "publicationDate": null,
        "registerNumber": null,
        "registerDate": null,
        "registerStatus": "공개",
        "drawingUrl": "http://plus.kipris.or.kr/...",
        "mainIpcCode": "G06F",
        "createdAt": "2025-11-21T12:00:00.000Z"
      }
    }

없는 경우:

    {
      "status": "fail",
      "message": "즐겨찾기를 찾을 수 없습니다."
    }

---

#### 3.5.4 관심특허 제거

- 메서드/경로: **DELETE** `/favorites/{applicationNumber}`
- 인증: AccessToken 필수
- 응답: **204 No Content**

삭제 대상이 없을 경우:

    {
      "status": "fail",
      "message": "즐겨찾기를 찾을 수 없습니다."
    }

---

### 3.6 Health Check

- 메서드/경로: **GET** `/health`
- 인증: 불필요
- 목적: 서버 및 DB 연결 상태 확인
- 응답 예시:

    {
      "status": "성공",
      "db": "연결"
    }

(실제 구현에 따라 메시지는 일부 상이할 수 있음)

---

## 4. 데이터 타입

### 4.1 날짜 형식

| 형식     | 예시        | 사용처                 |
|----------|------------|------------------------|
| YYYYMMDD | 20240101   | API 요청 파라미터      |
| ISO 8601 | 2024-01-01 | DB 및 응답 `createdAt` |

---

### 4.2 등록 상태 (registerStatus)

| 상태   | 설명      |
|--------|-----------|
| 공개   | 공개      |
| 출원   | 출원      |
| 심사중 | 심사 중   |
| 등록   | 등록 완료 |
| 취하   | 취하      |
| 소멸   | 소멸      |
| 포기   | 포기      |
| 무효   | 무효      |
| 거절   | 거절      |

---

### 4.3 IPC 코드

- 특허 분류 코드, 파이프(`|`)로 여러 개 표현
- 예: `"G06F 3/06|G06F 11/10|G06F 9/451"`
- Summary 분석에서는 **앞 4자리 메인 코드** 기준으로 통계 집계  
  예: `"G06F"`

---

## 5. HTTP 상태 코드

| 코드 | 설명                                     |
|------|------------------------------------------|
| 200  | 요청 성공                                |
| 201  | 리소스 생성 성공                         |
| 204  | 리소스 삭제 성공 (응답 바디 없음)       |
| 400  | 잘못된 요청 (필수 파라미터 누락 등)     |
| 401  | 인증 실패 (토큰 없음/만료/유효하지 않음)|
| 404  | 리소스를 찾을 수 없음                    |
| 500  | 서버 내부 오류                           |

---

## 6. 설계 원칙

- **JWT + RefreshToken DB 관리**
  - AccessToken: 짧은 수명(1시간), Header 기반 인증
  - RefreshToken: DB 저장, 로그아웃/만료 관리
- **프리셋 설계**
  - 목록: 요약 정보 위주
  - 상세: 설명 포함 전체 정보
- **특허 검색**
  - KIPRIS `getAdvancedSearch`를 단일 통합 API로 사용
  - 기본/상세 검색은 파라미터 조합으로 분리
  - 프론트엔드 페이지네이션 `/patents/search/*`에서 지원
- **요약 분석**
  - 한 번의 분석 요청으로 전체 기간의 특허를 수집
  - 상태/월별/IPC 등 다양한 차원에서 통계 제공
- **관심특허**
  - 출원번호 기준 유니크 관리
  - 검색/상세 결과와 연동하여 UX 일관성 유지
- **에러 처리**
  - 비즈니스 에러: `BadRequestError`, `UnauthorizedError`, `NotFoundError` 등 커스텀 에러 사용
  - 모든 컨트롤러는 `next(err)`로 에러를 통일 처리

---

## 7. 메타

- **Version**: 1.2.0  
- **Date**: 2025-11-21  
- **작성자**: 심우현 (KNU / Kicom Internship)  
- **비고**:
  - PostgreSQL 기반 최종 구현 코드 반영
  - Favorites 전체 구현 및 목록/상세/삭제 명세 반영
  - Summary 분석 로직(IPC Top5, 최근 3건, 월별 추이) 상세화
  - Patents 검색 결과 구조에 `mainIpcCode`, `ipcKorName`, `isFavorite` 필드 명시

© 2025 TechLens Project. All rights reserved.  
(Kicom × KNU 인턴십 문서 / 무단 복제·배포 금지)
