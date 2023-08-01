# Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라.

정적 팩터리와 생성자에 선택적 매개변수가 많을 때 고려할 수 있는 방안

- 대안1: 점층적 생성자 패턴 또는 생성자 체이닝
    - 매개변수가 늘어나면 클라이언트 코드를 작성하거나 읽기 어렵다.

        ```java
        public class NutritionFacts {
            private final int servingSize;  // (mL, 1회 제공량)     필수
            private final int servings;     // (회, 총 n회 제공량)  필수
            private final int calories;     // (1회 제공량당)       선택
            private final int fat;          // (g/1회 제공량)       선택
            private final int sodium;       // (mg/1회 제공량)      선택
            private final int carbohydrate; // (g/1회 제공량)       선택
        
            public NutritionFacts(int servingSize, int servings) {
                this(servingSize, servings, 0);
            }
        
            public NutritionFacts(int servingSize, int servings, int calories) {
                this(servingSize, servings, calories, 0);
            }
        
            public NutritionFacts(int servingSize, int servings, int calories, int fat) {
                this(servingSize, servings, calories, fat, 0);
            }
        
            public NutritionFacts(int servingSize, int servings, int calories, 
                                  int fat, int sodium) {
                this(servingSize, servings, calories, fat, sodium, 0);
            }
        
            public NutritionFacts(int servingSize, int servings,
                                  int calories, int fat, int sodium, int carbohydrate) {
                this.servingSize  = servingSize;
                this.servings     = servings;
                this.calories     = calories;
                this.fat          = fat;
                this.sodium       = sodium;
                this.carbohydrate = carbohydrate;
            }
        
            public static void main(String[] args) {
                NutritionFacts cocaCola = new NutritionFacts(10, 10);
            }
        }
        ```

- 대안2: 자바빈즈 패턴
    - 완전한 객체를 만들려면 메서드를 여러번 호출해야 한다. (일관성이 무너진 상태가 될 수 도 있다)
    - 클래스를 불변으로 만들 수 없다.
- 권장하는 방법: 빌더 패턴
    - 플루언트 API 또는 메서드 체이닝을 한다.
    - 계층적으로 설계된 클래스와 함께 사용하기 좋다.
    - 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈 보다 훨씬 안전하다.

    > ❗ 모든 경우에 빌더가 적절하지 않다<br/>
    빌더는 오히려 코드를 이해하기 어렵게 만든다. 빌더는 중복 필드 및 코드가 생성된다.<br/>
    필수적인 필드와 선택적인 필드가 나뉘어 있고, immuterble하게 만들고 싶을 경우 빌더 패턴이 유용

## 이펙티브 자바 완벽 공략

- 완벽공략 6 - 자바빈(JavaBean)

  java.beans 패키지 안에 있는 모든 것

    - 그 중에서도 자바빈이 지켜야 할 규칙
        - argument 없는 기본 생성자
        - getter와 setter 메소드 이름 규약
        - Serializable 인터페이스 구현
    - 하지만 실제로 오늘날 자바빈 스펙 중에서도 getter와 setter가 주로 쓰는 이유는?
        - JPA나 스프링과 같은 여러 프레임워크에서 리플렉션을 통해 특정 객체의 값을 조회하거나 설정하기 때문

        ```java
        public class NutritionFacts {
            // 필드 (기본값이 있다면) 기본값으로 초기화된다.
            private int servingSize  = -1; // 필수; 기본값 없음
            private int servings     = -1; // 필수; 기본값 없음
            private int calories     = 0;
            private int fat          = 0;
            private int sodium       = 0;
            private int carbohydrate = 0;
            private boolean healthy;
        
            public NutritionFacts() { }
        
            public void setServingSize(int servingSize) {
                this.servingSize = servingSize;
            }
        
            public void setServings(int servings) {
                this.servings = servings;
            }
        
            public void setCalories(int calories) {
                this.calories = calories;
            }
        
            public void setFat(int fat) {
                this.fat = fat;
            }
        
            public void setSodium(int sodium) {
                this.sodium = sodium;
            }
        
            public void setCarbohydrate(int carbohydrate) {
                this.carbohydrate = carbohydrate;
            }
        
            public void setHealthy(boolean healthy) {
                this.healthy = healthy;
            }
        
            public static void main(String[] args) {
                NutritionFacts cocaCola = new NutritionFacts();
                cocaCola.setServingSize(240);
                cocaCola.setServings(8);
        
                cocaCola.setCalories(100);
                cocaCola.setSodium(35);
                cocaCola.setCarbohydrate(27);
            }
        }
        ```

