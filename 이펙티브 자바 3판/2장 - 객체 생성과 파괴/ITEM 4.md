# ITEM 4. 인스턴스화를 막으려거든 private 생성자를 사용하라

<u>static 메서드와 static 필드를 모아둔 클래스를 만든 경우</u> 해당 클래스를 abstract로 만들어도 인스턴스를 만드는 걸 막을 순 없다. 상속 받아서 인스턴스를 만들 수 있기 때문에.

- Utility Class 만들때 abstract를 만들어서 방지할 수 있다.
- abstract를 쓰는 경우에는 noninstantiablility를 보장할 수 없다.

유틸리티 클래스처럼 인스턴스를 만들 필요가 없는 클래스의 경우에 인스턴스 생성을 하지 못하게 막는 방법이 몇가지 있다. 
  - 하나는 abstract 클래스를 사용하는 방법이고 
  - 다른 방법은 private 생성자를 추가하는 방법입니다.

그리고 아무런 생성자를 만들지 않은 경우 컴파일러가 기본적으로 아무 인자가 없는 pubilc 생성자를 만들어 주기 때문에 그런 경우에도 인스턴스를 만들 수 있다.

명시적으로 private 생성자를 추가해야 한다.

```java
// Noninstantiable utility class
public class UtilityClass {
    // Suppress default constructor for noninstantiability
    private UtilityClass() {
        throw new AssertionError();
    }
    // Remainder omitted
}
```

AssetionError는 꼭 필요하진 않지만, 그렇게 하면 의도치 않게 생성자를 호출한 경우에 에러를 발생시킬 수 있고, private 생성자기 때문에 상속도 막을 수 있다.

생성자를 제공하지만 쓸 수 없기 때문에 직관에 어긋나는 점이 있는데, 그 때문에 위에 코드처럼 주석을 추가하는 것이 좋다.

부가적으로 상속도 막을 수 있다. 상속한 경우에 명시적이든 암묵적이든 상위 클래스의 생성자를 호출해야 하는데, 이 클래스의 생성자가 private이라 호출이 막혔기 떄문에 상속을 할 수 없다.


```java
public class UtilClass { //utilclass를 만들면 
    public static String getName() { //static을 많이 만들잖아요 
        return "keesun"; 
    }

    //인스턴스를 만들 필요 없이 모두 static에 접근하니깐 사용하는 입장에서는 아래처럼 사용되길 원한다. 굳이 
    public static void main(String[] args) {
        UtilClass utilClass = new UtilClass(); //굳이 utilclass라는 인스턴스를 만들 필요가 없다. 
        UtilClass.getName();//이렇게 사용되길 바란다. 
    }
}
```

```java
public abstract class UtilClass { //인스턴스를 막기 위해  abstract를 붙이면 1차적으로 막을 수 있다.
    public static String getName() { 
        return "keesun"; 
    }
    
    //이때 UtilClass를 상속받게 될 경우 (static 메소드를 참조하고 있기 때문에 static을 붙여준다)
    static class AnotherClass extends UtilClass {
        
    }
    public static void main(String[] args) {
        AnotherClass
        UtilClass.getName();
    }
}
```