# item 10 equals는 일반 규약을 지켜 재정의하라

## equals를 재정의 하지 않아야 할 경우

1. 각 인스턴스가 본질적으로 고유할 떄
2. 인스턴스의 논리적 동치성을 검사할 일이 없을 때
3. 상위 클래스에서 정의한 equals가 하위 클래스에 사용해도 문제가 없을 때
4. 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없을 때

## 재정의 해야할 경우

- 논리적 동치성을 확인해야 되는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의 되어 있지 않을 경우
    - 주로 값 클래스들이 해당됨 (객체 동등성이 아니라 값 자체가 같은지 확인하고 싶은 경우)
    - 값 클래스라 해도 값이 같은 객체가 없이 유니크한 객체만 있다면 재정의 될 필요가 없음

      ⇒ 논리적 동치성과 객체 동등성이 같아지는 경우이기 때문에 재정의 필요 없음


<aside>
💡 equals 메서드를 재정의할 때는 반드시 일반 규약을 따라한다.

</aside>

## Object 명세서의 동치 관계

- 집합을 서로 같은 원소들로 이뤄진 부분 집합으로 나누는 연산
    - 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 함

### 반사성

객체는 자기 자신과 같아야 한다

### 대칭성

두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다.

### 추이성

첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면 첫 번째 객체와 세 번째 객체는 같아야 한다.
#### LSP 리스코프 치환 원칙 (Liskov substitution principle)

#### 2차원에서 점을 표현하는 클래스
```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;
        Point p = (Point) o;
        return p.x = x && p.y = y;
    }
// 나머지 코드는 생략
}
```

#### 색상이 추가된 Point 클래스 확장
```java
public class ColorPoint extends Point {
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }
// 나머지 코드는 생략
}
```

#### 대칭성을 위배한 equals 메서드
```java
@Override
public boolean equals(Object o) {
    if (!(o instanceof ColorPoint)) return false;
    return super.equals(o) && ((ColorPoint) o).color = color;
}
```
- Point의  equals는 색상을 무시하고 ColorPoint의 equals는 매개변수의 클래스 종류가 다르기 때문에 항상 false를 반환함
```java
Point p = new Point(1, 2);
ColorPoint cp = new ColorPoint(1, 2, Color.RED);
```
- p.equals(cp)는 true를 반환하지만 cp.equals(p)는 false를 반환함

#### 추이성을 위배한 equals 메서드
```java
@Override
public boolean equals(Object o) {
    if (!(o instanceof Point)) return false;

    // o가 일반 Point면 색상을 무시하고 비교
    if (!(o instanceof ColorPoint)) return o.equals(this);

    // o가 ColorPoint면 색상까지 비교
    return super.equals(o) && ((ColorPoint) o).color = color;
}
```
- 위 코드는 대칭성은 지켜주지만 추이성을 깨버림
```java
ColorPoint pl = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2);
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE);
```
- pl.equals(p2)는 true를 반환하고 p2.equals(p3)는 true를 반환하지만 pl.equals(p3)는 false를 반환함
- 재귀에 빠질 위험도 있음  

💡 **구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다**

#### 리스코프 치환 원칙 위배
```java
@Override 
public boolean equals(Object o) {
  if (o = null 11 o.getClass() != getClassO) 
      return false;
  Point p = (Point) o;
  return p.x = x && p.y = y; 
}
```

Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로 활용되어야 하는데 그러지 못하다.

#### equals 규약을 지키면서 값 추가
```java
public class ColorPoint {
    private final Point point;
    private final Color color;

    public ColorPoint(int x, int yf Color color) {
        point = new Point(x, y);
        this.color = Objects.requireNonNulKcolor);
    }

    /**
     * 이 ColorPoint의 Point 뷰를 반환한다.
     */
    public Point asPoint() {
        return point;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof ColorPoint)) return false;
        ColorPoint cp = (ColorPoint) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }
// 나머지 코드는 생략 
}
```

#### 정리
- 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀
  수 있어야 한다
- 다형성에서 하위 클래스는 **인터페이스 규약을 다 지켜야 한다는 것**, 다형성을 지원하기 위
  한 원칙, 인터페이스를 구현한 구현체는 믿고 사용하려면, 이 원칙이 필요하다.
- 상속을 사용하면 추이성을 만족하지 못하는 경우가 발생할 수 있다. 따라서 
  - 추이성을 해결 하기 위해 상속이 아니라 컴포지션 관계를 이용하는 방법이 있다.



### 일관성

두 객체가 같고 수정되지 않았더라면 영원히 같아야 한다.

항시 메모리에 존재하는 객체만을 사용한 결정적 계싼만 수행해야 한다.


### null 아님

모든 객체가 null과 같지 않아야 한다.

instanceof 연산자로 null이면 false를 반환하게 하여 묵시적으로 해결

## 올바르게 equals 메서드를 구현하는 방법

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인
2. instanceof 연산자로 입력이 올바른 타입인지 확인
3. 입력을 올바른 타입으로 형변환
4. 입력 객체와 자기 자신의 대응되는 ‘핵심’ 필드들이 모두 일치하는지 하나씩 검사
- float와 double을 제외한 기본 타입 필드는 == 연산자로 비교
- 참조 타입 필드는 각각 정의된 equals 메서드로 비교
- float와 double은 각각 Float.compare(), Double.compare() 로 비교
    - 부동소수값 때문에

<aside>
💡 어떤 필드를 먼저 비교하느냐에 따라 equals의 성능을 좌우하기도 한다.
⇒ 가장 다를 가능성이 크거나 비교하는 비용이 싼 필드를 먼저 비교

</aside>

- 대칭적, 추이성, 일관적인지 확인
- equals를 재정의할 땐 hashCode도 반드시 재정의
- 너무 복잡하게 해결하러 하지 말자
    - 필드들의 동치성만 검사해도 equals 규약을 지킬 수 있다.
    - alias는 비교하지 않는 것이 좋다.
- Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자.