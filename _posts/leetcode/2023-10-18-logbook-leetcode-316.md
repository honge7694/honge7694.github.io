---
title: 중복문자 제거 - leetcode 316번
date: 2023-10-18 23:02:00 +09:00
categories: [DevOps, Algorithm]
tags: [항해99, TIL, 알고리즘, Stack, leetcode]
image: /assets/img/posts/알고리즘.png
---

## [316. 중복문자 제거(Remove Duplicate Letters)](https://leetcode.com/problems/remove-duplicate-letters/description/)

문자열 이 주어지면 s모든 문자가 한 번만 나타나도록 중복 문자를 제거한다. 
사전식 순서로 정리한다.
- 사전식 순서란?
    - 글자 그대로 사전에서 가장 먼저 찾을 수 있는 순서를 말한다.
    - 해당 문제에서는 중복값만 삭제할 수 있다. 따라서 중복값이 아닌 처음 등장하는 값은 삭제할 수 없다.

Example 1:
> Input: s = "bcabc"    
> Output: "abc"

Example 2:
> Input: s = "cbacdcbc"    
> Output: "acdb"
    

## 접근 방법
 나는 문제가 쉬운 문제인 줄 알았다. 아래와 같이 생각했는데 예제만 통과되고, 답이 아니었다. 
1. a부터 시작할 수 있도록 a를 먼저 찾는다.
2. a부터 뒤에 오는 값들을 차례로 담는다.
3. 대신 이미 담긴 값은 넣지 않는다.

```python
def removeDuplicateLetters(s: str) -> str:
    stack = []

    # 1. a부터 시작하는 s 찾기.
    for idx, char in enumerate(s):
        if not stack and char == 'a':
            s = s[idx:]
            break
    
    # 2. a부터 뒤에 오는 값들을 차례로 담는다.
    for char in s:
        # 3. 대신 이미 담긴 값은 넣지 않는다.
        if not char in stack:
            stack.append(char)

    return "".join(stack)

s = "bcabc"
s1 = "cbacdcbc"
print(removeDuplicateLetters(s1))
```


다시 문제를 천천히 보니 **해당 문제에서는 중복값만 삭제할 수 있다. 따라서 중복값이 아닌 처음 등장하는 값은 삭제할 수 없다.**
라는 조건이 있었다. 그래서 a를 찾는 것이 아니고, 스택에 차례대로 넣되, 중복값만 삭제하여 최대한 맨앞단어가 되게 하는것이였다.    
ex) "ecbacba" → "eacb"
     
그래서 생각해 낸 방법이 
1. 딕셔너리를 이용하여 각 단어의 카운트를 센다.
2. 카운트가 1인 값만 스택에 쌓는다.

```python
def removeDuplicateLetters(s: str) -> str:
    stack = []

    # 1. 딕셔너리를 이용하여 단어 카운트
    count = {}
    for char in s:
        if char in count:
            count[char] += 1
        else:
            count[char] = 1
    
    # 2. 카운트가 1인 값만 스택에 쌓기
    for char in s:
        if count[char] > 1:
            count[char] -= 1
            continue
        else:
            stack.append(char)

    return "".join(stack)
```

하지만 카운트가 1이 되는 순서가 다르기 때문에 이방법도 결국엔 실패했다.. 그래서 결국 참고 할 수 밖에 없었다.

##  풀이

```python
def removeDuplicateLetters2(s):
    counter, seen, stack = collections.Counter(s), set(), []

    for char in s :
        counter[char] -= 1
        # 이미 처리된 문자 제거
        if char in seen :
            continue

        # stack이 있고 글자가 top보다 앞자리이면서 top의 카운트가 0이상일 경우
        while stack and char < stack[-1] and counter[stack[-1]] > 0 :
            seen.remove(stack.pop())
        stack.append(char)
        seen.add(char)

    return "".join(stack)
```



## 알게 된 것
아직은 적용해보지는 못했지만 기술매니저님이 언제, 어떻게 사용하는지 알아두면 좋다고 했다.
     
### Collection

`collections` 모듈은 파이썬의 기본 모듈 중 하나로, 여러 유용한 데이터 구조와 자료형을 제공한다. 이 모듈은 내장 데이터 타입인 리스트, 튜플, 딕셔너리, 집합 등을 확장하고 보다 풍부한 기능을 제공하는데 사용된다.

#### Counter

+ `Counter` 클래스는 반복 가능한(iterable) 객체 내의 요소들의 갯수를 세어주는 컨테이너이다.
+ 가장 흔한 사용 사례는 리스트나 문자열 내에서 어떤 요소가 몇 번 등장했는지 계산하는 것이다.

#### defaultdict

+ `defaultdict` 클래스는 딕셔너리와 유사하지만, 딕셔너리의 키를 만들 때 초기값을 제공한다. 이것은 키가 존재하지 않을 때 기본값을 반환한다.

#### namedtuple

+ `namedtuple` 클래스는 튜플을 생성하는데 사용되며, 필드에 이름을 부여할 수 있다. 이를 통해 튜플의 각 요소에 이름을 붙여서 읽기 쉽게 만들 수 있다.

#### deque

+ `deque` 클래스는 양 끝에서 삽입과 삭제가 효율적으로 이루어지는 덱(Double-ended Queue)을 구현하는데 사용된다.
+ 리스트에 비해 덱은 양 끝에서의 삽입과 삭제가 빠르며, 큐와 스택을 함께 구현하는 데 유용하다.

#### OrderedDict

+ `OrderedDict` 클래스는 딕셔너리와 유사하지만, 항목들이 추가된 순서를 유지한다. 이는 파이썬 3.7 이전의 딕셔너리에서는 순서를 보존하지 않는 문제를 해결한다.

#### ChainMap

+ `ChainMap` 클래스는 여러 딕셔너리를 하나의 뷰로 결합할 수 있게 해준다. 이것은 여러 딕셔너리에서 키를 검색하는 데 유용하다.



## 참고
+ [벨로그](https://velog.io/@mmy789/Python-collections)
+ [공식문서](https://docs.python.org/ko/3/library/collections.html)