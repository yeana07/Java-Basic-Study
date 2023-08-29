# 15. 컬렉션 프레임워크

## 주요 키워드 및 개념 정리

#### 컬렉션 프레임워크 Collection Framework

자바는 배열의 문제점을 해결하고 자료구조를 바탕으로 객체들을 효율적으로 추가, 삭제, 검색 하도록 java.util 패키지에 컬렉션과 관련된 컬렉션 프레임워크를 제공한다.

> 배열의 문제점: 저장할 객체 수가 배열 생성 시 결정됨, 객체 삭제 시 해당 인덱스가 빈 상태로 있음

#### 컬렉션 프레임워크 주요 인터페이스

컬렉션 프레임워크의 주요 인터페이스로는 List, Set, Map이 있다. List, Set은 공통 메소드만 모아 Collection 인터페이스로 정의해 두고 있다.

#### 인터페이스별 컬렉션 특징 정리

- Colletion
  - List
    - 순서를 유지하고 저장
    - 중복 저장 가능
    - 구현 클래스: ArrayList, Vector, LinkedList
  - Set
    - 순서 유지 않고 저장
    - 중복 저장 안 됨
    - 구현 클래스: HashSet, TreeSet
- Map
  - 키와 값 쌍으로 저장
  - 키는 중복 저장 안 됨
  - HashMap, HashTable, TreeMap, Properties

### List 컬렉션

List 컬렉션은 객체를 인덱스로 관리하고, 객체 자체를 저장하는 것이 아니라 객체의 번지를 참조한다.

동일한 객체 중복 저장하면 동일한 번지를 참조한다. null을 저장하면 해당 인덱스는 객체를 참조하지 않는다.

#### List 컬렉션 인터페이스의 공통 메소드들

인덱스로 객체 관리

- 객체 추가
  - `boolean add(E e)`: 주어진 객체 맨 끝에 추가
  - `void add(int index, E element)`
  - `E set(int index, E element)`
- 객체 검색
  - `boolean contains(Object o)`
  - `E get(int index)`
  - `boolean isEmpty()`
  - `int size()`
- 객체 삭제
  - `void clear()`
  - `E remove(int index)`
  - `boolean remove(Obejct o)`

#### ArrayList

ArrayList는 저장 용량(capacity)를 초과한 객체들이 들어오면 자동적으로 저장 용량이 늘어난다. (기본 초기 용량: 10)

ArrayList에서 특정 인덱스의 객체를 삭제하면 바로 뒤 인덱스부터 앞으로 1씩 당겨진다. (인덱스 삽입도 마찬가지로 1씩 밀려남) -> 객체의 삽입, 삭제가 빈번하면 ArrayList 사용X -> 인덱스 검색, 맨 마지막에 순차적으로 객체 추가 또는 삭제 하는 경우 사용

#### Vector

Vector는 ArrayList와 동일한 내부 구조를 가진다. Vector는 **동기화된(synchronized) 메소드**로 구성되어 멀티 스레드 환경에서 안전하게 객체를 추가, 삭제할 수 있다. -> 스레드가 안전(Thread Safe)

#### LinkedList

LinkedList는 ArrayList와와 내부 구조가 완전 다르다. LinkedList는 인접 참조를 링크해서 체인처럼 관리한다.

특정 인덱스의 객체를 삭제하면 앞뒤 링크만 변경된다.(객체 삽입도 마찬가지) 때문에 빈번한 객체 삭제, 삽입이 일어나는 곳에서 좋은 성능을 발휘한다.

LinkedList가 처음 생성되면 어떤 링크도 없기 때문에 비어 있다고 보면 된다.

### Set 컬렉션

Set 컬렉션은 저장 순서를 유지하지 않고, 객체를 중복해서 저장할 수 없다. 그래서 하나의 null만 저장한다. (인덱스 사용X)

#### Set 컬렉션 인터페이스의 공통 메소드들

인덱스 없음

- 객체 추가
  - `boolean add(E e)`
- 객체 검색
  - `boolean contains(Object o)`
  - `boolean isEmpty()`
  - `Iterator<E> iterator()`: 저장된 객체를 한 번씩 가져오는 반복자 리턴
  - `int size()`
