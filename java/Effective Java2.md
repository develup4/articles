# ITEM6: 불필요한 객체 생성을 피하라

## 객체 재사용

- 재사용은 빠르고 세련되다.
- 특히 불변 객체는 언제든 재사용 할 수 있다. 그리고 안전함이 명백하다.
- 정적 팩토리 메소드를 제공하는 불변 클래스에서는 불필요한 객체 생성을 피할 수 있다.

## 생성 비용이 아주 비싼 객체

- 예를 들어 정규표현식과 같은 객체는 내부적으로 유한 상태 기계를 만들기 때문에 인스턴스 생성 비용이 높다.

```java
public class EmailUtil {
	// 비용이 큰 Pattern 인스턴스가 한번만 생성되도록 구현
	private static final Pattern EMAIL = Pattern.compile("[a-za-z0-9.-]\\\\\\\\.[a-zA-Z]{2,6}$");

	static boolean isEmailValid(String s) {
		EMAIL.matcher(s).matches();
	}
}
```

## Adapter

- 어댑터는 실제 작업은 뒷단 객체에 위임하고, 자신은 제2의 인터페이스 역할을 해주는 객체이다.
- 하나만 두고 재사용하는 것이 바람직하다.

## Auto Boxing 문제

- `Boxing type`보다는 `Primitive Type`을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

```java
public static long sum() {
	Long sum = 0L;
	for (long i = 0; i <= Integer.MAX_VALUE; i++) {
		sum += i;	// i가 수많이 Boxing된다.
	}
	return sum;
}
```

- 무조건으로 `primitive type`을 사용하라는 의미는 아니다. `Boxing type`은 null이 될 수 있다. 0과 null은 의미가 다르므로 필요한 일이 있을때는 사용해야 한다.

## 객체 생성은 비싸니 피해야 한다?

- 주제인 불필요한 객체의 생성을 피하라와는 다른 이야기이다. 요즘의 JVM에서는 작은 객체의 생성과 회수에 큰 부담을 느끼지 않는다. 데이터베이스 연결과 같이 생성비용이 엄청 큰 객체가 아니라면 작은 객체에 대해 객체 풀을 이용하는 것도 성능상의 이점이 별로 없다.
- 객체의 생성은 프로그램의 명확성, 간결성, 기능성에 도움이 되는 일이다. 무조건적인 것은 없다.
- `ITEM 50`(새로운 객체를 만들어야 한다면 기존 객체를 재사용하지 마라)과 대조적이다.

# ITEM7: 다 쓴 객체 참조를 해제하라

## JAVA는 가비지 컬렉터가 있는데 메모리 누수는 어디서 일어날까?

- 다 쓴 참조(obsolete reference)를 여전히 가지고 있기 때문이다. 객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체뿐아니라 그 객체가 참조하는 모든 객체, 그리고 또 그 객체들이 참조하는 모든 객체를 회수해가지 못한다.
- 유효 Scope 밖으로 넘어가면(Unreachable Object가 되었을 때) 자동으로 가비지 컬렉터의 대상이 된다.
- 순환참조는 인스턴스를 회수해갈 기회가 오지 않는다. ITEM8의 close()를 명시적으로 사용하자.

## 그렇다면 해법은?

- 해법은 간단하다. **해당 참조를 다 썼을 때 null 처리(참조 해제)**하면 된다. 실수로 사용하려고 하면 `NullPointerException`을 발생시킬 수 있는 추가적인 이점도 있다.
- 하지만 객체 참조를 null 처리하는 일을 모든 참조에 할 필요는 없다. 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위 밖으로 밀어내는 것이다.
- null 처리가 필요한 경우는 자기 메모리를 직접 관리하는 클래스를 다룰 때이다. 비활성 영역의 객체가 더 이상 쓸모없다는 것을 프로그래머만이 아는 경우이다. 프로그래머는 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더는 쓰지 않을 것임을 가비지 컬렉터에 알려야 한다.
- 캐시 역시 메모리 누수를 일어키는 주범이다. `WeakHashMap` 자료구조를 사용해 캐시를 만들자.
- 리스터(Listener)와 콜백(Callback) 역시 메모리 누수의 주점이다. 등록만 하고 해지하지 않은 콜백들은 계속 쌓여갈 것이다.

