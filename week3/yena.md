# 9. 중첩 클래스와 중첩 인터페이스

## 주요 키워드 및 개념 정리

#### 중첩 클래스(Nestd Class)

클래스 내부에 선언한 클래스. 두 클래스의 멤버들을 서로 쉽게 접근할 수 있고, 외부에 불필요한 관계 클래스를 감춤으로써 코드의 복잡성을 줄인다.

- 멤버 클래스
  - 인스턴스 멤버 클래스
  - 정적 멤버 클래스
- 로컬 클래스

#### 중첩 인터페이스

클래스 멤버로 선언한 인터페이스. 해당 클래스와 긴밀한 관계를 맺는 구현 클래스를 만들기 위해 사용한다.

- 주로 UI 프로그래밍에서 이벤트 처리 목적으로 활용

#### 로컬 클래스의 매개 변수, 로컬 변수가 final 특성을 갖는 이유

메소드의 매개 변수나 로컬 변수를 로컬 클래스에서 사용할 때 발생하는 문제를 해결하기 위함이다.

**문제:** 로컬 클래스의 **객체는 힙 메모리**에 존재해 계속 사용할 수 있고, **매개 변수나 로컬 변수는 스택 영역**에 저장되어 메소드의 실행이 끝나면 메모리에서 사라지기 때문에 로컬 객체에서 사용하면 문제가 발생한다.

**해결:** 자바는 컴파일 시 로컬 클래스에서 사용하는 매개 변수나 로컬 변수의 값을 로컬 클래스 내부에 복사해 두고 사용한다. 이 때 값이 동일하게 유지되도록 매개 변수나 로컬 변수를 final로 선언한다.

#### 익명 객체

단독으로 생성할 수 없고 클래스를 상속하거나 인터페이스를 구현해서 사용하는 객체이다. 필드, 로컬 변수의 초기값과 매개 변수의 매개값으로 대입된다.

#### 익명 객체 사용 이유

자식 클래스 또는 구현 클래스가 재사용되지 않고 초기값으로만 사용되는 경우 익명 객체를 사용해 초기값으로 대입하는 것이 좋다.

#### 익명 객체의 로컬 변수 사용

메소드의 매개 변수나 로컬 변수를 익명 객체에서 사용할 때, 메소드 실행이 끝나면 익명 객체에서 매개 변수나 로컬 변수를 사용할 수 없게 되므로 문제 발생. _(익명 객체는 힙 메모리, 매개 변수나 로컬 변수는 스택 메모리. 해결 방법은 로컬 클래스 final... 내용과 동일)_

## 관심 가는 내용

#### 중첩 클래스의 접근 제한

- 바깥 클래스의 필드와 메소드에서 사용 제한: 인스턴스 멤버 클래스는 바깥 클래스의 **정적 필드** 초기값이나 **정적 메소드**에서는 **객체를 생성**할 수 없다. _(정적멤버클래스는 객체 생성 제한X)_

- 멤버 클래스에서 사용 제한: 정적 멤버 클래스 안에서는 바깥 클래스의 **정적 필드**와 **정적 메소드에만 접근**할 수 있고, 인스턴스 필드와 메소드는 접근할 수 없다. _(인스턴스 멤버 클래스는 접근 제한X)_

#### 중첩 클래스 this.

중첩 클래스에서 this 키워드를 사용하면 바깥 클래스의 객체를 참조한다.

# 10. 예외 처리

## 주요 키워드 및 개념 정리

#### 에러(Error)

컴퓨터 하드웨어의 오동작 또는 고장으로 응용프로그램 실행 오류가 발생하는 것.

#### 예외(Exception)

사용자의 잘못된 조작 또는 개발자의 잘못된 코딩으로 인해 발생하는 프로그램 오류.

- 일반 예외(Exception)
  - 자바 소스 컴파일 과정에서 예외 처리 코드가 필요한지 검사
  - java.lang.Exception 상속
