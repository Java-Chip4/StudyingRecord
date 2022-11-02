# ITEM 2. 생성자에 매개변수가 많다면 빌더를 고려하라
***
정적 팩터리와 생성자에는 제약이 있다. => **선택적 매개변수가 많을 때 적절한 대응이 어렵다는 것**

```java
public class User{
    private final int id;
    private final String name;
    private final String nickName;
    private final String passWord;
    private final int weight ;
    private final int height;
}
```
책에는 식품 영양정보로 예시를 들었지만,웹 사이트의 유저에 관한 예시를 들어보겠다.
유저에 담을 정보는 굉장히 많고 또 키나 몸무게 같은 정보는 유저가 optional로 줄 것이다.

builder 이전의 선택적으로 많은 매개변수를 가지는 클래스의 생성패턴은 어떻게 해결했을까.
1. **점층적 생성자 패턴(telescoping constructor pattern)**
```java
public class User{
    private final int id;
    private final String name;
    private final String nickName;
    private final String passWord;
    private final int weight ;
    private final int height;
    
    public User(int id, String name,String passWord){
        this(id,name,'',passWord,0,0);
    }
    public User(int id, String name,String nickName, String passWord) {
        this(id, name, nickName, passWord, 0, 0);
    }
    public User(int id, String name,String nickName, String passWord,int weight, int height){
        this.id = id;
        this.name = name;
        this.nickName = nickName;
        this.passWord = passWord;
        this.weight = weight;
        this.height = height;    
    }
}
```

이중에서 가장 짧은 것을 골라 호출하면 된다.

``User user = new User(1,강승원,123456789)``

이 점층적 패턴의 단점은, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

2. **자바빈즈(JavaBeans patterns)패턴**
```java
public class User{
    private final int id = 0;
    private final String name = '';
    private final String nickName = '';
    private final String passWord = '';
    private final int weight = 0;
    private final int height = 0;
    
    public User(){}
    public void setId(int val){id=val;}
    public void setName(String val){name=val;}
    public void setNickName(String val){nickName=val;}
    public void setPassWord(String val){passWord=val;}
    public void setWeight(int val){weight=val;}
    public void setHeight(int val){height=val;}
}
    User user = new User();
    user.setName('승원');
    user.setNickName('ㄱ아');
    user.setId(1);
    user.setPassword('dqjca57874wqdq');    
```
점층적 생성자패턴에서의 단점은 줄었지만,
객체 하나를 만드려면 메서들을 여러개 호출해야 하고, 객체가 완전히 생성되기 전까진 **일관성**이 무너지게 된다.

일관성이 무너지면 버그가 생기기 심고 디버깅이 만만치 않다

자바빈즈 패턴에서는 클래스를 불변으로 만들수 없고 스레드 안정성을 얻으려면 프로그래머가 추가 작업을 해줘야한다.(item 17)

### 그래서 빌더를 쓴다.

```java
public class User {
    private final int id;
    private final String name;
    private final String nickName;
    private final String passWord;
    private final int weight;
    private final int height;

    public static class Builder {
        //        필수 매개변수
        private final int id;
        private final String name;
        private final String passWord;
        //        선택 매개변수 - 기본값으로 초기화
        private final String nickName = '';
        private final int weight = 0;
        private final int height = 0;

        public Builder(int id, String name, String passWord) {
            this.id = id;
            this.name = name;
            this.passWord = passWord;
        }

        public Builder nickName(String val) {
            nickName = nickName;
            return this;
        }

        public Builder weight(int val) {
            weight = val;
            return this;
        }

        public Builder height(int val) {
            height = val;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }

    private User(Builder builder) {
        id = builder.id;
        name = builder.name;
        nickName = builder.nickName;
        passWord = builder.passWord;
        weight = builder.weight;
        height = builder.height;
    }
}
    User user = new Builder(1, '강승원', 'qwdjsqq2w213')
            .height(170).weight(895).nickName('승원').build()
```
점층적 생성자 패턴과 자바빈 패턴의 장점들만 모아서 일관성을 유지하면서 가독성이 증가하는 것을 볼 수있다.

이 빌더 패턴은 파이썬과 스칼라의 명명된 **선택적 매개변수**를 흉내낸 것이라고 한다.

이 패턴은 계층적으로 설계된 클래스와 함께 쓰기 좋다.

```java
//예제 찾는중
```

상위 클래스의 빌더를 하위 클래스의 빌더가 상속받아서 추가로 더 받을 데이터들을 확장하는 방식으로 사용할 수 있다.

### 단점
- 코드가 장황해서 매개변수가 4개 이상은 되야 값을 한다.
- 성능에 민감한 상황에서는 문제가 될 수 있다.