- 객체 삭제
  - `void clean()`
  - `boolean remove(Obejct o)`

#### Set 컬렉션의 반복자 Iterator

반복자는 Iterator 인터페이스를 구현한 객체를 말하는데, `iterator()` 메소드를 호출하면 얻을 수 있다.

```java
Set<String> set = ...;
Iterator<String> iterator = set.iterator();
```

#### Iterator 인터페이스 메소드들

- `boolean hasNext()`: 가져올 객체가 있으면 true, 없으면 false 리턴
- `E next()`: Set 컬렉션에서 하나의 객체 가져옴
- `void remove()`

-> 먼저 `hasNext()` 사용해 가져올 객체가 있는지 확인하고 true면 `next()`사용

#### HashSet

HashSet은 객체들을 순서 없이 저장하고 동일한 객체는 중복 저장하지 않는다. HashSet에서 동일한 객체란 `hashCode()`의 값과 동일한 해시코드가 있고 `equals()`의 값이 true인 객체를 말한다. (객체 저장전 비교)

문자열을 HashSet에 저장하면 같은 문자열을 갖는 String 객체는 동등한 객체로 간주되도록 String 클래스가 `hashCode()`와 `equals()`를 재정의 하고 있다.

필요 시 사용자 정의 클래스를 만들고 `hashCode()`, `equals()`를 재정의 해서 사용하면 된다.

### Map 컬렉션

Map 컬렉션은 키와 값으로 구성된 Entry 객체를 저장하는 구조를 가지고 있다. 키와 값은 모두 객체이고, 기존 저장된 키와 동일한 키로 값을 저장하면 새로운 값으로 대치된다.

#### Map 컬렉션 공통 메소드들

키로 객체를 관리

- 객체 추가
  - `V put<K key, V value>`: 주어진 키로 값 저장, 새로운 키면 null 리턴, 동일 키 있으면 값 대치 후 이전 값 리턴
- 객체 검색
  - `boolean containsKey(Object key)`
  - `boolean containsValue(Object value)`
  - `Set<Map.Entry<K,V>> entrySet()`
  - `V get(Object key)`: 주어진 키의 값 리턴
  - `boolean isEmpty()`
  - `Set<K> keySet()`
  - `int size()`
  - `Colletion<v> values()`
- 객체 삭제
  - `void clear()`
  - `V remove(Object key)`: 주어진 키의 Map.Entry를 삭제하고 값 리턴

#### 전체 객체 하나씩 가져오기

- `keySet()`사용
  1. `keySet()`으로 모든 키를 Set 컬렉션으로 얻기
  2. 반복자로 키 하나씩 얻기
  3. `get()`으로 값 얻기
- `entrySet()`사용
  1. `entrySet()`으로 모든 Map.Entry객체를 Set 컬렉션으로 얻기
  2. 반복자로 Map.Entry 객체 하나씩 얻기
  3. `getKey()`, `getValue()`로 키와 값 얻기

#### HashMap

HashMap은 대표적인 Map 컬렉션이다. 키로 사용할 객체는 `HashCode()`와 `equals()`를 재정의해 동등 객체가 될 조건을 정해야 한다.

주로 키 타입은 String을 많이 사용한다.

키와 값이 타입은 기본 타입은 사용할 수 없고, 클래스 및 인터페이스 타입만 가능하다.

#### HashTable

HashTable은 HashMap과 동일한 내부 구조를 가진다. 키로 사용할 객체는 `HashCode()`와 `equals()`를 재정의해 동등 객체가 될 조건을 정해야 한다.

HashTable은 **동기화된(synchronized) 메소드**로 구성되어 있어 멀티 스레드 환경에서 안전하게 객체를 추가, 삭제할 수 있다.

#### Properties

Properties는 HashTable의 하위 클래스이기 때문에 모든 특징을 그대로 가진다.

차이점으로 Properties는 키와 값을 String 타입으로 제한한 컬렉션이다. ( 키와 값 `=`로 연결)

Properties는 애플리케이션 옵션 정보, DB 연결 정보, 다국어 정보가 저장된 프로퍼티 파일을 읽을 때 주로 사용한다.

