## Filter

- 스프링의 AOP처럼 서블릿의 공통 관심사에 대한 처리를 담당하는 요소가 Filter이다.

### Filter의 흐름(Filter Chain)

```
HTTP REQUEST -> WAS -> FILTER1 —> FILTER2 -> … -> SERVLET -> CONTROLLER
```

### Filter 구현

```java
public class LogFilter implements Filter {

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		log.info(“log filter init”);
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		HttpServletRequest httpRequest = (HttpServletRequest)request;
		String requestUri = httpRequest.getRequestURI();
		String uuid = UUID.randomUUID().toString();

		try {
			log.info(“REQUEST {} {} ”, uuid, requestUri);

			// 매우 중요! 이 코드가 있어야 다음 체인으로 넘어간다.
			chain.doFilter(request, response);
		} catch(Exception e) {
			throw e;
		} finally {
			log.info(“RESPONSE {} {}”, uuid, requestUri);
		}
	}

	@Override
	public default void destroy() {
		log.info(“log filter destroy”);
	}
}
```

### Filter의 등록

- Configuration에서 `FilterRegistrationBean`을 이용하여 등록한다.
- 우선순위, 적용할 url 패턴 등을 등록할 수 있다.

```java
@Configuration
public class WebConfig {

	@Bean
	public FilterRegistrationBean logFilter() {
		FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();

		filterRegistrationBean.setFilter(new LogFilter());
		filterRegistrationBean.setOrder(1);
		filterRegistrationBean.addUrlPattern(“/login”);
		return filterRegistrationBean;
	}
}
```

## Interceptor

- Filter는 서블릿이 제공하는 기술이며, Interceptor는 스프링이 제공하는 기술이다. 더 뛰어난 부분이 많다.

### Interceptor의 흐름

- 스프링이 제공하는 기능이므로 Dispatcher servlet 이후에 적용된다.

```
HTTP REQUEST -> WAS -> FILTER1 —> FILTER2 -> … -> SERVLET -> INTERCEPTOR1 -> INTERCEPTOR2 -> … -> CONTROLLER
```

### Interceptor 구현

```java
public class LogInterceptor implements HandlerInterceptor {

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		// 인터셉터가 처리하는 부분
		return true;	// false를 리턴하면 더 이상 진행하지 않는다.
	}

	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
		// 예외 발생시 호출되지 않는다.
	}

	@Override
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		// 예외가 발생해도 호출된다.
	}
}
```

### Interceptor 등록

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new LogInterceptor())
			.order(1)
			.addPathPatterns(“/login”)
			.excludePathPatterns(“/css”);
	}
}
```

---

[1] 백기선, 인프런, 스프링 부트 개념과 활용
