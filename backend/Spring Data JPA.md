## Transaction

- 스프링 데이터 JPA는 `@Transaction`없이도 등록, 수정, 삭제를 처리한다.
- `서비스 계층(외부)`에서 Transaction이 시작되면 해당 Transaction을 전파받아서 사용한다.
- 그렇지 않다면 `Repository(내부)`에서 자체적으로 Transaction을 시작한다.

## 쿼리 메소드

### 메소드 이름으로 쿼리 생성

- 순수 JPA

```java
public List<Member> findByUsernameAndAgeGreaterThan(String username, int age) {
	return em.createQuery("select m from Member m where m.username = :username and m.age > :age")
		.setParameter("username", username)
		.setParameter("age", age) .getResultList();
}
```

- 스프링 데이터 JPA

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
	List<Member> findByUsernameAndAgeGreaterThan(String username, int age);
}
```

### 조회

- `find…By`
- `read…By`
- `query…By`
- `get…By`
- `findHelloBy`처럼 …에 식별하기 위한 내용(설명)이 들어가도 된다.

### COUNT

- `long count…By()`

### EXISTS

- `boolean exists…By()`

### DELETE

- `long delete…By()`
- `long remove…By()`

### DISTINCT

- `findDistinct`
- `findMemberDistinctBy`

### LIMIT

- `findFirst3`
- `findFirst`
- `findTop`
- `findTop3`

### @Query를 통해 Repository 메소드에 쿼리 정의하기

```java
public interface MemberRepository extends JpaRepository<Member, Long> {

	@Query("select m from Member m where m.username= :username and m.age = :age")
	List<Member> findUser(@Param("username") String username, @Param("age") int age);
}
```

- 이전 항목인 메소드 이름으로 쿼리 생성시 파라메터가 증가하면 이름이 매우 지저분해진다. 이런 경우 `@Query`를 자주 사용하게 된다.
- 쿼리문은 JPQL을 통해 작성한다.
- `@Param`을 통해 쿼리문의 파라메터를 바인딩한다.
- 아래와 같이 컬렉션에 대해서도 `in절` 안의 파라메터를 바인딩 할 수 있다.

```java
@Query("select m from Member m where m.username in :names")
List<Member> findByNames(@Param("names") List<String> names);
```

### 값 타입 조회하기

```java
@Query("select m.username from Member m")
List<String> findUsernameList();
```

### DTO로 직접 조회하기

```java
@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) from Member m join m.team t")
List<MemberDto> findMemberDto();
```

### 조회 결과

- 다양한 타입으로 지정이 가능하다.

### 컬렉션

- 결과가 없으면 빈 컬렉션을 반환한다.

```java
List<Member> findByUsername(String name);
```

### 단건

- Optional을 붙일 수도 있다.
- 결과가 없으면 null, 2건 이상일 경우 `javax.persistence.NonUniqueResultException`일 발생된다.

```java
Member findByUsername(String name);
Optional<Member> findByUsername(String name);
```

### 네이티브 쿼리

- 가급적 사용하지 말자.

```java
@Query(value = "select * from member where username = ?", nativeQuery = true)
Member findByNativeQuery(String username);
```

## 페이징과 정렬

### 파라메터

- `org.springframework.data.domain.Sort` : 정렬 기능
- `org.springframework.data.domain.Pageable` : 페이징 기능 (내부에 Sort 포함) 특별한 반환 타입

### 반환타입

- `org.springframework.data.domain.Page` : 추가 count 쿼리 결과를 포함하는 페이징
- `org.springframework.data.domain.Slice` : 추가 count 쿼리 없이 다음 페이지만 확인 가능 (내부적으로 limit + 1조회)
- `List (자바 컬렉션)`: 추가 count 쿼리 없이 결과만 반환

> count 쿼리는 매우 무겁다. 별도로 쿼리를 분리해서 만드는 것을 추천한다.

### 예제

- Page는 1이 아니라 0부터 시작하는 것을 주의하자

```java
Page<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용
Slice<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함
List<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함
List<Member> findByUsername(String name, Sort sort);

// PageRequest는 Pagable 인터페이스의 구현체이다
PageRequest pageRequest = PageRequest.of(0, 3, Sort.by(Sort.Direction.DESC, "username")); Page page = memberRepository.findByAge(10, pageRequest);

