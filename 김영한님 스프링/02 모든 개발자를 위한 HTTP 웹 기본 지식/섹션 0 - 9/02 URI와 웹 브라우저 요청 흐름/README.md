# 모든 개발자를 위한 HTTP 웹 기본 지식
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

---

참고 : [김영한님 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/)
