---
title: 큐를 이용한 스택 구현 - leetcode 225번
date: 2023-10-19 23:01:00 +09:00
categories: [IT, Algorithm]
tags: [항해99, TIL, algorithm.png, Queue, Stack, leetcode]
image: /assets/img/posts/algorithm.png.png
---

## [225. 큐를 이용한 스택 구현](https://leetcode.com/problems/implement-stack-using-queues/description/)

두 개의 대기열만 사용하여 LIFO(후입선출) 스택을 구현한다. 
+ 후속 조치: 하나의 대기열만 사용하여 스택을 구현할 수 있습니까?

MyStack 클래스 구현.
+ void push(int x)요소 x를 스택의 맨 위로 푸시한다.
+ int pop()스택 맨 위에 있는 요소를 제거하고 반환한다.
+ int top()스택의 맨 위에 있는 요소를 반환한다.
+ boolean empty()true스택이 비어 있으면 반환하고 , false그렇지 않으면 반환한다.


Example 1:
> **Input**     
["MyStack", "push", "push", "top", "pop", "empty"]     
[[], [1], [2], [], [], []]      
> **Output**
[null, null, null, 2, 2, false]
> **Explanation**     
MyStack myStack = new MyStack();     
myStack.push(1);     
myStack.push(2);     
myStack.top(); // return 2     
myStack.pop(); // return 2     
myStack.empty(); // return False     

## 접근 방법
`deque`로 푸니까 너무 쉽게 풀렸다!

```python
class MyStack:
    def __init__(self):
        self.que = deque()

    def push(self, x: int) -> None:
        self.que.appendleft(x)

    def pop(self) -> int:
        return self.que.popleft()

    def top(self) -> int:
        return self.que[0]

    def empty(self) -> bool:
        return len(self.que) == 0
```