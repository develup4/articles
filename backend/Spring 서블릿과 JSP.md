# JSP와 서블릿

옛날에 사서 한번도 보지 않은 책 중에 `JSP / 서블릿`이라는 책이 있었다. 막연하게 자바를 통해 구현하는 웹이라는것 이외에는 JSP가 뭔지 서블릿이 뭔지 전혀 감이 없었다. JSP가 템플릿 엔진이라는 사실도 이번에서야 알게 되었다. 시대는 지나 쌩 서블릿, 그리고 JSP를 템플릿 엔진으로 사용하는 일은 거의 없겠지만 **스프링의 근간**이 되므로 조금은 정리해보도록 하자. **스프링부트는 서블릿을 포함**하고 있으므로(Wrapper이니 당연하게도), 예제 코드는 모두 스프링 내에서 작성한 버전으로 한다.

```java
@ServletComponentScan // 서블릿 자동 등록
@SpringBootApplication
public class ServletApplication {
  public static void main(String[] args) {
    SpringApplication.run(ServletApplication.class, args);
  }
}
```

위와 같은 애노테이션을 통해 서블릿을 자동등록하도록 한 뒤, 첫 서블릿을 생성하여 등록하도록 하자.

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
  @Override
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    System.out.println("HelloServlet.service");
    System.out.println("request = " + request);
    System.out.println("response = " + response);

		// http://localhost:8080/request-param?username=hello&age=20
    String username = request.getParameter("username");  // 쿼리 파라메터
    System.out.println("username = " + username);
    response.setContentType("text/plain");
    response.setCharacterEncoding("utf-8");
    response.getWriter().write("hello " + username)
  }
}
```

다짜고짜 코드부터 살펴보았는데 간단히 살펴보면 아직 설명하지 않은 `서블릿`이 무엇인지를 알 수 있을 것이다. 간단하게 설명하자면 **웹 요청을 처리**하기 위한 자바 클래스인 것이다. `@WebServlet` 서블릿 애노테이션을 통해 서블릿 클래스를 등록할 수 있으며, name 및 urlPatterns을 정의할 수 있다.

위 예제의 서블릿은 `http://localhost:8080/hello`에 대한 웹 요청을 처리할 수 있는 것이다. 서블릿은 `서블릿 컨테이너`에 보관되며 매치되는 url로 요청이 오면 아래와 같은 메서드가 호출된다. 개발자는 이 메서드를 오버라이딩해서 해당 요청에 대한 동작을 정의하는 것이다.

```java
protected void service(HttpServletRequest request, HttpServletResponse response)
```

현재의 스프링부트나 다른 웹 프레임워크를 생각해보면 아쉬울 수도 있지만, 이미 서블릿만으로도 http request에 대한 **추상화**가 많이 이루어져 있다. `HttpServletRequest`라는 클래스가 이미 **Request를 추상화**했다는 의미가 되지 않겠는가? 간단히 예를 들면 HTTP Request header의 Content-Length만 해도 계산없이 자동으로 서블릿에 제공이 된다.

그렇다면 서블릿들이 저장되는 **서블릿 컨테이너는 어디에 있는걸까?** 정답은 `톰켓(Tomcat) 서버`이다. 스프링부트를 통해 서블릿을 작성하면 내장된 톰켓 서버를 통해 서블릿이 실행되는 것이다. 확실히 **WAS답게 단순한 웹 서버와는 달리 로직을 보관**한다.

# HttpServletRequest

이제 좀 더 디테일하게 서블릿의 요소를 살펴보자. 먼저 `HttpServletRequest`이다. 서블릿은 개발자 대신 HTTP Request message를 파싱해서 위 클래스의 객체를 생성하고 service 메서드의 인자로 넣어준다. 구체적으로 제공해줄 수 있는 데이터는 아래와 같다.

