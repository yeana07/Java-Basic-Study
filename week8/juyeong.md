

# 스트림과 병렬처리

## 16.1 스트림 소개
> 스트림은 컬렉션(배열 포함)의 저장 요소를 하나씩 참조해서 람다식으로 처리할 수 있게 해주는 '반복자'

스트림 이전에는 List<String> 컬렉션에서 값을 하나씩 뽑기 위해 Iterator 반복자 사용
스트림 이후로는 list.stream();, stream.forEach를 사용

	``` java
	List<String> list = Arrays.asList("주영", "예나");
	Iterator<String> Iterator = list.iterator();
	while(interator.hasNext()) {
    String name = Iterator.next();
    system.out.println(name)
	}
	```

이 코드를 스트림을 사용하면

	``` java
	List<String> list = Arrays.asList("주영", "예나");
	Stream<String> stream = list.stream();
	stream.forEach (name -> System.out.println(name));
	```

	1. stream() 메소드로 스트림 객체를 얻고
	2. 포 이치 메소드를 통해 컬렉션 요소를 하나씩 시스아웃한다.

이 때 forEach 메소드는 Consumer 함수적 타입 매개값을 가지므로 람다식으로 표기할 수 있다.
(람다식 Consumer 타입 보면 이해갈 듯! 책으로 치면 P689)

> void forEach(Consumer<T> action)

### 스트림의 특징
Iterator와 비슷하지만 어떤 이점이 있어 쓰는걸까?

1. 람다식으로 처리할 수 있다
2. 내부 반복자를 사용하여 병렬처리 쉬움
3. 중간처리, 최종처리 작업을 수행함

이 세가지 차이가 있다.

1. 람다식으로 처리
   
말 그대로 처리를 람다식을 이용하여 할 수 있다.
스트림이 제공하는 대부분의 요소 처리 메소드는 함수적 인터페이스 매개 타입을 가지기 때문에
람다식 또는 메소드 참조를 이용하여 요소 처리 내용을 매개값으로 전달할 수 있다. 
(P.784 코드 참조)


2. 내부 반복자를 사용하므로 병렬처리가 쉽다.
   
외부 반복자는 개발자가 코드로 직접 컬렉션 요소를 반복해서 가져오는 코드다.
index를 사용한 for문, Iterator를 사용한 while문이 여기에 해당한다.

반면, 내부 반복자는 **컬렉션 내부에서 요소들을 반복** 하고, 개발자는  **요소 당 처리해야 할 코드만 제공하는 코드 패턴** 을 말한다.
 
> 내부 반복자의 이점
 1. 컬렉션 내부에서 어떻게 요소를 반복시킬 것인가는 컬렉션에게 맡기고, 개발자는 요소 처리 코드에만 집중할 수 있다.
 2. 내부반복자는 요소들의 반복 순서를 변경하거나 멀티코어 CPU를 최대한 활용하기 위해 병렬작업을 지원하기 때문에 효율적으로 요소 반복이 가능하다.

또, 이터레이터는 컬렉션 요소를 가져와서 처리하는 것까지 모두 개발자가 신경쓰지만
스트림은 람다식으로 요소처리내용만 전달하고, 반복은 컬렉션 내부에서 일어난다. 
 -> 코드가 간결해지고, 요소들의 병렬처리가 컬렉션 내부에서 처리되므로 일석이조

> 병렬처리가 뭐냐면
  - 한 가지 작업을 서브 작업으로 나눠
  - 서브 작업들은 분리된 스레드에서 병렬적으로 처리
  - 런타임 시 하나의 작업을 서브작업으로 자동으로 나눔
  - 서브 작업 결과를 자동으로 결합하여 최종 결과물 생성
  

3. 스트림은 중간 처리와 최종 처리를 할 수 있다
   - 중간처리: 매핑, 필터링, 정렬
   - 최종처리: 반복, 카운팅, 평균, 총합 등 집계처리수행
  eg. double avg = studentList.stream().mapToInt(Student :: getScore) //메소드 참조.. 제네릭인가 람다에 있는 내용 다시 봐야함


## 16.2 스트림의 종류
Base Stream 아래에
Stream, IntStream, LongStream, DoubleStream이 있다.

> 베이스 스트림에는 공통 메소드들이 정의되어 있을 뿐, 코드에서 직접적으로 사용되지는 않는다. 하위 스트림들이 직접적으로 이용된다.
- Stream : 객체 요소를 처리
- IntStream, LongStream, DoubleStream : 인트, 롱, 더블 요소를 처리한다.

**이 스트림 인터페이스의 구현 객체는 다음과 같은 다양한 소스(주로 컬렉션, 배열)로부터 얻을 수도 있다.** 
![스트림 소스](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9995E64B5A5DBE5403)


