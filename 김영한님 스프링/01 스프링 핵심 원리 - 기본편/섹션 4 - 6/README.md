# 스프링 핵심 원리 - 기본편
## 해당 주차 리더 : @anthologia
## 해당 주차 섹션
섹션 4. 스프링 컨테이너와 스프링 빈  
섹션 5. 싱글톤 컨테이너  
섹션 6. 컴포넌트 스캔  


---


## Issue 6 : 클래스, 추상 클래스, 인터페이스 : 인터페이스만 다중 상속이 가능한 이유?
### https://github.com/Java-Chip4/StudyingRecord/issues/6
### 질문자 : @charlesuu
> 스프링 핵심 원리 기본편 : 강의 6. 좋은 객체 지향 프로그래밍이란? [10 : 31]

질문 : 인터페이스만 다중 상속이 가능한 이유에 대해 논의해보고 싶습니다.

다형성은 `인터페이스 - 구현` 관계가 아닌 `클래스 - 상속 클래스`  `추상 클래스 - 상속 클래스` 관계에서도 동작합니다.
그러나 다형성을 이용하려 할 때 일반적으로 클래스보다는 인터페이스를 부모로 두는 것을 선호합니다.
그 이유 중 하나는 인터페이스가 부모로 있을 때만 다중 상속이 가능하기 때문인데요.
자바에서는 왜 인터페이스만 다중 상속이 허용되는지, 클래스에 대한 다중 상속은 금지시킨 이유가 무엇일지에 대해 논의해 보고 싶습니다.(참고로 C++에서는 클래스 다중 상속해도 컴파일 오류X)

