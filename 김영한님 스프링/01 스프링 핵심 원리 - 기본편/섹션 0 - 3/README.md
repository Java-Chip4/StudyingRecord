# 스프링 핵심 원리 - 기본편
## 해당 주차 리더 : @KimChanJin97
## 해당 주차 섹션
섹션 1. 객체 지향 설계와 스프링  
섹션 2. 스프링 핵심 원리 이해 1 - 예제 만들기  
섹션 3. 스프링 핵심 원리 이해 2 - 객체 지향 원리 적용


---


## Issue 1 : Spring boot 정적 리소스 및 타임리프 기본 path 설정
### https://github.com/Java-Chip4/StudyingRecord/issues/1
### 질문자 : @songks0922
### 답변자 : @songks0922
![image](https://user-images.githubusercontent.com/71240296/179935420-f8220c6c-d841-4e36-a982-96810b82b130.png)  

88, 42번째 라인에 경로 설정이 되어 있는데 @SpringBootApplication 이 어노테이션에 의해 메인 함수가 실행되면서 모든 컴포넌트를 스캔하면서 해당 설정들이 자동으로 주입되어 따로 설정하지 않아도 찾아갑니다.  


---

## Issue 2 : DI에 사용되는 어노테이션 @Autowired, @Resource, @Inject 차이점?
### https://github.com/Java-Chip4/StudyingRecord/issues/2
### 질문자 : @songks0922
`@Inject`는 자바에서 제공해주는 어노테이션이고 `@Autowired`는 스프링에서 제공해주는 것으로 알고 있는데 언제 한번 보기를 스프링에서 제공해주는 어노테이션을 사용하는것이 더 호환성이 좋다 정도로 알고 있어서 `@Autowired`를 쓴다라고 듣긴 했던 거 같은데 확실하지 않아서 알아보면 좋을 것 같습니다.

### 답변자 : @songks0922
### `@Resource`, `@Autowired`, `@Inject`의 차이점
`@Resource`, `@Inject`은 자바에서 지원해주는 어노테이션으로 특정 프레임워크에 종속적이지 않다는 특징을 가지고 있다.
둘의 차이점은 의존 객체를 찾는 방식에서 나타나는데, Bean을 찾기 위해 확인하는 순서가 다르다.
* `@Resource`는 이름 -> 타입 -> `@Qualifier` -> 실패의 순서로 찾게 되고 사용할 수 있는 위치가 멤버변수, settrer 메소드에 한정되어 있다.
*   `@Inject`는 타입 -> @Qualifier-> 이름 -> 실패의 순서로 찾게되고 멤버변수, setter 메소드, 생성자, 일반 메소드에 적용 가능하다.
* `@Autowired`는 Spring에서 지원하는 어노테이션으로 타입 -> 이름 -> @Qualifier -> 실패의 순서로 Bean을 조회한다. 사용할수 있는 위치는 멤버변수, setter메소드, 생성자, 일반 메소드이다.


### `@Qualifier`
동일한 타입을 가진 Bean 객체가 두 개 이상 존재하면 Spring이 어떤 Bean을 주입해야 할지 모르기 때문에 Exception이 발생하여 `@Qualifier` 어노테이션으로 사용할 의존 객체를 지정할 수 있다. `@Bean`과 함께 사용하거나 xml에 태그로 삽입하여 사용한다.

### 참고 링크
* `@Qualifier` https://mungto.tistory.com/458
*  `@Resource`, `@Autowired`, `@Inject`의 차이 https://velog.io/@sungmo738/Resource-Autowired-Inject-%EC%B0%A8%EC%9D%B4


--

## Issue 3 : AppConfig 파일에 적용된 GRASP 패턴
### https://github.com/Java-Chip4/StudyingRecord/issues/3
### 질문자 : @songks0922
creator 패턴이 적용되어 있는데 이것이 무엇인지 이로 인해 얻는 이점은 무엇인지 토의해보면 좋겠습니다.

### 답변자 1 : @KimChanJin97
### Creator pattern :  A객체를 포함하거나 기록하는 B객체가 A객체를 생성할 책임이 있다는 원칙
### A 객체와 B 객체의 관계의 관계가 다음 중 하나라면 A의 생성을 B의 책임으로 부여.
경우1. B 객체가 A 객체를 포함하고 있다.
경우2. B 객체가 A 객체의 정보를 기록하고 있다.
경우3. A 객체가 B 객체의 일부이다.
경우4. B 객체가 A 객체를 긴밀하게 사용하고 있다.
경우5. B 객체가 A 객체의 생성에 필요한 정보를 가지고 있다.

**// hello.core.member.MemberServiceImpl**
```
package hello.core.member;

public class MemberServiceImpl implements MemberService {
    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
      ... (실행)
}
```

**// hello.core.AppConfig**
```
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }
      ... (실행)
}
```

**hello.core.AppConfig 에서**
new 키워드로 MemoryMemberRepository() 생성자 호출, 객체 생성
new 키워드로 MemberServiceImpl() 생성자 호출, 객체 생성 + 앞서 생성했던 객체 주입
MemberServiceImpl() 생성자의 인자에 MemoryMemberRepository() 생성자를 주입한다는 것을
MemberServiceImpl() 객체가 MemoryMemberRepository() 객체를 포함한다고 볼 수 있다. 이는 creator 패턴의 경우1 에 해당한다고 볼 수 있다. (내 생각...)

### creator 패턴을 적용함으로써 얻는 이득을 구현체와 AppConfig로 구분해서 정리하자면
**hello.core.member.MemberServiceImpl 에서**
- 첫번째 이득. MemberServiceImpl는 의존관계에 대한 고민은 외부(AppConfig)에 맡기고 실행(...부분)에만 집중할 수 있음!
- 두번째 이득. OrderServiceImpl 클래스가 더 이상 인터페이스와 구현체 모두에게 의존하지 않고 인터페이스에만 의존할 수 있다. 즉, DIP을 지킬 수 있다는 것이다
- 세번째 이득. 만약 OrderServiceImpl 클래스가 인터페이스와 구현체 모두에게 의존하는 상황이었더라면 구현체 생성자를 변경해야 했을 경우 구현체 생성자를 변경해야 했을 것이다. 이는 OCP를 위반하는 상황이다. 그래서 creator 패턴을 적용한다면 다시말해, OrderServiceImpl 클래스가 인터페이스에만 의존한다면 OCP를 지킬 수 있다는 것이다.

**hello.core.AppConfig 에서**
- 첫번재 이득. MemberServiceImpl 생성자를 통해 어떤 구현체를 주입할지는 오직 외부(AppConfig)에서 결정! OCP를 지킬 수 있다.


### 답변자2 : @songks0922
김영한님이 역할과 책임의 분리를 강조하시는데 Creator 패턴을 적용해서 객체를 생성하는 책임을 해당 클래스에 할당한다고 보면 되겠습니다. General Responsibility Assignment Software Patterns(GRASP) 책임을 할당하는 소프트웨어 패턴


---


## Issue 4 : 객체 다이어그램과 클래스 다이어그램의 차이
### https://github.com/Java-Chip4/StudyingRecord/issues/4
### 질문자 : @songks0922
각각 다이어그램을 표현할 때 기호의 의미 정도는 알고 넘어갔으면 좋겠습니다.

### 답변자 : @KimChanJin97
#### 실선은 화살표로 가리키는 인터페이스 또는 구현(객)체를 참조한다는 의미이다.
#### 점선은 화살표로 가리키는 인터페이스로부터 상속받는다는 의미이다.

------------------------------------------------------------------------------

**도메인 협력 관계**
- 우선 '도메인'이란, 프로그래밍으로 해결하고자 하는 주제에 대한 영역과 핵심 비즈니스 요구사항을 의미
- 도메인 협력 관계: 도메인 협력 관계는 기획자들도 볼 수 있는 그림 정도
- 핵심 비즈니스 요구사항은 필드( ex) member, order )가 될 수도 있고 메서드( ex) setMeber(), getMember(), setOrder(), getOrder() )가 될 수도 있다?
- 도메인과 리포지토리를 헷갈리지 말 것
- 도메인은 DB에 저장될 객체를 선언하고 그 객체를 활용해 사용할 수 있는 필드와 메서드를 선언하는 역할
- 리포지토리는 객체(도메인 객체)을 DB에 저장, 관리, 접근하는 역할 ( ex) findById(), findByName() )



