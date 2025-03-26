
# ORM과 아키텍처

- 레이어 아키텍처 
	- 프리젠테이션 레이어
	- 도메인 레이어
	- 퍼시스턴스 레이어


### 도메인 레이어
- 엔터프라이즈 애플리케이션 아키텍처 패턴 (마틴 파울러)
	- EJB와 POJO가 혼용되던 시절의 아키텍처 패턴 정립
	- 도메인 모델, 트랜잭션 스트립트, 액티브 레코드 등의 용어 정리
	
- 트랜잭션 스트립트 
	- 모든 논리를 주로 **단일 프로시저**로 구성하여 데이터베이스에서 직접 또는 얇은 데이터베이스 래퍼를 통해 호출한다.
	- 공통적인 하위 작업은 더 작은 절차로 나뉠 수 있겠지만, 각 트랜잭션은 자신만의 트랜잭션 스트립트를 가지게 된다.
	- 프로세스와 데이터를 분리 -> 도메인 로직을 절차적인 방식으로 구현
	- 즉, 도메인 모델이 빈약하다 (도메인은 로직없이 필드와 getter, setter로만 이루워짐)
- 도메인 모델 
	- 비즈니스 로직은 경우에 따라 아주 복잡할 수 있다 -> 규칙과 논리는 매우 다양한 사례와 동작의 변형을 나타내기 때문에 객체는 이러한 복잡성을 처리하기 위해 고안되었다.
	- 도메인 모델은 각 객체가 의미있는 하나의 대상을 나타내는 상호 연결된 객체의 연결망으로 이루워진다.
	- 프로세스와 데이터의 통합 -> 도메인 로직을 객체지향 방식으로 구현
	- **상태**와 **행위**를 포함하는 객체 사이의 **협력**을 통해 구현



## 서비스 레이어
- 도메인 레이어의 캡슐화
	- 도메인 레이어가 서비스 레이어와 도메인 레이어로 분리
	- 계층
		- 프레젠테이션 레이어
		- 서비스레이어
		- 도메인레이어
		- 퍼시스턴스 레이어

- 서비스 레이어의 역할
	- 애플리케이션 경계
	- 애플리케이션 로직
	- 도메인 로직의 재사용성 촉진
- Transaction을 시작하고 Commit/Rollback을 하는 주체가 된다.

- 트랜잭션 스트립트 일때는 ? 
	- 도메인 레이어가 각각의 단일 프로시저로 구현되기 때문에 별도의 서비스 레이어는 불필요하다.
![[레이어드.png]]


## 퍼시스턴스 레이어
- 트랜잭션 스크립트
	- 테이블 데이터 게이트웨이 (Gateway) : 단일 테이블 또는 뷰에 접속하는 SQL 처리
		- ex ) movieGateway.findWithTitle
	- 데이터 접속 객체 (DAO) : 하나의 데이블 또는 뷰에 대한 영속성 로직을 담당하는 객체, 테이블 데이터 게이트 웨이 패턴의 또 다른 이름 
		- ex ) movieDAO.findWithTitle

- 도메인 모델
	- 객체 모델과 DB 스키마 사이의 불일치 : 객체 패러다임과 관계 패러다임(관계형 DB)간의 불일치
		- 데이터 매퍼 (Data Mapper) : 객체 모델과 DB 스키마 간의 독립성 유지
		- 객체 관계 매핑 (Object-Relational Mapping : ORM) : 객체와 데이터베이스 테이블 사이의 매핑 기법 또는 매핑 도구 -> 일반적으로 재사용이 가능한 데이터 매퍼 프레임워크 사용
		- 레파지토리 (Repository) : 데이터 매퍼 용도로 사용되는 객체. 데이터 접근 객체의 개념 포함

## JPA
- 자바에서 사용되는 ORM
- 비슷한 형태의 객체와 테이블 변환 (객체지향 패러다임과 관계형 DB간의 불일치 해소)
- SQL 자동화 


# 트랜잭션 스트립트 패턴

