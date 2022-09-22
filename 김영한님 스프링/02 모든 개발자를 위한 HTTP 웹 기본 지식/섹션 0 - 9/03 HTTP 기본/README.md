# 모든 개발자를 위한 HTTP 웹 기본 지식
## 3. HTTP 기본
### 3.1 모든 것이 HTTP
- HTTP (HyperText Transfer Protocol)
  - 거의 모든 형태의 데이터가 HTTP를 통해 전송된다


- HTTP의 역사
  - HTTP/0.9 : GET 메서드만 지원, HTTP 헤더 X
  - HTTP/1.0 : 메서드, 헤더 추가
  - **HTTP/1.1 : 가장 많이 사용, 우리에게 가장 중요한 버전**
    - RFC2068 (1997) -> RFC2616 (1999) -> RFC7230~7235 (2014)
  - HTTP/2 : 성능 개선
  - HTTP/3 : 성능 개선을 위해 TCP 대신에 UDP 사용


- 기반 프로토콜
  - TCP : HTTP/1.1, HTTP/2
  - UDP : HTTP/3
  - 현재는 HTTP/1.1가 주로 사용되나, HTTP/2, 3도 점차 사용량 증가하는 중


- HTTP 특징
  - Client - Server 구조
  - Stateless 프로토콜, 비연결성 HTTP 메시지
  - 단순해서 확장 가능


### 3.2 Client - Server 구조
- Request - Response 구조
- 클라이언트는 서버에 요청을 보내고, 응답을 대기한다
- 서버가 요청에 대한 결과를 만들어서 응답한다  

❗️ : **Client - Server 구조를 사용하면, 양쪽이 서로의 역할에만 집중하면서 독립적으로 진화할 수 있다**


### 3.3 Stateful, Stateless
- **Stateless**
  - 서버가 클라이언트의 요청을 저장하지 않는다
    - 장점
      - 서버의 확장성이 높다 (Scale out)
        - 응답서버를 쉽게 바꿀 수 있기 때문에 무한한 서버 증설이 가능하기 때문이다
    - 단점 
      - 실무적으로 모든 것을 stateless하게 설계할 수 없을 수도 있다
      - 클라이언트가 추가 데이터를 전송해야 한다
      

### 3.4 비 연결성 (connectionless)
- HTTP는 기본적으로 connectionless
- 사용 이유
  - 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작다.
    - Ex, 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지는 않는다
- 장점
  - 일반적으로 초 단위 이하의 빠른 속도로 응답
  - 매우 효율적인 서버 자원 사용 가능
- 단점
  - 매번 TCP/IP 연결을 새로 맺어야 한다
    - 3 handsake 시간 추가
  - 매번 수 많은 웹 사이트 리소스를 다시 받아야 한다. 
    - _지금은 HTTP 지속 연결(persistent connections)로 문제 해결_
      - 일반적으로 60초 정도 연결 유지
      
**동시간에 많은 트래픽이 몰리는 이벤트는 이런 stateless, connectionless가 힘을 쓰지 못한다. 그럼에도 불구하고, 최대한 stateless하게 설계를 해야 서버 증설이 가능하다.**


### 3.5 HTTP 메시지
```
HTTP-message = start-line
               * (header-field CRLF)
               CRLF
               [ message-body ]
```

- start-line = request-line / status-line
  - request-line = method SP(공백) request-target SP HTTP-version CRLF(엔터)
    - method : GET
      - 서버가 수행해야 할 동작 지정
    - request-target : /search?q=hello&hl=ko
      - 절대 경로 = "/"로 시작하는 경로
    - HTTP version : HTTP/1.1
  - status-line = HTTP-version SP status-code SP reason-phrase CRLF
    - HTTP-version
    - status-code : HTTP 상태 코드
    - reason-phrease : 사람이 이해할 수 있는 짧은 상태 코드 설명


- header-field = field-name: OWS field-value OWS (OWS: SP 허용)
  - field-name은 대소문자 구분 없음
  - 용도 : HTTP 전송에 필요한 모든 부가정보
    - 메시지 바디의 내용, 크기, 압축, 인증, 요청 클라이언트 정보 등등...
    - 표준 헤더가 너무 많음
    - 필요시 임의의 헤더 추가 가능


- message-body
  - 실제 전송할 데이터


- 단순함 확장 가능
  - HTTP는 단순하다
  - 크게 성공하는 표준 기술은 단순하지만 확장 가능한 기술
    - 프로그램 역시 복잡한 코드가 아닌, 단순하고 확장 가능한 코드가 좋은 프로그램이 될 수 있는 것 같다.

---

참고 : [김영한님 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/)
