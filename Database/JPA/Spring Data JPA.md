# Spring Data JPA

Spring Data JPA는 JPA의 상속 인터페이스로  Spring Framework에서 JPA의 사용을 용이하게 하도록 하는 모듈입니다. JPA의 스펙을 사용하면서 그 내부적인 동작을 단순화합니다.

![image](https://user-images.githubusercontent.com/49678555/125771008-59dee9d3-90e7-48c6-ae66-39c896c900ec.png)

# JPA와 차이

JPA에서는 엔티티와 엔티티 매니저를 통해서 트랜잭션을 관리하고 CRUD 메서드를 수행해야합니다. 하지만 이러한 동작을 Repository에 정의하여 개발자가 직접 엔티티 매니저를 제어하지 않아도 됩니다.

또한 매니저를 제어하기 위한 DAO 클래스의 구현체를 작성하지 않아도 되는 장점이 있습니다. 

- SimpleJpaRepository

`SimpleJpaRepository` 클래스는 `JpaRepository` 의 구현체로 내부적으로 `EntityManager` 를 통한 JPA 동작을 수행한다는 것을 알 수 있습니다.

# JpaRepository

**JPA를 상속한 `JpaRepository` 인터페이스를 제공하며 해당 인터페이스를 상속 혹은 구현하는 것 만으로 기본 CRUD 메서드가 제공됩니다.**

`JpaRepository` 를 상속/구현한 클래스를 Spring Data에서 감지하여 구현체를 만들어 `Bean` 에 등록합니다. 이는 `JpaRepository` 내에 `@Repository` 어노테이션이 존재하기 때문입니다.

```java
public interface MemberRepository extends JpaRepository<Member, Long>{
}
```

위 코드를 작성하여 CRUD 기능을 사용할 수 있습니다. 이 때 제네릭 타입은 엔티티 클래스와 엔티티 클래스의 `PK` 타입이 입력되어야합니다.

## 기본 메서드 쿼리

`JpaRepository` 에서 CRUD를 수행하기 위한 기본 메서드가 제공됩니다. 이는 `JpaRepository` 가 상속하는 `CrudRepository` 에 작성되어 있습니다.

- CrudRepository

```java
/*
 * Entity 타입, PK(식별자) 타입을 제네릭으로 선언
 * 각 아규먼트가 null 일경우 IllegalArgumentException을 던짐
*/
@NoRepositoryBean
public interface CrudRepository<T, ID> extends Repository<T, ID> {

	/**
		엔티티 객체 혹은 자식객체를 영속(INSERT)하고 해당 엔티티 반환
	 */
	<S extends T> S save(S entity);

	/**
		엔티티 타입의 이터러블( ex) Collection) 을 모두 영속하고 엔티티 이터러블 반환
	 */
	<S extends T> Iterable<S> saveAll(Iterable<S> entities);

	/**
		식별자로 영속객체를 찾아서 Optional에 담아 엔티티 반환
		찾지 못했을 경우 비어있는 Optional 반환
	 */
	Optional<T> findById(ID id);

	/**
		해당 PK를 갖고 있는 엔티티의 존재 여부 반환
	 */
	boolean existsById(ID id);

	/**
		영속상태의 모든 엔티티 반환
	 */
	Iterable<T> findAll();

	/**
		식별자들을 포함한 이터러블을 매개로 엔티티 이터러블을 반환
	 */
	Iterable<T> findAllById(Iterable<ID> ids);

	/**
		등록된 엔티티 수 반환
	 */
	long count();

	/**
		해당 식별자 엔티티를 영속에서 제거
	 */
	void deleteById(ID id);

	/**
		해당 엔티티를 영속에서 제거
	 */
	void delete(T entity);

	/**
		엔티티 이터러블 내 모든 엔티티를 제거
	 */
	void deleteAll(Iterable<? extends T> entities);

	/**
	 모든 엔티티 제거
	 */
	void deleteAll();
}
```

## 자동 메서드 네임 쿼리

위와 같이 기본적으로 구현되는 쿼리 메서드를 사용할 수도 있지만 인터페이스 내에 메서드 네임 규칙을 따르는 메서드를 만들면 구현체 없이 CRUD 쿼리를 만들 수 있습니다.

```java
public interface MemberRepository extends JpaRepository<Member, Long>{
	Optional<Member> findByEmailAndName(String email, String name);
}
```

### 메서드 네임 규칙

[Spring Data Jpa 공식문서]([https://docs.spring.io/spring-data/jpa/docs/1.4.1.RELEASE/reference/html/jpa.repositories.html](https://docs.spring.io/spring-data/jpa/docs/1.4.1.RELEASE/reference/html/jpa.repositories.html))에서 메서드 명 규칙을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/49678555/125771086-8ac62734-20dd-4249-8553-404379c762f9.png)

## @Query 어노테이션

위와 같이 메서드 명명 규칙을 이용해서 쿼리 메서드를 JPA가 생성하도록할 수도 있지만 `@Query` 어노테이션으로 수동 쿼리를 입력해줄 수 있습니다.

이때 쿼리문에 작성하는 쿼리는 **JPQL** 로 SQL을 작성하기 위해서는 `nativeQuery = true` 옵션을 사용해야합니다.

## JPQL

간단하게 JPQL로 쿼리문을 작성하는 법을 알아보겠습니다. 기본적인 문법은 SQL과 같습니다.

### 컬럼 매핑

- `?1`
    - PreparedStatement에서 사용한 것과 같이 파라미터의 순서대로 매핑
    - `@Query("SELECT Member FROM member where name = ?1"`
- 파라미터 매핑
    - 파라미터에 `@Param` 어노테이션을 작성하여 `:param` 과 같은 형태로 매핑
    - `@Query("SELECT Member FROM member where name = :name"`

```java
public interface MemberRepository extends JpaRepository<Member, Long>{
	@Query("SELECT Member FROM member where name = :name")
	Optional<Member> findByName(@Param("name") String name);
}
```

### 엔티티 매핑

- `#{#enitityName}`
    - 엔티티 명을 매핑해서 조회 대상 엔티티의 타입을 자동으로 설정

```java
public interface MemberRepository extends JpaRepository<Member, Long>{
	@Query("SELECT #{#entityName} FROM member where name = :name")
	Optional<Member> findByName(@Param("name") String name);
}
```

<br/>

> **Reference** 
> 
> [https://goodgid.github.io/Spring-Data-JPA-Query_Part_2/](https://goodgid.github.io/Spring-Data-JPA-Query_Part_2/) 
>
>  [https://docs.spring.io/spring-data/jpa/docs/1.4.1.RELEASE/reference/html/jpa.repositories.html](https://docs.spring.io/spring-data/jpa/docs/1.4.1.RELEASE/reference/html/jpa.repositories.html)
>
> [https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa](https://www.baeldung.com/the-persistence-layer-with-spring-data-jpa)