- 영화 예매 시스템
	- 도메인 개념 : 영화, 상영, 할인 정책, 할인 조건, 할인 정책 + 할인 조건 (다중성), 예매, ![[예매 프로젝트ERD.png]]

- 절차적 : 프로세스의 흐름에 따라 필요한 데이터들을 가져와씀
- 데이터 구현
	- 어떤 데이터를 저장할 것인가
	- 데이터를 저장할 클래스 구현
		- 메서드를 통해 내부 캡슐화
		- 필드는 클래스 내부에서 캡슐화
		- 어떤 상황에서도 제한없이 접근할 수 있도록 모든 필드에 getter, setter 추가  (데이터 클래스 내부에서는 필드가 어떻게 사용될지 알수없으니 모두 열어두기)
- 프로세스(알고리즘) 구현
	- 어떻게 처리할 것인가
	- 데이터접근객체(DAO) : 영속성 저장소와의 상호 작용을 담당하는 객체
	- service 클래스 구현
		- service 클래스는 프로세스에 필요한 모든 도메인의 DAO를 필드로 갖는다.
		- service 클래스 안에 프로세스를 구현하기 위한 메소드 정의
		- 절차적인 구현
			1. 데이터베이스로 부터 영화, 상영, 할인 정책 관련데이터를 조회
			2. 해당 상영에 적용할 수 있는 할인정책이 있는지 판단
			3. 있을 경우 할인된 요금 계산
			4. 없을 경우 정가 계산
			5. 예약 도메인을 생성 후 데이터베이스에 저장
	- 중앙집중식 제어 스타일 

-> 트랜잭션 스크립트는 이 모든 논리를 주로 단일 프로시저로 구성. 공통적인 하위 작업은 더 작은 절차로 나뉠 수 있겠지만, 각 트랜잭션은 자신만의 트랜잭션 스트립트를 가지게 된다.
![[트랜잭션스트립트 패턴.png]]


# JPA 기본 매핑

- 영속성 컨텍스트 : 데이터베이스에서 조회했거나 저장할 객체들을 보관하는 곳
- 식별자 맵 : 식별자 키를 기준으로 영속성 컨텍스트에서 관리되는 엔티티들을 보관
- 식별자 필드 : @Id 필드
	- 식별자 선택시 고려사항
		- *자연키(비즈니스 의미를 가지는 식별자)* 와 *대리키(비즈니스 의미가 없는 식별자)* -> 식별자는 변경되지 말아야하기 때문에 <u>대리키를 선호</u> 
		- *단순키(하나의 컬럼의 식별자)* 와 *복합키(여러 컬럽의 조합 식별자)* -> 일관성과 불변성 측면에서 하나의 컬럼을 사용하는 <u>단순키 선호</u>
		- *생성키(identity, sequence, auto, table, guid)* 와 *할당키(사용자가 직접 할당 GUID, 자연키)* -> 단순성과 관리 관점에서 <u>생성키 선호</u>
-
### 생성키 전략
- AUTO : 데이터베이스에 적합한 최선의 전략 선택 (default)

- SEQUENCE : 데이터베이스 시퀀스 이용 
```java
@Entity
@SequenceGenerator(
name = "movie_sequence",
sequenceName = "movie_seq",
initialValue = 1, allocationSize = 50 // 서버에 캐시하는 단위
)
public class Movie {
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "movie_sequence")
private Long id;
}
```
- IDENTITY : 기본키 생성을 데이터베이스에 위임. insert 되는 시점에 순자적 숫자 값 생성
	- mysql auto_increment

- TABLE : 키 생성을 위한 테이블을 이요하여 데이터베이스 스퀀스 시뮬레이션
	- sequence_table을 별도의 트랜잭션에서 처리가 필요하기 떄문에 성능이슈이 있다
```java
@Entity
@TableGenerator(
name = ”movie_table",
table = "sequence_table",
pkColumnName = "seq_name", pkColumnValue = "movie_seq",
initialValue = 1, allocationSize = 20
)
public class Movie {
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "movie_table")
private Long id;
}
```


