# ITEM1: 생성자 대신 정적 팩터리 메서드를 고려하라

## Static Factory Method

```java
// Public 생성자 - 클래스의 인스턴스를 얻는 전통적인 방법
public Laptop(String model, String company) {
	this.model = model;
	this.company = company;
}

// Static Factory Method
class Laptop {
	private String model;
	private String company;

	public static Laptop ofModelAndCompany(String model, String company) {
		Laptop laptop = new Laptop();
		laptop.model = model;
		laptop.company = company;
		return laptop;
	}
}
```

## 네이밍 관례

- `from` : 매개변수를 하나 받아서 인스턴스를 반환할 때
  - 영어의 늬앙스를 생각할 때 from은 하나에서 하나로 converting된다는 느낌
  - Date d = Date.from(instance);
- `of` : 여러 매개변수를 받아 적합한 인스턴스를 반환할 때
  - 영어의 늬앙스를 생각할 때 of는 여러가지 재료로 된 무언가를 표현하는 느낌
  - Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
- `instance` 혹은 `getInstance` : 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않는다.
- `create` 혹은 `newInstance` : 매번 새로운 인스턴스를 반환함을 보장한다.
- `get{Type}` : getInstance와 같으나 생성할 클래스가 아닌 다른 클래스의 정적 팩토리 메소드를 사용할 때
  - FileStore fs = Files.getFileStore(path)
- `new{Type}` : newInstance와 같으나 생성할 클래스가 아닌 다른 클래스의 정적 팩토리 메소드를 사용할 때
- `{type}` : getType, newType의 간결한 버전
  - List<Complaint> litany = Collections.list(legacyLitany);

## 장점

### 이름을 가질 수 있다.

- 한 클래스에 시그니처가 같은 생성자가 여러개 필요할 것 같으면, 생성자를 정적 팩토리 메소드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주자.

### 호출할 때마다 인스턴스를 새로 생성하지는 않아도 된다.

- 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식의 활용이 가능하다.

```java
public static Boolean valueOf(boolean b) {
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

- 인스턴스 통제(intance-controlled) 클래스로 만들 수 있다.
  - Singleton
  - 인스턴스화 불가(noninstantiable)
  - 불변값 클래스 : a == b일때만 a.equals(b)가 성립

### 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

- 하위타입 객체를 반환하도록 하면 사용자가 세부적인 구현을 모르고도 확장된 기능을 제공받을 수 있다.
- 동반 클래스(companion class) : Java8 이전에는 인터페이스에 정적 팩토리 메소드가 필요할 때 {Interface이름}s라는 이름으로 인스턴스화 불가 클래스를 만들어 제공하는 것이 관례였다. Java8부터는 인터페이스에서도 정적 메소드 제공이 가능하다.

### 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

### 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

## 단점

### 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩토리 메소드만 제공하면 하위 클래스를 만들 수 없다.

- inheritance대신 composition을 강제할 수 있으므로 오히려 장점이 될 수도 있다.

### 정적 팩토리 메소드는 프로그래머가 찾기 힘들다.

- 생성자처럼 이름에 대한 명확한 규칙이 없기 때문이다. 위에서 정리한 관례를 잘 활용한다.

# ITEM2: 생성자에 매개변수가 많다면 빌더를 고려하라

## 안티패턴

### 점층적 생성자 패턴(Telescoping Constructor Pattern)

- 생성자 오버로딩을 이용하여 매개변수가 늘어남에 따라 생성자도 늘려나가는 방식이다.
- 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵고, 순서를 실수하기 쉽다.

### 자바빈즈 패턴(JavaBeans Pattern)

- **매개변수가 없는 생성자**로 객체를 만든 후, Setter를 호출해 원하는 매개변수의 값을 설정하는 방식이다.
- 객체를 하나 만들기 위해 여러 개의 메소드를 호출해야 한다.
- 객체가 완전히 생성되기 전까지는 일관성(consistency)가 무너진 상태에 놓이기 된다.
- 값이 다 채워질 때까지 객체를 사용할 수 없도록 freezing시켜 쓰기도 하지만 빌더에 비해 런타임에 취약하다.
- 클래스를 불변으로 만들 수 없다.

<aside>
💡 **불변 객체(Immutable object)**
String 객체와 같이 한번 만들어지면 절대 값을 바꿀 수 없는 객체를 불변 객체라 한다. String에서도 StringBuilder를 통해 점층적인 객체 생성을 할 수 있다.

</aside>

### 빌더 패턴(Builder Pattern)

- 필수 매개변수만으로 생성자 혹은 정적 패토리를 호출해 빌더 객체를 얻는다.
- 빌더 객체가 제공하는 Setter를 통해 선택적 매개변수를 채워넣는다.
- `build()` 메소드를 호출해 최종 객체를 생성한다.

```java
public class NutritionFacts {
	private final int servingSize;
	private final int servings;
	private final int calories;
	private final int fat;
	private final int sodium;

