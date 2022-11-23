# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
# 3. 서블릿, JSP, MVC 패턴
## 1) 회원 관리 웹 애플리케이션 요구사항
- Member 클래스 생성
- Singleton MemoryMemberRepository 클래스 생성
- MemberRepository에 대한 Test 작성

## 2) 서블릿으로 회원 관리 웹 애플리케이션 만들기
- 서블릿과 자바 코드만으로 HTML 생성
    - 서블릿 덕분에 PrintWriter를 사용하여 동적 HTML을 만들 수 있지만 비효율적이다
    - 그래서 템플릿 엔진이 나왔고, 앞으로는 템플릿 엔진을 사용할 것이다

## 3) JSP로 회원 관리 웹 애플리케이션 만들기
- JSP를 사용하기 위해 build.gralde에 JSP 라이브러리 추가
- JSP 기본 문법
    - Java import
        - `<%@ page import=" ~~ " %>`
    - Java code
        - `<% ~~ %>`
    - Java code 출력
        - `<%= ~~ %>`
- JSP의 한계
    - HTML과 Java 코드가 뒤섞여 지저분하고 복잡하다
    - 비즈니스 로직과 뷰 영역이 함께 공존한다
    - JSP가 너무 많은 역할을 한다

## 4) MVC 패턴 - 개요
- ❗[TIP] : 설계시, 두 로직의 변경 주기가 다르면 분리하자
    - 라이프 사이클이 다르면, 일반적으로 서로에게 영향을 주지 않으므로 유지보수가 더 쉬워진다
- MVC : Model - View - Controller
    - Controller
        - HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직 실행
        - 뷰에 전달할 결과 데이터를 조회해서 모델에 저장
        - Service
            - 비즈니스 로직은 Service에서 별도로 처리
            - 컨트롤러가 너무 많은 역할을 담당하는 것을 막기 위함
    - Model
        - 뷰에 출력할 데이터 저장체
        - 덕분에 뷰는 다른 일을 신경쓰지 않고 화면 렌더링에만 집중할 수 있다
    - View
        - 모델 데이터를 사용해서 화면 렌더링
- 곁들이기 : https://velog.io/@eddy_song/mvc

## 5) MVC 패턴 - 적용
- Controller(Servlet)에서 View(JSP) 호출하기
    - Dispatcher 가져오기
        - ``` java
          String viewPath = "/WEB-INF/views/new-form.jsp"
          request.getRequestDispatcher(viewPath);
          ```
    - forward를 통한 JSP 호출
        - ``` java
          dispatcher.forward(request, response);
          ```
- Model에 데이터를 보관
    - `request.setAttribute("member", member);`
- JSP를 학습하는 것이 주 목표가 아니므로, 사용한 JSP 문법에 대해서는 다루지 않았음

## 6) MVC 패턴 - 한계
- Controller에 여전히 중복 코드가 많이 존재함
    - Ex : forward(), ViewPath
- 사용하지 않는 코드
    - Ex : HttpServletRequest request, HttpServletResponse response
- 공통처리가 어렵다❗️
    - Ex : 여러 컨트롤러에서 공통으로 처리해야 하는 일이 있는 경우, 또 중복이 이뤄짐
    - 해결 방안
        - Controller 호출 전에 먼저 공통 기능을 처리해야 함
        - **Front Controller 패턴**을 통해 해결 가능

# 4. MVC 프레임워크 만들기
## 1) 프론트 컨트롤러 패턴 소개
- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
- 스프링 웹 MVC와 프론트 컨트롤러
    - 스프링 웹 MVC의 핵심도 바로 Front Controller
    - DispatcherServlet이 Front Controller 패턴으로 구현됨

## 2) 프론트 컨트롤러 도입 - v1
- ControllerV1 인터페이스를 구현한 Form, Save, List 컨트롤러 작성
- ControllerV1 다형성을 활용한 FrontController 구현

## 3) View 분리 - v2
- Dispatcher, forward를 담당하는 MyView 클래스 분리
    - MyView.render()를 통해 해당 로직을 일관되게 처리함

## 4) Model 추가 - v3
- Controller의 서블릿 종속성 제거
    - Controller에서 ModelView를 사용하여 서블릿 기술을 전혀 사용하지 않도록 변경
    - 이는 구현 코드의 단순화, 간편한 테스트 코드를 이뤄냄
- View 이름 중복 제거
    - Controller에서는 View의 논리 이름만 반환하도록 단순화
    - 중복되는 경로는 Front Controller의 ViewResolver에서 처리

## 5) 단순하고 실용적인 컨트롤러 - v4
- 매번 ModelView 객체를 생성하고 반환하는 부분 개선
    - model 객체를 파라미터로 전달
    - 덕분에 Controller는 viewName만 반환

## 6) 유연한 컨트롤러1 - v5
- 다양한 버전의 Controller를 사용하기 위해 Adapter Pattern 적용하기
    - HandlerAdapter
        - Adapter Pattern을 적용한 HandlerAdapter 덕분에 다양한 컨트롤러 호출 가능
        - ``` java
          public interface MyHandlerAdapter {
            boolean support(Object handler);
            ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
          }
          ```
            - `boolean support(Object handler);`
                - Adapter가 handler(컨트롤러)를 처리할 수 있는지 판단
            - `ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler);`
                - Adapter는 실제 컨트롤러를 호출하고, 그 결과로 ModelView를 반환
                - 이전에는 Front Controller가 직접 실제 컨트롤러를 호출했지만, 이제는 Adpater를 통해서 실제 컨트롤러가 호출
    - Handler
        - Controller의 이름을 더 넓은 개념인 Handler로 변경하여 처리
- Adpater Pattern을 적용한 FrontController
    - ``` java
      Object handler = getHandler(request);
      ```
        - handlerMappingMap에서 URI에 매핑된 handler(컨트롤러) 객체를 찾아서 반환
    - ``` java
      MyHandlerAdapter adapter = getHandlerAdapter(handler);
      ```
        - handler(컨트롤러)를 처리할 수 있는 어댑터를 `adapter.supports(handler)`를 통해 찾는다
        - 이후, 해당 HandlerAdpater 구현체 반환
    - ``` java
      ModelView mv = adapter.handle(request, response, handler);
      ```
        - 어댑터의 `handle()` 메서드를 통해 실제 어댑터가 호출
        - 그 결과를 어댑터에 맞추어 반환
- Adapter Pattern에 대해 다룬 Github Issue
    - https://github.com/Java-Chip4/StudyingRecord/issues/16

## 7) 유연한 컨트롤러2 - v5
- 두 메서드에 확장 코드를 DI 할 수 있도록 변경하면 OCP를 지킬 수 있게 됨
    - `initHandlerMappingMap();`
    - `initHandlerAdapters();`