# 참조 객체와 값 객체

### 예시) 판매 현황 도메인
- Product 도메인
```java
public class Product {
	private String name;
	private long price;

	// 판매 가격을 계산하는 메소드
	public long salePrice(double discountRate) {
		return price - (long)Math.ceil(price * discountRate);
	}
}
```
- Sales 도메인
```java
public class Sales {
	private Product product;
	private int quantity;
	private long totalAmount;

	// 판매 누적 메소드
	public void addSale(int quantity, double discountRate) {
		this.quantity += quantity;
		this.totalAmount += product.salePrice(discountRate) * quantity;
	}

	// 순수익 계산 메소드
	public long profit() {
		return (long)Math.ceil(totalAmount * 0.03);
	}
}
```

> Sales 객체가 여러개 있을 경우, 매출 이익이 나눠서 저장되게 된다. -> 올바른 매출 이익을 참조하기 위해서는 매출을 하나의 객체로 통합해야함! 즉, 모든 클라이언트가 동일한 객체 참조하게 된다.

(-> 동일한 상태 공유로 인한 부수효과)

### 참조 객체
- 객체 식별자(메모리 주소)를 기반으로 동등성 체크 
- 협력하는 객체들 사이에 상태 변경을 공유하고 생명 주기를 함께 관리
- 일반적으로 가변 객체로 구현
- 별칭 버그 (Aliasing Bug)로 원치않는 부수효과
	- 별칭은 하나의 객체를 가리키는 여러개의 참조 변수(클라이언트1, 클라이언트2, ..)를 말한다. (같은 객체를 여러 변수에 할당함)
	- 여러 이름으로 존재하지만 같은 메모리 주소를 사용 -> 즉 상태를 공유하게 된다.
	- 그로 인한 원치않는 부수 효과가 발생할 수 있음

### 값 객체
- **값이 동일하면 동일한 객체로 취급** (equals and hashcode 메소드 오버라이딩)
- 불변 객체로 구현 
	- 생성자에서 대입 후 변경 불가능
	- 상태 변경이 필요하면 새로운 객체 생성 후 반환
	- 반대로 가변객체라면 속성에 계산 결과를 다시 대입, 객체의 상태 변경

> 값 객체가 중요한 이유 
> 	- 개념을 명시적으로 드러내서 복잡성을 감소
> 	- 암시적인 개념을 명시적으로 드러냄

as is
```java
public class Product {
	private String name;
	private long price;
	
	public long salePrice(double discountRate) {
		return price - (long)Math.ceil(price * discountRate);
	}
}
```

to be
```java
public class Product {
	private String name;
	private Money price;
	public Money salePrice(double discountRate) {
		return price.minus(price.ceil(discountRate));
	}
}
```
long 타입을 명시적인 Money 값 객체로 대체


### 중복제거
중복되는 계산 로직을 Money 객체로 이동
```java
@Value
public class Money {
	private final BigDecimal fee;
	
	public Money plus(Money amount) {
		return new Money(this.fee.add(amount.fee));
	}
	public Money ceil(double rate) {
		return Money.wons((long)Math.ceil(fee.longValue() * rate));
	}
}
```

-> 참조객체(Sales, Product)의 복합한 로직을 값 객체로 이동

Money는 Salse, Product와 합성 관계가 되며 해당 참조객체의 생명주기에 종속된다.


### 값객체 매핑 (포함 값 매핑)
```java
@Embeddable
@EqualsAndHashCode @ToString @AllArgsConstructor @NoArgsConstructor
public class Money {
	private BigDecimal fee; // final 제거
	
	public Money plus(Money amount) {
		return new Money(this.fee.add(amount.fee));
	}
	public Money ceil(double rate) {
		return Money.wons((long)Math.ceil(fee.longValue() * rate));
	}
}
```

```java
@Entity
public class Movie {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
	private String title;
	private Integer runningTime;
	
	@Embedded
	@AttributeOverride(name = ”fee", column = @Column(name=”MOVIE_FEE"))
	private Money fee; // money 객체를 MOVIE_FEE 컬럼으로 매핑
}
```

