# Item 13. clone 재정의는 주의해서 진행하라.

### **핵심 정리: 애매모호한 clone 규약**

- clone 규약
    - x.clone() != x 반드시 true
    - x.clone().getClass() == x.getClass() 반드시 true
    - x.clone().equals(x) true가 아닐 수도 있다.
- 불변 객체라면 다음으로 충분하다.
    - Cloenable 인터페이스를 구현하고
    - clone 메서드를 재정의한다. 이때 super.clone()을 사용해야 한다.


### 핵심 정리: 가변 객체의 clone 구현하는 방법

- 접근 제한자는 public, 반환 타입은 자신의 클래스로 변경한다.
- super.clone을 호출한 뒤 필요한 필드를 적절히 수정한다.
    - 배열을 복제할 때는 배열의 clone 메서드를 사용하라.
    - 경우에 따라 final을 사용할 수 없을지도 모른다.
    - 필요한 경우 deep copy를 해야한다.
    - super.clone으로 객체를 만든 뒤, 고수준 메서드를 호출하는 방법도 있다.
    - 오버라이딩 할 수 있는 메서드는 참조하지 않도록 조심해야 한다.
    - 상속용 클래스는 Cloneable을 구현하지 않는 것이 좋다.
    - Cloneable을 구현한 스레드 안전 클래스를 작성할 때는 동기화를 해야 한다.

### 핵심 정리: clone 대신 권장하는 방법

- “복사 생성자” 또는 변환 생성자, “복사 팩터리” 또는 변환 팩터리.
- 생성자를 쓰지 않으며, 모호한 규약, 불필요한 검사 예외, final 용법 방해 등에서 벗어날 수 있다.
- 또 다른 큰 장점 중 하나로 인터페이스 타입의 인스턴스를 인수로 받을 수 있다.
    - 클라이언트가 복제본의 타입을 결정할 수 있다.
- **읽어볼 것) Josh Bloch on Design, “Copy Constructor versus Cloning”**


- 완벽 공략 29 - UncheckedException

  왜 우리는 비검사 예외를 선호하는가?

    - 컴파일 에러를 신경쓰지 않아도 되며,
    - try-catch로 감싸거나
    - 메서드 선언부에 선언하지 않아도 된다.
    - 그렇다면 우리는 비검사 예외만 쓰면 되는걸까? 검사 예외는 왜 있는것일까?
- 왜 잡지 않은 예외를 메서드에 선언해야 하는가?
    - 메서드에 선언한 예외는 `프로그래밍 인터페이스의 일부`다. 즉, 해당 메서드를 사
      용하는 코드가 반드시 알아야 하는 정보다. 그래야 해당 예외가 발생했을 상황에
      대처하는 코드를 작성할 수 있을테니까.
- 비검사 예외는 그럼 왜 메서드에 선언하지 않아도 되는가?
    - 비검사 예외는 어떤 식으로든 처리하거나 복구할 수 없는 경우에 사용하는 예외
      다. 가령, 숫자를 0으로 나누거나, null 레퍼런스에 메서드를 호출하는 등.
    - 이런 예외는 프로그램 전반에 걸쳐 어디서든 발생할 수 있기 때문에 이 모든 비
      검사 예외를 메서드에 선언하도록 강제한다면 프로그램의 명확도가 떨어진다.
- 단순히 처리하기 쉽고 편하다는 이유만으로 RuntimeException을 선택하지는 말
  자.
- 가이드라인: 클라이언트가 해당 예외 상황을 복구할 수 있다면 검사 예외를 사용하
  고, 해당 예외가 발생했을 때 아무것도 할 수 없다면, 비검사 예외로 만든다.
- https://docs.oracle.com/javase/tutorial/essential/exceptions/
  runtime.html


- 완벽 공략 30 - TreeSet

  AbstractSet을 확장한 정렬된 컬렉션

    - 엘리먼트를 추가한 순서는 중요하지 않다.
    - 엘리먼트가 지닌 자연적인 순서(natural order)에 따라 정렬한다.
    - 오름차순으로 정렬한다.
    - 스레드 안전하지 않다.
    - 과제) 이진 검색 트리, 레드 블랙 트리에 대해 학습하세요.