### 검색 기능을 강화시킨 컬렉션

컬렉션 프레임워크는 검색 기능을 강화 시킨 TreeSet과 TreeMap을 제공한다. 이진 트리(Binary Tree)
를 이용해 계층적 구조(Tree)구조를 가지며 객체를 저장한다.

#### 이진 트리 구조

첫 번째로 저장되는 값이 루트 노드. 다음 저장되는 값은 루트 노드 부터 값 크기를 비교 하며 트리를 따라 내려간다. (문자는 유니코드 비교)

왼쪽 마지막 노드가 제일 작은 값, 오른쪽 마지막 노드가 가장 큰 값이 된다.

- 왼쪽 마지막 노드 부터 읽기 -> 오름차순 정렬된 값
- 오른쪽 마지막 노드 부터 읽기 -> 내림차순 정렬된 값

#### TreeSet

이진 트리 기반 Set 컬렉션이다. 하나의 노드는 노트값인 value와 왼쪽 오른쪽 자식 노드를 참조하기 위한 두 개의 변수로 구성된다. TreeSet에 객체를 저장하면 자동 정렬된다.

#### TreeSet 컬렉션 검색 관련 메소드

TreeSet 객체 생성 시 Set 객체가 아닌 Set 인터페이스 변수에 대입해서 검색 관련 메소드 사용하기.

- `E first()`
- `E last()`
- `E lower(E e)`
- `E higer(E e)`
- `E floor(E e)`
- `E ceiling(E e)`
- `E pollFirt()`
- `E pollLast()`

#### TreeSet 컬렉션 정렬 관련 메소드들

- `Iterator<E> descendingIteraotr()`
- `NavigatorSet<E> descendingSet()`

NavigatorSet은 treeSet과 마찬가지로 검색 관련 메소드들을 제공하고, 정렬 순서를 바꾸는 `descendingSet()`도 제공한다. `descendingSet()`을 두 번 호출하면 오름차순 정렬한다.

#### TreeSet 컬렉션 범위 검색 관련 메소드들

- `NavigatorSet<E> headSet(E toElement, boolean inclusive)`
- `NavigatorSet<E> tailSet(E fromElement, boolean inclusive)`
- `NavigatorSet<E> subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive)`: 주어진 객체 사이의 객체들을 NavigatorSet으로 리턴

#### TreeMap

TreeMap은 이진트리를 기반으로 한 Map 컬렉션이다. 키와 값이 저장된 Map.Entry를 저장한다. 객체를 저장하면 자동 정렬한다.

#### TreeMap 컬렉션 검색 관련 메소드들

Map타입 변수 말고 TreeMap 클래스 타입 변수에 트리맵 객체 대입하기.

메소드들의 리턴 타입은 모두 `Map.Entry<K, V>`

- `firstEntry()`
- `lastEntry()`
- `lowerEntry(K key)`
- `higerEntry(K key)`
- `floorEntry(K key)`
- `ceilingEntry(K key)`
- `pollFirtEntry()`
- `pollLastEntry()`

#### TreeMap 정렬과 관련된 메소드들

- `NavigablerSet<E> descendingKeySet()`
- `NavigableMap<E> descendingMap()`

NavigableMap 객체는 TreeMap의 검색 관련 메소드들과 정렬 순서를 바꾸는 메소드를 제공한다.

#### TreeMap 범위 검색 관련 메소드들

- `NavigableMap<E> headMap(K toKey, boolean inclusive)`
- `NavigableMap<E> tailMap(K fromKey, boolean inclusive)`
- `NavigableMap<E> subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive)`:

#### Comparable과 Comparator

TreeSet객체, TreeMap의 키는 저장과 동시에 자동 오름차순으로 정렬된다.

TreeSet, TreeMap은 정렬을 위해 java.lang.Comparable을 구현한 객체를 요구한다. (Integer, Double, String 모두 Comparable을 구현)

사용자 정의 클래스도 Comparable을 구현하고 `compareTo()`메소드를 오버라이딩하면 자동 정렬이 가능하다.

- `compareTo(T o)`: 리턴값 -> 주어진 객체와 같으면 0, 작으면 음수, 크면 양수

