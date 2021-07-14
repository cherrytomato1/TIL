# Entity란

`Entity Class` **는 하나의 데이터베이스의 테이블과 매칭되는 클래스**로 볼 수 있습니다. 그리고 이 클래스로 작성된 객체를 `Entity Instance` 라고 부릅니다.

### XML 매핑

`Entity Class` 는 .xml 파일로 매핑 설정을 등록하여 사용할 수 있습니다. 

`entity-mappings` 태그 내부에 각 `entity` 클래스명에 맞는 속성과 테이블 명을 매칭시켜 작성합니다.

- xml 매핑 예제

`Member` 클래스가 존재하고 각 속성에 알맞은 이름이 지정되어 있다면 아래와 같이 작성할 수 있습니다.

```xml
<? xml version="1.0" encoding="UTF-8" ?>

<entity-mappings xmlns="http://java.sun.com/xml/ns/persistence/orm"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://java.sun.com/xml/ns/persistence/orm    
   http://java.sun.com/xml/ns/persistence/orm_1_0.xsd"
   version="1.0">
      
   <description> XML Mapping file</description>
      
   <entity class="Member">        
      <table name="Member"/>
      <attributes>
         <id name="id">
            <generated-value strategy="TABLE"/>
         </id>

         <basic name="name">
            <column name="NAME" length="100"/>
         </basic>
         
         <basic name="grade">
         </basic>
         
      </attributes>
   </entity>
   
</entity-mappings>
```

### @Entity 어노테이션

xml 파일로 매핑할 수 있지만 POJO 클래스에 `@Entity` 어노테이션을 작성하는 것 만으로도 JPA에 엔티티 클래스를 등록할 수 있습니다.

```java
@Entity
public class OrdinaryObjectClass{
	private long id;
//...
}
```

# EntityManager

JPA는 내부적으로 `EntityManager` 를 통해 각 `Entity instance` 를 관리합니다.

`EntityManager` 는 `DB connection pool` 으로 DB에 접근합니다. 이 때 각`EntityManager` 는 요청(각 `Transaction` 단위) 하나당 하나씩 생성되며 단 하나의 쓰레드에서 동작하며 트랜잭션이 끝나면 Connection을 반환하고 소멸합니다.

## EntityManagerFactory

매 트랜잭션 요청마다 `EntityManager` 를 생성하기 위해 `EntityManagerFactory` 가 존재합니다. 

```java
//EntityManagerFactory 인터페이스 내의 메서드
public EntityManager createEntityManager();
```

`EntityManagerFactory` 는 어플리케이션 로딩 시점에 DB마다 단 하나의 객체가 생성되며 종료 시에 `close` 합니다.

`close` 시 connection resource를 반환합니다.

![image](https://user-images.githubusercontent.com/49678555/125620736-fb6aed1b-b6c3-464e-b126-391a36c965bb.png)

## EntityTransaction

`EntityManager` 에서 데이터를 변경하는 모든 작업은 트랜잭션 내에서 이루어져야 합니다. 트랜잭션은 `EntityManager` 의 `getTransaction()` 메서드로 인스턴스르 받아올 수 있습니다.

```java
public EntityTransaction getTransaction();
```

`EntityTransaction` 객체에서 다음 메서드를 호출하여 트랜잭션을 제어합니다.

```java
//트랜잭션 시작
public void begin();
//트랜잭션 종료 및 쿼리 커밋
public void commit();
//트랜잭션 롤백
public void rollback();
```

**트랜잭션을 수행하기 때문에 JPA는 쓰기 지연을 지원합니다. 즉 커밋하기 이전까지 실제 쿼리는 작성만 되고 DB에 전송되지 않습니다.**

# Entity 어노테이션

이전 내용에서 JPA의`Entity Class`는 `@Entity` 어노테이션에 의해 테이블과 매핑될 수 있다고 작성했습니다.

그에 따라 `Entity Class` 내의 속성도 어노테이션을 작성하여 DB 테이블에 맞게 설정할 수 있습니다.

JPA에서 사용할 수 있는 기본적인 `Entity` 어노테이션은 다음과 같습니다.

- `@Entity`
    - 대상 클래스를 `Entity Class` 로 매핑
    - JPA 가 테이블과 1:1 매핑

- `@Table`
    - `Entity Class` 를 특정 테이블에 매핑
    - 클래스 명을 테이블명으로 하지 않고 임의의 테이블을 지정할 때 사용

- `@Column`
    - 컬럼에 매핑되는 속성의 옵션을 설정
    - 속성 명과 DB 컬럼명을 다르게 매핑하거나, 컬럼의 사이즈 및 제약조건을 설정하는데 사용

- `@id`
    - 대상 속성이 `PK` 임을 지정
    - **모든 `Entity Class` 내에는 `@id` 어노테이션을 통해 적어도 하나의 `PK` 를 지정해야함**

- `@GenerateValue`
    - 대상 속성(컬럼)의 값을 자동생성
    - `stragtegy` 옵션을 작성하여 생성방식 결정
        - `TABLE` : 별도의 키를 생성해주는 테이블 이용
        - `SEQUENCE` : 데이터베이스 시퀀스로 식별키 생성(오라클)
        - `IDENTITY` : 데이터베이스에 생성방식 위임
        - `AUTO` : 데이터베이스에 맞게 위 3가지 방식 자동 선택

    ```java
    // id 값을 PK로 지정 및 데이터 베이스 방식으로 자동생성
    @id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    ```

- `@Transient`
    - 어노테이션 대상 속성을 영속성 관리에서 제외
    - JPA 엔티티 속성이 아닌 로직을 위해 존재하는 속성에 사용

 *cf) 영속성 관련 내용은 다음 글에서 확인할 수 있습니다.*
 
 <br/>

> Hibernate에서 사용되는 Entity 어노테이션

[http://www.techferry.com/articles/hibernate-jpa-annotations.html](http://www.techferry.com/articles/hibernate-jpa-annotations.html)
