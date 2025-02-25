그동안 Node.js, Nest.js, Django 등 웹 프레임워크를 공부해왔지만 스프링이 가지는 차별점은 무엇일까? 우선 비교의 위상 자체가 잘못되었다. 나열한 위 프레임워크들은 Spring MVC와 비교되어야 할 녀석들이고,

스프링의 본질은,

<aside>
💡 좋은 객체 지향 애플리케이션을 개발할 수 있게 도와주는 프레임워크

</aside>

이다.

구체적인 예로 바로 들어가보자.

```java
public class OrderServiceImpl implements OrderService {
		// private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
		private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
}
```

이 코드는 `다형성`을 이용해서(그것도 `전략 패턴`을 사용하여) **switch~case문을 대체**하였다. 지금까지는 이정도로도 충분히 만족스웠지만 정책이 변할 때는 주석처리한 라인처럼 분명히 클라이언트 코드의 수정이 이루어지고 있다. 코드가 **추상화(Interface)에 의존하지 않고 구체화(RateDiscountPolicy)에 의존**하고 있기 때문이다.

이는 `SOLID` 원칙 중 `DIP`를 위반하고 있다. 또한 기능을 확장하는데 변경이 필요하다. 세트인 `OCP` 또한 위반하고 있다.

그렇다면 어떻게 DIP 문제를 해결할 것인가? ~~(사실 이름에 답이 있다)~~

```java
public class OrderServiceImpl implements OrderService {
      private DiscountPolicy discountPolicy;

		public OrderServiceImpl(DiscountPolicy discountPolicy) {
			this.discountPolicy = discountPolicy;
		}
}
```

서비스를 생성하는 곳에서 적절한 구현체를 넣어주고 `ServiceImpl`은 인터페이스 형태로 받아서 사용을 하면 문제가 해결된다. 이는 비단 스프링만의 해결방식은 아니며 C++에서도 이러한 방식의 해결을 위해 열심히 구현체를 전달하고 전달해서, 위와 같은 해결을 하려고 노력한다. 그렇다면 스프링에서는 어떻게 세련된 문제해결을 보여줄 것인가?

우선 스프링에서는 문제 해결을 위하여 **관심사의 분리**를 이용한다. 비유하자면 객체지향의 다형성은 로미오와 줄리엣이라는 대본이 있을때 각 배역에 어떤 배우가 캐스팅되더라도 전체 극의 내용은 변함이 없는 것과 같은 것이고, 여기서 관심사의 분리란 배역에 대한 캐스팅을 별도의 역할로 분리해서 생각하자는 것이다.

그래서 공연기획자에 해당하는 코드가 추가된다.

```java
public class AppConfig {
      public MemberService memberService() {
          return new MemberServiceImpl(memberRepository());
      }

      public OrderService orderService() {
          return new OrderServiceImpl(memberRepository(), discountPolicy());
      }

      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }

      public DiscountPolicy discountPolicy() {
          return new FixDiscountPolicy();
      }
}
```

AppConfig는 애플리케이션의 실제 동작에 필요한 구현 객체를 생성한다. 이는 SOLID 원칙 중 `SRP`와도 관계가 있다. 비지니스 로직처리와 의존관계 설정이라는 두 개의 책임을 두 개의 클래스로 분리한 것이다.

또한 공연기획자가 생성한 객체를 생성자를 통해서 **입력(주입)**받는다. 이를 통해서 서비스 구현체는 구체적인 정책을 모르고도 작동할 수 있게 된다.

```java
public static void main(String[] args) {
      AppConfig appConfig = new AppConfig();
      MemberService memberService = appConfig.memberService();
      Member member = new Member(1L, "memberA", Grade.VIP);
      memberService.join(member);
}
```

클라이언트(`MemberServiceImpl`) 입장에서 의존성이 외부에서 주입된다고 하여, 이를 **의존성 주입(Dependancy Injection)**이라고 한다.

> 비슷한 개념으로 DL(Dependancy Lookup)이 있다. 의존성을 주입받지 않고 찾기만 하는 개념인데, 뒤에 프로토타입 빈에서 소개될 것이다.

AppConfig의 등장으로 애플리케이션이 크게 사용 영역과 객체를 생성하고 **구성(Configuration)** 영역으로 분리되었다. 이제 슬슬 스프링은 세팅하는게 너무 복잡해라고 언뜻언뜻 들었던 이야기가 기억나기 시작한다. 점점 애노테이션도 사용하고 최대한 편해지려고 노력하지만 본질은 **의존관계**가 설정에서 이미 정의되기 때문이다.

그런데 잘 생각해보면 아직 스프링은 사용하지도 않았다. 스프링의 주요기능인 DI를 java로 구현했을뿐이다. 이제부터 진짜로 스프링을 사용해보도록 하자.

```java
@Configuration
public class AppConfig {
      @Bean
      public MemberService memberService() {
          return new MemberServiceImpl(memberRepository());
      }
      @Bean
      public OrderService orderService() {
          return new OrderServiceImpl(
                  memberRepository(),
                  discountPolicy());
      }
      @Bean
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }
      @Bean
      public DiscountPolicy discountPolicy() {
          return new RateDiscountPolicy();
      }
}
```

기존 AppConfig에 `애노테이션`(@가 붙은 키워드)을 추가하였다. 애노테이션은 C++에서 하던 것처럼 부가적인 정보를 전달해서 메타 프로그래밍을 가능하게 해준다. 애노테이션에 대한 구체적인 문법과 구현은 다른 글을 통해 정리하려 한다.

`@Configuration`에 의해 공연기획의 주체는 **Spring Container**가 되고, 공연의 객체는 `@Bean`에 의해 정의된다. 또한 AppConfig를 사용하는 코드는 아래처럼 변경되었다.

```java
public static void main(String[] args) {
  // AppConfig appConfig = new AppConfig();
  // MemberService memberService = appConfig.memberService();
     ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
     MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
}
```

설정의 방식은 **XML을 사용하는 방법**과 **애노테이션(AnnotationConfigApplicationContext)**을 사용하는 방법이 있는데, 후자가 현재는 절대적인 대세이므로 앞으로 이 방식으로 사용하도록 한다.

> XML 방식은 아래와 같이 GenericXmlApplicationContext를 사용한다. 이 방식은 컴파일없이도 의존관계를 수정할 수 있는 아주 큰 장점이 있지만 아래에서 보듯 작성이 귀찮다. 또한 working code를 중요시하는 애자일 문화 속에서 이 방식은 애노테이션에게 밀려버리고 말았다. 아직 사용하는 레거시가 있을 수 있으니 알아는 두자.

```java
ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="<http://www.springframework.org/schema/beans>"
         xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>"
         xsi:schemaLocation="<http://www.springframework.org/schema/beans> http://
  www.springframework.org/schema/beans/spring-beans.xsd">
      <bean id="memberService" class="hello.core.member.MemberServiceImpl">
          <constructor-arg name="memberRepository" ref="memberRepository" />
      </bean>
</beans>
```

---

[1] 김영한, 인프런, 스프링 핵심원리