converter 등록
```java
@Converter(autoApply = true)
public class MoneyConverter implements AttributeConverter<Money, Long> {
	@Override
	public Long convertToDatabaseColumn(Money attribute) {
		return attribute.longValue();
	}
	
	@Override
	public Money convertToEntityAttribute(Long dbData) {
		return Money.wons(dbData);
	}
}
```

# JPA의 두 가지 관점
### 구조 관점
- 주키(PK)와 식별자 필드(id) 매핑
- 외래키와 객체 참조 매핑
- 컬렉션 매핑
	- 객체와 테이블 사이의 구조 반전
	- Movie가 Screening의 참조 컬렉션을 가짐 <-> SCREENING 테이블이 MOVIE 외래키를 저장
- 포함 값 매핑
- 상속 매핑
	- 단일 테이블 전략
	- 조인 전략
	- 구현 클래스마다 테이블 전략 (중복 필드 사용)
- 동일한 스키마를 다양한 방식의 클래스 구조와 매핑 가능
	- 단방향 참조
	- 양방향 참조

### 동작 관점
- 작업단위 : 비즈니스 트랜잭션의 영향을 받는 엔티티 목록을 유지 관리하고 변경 사항 기록 및 동시성 문제 해결 조정
- 식별자 맵 : 로드된 모든 객체를 Map에 유지하여 각 객체가 한번만 로드되도록 처리 (캐싱)
- 영속성 컨텍스트 : 작업단위 + 식별자맵 (영속성컨텍스는 하나의 작업단위 내에서 유효함)

- 쓰기 지연 : persist 실행 시점에 쿼리를 전송하지 않고 트랜잭션을 커밋할 때 모아둔 쿼리를 한번에 DB에 보냄
	- 쓰기 지연 SQL 저장소 : 영속성 컨텍스트의 수정사항을 DB에 동기화
	- ID 전략별 쓰기지연
		- SEQUENCE : persist 시점에 시퀀스 select 절 발생, flush 시점에 시퀀스 select와 insert문 일괄 실행?
		- IDENTITY : 채번을 위해 persist 시점에 insert 쿼리 실행

> 영속성 컨텍스트의 중요한 두가지 메커니즘
> 1. 도달 가능성에 의한 영속성
> 	- 영속 상태의 전파로 매핑된 도메인도 함께 DB에 반영됨
> 2. 지연로딩
> 	- 특정 도메인을 조회할때 매핑된 도메인을 Proxy로 가지고 있다가 접근할때 DB에서 로드해옴
> 	- 지연 로딩 시 영속성 컨텍스트가 존재하지 않을 경우(작업단위가 끝나 영속성컨텍스트가 존재하지 않음) LazyInitializationException 발생


# 쿼리 언어

### JPQL (Jakarta Persistence Query Language)
```java
Movie result =
	em.createQuery(
	"select m from Movie m where m.id = :movieId", Movie.class)
	  .setParameter("movieId", movie2.getId())
	  .getSingleResult();
```

쓰기 지연에 의해 영속성 컨텍스트와 DB 사이에 데이터의 차이가 있을 수 있다. JPQL 조회시 영속성 컨텍스트의 내용을 DB에 반영 후에 쿼리를 실행해야 한다.


### Repository 인터페이스
DAO를 Spring Data JPA Repository로 변경하기

쿼리 작성법
- 인터페이스 쿼리 메서드 선언 방식
- @Query 어노테이션 : 메서드 이름이 길어질 경우 직접 쿼리 명시\
	- Native Query
	- 쓰기 작업시 @Modifying


# 객체지향 설계
### 책임 주도 설계
1. 애플리케이션이 제공할 기능 파악
2. 애플리케이션의 기능 요구사항을 시스템의 책임으로 변환
3. 시스템의 책임을 객체의 책임으로 변환
4. 책임을 담당할 적절한 객체 선택
5. 객체의 책임 일부를 수행하기 위해 외부의 도움이 필요하다면 다른 객체에게 도움 요청
6. 이 요청을 또 다른 객체의 책임으로 변환
7. 책임을 담당할 적절한 객체 선택