- HTTP 메소드(GET, POST, DELETE, PUT, ...)
- URL
- Query string
- 스키마
- 프로토콜 헤더
- Form 파라미터 형식 조회
- message body 데이터 직접 조회

이것만으로는 재미가 없다. 파싱정도는 시시하지 않겠는가? 부가적인 기능을 아래와 같이 제공한다.

- 임시 저장소 기능: \*\*\*\*해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소를 제공한다. 나중에도 핵심적으로 쓰게되는 기능이니 기억해두자.

```java
request.setAttribute(name, value)
request.getAttribute(name)
```

- 세션 관리 기능

```java
request.getSession(create: true)
```

이렇게 HttpRequest에 대한 대부분의 정보를 API를 통해 얻어올 수 있다. 스프링에서도 필요시 사용할 수 있으므로 필요할때 레퍼런스를 찾아보도록 하자.

## Jackson 라이브러리

스프링에서는 필요없지만 가끔 보이는 라이브러리이다. 마치 node.js와 javascript간에 그러하듯이 **JSON 형식을 받아서 바로 객체로 만들어주는** 방법이다. 스프링에서는 기냥 된다.

```
POST [http://localhost:8080/request-body-json](http://localhost:8080/request-body-json)
content-type: application/json
message body:
{"username": "hello", "age": 20}

>> 결과: messageBody = {"username": "hello", "age": 20}
```

```java
@Getter @Setter
public class HelloData {
    private String username;
    private int age;
}

// 이 녀석이 json을 object로 매핑해준다.
import com.fasterxml.jackson.databind.ObjectMapper;

@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {
  private ObjectMapper objectMapper = new ObjectMapper();

	@Override
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    ServletInputStream inputStream = request.getInputStream();
		String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
    HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
    System.out.println("helloData.username = " + helloData.getUsername());
    System.out.println("helloData.age = " + helloData.getAge());
    response.getWriter().write("ok");
  }
}
```

# HttpServletResponse

- HttpServletResponse 역할
- HTTP 응답 메시지 생성 HTTP 응답코드 지정 헤더 생성
- 바디 생성
- 편의 기능 제공
- Content-Type, 쿠키, Redirect

Request와 마찬가지로 Response도 편의기능을 많이 제공한다. 그리고 역시나 스프링에서는 더 편하게 제공한다. Response의 헤더는 저렇게 작성한다 치고, 이제 Response 메시지의 body를 작성하는 다양한 방법에 대해 알아보자. 요즘은 Rest API에 프론트엔드는 다른 프레임워크로 구성하는게 일반적이지만 예전엔 `서버사이드 렌더링`이 일반적이었고, 따라서 웹 요청에 대해 HTML을 반환할 필요가 있는 것이다.

아이고야...그런데 이것을 보라. HTML을 직접 생성해서 Response로 전달하고 있다.

```java
@WebServlet(name = "memberFormServlet", urlPatterns = "/servlet/members/newfrm")
public class MemberFormServlet extends HttpServlet {
  private MemberRepository memberRepository = MemberRepository.getInstance();

	@Override
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    response.setContentType("text/html");
    response.setCharacterEncoding("utf-8");

		Cookie cookie = new Cookie("myCookie", "good");
		cookie.setMaxAge(600); //600초
    response.addCookie(cookie);

		PrintWriter w = response.getWriter();
    w.write("<!DOCTYPE html>\\n" +
              "<html>\\n" +
              "<head>\\n" +
              "    <meta charset=\\"UTF-8\\">\\n" +
							"    <title>Title</title>\\n" +
							"</head>\\n" +
							"<body>\\n" +
							"<form action=\\"/servlet/members/save\\" method=\\"post\\">\\n" +
							"    username: <input type=\\"text\\" name=\\"username\\" />\\n" +
							"    age:      <input type=\\"text\\" name=\\"age\\" />\\n" +
							" <button type=\\"submit\\">전송</button>\\n" + "</form>\\n" +
							"</body>\\n" +
							"</html>\\n");
	}
}
```

