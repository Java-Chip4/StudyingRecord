# Chap 7. 객체지향 프로그래밍 II
## 추상 클래스
- [선언만 되어있고 구현은 되어있지 않지만, 정작 추상메서드가 아닌 메서드](https://github.com/Java-Chip4/StudyingRecord/issues/16)  
  : Issue에서 추상 클래스와 Adapter Pattern이 적용된 WindowAdapter 클래스에 대해서 다루었다.

   ### 질문자 : @KimChanJin97
   ### 선언만 되어있고 구현은 되어있지 않지만, 정작 추상메서드가 아닌 메서드
   자바의 정석(상) p.347
   
   ```
   처음부분
   "abstract는 '미완성'의 의미를 가지고 있다."
   ```
   
   ```
   중간부분
   "꽤 드물지만 추상 메서드가 없는 클래스, 즉 완성된 클래스도 abstract를 붙여서 추상 클래스로 만드는 경우도 있다.
   예를 들어, java.awt.event.WindowAdapter는 아래와 같이 아무런 내용이 없는 메서드들만 정의되어 있다."
   ```
   
   ```
   WindowAdapter 클래스 소스코드
   https://developer.classpath.org/doc/java/awt/event/WindowAdapter-source.html
   ```
   
   WindowAdapter 클래스의 소스코드를 보면 메서드들이 선언만 되어있고 구현은 되어있지 않지만 정작 메서드들은 abstract이 붙여져있지 않다. 심지어 완성된 클래스라고 이야기하고 있다.
   
   일반적으로 추상클래스는 (abstract이 붙여진) 추상메서드를 가진다.
   하지만 **WindowAdapter의 예시처럼 (abstract이 붙여지지 않은) 선언만 되어있고 구현이 되어있지 않은 메서드를 가질 수도 있다.**
   
   **선언만 되어있고 구현은 되어있지 않지만, 정작 추상메서드가 아닌 메서드**는 어떻게 존재할 수 있는 것이고, 왜 사용하는 것일까?

   ---

   ### 답변자 : @anthologia
   ### Q1. WindowAdapter 와 같은 추상 클래스는 왜 있을까❓
   ### Q1-1. 추상 클래스❓
   
   먼저, Oracle에서 정의하는 추상 클래스에 대해 찾아보았습니다.
   
   > 추상 클래스는 abstract 키워드로 선언된 클래스이며, 추상 메서드를 포함할 수도 포함하지 않을 수도 있다. 객체를 생성할 수는 없지만, 상속을 통해 하위 클래스를 받을 수 있다.
   (https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)
   
   위의 정의를 통해 WindowAdapter 클래스처럼 추상 메서드가 없는 추상 클래스가 존재할 수 있다는 걸 알게 되었습니다.
   
   그러나 WindowAdapter 클래스는 텅 빈 메서드만을 구현하고 있습니다.
   
   이런 클래스는 왜 존재하는 걸까요?
   
   ### Q1-2. 텅 빈 메서드를 구현하는 추상 클래스❓
   
   이번에도 Oracle에서 설명하는 WindowAdapter 클래스의 내용을 찾아보았습니다.
   > 이 추상 어댑터 클래스는 window event를 위해 존재합니다. 이 클래스 내에 존재하는 메서드는 모두 비어 있습니다. 이 클래스는 listener 객체를 편리하게 생성하기 위해 존재합니다.  
   (https://docs.oracle.com/javase/8/docs/api/java/awt/event/WindowAdapter.html)
   
   메서드가 비어있는 것이 왜 listener 객체를 생성하는데 편리함을 준다는 걸까요?
   
   이를 위해서는 Adapter Pattern에 대해서 알아 보아야 합니다.
   
   #### Q1-2-a. Adapter Pattern❓
   > 한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환합니다. 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있습니다.
   
   Adapter Pattern이 적용된 아래 코드를 통해 이해해 볼 수 있습니다.
   
   ```
      // 클라이언트에서 사용하고자 하는 인터페이스
      interface Target {
        Response request();
      }
      
      // 이미 개발되어 사용 중인 클래스
      class Adaptee {
        Response specificRequest() {
          return new Response();
        }
      }
      
      // Adaptee 클래스를 클라이언트에서 Target으로 사용할 수 있도록 하는 Adapter 클래스
      class Adapter implements Target {
        private final Adaptee adaptee;
      
        public Adapter(Adaptee adaptee) {
          this.adaptee = adaptee;
        }
      
        @Override
        public Response request() {
          return this.adaptee.specificRequest();
        }
      }
      
      // 클라이언트
      class Client {
        Target target = new Adapter(adaptee);
      }
      ```
   
   결과적으로 Adapter 클래스를 통해서 Adaptee 클래스를 Target 인터페이스로 사용할 수 있게 되었습니다.  
   (https://johngrib.github.io/wiki/pattern/adapter/#fn:gof)
   
   이제 다시, WindowAdapter 클래스로 돌아오겠습니다.
   
   ```
      public abstract class WindowAdapter
      extends Object
      implements WindowListener, WindowStateListener, WindowFocusListener
      ```
   
   사실 WindowAdapter 클래스는 위에서 다룬 Adapter Pattern을 적용한 클래스입니다.
   
   그래서 총 3 개의 WindowListener, WindowStateListener, WindowFocusListener 인터페이스를 구현하고 있는 모습을 볼 수 있습니다.
   
   그 덕분에 WindowAdapter 클래스를 통해 필요한 메서드만 오버라이드하여 사용할 수 있습니다.
   
   #### 1-2-b. 메서드가 비어있는 것이 왜 편리함을 주는 것일까❓
   만약 WindowAdapter 클래스의 모든 메서드가 추상 메서드였다면, 우리는 어쩌면 그 메서드를 모두 구현해야만 합니다.
   
   그러나 텅 빈 일반 메서드로 구현함으로써, 다른 메서드는 신경 쓰지 않고, 필요한 메서드만 오버라이드하여 사용하는 것이 가능해집니다.
   
   그래서 편리함을 준다고 표현되어 있는 것입니다.
   
   아래 코드를 통해 WindowAdapter 클래스의 편리함을 보겠습니다.
   
   ```
          JFrame f = new JFrame("Test");
          f.addWindowListener(new WindowAdapter() {
      
              @Override
              public void windowClosing(WindowEvent e) {
                  System.out.println(e);
              }
          });
      ```
   (https://stackoverflow.com/questions/39845163/is-windowadapter-is-an-adapter-pattern-implementation-in-java-swing)
   
   
---

   
## 객체지향과 캡슐화
- [최범균 - 객치제향 프로그래밍 입문](https://github.com/Java-Chip4/StudyingRecord/tree/main/%EC%9E%90%EB%B0%94%EC%9D%98%20%EC%A0%95%EC%84%9D/Chapter07,%2009/%EC%B5%9C%EB%B2%94%EA%B7%A0_%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%9E%85%EB%AC%B8.md)  
  : 객체와 캡슐화에 대한 내용을 다루었다. 특히, 캡슐화를 해야하는 이유에 중점을 두고 정리하였다.


---


## 인터페이스와 다중 상속
- [인터페이스만 다중 상속이 가능한 이유](https://github.com/Java-Chip4/StudyingRecord/issues/6)  
 : Issue에서 인터페이스에 대해 다룬 내용이다. 아래의 두 주제에 대해서 자세하게 다루었다. 
  - Java에서 인터페이스만 다중 상속이 가능한 이유
  - 인터페이스에서 다이어몬드 문제가 발생하는 경우와 그 해결
