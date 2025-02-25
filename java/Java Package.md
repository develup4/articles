## 패키지

- 클래스를 체계적으로 관리하기 위한 도구
- 클래스들을 구분짓는 디렉토리
- 패키지의 물리적인 형태는 **파일 시스템의 디렉토리**
- 단순히 파일 시스템의 디렉토리 기능만 하는 것이 아니라 클래스의 일부분이다.

### 목적

- C++의 네임스페이스처럼 클래스 이름 중복을 방지한다.

### 실행

```bash
$ java {패키지/패키지/클래스명}
```

- Java는 **최상위 디렉토리에서 실행하기**로 약속이 되어있다.
- 직접 `cd`로 디렉토리에 들어가지말고 위처럼 패키지명으로 구분해서 실행시키자.
- **패키지 명과 위치한 디렉토리의 이름은 같아야한다.** 따라서 위와 같은 동작이 가능하다.

### 규칙

- Package 선언은 최상단에 하나만 있어야 한다.
- java로 패키지명이 시작할 수 없다.
- 어길경우 `java.lang.SecurityException`이 발생한다.
- 패키지 이름은 **소문자로 시작하는게 국룰**이다.

### Package의 시작이름

- `java` : 자바기본패키지, java 벤더에서 개발
- `javax` : 자바확장패키지, java 벤더에서 개발
- `org` : 오픈소스 등 비영리단체의 패키지 (ex) [org.apache.xxx](http://org.apache.xxx/)
- `com` : 영리단체의 패키지

### import static

- Java 1.5버전부터 `import static`이라는것이 추가되었다.
- import static c.javapackage.sub.AnyClass.staticmethod;
- 위와 같이 선언하면 일반적인 import와 다르게 static method, field를 패키지 및 클래스명 없이 접근가능하다.

### FQCN

- 패키지와 클래스를 모두 기술하는 것을 `FQCN(Fully Qualified Class Name)`이라고 부른다.
- 이름이 동일한 라이브러리를 사용할때 어쩔 수 없이 한쪽은 FQCN을 사용해야한다.
- c.javapackage.sub.AnyClass anyClass = new c.javapacakge.sub.AnyClass();
- `import static c.javapackage.sub.AnyClass.*;`도 가능하다.

### import를 안해도 되는 경우

- java.lang 패키지 (Built-in package라고 부른다)
- 같은 패키지 (hierarchy와는 상관이 없다. 하나라도 다르면 다른거다.)

## 접근지정자

- Package는 접근지정자와도 관계가 있다.

### package-private

- 아무 접근 제어자도 적지 않으면, 디폴트로 `package-private 접근지정자`가 된다.
- **같은 패키지 내에 있을때 private에도 접근할 수 있다.**

### 클래스 선언과 접근지정자

- 접근지정자는 클래스 선언에도 붙을 수 있다.

```java
// 이 경우 파일명은 반드시 AAA.java이어야 한다
public class AAA {

}
```

- java 파일에 여러 개의 클래스가 있는 것은 상관없지만
- 하나의 java 파일에 여러 개의 `public class`는 있을 수 없다.

---

[1] 이상민, 9장 “자바를 배우면 패키지와 접근 제어자는 꼭 알아야 해요”, 자바의神 2nd Edition
[2] 생활코딩, [패키지 - 생활코딩](https://opentutorials.org/course/1223/5531)
