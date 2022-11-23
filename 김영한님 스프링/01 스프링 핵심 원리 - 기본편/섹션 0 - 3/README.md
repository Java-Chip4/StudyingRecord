# 스프링 핵심 원리 - 기본편
## 1. 객체 지향 설계와 스프링
### 1) 이야기 - 자바 진영의 추운 겨울과 스프링의 탄생
- 옛날 옛적에 EJB 라는 지옥이 있었다. 이를 탈출하기 위해 Rod Johnson이라는 사람이 [J2EE Design and Development](http://index-of.es/Java/Wrox%20Press%20-%20Expert%20one-on-one%20J2EE%20Design%20and%20Development.pdf) 라는 책을 썼다.
  이 책의 예제코드가 지금의 Spring Framework가 되었다. EJB라는 Winter가 가고 Spring이 오게 된 것이다.
### 2) 스프링이란?
- 좋은 객체 지향 애플리케이션을 개발할 수 있게 도와주는 프레임워크
- 스프링 부트는 스프링 생태계를 편리하게 사용할 수 있도록 지원한다
### 3) 좋은 객체 지향 프로그래밍이란?
- 객체들의 모임, 객체들의 협력 등이 있지만 가장 중요한 것은 유연하고 변경이 용이하다는 것이다. 이를 위해서 역할과 구현을 분리하여 객체를 설계한다.
- 객체는 언제나 협력 관계를 가진다. 다형성은 클라이언트를 변경하지 않고, 서버의 구현 기능을 유연하게 변경해도 두 객체의 협력에 문제가 없도록 만든다.
- 인터페이스가 바뀌면 객체 간 협력에 문제가 생긴다. 그러므로 인터페이스를 안정적으로 바뀌지 않게 잘 설계하는 것이 중요하다.
### 4) 좋은 객체 지향 설계의 5가지 원칙 (SOLID)
- **SRP : 단일 책임 원칙 (Single Responsibility Principle)**
    - 문맥과 상황에 따라 다르지만, 한 클래스는 하나의 책임만 가져야 한다
    - 코드 변경이 있을 때, 파급 효과가 적다면 SRP를 잘 따른 것이다

- **OCP : 개방 폐쇄 원칙 (Open/Closed Principle) ⭐️**
    - 다형성을 활용하여 확장에는 열려 있어야 하지만, 변경에는 닫혀 있어야 한다
    - 역할과 구현의 분리를 생각해보자

- **LSP : 리스코프 치환 원칙 (Liskov Substitution Principle)**
    - 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것이다
    - 즉, 본래 기능의 보장을 말한다

- **ISP : 인터페이스 분리 원칙 (Interface Segregation Principle)**
    - 여러 개의 역할로 분리된 인터페이스가 하나의 범용 인터페이스보다 낫다. 그 이유는 대체 가능성이 높아지기 때문이다.

- **DIP : 의존관계 역전 원칙 (Dependency Inversion Principle) ⭐**️
    - 추상화(역할)에 의존해야지, 구체화(구현)에 의존하면 안된다
    - 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다


- ❓ : 그러나 앞선 MemberService 클라이언트는 구현 클래스에 의존적이었다. 다형성을 사용했지만, OCP와 DIP를 지킬 순 없었다.  
  `MemberRepository m = new MemoryMemberRepository(); //기존 코드`  
  `MemberRepository m = new JdbcMemberRepository(); //변경 코드`  
  그렇기에 객체의 생성과 연관관계를 맺어줄 별도의 조립 / 설정자가 필요하다.

## 2. 스프링 핵심 원리 이해1 - 예제 만들기
### 1) 프로젝트 생성
- 스프링을 사용하지 않고 순수한 자바로만 구현하는 프로젝트
### 2) 비즈니스 요구사항과 설계
- 미확정된 부분이 있어도, 역할과 구분을 구분하여 개발하면 추후 구현체를 갈아끼울 수 있다
### 3) 회원 도메인 설계
- 도메인 협력관계 : 기획자와의 커뮤니케이션에 사용
- 클래스 다이어그램 : 도메인 협력관계를 구체화하여 개발자가 구현사항을 적은 것
- 객체 다이어그램 : 구현체는 동적으로 결정되기 때문에 서버 런타임시 어떻게 돌아가는지 한 눈에 보기 위함
### 4) 회원 도메인 개발
- 회원 Entity, Repository, Service에 대한 인터페이스와 구현체 작성
- HaspMap을 사용하여 MemoryMemberRepository를 구현했다. 그러나 HashMap 클래스를 살펴보면, synchronized 키워드가 존재하지 않아 Multi-Thread 환경에서 사용할 수 없다.
  동시에 HaspMap에 접근하면, 값이 덮어씌어지는 동시성 문제가 발생하기 때문이다. 그래서 실무에서는 ConcurrentHashMap을 사용한다.
    - ❓ ConcurrentHashMap 이란 : ConcurrentHashMap은 get() 메소드에는 synchronized가 존재하지 않고, put()에만 존재한다.
      이는 여러 쓰레드가 동시에 읽는 것은 가능하지만, 동시에 쓰는 것은 불가능하다는 뜻이다.
    - ❓ 그렇다면, 어떤 방식으로 Thread-safe하게 만드는걸까? : ConcurrentHashMap은 버킷 단위로 Lock을 사용한다. 살펴보면, 아래와 같은 코드가 있다.  
      `private static final int DEFAULT_CAPACITY = 16;`  
      `private static final int DEFAULT_CONCURRENCY_LEVEL = 16;`  
      그렇기 때문에 버킷의 수와 동시 작업 가능한 쓰레드의 수가 같은 것이다.
      그래서 여러 쓰레드에서 ConcurrentHashMap에 접근하더라도, 다른 버킷을 작업하는 것이라면 경쟁이 일어나지 않는다.  
      - 참고 : [https://devlog-wjdrbs96.tistory.com/269](https://devlog-wjdrbs96.tistory.com/269)
### 5) 회원 도메인 실행과 테스트
- Java 코드로 하는 테스트와 junit을 통한 단위 테스트의 차이 : 일일이 콘솔을 눈으로 확인하는 것보다는 코드를 변경할 때마다 자동적이고, 가시적으로 확인할 수 있는 junit 단위 테스트가 낫다고 느껴진다
- ❗️코드는 여전히 OCP, DIP 위반 중 : MemberServiceImpl은 추상화와 구체화 둘에 모두 의존하고 있기 때문이다   
  `private final MemberRepository memberRepository = new MemoryMemberRepository();`
### 6) 주문과 할인 도메인 설계
- 회원 저장소 구현체와 할인 정책 구현체가 바뀌더라도, 기존 역할들의 협력관계를 그대로 재사용할 수 있도록 역할과 구현을 분리하여 설계
### 7) 주문과 할인 도메인 개발
- OrderService, DiscountPolicy 인터페이스와 구현체 작성
- OrderServiceImpl가 할인 정책에 의존하지 않기 때문에 단일 책임 원칙(SRP)이 잘 지켜진 것
### 8) 주문과 할인 도메인 테스트

## 3. 스프링 핵심 원리 이해2 - 객체 지향 원리 적용
### 1) 새로운 할인 정책 개발
- @DisplayName을 사용한 테스트
### 2) 새로운 할인 정책 적용과 문제점
- OrderServiceImpl 클라이언트는 DiscountPolicy의 추상과 구현체 모두 의존하고 있다. 이는 DIP와 OCP를 모두 위반한다.
  이를 해결하기 위해 해당 클라이언트의 구현체 의존을 삭제하였다. 그러나 클라이언트와 구현체를 이어주는 코드가 없는데 접근을 하기 때문에 NPE가 발생한다.
  이 문제를 해결하기 위해서는 누군가가 클라이언트에 구현체를 생성하고 주입해주어야 한다.
    - ❓ : 앞서 사용했던 `@AutoWired`를 통해 생성자 DI를 사용하면 되지 않을까?
### 3) 관심사의 분리
- AppConfig라는 애플리케이션의 전체 동작 프로세스 구성을 책임지는 클래스 생성
    - 이를 통해 구현체를 선택 및 생성하고, 클라이언트와 연결시켜 생성자 DI가 가능하게 만들 수 있다
- 생성자 DI를 통해 클라이언트의 구현체 의존을 제거함으로써 OCP, DIP 위반 문제를 해결
### 4) AppConfig 리팩터링
- 앞에서 구현한 AppConfig의 중복 제거와 역할에 따른 구현 분리
### 5) 새로운 구조와 할인 정책 적용
- AppConfig로 인해 사용영역과 구성영역의 분리가 가능해짐
### 6) 전체 흐름 정리
### 7) 좋은 객체 지향 설계의 5가지 원칙의 적용
- SRP : OrderServiceImpl 클라이언트에서 구현체 의존를 제거하여 SRP 적용
- DIP : AppConfig에서 OrderServiceImpl로 구현체를 DI함으로써 DIP 적용
- OCP : DiscountPolicy 구현체가 바뀌어도 OrderServiceImpl 코드 수정이 필요하지 않으므로 OCP 적용
### 8) IoC, DI, 그리고 컨테이너
- **제어의 역전 IoC(Inversion of Control)**
  : 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것
  - 프로그램에 대한 제어 흐름을 OrderServiceImpl 클라이언트가 아닌, AppConfig가 가진다
  - **의존관계 주입 DI(Dependency Injection)**
  : 애플리케이션 런타임때, 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결 되는 것
  - ❗[TIP 1] : 의존관계는 정적인 클래스 의존관계와, 실행 시점에서 결정되는 동적인 인스턴스 의존관계를 분리해서 생각해야 한다
  - 정적인 클래스 의존관계 : 클래스 다이어그램. 클래스가 사용하는 import code만 보고도 판단 가능
  - 동적인 인스턴스 의존관계 : 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존관계
  - **IoC 컨테이너 / DI 컨테이너**
  : AppConfig와 같이 객체를 생성하고 관리하면서 의존관계를 연결해주는 것
  ### 9) 스프링으로 전환하기
  - `@Configuration`을 사용하여 AppConfig를 스프링 컨테이너의 설정 정보로 등록
  - `@Bean`을 사용하여 각 메서드를 스프링 컨테이너에 스프링 Bean으로 등록
  - 스프링 컨테이너란, ApplicationContext 를 말한다
  - 스프링 Bean은 `applicationContext.getBean()` 메서드를 통해 찾을 수 있다