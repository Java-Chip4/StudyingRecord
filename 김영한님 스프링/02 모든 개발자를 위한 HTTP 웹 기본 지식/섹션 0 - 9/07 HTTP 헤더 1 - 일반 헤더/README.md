# 모든 개발자를 위한 HTTP 웹 기본 지식
## 7. HTTP 헤더1 - 일반 헤더
### 7.1 HTTP 헤더 개요
- header-field = field-name: OWS field-value OWS (OWS: SP 허용)


- HTTP 헤더 분류 (RFC2616 과거 버전)
  - General 헤더 : 메시지 전체에 적용되는 정보
  - Request 헤더 : 요청 정보
  - Response 헤더 : 응답 정보
  - Entity 헤더 : 엔티티 바디 정보
    - Content-Type: text/html


- HTTP 헤더 분류 (RFC723X 현재 버전)
  - 메시지 본문을 통해 표현 데이터 전달
  - 메시지 본문 = 페이로드 payload

### 7.2 표현
- Content-Type
  - 표현 데이터의 형식 설명
  - 미디어 타입, 문자 인코딩
- Content-Encoding
  - 표현 데이터를 압축하기 위해 사용
- Content-Language
  - 표현 데이터의 자연 언어를 표현
  - 예시
    - ko
    - en
- Content-Length
  - 표현 데이터의 길이로 바이트 단위
  - Transfer-Encoding(전송 코딩) 사용시에는 Content-Length 사용하면 X 


- 표현 헤더는 전송, 응답 둘 다 사용

- Content-Type
  - 표현 데이터의 형식 설명
  - 미디어 타입, 문자 인코딩

### 7.3 콘텐츠 협상
- 협상 (콘텐츠 네고시에이션)
  - 클라이언트가 선호하는 표현 요청
  - Accept : 클라이언트가 선호하는 미디어 타입 전달
  - Accept-Charset : 클라이언트가 선호하는 문자 인코딩
  - Accept-Encoding : 클라이언트가 선호하는 압축 인코딩
  - Accept-Language : 클라이언트가 선호하는 자연 언어

- 협상 헤더는 요청시에만 사용

- 협상과 우선순위
  - Quality Values(q)
    - 0~1, 클수록 높은 우선순위

### 7.4 전송 방식
- 단순 전송
  - Content-Length
    ```
    HTTP/1.1 200 OK
    Content-Type:text/html;charset=UTF-8
    Content-Length: 521

    <html>
        <body>...</body>
    <html>
    ```
  
- 압축 전송
  - Content-Encoding
    ```
    HTTP/1.1 200 OK
    Content-Type:text/html;charset=UTF-8
    Content-Encoding: gzip
    Content-Length: 521

    qwqe2qwffefsdf3tdgfh4tsf6544
    ```

- 분할 전송
  - Transfer-Encoding
    ```
    HTTP/1.1 200 OK
    Content-Type:text/plain
    Transfer-Encoding: chunked
    
    5
    Hello
    5
    World
    0
    \r\n
    ```

- 범위 전송
  - Range, Content-Range
    ```
    HTTP/1.1 200 OK
    Content-Type:text/plain
    Content-Range: bytes 1001-2000 / 2000

    qwqe2qwffefsdf3tdgfh4tsf6544  
    ```
    
### 7.5 일반 정보
- From
  - 유저 에이전트의 이메일 정보
  - 요청에서 사용
- Referer * 자주 사용
  - 현재 요청된 페이지의 이전 웹페이지 주소
  - Referer을 사용해서 유입 경로 분석 가능
  - 요청에서 사용
  - *referrer의 오타인데, 그냥 사용 중

- User-Agent
  - 유저 에이전트 애플리케이션 정보
  - 통계 정보
  - 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
  - 요청에서 사용

- Server
  - 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보
  - 응답에서 사용

- Date
  - 메시지가 발생한 날짜와 시간
  - 응답에서 사용

### 7.6 특별한 정보
- Host * 필수값
  - 요청한 호스트 정보(도메인)
  - 하나의 서버가 여러 도메인을 처리해야 할 때
  - 하나의 IP 주소에 여러 도메인이 적용되어 있을 때

- Location
  - 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, 해당 위치로 자동 이동

- Allow
  - 허용 가능한 HTTP 메서드
  - 405 (Method Not Allowed)에서 응답에 포함해야함
  - 사실 잘 사용되지는 않음

- Retry-After
  - 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
  - 실제로 사용하기가 쉽지 않음

### 7.7 인증
- Authorization
  - 클라이언트 인증 정보를 서버에 전달
  - Authorization: Basic xxxxxxxxxxx

- WWW-Authenicate
  - 리소스 접근시 필요한 인증 방법 정의
  - 401 Unauthorized 응답과 함께 사용

### 7.8 쿠키


- Stateless
  - HTTP는 무상태 프로토콜이기 때문에 생기는 문제가 있다
  - 이를 쿠키를 통해 해결함


- 쿠키
- `set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; domain=.google.com; Secure`
  - 사용처
    - 사용자 로그인 세션 관리
    - 광고 정보 트래킹
  - 쿠키 정보는 항상 서버에 전송됨
    - 그래서 네트워크 트래픽 추가 유발
    - 최소한의 정보만 사용(세션 id, 인증 토큰)
    - 서버에 전송하지 않고, 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지(localStorage, sessionStorage) 참고
  - 주의
    - 보안에 민감한 데이터는 저장하면 안됨

- 쿠키 - 생명주기
  - `Set-Cookie: expires=Sat, 26-Dec-2020 00:00:00 GMT`
    - 만료일이 되면 쿠키 삭제
  - `Set-Cookie: max-age=3600` (3600sec)
    - 0이나 음수를 지정하면 쿠키 삭제
  - 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료시까지만 유지
  - 영속 쿠기 : 만료 날짜를 입력하면 해당 날짜까지 유

- 쿠키 - 도메인
  - `domain=example.org`
  - 명시 : 명시한 문서 기준 도메인 + 서브 도메인 포함
  - 생략 : 현재 문서 기준 도메인만 적용

- 쿠키 - 경로
  - `path=/home`
  - 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
  - 일반적으로 path=/ 루트로 지정

- 쿠키 - 보안
  - Secure
    - HTTPS인 경우에만 쿠키를 전송하도록 설정
  - HttpOnly
    - XSS 공격 방지
    - js에서 접근 불가
    - HTTP 전송에만 사용
  - SameSite
    - XSRF 공격 방지
    - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우에만 쿠키 전송

---

참고 : [김영한님 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/)