컬렉션으로부터, 배열로부터, 숫자로부터, 파일로부터 (버퍼드리더 사용), 디렉토리로부터 스트림을 얻을 수 있고 예제는 P791~794 참고할 것


## 16.3 스트림 파이프라인

- 리덕션(Reduction) : 대량의 데이터를 가공해서 축소하는 것
- 데이터의 합계, 카운팅, 최대/최솟값 등을 구하는 것이 대표적인 리덕션이다. 
- 컬렉션 요소를 리덕션의 요소로 바로 집계할 수 없는 경우에는, **집계하기 좋도록 중간처리 (필터링, 매핑, 정렬, 그룹핑 등)이 필요하다.**

### 중간처리와 최종처리
> 스트림 파이프라인


- 스트림은 데이터 처리를 파이프 라인으로 처리한다. 
- 그림을 보면 쉽게 이해가는데, 파이프라인은 여러 개의 스트림이 연결된 구조다.
  ![스트림 파이프라인](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99BBF93D5A5DF1F007)
- 재밌는 것은 중간 스트림이 생성될 때 요소들이 바로 중간처리 되는 것이 아니라, 최종 처리가 시작되기 전까지 지연(lazy) 되다가, 최종 처리가 시작되면 요소가 하나씩 중간처리 스트림에서 메소드를 타고 최종처리까지 오게 된다.
- 메소드를 탄 뒤 처리된 스트림을 리턴하고, 리턴한 스트림에서 다시 다음 중간처리 메소드를 호출하여 파이프라인을 형성한다. 

	```java
	double ageAvg = list.stream() //오리지날 스트림
		.filter(m -> m.getSex() == member.MALE) //중간 처리 스트림
		.mapToInt(Member :: getAge) 
		.average() //최종처리
		.getAsDouble();
	```

### 중간처리 메소드와 최종처리 메소드
- 중간처리
![중간처리](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2Fbde37c9c-b959-4246-a0a1-d7ac7f707703%2Fimage.png)

-최종처리
![최종처리](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F0bcc4547-1064-4a79-b092-a43662558c8b%2Fimage.png)

- 중간처리 메소드 : 리턴타입이 스트림
- 최종처리 메소드 : 리턴타입이 기본타입 or OptionalXXX
- 위의 표에서 공통의 의미는 스트림,인트스트림,롱스트림,더블스트림에서 모두 제공된다는 뜻
  
## 16.4 필터링
- 걸러내는 역할. 
  ![필터링](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2Fb43b22d8-c75d-411e-b111-4f8e5892ac39%2Fimage.png)

- distinct(), filter() : 모든 스트림이 가진 공통 메소드

  - distinct() : 중복을 제거(Object.equals()로 비교하여 true 리턴하면 제거함)
  - filter() : 매개값으로 주어진 Predicate가 true를 리턴하면 걸러냄

  

## 16.5 매핑
- 스트림의 요소를 다른 요소로 대체하는 작업.
- flatXXX(), mapXXX(), asDoubleStream(), asLongStream(), boxed()가 있다.
  - flatXXX(): 요소를 대체하는 복수개의 요소들로 구성된 새로운 스트림을 리턴한다.
  - ![플랫](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F296d212a-7206-4d20-b4b9-0f768b735765%2Fimage.png)
  - ![플랫 메소드 종류](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F5d419e3a-ee63-43d9-9be9-c1d0446885f0%2Fimage.png)
    ```java
	inputList1.stream()
		.flatMap(data -> Arrays.stram(data.split(" ")))
		.forEach(word -> System.out.println(word))
	```
  - mapXXX()
    - 요소를 대체하는 요소로 구성된 새로운 스트림을 리턴한다.
    - ![맵](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F0a4bee0c-ece8-4cce-9d79-dc6bf26ba736%2Fimage.png)
  - asDoubleStream(), asLongStream(), boxed()
    - ![애즈애즈](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F262237b3-d4bb-4912-8e61-ee9a9eb867a4%2Fimage.png)

## 16.6 정렬
- 스트림은 요소가 최종 처리되기 전에, **중간단계에서 요소를 정렬해서 최종처리 순서를 변경** 할 수 있다.
- 정렬메소드는 다음과 같다
![정렬sorted](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2Fe8cf60a1-8cc5-4a30-a86f-c3ba0f349493%2Fimage.png)

- 객체 요소일 경우, 클래스가 Comparable을 구현하지 않으면 sorted()메소드를 호출했을 때 클래스캐스트익셉션(ClassCastException)이 발생하기 때문에 Comparable을 구현한 요소에서만 sorted()메소드를 호출해야 한다. (예제 P806)

