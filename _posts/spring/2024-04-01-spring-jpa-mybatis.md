---
title: "Spring Boot MyBatis와 JPA"
date: 2024-03-31 18:00:00 +09:00
categories: [IT, Spring]
tags: [til, spring boot, mybatis]
image: /assets/img/posts/spring.png
---

그동안 스프링을 이용한 개발을 할 때 JPA로 개발을 해왔다. 회사에서 MyBatis를 사용하게 될 것 같아 MyBatis에 대해 알아보고 차이를 작성해보자.

# JPA와 MyBatis
백엔드에서 데이터베이스를 사용하는 프레임워크로 가장 많이 쓰이는 기술이 `MyBatis`와 `JPA`이다.     
데이터베이스 접속을 편하게 사용하기 위해 SQL Mapper 기술과 ORM 기술을 제공한다. 둘 다 DB와의 연동, 저장을 위한 기술이다. 

+ SQL Mapper
	+ **개발자가 작성한 SQL 실행 결과를 객체에 매핑**해주는 프레임워크
	+ 이것이 `MyBatis`이다.
+ ORM
	+ 객체와 DB의 데이터를 **자동으로 매핑** 시켜주는 프레임워크
	+ 이것이 `JPA(Java Persistence Api)`이다.

## MyBatis

### Java 코드와 SQL 매핑
MyBatis를 사용하면, MyBatis 내부에서 Boilerplate 코드가 구현되어 있고, MyBatis에서 Java 메소드와 SQL 간에 매핑을 시켜서 **개발자는 Java 메소드 선언과 SQL 문만 만들면 MyBatis가 자동으로 둘을 연결시켜 준다.**

> **Boilerplate란?**    
> 컴퓨터 프로그래밍에서 보일러플레이트 또는 보일러플레이트 코드라고 부르는 것은 최소한의 변경으로 여러곳에서 재사용되며, 반복적으로 비슷한 형태를 띄는 코드를 말한다.
{: .prompt-info }

### 동적 SQL 생성 기능
동적으로 SQL 문이 생성된다는 의미는 검색 조건같이 사용자가 입력하는 값에 따라 서로 다른 SQL 문장이 생성되어 실행된다는 의미이다. MyBatis 내에 if 문, choose, when, otherwise, foreach 등의 문법을 지원해서 동적인 SQL 문 생성이 가능하다. 
    
작성자만 입력하여 검색 및 제목을 입력하여 검색 예시
+ `SELECT * FROM board WHERE author='파라미터';`
+ `SELECT * FROM WHERE title like '%파라미터';`

### 장점

#### SQL 직접 제어
MyBatis는 SQL에 대한 직접적인 제어를 제공하므로, 개발자가 SQL을 직접 작성하고 최적화 할수 있다. 또한 복잡한 쿼리 또는 특정 데이터베이스에 최적화된 쿼리 작성이 필요한 경우에 적합하다.

#### 학습 용이성
SQL을 잘 아는 경우에, JPA에 비해 학습이 용이하고 쉽게 사용이 가능하다. SQL을 직접 제어할 수 있다는 것은 DB에 적합한 성능 좋은 SQL문을 만들어서 사용할 수도 있고, 또, 통계/분석용 데이터 추출을 위해 복잡한 쿼리를 만들어야 하는 경우에 자유롭게 테이블 간 JOIN 등을 통해서 원하는 데이터를 추출할 수 있어서 좋다.

### 단점

#### CRUD 단순 작업에 반복 수작업 필요
SQL을 직접 만들어야 하므로 CRUD 같은 기본적인 코드에 대해서 단순한 코드를 계속 만들어야하는 불편함이 있다. 단순 코드를 반복하여 만들면 개발 생산성이 떨어지고 유지보수가 힘들다.

#### 데이터베이스에 종속적
특정 DB를 기준으로 SQL 문이 작성되어 있어서, DB를 변경하려면 SQL 문을 전체 다 확인 후 수정해야한다.

<br/>

## JPA
MyBatis는 개발자가 작성한 SQL 문을 Java 객체로 자동으로 매핑 시켜주는  프레임워크이다.    
JPA는 이보다 한발 더 나아가서 SQL 문까지 자동으로 생성해 주고, DB 데이터와 Java 객체를 매핑 시켜주는 기술이다. 

### 특징
MyBatis와 다르게 SQL 문의 작성이 불필요하며 ORM 내부적으로 java 메소드에 적합한 SQL 문이 자동으로 생성이 되어 실행되게 된다. Java 개발자는 클래스만 만들어서 사용하고, ORM 프레임워크가 자동으로 관련된 SQL을 만들어 준다.     

즉, Java 개발자는 jpa.persist(member)라고 사용하면, Insert SQL 문이 자동으로 생성되어 DB에 저장이 된다.


### 장점

#### 표준화 된 인터페이스
JPA는 자바에서 ORM을 위한 표준 인터페이스를 제공한다. Hibernate, EclipseLink, Apache OpnJPA 등의 구현체가 JPA 표준을 따른다. Java의 표준을 이용하므로 특정 제품에 종속되지 않고, 개발자 간에 협업이 용이하고, 일관된 방식으로 개발이 가능하다.

#### 자동 매핑으로 개발 및 유지보수성 향상
JPA는 객체와 데이터비스 간의 자동 매핑을 지원한다. 개발자가 별도의 SQL 작성 없이도 데이터베이스와 객체간의 매핑이 이루어진다.

#### 객체 지향적인 접근 지원
JPA는 객체 지향적인 개발에 중점을 둔다. 테이블 간의 연관 관계와 객체 간의 관계를 쉽게 다룰 수 있다.

