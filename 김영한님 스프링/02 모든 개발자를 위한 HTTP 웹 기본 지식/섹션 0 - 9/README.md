# 모든 개발자를 위한 HTTP 웹 기본 지식
## 해당 주차 리더 : @rkdtmddnjs97
## 해당 주차 섹션
### [섹션 1. 인터넷 네트워크](##1-인터넷-네트워크)   
### [섹션 2. URI와 웹 브라우저 요청 흐름](##2-URI와-웹-브라우저-요청-흐름)    
### [섹션 3. HTTP 기본](##3-HTTP-기본)  
### [섹션 4. HTTP 메서드](##4-HTTP-메서드)    
### [섹션 5. HTTP 메서드 활용](##5-HTTP-메서드-활용)    
### [섹션 6. HTTP 상태코드](##6-HTTP-상태코드)    
### [섹션 7. HTTP 헤더1 - 일반 헤더](##7-HTTP-헤더1-일반-헤더)    
### [섹션 8. HTTP 헤더2 - 캐시와 조건부 요청](##8-HTTP-헤더2-캐시와-조건부-요청)    
### 섹션 9. 다음으로    


---


## 1. 인터넷 네트워크
### 1.1 인터넷 통신
**_우리의 메세지가 어떻게 Client에서 Server까지 도달할 수 있는 걸까?_**


### 1.2 IP(인터넷 프로토콜)
복잡한 인터넷 망에서 메세지를 친구에게 보내기 위해서는 IP 주소라는 규칙을 통해서 보내지게 된다.

_**Client인 나와 친구인 Server가 각자 IP를 부여 받아야 한다.**_

IP(인터넷 프로토콜)은 패킷(packet)이라는 통신 단위로 지정한 IP 주소에 데이터를 전달한다. IP 패킷에는 출발지의 IP 주소, 목적지의 IP 주소 등의 내용과 전송 데이터가 담겨져 있다.

_**Client인 내가 Server인 친구를 향해 패킷을 전송하면, 인터넷 망에서 노드를 타고 Server에게 어찌저찌 도달한다.**_

_**이후, Server인 친구가 메세지를 잘 받았다고 내게 다시 패킷을 전송하면, 다시 인터넷 망에서 노드를 타고 Client에게 도달한다.**_

이 과정에서 사용된 IP 프로토콜에는 세 가지의 한계점이 있다.
- IP 프로토콜 한계점
    - 비연결성
        - 패킷을 받을 대상이 없거나 해당 대상이 서비스 불능 상태여도 패킷을 전송한다.
    - 비신뢰성
        - 패킷이 전송되는 과정 중에 패킷이 사라져도 알 수 없다.
        - 여러 개의 패킷이 전송되다가 그 순서가 섞여버려도 알 수 없다.
    - 프로그램 구분
        - 같은 IP 주소를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이라면, 어떤 패킷이 어떤 프로그램의 것인지 구분할 수 없다.


### 1.3 TCP, UDP
- 인터넷 프로토콜 스택의 4계층
    - 애플리케이션 계층 : HTTP, FTP
    - 전송 계층 : TCP, UDP
    - 인터넷 계층 : IP
    - 네트워크 인터페이스 계층


- TCP(전송 제어 프로토콜) 특징
    - 연결 지향 : TCP 3 way handsake (가상 연결)
        - TCP 3 way handsake 과정
            1. (Client to Server) SYN
            2. (Server to Client) SYN + ACK
            3. (Client to Server) ACK (+ data)
    - 데이터 전달 보증
    - 순서 보장
        1. (Client to Server) 패킷 1, 패킷 2, 패킷 3 순서로 발신
        2. (Server) 패킷 1, 패킷 3, 패킷 2 순서로 수신
        3. (Server to Client) 패킷 2부터 다시 보내도록 요청
    - 정리
        - 많은 기능으로 인해 속도가 느리다.


- UDP 특징
    - 3 way handsake X
    - 데이터 전달 보증 X
    - 순서 보장 X
    - 정리
        - IP와 거의 같다. PORT, checksum 정도만 추가되어 있다. 그래서 단순하고 빠르다.
        - 최적화를 하고 싶으면, 애플리케이션에서 추가 작업함으로써 가능하다.


### 1.5 PORT
_**한 클라이언트에 둘 이상의 프로세스를 연결해야 하면, 어떻게 할까?**_
- IP는 목적지를 찾는 것, PORT는 목적지 내에서 정확한 프로세스를 찾는 것이라고 생각하자
- 0 ~ 65535 할당 가능
    - 0 ~ 1023은 주로 사용되는 포트이기에 사용을 피해자
        - FTP : 20, 21
        - TELNET : 23
        - HTTP : 80
        - HTTPS : 443

