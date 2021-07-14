영속성 컨텍스트는 JPA에서 Entity를 관리하는 가장 중요한 개념입니다.

# 영속성 컨텍스트(Persistence Context)란

JPA 내부에서 `Entity` 를 영구 저장하는 환경이라는 의미이며 논리적인 개념입니다. 데이터 삽입 시 `Entity` 를 영속화한다고 볼 수 있습니다.

실제 JPA 구현시 `Entity` 를 영속성 컨텍스트에 저장함으로써 DB에 INSERT 합니다.

`EntityManager` 인스턴스 생성 시 1 : 1로 영속성 컨텍스트가 생성됩니다.

## 영속성 저장 조건

== **영속성로 저장할 수 있는 값은 식별자를 통한 쿼리만 해당합니다. ==**

즉 식별자는 `PK`  (`@id` 어노테이션 지정 속성)를 이용해서 저장되므로 `PK` 가 아닌 다른 값을 통해 쿼리를 수행한다면 저장되지 않습니다.

# Entity LifeCycle

각 엔티티는 영속성 컨텍스트와의 관계로 생명주기가 결정됩니다.

![image](https://user-images.githubusercontent.com/49678555/125620979-b2c38647-06d9-4724-a7be-f4cce0d1977c.png)

## 비영속 ( new )

엔티티 인스턴스가 생성되었지만 영속성 컨텍스트와는 관련이 없는 상태를 말합니다. 즉 엔티티 매니저 에 의해 관리되지 않는 POJO 객체를 말합니다.

```java
Member member = new Member();
```

## 영속 (managed)

엔티티 인스턴스가 `persist()` 메서드를 통해 엔티티 매니저의 영속성에 등록되거나, `find()` 메서드 혹은 QueryDSL 메서드 호출로 DB에서 반환된 경우 영속상태로 분류합니다. 영속 상태의 엔티티는 **1차 캐시에 식별자를 포함하여** 저장됩니다.

```java
//아래의 member 인스턴스는 영속상태로 관리됨

//persist
//INSERT, 영속 컨텍스트 등록
entityManager.persist(member);
//find
//SLELCT, DB에서 영속 컨텍스트에 등록됨
member = em.find(member.class, 1);
```

## 준영속 (detached)

엔티티 매니저의 영속성 컨텍스트에 존재했다가 분리된 상태입니다. 영속 컨텍스트에서 분리되었으므로 속성의 수정이 영속성 컨텍스트에 반영되지 않습니다.

비영속 상태와 비슷하지만 다음과 같은 차이점이 있습니다.

- 한 번 영속상태가 되었기 때문에 **식별자**가 존재
- 영속 해제된 상태이므로 `persist()` 로 영속성 컨텍스트 저장 불가능
    - `merge()` 메서드로 재저장(managed) 가능
    - **이 때 해당 객체가 영속성을 갖는게 아닌 영속성을 갖는 새로운 객체를 반환**
- 지연 로딩 불가능

```java
//member 객체의 영속성 해제
entityManager.detach(member);
//해당 매니저의 영속성 컨텍스트 내 모든 영속성 해제
entityManager.clear();

//member 객체의 속성으로 새로운 영속성 컨텍스트 내 엔티티를 만들어 반환
//반환 후 원래의 레퍼런스에 저장
member = entityManager.merge(member);

//영속성 컨텍스트 종료(제거)
entityManager.close();
```

## 삭제( removed)

영속성 컨테스트 내에서 해당 엔티티를 제거합니다. 따라서 DB 내에서도 해당 데이터가 삭제됩니다.

```java
//member 제거
entityManager.remove(member);
```

# 1차 캐시

영속성 컨텍스트 내에는 1차 캐시가 존재하여 **managed가 된 entity를 캐시에 저장**합니다. **저장된 엔티티는 각각의 고유한 식별자**를 갖고 있습니다.

## SELECT 쿼리 중복 수행 제한

영속성 컨텍스트 내에 엔티티가 저장된다면 각 엔티티들은 식별자를 갖고 있기 때문에 중복객체를 식별할 수 있습니다.

**즉 한번 `find()` 메서드등을 통해 1차 캐시에 저장되어 있다면, 같은 객체를 찾는 `find()` 에서는 쿼리를 수행하지않고 엔티티를 반환합니다.**

```java
//영속성 저장
entityManager.persist(member);
//쿼리를 수행하지 않고 1차 캐시에 저장된 엔티티반환
Member memberCopy = entityManager.find(Member.class, member.getId());

```

## 동일성 보장

영속성에 저장되어 있거나 저장되었던 엔티티들은 각각의 식별자로 동등성을 보장받습니다. **같은 엔티티는 중복되어 등록되지 않기 때문입니다.**

따라서 한번 같은 엔티티는 수행 쿼리문과 상관없이 동일성을 보장받습니다.

```java
Member member1 = entityManager.find(Member.class, 1);
Member member2 = entityManager.find(Member.class, 1);

//true, 동등성 뿐 아니라 동일성도 보장
System.out.println(member1 == member2);
```

# 트랜잭션을 이용한 쓰기 지연

앞선 글에서 JPA의 CRUD 메서드는 `EntityTransaction` 내에서만 수행되어야 한다고 작성했습니다. 

이 때 CRUD 메서드는 수행시 쿼리를 작성하고 바로 전송하는 것이 아닌 `EntityManager.flush()` 를 통해 수행됩니다.

생성된 모든 쿼리들은 쿼리 저장소에 저장되어 있게 되며 `flush()` 메서드를 통해 DB에 전송됩니다. 

## Flush

flush는 JPA의 영속성 컨텍스트를 DB와 동기화하는 작업을 말합니다.

앞서 작성된 내용처럼  `EntityManager.flush()` 가 수행되어야 쿼리 저장소에 있는 DB를 보냅니다.

이 때 **변경 감지**를 통해 영속상태의 엔티티 중 속성이 변경된 엔티티를 UPDATE 합니다

- flush 동작 순서
    1. **변경 감지**로 수정된 엔티티 UPDATE
    2. 쓰기 지연 쿼리 동기화

### 쓰기 지연 사용 이유?

쓰기 지연을 지원하여 쿼리를 한번에 보내는 것이 매 쿼리를 보내는 것보다 성능이 좋기 때문입니다.

## Commit

트랜잭션을 `begin()` 으로 수행한 뒤 `commit()` 하여 트랜잭션을 종료해야합니다. 커밋을 수행하게 되면 내부적으로 `EntityManager` 의 `flush()` 메서드를 호출한 후 트랜잭션을 닫습니다.

### Commit vs Flush

flush는 쿼리를 전송하는 역할이고 commit은 내부적으로 flush를 수행한 뒤 트랜잭션을 끝내는 역할입니다.

**즉 flush로 전송된 쿼리는 rollback할 수 있지만 commit은 트랜잭션을 끝내므로 rollback 할 수 없습니다.**

<br/>

 *cf) `@Transactional` 어노테이션*

