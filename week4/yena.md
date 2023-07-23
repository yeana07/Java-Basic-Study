# 11. 기본 API 클래스

책에서 다루는 내용이 많아서 책 내용을 복습하는 의미로 정리함.

## 주요 키워드 및 개념 정리

### java.lang과 java.util 패키지

자바에서 가장 많이 사용하는 패키지.  
java.lang, java.util.

#### java.lang 패키지

자바 프로그램의 기본적인 클래스. java.lang 패키지의 클래스와 인터페이스는 import없이 사용 가능하다.

#### java.lang 패키지 주요 클래스들

- Object: 자바 클래스의 최상위 클래스
- System: IO장치 입출력, JVM 종료, GC 실행
- Class: 클래스를 메모리로 로딩
- String: 문자열 저장 및 정보 얻기
- StringBuffer, StringBuilder: 문자열 저장 및 내부 문자열 조작
- Math: 수학 함수 이용
- Wrapper: 입력값 검사, 박싱, 언박싱

#### java.util 패키지 주요 클래스들

- Arrays: 배열 조작
- Objects: 객체 비교, 널 여부 등 조사
- StringTokenizer: 특정 문자로 구분된 문자열 추출
- Random: 난수 얻기

### Object 클래스

Object는 자바의 최상위 부모 클래스이다.  
클래스 선언 시 명시적으로 다른 클래스를 상속 않으면 암시적으로 java.lang.Object 클래스를 상속한다.

#### 객체 비교 equals()

`public boolean equals(Object obj)`

두 객체를 비교한다. == 연산자와 동일한 결과를 리턴한다.  
직접 사용되지 않고 하위 클래스에서 재정의 하여 사용된다.

#### 객체 해시코드 hashCode()

객체의 메모리 번지를 변환해 해시코드(객체 식별하는 정수값)를 만들어 리턴한다.  
논리적 동등 비교 시에는 오버라이딩해 사용한다.

1. hashCode()의 리턴값이 같으면
2. equals()를 호출해 true가 도출되면
3. 두 객체는 동등 객체

String의 hashCode()는 같은 문자열일 경우 동일한 해시코드를 리턴하도록 오버라이딩 되어 있다.

#### 객체 문자 정보 toString()

객체의 문자 정보를 리턴한다. 하위 클래스에서 재정의해 유익한 정보를 리턴하도록 한다.

- java.util.Date.toString(): 현재 시스템 날짜 시간 정보
- String.toString(): 저장하고 있는 문자열

#### 객체 복제 clone()

원본 객체를 안전하게 보호하기 위해 사용한다. 복제된 객체를 신뢰하지 않는 영역에서 사용한다.

#### 얕은 복제(thin clone)

필드값을 복사해서 객체를 복제한다. (기본 타입이면 값 복사, **참조 타입이면 객체 번지 복사**)

Object의 clone()을 이용하려면 원본 객체는 반드시 java.lang.Clonable 인터페이스를 구현하고 있어야 한다.(의도적 표시)

#### 깊은 복제(deep clone)

참조 타입일 때 참조하고 있는 개체도 복제한다.  
Object의 clone()을 재정의해 참조 객체를 복제하는 코드를 직접 작성해야 한다.

### Ojbects 클래스

java.util.Objects 클래스는 정적 메소드들로 구성된 Object의 유틸리티 클래스이다.

#### 객체 비교 compare(T a, T b, Conparator<T> c)

두 객체를 비교자(Comparator)로 비교해 int 값을 리턴한다.  
compare()를 사용하려면 Comparator<T> 인터페이스를 구현해서 재정의하고 사용한다.

- 리턴 값이 음수: a < b
- 리턴 값이 0: a = b
- 리턴 값이 양수: a > b

#### 동등 비교 equals()

`Objects.equals(Object a, Object b)`는 null 값을 포함해 두 객체의 동등을 비교한다.

#### 동등 비교 deepEquals()

`objects.deepEquals(Ojbect a, Object b)`는 두 객체의 동등을 비교 하는데 서로 다른 배열이어도 항목 값이 모두 같으면 true를 리턴한다.  
-> Array.deepEquals()와 동일

#### 해시코드 생성 hash()

`Objects.hash(Object ... values)`는 매개값을 이용해 배열을 만들고 Arrays.hashCode(object[])를 호출해 얻은 해시코드 값을 리턴한다.  
-> 클래스가 hashCode() 재정의할때 리턴값 생성하기 위해 사용  
-> 클래스의 여러 필드들의 해시코드를 한번에 얻기 위해 사용

