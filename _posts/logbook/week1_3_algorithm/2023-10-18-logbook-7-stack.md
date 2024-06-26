---
title: Stack
date: 2023-10-18 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [til, 자료구조, stack]
image: /assets/img/posts/til.png
---

## Stack
스택은 프로그래밍과 데이터 구조에서 중요한 개념 중 하나이다. 이 데이터 구조는 "Last-In, First-Out" (LIFO) 원칙을 따르며, 요소의 추가와 제거가 상단에서만 일어난다. 스택은 주로 함수 호출, 뒤로 가기 버튼의 동작, 깊이 우선 탐색 등 다양한 응용 분야에서 사용된다.

### 스택 추상 데이터 타입(Abstract Data Type) 

| 연산  | 기능  |
|---|---|
| init()  | 스택 초기화  |
| create()  | 스택 생성 (`__init__` 을 이용하는게 일반적이다)  |
| is_empty()  | 스택 비어있는지 확인  |
| is_full()  | 스택이 가득 찼는지 확인  |
| push(item)  | 스택의 맨 위에 요소 추가  |
| pop()  | 스택의 맨 위에 있는 요소를 제거 후 반환  |
| peek()  | 스택의 맨 위 요소를 삭제하지 않고 반환  |

### 스택의 연산 목록

| 연산  | 기능  |
|---|---|
| push()   | 스택에 데이터 삽입   |
| pop()  | 스택에서 데이터 삭제하여 반환  |
| is_empty()  | 스택에 원소가 없으면 `True`, 있으면 `False` 반환|
| peek()  | 스택 맨 위 데이터 반환  |


```python
class Node:
    def __init__(self, data, next_node=None):
        self.data = data
        self.next = next_node

class Stack:
    def __init__(self):
        self.top = None

    def is_empty(self):
        return self.top is None

    def push(self, item):
        new_node = Node(item)
        new_node.next = self.top
        self.top = new_node

    def pop(self):
        if not self.is_empty():
            popped_item = self.top.data
            self.top = self.top.next
            return popped_item

    def peek(self):
        if not self.is_empty():
            return self.top.data

```