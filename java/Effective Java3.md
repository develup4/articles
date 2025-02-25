# ITEM10: equals는 일반 규약을 지켜 재정의하라

### equals를 재정의하지 않는 것이 최선인 경우

- 각 인스턴스가 본질적으로 고유하다. 값이 아닌 동작하는 개체를 표현하는 Thread 클래스와 같은 경우
- 인스턴스의 논리적 동치성(logical equality)을 검사할 일이 없다.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
- 클래스가 private이거나 package-private이고 equals 메소드를 호출할 일이 없다.

### equals를 재정의해야하는 경우

- 객치 식별성(object identity)이 아니라 논리적 동치성을 확인해야 하는 경우

### equals가 만족해야 하는 것

> 반사성(reflexivity) : x.equals(x) -> true
> 대칭성(symmetry) : x.equals(y) -> true then y.equals(x) -> true
> 추이성(transitivity) : x.equals(y) -> true, y.equals(z) -> true then x.equals(z) -> true
> 일관성(consistency) : x.equals(y)를 여러번 호출해도 항상 결과는 같게
> not null : if x is not null, then x.equals(null) -> false

### 대칭성 위반의 예

```java
public final class CaseInsensitiveString {
	private final String s;

	@Override
	public boolean equals(Object o) {
		if (o instanceof CaseInsensitiveString) {
			return s.equalsIgnoreCase(((CaseInsensitiveString)o).s);
		}
		if (o instanceof String) {		// String.equals(CIS)는 안된다. 대칭성 위반.
			return s.equalsIgnoreCase((String)o);
		}
		return false;
	}
}

List<CaseInsensitiveString> list = new ArrayList<>();
list.add(new CaseInsensitiveString(“Polish”);
list.contains(“polish”);	// OpenJDK가 false를 반환하지만 벤더에 따라 장담할 수 없다!
```

### 리스코프 치환 원칙과 관련해서

- **구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.**
- 하지만 리스코프 치환 원칙에 의하면 그 타입의 모든 메소드가 하위 타입에서도 똑같이 잘 작동해야 한다.
- 괜찮은 우회방법은 `상속 대신 합성`을 사용하는 것이다.
- 상위 클래스가 추상클래스와 같이 직접 인스턴스를 만들 수 없다면 애초에 문제가 발생하지 않는다.

### View method

```java
public class ColorPoint {
	private final Point point;

	public Point asPoint() {
		return point;	// Point와의 equals에 활용한다.
	}
}
```

### 기타 주의사항

- **equals 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안된다.** 네트워크, 쓰레드 등등의 요인들.
- 모든 객체는 null과 같지 않아야 한다. `if (o == null) return false`

### 양질의 equals 구현 방법

- float와 double 필드는 `Float.compare()`, `Double.compare()`를 사용하여 비교한다. `Float.equals()`, `Double.equals()`는 오토박싱을 수반할 수 있어 성능에 좋지 않다.
- 비교하기가 아주 복잡한 필드의 경우 `필드의 표준형(canonical form)`을 저장해둔 후 비교하면 훨씬 경제적이다. 특히 `불변 클래스`가 제격이다.
- 비교 순서도 성능을 좌우하기도 한다. 다를 가능성이 더 크거나 비교하는 비용이 싼 필드를 먼저 비교하자.
- **Object 외의 타입을 매개변수로 받는 equals 메소드는 선언하지 말자.**

### 전형적인 검사 패턴

```java
@Override
public boolean equals(Object o) {
	if (this == o) return true;
	if (o == null || getClass() != o.getClass()) return false;
	Dto dto = (Dto)o;
	return Objects.equals(model, dto.model) && Objects.equals(company, dto.company);
}
```

## ITEM11: equals를 재정의하려거든 hashCode도 재정의하라

### 명세

- 객체의 hashCode 메소드는 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다.
- **equals가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환**해야 한다.
- equals가 두 객체를 다르다고 판단했더라도 서로 다른 hash를 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시 테이블의 성능이 좋아진다.

### 작성요령

- 파생 필드는 hashCode 계산에서 제외해도 된다. 즉, 다른 필드로부터 계산해낼 수 있는 필드는 모두 무시해도 된다. 또한 equals 비교에 사용되지 않은 필드는 **반드시** 제외해야 한다.
- Objects 클래스는 임의의 개수만큼 객체를 받아 hashCode를 계산해주는 정적 메소드인 hash를 제공한다. 하지만 아쉽게도 속도는 더 느리다.
- hashCode가 처음 불릴 때 계산하는 `지연 초기화(lazy initialization)`을 사용하는 것도 좋지만, Thread safe하도록 주의해야 한다.
- 성능을 높이기 위해 핵심 필드를 생략하자 말자. 정작 hash table의 성능이 심각하게 떨어질 수 있다.

### Lombok을 활용하는 방법

```java
@EqualsAndHashCode
public class EqualAndHashCodeExample {
	private String name;
	private double score;

	@EqualsAndHashCode.Exclude
	private int id;
}
```

## ITEM12: toString을 항상 재정의하라

### 규약

- 간결하면서 사람이 읽기 쉬운 형태의 유익한 정보를 반환해야 한다.
- 모든 하위 클래스에서 이 메소드를 재정의하라!
- 포맷을 명시하든 아니든 의도는 명확하게 밝혀야 한다.

### 기본 구현

`className@hex-hashCode`

### Lombok을 활용하는 방법

