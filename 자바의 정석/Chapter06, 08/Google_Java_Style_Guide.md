# Google Java Style Guide - Naming
https://google.github.io/styleguide/javaguide.html#s5-naming
- 해당 문서는 Google Java Style Guide 문서의 #5. Naming 을 번역한 내용입니다.
- 식별자란 Java에서 변수, 상수, 배열, 메소드, 클래스 등을 구분하기 위해 사용자가 정의하는 이름을 뜻합니다.

## 1. 모든 식별자에 대한 공통적인 규칙
각 식별자는 오직 ASCII 문자와 숫자만을 사용해야 하지만, 간혹 _ 문자를 표기할 수 있습니다. 따라서, 유효한 식별자라면 `\w+` 정규 표현식 패턴과 일치해야 합니다.

Google의 Java 스타일에서는 특수 접두사 / 접미사가 사용되지 않습니다. 그 예시로, `name_`, `mName`, `s_name`, `kName` 은 Google 스타일이 아닙니다.

# 2. 식별자 타입별 규칙
## 2.1 패키지 이름
패키지 이름은 오직 소문자와 숫자에 의해서만 구성되어야 하며, _ 문자는 허용되지 않습니다. 그러므로, 연속된 단어들은 단순하게 연결하여 사용하세요.
그 예시로는 `com.example.deepspace`가 있습니다. `com.example.deepSpace`나 `com.example.deep_space`와 같은 사용은 지양해주세요.

## 2.2 클래스 이름
클래스 이름은 UpperCamelCase로 작성되어야 합니다.

클래스 이름은 보편적으로 명사나 명사구 형태로 작성됩니다. 그 예시로, `Character`나 `ImmutableList`가 있습니다. 인터페이스 이름 역시, `List`처럼 명사나 명사구일 수 있습니다. 그러나 가끔은 `Readable`처럼 형용사 혹은 형용사구가 사용되기도 합니다.

그러나 유달리 annotation 타입에는 명확히 규정된 네이밍 관습이 없습니다.

테스트 클래스의 이름은 `HashIntegrationTest`와 같이 `Test`로 끝날 수 있어야 합니다. 만약, 해당 클래스가 `HashImpl` 이라는 이름의 클래스 하나만을 위한 테스트 클래스라면, 뒤에 `Test`를 붙여 `HashImplTest`라는 이름이 되도록 작성해주세요.

## 2.3 메서드 이름
메서드 이름은 lowerCamelCase로 작성되어야 합니다.

메서드 이름은 일반적으로 `sendMessage`나 `stop`과 같이 동사나 동사구 형태로 작성됩니다.

_ 문자의 경우, JUnit 테스트 메서드에서 lowerCamelCase로 작성된 이름 간의 논리적 구성요소를 구분시키기 위해 사용될 수 있습니다.
그 예시로는 `transferMoney_deductsFromSource`가 있습니다. 그러나 정확히 정해진 테스트 메서드 명명 규칙은 없습니다.

## 2.4 상수 이름
상수는 UPPER_SNAKE_CASE를 사용합니다. UPPER_SNAKE_CASE란, 각각의 단어는 대문자로만 표기되며, 단어와 단어 사이의 구분을 위해 _ 문자가 사용되는 표기 방식입니다. 그러나 상수란 정확히 무엇을 의미하는 걸까요?

상수란 일반적으로 그 값이 immutable하고, 그 사용에 부수 효과(side effects)가 없는 static 필드를 의미합니다.
상수의 예로는 int와 같은 primitive 타입, String 문자열, immutable한 값을 가진 클래스 및 null로 설정된 모든 것들이 있습니다.
만약 한 인스턴스의 관측 가능한(observable) 값이 변경될 수 있다면, 해당 인스턴스는 상수가 아닙니다. 단순히 객체를 변경시키지 않으려는 의도만으로는 상수로 만들 수 없습니다. 이 예시는 아래 코드를 참고하세요.

