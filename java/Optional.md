## Null 체크를 문법적으로 강제할 수 있을까?

- Java8부터 가능하다. `Optional`을 반환하면 된다.
- Optional은 클라이언트에게 명시적으로 비어있는 값일 수도 있다는 것을 알려주고 처리를 강제한다.

```java
public Optional<Progress> getProgress() {
	return Optional.ofNullable(progress);
}
```

## 주의사항

### API 공식문서에서는 Optional을 리턴 값으로만 사용하기를 권장한다.

- **매개변수로 사용**하는 경우의 문제점

```java
public void setProgress(Optional<Progress> progress) {
	if (progress != null) {	// Optional 자체가 null일 수 있다. 의미없다.
		progress.ifPresent(p -> this.progress = p);
	}
}
```

- **Map의 키**로 사용할 경우의 문제점 : Map의 가장 큰 특징 중 하나는 **key가 반드시 null이 아니다**라는 점이므로 애초에 개념적으로 맞지 않다.
- **Instance의 field 타입**으로 사용할 경우의 문제점 : 해당 필드가 있을 수도 있고 없을 수도 있다는 의미인데, 도메인 설계시 좋지 않은 구조이다.

### Primitive 타입은 `OptionalInt`, `OptionalLong` 등 전용 Optional 타입을 사용해야 한다.

- 박싱 / 언박싱으로 인한 성능저하를 피하려면 전용 타입을 사용해야 한다.

### Return 타입이 Optional인 메소드에서 null을 리턴하지 말아야 한다.

- Optional 자체가 null이라면 클라이언트 쪽에서 처리가 쉽지 않다.

### Collection, Map, Stream Array, Optional 등은 Optional로 감싸지 말아야 한다.

- 해당 컨테이너들은 자체적으로 null 값에 대한 처리가 가능하기 때문이고 이중 wrapping될 경우 효율도 떨어지기 때문이다.

## API

### Optional 만들기

- Optional.`of()`, Optional.`ofNullable()`, Optional.`empty()`

```java
Optional<Integer> integer = Optional.of(10);	// null이 들어갈 경우 NullPointException 발생
Optional<Integer integerOptional = Optional.ofNullable((Math.random() > 5 ? null : 1);
Optional<Object> empty = Optional.empty();
```

### Optional에 값이 있는지 없는지 확인하기

- `isPresent()`, `isEmpty()`

```java
Optional<Integer> integer = Optional.of(10);
Optional<Object> empty = Optional.empty();

integer.isPresent();	// true
integer.isEmpty()l // false
```

### Optional에 있는 값 가져오기

```java
Optional<Integer> integer = Optional.of(10);
Optional<Object> empty = Optional.empty();

integer.get();	// 가장 기본이지만 이렇게 쓰면 큰 의미가 없다. 함수형 프로그래밍을 도입하자.
integer.ifPresent(System.out::println);	// 로직수행
integer.orElse(testInteger());	// 값이 있는 경우 가져오고 없는 경우 인자로 전달되는 값을 반환한다.
integer.orElseGet(App::testInteger);	 // 값이 있는 경우 가져오고 없는 경우 Supplier 수행
integer.orElseThrow(NoSuchElementException::new); // 값이 있는 경우 가져오고 없으면 예외를 던진다.
```

### 함수형 프로그래밍에 활용

```java
Optional<OnlineClass> spring = springClasses.stream()
	.filter(oc -> oc.getTitle().startsWith("spring"))	// 못찾을수도 있으니 Optional 반환이 타당
	.findFirst();

// filter
Optional<OnlineClass> onlineClass = optional.filter(oc -> oc.getId() > 10);

// map
Optional<Integer> integer = optional.map(OnlineClass::getId);

// flatMap
Optional<Progress> progress.flatMap(OnlineClass::getProgress);
```

---

[1] 백기선, 더 자바 Java 8, Optional
