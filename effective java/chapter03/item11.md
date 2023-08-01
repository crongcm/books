# Item 11. equals를 재정의하려거든 hashCode도 재정의하라

핵심 정리 : hashCode 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면 hashCode는 매번 같은 값을 리턴해야 한다. (변경되거나, 애플리케이션을 다시 실행했다면 달라질 수 있다.)
- `두 객체에 대한 equals가 같다면, hashCode의 값도 같아야 한다.`
- 두 객체에 대한 equals가 다르더라도, hashCode의 값은 같을 수 있지만 해시 테이블 `성능을 고려해 다른 값을 리턴하는 것이 좋다.`

### 핵심 정리 : hashCode 구현 방법

```java
@Override public int hashCode() {
	int result = Short.hashCode(areaCode); // 1
	result = 31 * result + Short.hashCode(prefix); // 2
	result = 31 * result + Short.hashCode(lineNum); // 3
	return result;
}
```

1. 핵심 필드 하나의 값의 해쉬값을 계산해서 result 값을 초기화 한다.
2. 기본 타입은 Type.hashCode
   참조 타입은 해당 필드의 hashCode
   배열은 모든 원소를 재귀적으로 위의 로직을 적용하거나, Arrays.hashCode
   result = 31 * result + hashCode 해당 필드의 계산값
3. result를 리턴한다.

- 완벽 공략 27 - 해시맵 내부의 연결 리스트

  내부 구현은 언제든지 바뀔 수도 있다.

    - 자바 8에서 해시 충돌시 성능 개선을 위해 내부적으로 동일한 버켓에 일정 개수 이상의 엔트리가 추가되면, 연결 리스트 대신 이진 트리를 사용하도록 바뀌었다.
        - **https://dzone.com/articles/hashmap-performance**
    - 연결 리스트에서 어떤 값을 찾는데 걸리는 시간은?
    - 이진 트리에서 어떤 값을 찾는데 걸리는 시간은?


- 완벽 공략 28 - 스레드 안전

  멀티 스레드 환경에서 안전한 코드, Thread-safety

    - 가장 안전한 방법은 여러 스레드 간에 공유하는 데이터가 없는 것!
    - 공유하는 데이터가 있다면:
        - Synchronization
        - ThreadLocal
        - 불변 객체 사용
        - Synchronized 데이터 사용
        - Concurrent 데이터 사용