- 완벽공략 7 - 객체 얼리기(freezing)

  임의의 객체를 불변 객체로 만들어주는 기능

    - Object.freeze()에 전달한 객체는 그 뒤로 변경될 수 없다.
        - 새 프로퍼티를 추가하지 못함
        - 기존 프로퍼티를 제거하지 못함
        - 기존 프로퍼티 값을 변경하지 못함
        - 프로토타입을 변경하지 못함
    - strict 모드에서만 동작함
    - 비슷한 류의 펑션으로 Object.seal()과 Object.preventExtenstion()이 있다.
- 완벽공략 8 - 빌더 패턴

  [빌더 패턴](https://github.com/crongcm/computer-science/blob/main/design%20pattern/%5BDesign%20Pattern%5D%20Builder.md)

    ```java
    public class NutritionFacts {
        private final int servingSize;
        private final int servings;
        private final int calories;
        private final int fat;
        private final int sodium;
        private final int carbohydrate;
    
        public static void main(String[] args) {
            NutritionFacts cocaCola = new Builder(240, 8)
                    .calories(100)
                    .sodium(35)
                    .carbohydrate(27).build();
        }
    
        public static class Builder {
            // 필수 매개변수
            private final int servingSize;
            private final int servings;
    
            // 선택 매개변수 - 기본값으로 초기화한다.
            private int calories      = 0;
            private int fat           = 0;
            private int sodium        = 0;
            private int carbohydrate  = 0;
    
            public Builder(int servingSize, int servings) {
                this.servingSize = servingSize;
                this.servings    = servings;
            }
    
            public Builder calories(int val)
            { calories = val;      return this; }
            public Builder fat(int val)
            { fat = val;           return this; }
            public Builder sodium(int val)
            { sodium = val;        return this; }
            public Builder carbohydrate(int val)
            { carbohydrate = val;  return this; }
    
            public NutritionFacts build() {
                return new NutritionFacts(this);
            }
        }
    
        private NutritionFacts(Builder builder) {
            servingSize  = builder.servingSize;
            servings     = builder.servings;
            calories     = builder.calories;
            fat          = builder.fat;
            sodium       = builder.sodium;
            carbohydrate = builder.carbohydrate;
        }
    }
    ```

<br/>
- 완벽공략 9 - IllegalArgumentException()

  잘못된 인자를 넘겨 받았을 때 사용할 수 있는 기본 런타임 예외

```java
public void setAsText(String text) throws java.lang.IllegalArgumentException {
    if (value instanceof String) {
        setValue(text);
        return;
    }
    throw new java.lang.IllegalArgumentException(text);
}
```

  질문1) checked exception과 unchecked exception의 차이?

  - Checked Exception

      > RuntimeException을 제외한 Exception
      컴파일 시점에 check되는 Exception으로 try catch 또는 throws로 예외에 관한 처리를 해줘야한다.

  - Unchecked Exception

      > RuntimeException, Error, and their subclasses

  질문2) 간혹 메소드 선언부에 unchecked exception을 선언하는 이유는?

    ```
    Client에게 명시적으로 알려주고 싶을 때 선언
    ```

  질문3) checked exception은 왜 사용할까?

    ```
    그 에러가 발생했을 때 Client에게 후속 작업이 필요함 또는 있음을 알려주기 위해
    ```

  과제1) 자바의 모든 RuntimeException 클래스 이름 한번씩 읽어보기

  [RuntimeException (Java SE 11 & JDK 11 )](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/RuntimeException.html)

  과제2) 이 링크에 있는 글을 꼭 읽으세요.

  [Unchecked Exceptions - The Controversy](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)


- 완벽공략 10 - 가변 인수

  여러 인자를 받을 수 있는 가변적인 argument(Var+args)

    - 가변인수는 메소드에 오직 하나만 선언할 수 있다.
    - 가변인수는 메소드의 가장 마지막 매개변수가 되어야 한다.

    ```java
    // Heap pollution
    public void printNumbers(int... numbers) {
    	System.out.println(numbers.getClass().getCanonicalName()); // int[]
    	System.out.println(numbers.getClass().getComponentType()); // int
    	Arrays.stream(numbers).forEach(System.out::println);
    }
    ```
