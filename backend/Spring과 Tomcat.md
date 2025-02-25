## 내장 서블릿 컨테이너

- 스프링 부트는 `Tomcat 서버`가 기본적으로 내장되어 있다.
- Tomcat은 서블릿 컨테이너로 JSP, Servlet, Http 요청과 응답 등을 처리한다.

### 수동으로 Tomcat 실행해보기

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        Tomcat tomcat = new Tomcat();
        tomcat.setPort(8080);
        Context context = tomcat.addContext("/", "/");

        HttpServlet servlet = new HttpServlet() {
            @Override
            protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
                PrintWriter writer = resp.getWriter();
                writer.println("<h1>Hello world!</h1>");
            }
        };

        final String servletName = "myServlet";
        tomcat.addServlet("/", servletName, servlet);
        context.addServletMappingDecoded("/home", servletName);

        try {
            tomcat.start();
            tomcat.getServer().await();
        } catch (LifecycleException e) {
            e.printStackTrace();
        }
    }
}
```

### Auto Configuration을 이용한 스프링 부트의 Tomcat 실행

- 위와 같은 과정을 스프링 부트는 자동으로 진행해준다. 이를 위해 다른 포스트로도 정리한 `Auto Configuration`이 이용된다.
- `ServletWebServerFactoryAutoConfiguration`의 `TomcatServletWebServerFactory`에서 위의 수동 설정과 같은 일들이 일어나게 된다.

```java
public WebServer getWebServer(ServletContextInitializer... initializers) {
	Tomcat tomcat = new Tomcat();
	File baseDir = this.baseDirectory != null ?
		this.baseDirectory :
		this.createTempDir("tomcat");
	tomcat.setBaseDir(baseDir.getAbsolutePath());
	Connector connector = new Connector(this.protocol);
	tomcat.getService().addConnector(connector);
	this.customizeConnector(connector);
	tomcat.setConnector(connector);
	tomcat.getHost().setAutoDeploy(false);
	this.configureEngine(tomcat.getEngine());
}
```

## 내장 웹 서버 변경

- `jetty`나 `undertow`와 같은 다른 웹 서버를 사용할 수도 있다.
- 그러기 위해서는 우선 Tomcat에 대한 의존성부터 제외하여야 한다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <!-- Exclude the Tomcat dependency -->
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!-- Use Jetty instead -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
</dependencies>
```

### 포트 변경

- `resources/application.properties` 혹은 `application.yml`에서 설정을 변경할 수 있다.
- 0으로 설정시 **Random 포트**가 지정된다. 서버 인스턴스를 동적으로 여러 개 띄울때 유용하게 사용할 수 있다.

```
server.port=8443
```

- 스프링 부트의 기동시 로그를 통해서도 포트를 확인할 수 있지만, 코드를 통해서는 아래와 같이 확인이 가능하다. `ApplicationListener`의 `onApplicationEvent` 메소드는 Application이 생성되면 호출되는 Callback method이다.

```java
// ApplicationListener는 당연하지만 Bean이어야 호출된다.
@Component
public class PortListener implements ApplicationListener<ServletWebServerInitializedEvent> {
	@Override
	public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
		WebServer webServer = servletWebServerInitializedEvent.getApplicationContext().getWebServer();
		System.out.println("port: " + webServer.getPort());
	}
}
```

## HTTPS 서버 기동

- HTTPS는 알다시피 비대칭키를 이용한 보안 프로토콜이다. 먼저 키를 생성해야 한다.

```
keytool -genkey -alias { 별명 } -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore keystore.p12 -validity 4000
```

### 스프링 부트에 HTTPS 설정하기

- `application.properties` 혹은 `application.yml` 파일에 아래와 같이 설정한다.

```
server.ssl.key-store=keystore.p12
server.ssl.key-store-password={ 키 생성시 설정한 비밀번호 }
server.ssl.key-store-type=PKCS12
server.ssl.key-alias={ 키 생성시 설정한 별명 }
```

- 이렇게 설정시 HTTPS(443 포트)로 접속할 수 있다. 물론 공인인증서가 아니기 때문에 크롬 등의 브라우저에서 경고 메시지를 나타내긴 할 것이다.
- 그리고 기존의 HTTP(80 포트)는 접속이 되지 않는다. 필요하다면 아래와 같이 `Connector`를 추가하여 해결이 가능하다.

```java
@SpringBootApplication
public class Application {
	@Bean
	public ServletWebServerFactory servletContainer() {
		TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
		tomcat.addAdditionalTomcatConnectors(createStandardConnector());
		return tomcat;
	}

	private Connector createStandardConnector() {
		Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
		connector.setPort(8080);	// HTTPS용 포트와 다르게 설정해야 한다.
		return connector;
	}

	public static void main(String[] args) {
		// SpringApplication.run(Application.class, args);
		new SpringApplicationBuilder()		// 사족으로 빌더 패턴을 이용해 스프링 기동도 가능하다.
			.sources(Application.class)
			.bannerMode(Banner.Mode.OFF)	// 시작시 출력되는 배너도 끌 수 있다.
			.run(args);
	}
}
```

- `ServletWebServerFactoryAutoConfiguration`에서도 코드를 다시 살펴보면 Connector를 설정하는 것을 확인할 수 있다.

### HTTP/2 활성화

- `application.properties`에 `server.http2.enabled=true`로 설정하기만 하면 된다.
- 물론 전제로 HTTPS가 설정되어 있어야 한다.

---

[1] 백기선, 인프런, 스프링 부트 개념과 활용