#### 해시코드 생성 hasoCode()

`Ojbects.hashCode(Object o)`는 매개값 객체의 해시코드를 리턴하는데 매개값이 null이면 0이 리턴된다.

#### 객체 문자 정보 toString()

- `toString(Object o)`
- `toString(Object o, String nullDefault)`: null-> nullDefault(저장하고 있는 문자열)

### System 클래스

자바 프로그램은 운영체제에서 바로 실행되는 것이 아니라 JVM 위에서 실행된다. java.lang.system 클래스를 이용해 운영체제의 일부 기능을 이용할 수 있다.  
모든 필드와 메소드가 static 이다.

#### 프로그램 종료 exit()

현재 실행하고 있는 프로세스를 강제 종료시킨다.  
int 매개값으로 종료 상태값을 넣을 수 있다. (정상 종료: 0, 비정상 종료: 1, 특정값 넣기도 가능)

#### 시스템 프로퍼티 읽기 getProperty()

시스템 프로퍼티(System Property)는 JVM이 시작될 때 자동 설정되는 시스템의 속성값이다. 키와 값으로 구성되어 있다.  
`System.getProperty(String key)` 매개값으로 해당 키 이름을 넣으면 키에 대한 값(프로퍼티 정보)을 문자열로 리턴한다.

### Class 클래스

자바는 클래스와 인터페이스의 메타 데이터를 java.lang 패키지의 Class 클래스로 관리한다.

> 메타 데이터: 클래스의 이름, 생성자 정보, 필드 정보, 메소드 정보

#### 클래스 정보 얻기 getClass()

Oject클래스의 getClass()로 Class 객체를 얻을 수 있다.

#### 클래스 정보 얻기 forName()

객체를 생성하기 전에 직접 Class 객체를 얻을 수 있다.

Class의 생성자는 접근할 수 없기 때문에 new 연산자로 객체를 만들 수 없고, 정적 메소드인 forName()을 이용해야 한다. 매개값으로 클래스 전체 이름을 입력하면 Class 객체를 리턴한다. 주어진 클래스를 찾지 못하면 ClassNotFoundException예외가 발생한다.

#### 리플렉션 Reflection

Class 객체를 이용해 클래스의 생성자, 필드, 메소드 정보를 알아내는 것을 리플렉션이라고 한다.  
Class 객체는 리플렉션을 위해 getDeclaredConstroctors(), getDeclaredFields(), getDeclaredMethods()를 제공한다.

```java
// 각 메소드는 배열을 리턴
Contructor[] constructors = clazz.getDeclaredConstructor();
Filed[] fields = clazz.getDeclaredFields();
Method[] methods = clazz.getDeclaredMethods();
```

#### 동적 객체 생성 newInstanc()

Class 객체를 이용해 new 연산자를 사용하지 않고 **동적으로 객체를 생성**한다. 코드 작성 시 클래스 이름을 결정할 수 없고, 런타임 시 클래스 이름이 결정되는 경우 사용한다.

기본 생성자를 호출해서 객체를 생성하기 때문에 반드시 클래스에 기본 생성자가 있어야 한다.

- 해당 클래스가 추상 클래스 또는 인터페이스 -> InstantiationException
- 클래스나 생성자가 접근 제한자로 인해 접근할 수 없을 경우 -> IllegalAccesException

> java9 이후 deprecated 되고 `clazz.getDeclaredConstructor().newInstance()`로 대체됨

### String 클래스

#### String 생성자

자바의 문자열은 java.lang.String 클래스의 인스턴스로 관리된다. 문자열 리터럴은 String 객체로 자동 생성되지만, 다양한 생성자를 이용해 직접 String 객체를 생성할 수 있다.  
보통 어딘가에서 받은 정보인 byte[] 배열을 문자열로 변환하기 위해 사용된다.

```java
//배열 전체를 String 객체로 생성
String str = new String(byte[] bytes);
//지정한 문자셋으로 디코딩
String str = new String(byte[] bytes, String charsetName);

//배열의 offset 인덱스 위치부터 length만큼 String 객체로 생성
String str = new String(byte[] bytes, int offset, int length);
//지정한 문자셋으로 디코딩
String str = new String(byte[] bytes, int offset, int length, String charsetName);
```

> length값은 배열 길이에서 -2
> 캐리지리턴(\r) + 라인피드(\n) 제외 위해

