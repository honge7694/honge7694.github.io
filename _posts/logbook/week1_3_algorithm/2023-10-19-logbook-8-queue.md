---
title: Queue
date: 2023-10-19 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [til, 자료구조, queue]
image: /assets/img/posts/til.png
---

## Queue
데이터 요소들이 선입선출(FIFO, First-In-First-Out)의 원칙에 따라 저장되고 관리되는 추상 데이터 구조이다. 큐는 현실에서 우리가 일상 생활에서 흔히 볼 수 있는 대기 줄과 유사한 개념으로 사용된다.

### 큐의 추상 데이터 타입(Abstract Data Type) 

| 연산  | 기능  |
|---|---|
| enque()   | 큐에 데이터 삽입   |
| deque()  | 큐의 맨 앞 데이터 삭제하여 반환  |
| is_empty()  | 큐에 원소가 없으면 `True`, 있으면 `False` 반환|
| peek()  | 큐의 맨 앞 데이터 반환  |
| size()  | 큐에 저장된 항목의 개수를 반환  |

### 큐 코드

```python
class Queue:
    def __init__(self):
        self.items = []

    def enqueue(self, item):
        # 큐의 뒤쪽(마지막)에 항목을 추가합니다.
        self.items.append(item)

    def dequeue(self):
        # 큐의 앞쪽(첫 번째)에서 항목을 제거하고 반환합니다.
        if not self.is_empty():
            return self.items.pop(0)

    def peek(self):
        # 큐의 앞쪽(첫 번째)에 있는 항목을 반환하지만 제거하지 않습니다.
        if not self.is_empty():
            return self.items[0]

    def is_empty(self):
        # 큐가 비어 있는지 여부를 확인하고 불리언 값을 반환합니다.
        return len(self.items) == 0

    def size(self):
        # 큐에 저장된 항목의 개수를 반환합니다.
        return len(self.items)
```

## 원형큐(Circular Queue)
선형 큐(Linear Queue)와 유사하지만, 일반적인 선형 큐의 문제 중 하나인 데이터의 이동(쉬프트) 작업을 최소화하는 큐 구조이다. 원형큐는 배열을 활용하여 데이터를 관리하며, 처음과 끝이 연결되어 원형으로 형성된다. 이렇게 하면 큐의 크기가 고정되어 있을 때 데이터를 효율적으로 관리할 수 있다.

### 주요 특징과 동작

#### Front와 Rear 포인터
원형큐는 두 개의 포인터, 즉 `front`와 `rear` 포인터를 사용한다.
`front`는 큐의 첫 번째 요소를 가리키고, `rear`는 마지막 요소를 가리킨다.

#### 공백 및 가득 참 상태
원형큐는 큐가 비어 있는 상태와 큐가 가득 찬 상태를 구별한다. 큐가 비어 있는 경우에는 `front`와 `rear`가 동일한 위치에 있으며, 큐가 가득 찬 경우에는 `front`와 `rear` 사이에 모든 요소가 채워진다.

#### 원형 데이터 저장
원형큐는 원형으로 데이터를 저장한다. 새로운 데이터가 추가되면 rear 포인터가 이동하며, 큐가 가득 찬 경우 rear는 다시 front 부근으로 돌아온다.

#### 더 효율적인 데이터 관리
원형큐는 선형 큐와 비교하여 데이터 이동(쉬프트) 작업을 최소화하므로 데이터를 효율적으로 관리할 수 있다.


### 원형큐의 추상 데이터 타입(Abstract Data Type) 

| 연산  | 기능  |
|---|---|
| enque()   | 큐의 마지막에 데이터 삽입   |
| deque()  | 큐의 맨 앞 데이터 삭제하여 반환  |
| is_empty()  | 큐에 원소가 없으면 `True`, 있으면 `False` 반환|
| peek()  | 큐의 맨 앞 데이터 반환  |
| is_full()  | 큐에 원소가 있으면 `True`, 없으면 `False` 반환  |

### 원형큐 코드