Comparable을 구현 하지 않는 TreeSet, TreeMap을 정렬하려면 생성자의 매개값으로 정렬자(Comparator)를 제공하면 된다.

정렬자는 Comparator를 구현한 객체로, `compare()` 메소드가 있다.

- `compare(T o1, T o2)`: o1과 o1이 동등하면 0, o1이 앞에 오게 하려면 음수를, o1이 뒤에 오게 하려면 음수 리턴

### LIFO와 FIFO 컬렉션

컬렉션 프레임워크에는 LIFO 자료구조를 제공하는 스택(Stack) 클래스와 FIFO 자료구조를 제공하는 큐(Queue) 인터페이스를 제공한다.

- 스택 응용 예: JVM 스택 메모리
- 큐 응용 예: 스레드풀(ExcecutorService)의 작업 큐

#### Stack 클래스 주요 메소드들

- `E push(E item)`
- `E peek()`
- `E pop()`

#### Queue 인터페이스 주요 메소드들

- `boolean offer(E e)`
- `E peek()`
- `E poll()`

Queue 인터페이스를 구현한 대표적인 클래스가 LinkedList이다. (List 컬렉션 이면서 Queue 구현)

### 동기화된 컬렉션

ArrayList, HashSet, HashMap은 멀티 스레드 환경에서 안전하지 않다. 싱글 스레드 환경에서 사용하다가 멀티 스레드 환경으로 전달하는 경우 문제가 발생한다.

컬렉션 프레임워크는 비동기화된 메소드를 동기화된 메소드로 래핑하는 Collection의 `synchronizedXXX()` 메소드를 제공한다. 매개값으로 비동기화된 컬렉션을 대입하면 된다.

- `List<T> synchronizedList(List<T> list)`
- `Map<K,V> synchronizedMap(Map<K,V> m)`
- `Set<T> synchronizedSet(Set s)`

### 병렬 처리를 위한 컬렉션

동기화된 컬렉션은 멀티 스레드 환경에서 전체 요소를 빠르게 처리하지는 못한다. 하나의 스레드가 요소를 처리할 때 **전체 잠금**이 발생해 다른 스레드는 대기 상태가 된다.  
-> 병렬적으로 컬렉션 요소 처리 못 함

자바는 멀티 스레드가 컬렉션의 요소를 병렬적으로 처리 하도록 특별한 컬렉션을 제공한다.  
-> java.util.concurrent 패키지의 `ConcurrentHashMap`, `ConcurrentLinkesQueue`

`ConcurrentHashMap`은 **부분(segment)잠금**을 사용한다. 처리 하는 요소가 포함된 부분만 잠금하고 나머지 부분은 변경 가능.

`ConcurrentLinkesQueue`은 **락-프리(lock-free) 알고리즘**을 구현한 컬렉션이다. 여러 개의 스레드가 동시 접근할 경우, 잠금을 사용하지 않고도 최소한 하나의 스레드가 안전하게 요소를 저장하거나 얻도록 해준다.

## 관심 가는 내용

#### 고정된 객체들로 구성된 List 생성하기

`Arrays.List(T...a)`를 사용하기. T 타입 파라미터에 맞게 매개값을 순차적으로 입력하거나 `T[]`배열을 매개값으로 주면된다.

#### 전체 객체 하나씩 가져오기 - 코드

- `keySet()`사용
  ```java
  Map<K,V> map = ~;
  Set<K> keySet = map.keySet();
  Itertor<K> keyIterator = keySet.iterator();
  while(keyIterator.hasNext()) {
    K key = keyIterator.next();
    V value = map.get(key);
  }
  ```
- `entrySet()`사용
  ```java
  Set<Map.Entry<K, V>> entrySet = map.entrySet();
  Iterator<Map.Entry<K, V>> entryIterator = entrySet.iterator();
  while(entryIterator.hasNext()) {
    Map.Entry<K, V> entry = entryIterator.next();
    K key = entry.getKey();
    V value = entry.getValue();
  }
  ```

#### 프로퍼티 파일 읽기

1. Proeperties 객체를 생성
2. 프로퍼티 파일로 부터 데이터를 읽는 FileReader객체를 매개값으로 갖는 load() 호출
