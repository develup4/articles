## 엔티티 매핑

- `@Entity`가 붙은 클래스는 JPA가 관리하며 엔티티라고 부른다.
- RDBMS의 `Table`과 매핑된다.

### 주의사항

- 파라메터가 없는 public 또는 protected **기본 생성자**가 필요하다.
- final 클래스, enum, interface, inner 클래스에는 사용할 수 없다.
- 저장할 필드에 final을 사용할 수 없다.
- 내부 구현상 상속받는 프록시 객체가 생겨서 발생하는 주의사항들이다.

### 데이터베이스 스키마 자동 생성

> create : 기존 테이블 삭제 후 다시 생성(DROP + CREATE) 한다.
> create-drop : create와 같으나 종료 시점에 테이블을 drop한다
> update : 변경된 사항만 반영된다.
> validate : 엔티티와 테이블이 정상 매핑되었는지 확인만 한다.
> none : 사용하지 않음

- 운영 환경에서는 **절대 create, create-drop, update**를 사용해서는 안된다.
- 개발 초기단계는 create 또는 update, 테스트서버는 update 또는 validate, 스테이징과 운영 서버는 validate 또는 none이 권장된다.

### 임베디드 타입

- `@Embeddable` : 값 타입을 정의하는 곳에 표시
- `@Embedded` : 값 타입을 사용하는 곳에 표시
- 기본 생성자가 필수이다.
- 별도의 클래스로 정의할 수 있지만 테이블 상으로는 사용하는 곳과 같은 테이블에 위치한다.
- 잘 설계한 ORM 어플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.
- 다만 임베디드 타입으로 정의한 객체는 객체이므로 대입시 복사가 아니라 참조가 일어난다. 공유 참조의 문제가 있을 수 있으므로 주의한다. 불변객체로 만든다면 가장 좋다.
- 임베디드 타입처럼 값타입인 경우 컬렉션으로 여러개를 저장할 때 별도의 테이블이 필요하다. 그리고 값 타입은 추적이 어렵다. 컬렉션으로 사용해야할 경우 일대다 관계를 사용하는 것이 권장된다.
- 식별자가 필요하고, 지속해서 값을 추적하고 변경해야 한다면 그것은 값 타입이 아니라 엔티티이다.

### 영속성 전이

- 엔티티를 영속화 할 때 연관된 엔티티도 함께 영속화하는 기능을 제공한다.
- 예를 들어 멤버를 저장할때 팀도 함께 **저장**, **삭제**
- 연관관계 매핑과는 전혀 관계가 없다! 주의. (이건 셀렉트와 관걔된거)

```java
@OneToMany(mappedBy=“parent”, cascade=CascadeType.PERSIST)
```

### 종류

> ALL : 모두 적용
> PERSIST : 영속
> REMOVE : 삭제
> MERGE : 병합
> REFRESH
> DETACH

## 연관관계 매핑

### 단방향

```java
@Entity
public class Member {
	@ManyToOne
	@JoinColumn(name = “TEAM_ID”)
	private Team team;
}
```

- 위와 같이 `@JoinColumn`을 통해 연관관계를 매핑하는 경우, `MEMBER` 테이블에 `TEAM_ID`라는 **ForeignKey**가 추가되는 형태가 된다.

### 프록시 객체

- 하지만 `team`이 실제로 코드상에서 참조되기 전까지는 team은 **프록시 객체**의 형태로 존재하게 되고 이를 통해 `지연 로딩`이 가능하게 된다.
- 위의 경우에도 영속성 컨텍스트에 찾는 엔티티가 이미 있다면 프롥시가 아닌 실제 엔티티가 반환된다.
- `@ManyToOne(fetch = FetchType.EAGER)`로 설정할 경우(사실 기본값이다) 지연로딩이 일어나지 않고 즉시 로딩이 일어난다. 하지만 절대로 EAGER를 사용하지 말자.
- 즉시로딩은 N+1 문제를 일으킨다. 상상만 해봐도 엄청 큰 트리 형태의 객체를 가져오기 위해 엄청난 부하가 발생하는 일이 있을 수 있다. 기본값이 EAGER이므로 열심히 LAZY로 설정하자.
- 즉시로딩이 필요하다면? EAGER가 아니라 `FETCH JOIN`을 이용하자! N+1 문제가 해결된다.

### 양방향

```java
@Entity
public class Member {
	@ManyToOne
	@JoinColumn(name = “TEAM_ID”)	// 외래키를 매핑할 때 사용한다. 즉 멤버의 테이블에 외래키
	private Team team;
}

@Entity
public class Team {
	@OneToMany(mappedBy = “team”)	// 반대쪽에 매핑되는 필드의 값을 준다
	List<Member> members = new ArrayList<>();
}
```

- 테이블은 **외래키 하나**로 두 테이블의 연관 관계를 관리한다.
- 반면 객체는 각각의 참조(team, members)를 각각 가진다.
- **연관 관계의 주인이 외래 키를 가진다.**
- 주인이 아닌쪽은 **읽기 만이** 가능하다.
- 단방향 매핑만으로 데이터베이스 입장에서는 이미 연관관계가 매핑되지만 양방향을 통해 반대 방향으로 객체 그래프 탐색기능이 추가되는 것이다.
- 주인이 아닌 경우 `mappedBy` 속성을 통해 주인을 지정한다.
- 일대다는 직관적이지 않다. 다대일을 사용하도록 하고 다쪽에 외래키를 두도록 한다.

### 연관관계 편의 메소드

```java
Member member = new Member();
Team team = new Team;
member.setTeam(team);
team.addMember(member);

// 양방향의 경우 연관관계 편의 메소드 활용을 통해 null 참조를 피하자
public void setTeam(Team team) {
	this.team = team;
	team.addMember(this);
}
```

- 양방향 매핑시 `toString`, `lombok`, `JSON 라이브러리` 등을 통한 무한루프도 조심해야 한다.

---

[1] 김영한, 자바 ORM 표준 JPA 프로그래밍
