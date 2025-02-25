```java
void strategyV1() {
    ContextV2 context = new ContextV2();
		context.execute(new StrategyLogic1());
    context.execute(new StrategyLogic2());
}

void callbackV2() {
		TimeLogTemplate template = new TimeLogTemplate();
		template.execute(() -> log.info("비즈니스 로직1 실행"));
		template.execute(() -> log.info("비즈니스 로직2 실행"));
}
```

## **자바 언어에서 콜백**

자바 언어에서 실행 가능한 코드를 인수로 넘기려면 객체가 필요하다. Java8부터는 람다를 사용할 수 있다. Java8 이전에는 보통 하나의 메소드를 가진 인터페이스를 구현하고, 주로 익명 내부 클래스를 사용했다. 최근에는 주로 람다를 사용한다.

## **템플릿 콜백 패턴**

CallbackV2와 같은 방식의 전략 패턴을 템플릿 콜백 패턴이라 한다. 전략 패턴에서 Context 가 템플릿 역할을 하고, Strategy 부분이 콜백으로 넘어온다 생각하면 된다.

참고로 템플릿 콜백 패턴은 GOF 패턴은 아니고, 스프링 내부에서 이런 방식을 자주 사용하기 때문에, 스프링 안에서만 이렇게 부른다. 전략 패턴에서 템플릿과 콜백 부분이 강조된 패턴이라 생각하면 된다. 스프링에서는 JdbcTemplate , RestTemplate , TransactionTemplate , RedisTemplate 처럼 다양한 템플릿 콜백 패턴이 사용된다. 스프링에서 이름에 XXXTemplate 가 있다면 템플릿 콜백 패턴으로 만들어져 있다 생각하면 된다.

한마디로 정리하자면 **전략패턴인데 전략을 미리 매치해두는게 아니라 실행때마다 주입해서 사용하는 패턴**이라고 할 수 있겠다.