### 답변자 1 : @KimChanJin97
### 1. 인터페이스만 다중상속이 가능한 이유
인터페이스는 멤버가 선언만 되어 있을 뿐 구현이 되어있지 않기 때문입니다. 인터페이스에는 애초에 멤버가 구현이 안되어있기 때문에 아무리 여러 인터페이스들에 각각 같은 이름의 멤버들이 있고, 그 여러 인터페이스들을 하나의 클래스가 다중상속받더라도 그 클래스에서 멤버를 오버라이딩(구현)하면 아무 문제가 없습니다.
![1](https://user-images.githubusercontent.com/102044895/181018348-f9399410-8248-4d0e-bee5-b9f86bc23935.png)  
( **Q.질문** 그렇다면 여러 인터페이스들로부터 상속받은 하나의 클래스가 같은 이름의 메서드들을 오버라이딩할 때 클래스는 어떤 인터페이스로부터 상속받은 메서드인지 어떻게 알 수 있을까? JVM이 알아서 판단하니 개발자가 알 필요가 없는 것일까?)

반면, 클래스는 선언과 구현이 모두 되어있는 구현체입니다.
따라서 클래스의 다중상속을 허용할 경우 **다이아몬드 문제**가 발생하게 됩니다.
예를 들어, 그 서로 다른 두 개의 부모 클래스에 각각 동일한 이름을 가진 메서드가 존재하고 하나의 자식 클래스가 두 개의 부모 클래스로부터 상속받는다고 가정한다면
자식 클래스는 어떤 부모로부터 메서드를 상속받아야할지 결정하지 못합니다.

### 2. 왜 자바에서는 클래스 다중상속이 불가하고, C++에서는 클래스 다중상속이 가능한가?
JAVA는 C++이 가지고 있던 단점인 낮은 안정성과 낮은 확장성을 보완한 언어입니다.
JAVA는 C++를 기반으로 개발되었지만 C++에서 혼란을 일으키는 메모리 관리제어, 포인터, 전처리기, 다중상속 기능 등을 없애버렸기 때문입니다.


### 답변자 2 : @KimChanJin97
### 수정사항
인터페이스는 변수 필드는 포함하지 않습니다.
추상클래스가 필드와 (추상 또는 일반)메서드를 포함합니다.

### 1. 인터페이스만 다중상속이 가능한 이유
인터페이스는 (멤버X -> 메서드O)가 선언만 되어 있을 뿐 구현이 되어있지 않기 때문입니다. 인터페이스에는 애초에 (멤버X -> 메서드O)가 구현이 안되어있기 때문에 아무리 여러 인터페이스들에 각각 같은 이름의 (멤버X -> 메서드O)들이 있고, 그 여러 인터페이스들을 하나의 클래스가 다중상속받더라도 그 클래스에서  (멤버X -> 메서드O)를 오버라이딩(구현)하면 아무 문제가 없습니다.


### 답변자 3 : @anthologia
### 추가사항
> ( **Q.질문** 그렇다면 여러 인터페이스들로부터 상속받은 하나의 클래스가 같은 이름의 메서드들을 오버라이딩할 때 클래스는 어떤 인터페이스로부터 상속받은 메서드인지 어떻게 알 수 있을까? JVM이 알아서 판단하니 개발자가 알 필요가 없는 것일까?)

JVM도, 개발자도 어떤 인터페이스의 메서드를 구현한 것인지 알 필요가 없습니다. 그래서 구분할 수도 없습니다. 인터페이스는 행동만을 표현하고, 구현체를 제공하지 않기 때문입니다. 해당 행동을 구현한 클래스의 메서드 구현체에 집중이 되어야 하는 것이지, 그 행동이 어디로부터 왔는지는 중요하지 않습니다. 그 덕분에 두 인터페이스를 구현한 클래스에서는 해당 메서드에 대해 **다이아몬드 문제**가 발생하지 않게 되는 것이죠.

그렇다면 이런 질문이 생길 수 있습니다. 만약, 해당 클래스에서 각각의 인터페이스를 타입으로 두고 해당 메서드를 호출하고 싶은 상황에서는 어떻게 해야 하나구요.

```
public class InterfaceTest {
    interface Gift  { void present(); }
    interface Guest { void present(); }

    interface Presentable extends Gift, Guest { }

    public static void main(String[] args) {
        Presentable johnny = new Presentable() {
            @Override public void present() {
                System.out.println("Heeeereee's Johnny!!!");
            }
        };
        johnny.present();                     // "Heeeereee's Johnny!!!"

        ((Gift) johnny).present();            // "Heeeereee's Johnny!!!"
        ((Guest) johnny).present();           // "Heeeereee's Johnny!!!"

        Gift johnnyAsGift = (Gift) johnny;
        johnnyAsGift.present();               // "Heeeereee's Johnny!!!"

        Guest johnnyAsGuest = (Guest) johnny;
        johnnyAsGuest.present();              // "Heeeereee's Johnny!!!"
    }
}
```

위의 코드를 보면, Gift와 Guest를 타입으로 두어 각각 호출하는 것 역시 가능합니다. 그 이유는 두 인터페이스의 present() 메서드가 동일한 method signature를 가졌기 때문이죠. 그래서 둘은 **@Override-equivalent** 하기 때문에 문제가 없습니다.
(https://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.4.8.4)

* method signature : https://wanna-b.tistory.com/75

그럼, 서로 다른 두 인터페이스에서 return type만 다르고 똑같은 이름의 메서드를 하나의 클래스에서 구현하고자 한다면 무슨 일이 벌어질까요?

```
public class InterfaceTest {
    interface Gift  { void present(); }
    interface Guest { boolean present(); }

    interface Presentable extends Gift, Guest { } // DOES NOT COMPILE!!!
    // "types InterfaceTest.Guest and InterfaceTest.Gift are incompatible;
    //  both define present(), but with unrelated return types"
}
```

이 경우에는 서로 다른 method signature를 가졌기 때문에 **@Override-equivalen 하지 않아서** 문제가 생깁니다. 컴파일 타임시 에러가 발생하게 되죠. 다만, 이건 복잡하게 생각하지 않아도 됩니다. 인터페이스를 통하지 않아도, 원래 return type이 다르고, 이름이 같은 메서드를 구현해보면 컴파일 오류가 뜨니까요.

(https://stackoverflow.com/questions/19959371/calling-same-method-name-from-two-different-interface-java)


### 답변자 4 : @anthologia
### 인터페이스에서 다이아몬드 문제가 발생하는 경우 - 1
그러나 Java 8 이후로는 인터페이스에서도 다이아몬드 문제가 발생하기 시작했습니다. 그 이유는 default method 때문이에요.

#### Default method란?
default 메서드는 기존에 존재하던 인터페이스에 새로운 기능을 추가함과 동시에 하위 호환성 유지를 위해 만들어졌습니다. default 메서드 설명을 위해 한 가지 상황을 예시로 들어볼게요.

우리에게는 TimeClient 인터페이스가 있습니다.

```
public interface TimeClient {
    void setTime(int hour, int minute, int second);
    void setDate(int day, int month, int year);
    void setDateAndTime(int day, int month, int year,
                               int hour, int minute, int second);
    LocalDateTime getLocalDateTime();
}

```

그리고 이를 구현하는 SimpleTimeClient 클래스가 있었습니다.

```
public class SimpleTimeClient implements TimeClient {
    public void setTime(int hour, int minute, int second) {
        LocalDate currentDate = LocalDate.from(dateAndTime);
        LocalTime timeToSet = LocalTime.of(hour, minute, second);
        dateAndTime = LocalDateTime.of(currentDate, timeToSet);
    }
    
    public void setDate(int day, int month, int year) {
        LocalDate dateToSet = LocalDate.of(day, month, year);
        LocalTime currentTime = LocalTime.from(dateAndTime);
        dateAndTime = LocalDateTime.of(dateToSet, currentTime);
    }
    
    public void setDateAndTime(int day, int month, int year,
                               int hour, int minute, int second) {
        LocalDate dateToSet = LocalDate.of(day, month, year);
        LocalTime timeToSet = LocalTime.of(hour, minute, second); 
        dateAndTime = LocalDateTime.of(dateToSet, timeToSet);
    }
    
    public LocalDateTime getLocalDateTime() {
        return dateAndTime;
    }
}
```

어느 날, 우리는 TimeClient 인터페이스에 한 가지 기능을 추가할 필요성을 느꼈습니다. 그래서 getZonedDateTime() 라는 메서드를 추가하기로 했습니다.

```
public interface TimeClient {
    void setTime(int hour, int minute, int second);
    void setDate(int day, int month, int year);
    void setDateAndTime(int day, int month, int year,
        int hour, int minute, int second);
    LocalDateTime getLocalDateTime();                           
    ZonedDateTime getZonedDateTime(String zoneString); // 새롭게 추가하고 싶은 기능
}
```

그러나 위와 같은 방식을 사용한다면, 우리는 SimpleTimeClient 클래스를 수정하고, 그 안에서 getZonedDateTime() 메서드를 구현해야 합니다.

그러나 아래와 같이 default 메서드로 직접 구현을 한다면, 우리는 SimpleTimeClient 뿐만이 아니라, TimeClient를 구현하는 모든 클래스를 수정할 필요가 없습니다. 그저 단순히 가져와서 호출하기만 하면 되기 때문이죠.

```
public interface TimeClient {
    void setTime(int hour, int minute, int second);
    void setDate(int day, int month, int year);
    void setDateAndTime(int day, int month, int year,
                               int hour, int minute, int second);
    LocalDateTime getLocalDateTime();
    
    static ZoneId getZoneId (String zoneString) {
        try {
            return ZoneId.of(zoneString);
        } catch (DateTimeException e) {
            System.err.println("Invalid time zone: " + zoneString + "; using default time zone instead.");
            return ZoneId.systemDefault();
        }
    }
        
    default ZonedDateTime getZonedDateTime(String zoneString) { // 새롭게 추가한 default 메서드
        return ZonedDateTime.of(getLocalDateTime(), getZoneId(zoneString));
    }
}
```

(https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)


### 딥변자 5 : @anthologia
### 인터페이스에서 다이아몬드 문제가 발생하는 경우 - 2
그런데 만약, 같은 이름의 default 메서드를 가진 여러 개의 인터페이스를 하나의 클래스에서 구현하고자 한다면 무슨 일이 벌어질까요?

```
public interface I1 {
    default String getGreeting() {
        return "Good Morning!";
    }
}

public interface I2 {
    default String getGreeting() {
        return "Good Afternoon!";
    }
}


public class C1 implements I1, I2 {
    public static void main(String[] args) {
        System.out.println(new C1().getGreeting());
    }
}
```

이를 실행하면, 컴파일 에러가 발생합니다. 그 이유는 둘 중 어느 인터페이스의 getGreeting()를 호출해야 할지 모호해서 알 수 없기 때문입니다.


#### 클래스의 다중상속 불가
이는 Java가 클래스의 다중상속을 막은 이유와 일맥상통합니다. 다이아몬드 문제가 발생하기 때문입니다. 더군다나 이를 무시하고 클래스의 다중상속을 문법적으로 허용할 경우, Java의 핵심인 객체지향 SOLID 원칙이 무너지게 됩니다.

- Ex 1) SOLID에서의 S는 Single Responsibility Principle(SRP)로 단일 책임 원칙을 뜻합니다. 클래스는 오직 하나의 기능을 갖고, 그 책임에만 집중해야 합니다.
- Ex 2) 또한, L : Liskov Subsititution Principle(LSP), 리스코프 치환 원칙에 의해 하위 클래스는 상위 클래스의 메서드를 모두 사용할 수 있어야 한다.

(https://gyubgyub.tistory.com/102)


#### 인터페이스의 다이아몬드 문제 해결
다시 인터페이스로 돌아와서, 그럼 어떻게 하면 모호함을 풀어줄 수 있을까요? 바로 구현 클래스에서 해당 메서드를 **@Override** 함으로써 문제를 해결할 수 있습니다.

```
public class C1 implements I1, I2 {
    public static void main(String[] args) {
        System.out.println(new C1().getGreeting());
    }

    @Override
    public String getGreeting() {
        return "Good Evening!";
    }
}

```

위와 같은 경우에는 최우선적으로 클래스 내에 오버라이딩된 메서드를 호출하기 때문입니다.

그 외에도 아래와 같이 중복되는 메서드에 인터페이스를 명시적으로 위임함으로써 문제를 해결할 수 있습니다.

```
public class C1 implements I1, I2 {
    public static void main(String[] args) {
        System.out.println(new C1().getGreeting());
    }

    @Override
    public String getGreeting() {
        return I1.super.getGreeting();
    }
}

```

(https://webfirewood.tistory.com/130)
(https://stackoverflow.com/questions/22685930/implementing-two-interfaces-with-two-default-methods-of-the-same-signature-in-ja)


---


## Issue 7 : python decorator 와 java annotation의 차이
### https://github.com/Java-Chip4/StudyingRecord/issues/7
### 질문자 : @songks0922
### 답변자 : @anthologia
### Python decorator와 Java Annotation의 차이
Python decorator와 Java annotation의 차이는 두 언어를 동시에 다루고 있는 사람이라면, 한 번쯤은 고민해볼만한 내용입니다.

___

#### Python decorator

먼저, 데코레이터 디자인 패턴에 대해 짧게 다루고 싶습니다.

> 데코레이터 패턴은 wrapper 객체 내에 새로운 객체를 배치하여 구현을 변경하지 않고도 객체에 새로운 동작을 동적으로 추가할 수 있도록 합니다.

즉, 기존 코드를 변경하지 않고도, 새로운 행동을 할 수 있도록 덧붙여주는 것입니다.
(https://gdtbgl93.tistory.com/9)

이 데코레이터 패턴은 여러 곳에서 상당히 유용하게 사용되고 있습니다.
그래서 Python에서는 이를 문법적으로 지원하기 위해 언어적 차원에서 decorator를 만들었습니다.

Python docs에서는 decorator를 다음과 같이 설명합니다.

> decorator는 한 함수를 @wrapper 문법을 통해 다른 함수로 반환하며, 일반적으로 classmethod()와 staticmethod()에 사용됩니다.
> 이는 그저 단순히 문법적으로 지원하는 편리성일뿐, 두 함수의 정의는 의미으로 동등합니다.

```
def f(arg):
...
f = staticmethod(f)

@staticmethod
def f(arg):
...
```

(https://docs.python.org/3/glossary.html)  


___


#### Java Annotation

Oracle Java 문서에서는 Annotation에 대해 다음과 같이 정의합니다.

> 메타데이터의 한 형태인 어노테이션은 프로그램에 대한 데이터를 제공합니다. 어노테이션은 자신이 @Annotation을 단 코드의 작동에 직접적인 영향을 미치지 않습니다.

(https://docs.oracle.com/javase/tutorial/java/annotations/index.html)

실제로 우리가 Java에서 자주 사용하는 어노테이션으로는 무엇이 있을까요?
@Override 가 있습니다. 이는 컴파일러에게 메서드에 대한 정보를 전달해주는 사례입니다.

다음으로는, 스프링 프레임워크에서 사용되는 어노테이션의 사례를 보겠습니다.
```
@RequestMapping(
    value = "/hello", method = GET)
@ResponseBody
public String getHelloMessage() {
    return "hello";
}
```
이는 클라이언트가 GET method로 /hello에 접근하는 것을 잡아내는 역할을 합니다.

그런데 Java 어노테이션의 정의에서는 메타 데이터를 전달하기만 할 뿐, 코드의 작동에 영향을 미치지 않는다고 했는데요.
@RequestMapping은 코드의 작동에 영향을 미치는 것으로 보입니다.

그 외에도, 스프링에서 AOP를 구현할 때, 어노테이션을 활용하는데, 이때 역시 코드의 작동에 영향을 미칩니다.

이게 무슨 일일까요?

사실, Oracle Java 문서에서는 어노테이션이 다음과 같이 사용될 수 있다고 했습니다.

> - 컴파일러에게 정보 전달에 사용 : 어노테이션은 컴파일러에게 오류 검출 혹은 경고 무시에 대한 지시를 내릴 때 사용될 수 있습니다.
> - 컴파일 타임과 개발 과정에 사용 : 소트트웨어 툴은 어노테이션 정보를 사용하여 코드, XML 파일 등을 생성할 수 있습니다.
> - 런타임에 사용 : 몇몇 어노테이션은 런타임시 활용될 수 있습니다.

스프링 프레임워크에서는 Java 어노테이션을 활용하여 여러 기능 코드를 추가하여 활용하고 있습니다.
우리 역시 커스텀 어노테이션을 작성하여 사용할 수도 있죠.
* 커스텀 어노테이션 : (https://mangkyu.tistory.com/130)

즉, Java 어노테이션을 활용하여 Python decorator와 같이 사용할 수는 있지만, Java 어노테이션 그 자체만으로는 불가능하다는 것입니다.

(https://medium.com/@dmytro.de/pythons-decorators-vs-java-s-annotations-same-thing-2b1ef12e4dc5)
(https://stackoverflow.com/questions/15347136/is-a-python-decorator-the-same-as-java-annotation-or-java-with-aspects)  


---


## Issue 8 : static 멤버로만 구성된 클래스 VS. 싱글톤 클래스
### https://github.com/Java-Chip4/StudyingRecord/issues/8
### 질문자 : @charlesuu
> 스프링 핵심 원리 기본편 : 싱글톤 패턴[00 : 31]

질문 :
1. `static 멤버로만 구성된 클래스`와 `싱글톤 클래스` 간에 어떤 차이점이 있나요?,
2. 둘 중에 싱글톤을 더 많이 쓰는 이유가 뭘까요?


강의를 통해 싱글톤 패턴이 어떤 상황에서 쓰이는지 배웠습니다.
요청이 올 때마다 새로운 객체를 만드는 대신, 이미 존재하는 하나의 객체가 모든 요청을 처리하게 했습니다.
이를 통해 불필요한 메모리 낭비를 없앴죠.

싱글턴으로 구현하는 대신, 모든 멤버 변수 및 메서드가 static인 클래스를 만들었어도 똑같은 장점을 누릴 수 있을거라는 생각이 들었습니다. 굳이 객체를 만들어서 동작하게 하지 않고, 클래스 멤버를 통해서 동작하게 하는거죠.

강의 속 예제에 한정하면 두 방식 사이에 아무런 차이가 없을 것 같습니다. 실제로는 두 방식 간에 어떤 차이점이 있나요? 싱글톤을 더 많이 쓰는 이유는 뭘까요? ~~다형성??~~

### 답변자 : @songks0922
객체 생성 안하고 사용 하면 문제가 생길 것 같은데 좀 더 찾아보겠습니다. 예상되는 문제로는 Thread safe 하지 않아서 race condition이 있을 경우 의도한 대로 작동하지 않을 가능성이 있어보여요.
https://cjw-awdsd.tistory.com/42
