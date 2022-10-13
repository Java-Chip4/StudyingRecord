# Chap 10. 날짜와 시간 & 형식화

# Chap 12. 지네릭스, 열거형, 애너테이션
## Generics
제네릭은 기존의 파라미터처럼 타입(클래스나 인터페이스)을 파라미터로 사용할 수 있도록 허용해주는 문법을 뜻한다.  
이를 사용하여 얻을 수 있는 장점은 아래와 같다.

- 컴파일 타임에 더 강한 타입 안정성을 제공한다
    - 타입 안정성이란, 의도하지 않은 타입의 객체가 저장되는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 잘못 형변환 되는 오류를 막아주는 것이다.
    - 컴파일러가 타입 추론을 통해 타입 에러를 사전에 잡아내어 컴파일 에러를 내준다.
    - 가장 좋은 오류는 컴파일 에러이다. 고치기 쉽고, 사용자에게 노출되지 않기 때문이다.
- 타입 체크와 형 변환을 생략할 수 있어 코드가 간결해진다
    - 제네릭을 사용하지 않는다면, Object로 받아와서 해당 객체를 다시 알맞은 타입으로 형 변환이 이뤄져야 한다.
- 프로그래머가 제네릭한 알고리즘을 구현할 수 있도록 한다
    - 제네릭한 코드는 타입 안정성이 높고, 읽기에도 쉽다.

---  

## Generics 용어 정리
Oracle 공식 문서를 보기 위해서, 더 잘 알고 쓰기 위해서는 제네릭 영어 용어에 대해 정리하고 넘어가야 한다.
아래의 Box 제네릭 클래스 코드를 기반으로 알아보자.
```
// 제네릭 클래스 정의
class Box<T> { ... }

// 제네릭 클래스 생성
Box<String> stringBox;
```
- Generic class (or interface)
    - `Box<T>` ; T box 라고 읽는다
- Raw type
    - `Box`
- type parameter
    - `T`
- type argument (= parameterized type)
    - `String`

---

## raw type의 사용
하위 호환성을 위해 자바는 parameterized type을 raw type에 넣는 것을 허용한다. 그러나 몇 가지 문제점이 존재한다. 따라서 아래 코드 예시를 통해 알아보자
- parameterized type을 raw type에 대입한 경우
  ```
  Box<String> stringBox = new Box<>();
  Box rawBox = stringBox;  // OK
  ```
- raw type을 parameterized type에 대입한 경우
  ```
  Box rawBox = new Box();           // rawBox is a raw type of Box<T>
  Box<Integer> intBox = rawBox;     // warning: unchecked conversion
  ```
- raw type이 제네릭 메서드를 호출한 경우
  ```
  Box<String> stringBox = new Box<>();
  Box rawBox = stringBox;
  rawBox.set(8);  // warning: unchecked invocation to set(T)
  ```
이러한 경고는 컴파일 타임이 아닌 런타임으로 지연시키기 때문에 특별한 경우가 아니라면, raw type을 사용하는 것을 지양해야 한다.

---

## Diamond 연산자
제네릭 클래스를 생성할 때, parameterized type을 적지 않고, 다이아몬드 연산자(<>)를 사용하여 간단히 생성할 수도 있다. 다이아몬드 연산자를 쓰는 것과 쓰지 않는 것에는 차이가 있을까?

A : `List<String> list = new LinkedList();`  
B : `List<String> list = new LinkedList<>();`

A의 경우, 다이아몬드 연산자를 사용하지 않았다. 이렇게 되면 raw type의 LinkedList를 사용하게 된다. 즉, 제네릭의 타입 안정성 장점을 누리지 못하는 것과 같다. 아래 코드를 보자.
```
 List<String> strings = ... // 몇 개의 문자열을 담은 몇 개의 리스트가 있다고 해보자

 // 아래 코드에서는 컴파일 에러를 만날 수 없다. raw type의 LinkedList를 사용했기 때문에 타입 안정성을 보장하지 않기 때문이다.
 List<Integer> integers = new LinkedList(strings);
 ```
