# Item 1. 생성자 대신 정적 팩터리 메서드를 고려하라

클래스는 생성자와 별도로 정적 팩터리 메서드(static factory method)를 제공할 수 있다.

- 클래스의 인스턴스를 반환하는 단순한 정적 메서드

❗디자인 패턴에서의 팩터리 메서드(Factory Method)와 다르다.

정적 팩터리 메서드란 ?

```java
public static Boolean valueOf(boolean b) {
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

정적 팩터리 메서드

- 장점
1. 이름을 가질 수 있다.
    - 한 클래스에 시그니처가 같은 생성자가 여러개 필요할 것 같으면, 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주자

2. 호출 될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
    - 반복되는 요청에 같은 객체를 반환하는 식으로 정적 팩터리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있다.
        - 불변 클래스(immutable class : item 17)는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.
        - 싱글턴(singleton : item 3)
        - 인스턴스화 불가(noninstantiable : Item 4)
        - 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있다.

          `a==b`일 때만 `a.equals(b)` 성립

        - 플라이웨이트 패턴(Flyweight pattern)

          '공유'를 통하여 다양한 객체들을 효과적으로 지원하는 방법

            - 객체 내부에서 참조하는 객체를 직접 만들지 않고, 있다면 객체를 공유하고 없다면 만들어 줌 (Pool로 관리)

          Ex. String Pool (Heap 영역의 permanant 영역에 할당): 같은 내용의 String 객체가 선언된다면 기존의 String 객체를 참조

          → 데이터를 공유하여 메모리를 절약하는 패턴

3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
    - 반환할 객체의 클래스를 자유롭게 선택할 수 있게 하는 ‘엄청난 유연성’을 선물한다.
    - `java.util.collections`
            
        프로그래머는 명시한 인터페이스대로 동작하는 객체를 얻을 것임을 알기에 굳이 별도 문서를 찾아가며 실제 구현 클래스가 무엇인지 알아보지 않아도 된다.
            
        나아가 정적 팩토리 메서드를 사용하는 클라이언트는 얻은 객체를 (그 구현 클래스가 아닌) 인터페이스만으로 다루게 된다.

4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
    - 3번의 내용과 같이, 해당 타입의 리턴하는 타입의 하위타입인 경우 리턴의 구현체로 사용 가능하다.
    - 3번 내용과 같이 클라이언트는 반환 타입의 존재를 모르고 알 필요도 없다. 그냥 해당 객체의 하위 클래스이기만 하면된다.
      - EnumSet 클래스는 public 생성자 없이 정적 팩터리만 제공하는데, OpenJDK에서는 원소의 수에 따라 두가지 하위 클래스 중 하나의 인스턴스를 반환한다.
        - 원소가 64개 이하면 원소들을 long 변수 하나로 관리하는 RegularEnumSet의 인스턴스를
        - 65개 이상이면 long 배열로 관리하는 JumboEnumSet의 인스턴스를 반환한다.
        
5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
    - ServiceLoader를 통해서 해당 인터페이스에 해당하는 구현체의 객체를 받아와 메서드를 실행 할 수 있음.
    - 이렇게 하면 구현체에 의존적이지 않고 인터페이스에 의존하게 됨 >> 많은 유연성 제공

- 단점
    1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
    2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

## 이펙티브 자바 완벽 공략

- 완벽공략 1 - 열거 타입
    - 상수 목록을 담을 수 있는 데이터 타입
    - 특정한 변수가 가질 수 있는 값을 제한할 수 있다. Type-Safety를 보장할 수 있다.
    - 싱글톤 패턴을 구현할 때 사용하기도 한다.

  질문 1) 특정 enum 타입이 가질 수 있는 모든 값을 순회하며 출력하라.

    ```java
    Arrays.stream(OrderStatus.values()).forEach(System.out::println);
    ```

  질문 2) enum은 자바의 클래스처럼 생성자, 메소드, 필드를 가질 수 있는가?

  질문 3) enum의 값은 == 연산자로 동일성을 비교할 수 있는가?

    ```java
    public enum OrderStatus {
    	PREPARING(0), SHIPPED(1), DELEVERING(2), DELIVERED(3)
    
    	private int number;
    
    	OrderStatus(int number) {
    		this.number = number;
    	}
    }
    
    if (order.orderStatus == OrderStatus.DELIVERED) {
    	System.out.println("delivered");
    }
    ```

  과제) enum을 key로 사용하는 Map을 정의하세요. 또는 enum을 담고 있는 Set을 만들어 보세요.

- 완벽공략 2 - 플라이웨이트 패턴

  [플라이웨이트 패턴](https://github.com/crongcm/computer-science/blob/main/design%20pattern/%5BDesign%20Pattern%5D%20Flyweight.md)

- 완벽공략 3 - 인터페이스와 정적 메서드
    - 기본 메소드 (default method)와 정적 메소드를 가질 수 있다.
    - 기본 메소드
        - 인터페이스에서 메소드 선언 분 아니라, 기본적인 구현체까지 제공할 수 있다.
        - 기존의 인터페이스를 구현하는 클래스에 새로운 기능을 추가할 수 있다.
    - 정적 메소드
        - 자바 9부터 private static 메소드도 가질 수 있다.
        - 단, private 필드는 아직도 선언할 수 없다.

    ```java
    public interface HelloService {
    	String hello();
    	
    	// 정적 메소드를 정의할 것이냐
    	static public String hi() {
    		prepareMessage();
    		return "hi";
    	}
    
    	static public String hi1() {
    		prepareMessage();
    		return "hi";
    	}
    
    	static public String hi2() {
    		prepareMessage();
    		return "hi";
    	}
    
    	static private void prepareMessage() {
    	}
    	
    	// 인스턴스 메소드를 정의할 것이냐
    	default String bye() {
    		return "bye";
    	}
    }
    ```

    - 질문1) 내림차순으로 정렬하는 Comparator를 만들고 List<Integer>를 정렬하라.
    - 질문2) 질문1에서 만든 Comparator를 사용해서 오름차순으로 정렬하라.

        ```java
        List<Integer> numbers = new ArrayList<>();
        numbers.add(100);
        numbers.add(20);
        numbers.add(44);
        numbers.add(3);
        
        System.out.println(numbers);
        
        Comparator<Integer> desc = (o1, o2) -> o2 - o1;
        // Collections.sort(numbers, desc);
        numbers.sort(desc);
        numbers.sort(desc.reversed());
        
        System.out.println(numbers);
        ```

- 완벽공략 4 - 서비스 제공자 프레임워크
    - 주요 구성 요소
        - 서비스 제공자 인터페이스 (SPI)와 서비스 제공자 (서비스 구현체)
        - 서비스 제공자 등록 API (서비스 인터페이스의 구현체를 등록하는 방법)

        ```java
        @Configuration
        public class AppConfig {
        	@Bean
        		public HelloService helloService() {
        			return new HelloService();
        	}
        }
        ```

        - 서비스 접근 API (서비스의 클라이언트가 서비스 인터페이스의 인스턴스를 가져올 때 사용하는 API)

        ```java
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        HelloService helloService = applicationContext.getBean(HelloService.class);
        ```

    - 다양한 변형

      [브릿지 패턴](https://github.com/crongcm/computer-science/blob/main/design%20pattern/%5BDesign%20Pattern%5D%20Bridge.md)

        - 의존 객체 주입 프레임워크
        - java.util.ServiceLoader
- 완벽공략 5 - 리플렉션
    - 클래스로더를 통해 읽어온 클래스 정보(거울에 반사된 정보)를 사용하는 기술
    - 리플렉션을 사용해 클래스를 읽어오거나, 인스턴스를 만들거나, 메소드를 실행하거나, 필드의 값을 가져오거나 변형하는 것이 가능하다.
    - 언제 사용할까?
        - 특정 애노테이션이 붙어있는 필드 또는 메소드 읽어오기 (JUnit, Spring)
        - 특정 이름 패턴에 해당하는 메소드 목록 가져와 호출하기 (getter, setter)
    - https://docs.oracle.com/javase/tutorial/reflect/

    ```java
    Class<?> aClass = Class.forName("package...");
    ```