#### 문자열 비교 equals()

자바는 문자열 리터럴이 동일하면 동일한 String 객체를 참조한다. 그러나 new 연산자로 생성하면 다른 객체가 된다. String의 equals()는 두 String 객체의 문자열만 비교한다.

#### equalsIgnoreCase()

영어로 된 두 문자열을 대소문자 관계없이 비교할때 사용한다.

#### 바이트 배열로 변환 getBytes()

문자열을 바이트 배열로 변환(인코딩)한다. 매개값이 없으면 시스템의 기본 문자셋을 리턴하고, 매개값으로 특정 문자셋을 리턴하도록 설정할 수 있다. 잘못된 문자셋을 매개값으로 주면 java.io.UnsupportedException이 발생한다.

바이트 배열을 다시 문자열로 디코딩하는 생성자

- `String(byte[] bytes)`: 기본 문자셋으로 디코딩
- `String(byte[] bytes, String charSetName)`: 시스템 기본 문자셋과 다른 문자셋으로 인코딩된 바이트 배열을 디코딩

#### 문자열 찾기 indexOf()

매개값으로 주어진 문자열이 시작되는 인덱스를 리턴한다. 포함되어 있지 않으면 -1을 리턴한다.  
if문의 조건식에서 자주 사용된다.

#### 문자열 대치 relace()

첫 번째 매개값인 문자열을 찾아 두 번째 매개값으로 대치한 **새로운 문자열을 생성**하고 리턴한다.

#### 문자열 잘라내기 subString()

주어진 인덱스에서 문자열을 추출한다.

- `subString(int beginIndex, int endIndex)`: 주어진 시작과 끝 인덱스 사이의 문자열 추출(끝자리 수 제외)
- `subString(int beginIndex)`: 주어진 인덱스부터 끝까지 추출

#### 문자열 변환 valueOf()

기본 타입의 값을 문자열로 변환한다. (String클래스에는 매개 변수의 타입별로 오버로딩 되어있다.)

### StringTokenizer 클래스

문자열이 특정 구분자(delimiter)로 연결되어 있을 경우, 구분자를 기준으로 부분 문자열을 분리한다.  
String의 split()은 정규 표현식으로 구분하고, StringTokenizer는 문자로 구분한다.

#### split()

String 클래스의 split()은 매개값으로 주어진 정규 표현식 또는 특정 문자를 기준으로 문자열을 나누어 배열로 생성한다. (구분자로 사용할 특정 문자 또는 정규 표현식이 여러개면 |로 연결)

```java
String text = "체리&옥수수,감자,고구마-자두";

String[] names = text.split("&|,|-");
```

#### StringTokenizer

문자열이 한 종류의 구분자로 연결되어 있을 경우 문자열(토큰)을 분리할 수 있다.  
StringTokenizer 객체를 생성할 때 첫번째 매개값으로 전체 문자열을 주고, 두 번째 매개값으로 구분자를 주면 된다. 구분자 생략시 공백이 기본 구분자가 된다.  
객체가 생성되면 메소드를 사용해 부분 문자열을 분리할 수 있다.

- `int countTokens()`: 꺼내지 않고 남아있는 토큰 수
- `boolean hasMoreTokens()`: 남아 있는 토큰 유무
- `Stirng nextToken()`: 토큰 하나씩 꺼내기(토큰 없으면 java.util.NosuchElementException)
  -> 먼저 hasMoreTokens()로 꺼내올 토큰 있는지 조사 후 nextToken() 사용

### StringBuffer, StringBuilder 클래스

String은 내부의 문자열을 수정할 수 없다. 문자열을 결합하는 + 연산자를 사용할수록 그만큼 String 객체의 수가 늘어나 프로그램 성능을 느리게 하는 요인이 된다.  
문자열 변경 작업이 많을 경우 String 클래스 보다 java.lang패키지의 StringBuffer 또는 StringBuilder 클래스를 사용하는게 좋다.  
이 두 클래스는 데이터를 임시로 저장하는 메모리인 내부 버퍼 공간에 문자열을 저장하고, 그 안에서 수정 작업을 할 수 있다.

- StringBuffer: 멀티 스레드 환경에서 사용 하도록 동기화 적용.
- StringBuilder: 단일 스레드 환경에서 사용

#### StringBuilder 생성과 초기 버퍼