### 1.6 DNS (Domain Named Service)
**_IP는 기억하기 어렵고, 바뀔 수 있다. 쉽고, 바뀌지 않을 이름을 사용할 순 없을까?_**
- 도메인 명을 IP 주소로 변환하여 사용하는 것
    1. (Client to DNS Server) 도메인 명에 대응되는 서버의 IP 주소 요청
    2. (DNS Server to Client) 도메인 명에 대응되는 서버의 IP 주소 응답
    3. (Client to Server) 해당 IP 주소를 가진 서버에 접속 요청

    
## 2. URI와 웹 브라우저 요청 흐름
### 2.1 URI
- URI (Uniform Resource Identifier)
    - URI는 URL과 URN을 모두 포함하는 개념
    - 뜻
        - Uniform : 리소스 식별하는 통일된 방식
        - Resource : 자원, URI로 식별할 수 있는 모든 것
        - Identifier : 식별자


- URL (Uniform Resource Locator)
    - 리소스가 있는 위치를 지정
    - `scheme://[userinfo@]host[:port][/path][?query][#fragment]`
        - scheme
            - 주로 프로토콜 사용
        - userinfo **\* 거의 사용 X**
            - URL에 사용자정보를 포함해서 인증
        - host
            - 도메인명 또는 IP 주소를 직접 사용
        - port
            - 일반적으로 생략. 생략시 HTTP는 80, HTTPS는 443
        - path
            - 리소스 경로(path), 계층적 구조
        - query (= query parameter, query string)
            - key=value 형태
            - ?로 시작, &로 추가 가능
                - ?keyA=valueA&keyB=valueB
        - fragment
            - HTML 내부 북마크 등에 사용
            - 서버 전송 정보 X


- URN (Uniform Resource Name)
    - 리소스에 이름을 부여
    - 잘 사용되지 않음


### 2.2 웹 브라우저 요청 흐름
1. **(Client : 웹 브라우저)** 특정 URL로 접속 요청시, 웹 브라우저가 HTTP 요청 메시지 생성
   ```
   GET /search?q=hello&hl=ko HTTP/1.1
   Host: www.google.com
   ```
2. **(Client : SOCKET 라이브러리)** SOCKET 라이브러리를 통해 TCP/IP로 3way handshake를 실행하여 서버와 연결
3. **(Client : OS)** TCP/IP 계층으로 데이터 전송을 하기 위해 데이터 전달
4. **(Client : TCP/IP 계층)** HTTP 메시지가 포함된 TCP/IP 패킷 생성
5. **(Client : 네트워크 인터페이스)** 서버를 향해 요청 패킷 전송
6. **(Server)** 요청 패킷이 도착하면, TCP/IP 패킷 껍데기는 버리고 HTTP 메시지를 서버가 해석
7. **(Server)** 요청에 알맞는 HTTP 메시지를 위와 같은 방식을 통해 패킷을 생성하여 응답 패킷 생성 후, 클라이언트를 향해 전송
    ```
    HTTP/1.1 200 OK
    Content-Type: text/html;charset=UTF-8 Content-Length: 3423
    <html>
       <body>...</body>
    </html>
    ```
8. **(Client)** 웅답 패킷이 도착하면, 웹 브라우저가 HTML을 렌더링하여 화면에 출력


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


## 4. HTTP 메서드
### 4.1 HTTP API를 만들어보자
**_회원 정보를 관리하는 API를 구성해보려고 한다_**

- API URI 설계
    - 회원 목록 조회 /read-member-list
    - 회원 조회 /read-member-by-id
    - 회원 등록 /create-member
    - 회원 수정 /update-member
    - 회원 삭제 /delete-member

**_아래와 같은 URI 구성은 올바른 설계일까?_** **_아니다._**  
**_가장 중요한 것은 `리소스 식별`이다_**

- 리소스란?
    - 회원을 등록하고 수정하고, 조회하는 게 리소스가 아니다
    - 회원이라는 개념 자체가 리소스다

**_리소스 식별이 이뤄진 API URI를 갖도록 리팩토링을 해보자_**

- API URI 설계 리팩토링
    - 회원 목록 조회 /members
    - 회원 조회 /members/{id}
    - 회원 등록 /members/{id}
    - 회원 수정 /members/{id}
    - 회원 삭제 /members/{id}

