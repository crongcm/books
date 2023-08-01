# 이펙티브 자바

### Joshua Bloch

> 이펙티브 자바 3/E - 프로그래밍인사이트

![book](https://contents.kyobobook.co.kr/sih/fit-in/458x0/pdt/9788966262281.jpg)

예제 코드 : [https://github.com/WegraLee/effective-java-3e-source-code](https://github.com/WegraLee/effective-java-3e-source-code)

## 아주 핵심적인 기본 원칙
- 명료성(clarity)
- 단순성(simplicity)


컴포넌트는 정해진 동작이나 예측할 수 있는 동작만 수행해야 한다.

컴포넌트는 가능한 한 작되, 그렇다고 너무 작아서는 안 된다.<br/>
(컴포넌트: 개별 메서드부터 여러 패키지로 이뤄진 프레임워크까지 재사용 가능한 모든 SW 요소)

코드는 복사되는 게 아니라 재사용되어야 한다.

컴포넌트 사이의 의존성은 최소로 유지해야 한다.

오류는 만들어지자마자 가능한 한 빨리 잡아야 한다.

성능에 집중하는 부분은 많지 않다.<br/>
프로그램을 명확하고, 정확하고, 유용하고, 견고하고, 관리하기 쉽게 짜는데 집중한다.


## 2장 | 객체 생성과 파괴

- 객체를 만들어야 할 때와 만들지 말아야 할 때를 구분하는 법
- 올바른 객체 생성 방법과 불필요한 생성을 피하는 방법
- 제때 파괴됨을 보장하고 파괴 전에 수행해야 할 정리 작업을 관리하는 요령

[Item 1. 생성자 대신 정적 팩터리 메서드를 고려하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item1.md)

[Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라.](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item2.md)

[Item 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item3.md)

[Item 4. 인스턴스화를 막으려거든 private 생성자를 사용하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item4.md)

[Item 5. 자원을 직접 명시하지 않고 의존 객체 주입을 사용하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item5.md)

[Item 6. 불필요한 객체 생성을 피하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter02/item6.md)

## 3장 | 모든 객체의 공통 메서드

[Item 10. equlas는 일반 규약을 지켜 재정의하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter03/item10.md)

[Item 11. equals를 재정의하려거든 hashCode도 재정의하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter03/item11.md)

[Item 12. toString을 항상 재정의하라](https://github.com/crongcm/books/blob/main/effective%20java/chapter03/item12.md)

[Item 13. clone 재정의는 주의해서 진행하라.](https://github.com/crongcm/books/blob/main/effective%20java/chapter03/item13.md)
