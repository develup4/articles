## Bean Validation

- 특정 필드에 대한 검증 로직은 대부분 빈 값인지 아닌지, 특정 크기를 넘는지 아닌지와 같이 매우 일반적인 로직이다.
- 애노테이션을 통해서 검증로직을 작성할 수 있도록 표준화한 것이 `Bean Validation`이다.

### 의존관계 추가

- `org.springframework.boot:spring-boot-starter-validation`

### 사용례

```java
public class Item {

	private Long id;

	@NotBlank
	private String itemName;

	@NotNull
	@Range(min = 1000, max = 1000000)
	private Integer price;

	@NotNull
	@Max(9999)
	private Integer quantity;
}
```

### ValidatorFactory

- 스프링에서는 직접 사용할 일이 없지만 참고로 알아둔다.

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();

Set<ConstraintViolation<Item>> violations = validator.validate(item)
for (ConstraintViolation<Item> violation : violations) {
	violation.getMessage();	// ex: violation.message=1000에서 1000000 사이여야 합니다
}
```

## 스프링과 Bean Validator

- `spring-boot-starter-validation` 라이브러리를 넣으면 스프링 부트가 자동으로 Bean Validator를 인지하고 `LocalValidatorFactoryBean`을 글로벌 Validator로 등록한다.
- `@Valid`나 `@Validated` 애노테이션을 통해 검증할 수 있다. 전자는 자바 표준, 후자는 스프링에서 제공하는 기능이다.

### 예제

```java
@PostMapping(“/add”)
public String addItem(@Validated @RequestBody Dto dto, BindingResult bindingResult) {
	if (bindingResult.hasError()) {
		log.info(bindingResult);
		return bindingResult.getAllErrors();
	}
}
```

- 사용법을 위한 인위적인 예제이다. 실제로는 Request가 Dto로 변환자체가 안되서 핸들러 안으로 들어가지 못하고 이미 실패하게 된다.

## API 예외 처리

- 예외를 던졌을때(throw) 애플리케이션 내부에서 아무도 그것을 처리하지 않는다면 어떻게 될까?
- 예외는 서블릿을 호출한 WAS까지 전달된다. 그리고 MVC의 경우 예외 페이지, API의 경우 500번대의 서버 에러를 발생시킨다.
- 예외의 기본처리는 `response.sendError(statusCode, reason)`을 통해 WAS에 전달되고, 다시 서블릿을 통해 에러페이지 혹은 에러처리 서블릿이 호출되는 구조이다.
- 디테일하게 예외를 처리하기 위해 스프링에서는 `ExceptionResolver`를 제공한다.

### ExceptionResolver

- 우선순위는 `ExceptionHandlerExceptionResolver`, `ResponseStatusExceptionResolver`, `DefaultHandlerExceptionResolver`순이다.

### DefaultHandlerExceptionResolver

- 스프링 내부의 기본 예외를 처리한다.

### ResponseStatusExceptionResolver

- 예외에 따라 HTTP Status 코드를 지정해주는 역할을 한다.
- `@ResponseStatus`가 달려있는 예외, `ResponseStatusException` 예외 등을 처리한다.
- 내부 구현적으로는 이것도 `sendError()`를 통해 다시 서블릿이 호출되는 구조이다.

```java
// @ResponseStatus
@ResponseStatus(code = HttpStatus.BAD_REQUEST, reason = “잘못된 요청 오류”)
public class BadRequestException extends RuntimeException {

}

// ResponseStatusException - 라이브러리 등 예외를 직접 수정하기 어려운 경우 사용
@GetMapping(“/responseExceptionTest”)
public String responseStatusExample() {
	throw new ResponseStatusException(HttpStatus.NOT_FOUND, “error”, new IllegalArgumentException());
}
```

### ExceptionHandlerExceptionResolver

- 가장 우선순위가 높고 가장 많이 사용되는 방식이다.
- 지정한 예외 또는 **그 예외의 자식** 클래스를 모두 잡을 수 있다.

```java
@RestController
public class ApiExceptionController {

	// 컨트롤러 내에 예외처리 클래스 정의
	@ResponseStatus(HttpStatus.BAD_REQUEST)
	@ExceptionHandler(IllegalArgumentException.class)		// 예외 클래스는 생략할 수 있다.
	public ErrorResult illegalExHandler(IllegalArgumentException e) {
		log.error(e);
		return new ErrorResult(“BAD”, e.getMessage());
	}

	// 동적으로 상태코드를 변경하고 싶을 때
	@ExceptionHandler(IllegalArgumentException.class)		// 예외 클래스는 생략할 수 있다.
	public ErrorResult illegalExHandler(IllegalArgumentException e) {
		ErrorResult errorResult = new ErrorResult(“BAD”, e.getMessage());
		return new ResponseEntity<>(errorResult, HttpStatus.BAD_REQUEST);
	}

	// API
	@GetMapping(“/responseExceptionTest”)
	public String responseStatusExample() {
		throw new IllegalArgumentException(“잘못된 입력”);
	}
}
```

- 위 코드는 API를 위한 코드와 예외 처리 코드가 섞여있어 응집도가 떨어진다. 이런 경우 `@ControllerAdvice`, `@RestControllerAdvice` 등을 사용해 분리할 수 있다.

```java
// 대상을 지정하지 않으면 모든 컨트롤러에 글로벌로 적용
@ControllerAdvice

// 특정 애노테이션을 가진 컨트롤러에만 적용하는 경우
@ControllerAdvice(annotations = RestController.class)

// 특정 패키지 안의 컨트롤러에만 적용하는 경우
@ControllerAdvice(“org.example.controllers”)

// 특정 컨트롤러에만 적용하는 경우
@ControllerAdvice(assignableTypes = {ControllerInterface.class, AbsController.class})
public class ApiExceptionAdvice {

	@ResponseStatus(HttpStatus.BAD_REQUEST)
	@ExceptionHandler(IllegalArgumentException.class)		// 예외 클래스는 생략할 수 있다.
	public ErrorResult illegalExHandler(IllegalArgumentException e) {
		log.error(e);
		return new ErrorResult(“BAD”, e.getMessage());
	}

	// 다른 예외 처리 코드
	// …
}
```

---

[1] 백기선, 인프런, 스프링 부트 개념과 활용
