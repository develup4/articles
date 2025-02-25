# Bean 컨테이너

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

여기서 `ApplicationContext`를 바로 `스프링 컨테이너`(혹은 `Bean 컨테이너`)라고 부른다. 이 스프링 컨테이너는 **Bean들을 저장하고 필요할 때 이를 주입**해준다. 구체적인 형태는 Bean의 이름 문자열과 객체의 레퍼런스를 테이블 형태로 관리하는 형태이다.

저장될 Bean은 아래와 같이 `@Bean` 애노테이션을 통해 정의한다.

```java
@Bean
public OrderService orderService() {
       return new OrderServiceImpl(memberRepository(), discountPolicy());
}
```

- Bean 이름은 메서드의 이름을 기본으로 사용한다.
- 이름을 직접 부여할 수 도 있다.

```java
@Bean(name="memberService2")
public OrderService orderService()
```

주의할 점은 Bean의 이름은 유니크해야한다. 설정에 따라 Overwrite되거나 오류가 발생할 수 있다.

# Bean 관련 API

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);

// 1.
String[] beanDefinitionNames = ac.getBeanDefinitionNames();
for (String beanDefinitionName : beanDefinitionNames) {
    BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
}

// 2.
if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
    // 3.
    MemberService memberService = ac.getBean("memberService",
MemberService.class);
}
```

- `ac.getBeanDefinitionNames()` : 스프링에 등록된 모든 Bean 이름을 조회한다.
- `ac.getRole()` : 스프링에서 사용하는 Bean과 유저가 만든 Bean을 구분할 수 있다.
- `ac.getBean()` : 이름으로 Bean 객체를 조회한다.

```java
// 빈 이름으로 조회
MemberService memberService = ac.getBean("memberService",
MemberService.class);

// 이름 없이 타입만으로 조회")
MemberService memberService = ac.getBean(MemberService.class);

// 구체 타입으로 조회
MemberServiceImpl memberService = ac.getBean("memberService",
MemberServiceImpl.class);

// 조회 대상이 없을 때 예외발생
Assertions.assertThrows(NoSuchBeanDefinitionException.class, () ->ac.getBean("xxxxx", MemberService.class));

// 특정 타입을 모두 조회하기
Map<String, MemberRepository> beansOfType =
ac.getBeansOfType(MemberRepository.class);
```

스프링 Bean 조회는 부모 타입으로 조회하면 자식 타입도 함께 조회한다. 그래서 모든 자바 객체의 최고 부모인 Object 타입으로 조회하면 모든 스프링 Bean을 조회한다.

# 스프링 컨테이너 상속구조

```java
AnnotationConfigApplicationContext extends ApplicationContext extends BeanFactory
```

이 모두를 일반적으로 스프링 컨테이너라고 한다. `ApplicationContext`부터 의미있는 기능들이 많이 추가되므로 `BeanFactory`를 직접적으로 사용하는 일은 거의 없다.

- 메시지소스를 활용한 국제화 기능
- 환경변수
- 로컬, 개발, 운영등을 구분해서 처리 애플리케이션 이벤트
- 편리한 리소스 조회

`AnnotationConfigApplicationContext`, `GenericXmlApplicationContext`는 각각 **Java class, XML에서 Bean을 생성**해낸다. 그럴 수 있는 이유는 `BeanDefinition`이라는 인터페이스를 스프링의 코어가 활용하기 때문이다.

# Bean Definition

실제로 코드 레벨로 활용하는 일은 거의 없지만 알아두자.

- `BeanClassName:` 생성할 Bean의 클래스 명
- `factoryBeanName:` 팩토리 역할의Bean을 사용할 경우 이름
- `factoryMethodName:` Bean을 생성할 팩토리 메서드 지정
- `Scope:` Singleton, Prototype, web, ...
- `lazyInit:` 스프링 컨테이너를 생성할 때 Bean을 생성하는 것이 아니라, 실제 Bean을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부
- `InitMethodName:` Bean을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
- `DestroyMethodName:` Bean의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
- `Constructor arguments, Properties:` 의존관계 주입에서 사용한다.

# Bean과 싱글톤

웹 서비스를 구현할 때 Request마다 Bean을 생성한다면 어떻게 될까? 아마 잘나가는 서비스라면 배가 터져 죽을 것이다. (그렇지 않다면 더 열심히 유저를 모으자 ㅠㅠ) 그래서 **스프링은 Bean을 기본적으로는 싱글톤**으로 만들어서 **여러 코드에서 공유**하도록 구현하였다. (나중에 설명하겠지만 매번 새로운 객체를 생성하는 Prototype 방식도 있다)

중요한 점은 **싱글톤 객체는 상태를 유지(Stateful)하게 설계하면 안된다. 무상태(Stateless)로 설계해야 한다.**

- 특정 클라이언트에 의존적인 필드가 있으면 안된다.
- 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
- 가급적 읽기만 가능해야 한다.
- 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야한다.

좀 더 원리적인 부분을 알아보자.

```java
@Bean
public MemberService memberService() {
	return new MemberServiceImpl(**memberRepository()**);
}

@Bean
public OrderService orderService() {
	return new OrderServiceImpl(**memberRepository()**, discountPolicy());
}

@Bean
public MemberRepository memberRepository() {			  			System.out.println("call AppConfig.memberRepository");
	return new MemoryMemberRepository();
}
```

분명 `memberRepository()`가 두 번 호출되기 때문에 `new MemoryMemberRespository()`가 두 번 호출되어 인스턴스는 2개가 되어야 할 것 같다. 하지만 실제 출력을 살펴보면 println은 한번만 호출되는 것을 확인할 수 있다.

암만 스프링이라도 할지라도 JAVA 문법을 바꿀 수는 없는것 아닌가???
그런데 그게 실제로 일어났습니다.

스프링은 `CGLIB`라는 라이브러리를 통해 빌드 결과물인 Class파일을 수정해버리는 치트키를 사용해버렸다. JAVA 문법의 한계를 깨뜨려버렸다. (사실 깔끔한 방식인지는 모르겠지만, 사용자에게 깔끔한 환경을 제공하기 위해 백조처럼 우아하지 못한 발길질을 하는 것은 프레임워크의 덕목일지도 모른다.)

바이트 코드에 의해 변환된 코드는 아래와 같을 것으로 추정된다. 우리가 만든 AppConfig는 AppConfig@CGLIB라는 이름으로 변해있고 이는 AppConfig를 상속한다.

```java
@Configuration	// 이 애노테이션이 없으면 CGLIB의 마법은 일어나지 않는다
public class AppConfig {

...

@Bean
public MemberRepository memberRepository() {
	if (memoryMemberRepository가 이미 등록되어 있으면) {
		return 스프링 컨테이너에서 찾아서 반환;
	} else { //스프링 컨테이너에 없으면 기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록 return 반환
	}
}
```

---

[1] 김영한, 인프런, 스프링 핵심원리
