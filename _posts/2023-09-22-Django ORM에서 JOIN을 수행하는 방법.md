---
title: Django ORM에서 Join을 수행하는 방법
date: 2023-09-21 21:24:30 +09:00
categories: [DevOps, Django]
tags: [Django, ORM, JOIN]
image: /assets/img/posts/image-202309220048.png
---

Django ORM에서 `Join`을 수행하려면 `select_related()`또는 `prefetch_related()`메서드를 사용하여 `Join`을 할 수 있습니다.

<br/>

## Model 정의
```python
from django.db import models

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    
class Comment(models.Model):
    post = models.ForeignKey(BlogPost, on_delete=models.CASCADE)
    text = models.TextField()
```

<br/>

## Inner Join - select_related()
>`select_related()`는 **1:1**, **1:N의 관계에서 N이 사용**할 수 있습니다.   
**정방향 참조**에서의 `Join`에 유리하게 사용됩니다.   
`Join`을 통해 데이터를 즉시 가져오는 방법 **(SQL 단계에서 `Join`)**   
 `Inner Join`은 연결된 모든 데이터를 가져옵니다.    
게시물에 댓글이 하나도 없으면 해당 게시물은 결과에 나타나지 않습니다.   


### serializer에서 작성한 Inner Join
```python
# serializers.py
from rest_framework import serializers
from .models import BlogPost, Comment


class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['id', 'post', 'text']
```


### view에서 작성한 Inner Join
```python
# views.py
from rest_framework.generics import ListCreateAPIView
from .models import BlogPost, Comment
from .serializers import BlogPostSerializer, CommentSerializer


class CommentListAPIView(ListCreateAPIView):
    queryset = Comment.objects.select_related('post').all()
    serializer_class = CommentSerializer
```


### Result

```sql
SELECT "practice_comment"."id",
       "practice_comment"."post_id",
       "practice_comment"."text",
       "practice_blogpost"."id",
       "practice_blogpost"."title",
       "practice_blogpost"."content"
  FROM "practice_comment"
 INNER JOIN "practice_blogpost"
    ON ("practice_comment"."post_id" = "practice_blogpost"."id")
```

```
HTTP 200 OK
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

[
    {
        "id": 1,
        "post": 1,
        "text": "첫번째 게시글의 댓글 1번"
    },
    {
        "id": 2,
        "post": 1,
        "text": "첫번째 게시글의 댓글 2번"
    }
]
```

<br/>

## Left Outer Join - prefetch_related()
> `prefetch_related()`는 **1:N의 관계에서 1이 사용**, **M:N의 관계에서 사용**할 수 있습니다.   
**역방향 참조**에 유리하게 사용됩니다.   
추가 쿼리를 통해 데이터를 즉시 가져오는 방법입니다. **(추가 쿼리 발생, `Join`은 파이썬 level에서 이루어집니다.)**   
`Left Outer Join`은 왼쪽(기본)테이블의 모든 데이터를 가져오며, 오른쪽(연결된)테이블과 
관련된 데이터가 없는 경우에도 왼쪽 테이블의 데이터 결과에 포함시킵니다.  
 따라서 게시물에 댓글이 없어도 게시물은 결과에 나타납니다.

### serializer에서 작성한 Left Outer Join
```python
# serializers.py
from rest_framework import serializers
from .models import BlogPost, Comment


class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['id', 'post', 'text']


class BlogPostSerializer(serializers.ModelSerializer):
    comments = CommentSerializer(many=True, read_only=True, source='comment_set') 
    
    class Meta:
        model = BlogPost
        fields = ['id', 'title', 'content', 'comments']
```

`Inner Join`에서 예시로 보여준 코드에서 `source='comment_set'`을 추가하여 `Left Outer Join`과 유사한 형태로 결과를 나타냅니다. `BlogPost`와 연결된 모든 댓글을 가져오며, 만약 게시물에 연관 된 댓글이 없다면 빈 리스트가 표시됩니다. 

<details>
<summary>serializers.SerializerMethodField()을 이용하기</summary>

```python
from rest_framework import serializers
from .models import BlogPost, Comment

class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ['id', 'post', 'text']

class BlogPostSerializer(serializers.ModelSerializer):
    # SerializerMethodField를 사용하여 comments 필드를 추가
    comments = serializers.SerializerMethodField()

    class Meta:
        model = BlogPost
        fields = ['id', 'title', 'content', 'comments']

    def get_comments(self, obj):
        # BlogPost와 연관된 댓글들을 가져옵니다. (Left Outer Join)
        comments = Comment.objects.filter(post=obj)
        return CommentSerializer(comments, many=True).data
```

</details>

### view에서 작성한 Left Outer Join
```python
# views.py
from rest_framework.generics import ListCreateAPIView
from .models import BlogPost, Comment
from .serializers import BlogPostSerializer, CommentSerializer


class BlogPostListAPIView(ListCreateAPIView):
    queryset = BlogPost.objects.prefetch_related('comment_set').all()
    serializer_class = BlogPostSerializer
```

✔ 이러한 동작은 Left Outer Join과 유사하지만 Left Outer Join과는 약간 다를 수 있습니다.

### Result
```sql
SELECT "practice_comment"."id",
       "practice_comment"."post_id",
       "practice_comment"."text"
  FROM "practice_comment"
 WHERE "practice_comment"."post_id" = '1'
 
 SELECT "practice_comment"."id",
       "practice_comment"."post_id",
       "practice_comment"."text"
  FROM "practice_comment"
 WHERE "practice_comment"."post_id" = '2'
```

```
HTTP 200 OK
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

[
    {
        "id": 1,
        "title": "첫번째 게시글",
        "content": "첫번째 게시글 내용 입니다.",
        "comments": [
            {
                "id": 1,
                "post": 1,
                "text": "첫번째 게시글의 댓글 1번"
            },
            {
                "id": 2,
                "post": 1,
                "text": "첫번째 게시글의 댓글 2번"
            }
        ]
    },
    {
        "id": 2,
        "title": "두번째 게시글",
        "content": "두번째 게시글 내용입니다.",
        "comments": []
    }
]
```


<br/>

## Self Join - __ (더블언더바를 활용)

+ 작성 예정



<br/>

## 참고

[Django select_related()와 prefetch_related()](https://leffept.tistory.com/312)