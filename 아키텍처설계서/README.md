# mockcote 아키텍처 설계서

## 개요
본 문서는 mockcote 애플리케이션의 마이크로서비스 아키텍처(MSA) 설계 내용을 포함합니다. 각 서비스의 역할, 데이터 흐름, 데이터베이스 설계, 추가 로직 및 기술 스택을 상세히 설명합니다.

---

## 마이크로서비스 설계

### 1. **User Service (유저 서비스)**
- **역할:**
  - 사용자 관리 (회원가입, 로그인)
  - 사용자의 문제 풀이 히스토리 관리
  - 사용자별 취약 태그 분석 및 문제 추천 요청
  - 사용자 랭킹 계산 및 제공
- **주요 기능:**
  - 사용자별 문제 풀이 로그 저장
  - `Time Service`로부터 전달받은 검증 결과를 저장
  - 각 문제별 빠른 시간 내 풀이 랭킹 계산
  - 전체 사용자 종합 랭킹 제공
  - `Problem Service`와 연계하여 취약 태그 기반 문제 추천
- **데이터베이스:**
  - `users`, `user_problem_logs`, `problem_rankings`, `user_rankings`

---

### 2. **Problem Service (문제 관리 서비스)**
- **역할:**
  - 문제 데이터 크롤링 및 관리
  - 문제 데이터 제공 및 사용자 맞춤 문제 추천
  - 문제별 테스트 케이스 제공
- **주요 기능:**
  - 태그, 난이도, 정답률 기반 문제 검색 및 필터링
  - 최신 문제 데이터를 크롤링하여 저장
  - 사용자 취약 태그 정보를 기반으로 문제 추천
  - `Time Service`에 문제별 테스트 케이스 제공
- **데이터베이스:**
  - `problems`, `problem_tags`, `problem_test_cases`
- **외부 연동:**
  - 문제 데이터 크롤링 도구

---

### 3. **Time Service (시간 제한 문제 풀이 서비스)**
- **역할:**
  - 시간 제한 문제 풀이 환경 제공
  - 사용자 제출 코드 검증 및 결과 관리
- **주요 기능:**
  - 문제 풀이 환경 구성
  - 사용자 제출 코드 실행 및 테스트 케이스 검증
  - 풀이 결과(`status`, `execution_time`, `test_results`)를 `User Service`로 전달
- **데이터베이스:**
  - `timed_sessions`, `submission_logs`
- **추가 로직:**
  - **코드 검증:** 제출된 코드가 테스트 케이스를 통과하는지 확인.
  - **보안:** 실행 제한, 무한 루프 방지, 메모리 사용 제한 적용.
- **데이터 흐름:**
  - `Problem Service`로부터 테스트 케이스 데이터를 가져와 검증.
  - 검증 결과를 `User Service`로 전송하여 저장.

---

## 서비스 간 상호작용

### 1. `User Service` ↔ `Time Service`
- **데이터 흐름:**
  - `Time Service`는 문제 풀이 결과(성공 여부, 실행 시간 등)를 `User Service`로 전달.
  - `User Service`는 전달받은 결과를 `user_problem_logs`에 저장.

### 2. `User Service` ↔ `Problem Service`
- **데이터 흐름:**
  - `User Service`는 사용자 취약 태그 정보를 `Problem Service`에 전달.
  - `Problem Service`는 해당 태그 기반 문제를 추천.

### 3. `Time Service` ↔ `Problem Service`
- **데이터 흐름:**
  - `Problem Service`는 `Time Service`에 테스트 케이스 데이터를 제공.
  - 테스트 케이스는 문제 풀이 검증에 사용됨.

---

## 데이터베이스 설계

### User Service
- **`users` 테이블:** 사용자 정보
- **`user_problem_logs` 테이블:** 사용자 풀이 로그
- **`problem_rankings` 테이블:** 문제별 사용자 랭킹
  - `problem_id`, `user_id`, `execution_time`
- **`user_rankings` 테이블:** 전체 사용자 랭킹
  - `user_id`, `score`, `rank`

### Problem Service
- **`problems` 테이블:** 문제 데이터
- **`problem_tags` 테이블:** 문제 태그 정보
- **`problem_test_cases` 테이블:** 문제별 테스트 케이스
  - `test_case_id`, `problem_id`, `input_data`, `expected_output`

### Time Service
- **`timed_sessions` 테이블:** 시간 제한 문제 풀이 세션
  - `session_id`, `user_id`, `problem_id`, `start_time`, `end_time`
- **`submission_logs` 테이블:** 코드 제출 로그
  - `submission_id`, `user_id`, `problem_id`, `status`, `execution_time`, `test_case_results`


## 추가 로직: 코드 검증

### 흐름
1. **Time Service**에서 사용자 코드 및 테스트 케이스 데이터를 입력받음.
2. 제출된 코드를 실행하여 결과를 생성.
3. 결과를 테스트 케이스와 비교하여 검증.
4. 검증 결과(성공 여부, 실행 시간, 각 테스트 케이스 결과)를 `User Service`로 전송.

### 검증 결과 예시
```json
{
    "problem_id": 123,
    "user_id": "example_user",
    "submission_id": 456,
    "status": "success",
    "execution_time": 120,
    "test_results": [
        { "input": "1 2", "expected_output": "3", "actual_output": "3", "result": "pass" },
        { "input": "3 4", "expected_output": "7", "actual_output": "8", "result": "fail" }
    ]
}
```
## 기술 스택

### User Service
- **기술:** Java, Spring Boot, JPA , JWT

### Problem Service
- **기술:** Java, Spring Boot, JPA, Jsoup

### Time Service
- **기술:** Java, Spring Boot, JPA, Docker (Sandbox 실행 환경)

### 공통 요소
- **데이터베이스:** MySQL  
- **메시지 큐:** RabbitMQ 또는 Kafka  
- **API Gateway:** Spring Cloud Gateway  
- **컨테이너화:** Docker  
- **모니터링:** Prometheus, Grafana  

---

## 전체 아키텍처 흐름

1. 사용자가 로그인하고 문제 풀이를 시작합니다.
2. **Problem Service**에서 문제를 검색하거나 추천받아 제공합니다.
3. 사용자는 문제 풀이를 진행하며, 제출 코드는 **Time Service**에서 검증됩니다.
4. **Time Service**는 코드 실행 결과를 테스트 케이스와 비교하고 결과를 생성합니다.
5. 검증 결과는 **User Service**로 전달되어 사용자 히스토리에 저장됩니다.
6. **User Service**는 사용자 데이터를 분석하여 취약 태그 정보를 **Problem Service**에 전달하고, 맞춤형 문제를 추천받습니다.
7. **User Service**는 문제 풀이 로그를 기반으로 랭킹을 계산하고 제공합니다.


---
