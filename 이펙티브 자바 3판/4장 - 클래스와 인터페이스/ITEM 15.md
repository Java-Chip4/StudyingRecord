# ITEM 15. 클래스와 멤버의 접근 권한을 최소화 하라


> 정보 은닉, 캡슐화는 소프트웨어 설계의 근간이 되는 원리이다.
## 정보 은닉의 장점
- 시스템 개발 속도를 높인다.
- 시스템 관리 비용을 낮춘다.
- 성능 최적화에 도움을 준다.
  - 다른 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화 가능
- 소프트웨어 재사용성을 높인다.
- 큰 시스템을 제작하는 난이도를 낮춘다.

## 정보 은닉의 원칙
- 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다. (가능한 가장 낮은 접근 수준 부여)
- 패키지 외부에서 쓸 이유가 없다면 package-private으로 선언 한다.
  - 그러면 API가 아닌 내부 구현이 되어 언제든 수정할 수 있다. (클라이언트에 아무런 피해 없이 수정, 교체, 제거가 가능하다)
- 한 클래스에서만 사용하는 package-private 톱레벨 클래스나 인터페이스는 이를 사용하는 클래스 안에 private static으로 중첩시킨다.
  - 이렇게 하면 바깥 클래스 하나에서만 접근할 수 있게 된다.
- 제일 중요한 것은 **public일 필요가 없는 클래스의 접근 수준을 package-private 톱레벨 클래스로 좁히는 일**이다
> 클래스의 공개 API를 세심하게 설계한 후 그 외의 모든 멤버는 private로 만들고 같은 패키지의 다른 클래스가 접근해야 하는 멤버에 한하여 package-private으로 만들자.  
> 권한을 풀어야 하는 일이 자주 발생한다면 컴포넌트를 더 분해해야 되는 것은 아닌지 고민해본다.

### 멤버 접근성을 좁히지 못하게 방해하는 제약
- 상위 클래스의 메서드를 재정의 할 때는 그 접근 수준을 상위 클래스에서보다 좁게 설정 할 수 없다.
- 이 제약은 상위 클래스의 인스턴스는 하위 클래스의 인스턴스로 대체해 사용할 수 있어야한다는 리스코프 치환 원칙을 지키키 위해 필요하다.
- 클래스가 인터페이스를 구현하는 할 때는 인터페이스가 정의한 모든 메서드를 public으로 선언해야 한다,

### 테스트의 목적으로 접근 범위를 넓히는 경우
- 테스트를 위해 접근 범위를 넓히는 것은 좋지 않다.
- 그러나 public 클래스의 private 멤버를 package-private으로 바꾸는 것까지는 허용한다. 그 이상은 허용하지 않는다.
- **테스트만을 위해 클래스, 인터페이스, 멤버를 공개 API로 만들면 안된다.**

### public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.
- public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.
- 필드가 가변 객체를 참조하거나, final이 아닌 인스턴스 필드를 public으로 선언하면 그 필드에 담을 수있는 값을 제한할 힘을 잃게 된다.
- 그러나 해당 클래스가 표현하는 추상 개념을 완성하는 데 꼭 필요한 구성요소로써의 상수라면 public static final 필드로 공개해도 된다.

### 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안된다.
- 배열 필드를 public으로 선언하면 클라이언트에서 그 배열의 내용을 수정할 수 있다.
- 이를 막으려면 배열을 private으로 선언하고 public 불변 리스트를 반환하는 방법이 있다.
```java
public static final Thing[] VALUES = {...};
```
위 코드는 보안이 취약한 코드 이므로 다음과 같이 수정해야 한다.
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final List<Thing> VALUES = 
        Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```
- 다른 방법은 배열을 private으로 만들고 복사본을 반환하는 public 메서드를 추가하는 것이다.
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] values() {
    return PRIVATE_VALUES.clone();
}
```

### 모듈 시스템 도입으로 추가된 암묵적 접근 수준
- 모듈은 패키지들의 묶음으로 자신에 속하는 패키지 중 공개(export) 할 것들을 선언한다. (관례상 module-info.java 파일에 선언)
- protected 혹은 public 멤버라도 해당 ㅠㅐ키지를 공개하지 않았다면 모듈 외부에서는 접근할 수 없다.
- 모듈 시스템을 활용하면 클래스를 외부에 공개하지 않으면서 같은 모듈을 이루는 패키지 사이에서는 자유롭게 공유할 수 있다.