# ITEM8: finalizer와 cleaner 사용을 피하라

- 자바는 finalizer, cleaner 두 가지 객체 소멸자를 제공한다.

## C++의 소멸자와는 다른 개념이다.

- **즉시 수행된다는 보장이 없다.** 이는 가바지 컬렉터 구현마다 천차만별이다. 고객의 시스템에서는 엄청난 재앙을 일으킬지도 모른다.
- 자바 언어 명세에서는 수행 여부조차 보장하지 않는다.

```java
// Indicate to the virtual machine that it would be a good time to collect available memory. Note that, this is a [ hint ] only.
// Hint라 함은 보장하지 않는 내용이라는 뜻이다.
public static void gc() {
	RUNTIME.gc();
}
```

- 즉, 쓰지 말아야 한다.

## finalizer

- `finalizer 쓰레드`는 다른 애플리케이션 쓰레드보다 우선순위가 낮아서 실행될 기회도 얻지 못한채 `OutOfMemoryError`로 죽을 수 있다.
- finalize 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료된다. 그리고 다른 쓰레드가 이처럼 훼손된 객체를 사용하려 한다면 어떻게 동작할지 예측할 수 없다.
- finalizer는 심각한 성능 문제를 가지고 있다. 가비지 컬렉터의 효율을 떨어뜨리기 때문이다.
- finalizer 공격에 의한 심각한 보안문제도 가지고 있다. 방어를 위해서는 아무일도 하지않는 finalizer 메서드를 만들고 final로 선언하자.
- Java9에서 deplicate되었다.

## cleaner

- Java9에서 제공된 대안이지만 역시나 예측할 수 없고, 느리고, 덜 위험해도 위험하다. 일반적으로 불필요하다.
- 자신을 수행할 스레드를 제어할 수 있다는 면에서 낫지만 여전히 보장은 없다.

## 그렇다면 해결책은?

- `AutoCloseable` 인터페이스를 구현하고 `close()` 메소드를 호출하자.
- 일반적으로 ITEM9의 `try-with-resource`와 같이 사용한다.
- close()가 여러번 불리지 않도록 flag를 사용해 닫힌 객체에 사용시 `IllegalStateException`을 던지자.
- close()가 호출하지 않는 것에 대한 안전망으로 finalizer나 cleaner를 사용할 수 있지만 그럴만한 값어치가 있는지 심사숙고하자.

# ITEM9: try-finally보다는 try-with-resource를 사용하라

- 자바 라이브러리에서는 close() 메소드를 호출해 직접 닫아줘야 하는 자원이 많다.
- `InputStream`, `OutputStream`, `java.sql.Connection` 등
- Effective C++에서 익힌 것처럼 `RAII`를 활용하자.

## 여러 자원에 대해서 try-finally를 사용할 때 문제

- 중첩 try-finally는 재앙이 발생할 수 있다.

```java
static void copy(String src, String dst) throws IOException {
	InputStream in = new FileInputStream(src);
	try {
		OutputStrema out = new FileOutputStream(src);
		try {
			byte buf = new byte[100];
			int n;
			while((n = in.read(buf)) >= 0) {	// 1. read()에서 예외가 발생한다면?
				out.write(buf, 0, n);
			}
		} finally {
			out.close();	// 2. 예외처리 중 close()에서 예외가 발생한다면?
		}
	} finally {
		in.close();		// 3. read()에 대한 예외는 소실된다.
	}
}
```

## 해결책: try-with-resource 활용

- Java7에 의해 제공되는 기능이다.
- 해당 자원이 `AutoCloseable` 인터페이스를 구현해야 한다.

```java
static void copy(String src, String dst) throws IOException {
	try (InputStream in = new FileInputStream(src);			// 괄호안에 자원들을 ;로 연결
		OutputStream out = new FileOutputStream(dst)) {		// FileXXXStream->AutoCloseable 구현
		byte[] bif = new byte[100];
		int n;
		while ((n = in.read(buf)) >= 0) {
			out.write(buf, 0, n);
		}
	}
}
```
