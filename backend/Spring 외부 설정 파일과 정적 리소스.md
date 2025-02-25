## 외부 설정파일

### application.properties

- `application.yml`로 사용하기도 한다.
- Key-Value 형태로 설정을 저장한다.

```java
human.name = develup4
human.age = 35
```

- 스프링 부트가 Application을 실행한 때 자동으로 load한다.

### 적용 우선순위

1. {프로젝트 Root}/config/application.yml
2. {프로젝트 Root}/application.yml
3. classpath:/config/application.yml
4. classpath:/

### 참조 방법

- `@Value` 애노테이션을 통해 주입받는다.

```java
@Component
public class SimpleProperties implements ApplicationRunner {
    @Value("${human.name}")
    private String name;
}
```

### Type-safe하게 이용할 수 있는 방법

- property에 대응되는 Bean 클래스를 정의할 수 있다.
- `spring-boot-starter-validation`을 이용해 입력값 검증(Not Null, Not Empty 등)도 가능하다.

```java
@Component
@ConfigurationProperties("human")
@Validated
public class HumanProperties {
	@NotEmpty
	private String name;	// 타입이 지정되어 세이프하다.
	private int age;

	// getter나 어떠한 로직이 들어갈 수도 있다.
	public getAge() {
		return age;
	}
}

@Component
public class PropertyTest {
	@Autowired
	HumanProperties humanProperties;	// 주입받아 사용한다.
}
```

### Profile

- 환경에 따라 다른 설정 값을 사용하고 싶을 때 사용한다.
- 예를 들어 Production 환경, Test 환경같이 나눌 수 있다.

```java
@Profile("production")
@Configuration
public class BaseConfiguration {
	@Bean
	public String hello() {
		return "hello";
	}
}

@Profile("test")
@Configuration
public class TestConfiguration {
	@Bean
	public String hello() {
		return "hello test";
	}
}
```

- 사용할 Profile은 `application.properties` 파일에서 선택할 수 있다.

```
spring.profiles.active = production
```

### Profile마다 별도의 property 파일을 설정하는 방법

- `application-{Profile 이름}.properties`로 파일을 생성하여 별도의 환경을 설정한다.
- 예를 들어 `application.properties`에서 공통의 설정을 정의하고 `application-prod.properties`에서 라이브 환경의 세팅을, `application-test.properties`에서 테스트 환경을 설정할 수 있는 것이다.

## 정적 리소스

- 아래 경로의 리소스 들은 웹 서버에서 바로 접근할 수 있다. `classpath:`는 `src/main/`을 의미한다.

1. `classpath:/static`
2. `classpath:/public`
3. `classpath:/resources/`
4. `classpath:/META-INF/resources`

- `application.properties`에 경로를 직접 지정할 수도 있다.
  - spring.mvc.static-path-pattern=/static/\*\*
  - 다만 위 1, 2, 3, 4의 기본 경로는 무시된다.
- 기본 경로에 추가적으로 경로를 추가하고 싶다면 아래처럼 Configuration을 작성한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
	@Override
	public void addResourceHandler(ResourceHandlerRegistry registry) {
		registry.addResourceHandler("/path/**")
			.addResourceLocation("classpath:/m/")
			.setCachePeriod(20)
	}
}
```

### index 페이지와 파비콘

- 위 정적 리소스 경로에 `index.html`이 있다면 welcome 페이지로 이용한다.
- 위 정적 리소스 경로에 `favicon.ico`이 있다면 파비콘으로 이용한다.

### Error 페이지

- `resources/static/error`에 **{Status Error 코드}.html**을 작성하면 해당 에러 발생시 페이지를 표시해준다.
- 예를 들어 `404.html`, `5xx.html`과 같이 작성할 수 있다.

### Web Jar

- 스프링 부트 내의 정적 페이지에서 사용할 javascript나 css 등의 리소스들을 jar 형태로 포함할 수 있다.
- Maven Repository에서 검색해보면 `jQuery`와 같은 프론트엔드 라이브러리 들도 의존성을 추가할 수 있다.
- `<script src=“/webjars/jquery/3.3.1/dist/jquery.min.js”>`처럼 사용할 수 있다.

---

[1] 백기선, 인프런, 스프링 부트 개념과 활용