자바 코드로 HTML을 만들어 내는 것 보다 차라리 HTML 문서에 동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면 더 편리할 것이다. 이것이 바로 `템플릿 엔진`이 나온 이유이다. 템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있다. 템플릿 엔진에는 `JSP`, `Thymeleaf`, `Freemarker`, `Velocity` 등이 있다.

# JSP

뭔지도 몰랐던 JSP. 알고보니 **템플릿 엔진**이었다. 깨닫고 나니 아무도 이제는 쓰지 않는다. 그래도 알아보자. 그런데 코드에서 보듯이 이것은 매우 복잡하고 비효율적이다. 자바 코드로 HTML을 만들어 내는 것 보다 차라리 **HTML 문서에 동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면 더 편리**할 것이다. 이것이 바로 템플릿 엔진이 나온 이유이다. 템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있다.

```html
<%@ page import="hello.servlet.domain.member.MemberRepository" %> <%@ page
import="hello.servlet.domain.member.Member" %> <%@ page
contentType="text/html;charset=UTF-8" language="java" %> **<% // request,
response 사용 가능 MemberRepository memberRepository =
MemberRepository.getInstance(); System.out.println("save.jsp"); String username
= request.getParameter("username"); int age =
Integer.parseInt(request.getParameter("age")); Member member = new
Member(username, age); System.out.println("member = " + member);
memberRepository.save(member); %>**
<html>
  <head>
    <meta charset="UTF-8" />
  </head>
  <body>
    성공
    <ul>
      <li>id=<%=member.getId()%></li>
      <li>username=<%=member.getUsername()%></li>
      <li>age=<%=member.getAge()%></li>
    </ul>
    <a href="/index.html">메인</a>
  </body>
</html>
```

보면 HTML에 자바코드가 기냥 들어가 있다. 회원 저장 JSP를 보면, 회원 저장 서블릿 코드와 같다. 다른 점이 있다면, HTML을 중심으로 하고, 자바 코드를 부분부분 입력해주었다. **<% ~ %>** 를 사용해서 HTML 중간에 자바 코드를 출력하고 있다.

`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`
첫 줄은 JSP 문서라는 뜻이다. JSP 문서는 이렇게 시작해야 한다. 회원 등록 폼 JSP를 보면 첫 줄을 제외하고는 완전히 HTML와 똑같다. JSP는 서버 내부에서 서블릿으로 변환되는데, 우리가 만들었던 `MemberFormServlet`과 거의 비슷한 모습으로 변환된다.

