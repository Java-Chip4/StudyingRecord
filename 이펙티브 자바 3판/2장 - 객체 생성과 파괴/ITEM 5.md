# ITEM 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

대부분의 클래스는 하나 이상의 자원에 의존하는데 이러한 자원들은 경우에 따라 교체가 될 수 있다.

## 정적 유틸리티를 잘못 사용한 예

```java
public class SpellChecker {

    private static final Lexicon dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
```

## 싱클톤을 잘못 사용한 예

```java
public class SpellChecker {

    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}

class Lexicon {}
```

- 테스트 코드를 작성할 때 다른 사전 자원을 테스트 하기 어려움 (내부 코드를 고쳐써야 하는 문제가 있음)
  ⇒ 유연성이 떨어짐

예를 들어 이커머스 서비스에서 할인 정책을 기획한다고 가정하자. 초창기에는 물품의 가격대들이 다양하지 않아서 고정된 금액을 할인하는 정책으로 물품 당 할인을 천원씩 하기로 하였다. 이 정책을 바탕으로 구매 로직을 구성하였다.

하지만 물품들의 가격대가 다양해지면서 5,000원 물품을 천원씩 할인해주는 것과 30,000원 물품을 천원씩 할인 해주는 것이 소비자들에게 매력적으로 다가오지 않을 것이라 판단한 기획부가 물품 가격의 20%을 할인 해주는 정책을 새로 기획한다고 가정하자.

이 경우 기존에 있던 구매 로직에 있던 할인 정책을 교체해야 되는 소요가 발생한다. 즉, 구매 클래스에서 자원을 교체해야 된다. 필드에서 final 한정자를 제거하고 다른 것으로 교체하는 메서드를 추가할 수 있지만 이 방식의 경우 오류를 내기 쉬우며 멀티 쓰레드 환경에서는 동기화 문제를 일으키기에 사용할 수 없다.

따라서 **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글톤 방식이 적합하지 않다.**

**대신 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식을 사용한다.**

## 의존 객체 주입

```java
public class SpellChecker {

    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}

class Lexicon {}
```

## 의존 객체 주입 장단점

### 장점

- 자원의 개수와 의존 관계가 어떻게 되어있든 상관없이 잘 작동한다.
- 블변을 보장하여 같은 자원을 사용하는 여러 클라이언트가 의존 객체를 안심하고 공유할 수 있다
    - 멀티 쓰레드 환경에서의 안정성 확보
- 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있다.

### 단점

- 의존성이 많아질 경우 코드의 복잡도가 증가한다.
    - Spring framework 등의 의존 객체 주입 프레임워크 등을 사용하여 해소 하도록 한다.

## Factory Method pattern

**Factory Method Pattern (팩토리 메소드 패턴)**은 생성 패턴 중 하나로 **객체를 생성할 때 어떤 클래스의 인스턴스를 만들 지 서브 클래스에서 결정하게 한다.**

자바 8에서 소개한 Supplier<T> 인터페이스가 팩토리를 표현한 예시이다.

Supplier<T>를 입력으로 받는 메서드는 일반적으로 한정적 와일드카드 타입을 사용해 팩터리의 타입 매개변수를 제한해야 한다.

이 방식을 사용해 클라이언트는 자신이 명시한 타입의 하위 타입으라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다.

다음은 클라이언트가 제공한 팩토리가 생성한 Tile들로 구성된  Mosaic를 만드는 메서드이다.

```java
Mosaic create(Supplier<> extends Tile> tileFactory) {...}
```