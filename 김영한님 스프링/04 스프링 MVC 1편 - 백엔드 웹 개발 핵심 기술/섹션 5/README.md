# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
# 5. 스프링 MVC - 구조 이해
## 1) 스프링 MVC 전체 구조
- 동작 순서
    1. 핸들러 조회
        - 핸들러 매핑을 통해 요청된 URL에 매핑된 핸들러(컨트롤러)를 조회한다
    2. 핸들러 어댑터 조회
        - 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다
    3. 핸들러 어댑터 실행
        - 핸들러 어댑터를 실행한다
    4. 핸들러 실행
        - 핸들러 어댑터가 실제 핸들러를 실행한다
    5. ModelAndView 반환
        - 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환하여 반횐한다
    6. viewResolver 호출
        - 뷰 리졸버를 찾고 실행한다
    7. View 반환
        - 뷰 리졸버는 뷰를 논리 이름을 물리 이름으로 바꾸고, 랜더링 역할을 담당하는 뷰 객체에게 반환한다
    8. 뷰 랜더링
        - 뷰를 통해서 뷰를 랜더링한다

## 2) 핸들러 매핑과 핸들러 어댑터
- `@Controller`를 사용하지 않는 방식의 Controller 인터페이스
    - ``` java
      public interface Controller {
          ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;
      }
      ```
    - `@Component("/springmvc/old-controller")` 를 사용하여 빈 등록
        - "/springmvc/old-controller" 라는 이름으로 등록됨
        - 향후 빈의 이름으로 URL 매핑이 이뤄짐
- Spring이 위의 Controller를 호출하는 방법
    1. HandlerMapping 조회
       > 0 = RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용  
       1 = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.
        - 1을 통해 "/springmvc/old-controller" 라는 이름으로 등록된 스프링 빈을 찾아 핸들러로 반환한다
    2. HandlerAdapter 조회
       > 0 = RequestMappingHandlerAdapter : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용  
       1 = HttpRequestHandlerAdapter : HttpRequestHandler 처리  
       2 = SimpleControllerHandlerAdapter : Controller 인터페이스 (애노테이션 X) 처리
        - 2를 통해 Controller 인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행
    3. HandlerAdapter 실행
        - 디스패처 서블릿이 조회한 `SimpleControllerHandlerAdapter`를 실행하면서 핸들러 정보도 함께 반환
        - `SimpleControllerHandlerAdapter`는 oldController를 내부에서 실행하고, 그 결과를 반환

## 3) 뷰 리졸버
- View를 사용하기 위한 OldController 수정
  ``` java
  public class OldController implements Controller {
      
      @Override
      public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
          System.out.println("OldController.handleRequest");
          return new ModelAndView("new-form");
      }
  }
  ```
    - 여전히 `Whitelabel Error Page`가 나온다.
    - 그 이유는 ViewResolver가 `ModelAndView("new-form")` 뷰를 찾지 못했기 때문이다.
- 스프링 부트가 자동 등록하는 뷰 리졸버
  > 1 = BeanNameViewResolver : 빈 이름으로 뷰를 찾아서 반환한다. (예: 엑셀 파일 생성 기능에 사용)  
   2 = InternalResourceViewResolver : JSP를 처리할 수 있는 뷰를 반환한다.
    1. `"new-form"`이라는 빈이 없기 때문에 `InternalResourceViewResolver`가 호출된다
    2. 그러나 여전히 `"new-form"` 이라는 논리 뷰를 실행할 방법은 없다
    3. `application.properties`에 아래 코드를 추가함으로써 전체 경로를 완성한다.
        - ``` properties
          spring.mvc.view.prefix=/WEB-INF/views/
          spring.mvc.view.suffix=.jsp
          ```
    4. `InternalResourceViewResolver`는 `InternalResourceView`를 반환한다
    5. `InternalResourceView`는 forward()를 호출하여 JSP를 실행

## 4) 스프링 MVC - 시작하기
- `@RequestMapping` 사용한 애노테이션 컨트롤러 (⭐️ 99.9% 대세)
    - ``` java
      @Controller
      public class SpringMemberFormControllerV1 {
          @RequestMapping("/springmvc/v1/members/new-form")
          public ModelAndView process() {
              return new ModelAndView("new-form");
          }
      }
      ```
    - `@Controller`
        - 자동으로 스프링 빈 등록
        - 스프링 MVC에서 애노테이션 기반 컨트롤러로 인식
    - `@RequestMapping`
        - 요청 정보를 매핑
        - 인자에 작성된 URL이 호출되면 해당 메서드가 호출되므로, 메서드의 이름은 임의로 작성 가능
    - `ModelAndView`
        - 모델과 뷰 정보를 담아서 반환
- `@Controller`와 `@Component`의 차이는 뭘까❓
    - https://github.com/Java-Chip4/StudyingRecord/issues/41

## 5) 스프링 MVC - 컨트롤러 통합
- 하나의 컨트롤러에 여러 개의 컨트롤러 통합하기
    - `@RequestMapping`이 메서드 단위에 적용되어 있기 때문에 가능
    - `@RequestMapping`을 클래스 단위에 적으면, 중복되는 URI를 제거 가능
      ``` java
      @Controller
      @RequestMapping("/springmvc/v2/members")
      public class SpringMemberControllerV2 { ... }
      ```

## 6) 스프링 MVC - 실용적인 방식
- Model 파라미터를 사용하여 간편하게 모델 데이터 반환하기
    - Model vs ModelAndView 무슨 차이지❓
        - https://github.com/Java-Chip4/StudyingRecord/issues/40
- viewName 직접 반환을 통한 간편화
- `@RequestParam` 을 통한 HTTP 요청 파라미터 가져오기
- `@GetMapping`, `@PostMapping`을 활용한 HTTP Method 제한