_**여전히 나타나는 문제점이 있다.**_ _**같은 URI를 어떻게 서로 구분할 수 있을까?**_  
**_`리소스`와 `행위`를 구분함으로써 가능하다_**

- 리소스와 행위를 분리하자
    - 가장 중요한 것은 리소스를 식별하는 것
    - URI는 리소스만 식별해야 한다
    - 리소스와 해당 리소스를 대상으로 하는 행위를 분리

### 4.2 HTTP 메서드 - GET, POST
- GET : 리소스 조회
- POST : 요청 데이터 처리, 주로 등록

- 추가
    - HEAD : GET과 동일하지만 바디를 제외하고, 상태 줄과 헤더만 반환
    - OPTIONS
    - CONNECT
    - TRACE

- GET
    - 리소스 조회
    - 서버에 전달하고 싶은 데이터는 query를 통해서 전달
    - 메시지 바디를 사용해서 데이터를 전달할 수 있지만, 지원하는 곳이 많아서 권장 X

- POST
    - 요청 데이터 처리
    - 메시지 바디를 통해 서버로 요청 데이터 전달
    - 서버는 요청 데이터를 처리
        - 대상 리소스가 리소스의 고유한 의미 체계에 따라 요청에 포함된 표현을 처리하도록 요청한다
        - POST의 결과로 새로운 리소스가 생성되지 않을 수도 있음
            - ex) POST /order/{orderId}/start-delivery (컨트롤 URI)

### 4.3 HTTP 메서드 - PUT, PATCH, DELETE
- PUT
    - 리소스를 대체 = **덮어쓰기**
        - 기존에 존재하던 필드를 넣어주지 않으면, 넣어주지 않은 기존 필드 값은 삭제되어 버림
    - **클라이언트가 리소스를 식별한다** -> POST와 차이점

- PATCH
    - 간혹 지원되지 않는 서버도 있다
        - POST 사용으로 대체

### 4.4 HTTP 메서드의 속성
- 안전 Safe
    - 사례 : GET, HEAD, CONNECT, OPTION
    - 호출해도 리소스를 변경하지 않는다
        - 해당 리소스만 고려하기 때문에, 서버 이슈는 상관 X

- 멱등 Idempotent
    - f(f(x)) = f(x)
        - 한 번 호출하던 두 번 호출하던, 100번 호출하던 결과가 똑같다
    - 사례
        - GET : 계속 조회해도 멱등하다
        - PUT : 계속 덮어쓰기 해도 멱등하다
        - POST : 중복 결제가 될 수 있기에 **멱등하지 않다**
        - 참고 : <img width="708" alt="스크린샷 2022-09-22 오후 5 36 33" src="https://user-images.githubusercontent.com/13804810/191699509-30fca91a-a674-4cd2-9401-a834493ccd63.png">
    - 활용
        - 자동 복구 매커니즘
            - 서버로부터 response를 못받았을때, 다시 request를 보내도 되는가?
            - 멱등하다면 OK.

- 캐시 가능 Cacheable
    - 응답 결과 리소스를 캐시해서 사용해도 되는가?
        - GET, HEAD, POST, PATCH 캐시 가능
            - 실제로는 GET, HEAD 정도만 캐시로 사용
                - 나머지는 본문 내용까지 캐시 키로 고려해야 하는데, 구현이 쉽지 않아서 잘 사용하지 않음

    
## 5. HTTP 메서드 활용
### 5.1 클라이언트에서 서버로 데이터 전송
- 데이터 전달 방식은 크게 두 가지
    - 쿼리 파라미터를 통한 데이터 전송
        - GET
        - 주로 정렬 필터(검색어)
    - 메시지 바디를 통한 데이터 전송
        - POST, PUT, PATCH
        - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경