#### DBMS에 독립적
사용하는 DB 종류에 관계없이 JPA에서 자동으로 적합한 SQL Dialect(방언)을 만들어 주기때문에, DB가 변경되어도 SQL문을 다시 작성할 필요가 없다. DB 마이그레이션을 해야 할 경우에 MyBatis로 개발된 시스템과 비교하면 쉽게 설정 변경만으로 DB 변경이 가능하다.

### 단점

### 높은 학습곡선
JPA 관련 다양한 스펙과 작성법 (@Entitity, @Table, @Column, @Id, @OneToMany, @ManyToOne)을 학습해야 하고, 또 JPA 적용으로 생기는 다양한 이슈, 즉시로딩(EAGER LOADING), 지연 로딩(LAZY LOADING), 영속성 전이(CacadeType), 복합키 매핑(@EmbededId, @IdClass) 등에 대한 해결 방법을 익혀야 한다.

#### 복잡한 SQL 생성의 어려움
일반적으로 시스템을 개발하면 단순한 CRUD와 같은 기능도 많이 있지만, 통계 또는 분석과 같은 화면과 기능도 개발이 필요하다. 이때는 복잡한 쿼리를 만들어야 하는데 여러 테이블을 Join해서 데이터 결과를 가져아야 하는 경우에 JPA로는 복잡한 쿼리를 만드는데 용이하지않다.

#### 성능에 대한 고려 필요
 JPA에 의해 자동으로 SQL이 만들어지다 보니, DB의 특성(index, join 등)을 이해하고 DB에 맞는 SQL을 직접 튜닝해서 만들면 성능이 훨씬 뛰어날 수 있으나, 자동화된 SQL 문으로 인해서 데이터 조회 성능이 떨어질 가능성이 있다.

일반적인 간단한 CRUD에는 큰 문제가 없으나, 데이터가 많아지고, 테이블 간 Join이 많아지는 경우, SQL 문을 어떻게 튜닝하는 가로 인한 성능이 크게 차이가 날 여지가 있으므로, JPA 사용 시 이러한 부분을 주의해서 고려할 필요가 있다.

<br/>

## MyBatis 사용해보기

### 방법 1
 
 #### mybatis-config.xml
 
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias type="com.mybatis.board.entity.Board" alias="board"> // (1)</typeAlias>
    </typeAliases>
</configuration>
```
 
 (1) : `type="com.mybatis.board.entity.Board"`에 있는 dto를 alias를 이용해 board라는 별칭으로 사용하겠다고 한 것이다. 앞으로 이 board는 `board-mapper.xml`에서 정의 한 `parameterType`에서 사용된다.
 
 #### board-mapper.xml
 
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="Board"> // (1)
    <insert id="save" parameterType="board"> // (2)
        insert into board_table(boardTitle, boardWriter, boardPass, boardContents)
            values(#{boardTitle}, #{boardWriter}, #{boardPass}, #{boardContents})
    </insert>
</mapper>
```

(1) : Board는 Repository의 `sql.insert()`에서 사용된다.
(2) 
+ `id="save"`는 Repository에서 정의된 메서드의 이름을 의미한다. 
+ `parameterType="board"`는 `mybatis-config.xml`에 정의된 `alias`를 의미한다. 이를 다르게 사용하려면 `com.mybatis.board.entity.Board` 경로를 모두 입력해야한다.
        

#### BoardRepository

```java
@Repository
@RequiredArgsConstructor
public class BoardRepository {
    private final SqlSessionTemplate sql;

    public void save(BoardDto boardDto) {
        sql.insert("Board.save", boardDto); // (1)
    }
}
```

(1) : 위의 `board-mapper.xml`에서 정의된 `Board`와 `save`를 이용한다.

#### Service

```java
@Service
@RequiredArgsConstructor
public class BoardService {
    private final BoardRepository boardRepository;

    public void save(BoardDto boardDto) {
        boardRepository.save(boardDto); // (1)
    }
}
```


<br/>

### 방법 2

#### mybatis-config.xml
방법1과 같다.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias type="com.mybatis.board.entity.Board" alias="board"></typeAlias>
    </typeAliases>
</configuration>
```

#### board-mapper.xml

```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mybatis.board.repository.BoardMapper"> // (1)
    <insert id="save" parameterType="board">
        insert into board_table(boardTitle, boardWriter, boardPass, boardContents)
            values(#{boardTitle}, #{boardWriter}, #{boardPass}, #{boardContents})
    </insert>
</mapper>
```

(1) : 방법 1에서 사용하던 `namesapce="Board"`가 전체 경로를 나타나게 바뀌었다.

#### BoardMapper

```
@Mapper // (1)
public interface BoardMapper {
    void save(BoardDto boardDto); // (2)
}
```

(1) :  `@Mapper` 어노테이션을 사용하여 MyBatis가 해당 인터페이스에 대한 구현체를 동적으로 생성하고, SQL 쿼리와 메서드 호출을 매핑하여 데이터베이스와 상호작용할 수 있다.
(2) : board-mapper.xml의 (1)에서 연결하여 sql을 따로 연결하지 않아도 된다.

#### Service

```
@Service
@RequiredArgsConstructor
public class BoardService {
    private final BoardMapper boardMapper;
    public void save(BoardDto boardDto) {
        boardMapper.save(boardDto);
    }
}
```

<br/>


> 방법 2를 사용하는 것이 좀 더 Repository를 깔끔하게 개발하는 것 같다.
{: .prompt-info }


## 참고

+ [elancer 블로그](https://www.elancer.co.kr/blog/view?seq=231)


