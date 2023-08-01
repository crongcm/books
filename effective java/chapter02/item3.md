# Item 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

무상태(stateless) 객체나 설계상 유일해야 하는 시스템 컴포넌트

> 싱글턴(singleton)<br>
인스턴스를 오직 하나만 생성할 수 있는 클래스

- 장점 : 간결하고 싱글턴임을 API에 드러낼 수 있다.
- 단점
1. 싱글톤을 사용하는 클라이언트 테스트하기 어려워진다.

   IElvis (Test Mocking 위한 Interface)

    ```java
    public interface IElvis {
        void leaveTheBuilding();
        
        void sing();
    }
    ```

2. 리플렉션으로 private 생성자를 호출할 수 있다.

   Reflection

    ```java
    public class ElvisReflection {
        public static void main(String[] args) {
            try {
                Constructor<Elvis> defaultConstructor = Elvis.class.getDeclaredConstructor();
                defaultConstructor.setAccessible(true);
                Elvis elvis1 = defaultConstructor.newInstance();
                Elvis elvis2 = defaultConstructor.newInstance();
                Elvis.INSTANCE.sing();
            } catch (InvocationTargetException | NoSuchMethodException | InstantiationException | IllegalAccessException e) {
                e.printStackTrace();
            }
        }
    }
    ```

3. 역직렬화 할 때 새로운 인스턴스가 생길 수 있다.

   Serialization

    ```java
    public class ElvisSerialization {
        public static void main(String[] args) {
            try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("elvis.obj"))) {
                out.writeObject(Elvis.INSTANCE);
            } catch (IOException e) {
                e.printStackTrace();
            }
        
            try (ObjectInput in = new ObjectInputStream(new FileInputStream("elvis.obj"))) {
                Elvis elvis3 = (Elvis) in.readObject();
                System.out.println(elvis3 == Elvis.INSTANCE);
            } catch (IOException | ClassNotFoundException e) {
                e.printStackTrace();
            }
        }
    }
    ```


- private 생성자 + public static final 필드

  Elvis

    ```java
    public class Elvis implements IElvis, Serializable {
        /**
         * 싱글톤 오브젝트
         */
        public static final Elvis INSTANCE = new Elvis();
        private static boolean created;
    
        private Elvis() {
            if (created) {
                throw new UnsupportedOperationException("can't be created by constructor.");
            }
    
            created = true;
        }
    
        public void leaveTheBuilding() {
            System.out.println("Whoa baby, I'm outta here!");
        }
    
        public void sing() {
            System.out.println("I'll have a blue~ Christmas without you~");
        }
    
        // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
        public static void main(String[] args) {
            Elvis elvis = Elvis.INSTANCE;
            elvis.leaveTheBuilding();
        }
    
        private Object readResolve() {
            return INSTANCE;
        }
    }
    ```

  Concert

    ```java
    public class Concert {
    
        private boolean lightsOn;
    
        private boolean mainStateOpen;
    
        private IElvis elvis;
    
        public Concert(IElvis elvis) {
            this.elvis = elvis;
        }
    
        public void perform() {
            mainStateOpen = true;
            lightsOn = true;
            elvis.sing();
        }
    
        public boolean isLightsOn() {
            return lightsOn;
        }
    
        public boolean isMainStateOpen() {
            return mainStateOpen;
        }
    }
    ```

- private 생성자 + 정적 팩터리 메서드
    - 장점 1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
    - 장점 2. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.

        ```java
        // <T> Instance Scope
        public class MetaElvis<T> {
            private static final MetaElvis<Object> INSTANCE = new MetaElvis<>();
        
            private MetaElvis() { }
        		// <E> Static Scope
            @SuppressWarnings("unchecked")
            public static <E> MetaElvis<E> getInstance() { return (MetaElvis<E>) INSTANCE; }
        
            public void say(T t) {
                System.out.println(t);
            }
        
            public void leaveTheBuilding() {
                System.out.println("Whoa baby, I'm outta here!");
            }
        
            public static void main(String[] args) {
                MetaElvis<String> elvis1 = MetaElvis.getInstance();
                MetaElvis<Integer> elvis2 = MetaElvis.getInstance();
                System.out.println(elvis1);
                System.out.println(elvis2);
                elvis1.say("hello");
                elvis2.say(100);
            }
        }
        ```

    - 장점 3. 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다.

        ```java
        public class Elvis implements Singer {
            private static final Elvis INSTANCE = new Elvis();
            private Elvis() { }
            public static Elvis getInstance() { return INSTANCE; }
        
            public void leaveTheBuilding() {
                System.out.println("Whoa baby, I'm outta here!");
            }
        
            // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
            public static void main(String[] args) {
                Elvis elvis = Elvis.getInstance();
                elvis.leaveTheBuilding();
        
                System.out.println(Elvis.getInstance());
                System.out.println(Elvis.getInstance());
            }
        
            @Override
            public void sing() {
                System.out.println("my way~~~");
            }
        }
        ```

        ```java
        public interface Singer {
            void sing();
        }
        ```

        ```java
        public class Concert {
            public void start(Supplier<Singer> singerSupplier) {
                Singer singer = singerSupplier.get();
                singer.sing();
            }
        
            public static void main(String[] args) {
                Concert concert = new Concert();
                concert.start(Elvis::getInstance);
            }
        }
        ```

  - 열거 타입
      - 가장 간결한 방법이며 직렬화와 리플렉션에도 안전하다.
      - 대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.

      ```java
      public enum Elvis {
          INSTANCE;
    
          public void leaveTheBuilding() {
              System.out.println("기다려 자기야, 지금 나갈께!");
          }
    
          // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
          public static void main(String[] args) {
              Elvis elvis = Elvis.INSTANCE;
              elvis.leaveTheBuilding();
          }
      }
      ```

      ```java
      public class EnumElvisReflection {
          public static void main(String[] args) {
              try {
                  Constructor<Elvis> declaredConstructor = Elvis.class.getDeclaredConstructor();
                  System.out.println(declaredConstructor);
              } catch (NoSuchMethodException e) {
                  e.printStackTrace();
              }
          }
      }
      ```

      ```java
      public class EnumElvisSerialization {
          public static void main(String[] args) {
              try (ObjectOutput out = new ObjectOutputStream(new FileOutputStream("elvis.obj"))) {
                  out.writeObject(Elvis.INSTANCE);
              } catch (IOException e) {
                  e.printStackTrace();
              }
    
              try (ObjectInput in = new ObjectInputStream(new FileInputStream("elvis.obj"))) {
                  Elvis elvis = (Elvis) in.readObject();
                  System.out.println(elvis == Elvis.INSTANCE);
              } catch (IOException | ClassNotFoundException e) {
                  e.printStackTrace();
              }
          }
      }
      ```


