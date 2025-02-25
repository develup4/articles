## 함수형 인터페이스

- 추상 메소드를 **딱 하나**만 가지고 있는(Single Abstract Method) 인터페이스
- `@FuncationInterface` 애노테이션을 가지고 있는 인터페이스

### 딱 하나의 의미

- Java 8부터는 인터페이스에 `default method`와 `static method`를 추가할 수 있게 되었다.
- 그리고 추상 메소드의 개수로는 **카운트되지 않는다.**

```java
@FunctionalInterface
public interface RunSomething {
	void doIt();	// count!

	static void staticPrint() {	// no count!
		System.out.println("print");
	}

	default void defaultPrint() {	// no count!
		System.out.println("default");
	}
}
```

> **default method**
> 예를 들어 오픈소스가 엄청 유명해져서 모두 사용하고 있는데 인터페이스에 새로운 메소드를 만들어야 하는 상황이 온다면, 수많은 개발자들에게 컴파일 에러를 발생시킬 수는 없을 것이다. 이런 경우를 위해 추가된 문법이라 할 수 있다. 다만 구현체는 기능이 추가되었음을 알 수 없으므로 주의해야 한다.
>
> - 구현체에서 재정의가 가능하다.
> - 상속받는 인터페이스에서 다시 추상메소드로 재선언이 가능하다.
> - Object 클래스가 제공하는 기능은 기본 메소드로 제공할 수 없다.

## 활용

`익명 내부 클래스(Anonymous inner class)` 대신 간결하게 `람다(Lambda)`로 표현이 가능하다.

```java
// 익명 내부 클래스 활용
public class Foo {
	RunSomething runSomething = new RunSomething() {
		@Override
		public void doIt() {
			System.out.println("do");
		}
	}
}

// Lambda Expression 활용
public class Foo {
	RunSomething runSomething = () -> System.out.println("do");
	runSomething.doIt();
}
```

- IntelliJ에서 자동으로 익명 내부 클래스를 Lambda로 변환하는 기능도 있다.
- Java는 다른 언어(Javascript)처럼 함수 자체를 저장하는게 아니라 함수형 인터페이스 형태의 **객체**를 저장하므로`runSomething();`처럼 바로 호출하는 것이 아님을 주의하자.

## 람다 표현식(Lambda Expressions)

- 함수형 인터페이스의 **인스턴스를 만드는 방법**으로 쓰일 수 있다.
- 메소드 매개변수, 리턴 타입, 변수로 만들어 사용할 수도 있다 -> **일급 객체(First class object)**

> **일급 객체(First class object)의 조건**
>
> 1. 파라미터로 전달할 수 있다.
> 2. 반환값(return value)로 사용할 수 있다.
> 3. 변수나 데이터 구조 안에 담을 수 있다.
> 4. 할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.
>
> Object는 이미 충분히 First class의 조건을 만족한다. 함수(함수형 인터페이스) 또한 그렇게 되었다는 이야기이다. 이것에서부터 함수형 프로그래밍을 위한 조건이 완성되는 것이다. 계속해서 함수를 통한 파이프를 제공해서 전체 파이프라인을 구성할 수 있는 것이다.

### 사용법

`(인자리스트) -> {바디}`

- 인자의 타입은 (컴파일러가 추론이 가능한 경우) 생략이 가능하다.
- 한줄인 경우, `brace`나 `return`은 생략이 가능하다.

### 변수 캡쳐(Variable Capture)

- 로컬 변수 캡처 : **final이거나 effective final 인 경우에만 참조**할 수 있다. 그렇지 않을 경우 동시성 문제가 생길 수 있어서 컴파일러가 방지한다. C++의 캡쳐보다 아주 간결하다.
- 클로저(Closure) 개념은 Java8 이전부터 있었다. Local Class, Anonymous Class, Lambda에서 final이라면 참조가 가능하다.

```java
private void run() {
	final int base = 10;

	class LocalClass {
		void print() {
			System.out.println(base);	// 이너클래스에서 외부 변수 참조(final만 가능)
		}
	}
}
```

### Shadowing

- Local class, Anonymous inner class는 내부에 자체적인 Scope가 있기 때문에 외부와 같은 이름의 변수를 선언하면 가린다.

```java
private void run() {
	int base = 10;

	class LocalClass {
		void print() {
			int base = 11;
			System.out.println(base);	// 11
		}
	}
}
```

