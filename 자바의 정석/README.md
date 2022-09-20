# 자바의 정석 (남궁성)
## 목차
### [Chapter 1 - 5 : 자바 기본 문법](#chapter-15-자바-기본-문법)
### [Chapter 6 : 객체지향 프로그래밍 I](#chap-6-객체지향-프로그래밍-i)
### [Chapter 7 : 객체지향 프로그래밍 II](#chap-7-객체지향-프로그래밍-ii)
### [Chapter 8 : 예외 처리(Exception Handling)](#chap-8-예외-처리-exception-handling)
### Chapter 9 : java.lang패키지와 유용한 클래스
### Chapter 10 : 날짜와 시간 & 형식화
### Chapter 11 : 컬렉션 프레임웍
### [Chapter 12 : 지네릭스, 열거형, 애너테이션](#chap-12-지네릭스,-열거형,-애너테이션)
### Chapter 13 : 쓰레드(Thread)
### Chapter 14 : 람다와 스트림
### Chapter 15 : 입출력(I/O)
### Chapter 16 : 네트워킹(Networking)


---


# Chapter 1~5 자바 기본 문법
## 자바 실행 구조, JVM의 역할
 책 첫 챕터에 자바 실행 구조 및 JVM에 대한 설명이 있다. 자바 입문자를 염두에 두어 쓰인 책이다 보니 좀 더 내용을 보충할 수 있을 것 같아서 정리한다. 
 프로그램을 실행시키는데 있어 C와 자바의 차이점을 중심으로 이미지를 만들어보았다. 이미지로 비교하면 자바 실행 구조가 어떤 특징을 가지고 있는지 좀 더 명확하게 이해할 수 있을 것 같았기 때문이다.

### 실행 구조
자바를 공부하면서 C의 실행 구조를 깊이 공부할 필요는 없다. 하지만 C의 실행 구조를 알면 자바는 기존 언어와 어떤 차이점이 있는지, 자바가 환경에 독립적인 언어가 될 수 있었던 원리는 무엇인지 이해할 수 있게 된다.

어떤 프로그래밍 언어든 1)소스 코드는 2)변환 과정을 거쳐 3)특정 환경에서 실행될 수 있는 기계어가 된다. CPU나 운영체제 종류 별로 이해할 수 있는 기계어는 모두 다르다. 따라서 어떤 환경에서 실행될 수 있는 기계어는 그 환경에 맞춤되어 있는 특정 기계어여야만 한다. 자바와 C의 실행 모델 모두 컴퓨터에서 실행되기 위해 이 기계어를 만들어내는 과정이라고 볼 수 있다.

