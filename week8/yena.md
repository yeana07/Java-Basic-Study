# 16. 스트림과 병렬 처리

## 주요 키워드 및 개념 정리

### 스트림 소개

스트림(Stream)은 자바 8부터 추가된 컬렉션의 저장 요소를 하나씩 참조해 람다식으로 처리할 수 있도록 하는 반복자이다.

#### 반복자 스트림

Iterator 반복자 대신 Stream을 사용해 코드를 줄이고 훨씬 단순하게 반복자 사용 가능.

컬렉션`java.util.Collection`의 `stream()` 메소드로 스트림 객체를 얻고, `forEach()` 메소드로 컬렉션의 요소를 하나씩 가져온다. `forEach()` 메소드는 Consumer 함수적 인터페이스 타입의 매개값을 가지므로 컬렉션 요소를 소비할 코드를 람다식으로 기술할 수 있다.

```java
void forEach(Consumer<T> action)
```

```java
List<String> list = Arrays.asList("김", "이", "박");
Stream<Sring> stream = list.stream();
stream.forEach(name -> System.out.println(name));

```

#### 스트림의 특징

- **스트림은 람다식으로 요소 처리 코드를 제공한다**  
  -> Stream 제공 대부분 요소 처리 메소드는 함수적 인터페이스 매개 타입을 가짐 -> 람다식 또는 메소드 참조 사용 가능
- **내부 반복자를 사용하므로 병렬 처리가 쉽다**  
  -> 내부 반복자(internal iterator)는 컬렉션 내부에서 알아서 요소들을 반복시킴
- **중간 처리와 최종 처리 작업을 수행한다**  
  -> 중간처리에서 매핑, 필터링, 정렬 수행하고 최종 처리에서 반복, 카운팅, 평균, 총합 등 집계 처리

> 외부 반복자(external iterator)는 개발자가 코드로 직접 컬렉션 요소를 반복해 가져오는 코드 패턴이다.  
> 예시: index 이용하는 for문, Iterator 이용하는 while문

#### 스트림 특징 - 내부 반복자

내부 반복자를 사용하면 요소의 반복은 컬렉션에게 맡기고, 개발자는 요소 처리 코드에만 집중할 수 있다.

내부 반복자는 요소들의 반복 순서를 변경하거나, 멀티 코어 CPU를 최대 활용 위해 요소들을 분배시켜 병렬 작업을 할 수 있게 도운다.

**스트림은 람다식으로 요소 처리 내용만 전달**할 뿐, 반복은 컬렉션 내부에서 일어난다.

=> 내부 반복자는 하나씩 처리 하는 외부 반복자 보다 효율적

#### 스트림 특징 - 병렬처리

병렬(parallel) 처리란 한 가지 작업을 서브 작업으로 나누고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리하는 것을 말한다.

**병렬 처리 스트림**을 이용하면 런타임 시 하나의 작업을 서브 작업으로 **자동으로 나누고**, 서브 작업의 결과를 **자동으로 결합**해 최종 결과물을 생성한다.

_순차 처리 스트림 <-> 병렬 처리 스트림_

### 스트림의 종류

`java.util.stream` 패키지에는 스트림 API들이 있다. 모든 스트림이 사용하는 공통 메소드를 가진 BaseStream 인터페이스를 부모로 가지는 하위 스트림 Stream, IntStream, LongStream, DoubleStream들이 있다.

스트림 인터페이스 구현 객체의 소스는 주로 컬렉션과 배열에서 얻고 그 외 여러가지 소스에서 얻을 수 있다.

| 리턴 타입                                                  | 메소드(매개 변수)                                                                                                                                                                                   | 소스      |
| :--------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------- |
| Stream<T>                                                  | java.util.Collection.stream() <br> java.util.Collection.parallelStream()                                                                                                                            | 컬렉션    |
| Stream<T> <br> IntStream <br> LongStream <br> DoubleStream | Arrays.stream(T[ ]), Stream.of(T[ ]) <br> Arrays.stream(int[ ]), IntStream.of(int[ ]) <br> Arrays.stream(long[ ]), LongStream.of(long[ ]) <br> Arrays.stream(double[ ]), DoubleStream.of(double[ ]) | 배열      |
| IntStream                                                  | IntStream.reange(int, int) <br> IntStream.rangeStreamClosed(int, int)                                                                                                                               | int 범위  |
| LongStream                                                 | LongStream.reange(long, long) <br> LongStream.rangeStreamClosed(long, long)                                                                                                                         | long 범위 |
| Stream<Path>                                               | Files.find(Path, int, BiPredicate, FileVisitOption) <br> Files.list(Path)                                                                                                                           | 디렉토리  |
| Stream<String>                                             | Files.lines(Path, Charset) <br> BufferedReader.lines()                                                                                                                                              | 파일      |
| DoubleStream <br> IntStream <br> LongStream <br>           | Random.doubles(...) <br> Random.ints() <br> Random.longs()                                                                                                                                          | 랜덤 수   |