위 코드는 컴파일 에러는 발생하지 않겠지만, 런타임 에러가 발생한다. 자바는 이런 코드를 왜 허용한 것일까? 바로 하위 호환성 때문이다. 제네릭이 처음부터 존재했던 문법이 아니기에 이전의 코드 호환성을 위해 허용된 문법이다.

그에 반해, B는 디이아몬드 연산자를 사용했다. 이로 인해 제네릭의 타입 안정성을 그대로 얻을 수 있다.
사실 B는 `List<String> list = new LinkedList<Integer>();` 와 똑같은 코드이다. 그러나 Integer를 지우고 `<>`로 사용하는 이유는 컴파일러가 타입 추론을 통해 유연하게 제네릭을 통제할 수 있도록 하기 위함이다. 또한, DRY(Don't Repeat Yourself : 반복하지 마라) 원칙에 따라 같은 말을 또 반복할 필요는 없기 때문이기도 하다.  
https://stackoverflow.com/questions/4166966/what-is-the-point-of-the-diamond-operator-in-java

---

## Generic method
제네릭 메서드란, 제네릭을 사용한 메서드로, 제네릭 클래스 안에 존재할 수도 있고 일반 클래스 안에 존재할 수도 있다. 메서드가 static하거나 non-static해도 사용할 수 있으며, 심지어 제네릭 클래스 생성자도 만들 수 있다.

아래는 일반 클래스 내에 제네릭 메서드를 구현한 예시이다. 2번째 줄에서 특이하게 static 과 boolean 사이에 <K, V>가 들어가 있는 것을 확인할 수 있다.
이는 해당 메서드에서 <K, V>를 독자적으로 사용하겠다고 미리 알려주는 것인데, 제네릭 클래스 안에서의 제네릭 메서드 예시를 보면 이해가 더 쉽다.
```
public class Util {
  public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
    return p1.getKey().equals(p2.getKey()) &&
           p1.getValue().equals(p2.getValue());
  }
}
```

다음은 제네릭 클래스 안에서의 제네릭 메서드를 구현한 예시이다. Pair 클래스는 <K, V> type parameter를 사용하고 있다. 그런데 compare() 메서드 역시 <K, V> type parameter를 사용 중이다. 그럼 이 둘은 서로 같다고 보면 될까?

```
@Getter @Setter
public class Pair<K, V> {
  private K key;
  private V value;

  public Pair(K key, V value) {
      this.key = key;
      this.value = value;
  }

  public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
    return p1.getKey().equals(p2.getKey()) &&
           p1.getValue().equals(p2.getValue());
  }
}
```
아니다. 우선, 메서드에 선언된 제네릭 type parameter는 해당 메서드 내에서만 지역적으로 사용되므로, Pair 클래스의 <K, V>와 다르다. 또한, 이와 같이 클래스와 메서드의 type parameter가 같다면, 메서드의 type parameter를 우선시하여 따라간다.

---
## Bounded Type Parameter
제네릭에서 제한된 type argument만 사용하고 싶을 수도 있다. 예를 들어, 숫자를 다루는 한 메서드에 Number 객체와 그 하위 클래스의 객체만 들어갈 수 있도록 하는 것이다. 이를 앞으로 bounded type parameter라고 부르자.

bounded type parameter를 사용하기 위해서는 `extends` 키워드를 사용하면 된다. 이는 클래스의 상한을 정하는 것이다. 여기서 기억해야 할 점은 인터페이스를 구현한 타입을 제한하고 싶을 때에도 `implements`가 아닌 `extends`를 사용해야 한다는 점이다.

아래 코드를 통해 사용 방법을 살펴보자.
```
public <U extends Number> void inspect(U u){
  System.out.println("T: " + t.getClass().getName());
  System.out.println("U: " + u.getClass().getName());
}
```
다음은 한 가지 제한이 아닌 여러 제한을 둔 경우의 코드이다.
```
<T extends B1 & B2 & B3>
```
이와 같이 `&`를 사용하여 multiple bounds 설정이 가능하다.


---
## Generics 변성
https://www.youtube.com/watch?v=PtM44sO-A6g

---
# Annotation
[Github issue : python decorator 와 java annotation의 차이](https://github.com/Java-Chip4/StudyingRecord/issues/7) 에서 다룬 내용으로 대체하였다.
