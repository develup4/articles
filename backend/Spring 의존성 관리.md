## 스프링 부트의 기본설정

- 가장 기본적으로 스프링 부트를 실행시키기 위해서는 아래 내용이 필요하다.

```xml
<!--
Maven 설정은 Hierarchy를 가질 수 있다.
부모 프로젝트를 spring-boot-starter-parent로 설정하여 "의존성 관리"를 상속받는다.
-->
<!-- Inherit defaults from Spring Boot -->
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.2.4.RELEASE</version>
</parent>

<!--
아래 dependancy에서 버전을 명기하지 않아도 되는 이유는,
위 부모 프로젝트에서 호환되는 버전정보가 이미 명시되어있기 때문이다.
물론 여기서 새로 명시해서 override할 수도 있다.
-->
<!-- Add typical dependencies for a web application -->
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>

<!-- Package as an executable jar -->
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

### Parent POM(spring-boot-starter-parent)의 의존성을 사용하고 싶지 않다면?

- `<dependencyManagement>`를 통해 `Scoped Dependency`를 사용할 수 있다. 다만 spring-boot-starter-parent는 스프링 부트에 최적화된 각정 설정(Java 버전, 인코딩 설정 등)이 정의되어 있어 가급적 parent를 사용하는 것이 좋다.

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <!-- Import dependency management from Spring Boot -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.3.1.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### 버전만 변경해보고 싶다면?

- `<dependancies>`에서 버전을 명기하는 것과는 다른 이야기이다. Parent에서의 의존성 관리 버전을 변경해보려면 아래와 같이 `<properties>`내에서 지정할 수 있다.

```xml
<properties>
	<spring.version>5.0.6.RELEASE</spring.version>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
	<java.version>11</java.version>
</properties>
```

## 의존성을 추가하는 방법

- `https://mvnrepository.com/`에서 검색할 수 있다.
- `npm`과 `package.json`만큼 편하지는 않은 것 같다.

![Untitled](SpringDI/Untitled.png)

## 스프링 부트 프로젝트를 Jar로 빌드했을 때의 의존성

- 프로젝트에 추가된 의존성에 해당하는 Jar 파일들이 **모두 포함**된채로 Jar로 빌드된다.
- Jar 파일을 압축해제해보면 `target/app/BOOT_INF/lib`에 라이브러리들이 Jar 형태로 포함되어 있다.
- 이러한 방식을 `self-contained application`이라 한다.

### Jar로 된 라이브러리를 사용하는 방법

- Java에는 기본적으로 **jar 내의 파일(nested jar)들을 읽을 수 있는 표준적인 방법이 없다.** 하지만 라이브러리 사용을 위해서는 라이브러리의 수많은 클래스 들을 활용해야 한다.
- 예전에는 `uber jar`를 사용했으나 어떤 라이브러리를 사용하는지 알기 어렵고, 이름 충돌에 취약했다.
- 그래서 스프링 부트가 Jar를 읽고 사용할 수 있는 `loader`와 `launcher`를 빌드시 제공해준다.

### 실행 가능한 Jar의 구조

```
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF		# 실행 가능한 Jar의 메타 정보를 담고 있다. Main Class와 Start Class가 정의되어 있다.
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-Launcher		# Launcher이다. 실행 가능한 Jar의 실행지점(Main Class)로 사용된다.
 |           +-jar
 |              +-JarFile	# Loader이다. nested jar들을 읽을 수 있는 파일
 +-BOOT-INF
    +-classes
    |  +-mycompany
    |     +-project
    |        +-YourClasses.class
    +-lib			# 라이브러리(의존성)들이 Jar 형태로 포함되어 있다.
       +-dependency1.jar
       +-dependency2.jar

```

### 실행 가능한 Jar의 의미

- 실행 가능한 Jar는 스프링 부트를 **독립적으로 실행 가능한 어플리케이션**으로 만들어준다.
- 이는 스프링 부트의 주요 목표 중 하나이다.

---

[1] 백기선, 인프런, 스프링 부트 개념과 활용