## 16.7 루핑
- 루핑은 요소 전체를 반복하는 것
- Peek(), forEach() 메소드가 있다.
- 두 메소드는 루핑 기능에서는 동일 but 동작 방식이 다르다.
- Peek() : 중간처리 메소드
  - 중간 처리 단계에서 전체 요소를 루핑하며 추가 작업하기 위해 사용
  - 최종처리 메소드가 실행되지 않으면 지연
    - 반드시 최종 처리 메소드가 호출되어야 동작
- forEach() : 최종처리 메소드
  - 파이프라인 마지막에 루핑하여 요소를 하나씩 처리
  - 요소를 소비하는 최종 처리 메소드
    - sum()과 같은 다른 최종 메소드 호출 불가


## 16.8 매칭
- 최종 처리 단계에서 요소들이 특정 조건에 만족하는지 조사하는 것
- 세 가지 메소드가 있다.
  1. allMatch() 메소드
	- 모든 요소들이 매개값으로 주어진 Predicate의 조건을 만족하는지 조사
  2. anyMatch()메소드
	- 최소한 한 개의 요소가 매개값으로 주어진 Predicate조건을 만족하는지 조사
  3. noneMatch() 메소드
	- 모든 요소들이 매개값으로 주어진 Predicate의 조건을 만족하지 않는지 조사

