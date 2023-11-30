---
title: 카드2 - baekjoon 2164번
date: 2023-10-19 23:04:00 +09:00
categories: [IT, Algorithm]
tags: [항해99, TIL, algorithm.png, Queue, baekjoon]
image: /assets/img/posts/algorithm.png.png
---

## [2164. 카드2](https://www.acmicpc.net/problem/2164)

N이 주어졌을 때, 제일 위에 있는 카드를 바닥에 버린다. 그 다음, 제일 위에 있는 카드를 제일 아래에 있는 카드 밑으로 옮기는 과정을 반복하여 제일 마지막에 남게 되는 카드를 구하는 프로그램을 작성한다.

## 접근 방법
1. 제일 위에 있는 카드를 버린다.
2. 1.을 시행했으면 제일 위 카드를 아래로 옮긴다.
3. 카드가 1장 남을 때까지 반복한다.
위에 처럼 풀면 정말 간단하게 풀릴 줄 알았는데, 시간제한에 자꾸 걸렸다.. 쉬운 문제인줄 알았으나 그게 아니였다..

```python
N = int(input())
cards = [ num for num in range(1, N+1) ]

while len(cards) > 1:
    cards.pop(0) # 1.
    card = cards.pop(0)
    cards.append(card) # 2.

print(''.join(map(str, cards)))
```

## 풀이
어제 정리했던 파이썬 내장 라이브러리인 `collections.deque`를 이용하면 풀 수 있던 간단한 문제이다. 역시 `collections` 사용법을 얼른 숙지해야겠다..

```python
from collections import deque

def card_queue(num):
    cards = deque([num for num in range(1, num+1)])

    while len(cards) > 1:
        cards.popleft()
        cards.append(cards.popleft())
    return cards.popleft()

N = int(input())
print(card_queue(N))
```