# Chap 6. 객체지향 프로그래밍 I
## 1. 객체지향 언어
- 재사용성과 유지보수성 그리고 중복된 코드 제거 이 세 가지 관점에서 Java를 바라보는 것부터 시작하자.

## 2. 클래스와 객체
- 데이터 저장 형태의 발전 과정
    - 변수 -> 배열 -> 구조체 -> 클래스
    - 그동안 데이터와 함수가 서로 관계가 없는 것처럼 데이터는 데이터끼리, 함수는 함수끼리 따로 다루어져 왔지만 사실 함수는 주로 데이터를 가지고 작업을 하기 때문에 많은 경우에 있어서 데이터와 함수는 관계가 깊다.


- 자바에서 문자열을 단순히 문자의 배열로 정의하지 않고 String 클래스로 정의한 이유는 문자열과 문자열을 다루는데 필요한 함수들을 함께 묶기 위해서이다.

## 3. 변수와 메서드
- 메서드의 이름을 지을 때에는 함축적이면서 기능을 쉽게 알 수 있도록 해야한다.
  - 이는 모두가 알고 있는 내용이지만, 조금 더 체계적인 네이밍을 위해 관련 문서에서는 네이밍에 대해 어떻게 정의하고 있는지 찾아보았다.
  - [**Google Java Style Guide - Naming**](https://github.com/Java-Chip4/StudyingRecord/tree/main/%EC%9E%90%EB%B0%94%EC%9D%98%20%EC%A0%95%EC%84%9D/Chapter06,%2008/Google_Java_Style_Guide.md)
  - [**Effective Java 3rd Edition - ITEM 68 : 일반적으로 통용되는 명명 규칙을 따르라**](https://github.com/Java-Chip4/StudyingRecord/tree/main/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%20%EC%9E%90%EB%B0%94%203%ED%8C%90/9%EC%9E%A5%20:%20%EC%9D%BC%EB%B0%98%EC%A0%81%EC%9D%B8%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%20%EC%9B%90%EC%B9%99/README.md)
  

- 재귀 호출이 계속 일어나다 보면, 결국 스택의 저장 한계를 넘어버려 StackOverflow Error가 발생한다. 그러므로, 재귀 메서드를 작성할 때에는 사용자의 입력에 의존하기 보다는 매개변수의 유효성 검사를 통해 어떤 값이 들어와도 에러 없이 처리될 수 있도록 하자.


- 클래스 멤버가 인스턴스 멤버를 호출하고자 할 때에는 인스턴스를 생성해야 한다. 인스턴스 멤버가 존재하는 시점에 클래스 멤버는 항상 존재하지만. 클래스멤 버가 존재하는 시점에 인스턴스 멤버가 존재하지 않을 수도 있기 때문이다.


## 4. 오버로딩
- 가변인자
    - 가변인자는 사용하기에 편리하지만, 호출될 때마다 내부적으로 배열이 생성되므로 cost가 있다.  
      `public func_a(Object... args) {}`


## 5. 생성자
- 생성자간의 유기적인 관계를 만들어주는 것이 유지보수에 더 좋을 수 있다. 수정이 필요한 경우에도 적은 코드만 변경하면 되기 때문이다. 


- 유틸리티 클래스의 경우, 해당 클래스의 객체를 만들 이유는 전혀 없다. 그래서 객체 생성을 막기 위해 생성자의 접근 지정자를 private로 설정한다.
  ```
  public final class Math {

      //  Don't let anyone instantiate this class.
      private Math() {}
  ... 
  }
  ```


## 6. 변수의 초기화
- 초기화 블럭
  : 인스턴스 초기화 블럭에 모든 생성자에서 공통적으로 수행되어야 하는 코드를 적으면, 코드의 재사용성을 높이고 중복성을 제거할 수 있다.
  ```
  class InitBlock {
      static { // 클래스 초기화 블럭 }
      { // 인스턴스 초기화 블럭 }
  }
  ```


# Chap 8. 예외 처리 (Exception Handling)
# 실행 흐름 제어용 예외의 강력함
throw 된 예외는 catch 될 때 까지 함수 호출 스택을 거슬러 올라갑니다.
따라서 throw와 catch 문법을 사용하면 호출 스택 어디로든 이동할 수 있습니다. if, for문을 사용해서 흐름을 제어하는 것보다 훨씬 강력하죠.(어쩌면 goto보다도 강력합니다)

예를 들어, 아래 재귀 함수는 결과를 순차적으로 return 하면서 탈출하는 전형적인 방식을 따르지 않았습니다. 대신 throw 문 안에 결과를 넣어서 return 없이 한 번에 탈출하고 있습니다.

```java
(예시가 괜히 복잡하긴 한데 재귀 함수를 예시로 들어야 함수 호출 스택 어디로든 한 번에 이동할 수 있다는 걸 표현하기 쉬울 것 같았습니다)  

void searchRecursive(TreeNode node, Object data ) throws ResultException {
    if(node.data.equals(data))
        throw new ResultException(node);
    } else {
        searchRecursive(node.leftChild, data );
        searchRecursive(node.rightChild, data );
    }
}  

출처: https://web.archive.org/web/20140430044213/http:/c2.com/cgi-bin/wiki?DontUseExceptionsForFlowControl
```

searchRecursive 함수를 호출한 함수는 `catch (ResultException e)`로 예외를 받고 있겠죠. 예외 안에 담긴 노드를 빼서 쓰면 복잡한 재귀 리턴 과정을 겪지 않고 쉽게 목표 노드를 찾을 수 있을 것 입니다. 재귀 함수 안에 catch문이 없기에 손쉽게 호출 함수까지 올라올 수 있었기 때문입니다.  

  얼핏 보면 굉장히 편하고 가독성도 좋은 방식 같습니다. 그렇지만 실제로는 이런 코드를 쓰지 않는 이유가 뭘까요?


# 예외를 진짜 예외 상황에만 사용해야하는 이유
몇 가지 이유를 찾았는데 스택 오버플로우와 이펙티바 자바에서 찾은 이유가 가장 좋았습니다. 두 답변의 내용이 겹치지 않아 둘 다 소개합니다.

## 1. stack overflow : "Why not use exceptions as regular flow of control?"
스택 오버 플로우에 위 질문이 있습니다.  
가장 높은 점수를 받은 답변의 내용이 와 닿았습니다.
### 1-1 ) 진짜 exception을 찾기 힘들어진다



(답변 캡쳐)
![image](https://user-images.githubusercontent.com/76809524/187076881-85bda321-035b-412d-a9ae-a9440dfcc054.png)

(출처)  https://stackoverflow.com/questions/729379/why-not-use-exceptions-as-regular-flow-of-control


요약하자면 일반적인 흐름에서 exception을 남발하면, 정말 exception 처리가 필요해서 한 곳을 찾기가 힘들다고 합니다.  
실제 예외 처리 한 곳을 찾기 힘들어지면 가독성 문제가 생기겠죠.  


### 1-2 ) 예외 디버깅이 힘들어진다(개인적인 의견)
답변을 본 다음에는 디버깅 문제도 생기겠다는 생각이 들었습니다. 인텔리제이는 예외 중단점 기능을 제공합니다. 예외가 발생한 곳에 자동으로 중단점을 생성해주어 예외 디버깅을 돕는 기능입니다. 그러나 위 경우 처럼 시작하자마자 예외가 200개 나오고, 이후로도 1초에 5개씩 던진다면 디버깅이 힘들어질 것입니다. 조건을 넣어서 훑는 건 여전히 가능하겠지만, 예외 전체를 훑는건 무의미해집니다. 편기한 툴 기능 하나를 잃는다는 점에서도 예외를 남발하면 안 좋습니다.

(예외 디버깅 창)

![image](https://user-images.githubusercontent.com/76809524/187079247-47e0528e-d855-4f6c-aa51-a274ed9e21bf.png)

  
  ---
  
## 2. Effective Java : Item 69 "예외는 진짜 예외 상황에만 사용하라" 에 제시된 이유
>예외는 （그 이름이 말해주듯） 오직 예외 상황에서만 써야 한다. 절대로 일상적인 제어 흐름용으로 쓰여선 안 된다.  
` Effective Java p 387 중단부`
  

이펙티브 자바 item 69에서는 예외를 절대 일상적인 흐름 제어용으로 사용해선 안 된다고 강조합니다.  

### 예시
더불어 배열 원소를 순회하는 두 가지 코드를 통해 그 이유를 제시합니다.  


(책 코드에서 조금 수정)
```java
코드 1번
try {
    int i = 0;
    while(true)
        array[i++].move();
} catch (ArraylndexOutOfBoundsException e) {
}
//무한 루프를 돌다가 배열의 끝에 도달해 ArraylndexOutOfBoundsException이 나면 catch문을 통해 빠져나가는 코드
```

```java
코드 2번
for (Index i : array)
    i.move();
//일반적인 코드
```
 ### 1번 코드를 작성한 이유에 대한 추론
 JVM은 배열에 접근할 때마다 경계를 넘었는지 검사합니다. 반복문도 배열의 끝에 도달하면 종료합니다. 코드 2번 같이 일반적인 반복문 안에서 배열에 접근하면 검사가 중복되므로 하나의 검사를 생략하기 위해서 1번 처럼 작성했을 것 입니다. 그러나 이는 잘못된 생각입니다.


### 1번 코드가 안 좋은 이유

1. 1번 코드는 직관성이 떨어집니다. 2번 코드는 전형적인 루프문으로 모든 프로그래머들이 보자마자 이해할 수 있는 반면, 1번은 그렇지 않습니다.  
   
2. for문을 통해 배열을 순회하는 코드는 JVM이 다양한 최적화를 수행해줍니다. 이 최적화를 통해 위에서 걱정한 중복 검사를 알아서 없애줍니다. 그러나 try-catch 블럭 안에 들어간 코드는 그렇지 않습니다. 예외는 예외 상황을 처리할 목적으로 만들어졌으므로 JVM 구현자가 성능 최적화에는 신경 쓰지 않았을 가능성이 높다고 합니다. 
   
3. **1번 코드는 디버깅을 훨씬 어렵게 합니다.** 위 반복문 안에 버그가 있다면 흐름 제어에 쓰인 catch문이 버그를 숨겨 디버깅을 훨씬 어렵게 할 것입니다. 만약 move() 메서드가 내부적으로 제 3의 배열을 사용하다가 `ArraylndexOutOfBoundsException`을 일으킨다면 어떻게 될까요? **2번 코드의 move()라면 이 버그는 catch문에 잡히지 않고 끝까지 올라가서 프로그램을 종료시킬 것입니다. 함수 호출 스택을 보고 즉시 어디에서 버그가 났는지 알 수 있겠죠. 그러나 1번에서는 버그 때문에 발생한 예외를 정상적인 프로그램 실행 흐름으로 오해하고 그냥 넘어갑니다.** 이런 경우에 대한 디버깅은 훨씬 어렵습니다.

# RuntimeException과 RE가 아닌 예외들의 차이점
# Throwable 계층 구조

Exception은 Runtime계열이든 아니든 Throwable을 상속 받고 있습니다. 예외에 대해 알아보기 전에 Throwable의 계층 구조를 살펴보겠습니다.
![image](https://user-images.githubusercontent.com/76809524/188663636-bb60e0b3-2786-401d-a219-a49947db77e6.png)
(출처: https://madplay.github.io/post/java-checked-unchecked-exceptions)

 Throwable 클래스는 Exception과 Error 클래스의 부모입니다. 자바에서 예외나 오류를 Throw할 수 있는 이유는 이들이 Throwable 클래스를 상속 받고 있기 때문입니다.  
## Throwable 자식들의 3분류
자바에서는 Throwable의 자식 클래스를 크게 3분류로 나누어보는 것이 일반적입니다. Error계열, Exception계열, RuntimeException 계열입니다. RuntimeException은 Exception을 상속 받았기 때문에 Exception과 비슷한 것이라고 생각할 수도 있습니다. 물론 비슷한 점도 있으나 RuntimeException은 자신의 부모 클래스인 Exception과 달리 Unchecked Exception이라는 결정적인 차이점이 있습니다. 따라서 RE와 RE가 아닌 예외를 나누어서 생각하는 것이 일반적입니다.

## 각 분류의 특징
(일반적인 범주를 말하는 것이며 예외적인 클래스도 많음. 예를 들어 Exception 클래스를 상속 받았지만 예외 처리를 할 수 없는 문제도 있다.)
1. **Error 클래스를 상속 받은 분류** : 실행을 계속할 수 없을 정도의 심각한 오류가 일어났음을 알립니다. (ex. 하드웨어, JVM 오류 등)
2. **Exception 클래스를 상속** : 미리 예외 처리가 가능한 문제. 예외 처리를 함으로서 문제 상황을 수복할 수 있는 예외를 일으킵니다. - 컴파일러 수준에서 예외 처리가 강제된다는 특징이 있습니다. 예외의 가능성이 있는데 catch하거나 throws 문을 작성하지 않았을 경우 컴파일 오류를 줍니다. 
3. **RunTime Exception 클래스를 상속** : 예외 처리 보다는 코드 수정으로 고쳐야 하는 문제 or 코드 수정으로도 고칠 수 없어 프로그램을 종료시켜야 하는 문제 등이 일어났을 때 예외를 일으킵니다.

위 분류 중 Exception과 RunTimeException의 용도가 달라진 이유는 Exception 계열은 Checked Exception이며 RunTimeException 계열은 Unchecked Exception이기 때문입니다.  
다시 말하지만 일반적인 특징 및 용도를 나타낸 것일 뿐 기본 계공되는 예외들이 무조건 위 설명을 따르는 것은 아닙니다. 커스텀 예외를 만들어서 사용할 때도 예외 처리를 강제하고 싶은 예외일지라도 RunTimeException을 상속 받도록 만들 수도 있습니다. 

## RuntimeException, Exception 차이점
RE와 RE가 아닌 예외의 차이점은 예외처리를 제대로 하고 있는지 컴파일러가 체크해주는지 여부에 있습니다.  
### RE인 예외 == UnChecked 예외
RE의 경우 예외를 발생시키기만 하고 처리해주지 않아도 컴파일러가 오류를 내지 않습니다.  

(컴파일러가 오류 체크하지 않는 모습)
![image](https://user-images.githubusercontent.com/76809524/188786832-db5c05f7-07e5-4134-ac94-dcbf449d7e77.png)  

(실행 후 런타임 오류로 잡아야 함)
![image](https://user-images.githubusercontent.com/76809524/188786841-f888132b-757a-4179-8fb3-ae0ccdc8d4e0.png)  


### RE가 아닌 예외 == Checked 예외
반면 RE가 아닌 예외의 경우 예외를 발생 시키기만하고 catch를 하거나 이전 함수로 throws하지 않으면 컴파일 오류를 줍니다.  

(처리되지 않은 예외로 인한 컴파일 오류 발생)
![image](https://user-images.githubusercontent.com/76809524/188786500-ba6036e7-0330-4d93-b645-f891af0ef89d.png)  

(컴파일 오류를 해결하기 위해서 catch해주거나)
![image](https://user-images.githubusercontent.com/76809524/188786642-57fbaf34-cbe7-41e4-ab3c-40f5bb0305a0.png)  

(이전 함수로 throws 해줄 것)
![image](https://user-images.githubusercontent.com/76809524/188786646-71d2ca2b-5724-448a-9065-655c1b0465b7.png)  


# checked / unchecked 예외를 선택하는 기준
정석에서 RE가 아닌 계열, RE 계열 예외의 차이에 대해서 배울 수 있었습니다. 하지만 언제 어떤 계열의 예외를 사용해야하는지에 대한 기준은 부족한 것 같았습니다. 이펙티브 자바의 item 70, 71, 77 에서 선택 기준에 대한 내용을 찾을 수 있었고 그 내용을 공유합니다.

저희 스터디가 다음 책으로 이펙티브 자바를 공부할 예정이니 자세한 공부는 그 때 가서 하도록 하겠습니다. 이번 이슈에서는 item들이 왜 저런 주장을 했는지 납득할 수 있을 정도로 간략하게만 공유해보겠습니다.
## item 70, 71, 77)
### -복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라
### -필요 없는 검사 예외 사용은 피하라
### -예외를 무시하지 말라

**item 70, 71**은 '**호출자가 예외를 복구(완전히 처리)할 수 있는 상황에서는 checked를, 그렇지 않은 대부분의 상황에서는 unchecked를 쓰라**'고 주장하고 있습니다. checked 예외를 던지면 호출자가 그 예외를 잡아 catch로 처리하거나 throws로 더 바깥으로 전파하게 강제됩니다. 따라서 어떤 메서드가 checked 예외를 던진다는 건 메서드 제작자가 이 메서드를 사용했을 때 발생할 수 있는 문제를 외부 사용자에게 알려주는 것 입니다. 이를 달리 해석하면 메서드 사용자에게 문제 발생에 대한 대비를 하여 이를 처리하라고 요구한 것입니다. 발생하는 문제를 프로그래머가 제대로 처리하면 프로그램의 안정성을 높일 수 있습니다.

 한편, 호출자가 문제를 catch만 하고 아무 조치를 취하지 않을 수도 있지만 이는 좋지 않은 생각이라고 합니다(**item77**). 꼭 복구시켜야 하거나, 프로그램을 종료 시켜야하는 문제가 발생했을 때 의미 없는 catch로 인해 문제를 무시하고 넘어갈 수 있기 때문입니다. 치명적인 문제를 빈 catch블럭으로 못 본척 지나치면 프로그램은 오류를 내재한 채 동작하게 됩니다. 그러다 어느 순간 문제의 원인과 상관 없는 곳에서 갑자기 작동을 멈출 수 있습니다. 이러면 문제가 무엇인지, 어디에서 발생했는지 찾기가 힘듭니다. 결과적으로 문제가 발생했을 때 catch하지 않고 프로그램을 종료시키는 경우보다 훨씬 디버깅하기 어려운 문제로 변하는 것입니다.

 다시 예외를 선택하는 기준으로 돌아오겠습니다. 조슈아 블로크는 복구할 수 없거나, 실행을 계속할 수 없을 정도로 치명적인 예외라면 RE를 사용하라고 권합니다. 이런 경우라면 (위 문단에서 제시한 이유로 )더 실행해봐야 득보다 실이 많기 때문입니다. 컴파일러가 catch 유무를 체크해준다 한들 catch에서 할 수 있는 일이 없다면 RE로 선언하여 catch를 강제하지 않도록 하는 편이 낫다는 뜻입니다(checked 예외를 던진다 한 들 호출자 입장에서 **아래 코드1, 2**와 같이 처리할 수 밖에 없다면 checked를 던지는 의미가 있을까요?). 반면 호출자가 문제 상황을 완벽하게 복구할 수 있는 예외는 checked를 사용하면 될 것입니다. 프로그램을 종료 시키지 않고 제대로 된 예외처리 후 계속 동작하게 할 수 있기 때문입니다.
```java
//1
} catch (TheCheckedException e) {
    throw new AssertionErrorO;
}
//2
} catch (TheCheckedException e) {
    e.printStackTraceO;
    System.exit(1);
}
// 출처 이펙티브 자바 item 71 : 필요 없는 검사 예외 사용은 피하라  
```
