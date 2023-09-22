---
title: Django의 다양한 QuerySet
date: 2023-09-22 18:14:30 +09:00
categories: [DevOps, Django]
tags: [Django, QuerySet, filter]
image: /assets/img/posts/image-202309221824.png
---

Django의 QuerySet은 데이터베이스에서 데이터를 조회, 필터링, 정렬, 그룹화 및 조작하기 위한 파이썬 객체입니다. QuerySet은 Django ORM의 핵심 개념 중 하나로, 데이터베이스 테이블에 대한 SQL 쿼리를 생성하고 실행하는데 사용됩니다.

## filter
> 필드 조회는 SQL WHERE 절의 핵심을 지정하는 방법입니다.    이는 QuerySet 메소드 `filter()`, `exclude()` 및 `get()`에 대한 키워드 인수로 지정됩니다.

### exact, iexact - 일치
> `exact` : 정확히 일치하는 데이터를 찾습니다.
`iexact` : 대소문자를 구분하지 않고 일치하는 데이터를 찾습니다.
값이 `None`이면, SQL `Null`로 해석됩니다.

```python
Entry.objects.get(id__exact=14)
Entry.objects.get(id__exact=None)
Blog.objects.get(name__iexact="beatles blog")
Blog.objects.get(name__iexact=None)
```

```sql
SELECT ... WHERE id = 14;
SELECT ... WHERE id IS NULL;
SELECT ... WHERE name ILIKE 'beatles blog';
SELECT ... WHERE name IS NULL;
```

### contains, icontains - 포함
> `contains` : 포함하는 데이터를 찾습니다.   
`icontains` : 대소문자를 구분하지 않고 일치하는 데이터를 찾습니다.


```python
Entry.objects.get(headline__contains="Lennon")
Entry.objects.get(headline__icontains="Lennon")
```

```sql
SELECT ... WHERE headline LIKE '%Lennon%';
SELECT ... WHERE headline ILIKE '%Lennon%';
```


### in - 주어진 목록 중 하나와 일치
> `list`, `tuple`, `string` 또는 queryset과 같이 같이 반복가능한 객체를 대상으로 각 데이터를 조회합니다.

```python
Entry.objects.filter(id__in=[1, 3, 4])
Entry.objects.filter(headline__in="abc")
```

```sql
SELECT ... WHERE id IN (1, 3, 4);
SELECT ... WHERE headline IN ('a', 'b', 'c');
```

> 또는 다음과 같이 queryset을 직접 조건으로 넣을 수 있습니다.

```python
inner_qs = Blog.objects.filter(name__contains="Cheddar")
entries = Entry.objects.filter(blog__in=inner_qs)
```

```sql
SELECT ... WHERE blog.id IN (SELECT id FROM ... WHERE NAME LIKE '%Cheddar%')
```


### gt, gte, lt, lte - 대소 관계 비교
> `gt` : 비교대상보다 **초과**인 데이터를 조회합니다.   
`gte` : 비교대상보다 **이상**인 데이터를 조회합니다.    
`lt` : 비교대상보다 **미만**인 데이터를 조회합니다.   
`lte` : 비교대상보다 **이하**인 데이터를 조회합니다.   


```python
Entry.objects.filter(id__gt=4)
```

```sql
SELECT ... WHERE id > 4;
```


### startswith, istartswith, endswith, iendswith - 접두사, 접미사
> `startswith` : **특정 문자열로 시작**하는 데이터를 조회합니다. 대소문자를 구분합니다.   
`istartswith` :**특정 문자열로 시작**하는 데이터를 조회합니다.  
`endswith` : **특정 문자열로 끝**나는 데이터를 조회합니다. 대소문자를 구분합니다.   
`iendswith` : **특정 문자열로 끝**나는 데이터를 조회합니다. 

```python
Entry.objects.filter(headline__startswith='Lennon')
Entry.objects.filter(headline__istartswith="Lennon")
Entry.objects.filter(headline__endswith='Lennon')
Entry.objects.filter(headline__iendswith="Lennon")
```
  
```sql
SELECT ... WHERE headline LIKE 'Lennon%';
SELECT ... WHERE headline ILIKE 'Lennon%';
SELECT ... WHERE headline LIKE '%Lennon';
SELECT ... WHERE headline ILIKE '%Lennon';
```


### range - 범위
> 조건에 **포함**되는 범위의 데이터를 조회합니다.

```python
import datetime

start_date = datetime.date(2005, 1, 1)
end_date = datetime.date(2005, 3, 31)
Entry.objects.filter(pub_date__range=(start_date, end_date))
```

```sql
SELECT ... WHERE pub_date BETWEEN '2005-01-01' and '2005-03-31';
```


<br/>

## 참고
[공식문서](https://docs.djangoproject.com/en/4.2/ref/models/querysets/#field-lookups)