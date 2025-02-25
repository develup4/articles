## 도입 - 기존 방식의 문제점

- 객체와 관계형 데이터베이스의 패러다임의 불일치
- 객체지향이 아무리 좋아도 영구적으로 보관하기 위해서는 현실적으로느 관계형 데이터베이스를 사용한다.
- SQL에 의존적인 개발을 피하기 어렵다.

![Untitled](SpringJPA/Untitled.png)

- 관계형 데이터베이스에도 상속관계와 유사한 구조가 있지만, 모든 쿼리에 조인을 넣어서 구현할 것인가?
- 객체 내부의 Has-A 관계를 계층적으로 표현할 수는 없을까?
- 결론적으로 관계형 데이터베이스도 자바 컬렉션처럼 사용할 수는 없을까?
- 그리고 엔티티를 신뢰할 수 있을까?

```java
class MemberDAO {
  public Member getMember(String memberId) {
 String sql = "SELECT * FROM MEMBER WHERE MEMBER_ID = ?";
 ...
 //JDBC API, SQL 실행
 return new Member(...);
 }
}

String memberId = "100";
Member member1 = memberDAO.getMember(memberId);
Member member2 = memberDAO.getMember(memberId);
member1 == member2; //다르다.
```

위처럼 쿼리를 통해 얻어온 엔티티는 같은 객체가 아니지만 우리가 쓰는 컬렉션은 같은 객체를 얻어올 수 있다.

```java
String memberId = "100";
Member member1 = list.get(memberId);
Member member2 = list.get(memberId);
member1 == member2; //같다.
```

이러한 도입배경으로 인해 JPA라는 기술이 대두되게 된다.

## JPA

- Java Persistence API
- node.js의 TypeORM, django의 django ORM처럼 자바 진영의 ORM 기술 표준이다.
- Object-relational mapping(객체 관계 매핑)
- 좋은 프레임워크들이 그렇듯 기존의 JDBC를 잘 wrapping해서 좋은 인터페이스를 제공한다.

### History

- 스프링의 모태이기도 한 EJB(엔터프라이즈 자바 빈)에서 불편함을 느낀 개발자들이 하이버네이트라는 오픈소스를 개발하였다.
- 대중적으로 엄청나게 흥행하여 사실상의 표준이 되자 자바 진영에서 하이버네이트를 수용하여 JPA라는 표준을 정의하였다.
- 스프링과는 다른 양상으로 스프링은 사실상 표준이지만 아직 자바진영에서 표준은 아니다.
- JPA는 인터페이스의 모음으로 하이버네이트는 그 구현체이다. 사실 구현체가 더 먼저 나오기는 했지만…
- Hibernate, EclipseLink, DataNucleus같은 구현체들이 있다.

### 패러다임 불일치 문제를 해결

- 위에서 언급했던 모든 문제를 JPA가 해결하였다.
- 테이블의 슈퍼타입, 서브타입 관계를 객체의 상속관계와 매핑하여 깔끔한 인터페이스를 제공한다. 물론 뒤에서는 백조의 발처럼 엄청난 노가다가 대신 이루어지고 있다.
- Has-A 관계를 표현할 수 있게 되었다. 객체 내부의 객체도 JPA가 처리해줄 수 있게 되었다.

```java
 member.setTeam(team);
 jpa.persist(member);

 Member member = jpa.find(Member.class, memberId);
 Team team = member.getTeam();
```

- 엔티티의 신뢰성이 생겼다.

```java
String memberId = "100";
Member member1 = jpa.find(Member.class, memberId);
Member member2 = jpa.find(Member.class, memberId);
member1 == member2; //같다.
```

### JPA에서 생긴 추가적인 장점

- 1차 캐시와 동일성(identity) 보장
- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)
- 지연 로딩(Lazy Loading)

## JPA를 실제로 사용해보자

### Dialect(방언)

- 세팅(persistence.xml)시 필요하다. 관계형 데이터베이스마다 비슷한듯 다른 SQL 문법을 가지고 있으므로 설정해주게 되면 알아서 하이버네이트가 번역해준다.
- 하이버네이트는 대부분의 관계형 데이터베이스를 모두 지원해준다.

```yaml
H2: org.hibernate.dialect.H2Dialect
Oracle 10g: org.hibernate.dialect.Oracle10gDialect
MySQL: org.hibernate.dialect.MySQL5InnoDBDialect
```

### 구동방식

![Untitled](SpringJPA/Untitled1.png)

- 엔티티 매니저 팩토리는 하나만 생성해서 애플리케이션 전체에 서 공유
- 엔티티 매니저는 쓰레드간에 공유X (사용하고 버려야 한다).
  => 위 두개는 Bean의 특성이기도 하다.
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행

### JPA로 모두 다 표현할 수 있는가?

- 안될건 없지만 복잡한 쿼리를 표현하기 위해서는 다른 기술들이 필요하다.
- JPQL : SQL과 유사하지만 테이블이 아닌 엔티티 객체를 대상으로 쿼리문을 작성할 수 있다. 다소 아쉽지만 그래도 생 SQL보다 많은 이점이 있다.
- QueryDSL : 아주 복잡한 쿼리도 자바 코드로 표현할 수 있다.

---

[1] 김영한, 자바 ORM 표준 JPA 프로그래밍
