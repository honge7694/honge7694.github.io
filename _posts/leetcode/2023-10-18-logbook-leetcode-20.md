---
title: 유효한 괄호 - leetcode 20번
date: 2023-10-18 23:01:00 +09:00
categories: [DevOps, Algorithm]
tags: [항해99, TIL, 알고리즘, Stack, leetcode]
image: /assets/img/posts/알고리즘.png
---

## 20. 유효한 괄호 (Valid Parentheses)
[리트코드 20번](https://leetcode.com/problems/valid-parentheses/)

'(', ')', '{', '}', '[' 및 ']' 문자만 포함하는 문자열 s가 주어지면 입력 문자열이 유효한지 확인한다.     
    
다음과 같은 경우 입력 문자열이 유효하다. 
1. 열린 괄호는 동일한 유형의 괄호로 닫혀야 한다. 
2. 열린 괄호는 올바른 순서로 닫혀야 한다. 
3. 모든 닫는 괄호에는 동일한 유형의 해당 열린 괄호가 있다.

Example 1:
> Input: s = "()"    
> Output: true

Example 2:
> Input: s = "()[]{}"    
> Output: true

Example 3:
> Input: s = "(]"    
> Output: false


## 접근 방법

1. 여는 괄호들만 스택에 넣어본다.
2. 닫히는 괄호일 때  `pop()`을 하여 괄호를 비교해본다.
3. 올바른 괄호가 완성되면 `continue`를 하며 진행한다.
4. 스택이 남아있거나, 올바르게 완성되지 않으면 `False`를 반환한다.

```python
def isValid(s: str) -> bool:
    stack = []
    
    for char in s:
        if char in "({[":
            stack.append(char)
        elif char in "]})":
            if not stack:
                return False
            top = stack.pop()

            if top == '(' and char == ')':
                continue
            elif top == '{' and char == '}':
                continue
            elif top == '[' and char == ']':
                continue
            else:
                return False
        
    return not stack

s = "()"
s2 = "()[]{}"
s3 = "(]"
print(isValid(s3))
```

생각했던 접근 방법대로 했더니 문제는 풀렸다. 다만, 강의에서 본 코드는 좀 더 깔끔하게 짜는 것을 볼 수 있었다.    
    
아래는 강의에서 본 코드이다.

```python
def is_valid_parenthesis(s):
    pair = {
        '}': '{',
        ')': '(',
        ']': '[',
    }
    opener = "({["
    stack = []

    for char in s:
        if char in opener:
            stack.append(char)
        else:
            if not stack:
                return False
            top = stack.pop()
            if pair[char] != top:
                return False

    return not stack
```

딕셔너리를 이용하여 올바른 괄호끼리 묶어주었다. 나와 다른 점은 괄호를 비교하는 때 인데, 딕셔너리를 이용하여 비교해주니 좀 더 깔끔한 코드를 짤 수 있었다.