- 실행 예외(Runtime Exception)
  - 컴파일 과정에서 예외 처리 코드 검사하지 않는 예외
  - java.lang.Exception, java.lang.RuntimeException 상속

#### 자주 발생 하는 실행 예외

- NullPointerException
  - 객체 참조가 없는 상태. null 값을 갖는 참조 변수로 객체를 접근할 때 발생.
- ArrayIndexOutOfBoundsException
  - 배열에서 인덱스 범위를 초과하여 사용할 경우 발생. _(배열값 읽기 전 배열 길이를 먼저 조사 하기)_
- NumberFormatException
  - 문자열을 숫자로 변경할 때 숫자로 변환될 수 없는 문자가 포함되어 있으면 발생.
- ClassCastException
  - 억지로 타입 변환을 시도할 경우 발생. _(타입 변환 전 instanceOf 연산자로 확인)_

#### 예외 처리 코드

예외가 발생했을 경우 프로그램의 갑작스러운 종료를 막고 **정상 실행을 유지**하도록 처리하는 코드이다.

#### try-catch-finally

생성자 내부와 메소드 내부에서 작성된다. 다중 catch 블록 작성 시 상위 예외 클래스를 제일 아래쪽에 작성 한다.

- try 블록: 예외 발생 코드
- catch 블록: 예외 처리 코드
- finally 블록: 생략 가능. try, catch 블록에서 return문을 사용해도 항상 실행된다.

#### 자동 리소스 닫기 try-with-resource

예외 발생 여부와 상관없이 (try블록이 실행 완료 했거나 예외가 발생하면) 자동으로 사용했던 리소스 객체의 close()를 호출해서 안전하게 리소스를 닫아준다.

> 리소스 객체가 AutoClosealbe 인터페이스(close() 정의되어 있음)를 구현하고 있어야 한다.

#### 예외 떠넘기기 throws

메소드에서 처리하지 않은 예외를 메소드를 호출한 곳으로 예외를 떠넘긴다.

> throw 키워드 뒤는 떠넘길 예외 클래스 나열

#### 사용자 정의 예외

애플리케이션 서비스와 관련된 예외여서 애플리케이션 예외(Application Exception)라고도 한다.

- 일반 예외로 선언: Exception 상속
- 실행 예외로 선언: RuntimeException 상속

#### 사용자 정의 예외 사용

사용자 정의 예외 클래스 이름은 Exception으로 끝나도록 한다. 대부분 두 개의 생성자만 갖고 있다.

- 기본 생성자
- String 타입의 매개 변수를 갖는 생성자: 예외 메시지 전달. 상위 클래스의 생성자를 호출해 예외 메시지를 넘겨준다. 예외 메시지는 catch 블록의 예외 처리코드에서 이용.

#### 예외 정보 얻기

try 블록에서 예외가 발생되면 예외 객체는 cath 블록의 매개 변수에서 참조하게 되므로, 매개 변수를 이용해 예외 객체의 정보를 알 수 있다.

모든 예외는 Exception을 상속 -> Exception의 메소드인 getMessage(), printStackTrace() 사용 가능

- getMassage(): 예외 메시지 리턴.
- printStackTrace(): 예외 발생 코드 추적해 상세히 콘솔에 출력.

## 관심 가는 내용

#### 멀티 catch

하나의 catch 블록에서 여러 개의 예외를 처리 하는 기능. _(예외 `|` 로 연결)_

#### throws

throws가 붙은 메소드는 반드시 try 블록 내에서 호출되어야 하고, catch 블록에서 떠넘겨 받은 예외를 처리 한다.

#### 예외 발생시키기

throw new 연산자로 예외 객체를 생성한다.  
예외를 발생시킨 메소드 내부에서 try-catch 블록으로 예외 처리할 수 있지만, 대부분 호출한 곳에서 예외 처리 하도록 throws로 예외를 떠넘긴다.