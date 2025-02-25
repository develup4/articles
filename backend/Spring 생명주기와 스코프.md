# Spring 생명주기

유니티 3D 게임엔진의 GameObject라던지 React의 Component를 보면 모두 생명주기 함수가 있다. 객체가 생성될 때, 소멸될 때, 업데이트 될 때의 동작들을 정의하는 것이다. 이는 Effective C++에서 설명하는 RAII(Resource Acquisition Is Initialization) 개념과도 맞닿아 있다. 어느것이 오리지널인지는 몰라도 스프링 Bean 역시 이러한 생명주기 함수를 가지고 있다. 그것에 대한 설명을 할 것이다.

먼저 클래스에는 생성자와 소멸자라는 훌륭한 생명주기 도구가 있는데 왜 다른 콜백함수가 필요한 것일까? 답은 스프링의 경우 Bean의 생성과 의존성 주입의 시점이 다를 수 있기 때문이다. (물론 가장 많이 사용하는 생성자 주입의 경우는 두 시점이 동일하여 걱정할 필요는 없다)

> 위에서 RAII에 대해 언급하였지만 스프링에서는 조금 다른 철학을 가지고 있는듯하다. 객체의 생성과 초기화를 분리하여 역할을 나누는 것이다. 단일책임원칙을 생각하면 옳은 말일 수 있고, RAII 개념이 나온 C++에서는 가비지 컬렉터가 없어 자원의 해제가 너무나도 중요하다는 배경을 생각해봐야 한다.

그럼 본격적으로 생명주기에 관련된 콜백에 대해 알아보자.

- 상속을 이용하는 방법

```java
public class NetworkClient implements InitializingBean, DisposableBean {
   @Override
   public void afterPropertiesSet() throws Exception {
      connect();
   }
   @Override
   public void destroy() throws Exception {
      disConnect();
   }
}
```

위 코드에서 보듯 `InitializingBean`, `DisposableBean` 인터페이스를 상속하고 그에 따른 `afterPropertiesSet()`, `destroy()`를 구현하는 방식이다. 하지만 거의 사용되지 않는 방법인데 이유는 저 인터페이스들은 스프링에서 제공하는 내용이라 유연성이 떨어지고 메소드명을 변경할 수 없기 때문이다.

- Bean 설정시 지정하는 방법

```java
@Configuration
static class LifeCycleConfig {
   @Bean(initMethod = "init", destroyMethod = "close")
   public NetworkClient networkClient() {

...
```

코드에서 보듯 Bean 애노테이션에서 직접 생명주기 함수명을 등록할 수 있다. 앞서 방식의 문제였던 메소드명 지정문제를 해결할 수 있다. 한가지 더 비밀이 있는데 이 기능에는 메소드명 추론 기능이 있어서 `destroyMethod`를 넣지 않아도 Close, Shutdown과 같은 의미의 메소드를 찾아서 자동으로 호출해준다. 편리할수도 있지만 역시나 명시적인 것이 좋다. 개인적으로는 쓰지 않을 것 같다.

- `@PostConstruct`, `@PreDestory` 애노테이션을 활용하는 방법

```java
@PostConstruct
public void init() {
   connect();
}

@PreDestroy
public void close() {
   disConnect();
}
```

딱 보면 알다시피 가장 권장되는 기법이다. 최신 스프링 트렌드에 맞게 애노테이션으로 간단하게 정의가 가능하고 메소드명 지정도 가능하며, 무엇보다 자바 표준으로 동작해서 스프링에 종속성이 없다.

# Bean 스코프

Bean은 기본적으로 **싱글톤으로 구현되어 스프링 컨테이너가 사라질 때까지**(애플리케이션이 종료될 때까지) 유지된다. 하지만 싱글톤 Bean만 있는 것은 아니며 다양한 스코프를 가진 Bean들이 존재한다. 이번에는 Bean 스코프에 대해 알아볼 것이다.