- Lambda는 Shadowing(이름 가리기)하지 않는다. **Lambda를 감싸고 있는 Scope과 동일한 Scope**이기 때문이다.

```java
private void run() {
	int base = 10;

	// 람다는 (중괄호를 굳이 한다쳐도) 상위랑 같은 스코프다. 쉐도잉 자체가 불가능하다.
	Consumer<Integer> c = (i) -> {
		System.out.println(i);
	}
}
```

### 순수 함수(Pure function)

> 사이드 이펙트가 없다. 즉, 함수 밖에 있는 값을 변경하지 않는다.
> 상태가 없다. 즉, 함수 밖에 있는 값을 사용하지 않는다.

- 따라서 동시성 문제가 없는 것이 크나 큰 장점이다. (문제를 정의하는 것이 어려워서 그렇지…)
- 호출을 여러번해도 항상 같은 결과가 나와야한다.

```java
// Example1
public class Foo {
	RunSomething runSomething = (number) -> {
		return number + 10;
	}

	// 여러번 호출해도 항상 같은 값이 나온다! 순수함수
	runSomething.doIt(1);
	runSomething.doIt(1);
	runSomething.doIt(1);
}

// Example2
public class Foo {
	int base = 10;

	RunSomething runSomething = (number) -> {
		return number + base;
	}

	// 외부의 영향(base)을 받아 항상 동일한 결과가 나온다고 할 수 없다.
	// 심지어 컴파일 에러가 발생한다. base가 final이어야 에러가 나지 않는다.
	runSomething.doIt(1);
	base++;
	runSomething.doIt(1);
	base++;
	runSomething.doIt(1);
}

// Example3
public class Foo {
	int base = 10;	// effective final OK!

	RunSomething runSomething = (number) -> {
		return number + base;
	}
	runSomething.doIt(1);
	runSomething.doIt(1);
	runSomething.doIt(1);
}
```

> **effective final**
> Java8부터 추가된 개념으로 어느 곳에서도 해당 변수의 값이 변하지 않는다면 사실상 final로 보기 때문에, Local Class, Anonymous Class, Lambda 등에서도 사용이 가능하다.

### 고차 함수(Higher-Order Function)

- 함수가 함수를 매개변수로 받을 수 있고 함수를 리턴할 수도있다.
- Lambda도 특수한 형태의 Object이기 때문에 당연히 받을 수도 리턴할 수도 있다.

> **불변성에 대해**
> 자바는 안전한 언어다. 이것이 자바를 쓰는 즐거움 중 하나다. 네이티브 메서드를 사용하지 않으니 C, C++같이 안전하지 않은 언어에서 흔히 보는 버퍼 오버런, 배열 오버런, 와일드 포인터 같은 메모리 충돌 오류에서 안전하다. 자바로 작선한 클래스는 시스템의 다른 부분에서 무슨 짓을 하든 그 불변식이 지켜진다. (즉, C, C++에서는 불변을 보장하는 방법은 없다는 뜻이다) 메모리 전체를 하나의 거대한 배열로 다루는 언어에서는 누릴 수 없는 장점이다.
>
> - Effective Java - ITEM 50 中

## 메소드 레퍼런스

Lambda를 통해 이미 존재하는 메소드를 호출하거나 생성자 호출, 혹은 인스턴스의 메소드를 호출하는 경우에는 메소드 레퍼런스를 사용하여 매우 간결하게 표현할 수 있다. **매번 직접 인라인으로 작성할 필요가 없다**는 의미이다.

### 참조 방법

- 스태틱 메소드 참조 -> `Type::Static Method`
- 특정 객체의 인스턴스 메소드 참조 -> `Object Reference::Instance Method`
- 임의 객체의 인스턴스 메소드 참조 -> `Type::Instance Method`
- 생성자 참조 -> `Type::new`

```java
Consumer<String> c = System.out::println;
c.apply();

Arrays.sort(arr, (o1, o2) -> o1 > o2);
Arrays.sort(arr, String::compareToIgnoreCase);	// Type과 Return Type이 맞는 함수 지정가능

Function<String, Greeting> newGreetingWithName = Greeting::new;	// 생성자의 Supplier
Greeting greeting = newGreetingWithName.apply(“name!”);
```