**클래스 다이어그램**
- 클래스 다이어그램: 실제 서버를 실행하지 않은 채로 클래스들의 관계(구현과 의존)를 파악하는 그림
- 클래스 다이어그램에서 구현체가 구현체가 아닌 인터페이스를 참조하는 이유는 강의에서 배웠듯이 "디카프리오(구현체)가 로미오 배역(인터페이스)을 소화함과 동시에 핫세(구현체)에게 줄리엣 배역(인터페이스)을 지시하는 것은 옳지 않다! 핫세(구현체)에게 줄리엣 배역(인터페이스)을 지시하는 건 공연 기획자(AppConfig)가 할 일이다!" 라는 비유를 통해 알 수 있음



**객체 다이어그램**
- 객체 다이어그램: 실제 서버가 실행될 때 생성되는 객체들의 관계를 파악하는 그림
- 클래스 다이어그램은 클래스들의 관계(상속 및 구현, 의존)을 파악하는 반면
- 객체 다이어그램은 실제 서버에서 어떤 객체들을 사용하는지, 어떤 객체들이 서로 의존하는지를 파악함
- 객체 다이어그램에는 객체만이 요소로 들어갈 수 있는데, 이는 인터페이스가 아닌 구현체만 들어갈 수 있음을 의미한다. 인터페이스는 추상이기 때문에 객체를 생성할 수 없기 때문이다.
- 다른 말로 표현하자면 객체 다이어그램에서 점선은 그릴 수 없고 실선만 그릴 수 있다.


