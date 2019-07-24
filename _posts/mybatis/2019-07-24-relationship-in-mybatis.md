---
layout: post
current: post
navigation: True
title: MyBatis 1:1, 1:N 관계 매핑 (association, collection)
date: 2019-07-24 21:13
tags: [Mybatis]
class: post-template
subclass: 'post'
author: saturday
---

### 소개
MyBatis 에서 1:1, 1:N 관계를 매핑하기 위해서는 어떻게 해야할까?
1:1 관계 매핑을 위한 association, 1:N 관계 매핑을 위한 collection 에 대해 알아보자.

### 예제
게시판을 만든다고 가정하고 예제 코드를 작성해보았다.
(코드 라인 수를 줄이기 위해 import 구문은 생략)

### Account
`Account Table`
```sql
create table account (
    id primary key,
    name text
);
```

`Account.java`
```java
package com.exam.mybatis.domain;

@Getter
@Setter
@ToString
public class Account {
    private int id;
    private String name;
}
```

`AccountRepository.java`
```java
package com.exam.mybatis.repository;

@Repository
public interface AccountRepository {
    Board findOne(int id);
}
```

`AccountRepository.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.exam.mybatis.repository.AccountRepository">
    <select id="findOne" resultType="com.exam.mybatis.repository.Account">
        select * from account where id = #{id}
    </select>
</mapper>
```

### Board
`Board Table`
```sql
create table board (
    id primary key,
    title text,
    contents text,
    writer_id integer
);
```

`Board.java`
```java
package com.exam.mybatis.domain;

@Getter
@Setter
@ToString
public class Board {
    private int id;
    private String title;
    private String contents;
    private Account writer;
    private List<Comment> comments;
}
```

`BoardRepository.java`
```java
package com.exam.mybatis.repository;

@Repository
public interface BoardRepository {
    Board findOne(int id);
}
```

`BoardRepository.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.exam.mybatis.repository.BoardRepository">
    <select id="findOne" resultMap="Board">
        select * from board where id = #{id}
    </select>
    <resultMap id="Board" type="com.exam.mybatis.domain.Board">
        <association property="writer" column="writer_id" select="com.exam.mybatis.repository.AccountRepository.findOne" />
        <collection property="comments" column="id" select="com.exam.mybatis.repository.CommentRepository.findByOrderId" />
    </resultMap>
</mapper>
```

### Comment
`Comment Table`
```sql
create table comment (
    id primary key,
    board_id integer,
    contents text
);
```

`Comment.java`
```java
package com.exam.mybatis.domain;

@Getter
@Setter
@ToString
public class Comment {
    private int id;
    private int boardId;
    private String contents;
}
```

`CommentRepository.java`
```java
package com.exam.mybatis.repository;

@Repository
public interface CommentRepository {
    List<Comment> findByOrderId(int orderId);
}
```

`CommentRepository.xml`
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.exam.mybatis.repository.CommentRepository">
    <select id="findByOrderId" resultType="com.exam.mybatis.domain.Comment">
        select * from comment where board_id = #{boardId}
    </select>
</mapper>
```