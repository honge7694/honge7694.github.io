---
title: 괄호 - baekjoon 9012번
date: 2023-10-18 23:04:00 +09:00
categories: [IT, Algorithm]
tags: [항해99, TIL, algorithm-1.png, Stack, for-else, baekjoon]
image: /assets/img/posts/algorithm-1.png
---

## [9012. 괄호](https://www.acmicpc.net/problem/9012)

N개의 입력으로 주어진 문자열이 VPS인지 아닌지를 판단하여 결과를 YES와 NO로 나타내야한다.
+ VPS: '( )'와 같이 한 쌍의 괄호로 이루어진 문자열


## 접근 방법
1. 열린 괄호는 스택에 쌓는다.
2. 닫힌 괄호가 나오면 스택에서 꺼낸다.
3. 스택이 비어있는데, 닫힌 괄호가 나오면 잘못된 VPS이다.

```python
N = int(input())

dataList = []
for _ in range(N):
    dataList.append(input())

for data in dataList:
    stack = []
    for char in data:
        # 1. 열린 괄호는 스택으로
        if char == "(":
            stack.append(char)
        elif char == ")":
            # 3. 잘못된 VPS
            if not stack:
                stack.append(char)
                break
            # 2. 옳바른 VPS
            pair = stack.pop()
            if char==')' and pair=='(':
                continue
    
    if not stack:
        print("YES")
    else:
        print("NO")
```

## 다른 풀이
스터디에서 발표해주신 팀원분이 `for - else`를 사용한 풀이가 있다고 말해주었다. `for-else`는 처음 들었는데 되게 유용한것 같았다.

```python
T = int(input())

for i in range(T):
    stack = []
    a=input()
    for j in a:
        if j == '(':
            stack.append(j)
        elif j == ')':
            if stack:
                stack.pop()
            else: # 스택에 괄호가 없을경우 NO
                print("NO")
                break
    else: # break문으로 끊기지 않고 수행됬을경우 수행한다
        if not stack: # break문으로 안끊기고 스택이 비어있다면 괄호가 다 맞는거다.
            print("YES")
        else: # break안 걸렸더라도 스택에 괄호가 들어있다면 NO이다.
            print("NO")

# 출처 https://wookcode.tistory.com/49
```



### for - else란?

`for - else` 구문은 루프가 완전히 종료되었을 때만 실행되며, 루프가 break 문을 통해 조기에 종료되지 않는 한 실행된다. 이것은 특정 조건이 충족되거나 루프에서 원하는 항목을 찾았을 때 추가 작업을 수행하려는 경우에 유용하다.

```python
fruits = ['apple', 'banana', 'date']

for fruit in fruits:
    if fruit == 'cherry':
        print('Cherry found!')
        break
else:
    print('Cherry not found.')
	
# 결과: Cherry not found.
```