```
// 상수
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");
static final Map<String, Integer> AGES = ImmutableMap.of("Ed", 35, "Ann", 32);
static final Joiner COMMA_JOINER = Joiner.on(','); // because Joiner is immutable
static final SomeMutableType[] EMPTY_ARRAY = {};

// 상수가 아닌 것들
static String nonFinal = "non-final";
final String nonStatic = "non-static";
static final Set<String> mutableCollection = new HashSet<String>();
static final ImmutableSet<SomeMutableType> mutableElements = ImmutableSet.of(mutable);
static final ImmutableMap<String, SomeMutableType> mutableValues =
ImmutableMap.of("Ed", mutableInstance, "Ann", mutableInstance2);
static final Logger logger = Logger.getLogger(MyClass.getName());
static final String[] nonEmptyArray = {"these", "can", "change"};
```
위의 예시를 보며, 이름은 주로 명사 혹은 명사구로 작성되었음을 다시 한 번 기억하세요.

## 2.5 상수가 아닌 필드의 이름
상수가 아닌 필드의 이름은 lowerCamelCase로 작성합니다.

해당 필드의 이름은 보편적으로 `computedValues` 혹은 `index`와 같이 명사 혹은 명사구로 작성됩니다.

## 2.6 파라미터 이름
파라미터의 이름은 lowerCamelCase로 작성합니다.

public 메서드에서 오직 한 글자로 구성된 이름의 파라미터는 사용을 지양해주세요.

## 2.7 지역 변수 이름
지역 변수의 이름은 lowerCamelCase로 작성합니다.

지역 변수는 final이거나 immutable 할지라도 상수로 간주되지 않기 때문에 상수와 같이 UPPER_SNAKE_CASE로 작성되지 말아야 합니다.

## 2.8 타입 변수 이름
각각의 타입 변수는 두 가지의 작명 방식이 있습니다.

- 대문자로 한 글자 경우에는 하나의 숫자가 붙을 수 있습니다. 그 예시는 `E`, `T`, `X`, `T2` 입니다.
- 5.2.2의 형태를 따른 클래스 형식의 이름인 경우에는, 대문자 T가 따라 붙을 수 있습니다. 그 예시는 `RequestT`, `FooBarT` 입니다.

# 3. 카멜 케이스 정의하기
때로, "IPv6" 혹는 "iOS"와 같이 현실에서 사용되는 약어나 특이한 구조가 사용되는 경우처럼, 영어 구문을 camel case로 변경하는 합리적인 방법에는 여러 가지가 있습니다.

정확도를 개선하기 위해서 Google 스타일은 다음과 같은 결정론적인 체계를 정의했습니다.

1. 구문을 일반 ASCII 코드에 따른 문자로 변환하고, 모든 `'(어퍼스트로피)`를 제거합니다. 예를 들어, `Müller's algorithm`은 `Muellers algorithm`가 되어야 합니다.
2. 이 결과를 공백과 나머지 구두점(일반적으로 `-`하이픈)을 제거함으로써 두 글자로 분할하세요.
    - 어떤 단어가 이미 일반적인 camel case라면, 이를 구성 부분으로 분할하세요. 해당 예시는 `AdWords`가 `ad words`가 되는 경우입니다. 참고로 `iOS`와 같은 단어는 일반적이지 않기 때문에 camel case에 적용되지 않아 `i os`와 같이 이 규칙을 적용할 수는 없습니다.
3. 이제 모든 문자(약어 포함)를 소문자로 표시한 다음,
    - upper camel case를 적용시키기 위해서는 모든 단어의 첫번째 문자를 대문자로 작성하세요.
    - lower camel case를 적용시키기 위해서는 _첫번째 단어를 제외한_ 모든 단어의 첫번째 문자를 대문자로 작성하세요.
4. 마지막으로, 모든 단어를 하나의 식별자가 될 수 있도록 붙여 주세요.

다음은 위 체계를 적용한 예시입니다. 기존 단어의 대소문자 형태는 대부분 소실된다는 점을 유념하세요.

|산문 형태|올바른 변환|잘못된 변환|
|------|---|---|
|"XML HTTP request"|XmlHttpRequest|XMLHTTPRequest|
|"new customer ID"|newCustomerId|newCustomerID|
|"inner stopwatch"|innerStopwatch|innerStopWatch|
|"supports IPv6 on iOS?"|supportsIpv6OnIos|supportsIPv6OnIOS|
|"YouTube importer"|YouTubeImporter| |
|"YouTube importer"|YoutubeImporter *| |

- 마지막의 * 형태는 허용되긴 하지만, 추천하는 형태는 아닙니다.
