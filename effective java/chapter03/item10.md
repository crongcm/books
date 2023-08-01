# Item 10. equlas는 일반 규약을 지켜 재정의하라

- 다음의 경우에 해당한다면 equals를 재정의 할 필요가 없다.
- 각 인스턴스가 본질적으로 고유하다.
- 인스턴스의 `논리적 동치성`을 검사할 필요가 없다.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 적절하다.
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.

### 핵심정리 - euqals 규약

- 반사성 : A.equals(A) == true
- 대칭성 : A.equals(B) == B.equals(A)
    - CaselnsensitiveString
- 추이성 : A.euqals(B) && B.equals(C), A.equals(C)
    - point, ColorPoing(inherit), CounterPointer, ColorPoint(comp)
- 일관성 : A.equals(B) == B.equals(B)
- null-아님 : A.equals(null) == false

### 핵심정리 - equals 구현 방법

- == 연산자를 사용해 자기 자신의 참조인지 확인한다.
- instanceof 연산자로 올바른 타입인지 확인한다.
- 입력된 값을 올바른 타입으로 형변환 한다.
- 입력 객체와 자기 자신의 대응되는 핵심 필드가 일치하는지 확인한다.

- 구글의 AutoValue 또는 Lombok을 사용한다.
- IDE의 코드 생성 기능을 사용한다.
- 과제) 자바의 Record를 공부하세요.

    ```java
    double, float(부동소수점) - Double.compare(A)
    primitive - ==
    reference - A.equals(B)
    null - Objects.equals(A, B)
    ```

- equals를 재정의 할 때 hashCode도 반드시 재정의하자.(아이템 11)
- 너무 복잡하게 해결하지 말자.
- Object가 아닌 타입의 매개변수를 받는 equals 메서드는 선언하지 말자.


- 완벽 공략 24 - Value 기반의 클래스

  클래스처럼 생겼지만 int 처럼 동작하는 클래스

    - 식별자가 없고 불변이다.
    - 식별자가 아니라 인스턴스가 가지고 있는 상태를 기반으로 equals, hashCode, toString을 구현한다.
    - == 오퍼레이션이 아니라 equals를 사용해서 동등성을 비교한다.
    - 동일한(equals) 객체는 상호교환 가능하다.


- 완벽 공략 25 - StackOverflowError

  로컬 변수와 객체가 저장되는 공간의 이름은?

    - 스택(stack)과 힙 (heap)
    - 메소드 호출시, 스택에 스택 프레임이 쌓인다.
        - 스택 프레임에 들어있는 정보: 메소드에 전달하는 매개변수, 메소드 실행 끝내고 돌아갈 곳, 힙에 들어있는 객체에 대한 레퍼런스…
        - 그런데 더이상 스택 프레임을 쌓을 수 없다면? StackOverflowError!
    - 스택의 사이즈를 조정하고 싶다면? -Xss1M


- 완벽 공략 26 - 리스코프 치환 원칙

  객체 지향 5대 원칙 SOLID 중에 하나

    - 1994년, 바바라 리스코프의 논문, “A Behavioral Notion of Subtyping”에서 기원한 객체 지향 원칙.
    - `하위 클래스의 객체`가 `상위 클래스 객체`를 대체하더라도 소프트웨어의 기능을 깨트리지 않아야 한다. (semantic over syntacic,구문보다는 의미!)