# 변경 감지(dirty checking)

`EntityManager` 에는 INSERT, DELETE, SELECT 를 담당하는 메서드는 존재하지만 UPDATE에 관련된 메서드는 없습니다.

그 이유는 1차 캐시에 존재하는 엔티티(영속 상태)는 속성의 변경이 이루어지면 UPDATE를 `flush()` 시점에 수행하기 때문입니다.

## 스냅샷

변경 감지가 가능한 이유는 1차 캐시에 엔티티가 저장될 경우 그 상태를 복사해서 스냅샷으로 같이 저장하기 때문입니다. `flush()` 로 동기화 시킬 때 엔티티의 속성이 변경되었다면 UPDATE 쿼리를 수행합니다.

<br/>

> 참고

- [https://victorydntmd.tistory.com/207](https://victorydntmd.tistory.com/207)
- [https://jobc.tistory.com/209](https://jobc.tistory.com/209)
- [https://gmlwjd9405.github.io/2019/08/06/persistence-context.html](https://gmlwjd9405.github.io/2019/08/06/persistence-context.html)
- [https://ict-nroo.tistory.com/130](https://ict-nroo.tistory.com/130)
- [https://stackoverflow.com/questions/28392747/jpa-flush-vs-commit](https://stackoverflow.com/questions/28392747/jpa-flush-vs-commit)
