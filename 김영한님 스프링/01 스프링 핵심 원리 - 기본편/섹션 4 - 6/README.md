# 스프링 핵심 원리 - 기본편
## 4. 스프링 컨테이너와 스프링 빈
### 1) 스프링 컨테이너 생성
- 스프링 컨테이너인 ApplicationContext는 인터페이스로, 여러 구현체를 담을 수 있다. 여기서는 자바 설정 클래스를 기반으로 `AnnotationConfigApplicationContext` 구현체를 사용하였다.
- 스프링 컨테이너를 생성할 때는 구성 정보(Ex, AppConfig) 지정이 필요하다
- ❗[TIP 2] : 스프링 Bean은 항상 다른 이름을 부여해야 한다
### 2) 컨테이너에 등록된 모든 빈 조회
- `ac.getBeanDefinitionNames()`을 통한 모든 Bean의 이름 조회
- `ac.getBean()`을 사용하여 빈 이름으로 Bean 인스턴스 조회
    - `getBean(Name, Class)`로 호출하는 경우에는 정확하게 Class에 맞는 Bean 인스턴스가 반환이 되지만,
      `getBean(Name)`은 Name 이름을 가진 Bean 인스턴스를 반환하므로 Object에 담는 것이 좋다.
- `getRole()`을 사용한 Bean 구분
    - ROLE_APPLICATION : 직접 등록한 애플리케이션 Bean
    - Role ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 Bean
### 3) 스프링 빈 조회 - 기본
- `ac.getBean(MemberService.class)`와 같이 타입만으로 Bean 조회
- 실패하는 테스트 역시 필요하다. 이는 `assertThrows()`를 주로 사용한다.
### 4) 스프링 빈 조회 - 동일한 타입이 둘 이상
- `ac.getBeansOfType(Class)`를 사용하면 해당 타입의 모든 Bean을 조회할 수 있다
### 5) 스프링 빈 조회 - 상속 관계
- 부모 타입으로 조회하면, 자식 타입도 함께 조회한다
### 6) BeanFactory와 ApplicationContext
- BeanFactory는 스프링 컨테이너의 최상의 인터페이스다
- ApplicationContext는 BeanFactory의 모든 기능을 상속 받아서 제공한다.
  그 외에 MessageSource, EnviromentCapable, ApplicationPulisher, ResourceLoader의 기능도 상속 받아서 제공한다.
### 7) 다양한 성정 형식 지원 - 자바 코드, XML
- XML을 통한 스프링 컨테이너 설정
    - `GenericXmlApplicationContext`를 사용해 resources/appConfig.xml 를 넘겨주면 된다
### 8) 스프링 빈 설정 메타 정보 - BeanDefinition
- 설정 형식을 유연하게 지원 가능한 이유는 BeanDeifinition이 추상이기 때문이다. 스프링은 XML 혹은 자바 코드를 읽어서 BeanDefinition을 만든다.  
  (이렇게 만들어진 BeanDeifinition은 factoryBean을 통해 만들었다고 한다.)
- 그래서 BeanDefinition을 Bean 설정 메타정보라고 한다. 스프링 컨테이너는 이 Bean 설정 메타정보를 기반으로 스프링 Bean을 생성한다.
- factoryBean을 통해 만드는 것 외에도 직접 BeanDefinition을 정의하여 사용하는 것도 가능하지만, 이는 스프링 내부 코드나 스프링 관련 오픈소스에서나 사용된다.

## 5. 싱글톤 컨테이너
### 1) 웹 애플리케이션과 싱글톤
- 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때마다 새로운 객체를 생성한다
- 만약 TPS가 높다면, 수많은 객체가 생성되어야 하기 때문에 메모리 낭비가 심하다
### 2) 싱글톤 패턴
- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
- ❗[TIP 3] 잘 설계한 객체는 잘못 프로그래밍 했을 때, 컴파일 오류가 나도록 하는 것
- ❓[싱글톤 패턴에 대해 더 알아보았다]()
- ❓[싱글톤 패턴은 DIP를 위반한다?](https://www.inflearn.com/questions/458136)
### 3) 싱글톤 컨테이너
- 스프링 컨테이너는 싱글톤 패턴을 직접 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다
    - 이런 기능을 싱글톤 레지스트리라고 부른다
### 4) 싱글톤 방식의 주의점 ⭐️
- 싱글톤 방식은 인스턴스 하나를 여러 클라이언트가 공유하기 때문에 **stateless**(무상태)로 설계해야 한다
    - 클라이언트에 의존적인 필드 제거
    - 클라이언트가 값을 변경할 수 있는 필드 제거
    - 가급적 읽기만 허용
    - 필드 대신 공유되지 않는 지역변수, 파라미터, ThreadLocal 사용
### 5) @Configuration과 싱글톤
- 스프링은 @Configuration에서 만들어진 스프링 Bean에게 어떻게든 싱글톤을 보장한다
### 6) @Configuration과 바이트코드 조작의 마법
- @Configuration가 스프링 Bean에게 싱글톤을 보장하는 방법은 바이트코드 조작 라이브러리인 CGLIB를 사용하기 때문이다. CGLIB를 통해 AppConfig를 조작하여 새로운 AppConfig를 만들어 싱글톤을 보장한다.
- 그래서 @Configuration없이 등록된 스프링 Bean은 추가적인 코드 없이는 싱글톤 보장이 되지 않는다

## 6. 컴포넌트 스캔
### 1) 컴포넌트 스캔과 의존관계 자동 주입 시작하기
- `@ComponentScan`과 `@Component`를 사용한 컴포넌트 스캔
    - `excludeFilters`를 사용하여 특정 클래스를 컴포넌트 스캔에서 배제
- `@Autowired`를 사용한 의존관계 주입
    - 스프링 컨테이너가 자동으로 타입이 같은 스프링 빈을 찾아서 주입한다
### 2) 탐색 위치와 기본 스캔 대상
- `basePackages`를 통한 컴포넌트 스캔의 탐색 위치를 지정
    - ❗[TIP 4] `basePackagesClasses`는 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.
      그러나 이를 사용하지 않으면, `@ComponentScan`이 붙은 설정 정보 클래스의 패키지가 탐색 시작 위치로 지정된다.
      이러한 관례를 잘 이용하면, 패키지 위치를 지정하지 않고 깔끔하게 설정 정보 클래스를 구현할 수 있다.
- 자바 문법 상으로 애노테이션에는 상속관계가 없음에도 @Repository, @Controller 등이 @Component를 가질 수 있는 이유는 스프링 자체 기능이기 때문이다
### 3) 필터
- `includeFilters`, `excludeFilters`를 활용한 테스트 작성
### 4) 중복 등록과 충돌
- 자동 빈 등록과 수동 빈 등록이 중복으로 이뤄질 경우에는 스프링 부트에서 오류를 발생시킨다
    - 그러나 overriding 옵션을 주면 수동 빈이 자동 빈을 오버라이딩하여 실행된다
- ❗[TIP 5] 개발은 협업이기 때문에 명확하지 않은 코드는 나쁜 코드다.
