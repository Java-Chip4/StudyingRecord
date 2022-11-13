# ITEM 7. 다 쓴 객체 참조를 해제하라

**자바에는 가비지컬렉터가 있다.**

#### Garbage란?
> 주소를 잃어버려서 사용할 수 없는 메모리(정리되지 않은 메모리), 앞으로 사용하지 않고 메모리를 가지고 있는 객체.

```java
int[] array = new int[2];
array[0] = 1;
array[1] = 2;
array  =new String[] {'G','C'}
```
처음에 할당 되었던 '0'과 '1'은 어떻게 됐을까?

#### 가비지컬렉터란?
> 사용하지 않는 메모리 즉, Garbage의 해제해주는 역할을 해주는 프로그램을 말한다. 

#### GC의 동작 방식
1. Stop The World
2. Mark and Sweep

#### Stop The World
가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행이 멈춤
GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중간
GC가 완료되면 작업이 재개된다. 다른 쓰레드들을 멈추기 때문에 성능에 영향을 미치고
GC의 성능 개선을 위해 튜닝을 한다면, 보통 이 Stop The World의 시간을 줄이는 작업을 한다.
jvm에서도 이러한 문제를 해결하기 위해 다양한 실행 옵션을 제공한다.

#### Mark and Sweap
- Mark: 사용되는 메모리와 사용되지 않는 메모리 식별하는 작업
- Sweep: Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업

Stop The World 후 =>  GC는 스택의 모든 변수 또는 Reachable 객체를 스캔 => 사용되고 있는 메모리를 식별(Mark)=> Mark가 되지 않은 객체들을 메모리에서 제거

#### 동작방식


### 자바의 메모리 누수 예제(인터넷에서 찾아봄)
1. Integer, Long 같은 래퍼 클래스(Wrapper)를 이용하여, 무의미한 객체를 생성하는 경우
2. 맵에 캐쉬 데이터를 선언하고 해제하지 않는 경우
3. 스트림 객체를 사용하고 닫지 않는 경우
4. 맵의 키를 사용자 객체로 정의하면서 equals(), hashcode()를 재정의 하지 않아서 같은 키로 착각하여 데이터가 계속 쌓이게 되는 경우
5. 맵의 키를 사용자 객체로 정의하면서 equals(), hashcode()를 재정의 하였지만, 키값이 불변(Immutable) 데이터가 아니라서 데이터 비교시 계속 변하게 되는 경우
자료구조를 생성하여 사용하면서, 구현 오류로 인해 메모리를 해제하지 않는 경우


### Effective 자바
1. 사용하지 않는 객체의 참조를 해제하지 않는 경우
2. 캐시에 참조키를 저장하고 잊어먹는 경우
3. 클라이언트가 리스너 혹은 콜백에서 등록만 하고 해지하지 않는 경우 


```java
public class Stack{
    private Object[] elements;
    private int size =0;
    private static final int DEFAULT_INITIAL_CAPACITY=16;
    
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY]
    }
    
    public void push(Object e){
        ensureCapacity();
        elements[size++] =e
    }
    public Object pop(){
        if(size ==0)
            throw new EmptyStackException();
        return elements[--size];
    }
    private void ensureCapacity(){
        if(elements.length == size){
            elements = Arrays.copyOf(elements,2*size+1);
        }
    }
}
```
이 코드에서 메모리 누스는 어디서 일어날까?
스택이 커졌다가 줄어들었을 떄 스택에서 꺼내진 객체들은 가비지 컬렉터가 회수하지 않는다.

스택이 그 객체들의 다 쓴 참조를 여전히 가지고 있기 때문이다.
가비지 컬렉터는 객체 참조 하나를 살려두면 그 객체뿐 아니라 그 객체가 참조하는 모든 객체를 
회수하지 못한다.
해법은 참조를 다썼을 때 null처리(참조 해제) 해주면 된다.

```java
public Object pop(){
      if(size == 0){
          throw new EmptyStackException();
        }
      Object result = elements[--size];
    elements[size] = null;
    return result;
}
```
null처리를 하면 실수로 이 참조를 접근할때, NullPointerException을 던지며 종료된다.

위와 같은 객체 자체가 아니라 객체의 참조를 관리하는 class는 메모리누수를 주의해야한다.

#### 캐시 역시 메모리 누수를 일으키는 주범이다.
캐시에 객체 참조를 나두고 잊어먹는 경우가 많다.
WeakHashMap을 사용해 캐시를 만들자
단, WeakHashMap은 이러한 상황에서만 유용하다.

캐시 엔트리의 유효기간을 정확히 정의하기 어렵다.
엔트리를 이따금 청소해야하는데, ScheduledThreadPoolExcuter와 같은 백그라운드 스레드를 활용하거나
캣ㅣ에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법이 있다.

복잡한 캐시를 만들고 싶다면 java.lang.ref 패키지를 직접 활용해야 할 것이다.

리스너 혹은 콜백이 쌓이면서 생기는 메모리 누수는 약한 참조로 저장하면 GC가 수거해간다.
예를 들어 WeakHashMap에 키로 저장하면 된다.


> 메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수십년간 잠복하는 사례또 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다

WeakHashMap 예제
```java
public class WeakHashMapTest {
    public static void main(String[] args) {
        WeakHashMap<Integer, String> map = new WeakHashMap<>();
        Integer key1 = 1000;
        Integer key2 = 2000;
        map.put(key1, "test a");
        map.put(key2, "test b");
        key1 = null;
        System.gc();  //강제 Garbage Collection
        map.entrySet().stream().forEach(el -> System.out.println(el));
    }
}
```