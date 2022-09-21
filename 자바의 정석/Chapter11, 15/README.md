# Chap 11. 컬렉션 프레임워크
## 컬렉션 프레임워크의 핵심 인터페이스
- List
  - 순서가 있는 데이터 집합, 데이터 중복 O
  - ArrayList, LinkedList, Stack, Vector ...
- Set
  - 순서를 유지하지 않는 데이터 집합, 데이터 중복 X
  - HashSet, TreeSet ...
- Map
  - Key와 Value로 이뤄진 pair의 데이터 집합. 순서 유지 X, Key 중복 X, Value 중복 O
  - HashMap, TreeMap, Hashtable, Properties ...
- ❗️주의 : Vector, Hashtable은 하위 호환성을 위해 남겨진 클래스이므로, 대신 ArrayList, HashMap을 사용하자
  - Github Issue : [레거시 컬렉션 프레임워크인 Vector, Hashtable을 왜 쓰면 안될까?](https://github.com/Java-Chip4/StudyingRecord/issues/33)


## ArrayList
- ArrayList를 생성할 때, 실제 저장할 개수보다 약간 여유롭게 설정하자
  - 초기 사이즈를 넘어가면, 동적으로 크기를 키우지만, 비용이 크기 때문이다.
  - 만약 사이즈를 주지 않으면❓
    - Ex : `ArrayList<Integer> list = new ArrayList<>();`
    - Answer : `private static final int DEFAULT_CAPACITY = 10;`
  - 사이즈는 동적으로 얼마나 커지는가❓
    - 50%씩 증가한다.
    - Ex : 기본값이라면, 10 -> 15 -> 22 -> 33 ... 
  - 어떻게 사이즈가 증가하게 되는가❓
    1. `ensureCapacity()` 가 호출이 되고, 이는 다시 `grow()`를 호출한다.
    2. `grow()`는 새로운 크기의 새로운 array를 생성한다.
       - 그 이유는 ArrayList가 data를 `transient Object[] elementData;`로 저장하기 때문이다.
    3. 새롭게 생성한 array를 기존의 array로 붙여넣는다.
    4. 새로운 array를 return 한다.
    

## ArrayList vs LinkedList
Collection Framework를 잘 아는 것도 중요하지만, 더 중요한 것은 나의 데이터를 어떤 collection에 담은 것인지를 결정할 수 있는 것이 더 중요하다고 생각된다.

만약, 나의 데이터가 삽입, 삭제와 같은 수정보다는 **조회**가 더 자주 이뤄지는 데이터라면, ArrayList를 사용하는 것이 옳다.  
- ArrayList의 remove()에 대한 time complexity : **O(N)**  
- ArrayList의 get()에 대한 time complexity : **O(1)**  
  
이기 때문이다.


반대로, 수정이 더 자주 이뤄지는 데이터라면, LinkedList를 사용해야 한다.
- LinkedList의 remove()에 대한 time complexity : **O(1)**
- LinkedList의 get()에 대한 time complexity : **O(N)**  


참고  
https://www.cs.cmu.edu/~mrmiller/15-121/Slides/09-BigO-ArrayList.pdf  
https://www.baeldung.com/java-collections-complexity

## PriorityQueue, Deque
- PriorityQueue
  - Queue 인터페이스의 구현체로, 저장한 순서에 상관없이 priority가 높은 순서부터 꺼내게 된다.
  - priority에 따른 순서 구현을 위해 Min-Heap을 구현한 형태이다.
  - null 저장시 NPE이 발생한다.
  - Max-Heap 구조로는 구현할 수 없을까❓
    - CASE 1: Comparator 생성
      ``` java
      public class customComparator implements Comparator<Integer> {
          public int compare( Integer x, Integer y ) {
            return y - x;
          }
      }
    
      PriorityQueue minHeap = new PriorityQueue();
      PriorityQueue maxHeap = new PriorityQueue(size, new customComparator());
      ```
    - CASE 2: Collection.reverseOrder() 사용
      ```
      PriorityQueue<Integer> queue = new PriorityQueue<>(10, Collections.reverseOrder());
      ```   

- Deque
  - Queue의 변형으로, 양 끝에서 추가 / 삭제가 가능한 구조이다.
  - 이런 구조를 언제 사용할 수 있을까❓
    - [영화관 티켓 구매 라인](https://stackoverflow.com/questions/3880254/why-do-we-need-deque-data-structures-in-the-real-world) 
      - 티켓 라인은 마치 큐처럼 작동하지만, 때로 누군가가 티켓을 구매한 후, 무언가를 묻기 위해 갑자기 줄 앞으로 다시 돌아온다.
        그들은 이미 티켓을 구입했기 때문에 추가 질문을 요청할 수 있는 권한이 있다.
        따라서 이런 시나리오에서는 요구사항에 따라 데이터를 제일 앞에 추가할 수 있는 데이터 구조가 필요하다.
        또한 사용자는 뒤쪽에서 대기열을 떠날 수 있다.


## HashSet, TreeSet
- HashSet
  - HashSet은 순서를 보장하지 않지만, LinkedHashSet은 순서를 보장한다.
    - HashSet은 hashing을 사용하기 때문이다.
  - Github Issue : [서로 다룬 두 객체를 같은 객체로 인식하기 위한 방법](https://github.com/Java-Chip4/StudyingRecord/issues/20)
  - hashCode()가 잘 동작하기 위한 조건
    - Application 실행중 객체의 값이 바뀌지 않았다면, hashCode()의 값은 항상 같아야 한다
    - equals()로 비교하여 true인 두 객체의 hashCode() 값은 항상 같아야 한다
    - equals()로 비교하여 false인 두 객체의 hashCode() 값은 같아도 괜찮지만, hashing 검색 성능을 위해 다른 것이 좋다


- TreeSet
  - Set 인터페이스 구현체이므로 중복 X, 순서 보장 X
  - binary search tree(Red-Black tree)로 구현되어 있어 검색 효율이 뛰어나다
    - 그래서 가장 왼쪽의 leaf node에는 최소값, 가장 오른쪽의 leaf node에는 최대값이 저장된다
  - Tree이기에 검색 / 정렬 성능은 뛰어나지만, 추가 / 삭제 성능은 떨어진다
    - 왜 ❓ : 순차적 저장이 아니기에 저장 위치 탐색과 tree 재구성이 필요하기 때문이다
  - 알파벳을 저장할 경우, 대 / 소문자 구분이 이뤄진다
    - ❗️: 기본적으로 대문자 > 소문자이므로, 하나로 통일하여 저장하거나, Comparator를 이용하자
      - ex) "dzzzz" < "dance"


## HashMap
HashMap의 내부 코드를 보면 데이터를 아래와 같이 저장하고 있다.
```
transient Node<K,V>[] table;
```
`transient` 키워드는 serialize 하는 과정에서 데이터를 제외하고 싶은 경우 선언된다. 예를 들어, 로그인 데이터와 같은 보안적인 내용이나 불필요한 데이터일 경우 사용할 수 있다. (deserialize 해보면, 해당 transient field에는 null 값이 들어있다.)

- 그런데 HashMap에서는 왜 table에 transient 키워드를 적용시킬까❓ table을 알아야 빠르게 검색할 수 있는 게 아닐까?
  - https://github.com/Java-Chip4/StudyingRecord/issues/34


- 추가적으로 `transient` 키워드에 대해 검색하다가 멋진 내용을 보았다.
  - transient는 VM마다 다르게 구현이 되어 있어서 JVM이 아닌 다른 VM에서는 설계대로 동작하지 않을 수 있다고 한다. 
  - transient의 본질적 의미는 "이 field는 메모리 안에서만 사용되어야 한다" 라고 한다. 
  - 그래서 이를 기억하고 설계하는 것이 중요하다고 한다.


- Hashing
  - 각각의 배열에 LinkedList가 저장된 형태로 구성되어 있다
  - 그래서 각각의 LinkedList에 최소한의 값만 저장되도록 hash code를 구성하는 게 성능에 좋다
    - 한 LinkedList에 너무 많은 값이 저장되면, 탐색하는데 비용이 많이 들기 때문이다.


- HashMap의 동작에 대해 다룬 Naver D2 article
  - https://d2.naver.com/helloworld/831311