먼저 Bean 스코프의 종류에 대해 알아보자.

- `싱글톤`: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.
- `프로토타입`: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다.
- `웹 관련 스코프`
  - request: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프이다.
  - session: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프이다.
  - application: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프이다.
  - websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프이다.

스코프 지정은 간단하다. 역시나 애노테이션을 통해서 아래와 같이 지정할 수 있다.

```java
@Scope("prototype")
@Component
public class HelloBean {

...
```

그렇다면 좀 더 자세히 각각의 스코프에 대해 알아보자.

`프로토타입 스코프`는 간단히 말해 싱글톤이 아닌 Bean을 의미한다. 의존성이 주입될 때마다 **새로운 인스턴스**가 생겨나는 것이다. 스프링 컨테이너는 의존성 주입 및 초기화까지만을 담당하고 더 이상 이 Bean을 관리하지 않는다. 따라서 위 생명주기에서 설명했던 Destroy 메소드 등도 호출되지 않는다.

이런 경우를 조심하자. 싱글톤 Bean이 프로토타입 Bean을 주입받는 경우 싱글톤 Bean 입장에서는 프로토타입 Bean을 주입받아 레퍼런스를 가지고 있는 것이므로 그 메소드가 불릴때마다 새로 생성되지는 않는다는 점이다. 다시 말하자면 사용할때마다 생성되질 않는다!

그렇다면 이런 문제는 어떻게 해결해야할까? 초반에 설명했던 Provider를 이용해서 Bean을 그때그때 주입받는다면 해결은 가능할 것이다.

```java
public int logic() {
      PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);

...
```

하지만 더 깔끔한 방법은 없을까? 위 방법은 컨텍스트를 가져와야하고 그리 이쁘지 않은 API를 계속 호출해야 한다. (Dependancy Lookup을 이용하면 가능하다. 의존관계를 외부에서 주입(DI) 받는게 아니라 이렇게 직접 필요한 의존관계를 찾는 것을 `Dependency Lookup(DL)` 의존관계 조회라한다.)

스프링에서 지정한 Bean을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 `ObjectProvider`이다. 사용법은 아래와 같다.

```java
  @Autowired
  private ObjectProvider<PrototypeBean> prototypeBeanProvider;
  public int logic() {
      PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
      prototypeBean.addCount();
      int count = prototypeBean.getCount();
      return count;
}
```

위 기능은 스프링에 의존적이다.

자바 표준만으로 해결하는 방법 또한 존재한다.

```java
//implementation 'javax.inject:javax.inject:1' gradle 추가 필수 @Autowired
private Provider<PrototypeBean> provider;
  public int logic() {
      PrototypeBean prototypeBean = provider.get();
      prototypeBean.addCount();
      int count = prototypeBean.getCount();
      return count;
}
```

스프링에 의존하지는 않지만 `Gradle`에 설정추가가 필요하고, Bean을 찾는다는 자체가 스프링에 의존적이지 않나하는 생각이 든다. 아무래도 ObjectProvider의 사용이 가장 좋아보인다. 대부분은 자바 표준보다 스프링이 편리하게 기능을 구현하고 있다.

이번에는 프록시를 사용하는 방법을 알아보자. 클라이언트는 실제 Bean과 똑같은 Proxy Bean을 주입받고 다형성을 이용해서 실제 Bean이 호출되어 각각 실제로는 알맞은 Bean을 사용할 수 있는 방식이다. 내부 구조를 살펴보면 이번에도 CGLIB를 통해 Proxy 객체가 생성되는 방식이다. 위임 로직이 자동으로 구현되며 실제 Bean을 찾는 과정은 호출까지 지연된다.

```java
 @Component
  @Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
  public class MyLogger {

  }
```

저렇게 설정하면 MyLogger를 사용하는 부분에는 프록시 객체가 자동으로 주입되는 것이다.

---

[1] 김영한, 인프런, 스프링 핵심원리
