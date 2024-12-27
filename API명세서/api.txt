# API Specification

## User Service

| **Method** | **Endpoint**                | **Description**                                                                 |
|------------|-----------------------------|---------------------------------------------------------------------------------|
| POST       | /user/join                  | 회원가입 기능을 제공.                                                          |
| POST       | /auth/login                 | 로그인 처리, Access Token 및 Refresh Token 발급.                               |
| POST       | /auth/logout                | 로그아웃 처리, 세션 및 토큰 초기화.                                            |
| POST       | /auth/refresh               | Refresh Token을 이용한 Access Token 재발급.                                    |
| GET        | /user                       | 모든 유저 정보를 조회.                                                         |
| GET        | /user/find/{handle}         | 특정 핸들을 통해 유저 정보 조회.                                               |
| DELETE     | /user/{handle}              | 특정 핸들의 유저 정보를 삭제.                                                  |
| PUT        | /user/{handle}              | 특정 유저의 비밀번호 변경.                                                     |
| POST       | /user/handle-auth           | 핸들과 문제번호를 입력받아 제출 여부를 검증.                                   |
| GET        | /auth/level/{handle}        | 핸들을 통해 Solved.ac에서 티어 정보를 조회.                                    |

---

## Stat Service

| **Method** | **Endpoint**                     | **Description**                                                             |
|------------|----------------------------------|-----------------------------------------------------------------------------|
| POST       | /stats/history                   | 풀이 히스토리를 저장하고 통계 테이블 갱신.                                  |
| GET        | /stats/tags?handle={}            | 핸들별 태그별 문제풀이 개수 조회 (많이 푼 순서).                            |
| GET        | /stats/levels?handle={}          | 핸들별 난이도별 문제풀이 개수 조회 (낮은 난이도부터).                        |
| GET        | /stats/tags/lowest?handle={}     | 특정 핸들의 취약 태그 상위 5개 조회.                                        |
| POST       | /stats/rank/problem              | 특정 문제의 문제별 랭킹을 갱신.                                             |
| GET        | /stats/rank/problem/{problemId}  | 특정 문제의 전체 랭킹 조회.                                                |
| POST       | /stats/rank/total                | 전체 사용자 랭킹 갱신.                                                     |
| POST       | /stats/rank/increment-score      | 특정 유저의 점수를 증가시키고 랭킹 갱신.                                    |
| GET        | /stats/rank/user?handle={}       | 특정 사용자의 랭킹 정보 조회.                                              |
| GET        | /stats/rank/all?page=1&size=20   | 전체 사용자 랭킹 정보를 페이지네이션하여 조회.                              |

---

## Problem Service

| **Method** | **Endpoint**                        | **Description**                                                             |
|------------|-------------------------------------|-----------------------------------------------------------------------------|
| POST       | /dbsave/problem/fetch-all          | 모든 문제 정보를 데이터베이스에 저장.                                       |
| POST       | /dbsave/tag/fetch-all              | 모든 태그 정보를 데이터베이스에 저장.                                       |
| POST       | /problem/query                     | 쿼리를 기반으로 문제를 추천.                                                |
| GET        | /problem/random                    | 브론즈 문제 중 랜덤으로 1개 추천 (회원가입용).                              |
| GET        | /problem/info?problemId={problemId}| 특정 문제 번호로 문제 정보를 조회.                                         |
| GET        | /problem/list?page=1&size=10       | 문제 리스트를 페이지네이션하여 조회.                                        |

---

## Time Service

| **Method** | **Endpoint**              | **Description**                                                             |
|------------|---------------------------|-----------------------------------------------------------------------------|
| GET        | /submissions/check        | 특정 핸들 및 문제 번호로 풀이 여부를 확인.                                   |
| POST       | /submissions/save         | 풀이 로그를 저장 (시작시간, 제한시간, 언어, 성공여부 포함).                  |
