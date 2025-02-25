## 자바 개발을 하면 꼭 알아야 하는 클래스 패스

classpath는 자바 개발자가 반드시 알아야하는 개념이라는데, 간단한 자바 프로그램을 작성할 때 우리는 왜 이것을 신경쓰지 않았을까? 왜냐하면 간단한 프로그램에서는 일반적으로 **현재 경로에** 모든 사용자 클래스가 있을 것이고 JDK에서 제공하는 표준 라이브러리만을 사용했을 것이기 때문이다.

처음 자바를 설치할 때 뭔지도 모르고 따라했던 환경변수 설정에서 `CLASSPATH`를 설정한 것이 바로 JDK에서 제공하는 표준 라이브러리에 대한 클래스 패스 설정이었던 것이다.

**클래스 패스는 JVM이 프로그램을 실행할 때 클래스를 찾기 위한 기준이 되는 경로**로, 클래스 패스를 설정하지 않는다면 현재 경로를 디폴트로 바라보게 된다. Maven Repository 등을 통해 외부 라이브러리를 가져올 때는 따로 설정이 필요없지만, 예를 들어 직접 jar 파일을 통해 라이브러리를 사용하려고 한다면 클래스패스의 설정이 필요하다. IntelliJ와 같은 IDE를 사용하더라도 이는 마찬가지다. 공짜 점심은 없다.

[IntelliJ 클래스패스(classpath) 추가 & Resource 추가 : 네이버 블로그](https://m.blog.naver.com/haskim0716n/221812414926)[외부 jar 파일 dependency 하기](https://blog.thereis.xyz/50)

(참고로 C나 C++에서도 makefile을 통해 경로를 설정하지 않는다면 표준 외의 헤더파일의 include가 제대로 되지 않는다. 툴이든 사람이든 누군가의 도움으로 신경쓰지 않고 사용할 수 있을 뿐이다.)

### 눈으로 확인해보자

```java
class Test {
    public void test() {
            System.out.println("Test");
    }
}

class TestMain {
    public static void main(String[] args) {
            Test a = new Test();
            a.test();
    }
}
```

이렇게 2개의 클래스가 존재하는 java 파일을 컴파일하면 소스코드와 같은 경로에 **Test.class, TestMain.class라는 2개의 class 파일**이 생성된다.

```bash
$ java TestMain
```

이렇게 main 함수를 실행시켜보면 JVM이 디폴트로 **TestMain이 있는 현재경로**를 클래스패스로 생각하기 때문에 Test.class를 잘 찾아서 정상적으로 실행이 된다. 하지만 Test.class를 다른 경로(예컨대 하위 폴더)로 이동시키고 실행을 해보면 JVM은 Test.class를 찾지 못해 `NoClassDefFoundError`를 발생시킨다.

반대로 말하자면 생략되어 있어서 그렇지 `$ java TestMain`은 `$ java -cp "." TestMain`과 같은 상태인 것이다.

### 클래스패스 확인하기

아래와 같이 콘솔에서 환경변수를 확인하는 방법과 코드 상에서 확인해보는 방법이 있다.

### 콘솔 상에서 확인하기

```bash
$ echo $CLASSPATH
```

### 코드 상에서 확인하기

```java
import java.util.Properties;
public class Test {	public static void main(String[] args) {
		System.getProperty("java.class.path")
	}
}
```

### 클래스패스 추가하기

그렇다면 어떻게 클래스패스를 추가할 수 있을까? **-classpath**라는 옵션을 이용한다. 줄여서 **-cp** 옵션을 사용할 수도 있다.

> 주의할 점은 패키지가 지정되어 있을 때에는 그 클래스가 있는 경로를 클래스 패스로 설정하면 안된다. 패키지의 가장 상위 디렉터리가 있는 경로를 클래스 패스로 설정해야 한다.

```bash
$ java -classpath {경로} {클래스명}

# -cp로 줄여서 사용하기
$ java -cp /test Test

# 패키지가 있는 클래스의 경우(경로에 패키지의 최상위 경로를 넣는다)
$ java -classpath /test testpackage.Test (O)
$ java -classpath /test/testpackage Test (X)
```

### 여러 개의 클래스패스를 추가하기

구분자를 통해 여러개의 클래스패스를 추가할 수 있다. **윈도우의 경우 세미콜론(;), UNIX 계열에서는 콜론(:)**을 사용한다. 보통 일일이 지정하기보다는 스크립트로 만드는 것이 일반적이다.

```bash
// 공백없이 구분자로 연결하는 것을 주의한다
$ java -cp /lib/*:/test Test:/work com.package.Work
```

### 환경변수를 통해 추가하기

흔히 JDK 등을 설치할때처럼 환경변수를 통해 클래스패스를 추가할 수도 있다.

```bash
// 윈도우의 경우
$ set CLASSPATH=c:\\lib\\*;c:\\test;

// UNIX 계열의 경우
$ export CLASSPATH=/lib/*:/test

// 현재 클래스패스에 추가하는 방법
$ export CLASSPATH=$CLASSPATH:/test:/lib/*
```

다만 이렇게 지정한 환경변수는 **해당 콘솔에서만 유효**하다는 것을 주의하자. 즉, 해당 콘솔이 꺼지고 나서는 설정이 사라진다는 말이다. 이를 유지하기 위해 다음 항목을 살펴보자.

### 시스템 상에 클래스패스 설정을 유지하기

윈도우에서는 OS상의 환경설정 값에 지정하면 되고, UNIX 계열에서는 profile 파일에 설정을 해주면 된다. 예를 들어 .bash_profile을 열어서 아래와 같이 클래스패스를 추가해줄 수 있다.

```bash
export CLASSPATH=$CLASSPATH:/test:/lib/*
```

이는 따로 수정되지 않는한 설정이 유지된다.

### IntelliJ에서 클래스패스 확인

`File > Project Structure > Modules` 항목을 살펴보면 현재 프로젝트의 경로 정보를 확인해볼 수 있다.

## 생활코딩 인용

> 필자는 상수, 변수, 연산자, 조건문, 반복문을 가장 기본적인 프로그래밍의 수단이라고 생각한다. 다시 말해서 로직 자체라고 생각 할 수 있다. 반면에 메소드, 유효범위, 클래스, 클래스 패스와 같은 것들을 관통하는 공통분모는 로직의 체계적인 관리수단이라고 할 수 있다. 로직 자체는 간단하다. 하지만, 이 로직을 체계적으로 관리하기 위한 수단들은 많다. 복잡하다. 프로그래밍이 이러한 복잡성을 감수하고 있는 것은 로직의 체계적 관리가 그만큼 중요한 문제라는 반증일 수도 있다. 그 중요성을 이해한다면 이러한 수단들이 단지 공부거리가 아니라 개발을 쾌적하게 도와주는 매우 유용한 도구라는 것을 이해할 수 있을 것이다.

---

[1] 최홍희, [자바 클래스패스 — 왜 모르는가?](https://vvshinevv.tistory.com/70)
[2] 이상민, “classpath와 자바 옵션들”, 자바의神 2nd Edition
[3] 생활코딩, “클래스 패스”, Java