```java
@ToString
public class Laptop {
	@ToString.Exclude
	private String company;
	private String modelName;
}
```

## ITEM13: clone 재정의는 주의해서 진행하라

### 즉, 되도록 재정의하지 말라

- `복사 생성자`나 `복사 팩토리`를 사용하자
- 단 하나의 예외가 있다. **배열을 복제할 때는 배열의 clone 메소드를 사용하기를 권장**한다. **Deep copy**를 제공하기 때문이다.
- 다만 아래 경우처럼 recursive하게 deep copy를 제공하지는 않기 때문에, 위 코드와 같은 문제가 발생할 수 있음을 주의한다.

```java
Laptop[] a = {new LapTop(“gram”, “samsung”)};
Laptop[] b = a.clone();
b[0].setCompany(“LG”);	// a[0] 역시 변한다?!
```

- 특히나 불변 클래스는 의미상으로도 제공하지 않는 것이 좋다.
- 하위 클래스에서 Cloneable을 지원하지 못하게 하는 clone 메소드

```java
@Override
protected final Object clone() throws CloneNotSupportedException {
	throw new CloneNotSupportedException();
}
```

### clone 메소드의 문제

- `Cloneable` 인터페이스의 메소드이지만 선언된 곳은 `Object`이며 protected로 정의되어 있다.
- `Cloneable` 인터페이스는 Object의 clone의 동작방식을 결정한다. `Cloneable`의 구현체인 경우에는 필드 대 필드 단위의 복사를 진행하며, 그렇지 않은 인스턴스를 호출하면 `CloneNotSupprotedException`을 던진다.
- 생성자를 호출하지 않고 객체를 생성하게 된다. 깨지기 쉽고, 위험하고, 모순적인 메커니즘이 될 수 있다.

### clone 메소드의 일반적인 의도

```java
x.clone() != x	// true(다른 참조값)
x.clone().getClass() == x.getClass()		// true
x.clone().equals(x)	// 일반적으로 true
```

### 일반적인 구현

```java
@Override
public PhoneNumber clone() {
	try {
		// 필드 단위의 복제를 구현한다(Cloneable 아키텍쳐의 기초)
		return (PhoneNumber)super.clone();		// 공변 반환 타입
	} catch (CloneNotSupportedException e) {
		throw new AssertionError();	// clone이 Cloneable의 메소드이므로 일어날 수 없다.
	}
}
```

- Java는 `공변 반환 타입(covariant return typing)`을 지원한다. 즉, override시 **반환 타입이 더 넓어질 수 있는 것**이다. clone은 공변 반환 타입으로 반환하는 것이 권장하는 방식이다.
- `Cloneable`을 구현하는 모든 클래스는 clone을 재정의해야 한다. 접근 지정자는 public으로, 반환 타입은 클래스 자신으로 변경한다.

## ITEM14: Comparable을 구현할지 고려하라

### Comparable

- `compareTo()` 메소드는 단순 동치성 비교에 더해 순서까지 비교할 수 있으며 제네릭하다.
- `Comparable`을 구현했다는 것은 그 클래스의 인스턴스들에는 **자연적인 순서(natural order)**가 있음을 뜻한다.
- 따라서 **정렬이 가능**하다.
- Java 플랫폼 라이브러리의 모든 값 클래스와 열거 타입은 `Comparable`을 구현했다. 알파벳, 숫자, 연대와 같이 순서가 명확한 값 클래스를 작성한다면 반드시 `Comparable` 인터페이스를 구현하자.
- 정렬된 컬렉션들은 **동치성을 비교할 때 equals 대신 compareTo를 사용**한다.

### 주의사항

- 기존 클래스를 확장한 구체 클래스에서 새로운 값 컴포넌트를 추가했다면 compareTo 규약을 지킬 방법이 없다.
- equals의 우회법과 동일하게 해결할 수 있다. 상속대신 합성으로 확장하고 view 메소드를 제공하자.
- `compareTo()` 메소드로 수행한 동치성 테스트의 결과는 **equals와 같아야** 한다.
- 역시나 가장 핵심적인 필드를 먼저 비교하도록 구현하자.

### 일반적인 구현

```java
public final class CaseInsensitiveString implements Comparable<CaseInsensitiveString> {
	public int compareTo(CaseInsensitiveString cis) {		// 자신의 타입으로만 비교하는 것이 일반적
		return String.CASE_INSENSITIVE_ORDER.compare(s, cis.s);
	}
}
```

### Java 7 이후의 compare

```java
// Java 7 이전
1 < 2;	// 정수 비교
Float.compare(1.2f, 3.4f);
Double.compare(1.2, 3.4);

// Java 7 이후
Integer one = 1;
one.compareTo(2);		// 정수 비교에도 박싱된 기본 타입 클래스의 compare를 활용하자
```

### 함수형 인터페이스 `Comparator`의 활용

```java
// 일반적인 구현
public int compareTo(Person p) {
	int result = Integer.compare(age, p.age);
	if (result == 0) {
		result = Double.compare(height, p.height);
	}
	if (result == 0) {
		result = name.compareTo(p.name);
	}
	return result;
}

// 함수형 인터페이스
private static final Comparator<Person> COMPARATOR = Comparator.comparingInt(Person::getAge)
	.thenComparingDouble(Person::getHeight)
	.thenComparing(person -> person.getName());

public int compareTo(Person p) {
	return COMPARATOR.compare(this, p);
}
```
