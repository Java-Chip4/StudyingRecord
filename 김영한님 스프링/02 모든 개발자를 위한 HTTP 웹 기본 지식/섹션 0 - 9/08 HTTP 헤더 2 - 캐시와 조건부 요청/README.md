# 모든 개발자를 위한 HTTP 웹 기본 지식
## 8. HTTP 헤더2 - 캐시와 조건부 요청
### 8.1 캐시 기본 동작
- 캐시가 없을 때
  - 데이터가 변경되지 않아도, 계속 네트워크르르 통해서 데이터를 전달 받아야 한다

- 캐시 적용
  - `cache-control: max-age=60`
  - 캐시 덕분에 캐시 가능 시강동안 네트워크를 사용하지 않아도 된다

- 캐시 적용 - 캐시 시간 초과
  - 캐시 유효 시간이 초과하면, 서버를 통해 데이터를 다시 조화하고, 캐시를 갱신한다
  - **_그런데 만약 캐시 데이터가 바뀌지 않았으면, 다시 다운로드 받지 않아도 되지 않을까?_**

### 8.2 검증 헤더와 조건부 요청1
- **_만약 캐시 데이터가 바뀌지 않았으면, 다시 다운로드 받지 않아도 되지 않을까?_**
- `Last-Modified: 2020년 11월 10일 10:00:00`
- `if-modified-since: 2020년 11월 10일 10:00:00`
  - 캐시 시간이 초과했을 때, 클라이언트가 해당 헤더를 서버에 보낸다.
  - 서버가 확인했을때, 데이터가 갱신되지 않았다면, body가 생략된 304 Not Modified 응답을 서버가 보낸디


### 8.3 검증 헤더와 조건부 요청2
- 검증 헤더
  - 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
    - Last-Modified
    - ETag
- 조건부 요청 헤더
  - 검증 헤더로 조건에 따른 분기
  - If-Modified-Since
    - 데이터 미변경 예시
      - 304 Not Modified, 헤더 데이터만 전송
    - 데이터 변경 예시
      - 200 OK, 모든 데이터 전송
    - 단점
      - 1초 미만 단위로 캐시 조정이 불가능
      - 날짜 기반의 로직 사용
      - 데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우
      - 서버에서 별도의 캐시 로직을 관리하고 싶은 경우 -> **ETag 사용**
        - Ex) 스페이스나 주석처럼 크게 상관없는 업데이트인 경우
  - ETag
    - `If-None-Matched`
    - 캐시 제어 로직을 서버에서 완전히 관리
      
### 8.4 캐시와 조건부 요청 헤더
- Cache-Control * 제일 중요
  - 캐시 지시어
  - `Cache-Control: max-age`
    - 캐시 유효 시간, 초 단위
  - `Cache-Control: no-cache`
    - 데이터는 캐시해도 되지만, 항상 origin 서버에 검증하고 사용
  - `Cache-Control: no-store`
    - 데이터에 민간함 정보가 있으므로 저장하면 X
    - 메모리에서 사용하고 최대한 빨리 삭제

- Pragma

- Expires
  - 캐시 만료일 지정 (하위 호환)
  - 지금은 더 유연한 max-age를 더 선호

### 8.5 프록시 캐시
- `Cache-Control: public`
- `Cache-Control: private`
- `Cache-Control: s-maxage`
- `Age: 40`

### 8.6 캐시 무효화
- 캐시를 절대 사용하고 싶지 않을 때
  - `Cache-Control: no-cache, no-store, must-revalidate`
  - `Pragema: no-cahche`
    - HTTP 1.0 하위 호환

- Cache-Control : 확실한 캐시 무효화 응답
  - no-cache
    - origin 서버에 접근할 수 없는 경우, 캐시 서버 설정에 따라 캐시 데이터를 반환할 수도 있음
  - must-revalidate
    - origin 서버에 접근할 수 없는 경우, 항상 오류가 발생

---

참고 : [김영한님 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/)