	// static inner class로 만든다.
	public static class Builder {
		// field들이 외부와 중복된다. 이후 Lombok을 활용한 방법을 활용하자.
		private final int servingSize;
		private final int servings;
		private int calories = 0;
		private final int fat = 0;
		private final int sodium = 0;

		// 필수 파라메터는 생성자를 통해 받는다.
		public Builder(int servingSize, int servings) {
			this.servingSize = servingSize;
			this.servings = servings;
		}

		// 선택적 매개변수 Setter
		public Builder calories(int val) {
			calories = val;
			return this;	// this를 리턴해서 chaining이 가능하도록 한다.
		}
		public Builder fat(int val) {
			fat = val;
			return this;
		}
		public Builder sodium(int val) {
			sodium = val;
			return this;
		}

		// 최종 객체 생성
		public NutritionFacts build() {
			return new NutritionFacts(this);
		}
	}
}

// 사용코드
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
		.calories(100).sodium(35).fat(300).build();
```

- Python, Scala 등의 문법인 명명된 선택적 매개변수(Named Optional Parameters)와 유사한 사용법이다.

```python
def info(object, spacing=10, collapse=1):
	pass

info(odbchelper)                    ￼
info(odbchelper, 12)                ￼
info(odbchelper, collapse=0)        ￼
info(spacing=15, object=odbchelper)
```

- 구현에 비용이 발생하기 때문에 매개변수가 많거나 많아질 수 있는 경우에 적용하도록 한다. 하지만 Lombok을 이용하여 간단하게 구현하는 것 또한 가능하다.

```java
@Data
@Builder(builderMethodName = "hiddenBuilder")
public class NutritionFacts {
	private final int servingSize;
	private final int servings;
	@Builder.Default private final int calories = 0;
	@Builder.Default private final int fat = 0;
	@Builder.Default private final int sodium = 0;

	// 필수 파라메터가 입력된 hiddenBuilder(Lombok에 의해 생성된 빌더)를 반환한다
	public static NutritionFactsBuilder builder(int servingSize, int servings) {
		return hiddenBuilder().servingSize(servingSize).servings(servings);
	}
}
```

- 빌더를 이용하면 가변인수(varargs) 매개변수를 Setter마다 여러개 사용할 수 있다.

# ITEM3: private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 보장되지 않는 경우

- public 생성자가 존재하는 경우
- Serializable의 경우 역직렬화될 때
- 리플렉션 API인 AccessibleObject.setAccessible()을 사용해 private 생성자를 호출하는 경우

## 열거 타입을 이용한 리플렉션 방어

```java
public enum Speaker {
	INSTANCE;
	private Speaker getInstance() {
		return INSTANCE;
	}
}

// 실행코드
Speaker speaker1 = Speaker.INSTANCE.getInstance();
```

- 직관적이지 않기 때문에 보안이 중요한 프로젝트에서 활용한다.

## 일반적인 싱글턴의 구현(Lazy Instantiate)

```java
public class Speaker {
	private static Speaker instance;
	private Speaker() {}
	public static synchronized Speaker getInstance() {
		if (instance == null) {
			// 실제 사용전까지는 객체 생성을 미룰 수 있다.
			instance = new Speaker();
		}
	}
}
```

# ITEM4: 인스턴스화를 막으려거든 private 생성자를 사용하라

```java
public class UtilityClass {
	private UtilityClass() {
		throw new AssertionError();
	}
}
```

- 예외를 던져 리플렉션 공격에도 대비한다.
- private 생성자만 있는 경우 상속을 불가능하게 하는 효과도 있다.

# ITEM5: 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

## 의존 객체 주입 패턴

```java
public class SpellChecker {
	private final Lexicon dictionary;

	public SpellChecker(Lexicon dictionary) {
		this.dictionary = Objects.requireNonNull(dictionary);
	}
}
```

- 이 방식은 아주 단순하여 수많은 프로그래머가 이 방식에 이름이 있다는 사실도 모른채 사용해왔다.
- Java8의 `supplier<T>`를 활용하여 함수형으로 빌더를 전달할 수도 있다.
  - supplier<T>는 인자를 받지 않고 Type T 객체를 리턴하는 함수형 인터페이스이다.
  - `Mosaic create(Supplier<? extends Tile> tileFactory { ... }`

## DI 프레임워크

- Dagger, Guice, Spring과 같은 의존 객체 주입 프레임워크를 활용할 수 있다.
- Testablity, Flexibility를 높일 수 있다.
- Property Injection도 활용할 수 있다.

```java
// appliction.yml
company:
  address: '서울시 강남구'

// CompanyConfig.java
@Configuration
public class CompanyConfig {
	@Value("${zerobase.address}")
	private String address;
}
```