- 데이터 전송의 4가지 상황
    - **정적 데이터 조회**
        - 이미지, 정적 텍스트 문서
        - 조회는 GET 사용
        - 쿼리 파라미터 미사용. 리소스 경로로 단순하게 조회 가능
    - **동적 데이터 조회**
        - 주로 검색, 게시판 목록에서 정렬 필터
        - 조회는 GET 사용
        - 쿼리 파라미터 사용
        - 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용
    - **HTML Form을 통한 데이터 전송**
        - 회원가입, 상품 주문, 데이터 변경
        - POST 전송 - 저장
            - Content-Type: application/x-www-form-urlencoded 사용
                - form의 내용을 메시지 바디를 통해서 전송(key=value, 쿼리 파리미터 형식)
                - 전송 데이터를 url encoding 처리
            - Content-Type: multipart/form-data
                - 파일 같은 binary 데이터를 전송할 때 주로 사용
                - 다른 종류의 여러 파일과 폼의 내용을 함께 전송할 수 있어서 multipart
            - HTML Form 전송은 POST, GET 지원
    - **HTTP API를 통한 데이터 전송**
        - 회원가입, 상품 주문, 데이터 변경
        - 서버 to 서버
            - 백엔드 시스템 통신
            - 앱 클라이언트
                - 아이폰, 안드로이드
        - 웹 클라이언트
            - HTML에서 Form 전송 대신 js를 통한 통신에 사용(ajax)
                - react, vueJs 같은 웹 클라이언트와 API 통신
            - POST, PUT, PATCH : 메시지 바디를 통해 데이터 전송
            - GET : 조회, 쿼리 파라미터로 데이터 전달
            - Content-Type: application/json을 주로 사용 (de facto)

### 5.2 HTTP API 설계 예시
- HTTP API - 컬렉션
    - POST 기반 등록
        - Ex) 회원 관리 API 제공

- HTTP API - 스토어
    - PUT 기반 등록
        - Ex) 정적 컨텐츠 관리, 원격 파일 관리

- HTML FORM 사용
    - 웹 페이지 회원 관리
    - GET, POST만 지원


---

### 회원 관리 시스템 - API 설계
#### POST 기반 등록 **\* 주로 사용**
- 회원 목록 : /members -> **GET**
- 회원 등록 : /members -> **POST**
- 회원 조회 : /members/{id} -> **GET**
- 회원 수정 : /members/{id} -> **PATCH, PUT, POST**
- 회원 삭제 : /members/{id} -> **DELETE**


- POST 기반 등록 특징
    - 클라이언트는 등록될 리소스의 URI를 모른다
        - 회원 등록 /members -> POST
        - POST /members
    - 서버가 새로 등록될 리소스 URI를 생성해준다
      ```
      HTTP/1.1 201 Created
      Location: /members/100
      ```
    - 컬렉션 (Collection)
        - 서버가 관리하는 리소스 디렉토리
        - 서버가 리소스의 URI를 생성하고 관리
        - 여기서 컬렉션은 /members


#### PUT 기반 등록
- 파일 목록 : /files -> **GET**
- 파일 조회 : /files/{filename} -> **GET**
- 파일 등록 : /files/{filename} -> **PUT**
- 파일 삭제 : /files/{filename} -> **DELETE**
- 파일 대량 등록 : /files -> **POST**

- PUT 기반 등록 특징
    - 클라이언트가 리소스 URI를 알고 있어야 한다
        - 파일 등록 /files/{filename} -> PUT
        - PUT /files/star.jpg
    - 클라이언트가 직접 리소스의 URI를 지정한다
    - 스토어 (Store)
        - 클라이언트가 관리하는 리소스 저장소
        - 클라이언트가 리소스의 URI를 알고 관리
        - 여기서 스토어는 /files


#### HTML Form 사용
- 회원 목록 : /members -> **GET**
- 회원 등록 폼 : /members/new -> **GET**
- 회원 등록 : /members/new, /members -> **POST**
- 회원 조회 : /members/{id} -> **GET**
- 회원 수정 폼 : /members/{id}/edit -> **GET**
- 회원 수정 : /members/{id}/edit, /members/{id} -> **POST**
- 회원 삭제 : /members/{id}/delete -> **POST**

- HTML Form 사용 특징
    - 컨트롤 URI의 사용이 불가피
        - GET, POST만 지원하므로 제약이 있음
        - 이를 해결하기 위해 동사로 된 리소스 경로 사용
        - POST의 /new, /edit, /delete가 컨트롤 URI
        - HTTP 메서드로 해결하기 애매한 경우 사용(HTTP API 포함)
        
---

#### 정리 : 참고하면 좋은 URI 설계 개념
- 문서 Document
    - 단일 개념
    - - ex) /members/100, files/star.jpg
- 컬렉션 Collection
    - 서버가 관리하는 리소스 디렉토리
    - 서버가 리소스의 URI를 생성하고 관리
    - ex) /members
- 스토어 Store
    - 클라이언트가 관리하는 자원 저장소
    - 클라이언트가 리소스 URI를 알고 관리
    - ex) /files
- 컨트롤러 Controller, 컨트롤 URI
    - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
    - 동사를 직접 사용
    - ex) /members/{id}/delete
