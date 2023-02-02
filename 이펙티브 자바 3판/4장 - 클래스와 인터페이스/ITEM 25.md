# ITEM 25. 톱레벨 클래스는 한 파일에 하나만 담으라

```aidl
예기치 못한 상황을 방지 하기 위해 클래스를 분리하여 소스 파일을 여러 개로 나누자. 
```
한 파일에 여러개의 톱레벨 클래스를 담게 되면 컴파일 되는 순서에 따라서 예기치 못한 상황이 발생할 수 있다. 이러한 문제는 소스 파일을 분리하거나 정적 멤버 클래스를 사용하는 것으로 해결할 수 있다.

예를 들어 다음과 같은 Main 클래스와 Utensil, Dessert 클래스가 있다고 하자. 컴파일이 될 수 있는 경우는 다음과 같다.
###1. javac Main.java Dessert.java
이 경우 Main.java를 컴파일 하면서 제일 먼저 나오는 Utensil를 참조하여 Utensil.java를 컴파일하여 Uetensil과 Dessert를 모두 찾아낸다. 그리고 두번째 인자로 넘어온 Dessert.java를 컴파일 할 때 중복된 클래스로 인해 오류가 날 것이다.
**즉, 컴파일 오류로 오류를 검출할 수 있다.**
###2. javac Main.java or javac Main.java Utensil.java
**컴파일 오류 없이** pancake를 출력한다.
###3. javac Dessert.java Main.java
**컴파일 오류 없이** potpie를 출력한다.

- Main.java
```java
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
```

- Utensil.java
```java
public class Utensil {
    static final String NAME = "pan";
}

public class Dessert {
    static final String NAME = "cake";
}
```

- Dessert.java
```java
public class Utensil {
    static final String NAME = "pot";
}

public class Dessert {
    static final String NAME = "pie";
}
```

## 해결 방안
```java
톱레벨 클래스들을 정적 멤버 클래스로 바꾼다.
```

- Test.java
```java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }

    private static class Utensil {
        static final String NAME = "pan";
    }

    private static class Dessert {
        static final String NAME = "cake";
    }
}
```