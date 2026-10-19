<div align="center">

# 최한민 · Backend Developer

**사용자 인증부터 데이터 저장과 조회까지, 서비스의 흐름을 구현합니다.**

`Java` · `Spring Boot` · `Python` · `FastAPI` · `PostgreSQL`

[Email](mailto:hanmin8434@naver.com) · [GitHub](https://github.com/chmdb) · [BPT 기여 내역](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pulls?q=is%3Apr+author%3Achmdb)

</div>

---

## About

Spring Boot와 FastAPI를 사용해 API와 데이터베이스를 개발하는 최한민입니다.

운동 자세 분석 앱 **BPT**에서는 사용자 인증과 운동 기록 관리, 앱과 서버의 데이터 연동을 담당했습니다. 지역 커뮤니티 앱 <strong>마실(Masil)</strong>에서는 토큰 갱신·로그아웃, 가맹점 홍보 API와 권한 검증을 구현했습니다.

정상 요청뿐 아니라 재전송, 인증 실패, 다른 사용자의 데이터 접근 같은 상황을 함께 살핍니다. 구현한 동작은 테스트와 API 문서에 남기며 프론트엔드 개발자와 협업합니다.

## Tech Stack

| 영역 | 프로젝트에 적용한 기술 |
| --- | --- |
| Java Backend | Java 17, Spring Boot 3, Spring Security, Spring Data JPA |
| Python Backend | Python, FastAPI, SQLAlchemy, Pydantic |
| Database & Authentication | PostgreSQL, JWT, Firebase Authentication, Cloud Firestore |
| Test & Collaboration | JUnit 5, Mockito, pytest, Git/GitHub, Swagger/OpenAPI |

## Projects

### BPT · AI 기반 운동 자세 분석 트레이닝 앱

**한밭대학교 캡스톤 디자인 · 3인 팀 · 백엔드 담당 · 진행 중**

스마트폰 카메라로 운동 자세와 반복 횟수를 분석하고 운동 결과를 기록하는 앱입니다. 저는 **인증·운동 데이터 API와 클라이언트 연동**을 담당했습니다.

**Stack** — Java 17, Spring Boot 3, Spring Security, Spring Data JPA, PostgreSQL, JWT

[Repository](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT) · [My Pull Requests](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pulls?q=is%3Apr+author%3Achmdb) · [API 명세](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/blob/main/docs/BPT_API_SPECIFICATION_NOTION.md)

#### Backend Contributions

| 담당 영역 | 구현 내용 | 변경 이력 |
| --- | --- | --- |
| 백엔드 전환 | Firebase 기반 연동을 Spring Boot REST API 구조로 전환하고, 서버 도메인과 클라이언트 요청·동기화 계층 구성 | [#10](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/10), [#15](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/15) |
| 인증·계정 관리 | 회원가입·로그인, 이메일 인증, 아이디 찾기, 비밀번호 재설정 및 토큰 갱신 API 구현 | [#31](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/31), [#54](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/54) |
| 운동 기록·통계 | 세트 중량을 포함한 운동 기록 처리, 대시보드 요약과 월간 캘린더 API 및 서비스 테스트 추가 | [#23](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/23) |
| 접근 제어 | 운동 기록 재전송 시 사용자 소유권 검증과 기존 결과 반환 동작을 보완하고 회귀 테스트 추가 | [#60](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/60) |

<details>
<summary><b>문제 해결 ① · 운동 기록 재전송과 소유자 검증</b></summary>

**문제**

이미 저장된 `clientRecordId`가 다시 전달되면 기존 기록을 반환하는 경로에서, 요청 사용자와 기록 소유자가 같은지 확인하는 검증이 필요했습니다.

**해결**

- 인증된 사용자 ID와 기존 기록의 소유자 ID를 비교하도록 검증을 추가했습니다.
- 다른 사용자의 기록 ID를 재사용한 요청은 접근 거부로 처리했습니다.
- 본인 기록의 재전송에는 기존 결과를 반환하고, 중복 저장이나 운동 횟수 증가가 발생하지 않도록 했습니다.

**검증을 위한 테스트**

다른 사용자 요청의 거절 및 저장 미발생, 본인 재전송의 기존 결과 반환을 각각 단위 테스트로 추가했습니다.

기록을 중복 저장하지 않는 것과 그 기록을 반환할 권한이 있는지는 별도로 확인해야 한다는 점을 코드에 반영했습니다.

[구현 및 테스트 보기 → PR #60](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/60)

</details>

<details>
<summary><b>문제 해결 ② · 이메일 발송과 인증 요청 처리 분리</b></summary>

**개선 대상**

이메일 발송이 인증 요청 처리에 포함된 구조를 분리하고, 재발송·인증번호 오입력·발송 실패 상황의 처리를 보완했습니다.

**해결**

- 메일 발송을 별도 `EmailService`로 분리하고 `@Async`를 적용했습니다.
- 비동기 발송 실패 시 해당 인증번호를 폐기하도록 처리했습니다.
- 인증번호 재발송에 1분 간격을 적용하고, 5회 오입력 시 번호를 폐기하도록 했습니다.
- 인증번호가 로그에 남던 코드를 제거했습니다.
- 비밀번호 재설정 요청에서 아이디와 이메일이 같은 계정인지 확인하도록 보완했습니다.

**협업과 테스트**

재발송 제한, 오입력 제한, 계정 일치 여부 등의 테스트를 보완하고 API 명세와 연동 가이드를 업데이트했습니다.

[구현 보기 → PR #54](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/pull/54) · [프론트엔드 연동 가이드](https://github.com/Hanbat-Personal-Training-AI-Agent/BPT/blob/main/docs/BPT_BACKEND_CHANGE_AND_API_GUIDE.md)

</details>

### 마실(Masil) · 지역 커뮤니티와 가맹점 홍보 서비스

**공공데이터 활용 공모전 프로젝트 · 백엔드 개발**

지역사랑상품권 가맹점 정보와 동네 소식을 연결하는 커뮤니티 앱입니다. 저는 **인증 상태 관리, 가맹점 홍보 API, 초대코드 만료 처리와 테스트**를 구현했습니다.

**Stack** — Python, FastAPI, SQLAlchemy, PostgreSQL, Pydantic, JWT, pytest

[Backend Repository](https://github.com/Komsco-Masil/Masil) · [Frontend Repository](https://github.com/Komsco-Masil/masil-frontend) · [구현 커밋](https://github.com/Komsco-Masil/Masil/commit/47bad35c476cf91532d0f2c064c1cb2c9dadfb55)

#### Backend Contributions

| 담당 영역 | 구현 내용 | 코드 |
| --- | --- | --- |
| 토큰 갱신·로그아웃 | Refresh Token의 DB 저장·만료·폐기 상태를 관리하고, 갱신 시 기존 토큰 폐기 및 새 토큰 발급. 로그아웃 시 Access Token 블랙리스트 등록과 해당 사용자의 활성 Refresh Token 폐기 | [인증 API](https://github.com/Komsco-Masil/Masil/blob/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/app/api/endpoints/auth.py) |
| 가맹점 홍보 API | 홍보글 생성·조회·수정·삭제와 가맹점별 조회 필터 구현. 생성·수정·삭제 요청에서 사용자와 해당 가맹점의 소속 관계 검증 | [홍보 API](https://github.com/Komsco-Masil/Masil/blob/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/app/api/endpoints/promotions.py) |
| 초대코드 정리 | 만료 시각이 지난 미사용 초대코드를 1시간 주기로 삭제하는 백그라운드 작업 추가 | [스케줄러](https://github.com/Komsco-Masil/Masil/blob/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/app/core/scheduler.py) |
| 데이터 모델·테스트 | 홍보글·Refresh Token·토큰 블랙리스트 모델과 요청·응답 스키마 추가. 인증 흐름, 홍보글 권한, 만료 초대코드 선별·삭제 테스트 작성 | [모델](https://github.com/Komsco-Masil/Masil/tree/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/app/models) · [테스트](https://github.com/Komsco-Masil/Masil/tree/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/tests) |

<details>
<summary><b>구현 사례 · 토큰 갱신과 로그아웃 이후의 인증 상태 관리</b></summary>

**구현 목적**

로그인 이후 토큰 갱신과 로그아웃 상태를 서버에서 관리하고, 폐기한 토큰을 다시 사용하는 요청을 거절하도록 인증 흐름을 확장했습니다.

**처리 흐름**

1. 로그인 시 Access Token과 Refresh Token을 발급하고, Refresh Token의 사용자·만료 시각·폐기 상태를 DB에 저장합니다.
2. 갱신 요청에서 DB 상태와 JWT를 검증한 뒤, 기존 Refresh Token을 폐기하고 새 토큰을 발급합니다.
3. 로그아웃 시 요청에 사용한 Access Token을 블랙리스트에 등록하고 사용자의 활성 Refresh Token을 폐기합니다.
4. 인증이 필요한 API의 공통 의존성에서 블랙리스트를 조회해 접근을 거절합니다.

**검증을 위한 테스트**

토큰 갱신 성공, 이전 Refresh Token 재사용 거절, 로그아웃한 Access Token의 API 접근 거절, 로그아웃 이후 Refresh Token 갱신 거절을 테스트 코드에 담았습니다.

[인증 흐름 테스트](https://github.com/Komsco-Masil/Masil/blob/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/tests/test_jwt_advanced.py) · [홍보 API와 권한 테스트](https://github.com/Komsco-Masil/Masil/blob/47bad35c476cf91532d0f2c064c1cb2c9dadfb55/tests/test_promotions.py)

</details>

## Working Principles

- **사용자 경계 확인:** 데이터의 존재 여부와 접근 권한을 함께 검증합니다.
- **실패와 재시도 고려:** 중복 요청, 네트워크 장애, 외부 서비스 실패 상황을 살핍니다.
- **동작을 코드와 문서로 설명:** 테스트와 API 명세, PR을 통해 변경 의도를 공유합니다.

---

**Contact** · [hanmin8434@naver.com](mailto:hanmin8434@naver.com)
