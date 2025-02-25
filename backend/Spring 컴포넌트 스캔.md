# 컴포넌트 스캔

이번엔 조금 더 실전적이고 스프링을 편하게 쓸 수 있는 방법에 대해 정리해볼 것이다.

- 스프링은 설정 정보가 없어도 자동으로 Bean을 등록하는 `컴포넌트 스캔(Component Scan)`이라는 기능을 제공한다.
- 또한 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공한다.

열심히 AppConfig를 만들고 `@Bean`을 붙여서 생성한 뒤에 `getBean()`을 호출해서 얻어오는 우아하지 못한(?) 방식을 벗어나, 스프링이 자동으로 무언가를 해주는 것이다. 이제부터 구체적으로 알아보자.

```java
@Configuration
@ComponentScan()
public class AutoAppConfig {

}
```

[Spring Bean과 Bean 컨테이너](Spring%20Bean%E1%84%80%E1%85%AA%20Bean%20%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%82%E1%85%A5%20a42a1b1c0f2b4ab2a4e69febe5b12296.md)

위 링크에서의 글과는 달리 클래스 안의 내용이 모두 사라져버렸다. 대신 `@ComponentScan`이라는 애노테이션이 하나 추가되었을 뿐이다. 이제 이 녀석은 `@Component`라는 애노테이션이 붙은 클래스를 몽땅 찾아서(나름 규칙이 있긴하지만), Bean으로 등록시킨다.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {}

@Component(“RatePolicyBean”)
public class RateDiscountPolicy implements DiscountPolicy {}
```

이전 예제들에서 익숙한 이 녀석들이 애노테이션 하나에 Bean으로 등록이 되었다. 두번째 애노테이션처럼 이름을 지정하면 Bean의 이름도 지정할 수 있다. 이번엔 이렇게 등록한 **Bean을 주입받는 방식**도 살펴보자.

```java
@Component
public class MemberServiceImpl implements MemberService {
	private final MemberRepository memberRepository;

  @Autowired
  public MemberServiceImpl(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
  }
}
```

이렇게 작성을 하면 `MemberServiceImpl` 역시 Bean이 될뿐더러 생성자 위에 `@Autowired` 애노테이션을 붙여주면 자동으로 객체가 주입이 된다. 스프링 컨테이너에 등록된 테이블을 뒤져서 맞는 타입의 Bean 객체를 넣어주는 것이다. 이는 그 이전 방식들에 비해 아주아주 편리하다. 생성자 외에도 여러 방법이 있는데 생성자 주입이 가장 권장되는 방식이다.

```java
@ComponentScan(
	basePackages = {"hello.core", "hello.service"}
}
```

아까 모든 컴포넌트를 찾는다라는 말을 했었는데, 구체적으로는 위에서 지정한 디렉토리의 하위에서 모든 @Component를 찾도록 되어있다. 처음처럼 basePackages를 지정하지 않으면 **@ComponentScan을 붙인 클래스의 Package를 기준**으로 검색하며 보통 이렇게 사용한다고 한다. 그러기 위해서는 설정정보 클래스를 프로젝트의 최상단에 두어야할 것이다.

컴포넌트 스캔의 대상들은 아래와 같다.

- `@Component` : 컴포넌트 스캔에서 사용
- `@Controlller` : 스프링 MVC 컨트롤러에서 사용
- `@Service` : 스프링 비즈니스 로직에서 사용
- `@Repository` : 스프링 데이터 접근 계층에서 사용
- `@Configuration` : 스프링 설정 정보에서 사용

위 다섯가지 애노테이션이 붙은 클래스들은 Bean이 된다는 이야기이다.

```java
@Component
public @interface Controller {

}
```

IntelliJ 등을 통해 `@Controller` 애노테이션을 따라가보면 애노테이션 정의 위에 `@Component`가 붙어있는 것을 알 수 있다. 따라서 Bean으로 등록되는 것이다. 다만 이런 애노테이션에는 순수 JAVA에서는 상속관계라는게 없는데 스프링에 의해서 가능해진 기능이란 것은 주의하자. 순수 JAVA에서도 표준으로 받아들이면 좋은 기능인 것 같다.

```java
@ComponentScan(
  includeFilters = {
    @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class)
    },
  excludeFilters = {
    @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
    @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)
  }
)
```

자동으로 Bean을 스캔하기 때문에 당연하게도 검색에서 제외되는 Bean도 정의를 할 수가 있어야 한다. 위 코드를 보면 사용법은 어렵지 않겠지만 많이 사용되지는 않는다고 한다. 특히나 IncludeFilter의 경우는 더욱 더 그렇다.

만약 자동 컴포넌트 스캔 중에 이름 혹은 타입이 동일한 Bean이 존재하여 충돌이 일어나면 어떻게 될까? 최근의 수정 기준으로 이야기하자면 스프링이 충돌시 에러를 발생시켜 개발자가 충돌을 알 수 있도록 하였다. 일단은 걱정할 필요가 없는 것이다.

이제 컴포넌트 스캔을 통해 Bean을 스프링 컨테이너에 등록시켰으니, Bean을 쉽게 주입받는 방법에 대해 자세히 알아보자.

# 의존관계 자동 주입

의존관계 자동 주입의 방법에는 아래 네 가지가 있다.

- 생성자 주입
- Setter 주입
- 필드 주입
- 일반 메서드 주입

먼저 앞서 잠깐 소개했고 가장 많이 사용하는 방법인 `생성자 주입 방식`은 아래와 같이 사용한다. 생성자 주입의 경우 처음 Bean이 세팅되면 불변이다. 전략 패턴같이 사용할 수 없다는 의미이지만 이런 유즈케이스는 많이 않으므로 가장 많이 사용되는 방식이다.

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
  this.discountPolicy = discountPolicy;
}
```

