# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
# 1. 웹 애플리케이션 이해
## 1) 웹 서버, 웹 애플리케이션 서버
- 웹 서버, 웹 애플리케이션 서버(WAS)
    - 웹 서버는 정적 리소스(파일) 제공, WAS는 애플리케이션 로직 수행
    - 사실 점점 두 용어의 경계는 모호해지고 있다
    - 자바는 서블릿 컨테이너 기능을 제공하면 WAS

- 웹 시스템 구성
    - WAS와 DB만으로 시스템 구성
        - 단점
            - WAS가 너무 많은 역할을 담당 -> 서버 과부화 우려
            - 비용이 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있음
            - WAS 장애시 오류 화면조차 노출 불가능
    - WEB, WAS, DB로 시스템 구성
        - 정적 리소스는 웹 서버가 처리
        - 웹 서버는 애플리케이션 로직처럼 동적 처리가 필요하면 WAS에 요청 위임
        - WAS는 중요한 애플리케이션 로직 처리 전담
        - 장점
            - 정적 리소스가 많이 사용되면 웹 서버만 증설
            - 애플리케이션 리소스가 많이 사용되면 WAS만 증설
            - WAS, DB 장애시 WEB 서버가 오류 화면 제공 가능

## 2) 서블릿
- 만약 서블릿이 없다면?
    - HTML FORM 데이터 전송만 하더라도 서버에서 처리해야 하는 업무가 많다.
        - Ex, TCP/IP, Socket, HTTP 관련 업무들 + 비즈니스 로직

- 서블릿은 서버가 비즈니스 로직만 처리하는 것을 가능케함
    - Ex, HTTP 요청시
        - WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체 호출
        - 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
        - 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
        - WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

- 서블릿 컨테이너
    - 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 한다
    - 서블릿을 계속 생성하는 건 비효율적이므로 **서블릿 객체를 싱글톤**으로 관리하여 재사용함
        - 공유 변수 사용에 주의
    - ❗️ **_동시 요청을 위한 멀리 쓰레드 처리를 지원함_**

## 3) 동시 요청 - 멀티 쓰레드
- Thead란?
    - 애플리케이션 코드를 하나하나 순차적으로 하나의 코드 라인만 실행
    - 동시 처리가 필요하면, 쓰레드를 추가 생성


- CASE1 : 쓰레드 하나만 사용하는 경우
    - 단일 요청
        - 문제 X
    - 다중 요청
        - 먼저 들어온 요청이 끝날 때까지 다른 요청은 계속 대기
- CASE2 : 요청마다 쓰레드를 생성하는 경우
    - 단일 요청
        - 문제 X
    - 다중 요청
        - 동시 요청 처리 O
        - 서버 리소스가 허용할 떼까지 처리 O
        - 한 쓰레드가 지연되어도 다른 쓰레드에 영향 X
        - 단점
            - 쓰레드의 높은 생성 비용 : 고객의 요청에 대한 응답 속도 지연
            - context switching 발생
            - 쓰레드 생성에 제한이 없으면, 서버 리소스를 초과하여 서버가 죽을 수 있음


- 쓰레드 풀
    - WAS의 주요 튜닝 포인트는 최대 쓰레드(max thread) 수
    - 최대 쓰레드 수가 너무 낮으면
        - 서버 리소스에 비해 처리 속도가 현저히 낮아 효율 X
    - 최대 쓰레드 수가 너무 높으면
        - 쓰레드 생성에 제한이 없으면, 서버 리소스를 초과하여 서버가 죽을 수 있음
    - 장애 발생시
        - 클라우드 O -> 서버 먼저 늘리고, 최대 쓰레드 수 튜닝
        - 클라우드 X -> 열심히 최대 쓰레드 수 튜닝
    - 최적의 최대 쓰레드 수 찾기
        - 상황마다 다르므로 성능 테스트를 통해 찾기
            - 아파치 AB, 제이미터, **_nGrinder_**


- WAS의 멀티 쓰레드 지원
    - WAS가 멀티 쓰레드를 처리하므로 개발자가 해당 부분을 신경쓰지 않아도 됨
    - 단, 싱글톤 객체(서블릿, 스프링 빈)를 주의하여 사용하기

## 4) HTML, HTTP API, CSR, SSR
- SSR
    - HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달
    - 주로 정적인 화면에 사용
    - JSP, 타임리프
- CSR
    - HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
    - 주로 동적인 화면에 사용, 웹 환경을 마치 앱 처럼 필요한 부분부분 변경할 수 있음
    - React, Vue.js

## 5) 자바 백엔드 웹 기술 역사
- Servlet(1997) -> JSP(1999) -> MVC Pattern(Servlet + JSP) -> MVC Framework(2000)
- 스프링 웹 기술의 분화
    - Web Servlet - Spring MVC
        - Annotation 기반의 **Spring MVC**가 MVC Framework 평정 -> Tomcat을 내장한 **Spring Boot**의 등장
    - Web Reactive - Spring WebFlux
        - 뭔가 다 좋고 한데, 기술적 난이도가 매우 높고, RDB 지원도 부족, Spring MVC가 여전히 주로 사용