- REST Resource Naming Guide
    - https://restfulapi.net/resource-naming
- REST API에 관하여
    - https://www.youtube.com/watch?v=RP_f5dMoHFc


## 6. HTTP 상태코드
### 6.1 HTTP 상태코드 소개
- 상태코드 : 클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능
    - 1xx (Informational) : 요청이 수신되어 처리중
    - 2xx (Successful) : 요청 정상 처리
    - 3xx (Redirection) : 요청을 완료하려면 추가 행동이 필요
    - 4xx (Client Error) : 클라이언트 오류, 잘못된 문법 등으로 서버가 수행할 수 없음
    - 5xx (Server Error) : 서버 오류, 서버가 정상 요청을 처리하지 못함


- 만약 모르는 상태 코드가 나타나면?
    - Nxx를 기준으로 상위 상태코드로 해석해서 생각하자


- 1xx (Infomational) : 요청이 수신되어 처리중
    - 거의 사용되지 않으므로 생략

### 6.2 2xx - 성공
- 200 OK : 요청 성공
- 201 Created : 요청 성공해서 새로운 리소스가 생성됨.
    - 생성된 리소스는 응답의 Location 헤더 필드로 식별
- 202 Accepted : 요청이 접수되었으나 처리가 완료되지 않음
    - 배치 처리 같은 곳에서 사용
        - Ex) 요청 접수 후, 1시간 뒤에 배치 프로세스가 요청을 처리함
- 204 No Content : 서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음
    - Ex) 웹 문서 편집에서 save 버튼
    - save 버튼의 결과로 아무 내용이 없어도 된다
    - save 버튼을 눌러도 같은 화면을 유지해야 한다
    - 결과 내용이 없어도 204 메시지(2xx)만으로 성공을 인식할 수 있다

### 6.3 3xx - 리다이렉션1
- 요청을 완료하기 위해 유저 에이전트의 추가 조치 필요

- 리다이렉션 이해
    - 웹 브라우저는 3xx 응답 결과에 Location header가 있으면, Location 위치로 자동 이동함

- 리다이렉션 종류
    - 영구 리다이렉션
        - 특정 리소스의 URI가 영구적으로 이동
        - Ex) /members -> /users
        - Ex) /event -> /new-event
    - 일시 리다이렉션
        - 일시적인 변경
        - 주문 완료 후 주문 내역 화면으로 이동
        - PRG : Post/Redirect/Get
    - 특수 리다이렉션
        - 결과 대신 캐시를 사용


- 영구 리다이렉션 : 301, 308
    - 특정 리소스의 URI가 영구적으로 이동
    - 원래의 URL 사용 X, 검색 엔진 등에서도 변경을 인지
    - **301 Moved Permanently**
        - **리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음**
    - **308 Permanently Redirect**
        - 301과 기능은 같음
        - **리다이렉트시 요청 메서드와 본문 유지**

### 6.4 3xx - 리다이렉션2
- 일시적 리다이렉션 : 302, 307, 303
    - 리소스의 URI가 일시적으로 변경
    - 띠리사 감색 엔진 등에서 URL을 변경하면 안됨

- PRG
    - POST로 주문 후에 새로 고침으로 인한 중복 주문 방지
        - POST로 주문 후에 주문 결과 화면을 GET으로 Redirect

- 기타 리다이렉션
    - 300
    - 304 Not Modified
        - 캐시를 목적으로 사용
        - 클라이언트에게 리소스가 수정되지 않았음을 알려준다. 따라서, 클라이언트는 저장된 캐시를 재사용한다.

### 6.5 4xx - 클라이언트 오류, 5xx - 서버 오류
- 4xx (Client Error) : 클라이언트 오류
    - 클라이언트의 요청에 잘못된 문법 등으로 서버가 요청을 수행할 수 없음
    - 오류의 원인이 클라이언트에 있음
    - 똑같은 재시도가 실패할 수 밖에 없음
        - 400 Bad Request : 클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음
        - 401 Unauthorized : 클라이언트가 해당 리소스에 대한 인증이 필요함
            - Authentication 인증 - 본인이 누구인지 확인
            - Authorization 인가 - 권한 부여
        - 403 Forbidden : 서버가 요청을 이해했지만 승인을 거부함
        - 404 Not Found : 요청 리소스를 찾을 수 없음


- 5xx (Server Error) : 서버 오류
    - 500 Internal Server Error : 서버 문제로 오류 발생, 애매하면 500 오류
    - 503 Service Unavailable : 서비스 이용 불가


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