가장 많이 사용되는 방식답게 생성자가 하나만 있는 경우에는 @Autowired를 생략하여도 생성자 주입이 이루어진다. 개인적으로는 무조건 가능한 명시적인게 좋지않나 하는 생각에 붙이는걸 선호한다.

다음은 `Setter 주입`이다. 동적으로 변경되는 경우에도 사용할 수 있지만 사용빈도는 높지 않다고 한다.

```java
private MemberRepository memberRepository;

@Autowired
public void setMemberRepository(MemberRepository memberRepository) {
	this.memberRepository = memberRepository;
}
```

다음은 간결함의 끝판왕인 필드 주입이다. 결론부터 말하자면 사용하지 말자. 뒤에 `Lombok` 라이브러리에 대해 설명할 것이다. 해당 기능을 활용한다면 좋다.

```java
@Component
public class OrderServiceImpl implements OrderService {

@Autowired
private MemberRepository memberRepository;	// 끝!
```

필드 주입은 외부에서 변경이 불가능하고 따라서 테스트도 쉽지 않다. 그리고 너무나도 명시적이지 않으며, DI 프레임워크가 없다면 Null reference exception을 마주하게 될 것이다. Lombok이 없으면 모르겠는데 있으니 단독으로는 사용하지 말 것!

마지막으로 일반적인 메소드에서도 주입이 가능하다. 사실상 원한다면 어떤 형태로든 받을 수 있는 것이다. 하지만 이 역시 비추천이다.

```java
@Autowired
public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
  this.discountPolicy = discountPolicy;
}
```

이번에는 `@Autowired`의 옵션에 대해 알아보자. 애초에 이런 옵션이 쓰이지 않는게 좋을 수 있지만 방어적인 차원에서 알아두자. Optional은 KOTLIN의 null 처리처럼 쓸 수 있는 JAVA 문법이지만 더 간단한 문법을 원한다! 자바에게!

```java
//호출 안됨
@Autowired(required = false)
public void setNoBean1(Member member) {
	System.out.println("setNoBean1 = " + member);
}

//null 호출
@Autowired
public void setNoBean2(@Nullable Member member) {
	System.out.println("setNoBean2 = " + member);
}

//Optional.empty 호출
@Autowired(required = false)
public void setNoBean3(Optional<Member> member) {
	System.out.println("setNoBean3 = " + member);
}
```

생성자 주입을 사용해서 Bean을 주입받을 수 없는 경우에는 컴파일러를 통해서 통지를 받는편이 가장 안전하기 때문에 생성자 주입이 가장 많이 쓰이는 것 같다. 컴파일(에)러 짱! 그리고 더 안전한 처리를 위하여 `final` 키워드를 가미해볼 수 있다. 생성자 주입 방식은 불변에만 사용하기 때문이다.