#### 숫자 범위로부터 스트림 얻기

Int 타입의 1부터 100까지의 합을 구하기 위해 IntStream의 `rangeClosed()` 메소드 이용. `rangeClosed()`는 첫 번째 매개값부터 두 번째 매개값까지 순차적으로 제공하는 IntStream을 리턴한다. `range()` 메소드는 두 번째 매개값을 포함X.

```java
IntStream stream = IntStream.rangeClosed(1, 100);
sream.forEach(a -> sum += a);
```

#### 파일로부터 스트림 얻기

`Files.lines(Path, Charset)`와 `BufferedReader.lines()`는 문자 파일의 내용을 스트림을 통해 행 단위로 읽는다.

#### 디렉토리로부터 스트림 얻기

`Files.find(Path, int, BiPredicate, FileVisitOption)`와 `Files.list(Path)` 메소드로 디렉토리의 내용(서브 디렉토리 또는 파일 목록)을 스트림을 통해 읽는다.

### 스트림 파이프라인

대량의 데이터를 가공해 축소하는 것을 리덕션(Reduction)이라고 하는데, 데이터의 합계, 평균값, 카운팅, 최소값 등이 대표적인 리덕션의 결과물이다.

컬렉션 요소를 리덕션 결과물로 만들기 위해 집계하기 좋도록 필터링, 매핑, 정렬, 그룹핑 등의 **중간 처리**가 필요하다.

#### 중간 처리와 최종 처리

스트림은 중간 처리와 최종 처리를 파이프라인(pipelines)으로 해결한다. 파이프라인은 여러 개의 스트림이 연결되어 있는 구조를 말한다. 최종 처리를 제외하고는 모두 중간 처리 스트림이다.

- `스트림 소스(컬렉션, 배열, 파일)` - `오리지널 스트림` - `중간처리 : 필터링 처리 중간 스트림` - `중간처리 : 매핑 처리 중간 스트림` - `최종 처리 : 집계 처리 결과물`

중간 스트림이 생성될 때 요소들이 바로 중간 처리 되는 것이 아니라 **최종 처리가 시작되기 전까지 중간 처리는 지연(lazy)**된다.

Stream 인터페이스에는 필터링, 매핑, 정렬 등 많은 중간 처리 메소드가 있는데 이 메소드들은 중간 처리된 스트림을 리턴한다. 그리고 이 스트림에서 다시 중간 처리 메소드를 호출해 파이프라인을 형성한다.

#### 중간 처리와 최종 처리 사용 예시

⬇ 회원 컬렉션에서 남자 필터링 중간 스트림 연결, 다시 남자 나이로 매핑하는 스트림 연결, 최종 남자 평균 나이 집계하는 파이프라인 코드.  
-> `filter()`로 필터링 - `mapToInt()`로 매핑 - `average()`로 평균값 산출

```java
//오리지널 stream 얻기
Stream<Member> maleFemaleStream = list.stream();
//중간처리: 오리지널 stream 필터링해서 남자요소 stream 얻기
Stream<Member> maleStream = maleFemaleStream.filter(m -> m.getSex() == Meber.MALE);
//중간처리: 남자요소를 나이요소로 매핑한 stream 얻기
IntStream ageStream = maleStream.mapToInt(Member :: getAge);
//최종처리: 스트림의 평균값 산출하는 집계 처리
OptionalDouble optionalDouble = ageStream.average();
//최종처리된 남자 평균 나이 가져오기
double ageAvg = optionalDouble.getAsDouble();
```

로컬 변수 생략하고 연결한 파이프라인 코드.

```java
double ageAvg = list.stream()
  .filter(m -> m.getSex() == Meber.MALE)
  .mapToInt(Member :: getAge)
  .average()
  .getAsDouble();
```

#### 중간 처리 메소드와 최종 처리 메소드

스트림이 제공하는 중간 처리와 최종 처리 메소드들.

리턴 타입이 스트림이면 중간 처리 메소드이고, 기본 타입이거나 OptionalXXX이면 최종 처리 메소드이다. 소속된 인터페이스가 공통이면 Stream, IntStream, LongStream, DoubleStream에서 모두 제공된다는 뜻.

