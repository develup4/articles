## Jar

- jar는 여러 개의 **클래스 파일**을 하나의 파일로 묶기 위해서 사용한다.
- java 명령과 동일한 위치인 `{jdk 설치위치}/bin/`에 포함되어 있다.
- jar는 사실상 그냥 `압축파일`이다. **tar 명령과 기본옵션이 동일**히다.
- `c` : jar 만들기
  `u` : jar 수정하기
  `x` : jar 풀기
  `t` : jar 내의 파일 목록 확인하기
- `f` : 파일명 지정
  `v` : verbose 로그 출력(jar에서는 필수)
  `m` : manifest 파일 지정(MANIFEST.MF 파일)

```bash
$ cd {클래스파일들이 있는 디렉토리}
$ jar -cvf b.jar ./b
$ jar -tvf b.jar		(파일 목록 확인)
$ jar -uvf b.jar ./c		(파일추가)
$ jar -xvf b.jar			(jar 풀기)
```

## Maven 프로젝트에서의 Jar

### Jar 생성

```bash
$ mvnw clean package		# clean은 기존 target 디렉토리 삭제, package는 jar 파일의 생성
$ mvnw clean package -DskipTests		# 테스트 코드를 생략하여 더 빠르다.
```

### Jar로 어플리케이션 실행

```bash
$ java -jar target/{JarFile}.jar
```

### Application Argument 전달

- JVM argument
  - `D`로 시작한다.
  - (ex) java -jar `D`skipTests
- Application argument
  - `-`로 시작한다.
  - (ex) java -jar `—square` blahblah.jar
- IntelliJ의 Run/Debug Configurations에서 입력도 가능하다.

> Application Argument 사용하기
> Bean 내의 인자가 단 하나인 생성자에서 ApplicationArguments 타입을 통해 주입받을 수 있다.

```java
@Component
public class SimpleArgument {
    public SimpleArgument(ApplicationArguments arguments) {
        System.out.println("square : " + arguments.containsOption("square"));
    }
}
```

---

[1] 이상민, 부록6 “Jar”, 자바의神 2nd Edition
[2] 백기선, 스프링 부트 개념과 활용, 4부 스프링 부트 활용