그리고 JSP의 실행은 [http://localhost:8080/jsp/members/new-form.jsp](http://localhost:8080/jsp/members/new-form.jsp)과 같이 접속해서 할 수 있다..jsp 까지 함께 적어주어야 한다는 것을 주의하자. 이것은 무엇을 의미할까? 단순 HTML을 반환하는 서블릿이라면 서블릿을 작성할 필요없이 JSP만 작성하면 그 url로 접속시 동일한 처리가 가능하다는 이야기이다.

> 이것은 자바진영의 원대한 꿈이었다. 자바는 자바 가상머신 위에서 동작한다. 그래서 웹 브라우저에 가상머신을 탑재해서 서버와 클라이언트간에 자바 바이트 코드를 전송해서 웹 어플리케이션을 구현하고 싶은 원대한 목표가 있었는데, 결국 결론은 자바스크립트가 동일한 방식으로 웹 세계를 지배해버리고 말았다.

# 서블릿과 JSP의 한계

서블릿으로 개발할 때는 뷰(View)화면을 위한 HTML을 만드는 작업이 자바 코드에 섞여서 지저분하고 복잡했다. JSP를 사용한 덕분에 뷰를 생성하는 HTML 작업을 깔끔하게 가져가고, 중간중간 동적으로 변경이 필요한 부분에만 자바 코드를 적용했다. 그런데 이렇게 해도 해결되지 않는 몇가지 고민이 남는다.
회원 저장 JSP를 보자. 코드의 상위 절반은 회원을 저장하기 위한 비즈니스 로직이고, 나머지 하위 절반만 결과를 HTML로 보여주기 위한 뷰 영역이다. 회원 목록의 경우에도 마찬가지다. 코드를 잘 보면, JAVA 코드, 데이터를 조회하는 리포지토리 등등 다양한 코드가 모두 JSP에 노출되어 있다. **JSP가 너무 많은 역할을 한다.** 이렇게 작은 프로젝트도 벌써 머리가 아파오는데, 수백 수천줄이 넘어가는 JSP를 떠올려보면 정말 지옥과 같을 것이다.

# MVC 패턴의 등장

비즈니스 로직은 서블릿 처럼 다른곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면(View)을 그리는 일에 집중하도록 하자. 과거 개발자들도 모두 비슷한 고민이 있었고 그에 따라 자연스레 `MVC 패턴`이 등장했다. 우리도 직접 MVC 패턴을 적용해서 프로젝트를 리팩터링 해보자.

MVC 패턴은 지금까지 학습한 것 처럼 하나의 서블릿이나, JSP로 처리하던 것을 `컨트롤러(Controller)`와 `뷰(View)`라는 영역으로 서로 역할을 나눈 것을 말한다. 웹 애플리케이션은 보통 이 MVC 패턴을 사용한다.

- 컨트롤러: HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
- 모델: 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
- 뷰: 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 여기서는 HTML을 생성하는 부분을 말한다.

> 컨트롤러에 비즈니스 로직을 둘 수도 있지만, 이렇게 되면 컨트롤러가 너무 많은 역할을 담당한다. 그래서
> 일반적으로 비즈니스 로직은 `서비스(Service)`라는 계층을 별도로 만들어서 처리한다. 그리고 컨트롤러는 비즈니스 로직이 있는 서비스를 호출하는 담당한다. 참고로 비즈니스 로직을 변경하면 비즈니스 로직을 호출하는 컨트롤러의 코드도 변경될 수 있다. 앞에서는 이해를 돕기 위해 비즈니스 로직을 호출한다는 표현 보다는, 비즈니스 로직이라 설명했다.

## 적용

```java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSaveServlet extends HttpServlet {
  private MemberRepository memberRepository = MemberRepository.getInstance();

	@Override
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      String username = request.getParameter("username");
      int age = Integer.parseInt(request.getParameter("age"));

			Member member = new Member(username, age);
      System.out.println("member = " + member);
      memberRepository.save(member);

			// Model에 데이터를 보관한다.
			request.setAttribute("member", member);

      String viewPath = "/WEB-INF/views/save-result.jsp";
      RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
      dispatcher.forward(request, response);
	}
}
```

- `dispatcher.forward()`**: 다른 서블릿이나 JSP로 이동할 수 있는 기능**이다. 서버 내부에서 다시 호출이 발생한다.
- /WEB-INF: 이 경로안에 JSP가 있으면 외부에서 직접 JSP를 호출할 수 없다. 우리가 기대하는 것은 항상 컨트롤러를 통해서 JSP를 호출하는 것이다.
- Redirect vs Forward: 리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다. 반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.

View에 해당하는 코드를 살펴보자.

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <meta charset="UTF-8">
  </head>
	<body> 성공
	  <ul>
      <li>id=${member.id}</li>
      <li>username=${member.username}</li>
      <li>age=${member.age}</li>
		</ul>
		<a href="/index.html">메인</a>
  </body>
</html>
```

`<%= request.getAttribute("member")%>`로 모델에 저장한 member 객체를 꺼낼 수 있지만, 너무 복잡해진다. JSP는 `${}` 문법을 제공하는데, 이 문법을 사용하면 Request의 Attribute에 담긴 데이터를 편리하게 조회할 수 있다.

---

[1] 김영한, 인프런, 스프링 MVC