- **중간 처리 메소드**
<table border = 1>
  <tr> 
    <th>종류</th>
    <th>리턴 타입</th>
    <th>메소드(매개 변수)</th>
    <th>소속된 인터페이스</th>
  </tr>
  <tr> <!-- 첫번째 줄 -->
    <td rowspan="2">필터링</td>
    <td rowspan="16">Stream <br> IntStream <br> LongStream <br> DoubleStream </td>
    <td>distinct()</td>
    <td>공통</td>
  </tr>
  </tr>
  <tr>
    <td>filter(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td rowspan="12">매핑</td>
    <td>flatMap(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>flatMapToDoouble(...)</td>
    <td>Stream</td>
  </tr>
  <tr>
    <td>flatMapToInt(...)</td>
    <td>Stream</td>
  </tr>
  <tr>
    <td>flatMapToLong(...)</td>
    <td>Stream</td>
  </tr>
  <tr>
    <td>map(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>mapToDouble(...)</td>
    <td>Stream, IntStream, LongStream</td>
  </tr>
  <tr>
    <td>mapToInt(...)</td>
    <td>Stream, LongStream, DoubleStream</td>
  </tr>
  <tr>
    <td>mapToLong(...)</td>
    <td>Stream, IntStream, DoubleStream</td>
  </tr>
  <tr>
    <td>mapToObj(...)</td>
    <td>IntStream, LongStream, DoubleStream</td>
  </tr>
  <tr>
    <td>asDoubleStream()</td>
    <td>IntStream, LongStream</td>
  </tr>
  <tr>
    <td>asLongStream()</td>
    <td>IntStream</td>
  </tr>
  <tr>
    <td>boxed()</td>
    <td>IntStream, LongStream, DoubleStream</td>
  </tr>
  <tr>
    <td>정렬</td>
    <td>sorted(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>루핑</td>
    <td>peek(...)</td>
    <td>공통</td>
  </tr>
</table>

- **최종 처리 메소드**

<table border = 1>
  <tr> 
    <th>종류</th>
    <th>리턴 타입</th>
    <th>메소드(매개 변수)</th>
    <th>소속된 인터페이스</th>
  </tr>
  <tr>
    <td rowspan = 3>매칭</td>
    <td>boolean</td>
    <td>allMatch(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>boolean</td>
    <td>anyMatch(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>boolean</td>
    <td>noneMatch(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td rowspan = 7>집계</td>
    <td>long</td>
    <td>count()</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>OptionalXXX</td>
    <td>findFirst()</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>OptionalXXX</td>
    <td>max(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>OptionalXXX</td>
    <td>min(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>OptionalXXX</td>
    <td>average()</td>
    <td>IntStream, LongStream, DoubleStream</td>
  </tr>
  <tr>
    <td>OptionalXXX</td>
    <td>reduce(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>int, long, double</td>
    <td>sum()</td>
    <td>IntStream, LongStream, DoubleStream</td>
  </tr>
  <tr>
    <td>루핑</td>
    <td>void</td>
    <td>forEach(...)</td>
    <td>공통</td>
  </tr>
  <tr>
    <td>수집</td>
    <td>R</td>
    <td>collect(...)</td>
    <td>공통</td>
  </tr>
</table>

### 필터링 distint(), filter()

필터링은 중간 처리 기능으로 요소를 걸러내는 역할을 한다. 모든 스트림이 가지고 있는 공통 메소드이다.

<table>
<tr>
  <th>리턴 타입</th>
  <th>메소드(매개 변수)</th>
  <th>설명</th>
</tr>
<tr>
  <td  rowspan = 5>Stream <br> IntStream <br> LongStream <br> DoubleStream</td>
  <td>distinct()</td>
  <td>중복 제거</td>
</tr>
<tr>
  <td>filter(Predicate)</td>
  <td rowspan = 4>중복 필터링</td>
</tr>
<tr>
  <td>filter(IntPredicate)</td>
</tr>
<tr>
  <td>filter(LongPredicate)</td>
</tr>
<tr>
  <td>filter(DoublePredicate)</td>
</tr>
</table>

### 매핑 flatMapXXX(), mapXXX(), asXXXStream(), boxed()

### 정렬 sorted()

### 루핑 peek(), forEach()

### 매핑 allMatch(), ansMatch(), noneMatch()

### 기본 집계 sum(), count(), average(), max(), min()

### 커스텀 집계 reduce()

### 수집 collect()

### 병렬 처리

병렬 처리(Parallel Operation)는 멀티 코어 CPU 환경에서 하나의 작업을 분할해 각각의 코어가 병렬적으로 처리하는 것을 말한다.

목적: 작업 처리 시간 줄이기

자바8부터 병렬 스트림을 제공하고, 컬렉션(배열)의 전체 요소 처리 시간을 줄여준다.

#### 동시성과 병렬성

멀티 스레드는 동시성(Concurrency) 또는 병렬성(Parallelism)으로 실행된다. -> 용어들 정확한 이해 필요

- 공통점: 멀티 스레드의 동작 방식
- 차이점: 목적이 상이
  - 동시성: 멀티 작업 위해 멀티스레드가 번갈아가며 실행하는 성질
  - 병렬성: 멀티 작업 위해 멀티 코어를 이용해 동시에 실행하는 성질

#### 데이터 병렬성

#### 작업 병렬성

## 관심 가는 내용