```java
private final MemberRepository memberRepository;
private final DiscountPolicy discountPolicy;

@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
}
```

이러한 특성 때문에 생성자 주입은 가장 JAVA 본연의 기능을 잘 살린다 할 수 있다. 생성자 주입이 권장되는 이유라고 할 수 있다.

그리고 여기서 찰떡인 라이브러리가 아까도 잠깐 언급했던 `Lombok`이다. 어떻게 편리해지는지 코드로 바로 살펴보자.

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
   private final MemberRepository memberRepository;
   private final DiscountPolicy discountPolicy;
}
```

`@RequiredArgsConstructor`에 의해 생성자가 자동으로 생성된다. 애노테이션에 의한 메타 프로그래밍이라 할 수 있다. 이제 가장 간단한 필드 주입의 형태로 가장 안전한 생성자 주입방식은 구현할 수 있게 되었다.

# 자동 주입시 충돌 문제

아래와 같은 Bean이 자동주입되면 어떤일이 벌어질까?

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}

@Component
public class RateDiscountPolicy implements DiscountPolicy {}

@Autowired
private DiscountPolicy discountPolicy;
```

컴파일러가 친절하게도 오류를 발생시켜준다. 다행이다. 그렇다면 이런 충돌을 어떻게 해결할까? 여러가지 방벙이 있다. 하나하나씩 살펴보도록 하자.

먼저 필드 명을 Bean 이름으로 변경하는 방법이 있다. 음...극혐이다(?) 문법만으로 알아낼 수 없는 이런 매직은 정말이지 싫다. 근데 아무튼 된다.

```java
@Autowired
private DiscountPolicy rateDiscountPolicy
```

다음은 `@qualifier`를 활용해 명시적 이름을 지정하는 방식이다. 생성과 사용에 이름을 붙여서 짝을 맞춘다.

```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}

@Autowired
public DiscountPolicy setDiscountPolicy(@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    return discountPolicy;
}
```

다음은 `@Primary` 애노테이션을 활용하는 방법이다. 동작은 간단하다. @Primary 애노테이션이 붙은 Bean이 우선적으로 선택된다.

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

@Primary와 @Qualifier가 함께 쓰이면 어떻게 될까? 항상 그렇듯 명시적인 것이 자동보다 우선권을 가진다. @Qualifier가 우선적으로 쓰인다. 이런 사항은 유즈케이스가 있는데, 메인 데이터베이스에 대한 Connector와 테스트용 데이터베이스에 대한 Connector가 있을 때 메인에 @Primary, 서브에 필요시 @Qualifier를 사용하면 깔끔하게 처리할 수 있다.

# Bean을 활용한 전략패턴 구현

아래 테스트 함수를 전략패턴을 통해 구현해보자. `discount 함수`의 결과가 `Policy`에 의해 결정될 것이다.

```java
@Test
void findAllBean() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

    DiscountService discountService = ac.getBean(DiscountService.class);

    Member member = new Member(1L, "userA", Grade.VIP);
    int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");

    assertThat(discountPrice).isEqualTo(1000);
}
```

그리고 아래 코드에서 Map과 List를 통해 Bean을 다발로 전달받아서 전략패턴을 구현한다. (타입으로 Bean을 얻어오면 매치되는 모든 Bean을 가져온다는 것을 상기하자.)

이제 아래 코드에서 생성자 주입 부분을 집중해서 살펴보자.

```java
static class DiscountService {
   private final Map<String, DiscountPolicy> policyMap;
   private final List<DiscountPolicy> policies;

   public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
      this.policyMap = policyMap;
      this.policies = policies;

      System.out.println("policyMap = " + policyMap);
      System.out.println("policies = " + policies);
   }

   public int discount(Member member, int price, String discountCode) {
      DiscountPolicy discountPolicy = policyMap.get(discountCode);

      System.out.println("discountCode = " + discountCode);
      System.out.println("discountPolicy = " + discountPolicy);
      return discountPolicy.discount(member, price);
   }
}
```

---

[1] 김영한, 인프런, 스프링 핵심원리
