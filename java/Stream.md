## 특징

- **연속된 정보를 처리**하는데 사용한다.
- Sequence of elements supporting sequential and parallel aggregate operations
- 데이터를 담고 있는 **저장소(컬렉션)이 아니다**.
- `Functional in nature` : **stream은 처리하는 데이터 소스를 변경하지 않는다.**

```java
List<String> names = new ArrayList<>();
names.add(“test”);

names.stream().map(String::toUpperCase).forEach(System.out::println);
names.forEach(System.out::println);

> TEST
> test
```

- 하나의 데이터를 오직 한번만 처리한다.
- 네트워크 등 무제한 stream이 있을 수도 있다. 그런 경우 Short Circuit 메소드로 제한할 수 있다.
- 중개 연산은 기본적으로 **lazy**하다.

```java
List<String> names = new ArrayList<>();
names.add(“test”);

names.stream().map(s -> {
	System.out.println(s);		// 종단 연산 전까지는 실행되지 않는다.
	return s.toUpperCase();
});
names.forEach(System.out::println);

> test
```

- `parallelStream`을 이용하여 손쉽게 **병렬 처리**할 수 있다.

```java
List<String> names = new ArrayList<>();
names.add(“test1”);
names.add(“test2”);

List<String> collect = names.parallelStream().map(s -> s.toUpperCase()).collect(Collectors.toList());

collect.forEach(System.out::println);
```

## 스트림 파이프라인

### 중개 연산

- **stream을 반환**한다.
- stateless와 stateful 방식으로 나눌 수 있다. 대부분 stateless이지만 distinct나 sorted 같은 경우 stateful이다.
- `filter`, `map`, `limit`, `skip`, `sorted`, …

### 종단 연산

- **stream을 반환하지 않는다.**
- `collect`, `allMatch`, `count`, `forEach`, `min`, `max`, …

## 스트림 API

### 걸러내기

- `filter(predicate)`
- 특정 조건(predicate)를 만족하는 element만 새로운 `stream`으로 반환한다.
- filter(Predicate.not(OnlineClass::isClosed)) -> !와 같은 연산자는 사용할 수 없다.

### 변경하기

- `map(function)`, `flatMap(function)`
- 각각의 요소에서 특정요소만 꺼내거나 변경하여 새로운 `stream`으로 반환한다.
- (ex) List<Stream<String>> -> String의 스트림으로 변환

### 생성하기

- `generate(Supplier)` 또는 `iterate(T seed, UnaryOperator)`
- seed로 부터 UnaryOperator를 무제한으로 반복하는 `stream`을 반환한다.
- Random integer 무제한 스트림 등이 가능하다.

### 제한하기

- `limit(long)`, `skip(long)`
- (ex) 앞에서 3개를 뺀 나머지 `stream`을 반환한다.

### Stream에 있는 데이터가 특정 조건을 만족하는지 확인

- `anyMatch()`, `allMatch()`, `nonMatch()`
- `stream`의 element 들을 돌면서 특정 조건을 만족하는지 확인한다.
- (ex) 제목 중 test가 들어가는지

### 개수 세기

- `count()`

### Stream을 데이터 하나로 뭉치기

- `reduce(identity, BiFunction)`, `collect()`, `sum()`, `max()`

### 통합 예제

```java
// 데이터 준비
List<OnlineClass> springClasses = new ArrayList<>();
springClasses.add(new OnlineClass(1, “spring boot”, true));
springClasses.add(new OnlineClass(2, "spring data jpa", true));
springClasses.add(new OnlineClass(3, "spring mvc", false));
springClasses.add(new OnlineClass(4, "spring core", false));
springClasses.add(new OnlineClass(5, "rest api development", false));

List<OnlineClass> javaClasses = new ArrayList<>();
javaClasses.add(new OnlineClass(6, "The Java, Test", true));
javaClasses.add(new OnlineClass(7, "The Java, Code mainpulation", true));
javaClasses.add(new OnlineClass(8, "The Java 8 to 11", false));

List<List<OnlineClass>> events = new ArrayList<>();
events.add(springClasses);
events.add(javaClasses);

// spring으로 시작하는 수업
springClasses.stream()
	.filter(oc -> oc.getTitle().startsWith(“spring”)
	.forEach(System.out::println);

// closed되지 않는 수업
springClasses.stream()
	.filter(Predicate.not(OnlineClass::isClosed))
	.forEach(System.out::println);

// 수업 이름만 모아서 stream만들기
Stream<String> springTitleStream = springClasses.stream()
	.map(OnlineClass::getTitle);

// 두 수업 목록에 들어있는 모든 수업 아이디 출력
events.stream()
	.flatMap(Collection::stream)	// 2차원 리스트를 1차원으로 평탄화
	.forEach(oc -> System.out.println(oc.getId());

// 10부터 1씩 증가하는 무제한 스트림 중에서 앞에 10개 빼고 최대 10개까지만
Stream.iterate(10, i -> i + 1)
	.skip(10)
	.limit(10)
	.forEach(System.out::println);

// 자바 수업 중에 Test가 들어있는 수업이 “있는지” 확인
boolean test = jaaClasses.stream()
	.anyMatch(oc -> oc.getTitle().contains(“Test”));

// 스프링 수업 중에 제목이 spring이 들어간 제목만 모아서 List로 만들기
List<String> spring = springClasses.stream()
	.filter(oc -> oc.getTitle().contains(“spring”)
	.map(OnlineClass::getTitle)
	.collect(Collector.toList());
```

---

[1] 백기선, 더 자바 Java 8, Optional