StringBuilder의 기본 생성자는 16개의 문자열을 저장하는 초기 버퍼를 만들고, 매개값으로 초기 버퍼의 개수를 지정할 수 있다. 버퍼가 부족할 경우 자동으로 크기를 늘리기 때문에 초기 버퍼 크기는 그다지 중요하지 않다. (매개값 Stirng도 가능)

### 정규 표현식과 Pattern 클래스

문자열이 정규 표현식(Regular Expression)으로 구성되어 있는지 검증해야 하는 경우 사용한다.

#### 정규 표현식 작성 방법

- []: 한 개의 문자
- \d: 한 개의 숫자, [0-9]와 동일
- \s: 공백
- \w: 한 개의 알파벳 또는 한 개의 숫자, [a-zA-Z0-9]와 동일
- ?: 없음 또는 한 개
- \*: 없음 또는 한 개 이상
- +: 한 개 이상
- {n}: 정확히 n개
- {n.}: 최소한 n개 이상
- {n,m}: n개에서부터 m개까지
- (): 그룹핑
- .: 한 개의 문자(문자, 숫자, 기호 포함)

#### Pattern 클래스

문자열을 정규 표현식으로 검증하는 기능은 java.util.regex.Pattern 클래스의 정적 메소드인 matches()가 제공한다.

`boolean result = Pattern.matches("정규표현식", "검증할 문자열")`

```java
// 전화번호 검증
String regExp = "(02|010)-\\d{3,4}-\\d{4}";
String data = "010-1234-5678";

boolean result = Pattern.matches(regExt, data);

if(result){...} else {...}
```

### Arrays 클래스

배열을 복사, 항목 정렬, 항목 검색 하는 배열 조작 기능을 가지고 있다. Arryas 클래스의 모든 메소드는 static 하다.

#### 배열 복사

- `Arrays.copyOf(원본배열, 복사할길이)`
- `copyOfRange(원본배열, 시작인덱스, 끝인덱스)`(끝인덱스 포함X)

#### 배열 항목 비교

Arrys의 equals()와 deepEquasl()는 배열 항목을 비교한다.

- `Arryas.equals()`:1차 항목의 값만 비교.(참조 타입일 경우 객체 주소 비교)
- `Arrays.deepEquals()`: 1차 항목이 다른 배열이 경우, 중첩된 배열의 항목까지 비교

### Wrapper 클래스

자바는 기본타입의 값 포장 객체를 이용해 객체로 생성할 수 있다. 내부의 값은 외부에서 변경할 수 없다.  
java.lang 패키지에 포장 클래스가 있는데 각 기본 타입에 대응되는 클래스들이 있다.

#### 박싱 Boxing

기본 타입의 값을 포장 객체로 만드는 과정을 박싱이라고 한다.  
포장 클래스의 생성자 매개값으로 기본 타입의 값 또는 문자열을 넘기면 된다.

> int - Integer, char - Character 제외한 기본 타입의 첫 글자를 대문자로 바꾸면 포장 클래스 이름이 됨

또는 각 포장 클래스마다 가지고 있는 정적 메소드 **valueOf()** 사용.

#### 언박싱 Unboxing

포장 객체에서 기본 타입의 값을 얻어내는 과정을 언박싱이라 한다.  
각 포장 클래스마다 가지고 있는 "기본타입명+Value()" 메소드 호출.

> byte num = obj.byteValue();
> int num = obj.ingValue();
> ...

#### 자동 박싱, 자동 언박싱

- 자동 박싱: 포장 클래스 타입에 기본값이 대일될 경우 발생한다.
- 자동 언박싱: 기본 타입에 포장 객체가 대입될 경우 발생한다.

#### 문자열을 기본 타입 값으로 변환 parse()

문자열을 기본 타입 값으로 변환할때에도 포장 클래스가 많이 사용된다.  
대부분의 포장 클래스에는 "parse+기본타입" 으로 되어있는 정적 메소드가 있어, 문자열을 매개값으로 받아 기본 타입 값으로 변환한다.

> byte num = Byte.parseByte("10");
> int num = Integer.parseInt("1000");
> ...

#### 포장 객체 값 비교

포장 객체 일부는 내부 값을 비교하기 위해 객체의 참조를 비교하는 == 또는 != 연산자를 사용 할 수 없다(일부 규칙에서만 사용 가능).  
직접 내부 값을 언박싱해서 비교하건, equals()로 내부 값을 비교 하도록 오버라이딩 해야 한다.

### Math, Random 클래스

#### Math 클래스

java.lang.Math 클래스의 메소드는 모두 정적 메소드이고 수학 계산에 사용된다..