```python
class CircularQueue:
    def __init__(self, size):
        self.size = size
        self.queue = [None] * size
        self.front = self.rear = -1

    def is_empty(self):
        return self.front == self.rear == -1

    def is_full(self):
        return (self.rear + 1) % self.size == self.front

    def enqueue(self, item):
        if self.is_full():
            print("Queue is full.")
            return
        if self.is_empty():
            self.front = self.rear = 0
        else:
            self.rear = (self.rear + 1) % self.size
        self.queue[self.rear] = item

    def dequeue(self):
        if self.is_empty():
            print("Queue is empty.")
            return
        removed_item = self.queue[self.front]
        if self.front == self.rear:
            self.front = self.rear = -1
        else:
            self.front = (self.front + 1) % self.size
        return removed_item

    def peek(self):
        if self.is_empty():
            print("Queue is empty.")
            return
        return self.queue[self.front]
```

#### front와 rear에 0이 아닌 -1을 주는 이유?
원형큐의 개념은 잘 몰라서 이해하는데 한참걸렸다. `front`와 `rear`에 -1을 주는 이유는 다음과 같은 상황을 나타낼 수 있다고 한다.
1. 큐가 비어 있는 경우
	+ `front`와 `rear`가 모두 -1로 설정되어 있다.
2. 큐에 하나의 요소가 있는 경우
	+ `front`와 `rear`가 모두 0으로 설정된다.

즉, 초기값으로 **-1을 사용하면 큐가 비어있거나 초기화된 상태를 쉽게 인식**할 수 있는 장점이 있다. 그리고 `front`와 `rear`가 같은 값인 경우, 큐에 하나의 요소만 있는 상태를 나타내며, 이것은 큐가 비어 있는지 확인하는 데 유용한 방법 중 하나이다.

#### enquue 메서드의 self.is_empty()는 왜 0으로 초기화 하나?
큐가 비어 있는 경우에 -1로 초기화 하는데 왜 enque 메서드의 self.is_empty()를 통과하면 0으로 초기화 했는지 궁금했다.    
    
이는 처음에 큐가 비어 있는 상태에서 요소를 추가할 때, `front`와 `rear`를 모두 0으로 설정하면 큐가 비어 있는 상태에서 첫 번째 요소를 추가하는 것을 나타낸다고 한다. 이렇게 초기화하면 **큐가 비어 있을 때와 큐에 요소가 하나 있는 경우를 구분**할 수 있는 장점이 있다고한다.

#### 나의 이해

1. is_empty()는 -1이 있는지 확인한다.
2. is_full()은 `rear`+1을 나눈 크기의 나머지가 `front`와 같다면 `true`
3. enque()는 `fornt`와 `rear`가 비어있으면  0으로 초기화하거나, `rear`+1을 나눈 크기의 나머지를 `rear`에 대힙하면서 `rear` 포인터 증가시킨 후 `rear`에 값을 넣는다.
4. deque()는 `fornt`와 `rear`가 같다면 -1로 초기화하여, 비어있는 상태로 만들거나, `front`+1을 나눈 크기의 나머지를 `front`에 대힙하면서 `front` 포인터 증가시킨 후 `front`에 값을 넣는다.
5. peek()은 `front`위치에 있는 data를 가져온다.

## Queue 공식?

```python
def bfs(height, width):
    dheight = [-1, 1, 0, 0, -1, -1, 1, 1] 
    dwidth = [0, 0, -1, 1, -1, 1, -1, 1]

    queue = deque() # queue 초기화
    queue.append((height, width)) # queue 초기값 넣기

    while queue: # queue 반복
        y, x = queue.popleft() # queue.popleft()
        for i in range(8):
            ny = y + dheight[i] 
            nx = x + dwidth[i]

            if ny < 0 or ny >= h or nx < 0 or nx >= w:
                continue

            if graph[ny][nx] == 1:
                graph[ny][nx] = 0
                queue.append((ny,nx))

    return depth
```
위의 문제는 [섬의 개수](https://www.acmicpc.net/problem/4963)를 `bfs`로 푼 문제이다.

    
`bfs`에서 Queue를 많이 이용하는데, Queue를 이용할 때 만드는 공식같은 것이 눈에보여서 작성한다.

1. queue 초기화    
`queue= deque((height, width))`로 자주한다.
2. queue가 존재하면 반복     
3. while문 다음 또는 반복문 for문 안에서 `popleft()`를 한다.    
`popleft()`를 while문에서 하는 문제가 있고, for문 안에서 하는 문제가 있다. 잘 구분해서 풀어보도록 하자.
4. for문 안에는 문제 풀이를 작성하면 되는데, 문제의 핵심을 잘 찾아야 풀 수 있다.

