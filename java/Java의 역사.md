## 자바의 역사

- 1991년, 제임스 고슬링 외 2인
- TV와 시청자가 상호작용하기 위한 용도로 시작
- 제임스 고슬링은 Sun이 Oracle로 넘어가기 전까지 자바와 함께 함

## Java SE, JDK, JRE

### JavaSE

- Java SE(Java Platform, Standard Edition)는 자바의 표준안이다. 자바라는 언어가 어떠한 문법적인 구성을 가졌는지와 같은 것들을 정의하고 있다. 이것은 구체적인 소프트웨어가 아니고 그 **소프트웨어의 설계도**라고 할 수 있다. 소프트웨어에서는 설계도라는 표현 대신에 명세서(spec, specification)이라는 말을 사용한다. 이 명세서에 따라서 Java가 만들어지게 된다. [Java SE 7](http://docs.oracle.com/javase/specs/jls/se7/html/index.html) 은 버전 7에 대한 명세서이다. 자바는 계속 진화하고 있는 기술이다. 이 명세서는 JCP(Java Community Process, [http://jcp.org](http://jcp.org/))라는 조직을 통해서 만들어진다.
  - JSR(Java Specification Requirement) : 자바에서 사용할 기술들을 나열한 문서

### JDK의 플랫폼에 따른 차이

- 자바는 예전의 Sun, 지금의 Oracle에서 만드는 것이 아니다.
- 표준 문서가 만들어지면 그 기준에 해당하는 각 벤더에 맞는 JDK가 별도로 만들어진다.
- Oracle JDK, HP의 JDK, JRockit JDK(WAS 최적화), Open JDK(라이센스 문제가 없는 완전한 오픈소스)

### JDK

- JDK(Java Development Kit)는 Java SE의 표준안에 따라서 만들어진 구체적인 소프트웨어다. Java 개발자라면 JDK를 다운받아서 설치해야 한다. 여기에는 Java 프로그램을 실행하면 Java 코드를 컴파일하는 컴파일러와 개발에 필요한 각종 도구 그리고 JRE가 포함되어 있다. 즉 개발자를 위한 자바 버전이다.

### JRE

- JRE(Java Runtime Environment)는 자바가 실제로 동작하는 데 필요한 JVM, 라이브러리, 각종 파일들이 포함되어 있다. 자바로 만들어진 프로그램을 구동하려고 한다면 이것을 설치한다. 일반인을 위한 자바 버전이라고 할 수 있다.

### JVM

- JVM(Java Virtual Machine) JVM은 자바가 실제도 구동하는 환경이다. 자바로 만들어진 소프트웨어는 JVM이라는 가상화된 환경에서 구동되고, 하드웨어나 운영체제에 따라서 달라질 수 있는 호환성의 문제는 운영체제 버전에 따라서 만들어진 JVM이 알아서 해결한다. 즉 하나의 자바 프로그램을 만들면 어떤 환경에서도 실행할 수 있는 것이 바로 JVM의 역할이라고 할 수 있다.

## 자바의 버전

- Java 의 버전은 역사적인 이유로 복잡하다. 현재 2013년 12월 기준으로 Java의 정식 명칭은 ‘Java SE 7’이다. 하지만 이것을 JDK 1.7이라고도 하고, JDK 7이라고도 하므로 혼란이 있다. 자바의 역사에 대해서는 [Java version history](http://en.wikipedia.org/wiki/Java_version_history) 를 참고한다.
- Java SE 1.5 버전부터는 앞의 1.을 뺀 Java SE 5라고 부르게 되었다.

## JDK의 분류

자바의 개발환경인 JDK에는 3가지가 있다. 이 중에 가장 중요한 JDK는 Java SE다. Java SE는 Java EE나 Java ME의 기반 환경이기도 하므로 무엇을 하든 간에 Java SE를 JDK를 설치해야 한다. 우리 수업은 Java SE를 다룬다.

- Java SE (Standard Edition) : 자바의 핵심으로 일반적으로 자바라고 하면 Java SE를 의미한다.
- Java EE (Enterprise Edition) : 기업용 시장에서 사용하는 자바 개발환경
- Jave Me (Micro Edition) : 모바일 개발을 위해서 사용하는 자바 버전

## 자바 언어의 특징

- 자바는 단순하고 객체지향이며 친숙해야 한다.
- 자바는 견고하며 보안상 안전하다.
  - 메모리 관리 모델이 단순
  - 기본적으로 분산 환경에서 사용하기 위해 디자인
- 자바는 아키텍처에 중립적이어야하며 포터블해야 한다.
  - 기본 데이터 타입의 크기 지정
  - 숫자 연산자에 대한 행위가 정의됨
- 자바는 높은 성능을 제공해야 한다.
  - 가비지 컬렉터는 낮은 우선 순위의 쓰레드에서 동작
- 자바는 인터프리트 언어이며, 쓰레드를 제공하고, 동적인 언어이다.
  - 자바 바이트 코드를 어떤 장비에서도 수행할 수 있도록 해준다.

## 자바의 버전별 차이

- 1.0 : 최초의 버전
- 1.1
  - Inner class 추가
  - JavaBeans 추가
  - JDBC 추가
- 1.2
  - Java Collections 추가
  - JIT 컴파일러 추가 : **Just-In-Time의 약자로 일부 혹은 전체 코드를 네이티브 코드로 변환하여 JVM에서 변역하지 않도록 함**
- 1.3
  - JNDI(Java Naming and Directory Interface) : 객체를 쉽게 찾기 위한 이름 지정
  - HotSpot JVM 제공 : CPU 코어가 하나뿐인 사용자를 위한 JVM
- 1.4
  - 정규표현식 추가
  - Exception chaining 추가
  - IPv6 지원
  - NIO 추가
- 5
  - 제네릭 추가
  - 애노테이션 기능 추가
  - enum 추가
  - varargs 추가
  - ranged-for 구문 추가
  - static import 추가
  - concurrent 패키지 추가
  - Scanner 클래스 추가
- 6
  - JVM에서 스크립트 언어가 수행 가능해짐
- 7
  - 2진수 표현법(0b), 큰 숫자 표시법(1_000_000) 추가
  - switch 문에 String 타입 사용 가능
  - catch 문에 여러 예외를 OR 표기법으로 함께 사용 가능
  - 제네릭 생성자 다이아몬드 표기법 추가
    - `Map<String, List<String>> map = new HashMap<>();`
- 8
  - 별도 항목에 정리할 정도로 많은 변화

### 배포 정책

- LTS 버전은 Java 8이다.
  - 2014년 3월 릴리즈
  - 현재 자바 개발자 중 83%의 점유율
- 비LTS 버전
  - 비-LTS는 업데이트 제공 기간이 짧다. 배포 주기가 6개월이며 지원기간도 6개월이다.
  - 매년 3월과 9월에 새 버전이 배포된다.
  - LTS 배포 주기는 3년으로 매 6번째 배포판이 LTS가 된다. 지원기간은 5년 이상이다.

---

[1] 생활코딩, [언어소개 - 생활코딩](https://opentutorials.org/course/1223/4551)
[2] 자바의 신 19장, 로드북, 2019
[3] 백기선, 더 자바 Java 8, 자바 8 소개
