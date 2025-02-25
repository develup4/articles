## EntityManager와 EntityManager Factory

![Untitled](SpringJPAContext/Untitled.png)

## 영속성 컨텍스트

- JPA를 이해하는데 가장 중요한 용어이다.
- 엔티티를 영구 저장하는 환경이라는 뜻이다.

`EntityManager.persist(entity);`

- 엔티티 매니저를 통해서 접근할 수 있다.

## JPA Entity의 생명주기

![Untitled](SpringJPAContext/Untitled1.png)

- **비영속 상태:** JPA가 관리하지 않는 그냥 자바 객체이다.

```java
//객체를 생성한 상태(비영속)
 Member member = new Member();
 member.setId("member1");
 member.setUsername("회원1");
```

- **영속 상태:** 그렇다면 이건 관리

```java
//객체를 생성한 상태(비영속)
 Member member = new Member();
 member.setId("member1");
 member.setUsername(“회원1”);
 EntityManager em = emf.createEntityManager();
 em.getTransaction().begin();
 //객체를 저장한 상태(영속)
 em.persist(member);

// 혹은 JPA에서 얻어온 객체(find 등)
```

무엇이 영속상태인가를 알수있는것이 중요하다. 나중에 개념을 통해 제대로 이해해보자.

- **준영속상태:** 영속상태였다가 분리된 상태
  em.detach(member);
- **삭제**
  em.remove(member);

## 영속성 컨텍스트의 이점

- 1차 캐시
- 사실 트랜잭션 하나 내에서의 캐시이므로 비지니스 로직이 엄청나게 크지않다면 성능상의 이점은 별로 없지만, 영속 상태를 구현하는데 가장 결정적인 요소이다. 사실상 1차 캐시를 영속성 컨텍스트라고 봐도 된다.
- 요약해서 설명하면, 영속성 컨텍스트에는 1차 캐시라는 곳이 존재해서 이를 통해 실제로 쿼리가 나갈지 안나갈지가 결정된다.

![Untitled](SpringJPAContext/Untitled2.png)

위 그림처럼 현재 영속성 컨텍스트에 있는 객체들은 1차 캐시에 보관되어 있으며, find를 통해 객체를 찾았을때 캐시에 존재한다면 실제로 쿼리는 나가지 않는다.

![Untitled](SpringJPAContext/Untitled3.png)

반면 캐시에 없는 객체 찾는다면 데이터베이스를 조회하게 되고, 얻어온 값은 반환되며 동시에 영속성 컨텍스트에 보관도 된다. 다음에 찾는 일이 있다면 캐시에서 해결될 것이다. 뒤에 설명에 나오겠지만 이러한 경우에도 쿼리가 바로 나가는 것은 아니다. flush되는 시점은 뒤에서 설명한다.

- 영속엔티티의 동일성 보장

```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");
System.out.println(a == b); //동일성 비교 true
```

위 코드에서 보듯 문제사항이었던 동일성도 보장된다. 1차 캐시가 반복 가능한 읽기(Repeatable Read)를 제공하기 때문이다.
=> **트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션 차원에서 제공**

- 트랜잭션을 지원하는 쓰기 지연

![Untitled](SpringJPAContext/Untitled4.png)

아까 설명했던 쿼리가 실제로 나가는 시점이다. flash()가 호출될때 실제로 쿼리가 나가지만, 트랜잭션이 commit될때 flush가 일어나므로 사실상 이 시점에 호출된다. 위 그림처럼 캐시에 연산이 일어날때마다 필요한 쿼리가 저장소에 저장됐다가 나가므로 쿼리를 한번에 버퍼링해서 날리는 성능상의 이점도 있다(설정에서 버퍼의 크기도 정할 수 있다.)

- 변경 감지(Dirty Checking)

```java
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
transaction.begin(); // [트랜잭션] 시작

// 영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

//em.update(member) 이런 코드가 있어야 하지 않을까? <========== 요부분 중요
transaction.commit(); // [트랜잭션] 커밋
```

![Untitled](SpringJPAContext/Untitled5.png)

영속성 객체는 따로 명령없이 객체만 변경되어도 변경사항이 데이터베이스에 적용된다. 이를 더티체킹이라고 한다. 원리는 위처럼 스냅샷과의 비교를 통해 업데이트 쿼리가 자동으로 생성되는 형식이다.

무엇인 영속성 객체인지를 잘이해하면 객체 수정 이외의 동작은 필요하지 않다.

## Flush()

- em.flush() : 별로쓸일은 없다.
- 트랜잭션 커밋
- JPQL 쿼리실행

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

//중간에 JPQL 실행
query = em.createQuery("select m from Member m", Member.class);
List<Member> members= query.getResultList();
```

제대로 jpql이 수행되려면 이전 결과가 반영되어 있어야하므로

- 이름때문에 헷갈리지 말자. SQL저장소가 플러시되는 것이다. 영속성 컨텍스트는 유지된다.
- clear()에 의해서 영속성 컨텍스트가 사라진다.

## 준영속상태가 되는법

- em.detach(entity)
- em.clear()
- em.close()

## 영속성 컨텍스트의 수명

아까 1차 캐시의 성능상의 이점은 별로 없다고 말한것이, 트랜잭션이 끝나면 영속성 컨텍스트는 삭제된다(이거 맞는지 확인)

---

[1] 김영한, 자바 ORM 표준 JPA 프로그래밍
