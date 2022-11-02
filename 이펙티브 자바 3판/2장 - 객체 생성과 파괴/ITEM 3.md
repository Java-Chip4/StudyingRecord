# ITEM 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
싱글턴에 대한 이해가 부족한 상태에서 이 아이템을 읽는 것은 무의미하다고 생각한다.  

만약 싱글턴이 헷갈린다면, 아래의 링크를 통해 싱글턴을 배워보자.

[❓ 싱글턴에 대해 알아보기 ❓](https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/)

---

### **싱글턴 클래스를 만들면, 이를 사용하는 클라이언트를 테스트하기 어려워질 수 있다.**
- 그 이유는 무엇일까❓
  - 싱글턴 인스턴스를 mock 구현으로 대체할 수 없기 때문이다.
    - 다만, 타입을 인터페이스로 정의하여 해당 인터페이스 구현체를 싱글턴으로 만든 경우라면 괜찮다.
  - 그 외에도 너무 많은 일을 하거나, 너무 많은 데이터가 공유되면, 다른 클래스와 결합도가 높아져 OCP 원칙이 위배될 수 있다. 그러면 유지보수도, 테스트도 어려워진다.

- 그럼 싱글턴 클래스를 사용하지 말아야 할까 ❓
  - 아니다. 공통된 객체를 여러 개 생성해서 사용해야 하는 상황에서 싱글턴을 통해 비용을 크게 줄일 수 있다.

---

## 싱글톤 생성하기
### 1. 생성자를 닫고, `public static final`한 필드로 두기
``` java
public class Settings {
    private static final Settings INSTANCE = new Settings();
    
    private Settings() { }
}
```
- 장점 
  1. 클래스가 싱글턴임이 API에 명백히 드러난다
  2. 간결하다
- 단점
  1. Reflection API를 통해 생성자 강제 호출 가능
     ``` java
     public void reflection_breaks_singleton() throws Exception {
         // given
         Settings settings1 = Settings.INSTANCE;
   
         // when
         Constructor<Settings> constructor = Settings.class.getDeclaredConstructor();
         constructor.setAccessible(true);
         Settings settings2 = constructor.newInstance();
   
         // then
         assertThat(settings1).isNotEqualTo(settings2);
     }
     ```
  2. readResolve()가 없다면, 직렬화시 싱글톤 깨짐
     ``` java
     public void given_SettingsHasNoReadResolve_serializable_breaks_singleton() throws Exception {
         // given
         SettingsNoReadResolve settings1 = SettingsNoReadResolve.getInstance();
         SettingsNoReadResolve settings2 = null;
     
         // when
         try (
             ObjectOutput out = new ObjectOutputStream(new FileOutputStream("settings.obj"))) {
             out.writeObject(settings1);
         }
     
         try (
             ObjectInput in = new ObjectInputStream(new FileInputStream("settings.obj"))){
             settings2 = (SettingsNoReadResolve) in.readObject();
         }
     
         // then
         assertThat(settings1).isNotEqualTo(settings2);
     }
     ```
     직렬화 시에도 싱글톤을 유지하고 싶다면, readResolve()를 구현해야 한다.
     ``` java
     public class Settings {
         private static final Settings INSTANCE = new Settings();
         
         private Settings() { }
         
         protected Object readResolve() {
             return INSTANCE;
         }
     }
     ```
     ``` java
     public void given_SettingsHasReadResolve_serializable_sustain_singleton() throws Exception {
         // given
         Settings settings1 = Settings.getInstance();
         Settings settings2 = null;
     
         // when
         try (
             ObjectOutput out = new ObjectOutputStream(new FileOutputStream("settings.obj"))) {
             out.writeObject(settings1);
         }
     
         try (
             ObjectInput in = new ObjectInputStream(new FileInputStream("settings.obj"))){
             settings2 = (Settings) in.readObject();
         }
     
         // then
         assertThat(settings1).isEqualTo(settings2);
     }
     ```  


### 2. 정적 팩터리 방식
``` java
public class Settings {
    private static final Settings INSTANCE = new Settings();
    
    private Settings() { }

    public static Settings getInstance() {
        return INSTANCE;
    }
}
```

