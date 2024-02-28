---
title: "[Spring] ConverterNotFoundException No converter found capable of converting from type "
date: 2024-02-27 18:00:00 +09:00
categories: [IT, Spring]
tags: [til, spring, trouble shooting, projection]
image: /assets/img/posts/trouble-shooting.png
---

## 에러 발생 상황
JPA를 활용한 nativeQuery로 DTO를 만들어 맵핑하는 과정에서 발생된 에러이다.    

<br/>

org.springframework.core.convert.ConverterNotFoundException No converter found capable of converting from type

<br/>

#### Repository
```java
@Query(value = "SELECT b.*, IFNULL(l.loan_status, '대출 가능') AS loan_status " +
"FROM books b " +
"LEFT JOIN ( " +
" SELECT book_id, IF(COUNT(*) > 0, '대출 중', '대출 가능') AS loan_status " +
" FROM loans " +
" WHERE return_date IS NULL " +
" GROUP BY book_id " +
") l ON b.book_id = l.book_id " +
"WHERE b.book_id = :bookId", nativeQuery = true)
BookLoanInfoDto findByBookWithLoanByBookId(@Param("bookId") Long bookId);
```

#### Dto

```java
@Getter
public class BookLoanInfoDto {
    private Long bookId;
    private String title;
    private String author;
    private String language;
    private String publisher;
    private LocalDateTime createdAt;
    private LocalDateTime modifiedAt;
    private String status;

    public BookLoanInfoDto(
            Long bookId,
            String title,
            String author,
            String language,
            String publisher,
            LocalDateTime createdAt,
            LocalDateTime modifiedAt,
            String status
    ) {
        this.bookId = bookId;
        this.title = title;
        this.author = author;
        this.language = language;
        this.publisher = publisher;
        this.createdAt = createdAt;
        this.modifiedAt = modifiedAt;
        this.status = status;
    }
}
```


## 해결 과정

### 필드명 변경
Entity에서 사용되는 칼럼명이 아닌 DB에서 사용되는 칼럼명으로 변경되어야 한다.

#### Dto 변경
```java
@Getter
public class BookLoanInfoDto {
    private Long book_id; // 변경
    private String title;
    private String author;
    private String language;
    private String publisher;
    private LocalDateTime created_at; // 변경
    private LocalDateTime modifie_at; // 변경
    private String loan_status;

    public BookLoanInfoDto(
            Long book_id, // 변경
            String title,
            String author,
            String language,
            String publisher,
            LocalDateTime created_at, // 변경
            LocalDateTime modifie_at, // 변경
            String loan_status // 변경
    ) {
        this.book_id = book_id; // 변경
        this.title = title;
        this.author = author;
        this.language = language;
        this.publisher = publisher;
        this.created_at = created_at;
        this.modified_at = modified_at;
        this.loan_status = loan_status;
    }
}
```

하지만 이렇게 변경하여도 여전히 같은 에러가 발생하였다.


## 해결

### interface 생성

#### BookDeatilView Interface 생성
```java
public interface BookDetailView {
    Long getbook_id();
    Long getuser_id();
    String getTitle();
    String getAuthor();
    String getLanguage();
    String getPublisher();
    LocalDateTime getCreated_at();
    LocalDateTime getModified_at();
    String getLoan_status();
}
```

#### Repository 변경
```java
@Query(value = "SELECT b.*, IFNULL(l.loan_status, '대출 가능') AS loan_status " +
"FROM books b " +
"LEFT JOIN ( " +
" SELECT book_id, IF(COUNT(*) > 0, '대출 중', '대출 가능') AS loan_status " +
" FROM loans " +
" WHERE return_date IS NULL " +
" GROUP BY book_id " +
") l ON b.book_id = l.book_id " +
"WHERE b.book_id = :bookId", nativeQuery = true)
BookDetailView findByBookWithLoanByBookId(@Param("bookId") Long bookId);
```


JPA에서 DB에 필요한 속성을 조회하는 것을 **`Projection`** 이라고 한다. `Projection`은 **Entity 기반**과 **DTO 기반**이 있는데, JPA에서 DTO 형식으로 조회 했기 때문에 `interface` 기반의 `Projection`을 사용해야 문제를 해결할 수 있다.    

DTO기반으로 할시 조회를 원하는 속성들의 집합으로 `get(필드명)`을 사용하면 호환이 가능하다.