- `int abs(int a)`, `doulbe abs(double a)`: 절대값 리턴
- `double ceil(double a)`: 올림값
- `double floor(double a)`: 버림값
- `int max(int a, int b)`, `double.max(double a, double b)`: 최대값
- `int min(int a, int b)`, `double.min(double a, double b)`: 최소값
- `double random()`: 랜덤값(0.0 <= ~ < 1.0 사이의 값)
- `double rint(double a)`: 가까운 정수의 실수값
- `long round(double a)`: 반올림값

#### Random 클래스

java.util.Random 클래스는 난수를 얻어내기 위한 메소드들을 제공한다.  
난수를 만드는 알고리즘에 사용되는 값인 종자값(seed)을 설정할 수 있다.

> 종자값이 같으면 같은 난수를 얻는다!

Random 객체 생성

- Random(): 호출 시마다 종자값(현재 시간)이 자동 설정된다.
- Random(long seed): 매개값으로 주어진 종자값이 설정된다.

Random 클래스 메소드

- `boolean nextBoolean()`
- `double nextDouble()`: 0.0 <= ~ < 1.0 사이의 난수 리턴
- `int nextInt()`: -2^31 <= ~ <= 2^31-1 사이의 난수 리턴
- `int nextInt(int n)`: 0 <= ~ <n 사이의 난수 리턴

### Data, Calendar 클래스

두 클래스 모두 java.util 패키지에 포함된다.

#### Data 클래스

- `new Date()`: 컴퓨터의 현재 날짜를 읽어 Date 객체로 만든다.

#### Calendar 클래스

추상 클래스이므로 new 연산자로 인스턴스를 생성할 수 없다.  
Calendar클래스의 정적 메소드인 getInstance()를 이용해 현재 운영체제에 설정되어 있는 시간대(timezone)을 기준으로 한 하위 객체를 얻는다.

하위 객체에서 get() 메소드를 이용해 날짜와 시간 정보를 얻을 수 있다. get() 메소드의 매개값은 모두 Calendar 클래스에 선언되어 있는 상수들이다.

```java
Calendar now = Calendar.getInstace();

int year = now.get(Calendar.YEAR);
int week = now.get(Calendar.DAY_OF_WEEK);
...
```

### Format 클래스

java.text 패키지의 형식(Format) 클래스를 이용해 원하는 형식으로 데이터를 표현할 수 있다.

#### 숫자 형식 클래스 DecimalFomrat

숫자 데이터를 원하는 형식으로 표현하기 위해 패턴을 사용한다. 적용할 패턴을 생성자 매개값으로 지정해 객체를 생성하고, format() 메소드를 호출해서 패턴이 적용된 문자열을 얻으면 된다.

- 패턴 기호: 0, #, ., -, E, ;, %, \u00A4

#### 날짜 형식 클래스 SimpleDateFormat

날짜를 특정 문자열 포맷으로 얻을때 사용한다. 패턴을 생성자 매개값으로 지정해 객체를 생성하고, format() 메소드를 호출해 패턴이 적용된 문자열을 얻는다.

- 패턴 문자: y, M, H, h, d, D, E, a, w, W, K, k, m, s, S

#### 문자열 형식 클래스 MessageFormat

문자열에 데이터가 들어갈 자리를 표시해두고, 프로그램이 실행되면 동적으로 데이터를 삽입해 문자열을 완성한다. 정적 format() 메소드를 호출해 완성된 문자열을 리턴시킨다.

```java
String text = "회원 ID: {0} \n회원 이름: {1} \n회원 전화: {2}";
Ojbect[] arguments = {id, name, tel};
String result = MessageFormat.format(text, arguments);
```

- 첫 번째 매개값 -> 매개 변수화된 문자열 지정
- 두 번째 이후 매개값 -> 인덱스 순서에 맞게 값 나열(배열 대입해도 됨)

### java.time 패키지

자바 8부터 날짜와 시간을 나타내는 API로 java.time 패키지와 하위 패키지가 사용된다.

- java.time: LocalDate, LocalTime, LocalDateTiem, ZonedDateTime 포함
- java.time.chrono: 다른 달력 시스템이 필요할 때 사용하는 API들 포함
- java.time.format: 날짜와 시간을 파싱, 포맷팅 API들 포함
- java.time.temporal: 날짜와 시간을 연산 하는 보조 API들 포함
- java.time.zone: 타임존 지원 API들 포함