## 이펙티브 자바 완벽 공략

- 완벽공략 11 - 메소드 참조

  메소드 하나만 호출하는 람다 표현식을 줄여쓰는 방법

    ```java
    public class Person {
        LocalDate birthday;
    
        public Person() {
        }
    
        public Person(LocalDate birthday) {
          this.birthday = birthday;
        }
    
        public static int compareByAge(Person a, Person b) {
          return a.birthday.compareTo(b.birthday);
        }
    
        public static void main(String[] args) {
          List<Person> people = new ArrayList<>();
          people.add(new Person(LocalDate.of(1982, 7, 15)));
          people.add(new Person(LocalDate.of(2011, 3, 2)));
          people.add(new Person(LocalDate.of(2013, 1, 28)));
    
          people.sort(new Comparator<Person>() {
              @Override
              public int compare(Person a, Person b) {
                  return a.birthday.compareTo(b.birthday);
              }
          });
        }
    
        public int getAge() {
          return LocalDate.now().getYear() - birthday.getYear();
        }
    }
    ```

    1. 스태틱 메소드 레퍼런스

    ```java
    public static int compareByAge(Person a, Person b) {
      return a.birthday.compareTo(b.birthday);
    }
    ```

    2. 인스턴스 메소드 레퍼런스

    ```java
    public int compareByAge(Person a, Person b) {
      return a.birthday.compareTo(b.birthday);
    }
    
    public static void main(String[] args) {
    	...
    	Person person = new Person();
    	people.sort(person::compareByAge);
    }
    ```

    3. 임의 객체의 인스턴스 메소드 레퍼런스

    ```java
    // 임의 객체인 경우 첫번째 인자는 자기자신으로 설정
    public int compareByAge(Person b) {
      return this.birthday.compareTo(b.birthday);
    }
    
    public static void main(String[] args) {
    	...
    	Comparator<Person> compareByAge = Person::compareByAge;
    	people.sort(compareByAge);
    }
    ```

    4. 생성자 레퍼런스

    ```java
    public Person(LocalDate birthDay) {
    	this.birthDay = birthDay;
    }
     
    public static void main(String[] args) {
      List<LocalDate> dates = new ArrayList<>();
    	dates.add(LocalDate.of(1982, 7, 15)));
      dates.add(LocalDate.of(2011, 3, 2)));
      dates.add(LocalDate.of(2013, 1, 28)));
    	Function<LocalDate, Person> aNew = Person::new;
    	dates.stream().map(aNew).collect(Collectors.toList());
    
      ...
    }
    ```
<br/>

- 완벽공략 12 - 함수형 인터페이스

  자바가 제공하는 기본 함수형 인터페이스

    ```java
    public class UsageOfFunctions {
      public static void main(String[] args) {
          List<LocalDate> dates = new ArrayList<>();
          dates.add(LocalDate.of(1982, 7, 15));
          dates.add(LocalDate.of(2011, 3, 2));
          dates.add(LocalDate.of(2013, 1, 28));
    
          List<Integer> before2000 = dates.stream()
                  .filter(d -> d.isBefore(LocalDate.of(2000, 1, 1)))
                  .map(LocalDate::getYear)
                  .collect(Collectors.toList());
      }
    }
    ```

    - 함수형 인터페이스는 람다 표현식과 메소드 참조에 대한 “타겟 타입”을 제공한다.
    - 타겟 타입은 변수 할당, 메소드 호출, 타입 변환에 활용할 수 있다.
    - 자바에서 제공하는 기본 함수형 인터페이스 익혀 둘 것. (java.util.funcion 패키지)
    - 함수형 인터페이스를 만드는 방법
    - 심화 학습 1) Understanding Java method invocation with invokedynamic
    - 심화 학슴 2) LambdaMetaFactory<br/>


- 완벽공략 13 - 객체 직렬화

  객체를 바이트스트림으로 상호 변환하는 기술

    - 바이트스트림으로 변환한 객체를 파일로 저장하거나 네트워크를 통해 다른 시스템으로 전송할 수 있다
    - Serializable 인터페이스 구현
    - transient를 사용해서 직렬화 하지 않을 필드 선언하기
    - serialVersionUID는 언제 왜 사용하는가?
    - 심화 학습) 객체 직렬화 스펙