- 자바 뷰 템플릿 역사
    - JSP -> Freemarker / Velocity -> Thymeleaf

# 2. 서블릿
## 1) Hello 서블릿
- `@ServletComponentScan`
    - 스프링 빈의 컴포넌트 스캔처럼 서블릿의 컴포넌트 스캔 역할

- `@WebServlet(String name, String urlPatterns)`
    - name : 서블릿 이름
    - urlPatterns : URL 매핑

- HTTP 요청을 통해 매핑된 URL이 호출되면 `void service()` 메서드가 실행됨
    - `protected void service(HttpServletRequest request, HttpServletResponse response)`
        - 인자로 들어오는 request, response 객체를 통해 HTTP response, request 설정 가능

## 2) HttpServletRequest - 개요
- 편의를 위해 HttpServletRequest 객체가 HTTP 요청 메시지를 파싱하여 제공함
    - START LINE, HEADER, BODY
- 임시 저장소 기능
    - HTTP 요청의 시작부터 끝날 때까지 유지되는 기능
    - 저장 : `request.setAttribute(name, value)`
    - 조회 : `request.getAttribute(name)`
- 세션 관리 기능
    - `request.getSession(create: true)`
- ❗️: HttpServletRequest, HttpServletResponse를 잘 이해하기 위해서는 HTTP 스펙에 대해 이해가 필요하다

## 3) HttpServletRequest - 기본 사용법
- HttpServletRequest가 제공하는 기본 메서드 알아보기
    - 코드를 통해 확인하자

## 4) HTTP 요청 데이터 - 개요
- GET - 쿼리 파라미터
    - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함하여 전달
- POST - HTML Form
    - 메시지 바디에 쿼리 파라미터 형식으로 전달
- HTTP message body
    - HTTP API에서 주로 사용 : JSON, XML, TEXT

## 5) HTTP 요청 데이터 - GET 쿼리 파라미터
- 전체 파리미터 조회
    - `request.getParameterNames();`
- 단일 파리미터 조회
    - `request.getParameter()`
- 이름이 같은 복수 파라미터 조회
    - `request.getParameterValues();`

## 6) HTTP 요청 데이터 - POST HTML Form
- POST HTML Form은 데이터를 `application/x-www-form-urlencoded` 형식으로 보낸다
    - 이 형식은 GET 쿼리 파라미터 형식과 같으므로, 쿼리 파라미터 조회 메서드를 그대로 사용할 수 있다
    - Client 입장에서는 두 방식에 차이가 있지만, Server 입장에서는 둘의 형식이 동일하기 떄문이다

## 7) HTTP 요청 데이터 - API 메시지 바디 - 단순 텍스트
- HTTP 메시지 바디 데이터 읽어오기
    - `ServletInputStream inputStream = request.getInputStream();`
- Spring이 지원하는 stream을 string으로 변환해주는 함수
    - `StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8)`

## 8) HTTP 요청 데이터 - API 메시지 바디 - JSON
- `application/json` 형식의 데이터 읽어오기
- JSON 데이터를 객체 형태로 변환
    - `HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);`
    - 이를 사용하기 위해서는 Jackson, Gson 같은 라이브러리를 사용해야 한다.
        - Spring boot에서는 Jackson(ObjectMapper)을 기본으로 제공

## 9) HttpServletResponse - 기본 사용법
- HTTP 상태 설정
    - ``` java
      response.setStatus(HttpServletResponse.SC_OK);
      ```
- HTTP 헤더 설정
    - Content-Type
        - ``` java
          // preferred
          response.setContentType("text/plain");
          ```
        - ``` java
          response.setHeader("Content-Type", "text/plain;charset=utf-8");
          ```
    - Character-Encoding
        - ``` java
          // preferred
          response.setCharacterEncoding("utf-8");
          ```
    - Content-Length (생략시 자동 생성)
        - ``` java
          response.setContentLength();
          ```
- Cookie
    - ``` java
      response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
      ```
    - ``` java
      // preferred
      Cookie cookie = new Cookie("myCookie", "good");
      cookie.setMaxAge(600); //600초
      response.addCookie(cookie);
      ```
- Redirect
    - ``` java
      // preferred
      response.sendRedirect("/basic/hello-form.html");
      ```
    - ``` java
      response.setStatus(HttpServletResponse.SC_FOUND); //302
      response.setHeader("Location", "/basic/hello-form.html");
      ```

## 10) HTTP 응답 데이터 - 단순 텍스트, HTML
- HTML을 보내기 위한 Content-Type 설정
    - `response.setContentType("text/html")`
- PrintWriter를 통한 HTML 태그 보내기
    - ```
      PrintWriter writer = response.getWriter();
      writer.println("<html>");
      ```
## 11) HTTP 응답 데이터 - API JSON
- JSON을 보내기 위한 Content-Type 설정
    - `response.setContentType("application/json");`
- Jackson을 통한 Json2String
    - `objectMapper.writeValueAsString()`