- 장점
  1. API를 변경하지 않고, 싱글턴 패턴을 제거할 수 있다
     ``` java
     public class Settings {
         private static final Settings INSTANCE = new Settings();
         
         private Settings() { }
     
         public static Settings getInstance() {
             return new Settings();
         }
     }
     ```
  2. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다
     ``` java
     public class GenericFactoryMethod {
         public static final Set EMPTY_SET = new HashSet();
     
         public static final <T> Set<T> emptySet() {
             return (Set<T>) EMPTY_SET;
         }
     }

     public void genericTest() {
         Set<String> set = GenericFactoryMethod.emptySet();
         Set<Integer> set2 = GenericFactoryMethod.emptySet();
         Set<Elvis> set3 = GenericFactoryMethod.emptySet();
     
         set.add("ab");
         set2.add(123);
         set3.add(Elvis.INSTANCE);
     }
     ```
     (코드 참고 : https://jake-seo-dev.tistory.com/13)
  3. 정적 팩터리의 메서드 참조를 `Supplier`로 사용할 수 있다.
     ``` java
     Supplier<Elvis> elvisSupplier = Elvis::getInstance;
     Elvis elvis = elvisSupplier.get();
     ```
- 단점
  1. Reflection API를 통해 생성자 강제 호출 가능
      ``` java
      public void reflection_breaks_singleton() throws Exception {
          // given
          Settings settings1 = Settings.getInstance();
    
          // when
          Constructor<Settings> constructor = Settings.class.getDeclaredConstructor();
          constructor.setAccessible(true);
          Settings settings2 = constructor.newInstance();
    
          // then
          assertThat(settings1).isNotEqualTo(settings2);
      }
      ```
  2. readResolve()가 없다면, 직렬화시 싱글톤 깨짐
     : 이에 관련된 내용은 ``1. 생성자를 닫고, `public static final`한 필드로 두기`` 의 단점 2번에서 다룬 내용과 같다.

### 3. 열거 타입의 싱글턴 (추천 ⭐️)
``` java
public enum EnumSettings {
    INSTANCE;

    private EnumSettings() { }
}
```

- 장점
  1. 직렬화 시에도 추가 구현 없이 싱글턴 유지 가능
     - 기본적으로 Enum 클래스를 상속받고 있고, 해당 클래스가 이미 Serialization을 상속하므로 안전한 직렬화 / 역직렬화가 가능하다
       ``` java
       public void enum_serializable_keep_singleton() throws Exception {
         // given
         EnumSettings settings1 = EnumSettings.INSTANCE;
         EnumSettings settings2 = null;
         
         // when
         try (
           ObjectOutput out = new ObjectOutputStream(new FileOutputStream("enum_settings.obj"))) {
           out.writeObject(settings1);
         }
         
         try (
           ObjectInput in = new ObjectInputStream(new FileInputStream("enum_settings.obj"))){
           settings2 = (EnumSettings) in.readObject();
         }
         
         // then
         assertThat(settings1).isEqualTo(settings2);
       }
       ```
  2. Reflection API 공격 방지 가능
      ``` java
      public void enumBlocksReflection() throws Exception {
        Constructor<?>[] declaredConstructors = EnumSettings.class.getDeclaredConstructors();
      
        for (Constructor<?> constructor : declaredConstructors) {
          constructor.setAccessible(true);
          Assertions.assertThrows(IllegalArgumentException.class, () -> {
            constructor.newInstance("INSTANCE");
          });
        }
      }
      ```
      Reflection이 방지되는 이유는 이유는 `java.lang.reflect.Constructor<T>.newInstance()`의 코드를 통해 알 수 있다.
      ``` java
      public T newInstance(Object ... initargs) throws InstantiationException, IllegalAccessException, IllegalArgumentException, InvocationTargetException {
        if (!override) {
          Class<?> caller = Reflection.getCallerClass();
          checkAccess(caller, clazz, clazz, modifiers);
        }
    
        if ((clazz.getModifiers() & Modifier.ENUM) != 0)
          throw new IllegalArgumentException("Cannot reflectively create enum objects");
        ConstructorAccessor ca = constructorAccessor;   // read volatile
    
        if (ca == null) {
          ca = acquireConstructorAccessor();
        }
    
        @SuppressWarnings("unchecked")
        T inst = (T) ca.newInstance(initargs);
        return inst;
      }
      ```
      위의 코드를 살펴보면, if ((clazz.getModifiers() & Modifier.ENUM) != 0) 부분을 볼 수 있다. Enum 타입인지 검사하여 IllegalArgumentException을 터트린다.

- 단점
  1. 부자연스러워 보일 수 있다
  2. 초기화 지연(lazy initialization)이 불가능하다
     - Enum type의 경우, load 되는 순간 인스턴스가 미리 생성되기 때문이다.
  3. 상속이 불가능하다
