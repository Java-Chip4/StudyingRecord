# ITEM 68 : 일반적으로 통용되는 명명 규칙을 따르라
자바는 명명 규칙이 잘 정립되어 있으며, [Java Specification의 Names 챕터](https://docs.oracle.com/javase/specs/jls/se11/html/jls-6.html) 에서 확인할 수 있다.

- 철자 규칙이나 문법 규칙을 어기면, 다른 프로그래머들이 그 코드를 읽기 번거로울 뿐 아니라 다른 뜻으로 오해할 수도 있고 그로 인해 오류까지 발생할 수 있다.


- 약자의 경우 첫 글자만 대문자로 할지 전체를 대문자로 할지는 살짝 논란이 있지만, 첫 글자만 대문자로 하는 쪽이 훨씬 많다. 여러 약자가 혼합된 경우라도 각 약자의 시작과 끝을 명확히 알 수 있기 때문이다.  
  : `HTTPURL`과 `HttpUrl`을 비교해보자. 후자가 가독성이 더 좋다.


- 타입 매개변수 명명 규칙  
  : 보통 한 문자로 표현한다. 다음은 주로 사용되는 예시이다.

|임의 타입|컬렉션 원소 타입|맵의 키와 값|예외|메서드 반환 타입|
|:------:|:---:|:---:|:---:|:---:|
|    T   |  E  | K, V|  X  |  R  |


- 메서드 명명 규칙
    - 어떤 동작을 수행하는 메서드
        - **동사**나 **목적어를 포함한 동사구** 형태 사용
        - 예시 : `append()`, `drawImage()`

    - boolean 값을 반환하는 메서드
        - **is**나 드물게 **has** 형태 사용
        - 예시 : `isDigit()`, `isProbablePrime()`, `isEmpty()`, `isEnabled()`, `hasSiblings()`

    - 반환 타입이 boolean이 아니거나 해당 인스턴스의 속성을 반환하는 메서드
        - **명사**나 **명사구**, 혹은 **get**으로 시작하는 동사구 형태 사용
        - 예시 : `size()`, `hashcode()`, `getTime()`
        - ❗️: get으로 시작하는 형태만 써야 한다는 주장도 있으나, get을 사용하지 않는 형태가 가독성이 더 좋은 경우도 있다.
      ```
      if (car.speedO > 2 * SPEED_LIMIT)
          generateAudibleAlert("경찰 조심하세요!");
      ```
    - 객체의 타입을 바꿔서, 다른 타입의 또 다른 객체를 반환하는 인스턴스 메서드
        - **toType** 형태 사용
        - 예시 : `toString()`, `toArray()`

    - 객체의 내용을 다른 뷰로 보여 주는 메서드
        - **asType** 형태 사용
        - 예시 : `asList()`

    - 객체의 값을 기본 타입 값으로 반환하는 메서드
        - **typeValue** 형태 사용
        - 예시 : `intValue()`

    - 정적 팩터리의 이름
        - `from()`, `of()`, `valueOf()`, `instance()`, `getlnstance()`, `newlnstance()`, `getType()`, `newType()`


- **핵심 정리**   
  : 표준 명명 규칙을 체화하여 자연스럽게 베어 나오도록 하자. 철자 규칙은 직관적이라 모호한 부분이 적은 데 반해, 문법 규칙은 더 복잡하고 느슨하다. [Java Specification](https://docs.oracle.com/javase/specs/jls/se11/html/jls-6.html) 의 말을 인용하자면 **_“오랫동안 따라온 규칙과 충돌한다면 그 규칙을 맹종해서는 안 된 다.”_** 상식이 이끄는 대로 따르자.