---


## Issue 5 : 제어의 역전 IoC(Inversion of Control)
### https://github.com/Java-Chip4/StudyingRecord/issues/5
### 질문자 : @KimChanJin97
### 답변자 : @KimChanJin97
강의자료에서 살짝 이해가 안되는 부분을 제 식대로 고쳐봤습니다.
3. 스프링 핵심 원리 이해2 - 객체 지향 원리 적용.pdf p.22 제어의 역전 부분입니다

### 제어의 역전 IoC(Inversion of Control)
기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행했다.
한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다. 개발자 입장에서는 자연스러운 흐름이다.

반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다.
프로그램의 제어 흐름은 이제 AppConfig가 가져간다.
예를 들어서 OrderServiceImpl 은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.
프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다.
심지어 OrderServiceImpl 객체도 AppConfig가 생성한다.

**// hello.core.order.OrderServiceImpl**
**// 옳지 않은 코딩, 제어의 역전이 이루어지지 않았던 코딩 (수정 전)**
```
    public class OrderServiceImpl implements OrderService {
        private final MemberRepository memberRepository = new MemoryMemberRepository();
        private final DiscountPolicy discountPolicy = new RateDiscountPolicy;
        ...
        }
    }
```
------------------------------------------------------------------------------------------------------
**// hello.core.order.OrderServiceImpl**
**// 옳은 코딩, 제어의 역전이 이루어진 코딩 (수정 후)**
```
    public class OrderServiceImpl implements OrderService {
        private final MemberRepository memberRepository;
        private final DiscountPolicy discountPolicy;

        public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
            this.memberRepository = memberRepository;
            this.discountPolicy = discountPolicy;
        }
        ... (실행)
    }
```

**// hello.core.AppConfig**
**// 옳은 코딩, 제어의 역전이 이루어진 코딩 (수정 후)**
```
    ...
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }
    ...
```

그리고 AppConfig는 OrderServiceImpl 이 아닌 OrderService 인터페이스의 다른 구현 객체( ex) OrderServiceImpl123)를 생성하고 실행할 수 도 있다. (물론 OrderServiceImpl 처럼 OrderService 인터페이스를 상속받고, 생성자를 만들고, 자신만의 로직을 만드는 과정은 동일하게 진행해야 한다!)
그런 사실도 모른 채 OrderServiceImpl 은 묵묵히 자신의 로직을 실행할 뿐이다.

**// hello.core.order.OrderServiceImpl**
**// 옳은 코딩, 제어의 역전이 이루어진 코딩(수정 후)**
**// OrderServiceImpl 은 자신에게 필요한 객체를 생성하지 않고 자신의 로직이 실행되기만을 기다리고 있다.**
**// 위에서 두번째 단락에 나온 코드랑 동일함**
```
    public class OrderServiceImpl implements OrderService {
        private final MemberRepository memberRepository;
        private final DiscountPolicy discountPolicy;

        public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
            this.memberRepository = memberRepository;
            this.discountPolicy = discountPolicy;
        }
        ... (실행)
    }
```

이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.
