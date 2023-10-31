---
title: 스택을 이용한 큐 구현 - leetcode 232번
date: 2023-10-19 23:02:00 +09:00
categories: [DevOps, Algorithm]
tags: [항해99, TIL, 알고리즘, Queue, Stack, leetcode]
image: /assets/img/posts/알고리즘.png
---

## [스택을 이용한 큐 구현](https://leetcode.com/problems/implement-queue-using-stacks/)

두 개의 스택만 사용하여 FIFO(선입선출) 대기열을 구현한다. 구현된 큐는 일반 큐( push, peek, pop및 empty)의 모든 기능을 지원해야 한다.
    
Example 1:
>**Input**    
["MyQueue", "push", "push", "peek", "pop", "empty"]    
[[], [1], [2], [], [], []]    
**Output**    
[null, null, null, 1, 1, false]    
**Explanation**    
MyQueue myQueue = new MyQueue();     
myQueue.push(1); // queue is: [1]    
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)    
myQueue.peek(); // return 1    
myQueue.pop(); // return 1, queue is [2]    
myQueue.empty(); // return false    


## 접근 방법
`deque`를 이용하면 매우 쉽게 될 것 같아서 `deque`로 진행해 보았더니 코드가 엄청 간결했다.

```python
class MyQueue:
    def __init__(self):
        self.stack = deque()
    def push(self, x):
        self.stack.appendleft(x)
    def pop(self):
        return self.stack.pop()
    def peek(self):
        return self.stack[-1]
    def empty(self):
        return len(self.stack) == 0
```

## 풀이
문제를 잘 못 읽었다.. 2개의 스택을 이용해야 하는데, 한개만 이용하여 풀었었다. 아래는 다시 제대로 푼 코드이다.
 
1. append
    + stack1을 메인으로하여, push에서 append를 받는다.
2. pop    
	+ pop을 할 때, stack1의 요소를 꺼내어, stack2에 넣어주면 먼저 들어간 것이 먼저 나오게되는 큐가 된다.
	+ stack2를 다른 곳에서도 쓰기 위해 다시 비어준다.
3. peek
    + peek 또한 pop과 마찬가지로 반복한다.

```python
class MyQueue:
    def __init__(self):
        self.stack1 = []
        self.stack2 = []

    def push(self, x: int) -> None:
        self.stack1.append(x)
        
    def pop(self) -> int:
        while self.stack1:
            self.stack2.append(self.stack1.pop())
        item = self.stack2.pop()
        
        while self.stack2:
            self.stack1.append(self.stack2.pop())
        return item
        
    def peek(self) -> int:
        while self.stack1:
            self.stack2.append(self.stack1.pop())
            
        item = self.stack2[-1]
        
        while self.stack2:
            self.stack1.append(self.stack2.pop())
        
        return item
        
    def empty(self) -> bool:
        if not self.stack1 and not self.stack2:
            return True
```