### C언어
![image](https://user-images.githubusercontent.com/76809524/184490285-62d3eec0-22ee-451c-aead-7c940a012120.png)

C의 경우 소스 코드를 컴파일러에 넣으면 특정 환경에서 바로 실행 할 수 있는 기계어로된 실행 파일이 나온다. 특정 환경용 실행 파일을 만들기 위해서는 실행 파일을 만들어주는 컴파일러 또한 그 환경 전용이어야한다.  

C에서 프로그램을 배포하려면 하나의 소스코드를 여러 개의 전용 컴파일러에 넣어서 각 환경에 맞는 실행 파일들을 배포해야 한다. 실행 파일 하나로는 모든 환경을 커버할 수 없기 때문이다. 따라서 C개발자들은 환경 별로 여러 버전의 실행 파일을 만들어서 모두 배포해야 하는 번거로움을 겪는다. 


### 자바, JVM의 역할
![image](https://user-images.githubusercontent.com/76809524/184490395-165b3693-077a-4034-9980-4be6bc1d69d3.png)
자바의 경우 소스 코드를 컴파일러에 넣으면 기계어 대신 바이트 코드가 나온다. 바이트 코드는 기계어가 아닌 JVM이 해석할 수 있는 범용적인 파일이다. 따라서 C와 자바의 컴파일러는 성격이 상당히 다르다.  

이렇게 나온 바이트 코드는 JVM이 실행시킨다. 바이트 코드는 범용적인 파일이라 어떤 환경 전용 JVM이 든 같은 .class파일을 보고 실행시킨다. 실제로 자바 프로그램은 기계어가 아닌 .class파일을 JVM에게 해석시키는 것으로부터 시작된다. 자바는 JVM 실행 모델을 도입함으로써 바이트 코드만 배포하면 모든 환경에서 프로그램을 실행시킬 수 있는 크로스 플랫폼을 구축한 것이다.  

물론 잃은 점도 있다. 컴퓨터가 프로그램을 실행하려면 기계어가 필요함으로 JVM은 바이트 코드를 기계어로 번역한다. 이 번역 과정이 실행 중에 일어나므로 C에 비해서는 속도가 느리다. C로 작성된 실행 파일의 경우 곧바로 실행될 수 있는 전체 기계어 파일이 완성되어 있기 때문이다. 하지만 최근 들어 인터프리터, JIT 최적화 기법이 발달됨에 따라 속도의 격차가 많이 줄어들었다.

### JIT 컴파일러 최적화
여기서 우리는 JIT 컴파일러가 어떠한 방식으로 최적화를 이뤄냈는지 궁금증을 가졌고, 그에 대해 짧게 찾아보았다. 

위에서 언급했듯이, 자바는 애플리케이션이 실행될 때, 정확히는 각각의 메서드가 실행될 때마다, JIT 컴파일러가 이를 인터프리팅하여 실행된다.  그런데, 인터프리팅을 하는 건 인터프리터인데, 왜 자바는 JIT 컴파일러라고 부르는 것일까? 이를 위해서는 컴파일과 인터프리팅의 차이에 대해 알아야 한다.

컴파일은 프로그램을 실행시키기 전, 코드 전체를 기계어(native code)로 변환한다. 그에 반해, 인터프리팅은 당장 실행되어야 하는 부분의 코드만을 읽어가며 기계어로 변환한다.

다시 돌아가면, 자바는 컴파일과 인터프리팅 작업을 두 번이나 거치기 때문에 느리다고 했다. 그래서 Java의 JIT 컴파일러는 성능 최적화를 위해 캐싱을 이용한다. 즉, 반복되는 코드를 모두 컴파일러를 통해 기계어로 미리 변환시켜 인터프리터가 해당 부분을 읽지 않아도 되도록 한다.  

이러한 이유 때문에 자바에서는 JIT 컴파일러라고 부르는 것이다.

[참고 블로그]((https://aboullaite.me/understanding-jit-compiler-just-in-time-compiler/))


---
## 기본형 vs 참조형 변수
 2장에서 변수의 타입에 대해 설명하지만 기본형에 대해서만 설명하고 있다. 참조형에 대해서는 일단 다루지 않다가 한 참 후인 6장 264p에서 메서드 매개변수와 함께 설명한다.
그 때 배워도 되겠지만, 프로그래밍에서 '기본형 vs 참조형'이라는 주제가 워낙 유명한 주제인 만큼 두 타입을 비교해보는게 의미 있을 것 같아 다루어 보겠다.  

### 값을 저장한 변수 vs 주소를 저장한 변수
두 변수 타입을 이해할 때 변수가 저장하고 있는 값이 무엇인지를 생각하면 이해하기 쉽다. '실제 값'을 저장하면 기본형, 다른 변수가 위치한 메모리 '주소'를 저장하면 참조형 변수이다(자바는 실제 메모리 주소 대신 JVM이 메모리 주소와 맵핑해주는 참조값을 저장하지만 이 또한 주소와 비슷한 개념이다).  

 
 ![image](https://user-images.githubusercontent.com/76809524/184539874-d969833e-0bc0-42cb-939b-8f1415be3aa6.png)

<div style="text-align: right"> (대충 만듬) </div>


  

 위 그림을 보면 스택 메모리 안에 int형 변수, float형 변수, Person 형 변수가 있다. 어떤 함수 안에서 변수를 선언하면 그 변수는 타입에 상관없이 항상 스택에 저장된다. Int나 float 같은 기본형은 스택에 변수의 값이 곧바로 저장되고, Person같은 참조형(클래스형)은 스택에 Person의 데이터들을 저장한 곳을 가르키는 주소값이 저장된다.

 그렇다면 실제 Person의 데이터(멤버 변수 등)는 어디에 저장되었을까? Person 객체의 데이터는 힙 메모리에 저장된다. 우리가 객체를 만들 때 'new' 라는 키워드를 쓰는데 이 키워드는 힙 메모리에서 새로운 공간을 할당 받겠다는 의미이다.  
'new' 키워드로 힙 메모리를 할당 받아 Person객체의 데이터를 저장하고 그 힙 메모리의 주소를 스택의 Person형 변수로 참조하는 것이다.  

### 값에 의한 호출 vs 참조에 의한 호출
함수에 매개변수를 넘길 때 매개변수 타입에 따라 **'값에 의한 호출'**,  **'참조에 의한 호출'** 로 나누곤 한다.  일반적으로 이 개념을 학습할 때 '기본형은 값이 복사되어서 넘어가며, 참조형은 원본이 넘어간다'고 배우는데 값형과 참조형의 차이를 안다면 **이렇게 나눠서 이해할 필요가 없다**. 매개변수가 저장하고 있는 값이 무엇인지를 생각하면 이해하기 쉽다.  

값형은 '실제 값'을 가지고 있고, 참조형은 '주소 값'을 가지고 있다. 따라서 값형 매개변수는 값을 복사해서 넘어가고, 참조형 매개변수는 주소를 복사해서 넘어간다. 두 매개변수 모두 매개변수 값이 복사되어 함수로 넘어간다는 점은 동일하다. 하지만 참조형의 경우 복사되는 값이 주소 값이므로 함수 안에서 힙 메모리의 객체 데이터에 접근할 수 있다(객체의 주소를 알기 때문). 이 때문에 참조형 매개변수는 원본이 넘어간 것처럼(?) 보이는 것이다.

'참조에 의한 호출'을 변수 원본이 넘어가는 것으로 이해해도 코딩할 때 불편함이 없다면 아무 상관없다. 하지만 참조형 안에 또 참조형 변수가 있어서 참조가 꼬리를 물고 여러 번 이어질 때 헷갈려하는 모습을 종종 봤다. 참조가 여러 번 이어질 때 참조형 변수들이 들어있는 배열 등을 참조하고 있으면 더욱 헷갈릴 수 있다. 따라서 변수 타입이나, 매개변수 전달에 대해서 좀 더 명확하게 이해하고 싶다면 위 같은 시각으로도 접근해보는 것을 추천한다.


---
## 자바에서의 문자열
자바에서 모든 문자열(string)은 String 클래스를 통해 표현된다. 그리고 String 클래스는 아래를 통해 확인할 수 있듯이, 문자열을 final한 byte 배열에 나누어 저장하기 때문에 변경될 수 없어 immutable하다고 불린다.
![image](https://user-images.githubusercontent.com/13804810/185648611-46219a77-aa0b-46ea-96f9-4fef6d1cedb9.png)

그러나 String 클래스의 객체를 생성해야만 문자열을 만들 수 있는 것은 아니다. `""` 를 통해 문자열 리터럴(String literal)을 생성할 수 있다. 그럼 String 객체와 문자열 리터럴의 차이는 무엇일까?  

### String 객체
new 키워드를 통해 String 객체를 생성하면, Heap 영역 내에 해당 문자열을 가진 새로운 객체가 생성된다. 이후, 똑같은 문자열을 갖고 다시 new 키워드를 통해 다른 객체를 생성하면 역시 문자열은 같지만, 다른 메모리 주소값을 가진 새로운 객체가 생성된다.

### String literal
그러나 문자열 리터럴을 통해 문자열을 생성하면, String constant pool(이하 pool) 덕분에 조금은 다른 일이 벌어진다. pool은 Heap 영역 내에 별도로 존재하는 문자열 상수를 위한 공간이다. 만약, 문자열 리터럴을 통해 한 문자열을 생성하면, 자바는 pool 내에 해당 문자열이 이미 존재하는지 확인한다. 만약 해당 문자열이 존재하지 않으면, pool 내에 해당 문자열을 새롭게 등록시킨다. 그러나 그 반대의 경우에는 중복 등록을 하는 것이 아니라, 이미 존재하는 문자열 리터럴을 참고하여 재사용하도록 한다.    

### String 객체와 String literal의 메모리 구조
```     
String a = "JavaChip";
String b = "JavaChip";
String c = new String("JavaChip");
String d = c.intern();
```
먼저, 위 코드에서 각각 a와 b는 "JavaChip"이라는 문자열 리터럴을, c는 String 객체를, d는 c의 intern() 메서드를 통해 문자열을 생성하였다. 그리고 네 개 문자열의 메모리 구조는 아래의 사진과 같다.

<img src="https://user-images.githubusercontent.com/13804810/185579225-5821f2b2-be77-4d6b-88bd-1bfa57574b9c.png" width="400" height="400" align=""/>

a와 b는 동일한 메모리 주소값을 가진다. c는 a, b와 동일한 문자열을 갖는 것처럼 보이지만, 문자열 리터럴이 아닌 new 키워드를 통한 문자열 객체를 생성했기 때문에 독립된 메모리 주소값을 가진다. 그러나 d는 c 객체를 통해 만들어졌음에도 불구하고 a, b와 동일한 메모리 주소값을 갖는다. 어떻게 이런 일이 벌어질 수 있을까?

### String intern() 메서드
그 이유는 intern() 메서드가 가진 특징 때문이다. 아래는 String 클래스에서 구현하고 있는 intern() 코드이다. intern() 메소드는 호출한 문자열 객체의 문자열이 pool에 이미 등록되어 있다면, 해당 문자열 리터럴의 주소값을 반환한다. 그 반대의 경우에는 해당 문자열을 pool에 새롭게 등록하고 해당 주소값을 반환한다.  이 덕분에 문자열 리터럴인 a, b와 문자열 객체의 intern() 메서드를 통해 만든 d의 메모리 주소값이 같을 수 있게 된 것이다.

추가적으로 사실, 문자열 리터럴로 초기화된 String은 String interning 과정을 거치게 된다. 즉, intern() 메서드를 통해 해당 문자열 리터럴을 String pool에 캐싱하고, 그 값은 불변성을 갖게 된다.  

#### 생각보다 큰 intern() 메서드 호출 비용
결과만 보면, 캐싱을 통해 메모리를 아낄 수 있기 때문에 intern() 메서드를 적극 사용하는 것이 옳아보인다. 그러나 이 역시 부작용이 있을 수 있다. 메서드 호출 비용이 크기 때문이다. intern() 메서드의 동작 원리를 생각해보면, 먼저 String 객체 생성이 이뤄져야 한다. 그리고 String constant pool 내에 존재하는 문자열 리터럴을 찾아서 String.equals() 메서드를 통해 비교해야 한다. 그러니 속도가 느릴 수 밖에 없다.  그러므로 메모리를 아낄 수 있다는 생각에 매몰되어 퍼포먼스를 낮추는 일을 저지르지 말자.

![image](https://user-images.githubusercontent.com/13804810/185646915-fa8552bd-9e09-48e0-be60-ac31063b075b.png)


### 자바는 왜 String constant pool을 만들었을까?
String은 다른 자료형과 달리, 한 번 사용되면 재사용될 확률이 높다. 그래서 한 번 사용된 값을 저장하고, 이를 재사용할 수 있도록 캐싱을 적용한 결과가 String constant pool이다. 그 덕분에 문자열 리터럴을 사용할 때 메모리 할당을 최적화할 수 있게 되었다.

초기에는 String constant pool가 Permanent 영역에 존재했었다. 이 영역은 고정된 사이즈이고, GC 대상에서 제외된 영역으로, 객체의 생명주기가 영구적일 것으로 생각되는 객체를 주로 저장하는 곳이었다. 그래서 interning 되는 문자열이 많아지면, Permanent 영역이 가득 차 OutOfMemory Error(OOM)가 발생할 수도 있었다.

그러나 Java 8 이후부터는 pool이 Heap 영역에 저장됨으로써, OOM 에러를 만날 확률이 줄어듦과 동시에 GC 대상이 되어 메모리 관리에서 더 많은 이점을 얻게 되었다.

### new String(...)을 사용할 때는 언제일까?
String 객체의 값  immutable하기 때문에 thread-safe하다. 그리고 문자열 리터럴 역시 String 문자열이기에 해당 장점을 그대로 갖고, 중복된 값을 저장하지 않아 메모리를 낭비하지 않는다는 장점까지 존재한다. 그에 반해, String 객체는 중복된 값을 가진 객체를 계속 생성할 수 있다.

많은 고민을 했으나, immutable한 값을 가진 객체를 중복 생성하여 사용할 상황을 생각해낼 수 없었다. 심지어 **Effective Java 3rd edition - ITEM 6 : 불필요한 객체 생성을 피하라**에서도 아래와 같은 예를 절대 따라하지 말 것! 이라고 표현하였다.

```
String s = new String("JavaChip");
```

그 이유는 `"JavaChip"` 문자열 리터럴이 string interning 과정을 거치고, s 객체를 생성하여 객체 내부의 값에 `"JavaChip"` 문자열 리터럴의 값을 넣기 때문이다. 즉, String 객체와 String literal 두 개를 만드는 셈이다.
 
그러나 더 찾아보니, String 생성자의 인자로 문자열 리터럴 대신 String 객체를 넘겨준다면, 상당히 유용하게 사용할 수 있는 사례가 있었다.

파일을 통해 읽어온 엄청나게 큰 크기의 문자열을 가진 String 객체를 substring() 할 경우였다. substring() 메서드가 내부적으로 원본 String 객체의 문자열 값이 담긴 배열 레퍼런스를 계속 참조하도록 하여, 큰 크기의 문자열이 GC되지 않아 문제가 생기는 것을 막을 수 있는 사례였다.  
(https://stackoverflow.com/questions/390703/what-is-the-purpose-of-the-expression-new-string-in-java/390854#390854)

그러나 이 사례는 Java가 업데이트 되면서 substring() 메서드가 더 이상 원본 문자열의 레퍼런스를 참조하지 않고, 잘라진 새로운 배열을 반환하게 되면서 더 이상 쓸모가 없게 되었다.

결국, String 객체를 사용할 상황이 거의 존재하지 않는다. 만약 사용되어야 한다면, String literal의 장점과 String Object의 단점을 모두 무시해야 할 상황이어야 할 것이다.

### 자바는 왜 String을 immutable하게 만들어서 불편한 부수효과를 만들어냈을까?
자바를 개발한 [제임스 고슬링에 의하면](https://www.artima.com/articles/james-gosling-on-java-may-2001#part13), 캐싱, 보안, 복사가 필요없는 빠른 재사용성, 동기화 성능 때문이라고 한다.  
(https://ellerymoon.tistory.com/104)
- 보안 이슈: 만약 네트워크 통신을 통해서 받은 ID와 password String이 mutable하다면 validation을 거친 이후에도 받아온 String의 안전을 보장할 수 없다.
- 동기화 이슈: immutable하다면 여러 쓰레드에서 접근해도 thread-safe하다.
- String.hashCode()를 이용한 캐싱: String 문자열이 immutable하므로, 이를 키값으로 사용한 해싱 컬렉션의 퍼포먼스가 향상된다. 이 방식은 String pool에서도 사용되어 기존에 캐싱된 문자열의 키값이 있는지 빠르게 조회할 수 있다.

더불어 **Effective Java 3rd edition - ITEM 17 : 변경 가능성을 최소화하라**에서도 immutable 클래스는 아래와 같은 이유로 적극 권장하였다.
- 가변 클래스보다 설계, 구현, 사용이 쉽고 오류가 생일 여지도 적다.
- 불변 객체는 단순하다. 값이 절대 바뀌지 않기 때문에 믿고 사용할 수 있다.
- 불변하기에 근본적으로 thread-safe하다. 그래서 안심하고 공유할 수 있다.
- 불변 객체끼리는 내부 데이터를 공유할 수 있다. 어차피 바뀌지 않기 때문이다.
- failure atomicity를 보장한다. 그래서 불일치 상태에 빠질 일이 없다.


### String.hashcode()가 문자열에 대한 고유값을 가질 수 있을까?
앞서 immutable한 문자열을 key로 사용하면, 해싱 퍼포먼스가 올라가고, String Constant pool에서도 내부적으로 HashTable 구조를 갖고 있어 성능이 보장된다고 한다.

그러나 해시는 중복된 키를 사용할 경우, 버킷에 이미 레코드가 존재하여 해시 충돌이 일어나고, 최적 성능을 보장받지 못하는 상황이 벌어지기도 한다. 그래서 문자열의 해시코드는 유일한 고유값을 가질 수 있는 것인지 궁금증이 생겼다.

먼저, [자바에서 String의 해시코드를 구하는 방법](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html#hashCode--) 은 ASCII 코드값을 이용해 비트 연산의 합으로 해시코드를 만들기 때문에 중복이 생길 수 있다. 예를 들어서, `"FB"`와 `"Ea"`의 해시코드는 같다.

게다가 String.hashcode()의 반환 타입은 int이다. int는 4byte의 정수공간만을 담을 수 있지만, String은 최대 2^31 - 1의 길이를 가질 수 있으므로 문자열 해시코드의 유일성은 보장받을 수 없다.

### String + String은 속도가 느리다?
결론 먼저 말하자면, 이제는 더 이상 느리지 않다.

String 문자열은 immutable하다. 그래서 두 String의 덧셈을 할 경우에는 기존 두 문자열은 그대로 존재하고, 두 개가 더해진 새로운 문자열 인스턴스가 생성된다. 이러한 과정 때문에 String 문자열의 덧셈은 느렸다. 
```
String str = "Java" + "Chip";
```
위의 경우라면, String constant pool에 `"Java"`와 `"Chip"`은 그대로 존재하고, `"JavaChip"`이라는 새로운 문자열 리터럴이 추가된다. 이후에는 str 레퍼런스 변수가 더 이상 `"Java"`와 `"Chip"`을 가르키지 않기 때문에 두 문자열 리터럴은 GC 대상이 된다.

그러나 JDK 1.5 이후부터는 컴파일 단계에서 String 문자열의 덧셈이 StringBuiler로 연산되도록 변경되었다. 그래서 더 이상 성능상의 큰 이슈는 존재하지 않는다.


### StringBuilder와 StringBuffer
StringBuilder가 무엇이길래 성능상의 이슈가 없다고 하는 것일까? StringBuilder는 immutable한 String과 달리 가변의 문자열을 다룰 수 있다. 그래서 문자열이 변경되어도 새로운 객체를 만들지 않아도 되기 때문에 속도가 빠르다.

StringBuilder 외에도 StringBuffer가 존재하는데, 이 역시 가변의 문자열을 다룰 수 있어 속도 면에서 우수하다.

이 둘이 String과 다르게 가변 문자열을 다룰 수 있는 이유는 AbstractStringBuiler를 상속받아 구현된 클래스이기 때문이다. 아래의 내부 코드를 보면, AbstractStringBuiler는 String과 달리 문자열 값을 final 키워드가 없이 일반 byte 배열에 담고 있다.

![image](https://user-images.githubusercontent.com/13804810/185712590-10621c63-850e-446b-8146-3f43a13ebc32.png)

사실 StringBuilder와 StringBuffer 클래스가 제공하는 메서드는 같다. 두 클래스의 차이는 StringBuffer가 각 메서드에 synchronized 키워드가 붙여져 있어 동기화를 보장한다는 점에 있다. 그래서 성능 상으로 동기화를 지원하지 않는 StringBuilder가 더 빠르다.



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



# Chap 8. 예외 처리 (Exception Handling)
## 실행 흐름 제어용 예외의 강력함
throw 된 예외는 catch 될 때 까지 함수 호출 스택을 거슬러 올라갑니다.
따라서 throw와 catch 문법을 사용하면 호출 스택 어디로든 이동할 수 있습니다. if, for문을 사용해서 흐름을 제어하는 것보다 훨씬 강력하죠.(어쩌면 goto보다도 강력합니다)

예를 들어, 아래 재귀 함수는 결과를 순차적으로 return 하면서 탈출하는 전형적인 방식을 따르지 않았습니다. 대신 throw 문 안에 결과를 넣어서 return 없이 한 번에 탈출하고 있습니다.

```java
// 예시가 괜히 복잡하긴 한데 재귀 함수를 예시로 들어야 함수 호출 스택 어디로든 한 번에 이동할 수 있다는 걸 표현하기 쉬울 것 같았습니다  

void searchRecursive(TreeNode node, Object data ) throws ResultException {
    if(node.data.equals(data))
        throw new ResultException(node);
    } else {
        searchRecursive(node.leftChild, data );
        searchRecursive(node.rightChild, data );
    }
}  

// 출처: https://web.archive.org/web/20140430044213/http:/c2.com/cgi-bin/wiki?DontUseExceptionsForFlowControl
```

searchRecursive 함수를 호출한 함수는 `catch (ResultException e)`로 예외를 받고 있겠죠. 예외 안에 담긴 노드를 빼서 쓰면 복잡한 재귀 리턴 과정을 겪지 않고 쉽게 목표 노드를 찾을 수 있을 것 입니다. 재귀 함수 안에 catch문이 없기에 손쉽게 호출 함수까지 올라올 수 있었기 때문입니다.  

  얼핏 보면 굉장히 편하고 가독성도 좋은 방식 같습니다. 그렇지만 실제로는 이런 코드를 쓰지 않는 이유가 뭘까요?

---

## 예외를 진짜 예외 상황에만 사용해야하는 이유
몇 가지 이유를 찾았는데 스택 오버플로우와 이펙티바 자바에서 찾은 이유가 가장 좋았습니다. 두 답변의 내용이 겹치지 않아 둘 다 소개합니다.

### 1. stack overflow : "Why not use exceptions as regular flow of control?"
스택 오버 플로우에 위 질문이 있습니다. 가장 높은 점수를 받은 답변의 내용이 와 닿았습니다.

#### 1-1 ) 진짜 exception을 찾기 힘들어진다

![image](https://user-images.githubusercontent.com/76809524/187076881-85bda321-035b-412d-a9ae-a9440dfcc054.png)
- 출처 : https://stackoverflow.com/questions/729379/why-not-use-exceptions-as-regular-flow-of-control


요약하자면 일반적인 흐름에서 exception을 남발하면, 정말 exception 처리가 필요해서 한 곳을 찾기가 힘들다고 합니다. 실제 예외 처리 한 곳을 찾기 힘들어지면 가독성 문제가 생기겠죠.  


#### 1-2 ) 예외 디버깅이 힘들어진다(개인적인 의견)
답변을 본 다음에는 디버깅 문제도 생기겠다는 생각이 들었습니다. 인텔리제이는 예외 중단점 기능을 제공합니다. 예외가 발생한 곳에 자동으로 중단점을 생성해주어 예외 디버깅을 돕는 기능입니다. 그러나 위 경우 처럼 시작하자마자 예외가 200개 나오고, 이후로도 1초에 5개씩 던진다면 디버깅이 힘들어질 것입니다. 조건을 넣어서 훑는 건 여전히 가능하겠지만, 예외 전체를 훑는건 무의미해집니다. 편기한 툴 기능 하나를 잃는다는 점에서도 예외를 남발하면 안 좋습니다.

- 예외 디버깅 창
![image](https://user-images.githubusercontent.com/76809524/187079247-47e0528e-d855-4f6c-aa51-a274ed9e21bf.png)

  
### 2. Effective Java : Item 69 "예외는 진짜 예외 상황에만 사용하라" 에 제시된 이유
>예외는 （그 이름이 말해주듯） 오직 예외 상황에서만 써야 한다. 절대로 일상적인 제어 흐름용으로 쓰여선 안 된다.  
` Effective Java p 387 중단부`
  
이펙티브 자바 item 69에서는 예외를 절대 일상적인 흐름 제어용으로 사용해선 안 된다고 강조합니다.  

#### 예시
더불어 배열 원소를 순회하는 두 가지 코드를 통해 그 이유를 제시합니다.  


```java
// 책 코드에서 조금 수정
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
// 코드 2번
for (Index i : array)
    i.move();
//일반적인 코드
```

#### 1번 코드를 작성한 이유에 대한 추론
 JVM은 배열에 접근할 때마다 경계를 넘었는지 검사합니다. 반복문도 배열의 끝에 도달하면 종료합니다. 코드 2번 같이 일반적인 반복문 안에서 배열에 접근하면 검사가 중복되므로 하나의 검사를 생략하기 위해서 1번 처럼 작성했을 것 입니다. 그러나 이는 잘못된 생각입니다.


#### 1번 코드가 안 좋은 이유
1. 1번 코드는 직관성이 떨어집니다. 2번 코드는 전형적인 루프문으로 모든 프로그래머들이 보자마자 이해할 수 있는 반면, 1번은 그렇지 않습니다.  

   
2. for문을 통해 배열을 순회하는 코드는 JVM이 다양한 최적화를 수행해줍니다. 이 최적화를 통해 위에서 걱정한 중복 검사를 알아서 없애줍니다. 그러나 try-catch 블럭 안에 들어간 코드는 그렇지 않습니다. 예외는 예외 상황을 처리할 목적으로 만들어졌으므로 JVM 구현자가 성능 최적화에는 신경 쓰지 않았을 가능성이 높다고 합니다. 

   
3. **1번 코드는 디버깅을 훨씬 어렵게 합니다.** 위 반복문 안에 버그가 있다면 흐름 제어에 쓰인 catch문이 버그를 숨겨 디버깅을 훨씬 어렵게 할 것입니다. 만약 move() 메서드가 내부적으로 제 3의 배열을 사용하다가 `ArraylndexOutOfBoundsException`을 일으킨다면 어떻게 될까요? **2번 코드의 move()라면 이 버그는 catch문에 잡히지 않고 끝까지 올라가서 프로그램을 종료시킬 것입니다. 함수 호출 스택을 보고 즉시 어디에서 버그가 났는지 알 수 있겠죠. 그러나 1번에서는 버그 때문에 발생한 예외를 정상적인 프로그램 실행 흐름으로 오해하고 그냥 넘어갑니다.** 이런 경우에 대한 디버깅은 훨씬 어렵습니다.


---

## RuntimeException과 RE가 아닌 예외들의 차이점
## Throwable 계층 구조
Exception은 Runtime계열이든 아니든 Throwable을 상속 받고 있습니다. 예외에 대해 알아보기 전에 Throwable의 계층 구조를 살펴보겠습니다.
![image](https://user-images.githubusercontent.com/76809524/188663636-bb60e0b3-2786-401d-a219-a49947db77e6.png)
(출처: https://madplay.github.io/post/java-checked-unchecked-exceptions)

Throwable 클래스는 Exception과 Error 클래스의 부모입니다. 자바에서 예외나 오류를 Throw할 수 있는 이유는 이들이 Throwable 클래스를 상속 받고 있기 때문입니다.

### Throwable 자식들의 3분류
자바에서는 Throwable의 자식 클래스를 크게 3분류로 나누어보는 것이 일반적입니다. Error계열, Exception계열, RuntimeException 계열입니다. RuntimeException은 Exception을 상속 받았기 때문에 Exception과 비슷한 것이라고 생각할 수도 있습니다. 물론 비슷한 점도 있으나 RuntimeException은 자신의 부모 클래스인 Exception과 달리 Unchecked Exception이라는 결정적인 차이점이 있습니다. 따라서 RE와 RE가 아닌 예외를 나누어서 생각하는 것이 일반적입니다.

### 각 분류의 특징
(일반적인 범주를 말하는 것이며 예외적인 클래스도 많음. 예를 들어 Exception 클래스를 상속 받았지만 예외 처리를 할 수 없는 문제도 있다.)
1. **Error 클래스를 상속 받은 분류** : 실행을 계속할 수 없을 정도의 심각한 오류가 일어났음을 알립니다. (ex. 하드웨어, JVM 오류 등)
2. **Exception 클래스를 상속** : 미리 예외 처리가 가능한 문제. 예외 처리를 함으로서 문제 상황을 수복할 수 있는 예외를 일으킵니다. - 컴파일러 수준에서 예외 처리가 강제된다는 특징이 있습니다. 예외의 가능성이 있는데 catch하거나 throws 문을 작성하지 않았을 경우 컴파일 오류를 줍니다. 
3. **RunTime Exception 클래스를 상속** : 예외 처리 보다는 코드 수정으로 고쳐야 하는 문제 or 코드 수정으로도 고칠 수 없어 프로그램을 종료시켜야 하는 문제 등이 일어났을 때 예외를 일으킵니다.

위 분류 중 Exception과 RunTimeException의 용도가 달라진 이유는 Exception 계열은 Checked Exception이며 RunTimeException 계열은 Unchecked Exception이기 때문입니다.  
다시 말하지만 일반적인 특징 및 용도를 나타낸 것일 뿐 기본 계공되는 예외들이 무조건 위 설명을 따르는 것은 아닙니다. 커스텀 예외를 만들어서 사용할 때도 예외 처리를 강제하고 싶은 예외일지라도 RunTimeException을 상속 받도록 만들 수도 있습니다. 

### RuntimeException, Exception 차이점
RE와 RE가 아닌 예외의 차이점은 예외처리를 제대로 하고 있는지 컴파일러가 체크해주는지 여부에 있습니다.

#### RE인 예외 == UnChecked 예외
RE의 경우 예외를 발생시키기만 하고 처리해주지 않아도 컴파일러가 오류를 내지 않습니다.  

(컴파일러가 오류 체크하지 않는 모습)
![image](https://user-images.githubusercontent.com/76809524/188786832-db5c05f7-07e5-4134-ac94-dcbf449d7e77.png)  

(실행 후 런타임 오류로 잡아야 함)
![image](https://user-images.githubusercontent.com/76809524/188786841-f888132b-757a-4179-8fb3-ae0ccdc8d4e0.png)  


#### RE가 아닌 예외 == Checked 예외
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