![매칭메소드](https://velog.velcdn.com/images%2Fdev_jhjhj%2Fpost%2F8dd58f13-dab5-4f4a-8833-57b5e18a738a%2FallMatch.png)

## 16.9 기본 집계
- 최종 처리 기능으로, 요소들을 처리해서 카운팅, 합계, 평균, 최대/최소값등 하나의 값으로 산출하는 것
- 대량의 데이터를 가공해서 리덕션하는 것
- 1. 스트림이 제공하는 기본 집계
- ![기본집계 메소드](https://velog.velcdn.com/images%2Fdev_jhjhj%2Fpost%2Ff2b24c3f-d65a-456e-93bd-a4012ab7372b%2F%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%A6%E1%84%80%E1%85%A9%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%E1%84%8C%E1%85%B5%E1%86%B8%E1%84%80%E1%85%A8.png)
  
- 2. Optional 클래스
  - Optional, OptionalDouble, OptionalInt, OptionalLong 클래스
  - 저장하는 값의 타입만 다를 뿐 제공하는 기능은 거의 동일
  - 단순히 집계 값만 저장하는 것이 아님
  - 집계 값이 존재하지 않을 경우 디폴트 값을 설정 가능
  - 집계 값을 처리하는 Consumer도 등록 가능
  - Optional 클래스들이 제공하는 메소드들
  - ![옵셔널](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F030037d6-e7bb-4104-946b-ac85f44a032f%2Fimage.png)

```java
  public static void main(String[] args) {
        int[] intArr = {1, 2, 3, 4, 5, 6, 7};

        long count = Arrays.stream(intArr)
                .filter(a -> a % 2 == 0)
                .count();
        System.out.println("2의 배수 개수 : " + count);

        long sum = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .sum();
        System.out.println("2의 배수인 요소들의 합 : " + sum);

        double avg = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .average()
                .getAsDouble();
        System.out.println("2의 배수인 요소들의 평균, double 타입 : " + avg);

        int max = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .max()
                .getAsInt();
        System.out.println("2의 배수인 요소들의 최댓값, double 타입 : " + max);

        int first = Arrays.stream(intArr)
                .filter(n -> n % 3 == 0)
                .findFirst()
                .getAsInt();
        System.out.println("3의 배수인 요소들 중에서 가장 첫 요소 : " + first);
    }
}
```

## 16.10 커스텀 집계
- 본 집계 메소드 외에도 프로그램화해서 다양한 집계 결과물을 만들 수 있도록 reduce() 메소드를 제공
- ![커스텀 집계](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F62e55513-b066-4dd9-a412-e5b04ec3da10%2Fimage.png)

## 16.11 수집
1. 스트림은 요소들을 필터링 또는 매핑 한 후 요소들을 수집하는 최종 처리 메소드인 collect()를 제공한다.
- 이 메소드를 사용하면 필요한 요소만 컬렉션으로 담을 수 있음
- 요소들을 그룹핑한 후 집계(리덕션)도 가능!

2. 필터링한 요소 수집
   > collect(Collect<T,A,R> collector)
   - Stream의 collect 메소드는 필터링 또는 매핑된 요소들을 새로운 컬렉션에 수집하고, 이 컬렉션을 리턴한다.
   - 매개값 Collector 
     - 어떤 요소를 어떤 컬렉션에 수집할 것인지 결정
     - Colletor의 타입 파라미터 **T**는 요소
     - **A**는 누적기 (accumulator)
     - **R**은 요소가 저장될 컬렉션
     - > 해석하면, T요소를 A 누적기가 R에 저장한다는 의미
     - Collector 클래스의 정적 메소드
     - ![정적메소드](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F53be3d50-bb87-4f10-a3fe-cd41fa03e241%2Fimage.png)


3. 사용자 정의 컨테이너에 수집
   - 스트림은 요소들을 필터링or매핑하여 사용자 정의 컨테이너 객체에 수집할 수 있도록 함
   - 이를 위해 추가적인 collector 메소드들이 있음
   - ![추가메소드](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F1fb00d87-9c9e-4185-af2b-737920a0f1e9%2Fimage.png)

4. 요소를 그룹핑해서 수집
   - collect() 호출 시 Collectors의 groupingBy() 또는 groupingByConcurrent()가 리턴하는 Collector를 매개값 대입
     - 컬렉션 요소들을 그루핑해서 Map 객체 생성
     - ![추가자료](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F8a1f4105-e6ce-4ee4-87f3-8bda480d99d3%2Fimage.png)

5. 그루핑 후 매핑 및 집계
   - Collectors.groupingBy()메소드
    - 그룹핑 후, 매핑이나 집계(평균, 카운팅, 연결, 최대, 최소, 합계)를 할 수 있도록 두 번째 매개값으로 Collector를 가질 수 있는 특성
      - ![추가자료](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2Fbc0debad-031c-4592-8a18-b7805aafbc3e%2Fimage.png)


## 16.12 병렬 처리
- 멀티코어 CPU 환경에 쓰임
- 하나의 작업을 분할해서 각각의 코어가 병렬적으로 처리하는 것
- 병렬 처리의 목적 : 작업 시간 줄이기 :)
- 자바 8부터 요소를 병렬 처리할 수 있도록 병렬 스트림을 제공한다.

### 동시성과 병렬성
- 동시성 (Concurrency) : 멀티 작업을 위해 **멀티 스레드**가 번갈아가며 실행하는 성질
- 병렬성 (Parallerliism) : 멀티 작업을 위해 **멀티 코어**를 이용해서 동시에 실행하는 성질

    - 싱글 코어 CPU를 이용한 멀티 작업
      - 병렬적으로 실행되는 것처럼 보이지만, 실제로는 번갈아가며 실행하는 동시성 작업
  
### 동시성과 병렬성의 비교 
![비교](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F439e49f8-ae2b-41ae-893d-a0b5874f75be%2Fimage.png)


### 병렬성의 종류
1. 데이터 병렬성
   1. 전체 데이터를 쪼개서 서브 데이터들로 만든 뒤, 병렬 처리하여 작업을 빨리 끝냄
   2. 자바 8에서 지원하는 병렬 스트림은 데이터 병렬성을 구현한 것
   3. 멀티 코어의 수많은 대용량 요소를 서브 요소들로 나누고,
   4. 각각의 서브 요소들을 분리된 스레드에서 병렬처리
2. 작업 병렬성
   1. 작업 병렬성은 서로 다른 작업을 병렬 처리하는 것
   2. eg.웹서버 
      1. 각각의 브라우저에서 요청한 내용을 개별 스레드에서 병렬로 처리

### 포크 조인 프레임워크
1. 런타임 시 포크조인 프레임워크 동작
2. 포크 단계에서는 전체 데이터를 서브 데이터로 분리
3. 서브 데이터를 멀티 코어에서 병렬로 처리
4. 조인 단계에서는 서브 결과를 결합해서 최종 결과 도출

### 포크 조인 프레임워크의 원리
![포크조인](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2F75994d55-bf2d-4e32-9906-4527d560ef8c%2Fimage.png)
- ForkJoinPool 사용해 작업 스레드 관리

### 병렬 스트림 생성
- 코드에서 포크조인 프레임워크 사용해도 병렬처리 가능
- 병렬 스트림 이용할 경우 백그라운드에서 포크조인 프레임워크 동작
  - 매우 쉽게 구현해 사용 가능하다.
- 병렬 스트림을 얻는 메소드
  - parallelStream() 메소드
    : 컬렉션으로부터 병렬 스트림을 바로 리턴
  - parallel() 메소드
    : 순차 처리 스트림 병렬 처리 스트림으로 변환해서 리턴


### 병렬 처리가 정상적으롤 이루어질 때 CPU의 상태
- 쿼드코어 CPU
- ![쿼드코어](https://velog.velcdn.com/images%2Fansalstmd%2Fpost%2Fddca5df3-0d76-43fc-8e81-e02d3d4d57ac%2Fimage.png)

### 병렬처리 성능
- 병렬 처리에 영향을 미치는 3가지 요인
1. 요소의 수와 요소당 처리 시간 : 요소 수가 적고 요소당 처리 시간 짧으면 순차 처리가 빠름
2. 스트림 소스의 종류 : ArrayList, 배열은 인덱스로 요소 관리 -> 병렬처리가 빠름
3. 코어(Core)의 수 : 싱글 코어의 경우 순차 처리가 빠름












