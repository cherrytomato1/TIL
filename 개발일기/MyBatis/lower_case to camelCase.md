
# Issue
DB의 컬럼은 lower_case로 작성하고 Java의 Object 속성은 camelCase로 작성했을 때 자동으로 매핑하지 않는 이슈

# 쿼리문 Alias 설정

쿼리문에 직접 Alias를 설정하여 원하는 이름으로 받아오기

```sql
select user_id as userId
from user
```

# 설정파일 수정

 mybatis 설정파일로 카멜케이스 매퍼 설정을 `true` 로 넣어준다

1. mybatis-config.xml 생성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "<http://mybatis.org/dtd/mybatis-3-config.dtd>">
<configuration>
  <settings>
    <!-- 자동으로 카멜케이스 규칙으로 변환 -->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
  </settings>

</configuration>

```

파일 생성 후 다음과 같은 설정을 주입

1. application.properties 설정

```
mybatis.config-location=classpath:/config/mybatis-config.xml
```

위와 같이 설정을 주입시킨 후 경로를 알맞게 지정

⇒ [https://m.blog.naver.com/PostView.naver?blogId=dudgns0612&logNo=221019428810&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.naver?blogId=dudgns0612&logNo=221019428810&proxyReferer=https:%2F%2Fwww.google.com%2F)