List<Member> content = page.getContent(); //조회된 데이터
assertThat(content.size()).isEqualTo(3); //조회된 데이터 수
assertThat(page.getTotalElements()).isEqualTo(5); //전체 데이터 수
assertThat(page.getNumber()).isEqualTo(0); //페이지 번호
assertThat(page.getTotalPages()).isEqualTo(2); //전체 페이지 번호
assertThat(page.isFirst()).isTrue(); //첫번째 항목인가?
assertThat(page.hasNext()).isTrue(); //다음 페이지가 있는가?
```

### DTO 변환

```java
Page<Member> page = memberRepository.findByAge(10, pageRequest);
Page<MemberDto> dtoPage = page.map(m -> new MemberDto());
```

### Controller에서의 활용

- 간단한 경우에 매우 편리하게 Paging API를 만들 수 있다.

```java
// 기본 페이지 사이즈: spring.data.web.pageable.default-page-size=20
// 최대 페이지 사이즈: spring.data.web.pageable.max-page-size=2000

// "/members?page=0&size=3&sort=id,desc&sort=username,desc"
@GetMapping("/members")
public Page list(Pageable pageable) {
	Page page = memberRepository.findAll(pageable);
	return page;
}

// 직접 설정값을 지정하는 경우
@RequestMapping(value = "/members_page", method = RequestMethod.GET)
public String list(@PageableDefault(size = 12, sort = “username”, direction = Sort.Direction.DESC) Pageable pageable) {
	Page page = memberRepository.findAll(pageable);
	return page;
}

// Dto로 반환하는 경우
@GetMapping("/members")
public Page list(Pageable pageable) {
	return memberRepository.findAll(pageable).map(MemberDto::new);
}
```

## 도메인 클래스 컨버터

- HTTP 파라메터로 넘어온 Entity의 id로 객체를 찾아서 바인딩해준다.
- 주의할 점은 **단순 조회용**으로 Entity를 사용해야 한다는 점이다. 변경하더라도 DB에 반영되지 않는다.

```java
@RestController
public class MemberController {

	private final MemberRepository memberRepository;

	@GetMapping("/members/{id}")
	public String findMember(@PathVariable("id") Member member) {
		return member.getUsername();
	}
}
```

## Bulk Update 쿼리

- 벌크성 수정 및 삭제는 `@Modifying` 어노테이션을 사용한다.
- 벌크 연산은 영속성 컨텍스트를 무시하고 실행된다. 따라서 동기화를 위해 **영속성 컨텍스트를 초기화**해야 한다.
- 벌크성 쿼리 후 영속성 컨텍스트를 초기화하려면 `@Modifying(clearAutomatically = true)`를 사용한다.

```java
@Modifying(clearAutomatically = true)
@Query("update Member m set m.age = m.age + 1 where m.age >= :age")
int bulkAgePlus(@Param("age") int age);
```

## @EntityGraph

- 지연로딩은 훌륭한 기능이지만 `N+1` 문제를 발생시키기도 한다.
- 해결을 위해 `fetch join`이라는 좋은 방법이 있지만 JPQL을 써야하는 번거로움이 있다.
- 간편한 사용을 위해 `@EntityGraph`가 제공되고 내부적으로 `LEFT OUTER JOIN`이 사용된다.

```java
@EntityGraph(attributePaths = {"team"})
List<Member> findAll();

@EntityGraph(attributePaths = {"team"})
@Query("select m from Member m")
List<Member> findMemberEntityGraph();

@EntityGraph(attributePaths = {"team"})
List<Member> findByUsername(String username);
```

### @NamedEntityGraph

- Entity를 정의할 때, EntityGraph에 이름을 붙여 활용할 수 있다.

```java
// Entity 파일
@NamedEntityGraph(name = "Member.all", attributeNodes = @NamedAttributeNode("team")) @Entity
public class Member {

}

// Repository 파일
@EntityGraph("Member.all")
@Query("select m from Member m")
List<Member> findMemberEntityGraph();
```

## JPA Hint와 Lock

```java
@QueryHints(value = @QueryHint(name = "org.hibernate.readOnly", value = "true"))
Member findReadOnlyByUsername(String username);		// update 쿼리 등이 실행되지 않는다.

@Lock(LockModeType.PESSIMISTIC_WRITE)
List<Member> findByUsername(String name);
```

## Projections

- 원하는 형태로 데이터를 가져올 수 있다.
- 복잡한 경우를 처리하기는 어려우므로 그런 경우에는 `QueryDSL`을 사용하자.
- 그래도 네이티브 쿼리보다 낫다! 그런 경우 사용하자.

```java
// Getter 형태를 사용
public interface UsernameOnly {
	String getUsername();
}

// SpEL 문법 사용(다만 모두 개별로 조회한 후 조합한다)
public interface UsernameOnly {
	@Value("#{target.username + ' ' + target.age + ' ' + target.team.name}")
	String getUsername();
}

public interface MemberRepository extends JpaRepository<Member, Long> {
	List<UsernameOnly> findProjectionsByUsername(String username);
}
```

---

[1] 김영한, 자바 ORM 표준 JPA 프로그래밍