- 메소드 또는 생성자의 매개변수로 Lambda의 입력 값을 받는다.
- 리턴 값 또는 생성한 객체는 Lambda의 리턴값이다.

## 자바에서 제공하는 함수형 인터페이스

- java.util.function 패키지에서 제공된다.

### Function<T, R>

- T 타입을 받아서 R 타입을 리턴하는 함수 인터페이스 `R apply(T t)`

```java
Function<Integer, Integer> plus10 = (i) -> i + 10;
System.out.println(plus10.apply(1))

> 11
```

- 함수 조합용 method

```java
Function<Integer, Integer> plus10 = (i) -> i + 10;
Function<Integer, Integer> multiply2 = (i) -> i * 2;

// andThen은 자신을 먼저 호출한다. (2 + 10) * 2 => 24
System.out.println(plus10.andThen(multiply2).apply(2));

// compose로 합셩하면 안에 함수가 먼저 호출된다. 2 * 2 + 10 => 14
System.out.println(plus10.compose(multiply2).apply(2));
```

### BiFunction<T, U, R>

- 두 개의 값(T, U)를 받아서 R 타입을 리턴하는 함수 인터페이스 `R apply(T t, U u)`

### Consumer<T>

- T 타입을 받아서 **아무값도 리턴하지 않는** 함수 인터페이스 `accept(T t)`
- 자주 사용되는 형식이고, 용어로도 많이 Consumer가 쓰이므로 기억해두자.

```java
Consumer<Integer> printT = (i) -> System.out.println(i);
printT.accept(10);
```

### Supplier<T>

- T 타입의 값을 제공하는 함수 인터페이스
- 인자가 없는 형식이며 역시나 용어로 많이 사용하므로 Supplier 형태가 어떤 것인지 기억해두자

```java
Supplier<Integer> get10 = () -> 10;
System.out.println(get10.get());
```

### Predicate<T>

- T 타입을 받아서 boolean을 리턴하는 함수 인터페이스 `boolean test(T t)`
- **filtering** 등에 많이 쓰일 수 있다

```java
Predicate<String> startsWith = (s) -> s.startWith("ABC");
if (startsWith.test("ABCDE")) {
	// do
}
```

### UnaryOperator<T>

- Function<T, R>의 특수한 형태로, 입력값 하나를 받아서 동일한 타입을 리턴하는 함수 인터페이스

```java
Function<Integer, Integer> plus10 = (i) -> i + 10;
UnaryOperator<Interger> plus10 = (i) -> i + 10;		// 동일
```

### BinaryOperator<T>

- BiFunction<T, U, R>의 특수한 형태로, 동일한 타입의 입렵값 두개를 받아 리턴하는 함수 인터페이스
- BiFunction<T, T, T>와 동일하다

## 함수형 프로그래밍을 위해 유용한 API

### Collection

- Iterable을 상속받는 인터페이스이다.

### forEach

- 내부 엘리먼트를 순회하며 각각의 요소들을 파라메터로 전달된 `Consumer`를 받아 처리한다.

```java
List<String> names = new ArrayList<>();
names.add(“aaa”);
names.add(“bbb”);

names.forEach(System.out::println);
```

### Spliterator

- iterator와 유사하지만 분할할 수 있는 기능을 가지고 있다.
- 분산처리에 유용하다.

```java
List<String> names = new ArrayList<>();
names.add(“aaa”);
names.add(“bbb”);

Spliterator<String> spliterator = names.spliterator();
Spliterator<String> trySplit = spliterator.trySplit();

while (spliterator.tryAdvance(System.out::println);
while (trySplit.tryAdvance(System.out::println);
```

### removeIf

- `Predicate`를 통해 해당하는(true를 반환하는) 값을 찾아 삭제한다.

```java
List<String> names = new ArrayList<>();
names.add(“aaa”);
names.add(“bbb”);

names.removeIf(s -> s.startsWith(“a”));
```

### Comparator

- 정렬에 사용되는 함수형 인터페이스이다.

```java
List<String> names = new ArrayList<>();
names.add(“aaa”);
names.add(“bbb”);

Comparator<String> compareToIgnoreCase = String::compareToIgnoreCase;
names.sort(compareToIgnoreCase);
names.sort(compareToIgnoreCase.reverse());
```

---

[1] 백기선, 더 자바 Java 8, Optional