- CRC 카드 : 책임과 협력을 표현하기 위한 객체지향 설계 도구
	- Candidate : 후보, 역할 또는 객체
	- Responsibility : 책임
	- Collaborator : 협력자

![[CRC카드.png]]


> 책임할당의 기본 원칙은 도메인 개념 중에 적절한 정보 전문가에게 할당

역할을 구현하는 클래스(Screening), 책임을 수행하는 메소드(reserve), 다른 클래스 인스턴스(movie)와의 협력 -> 책임을 위임한다.

- 위임식/ 분산식 제어 스타일

# 연관관계 설정

- 하나의 객체에서 다른 객체로 탐색할 수 있는 경로

**구조 관점**
- 외래키 매핑
	- 하나의 외래키를 두가지 방향으로 참조 매핑 할 수 있다.
- 다중성 방향
	- 다중성 : 일대일, 일대다, 다대일, 다대다
	- 방향 : 단방향, 양방향


- 다중성을 정의하는 어노테이션
	- @ManyToOne만 사용
		- 다대일 연관관계, 단방향 연관관계 선언
		- @JoinColumn으로 자신이 매핑되는 테이블의 FK 지정
	- @OneToMany만 사용
		- 해당 어노테이션을 이용해 단방향 연관관계 방향 바꾸기
		- @JoinColumn으로 상대 테이블의 FK를 지정
		- *FK 소유 여부에 따라 동작 방식이 달라진다. 객체 참조가 FK를 직접 관리하는지 여부에 따라 다른 방식으로 동작*
	- @ManyToOne와 @OneToMany 조합
		- 양방향 연관관께
		- DB 관점에서 두 객체 중에서 FK을 담당할 객체를 선택한다. 객체 참조는 두개지만 FK는 하나 -> 연관관계 주인을 선택
		- @JoinColumn은 연관관계의 주인쪽에 선언
		- 연관관계의 주인 반대쪽에는 mappedBy 선언 -> 데이터를 로드할때 JPA에게 두 객체의 싱크를 맞추도록 힌트를 주는 용도
			- @ManyToOne만 항상 FK를 포함하고 있기 때문에 mappedBy가 없다. @JoinColumn만 매핑함.
		- 객체 관점에서 두 객체 참조 싱크 맞추기를 고려해야한다. 
			- 연관관계 주인 쪽에 참조가 설정되지 않으면 테이블에 외래키가 저장되지 않다.
		- @JoinTable 어노테이션
	- 단방향 @OneToOne
		- 외래키를 보유한 연관관계 주인 쪽에 @OneToOne 매핑을 선언
		- 외래키가 없는 쪽에서는 단뱡향 @OneToOne 매핑이 불가능 -> 외래키가 없는 쪽에서 참조가 필요하다면 양방향 @OneToOne 매핑사용
	- 양방향 @OneToOne
		- FK를 포함하는 클래스에 @JoinColumn 지정
		- FK를 포함하지 않는 객체에 mappedBy 설정
	- 기본키 공유 방식의 @OneToOne 매핑
		- PK에 FK가 포함될때
		- @MapsId, @PrimaryKeyJoinColumn 사용

**동작 관점**
- 영속성 전이
- 로딩 (지연로딩, 즉시로딩)


# 영속성 전이
- 객체 단위 영속성 처리의 번거로움
- 객체를 저장/ 삭제/ 수정 할때 연관관게로 연결된 객체도 함께 저장/ 수정/ 삭제
- 도달 가능성에 의한 영속성
- FK의 소유 여부에 따라 동작 방식이 달라짐
	- FK를 알지 못하는 객체를 통해서 영속성 전이
		1. FK를 알지 못하니 개별적으로 insert 됨
		2. insert후 연관관계 주인 테이블의 FK가 update



