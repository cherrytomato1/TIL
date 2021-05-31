# Foreign key..

-   컬럼 modify

```sql
alter table interested_area
modify column dongcode
varchar(30) collate 'utf8_bin' null ;
```

-   테이블 상태 보기

```sql
show table STATUS like '<table_name_here>'
```

## 제약조건

-   fk 키 추가

```sql
ALTER TABLE 테이블이름

ADD [CONSTRAINT 제약조건이름]

FOREIGN KEY (필드이름)

REFERENCES 테이블이름 (필드이름)

//

alter
table	interested_area
add		constraint	interested_user_id
foreign key	(user_id)
references	user	(user_id)
on		delete	cascade;
```

-   ON DELETE CASCADE
    -   외래 키에서 참조하는 키가 포함된 행을 삭제하려고 하면 해당 외래 키가 포함되어 있는 모든 행도 삭제
-   ON UPDATE CASCADE

    -   외래 키에서 참조하는 키 값이 포함된 행에서 키 값을 업데이트 하면 해당 외래 키를 구성하는 모든 값도 키에 지정된 새 값으로 업데이트되도록 지정

-   fk키 확인

```sql
select *
from information_schema.table_constraints
where table_name = '테이블명';

```

-   fk키 제거

```sql
alter
table	interested_area
drop
foreign key	interested_area_userid_fk;
```

### 에러

`columns participating in a foreign key are renamed`

-   fk를 해제하지 않고 pk를 변경 혹은 삭제하려는 경우
-   fk를 먼저 해제

`referencing column and referenced column are incompatible`

[https://stackoverflow.com/questions/21526055/mysql-cannot-create-foreign-key-constraint](https://stackoverflow.com/questions/21526055/mysql-cannot-create-foreign-key-constraint)`

-   참조키가 unique 하지 않음
-   두 컬럼의 데이터 타입이 다름
-   두 컬럼의 collate가 다름
