---
title: 항해99 일지(6) - LinkedList
date: 2023-10-17 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [항해99, til, 자료구조, linkedlist]
image: /assets/img/posts/logbook99.png
---

LinkedList의 종류는 단일 연결 리스트, 이중 연결 리스트말고도 있지만 오늘은 단일 연결 리스트와 이중 연결 리스트부터 정리하려고 한다. 이론을 습득하고, 전체 코드를 연습한 뒤, 다양한 삽입, 삭제 코드를 직접 짜보면 이해가 쉽게된다.


# LinkedList
링크드 리스트(linked list)는 데이터 요소의 집합을 표현하는 데이터 구조이다. 각 데이터 요소는 노드(node)라고 불리며, 각 노드는 다음 노드의 참조(링크)를 포함하고 있다. 링크드 리스트는 데이터 요소를 메모리에 효율적으로 저장하고, **데이터 요소의 삽입 및 삭제를 효율적으로 수행**할 수 있도록 도와준다.

## 단일 링크드 리스트 (Singly Linked List)
각 노드가 데이터 요소와 다음 노드를 가리키는 링크를 가지는 형태이다.

```python
class Node:
    def __init__(self, val, next):
        self.val = val
        self.next = next


class LinkedList:
    def __init__(self):
        self.head = None

    def __str__(self):
        curr = self.head
        result = []

        while curr:
            result.append(curr.val)
            curr = curr.next
        
        return " -> ".join(map(str, result))
		
    # 연결 리스트에 새로운 노드를 추가하는 메서드
    def append(self, val):
        if not self.head:
            self.head = Node(val, None)
        else:
            curr = self.head

            while curr.next:
                curr = curr.next
            
            curr.next = Node(val, None)

    # 연결 리스트에서 특정 값을 삭제하는 메서드
    def delete(self, val):
        curr = self.head

        if not curr:
            return

        if curr.val == val:
            curr = curr.next
            return
        
        while curr.next:
            if curr.next.val == val:
                curr.next = curr.next.next
                return
            curr = curr.next
    
    # 연결리스트를 거꾸로 출력하는 메서드
    def reverseList(self):
        curr = self.head
        prev = None

        while curr != None:
            temp = curr.next
            curr.next = prev
            prev = curr
            curr = temp
        
        self.head = prev
		
	# 연결 리스트를 출력하는 메서드
    def display(self):
        current = self.head
        while current:
            print(current.val, end=" -> ")
            current = current.next
        print("None")



# 연결 리스트 생성
l1 = LinkedList()

# 노드 추가
list1 = [1, 2, 3, 4, 5]
for element in list1:
    l1.append(element)
l1.append(6)
print(l1)
# 결과 : 1 -> 2 -> 3 -> 4 -> 5 -> 6

# 노드 삭제
l1.delete(3)
print(l1)
# 결과 : 1 -> 2 -> 4 -> 5 -> 6

# 노드 거꾸로 출력
l1.reverseList()
print(l1)
# 결과 : 6 -> 5 -> 4 -> 2 -> 1


# 연결 리스트 출력
l1.display()
# 결과 : 6 -> 5 -> 4 -> 2 -> 1 -> None
```

### 노드 삽입
#### 연결 리스트 꼬리에 삽입
연결 리스트의 마지막에 새로운 노드를 추가한다. 이 연산은 기존의 노드를 순회하며 마지막 노드를 찾아야 하므로 보통 O(n)의 시간이 걸린다.
```python
# 연결리스트 끝에 새로운 노드 추가
def append(self, val):
    # Node가 없을 경우
    if not self.head:
        self.head = curr(val, None)
    else:
        curr = self.head

        # 마지막 노드에 추가하기 위해 끝으로 이동
        while curr.next:
            curr = curr.next
        
        # 노드 추가
        curr.next = Node(val, None)
```

#### 연결 리스트 중간에 삽입
연결 리스트의 중간에 새로운 노드를 추가한다. 이때 특정 노드 뒤에 새로운 노드를 찾는 경우, 해당 노드를 찾아야 한다. 일반적으로 O(n)의 시간이 걸린다.
```python
# 특정 노드 값 뒤에 새로운 노드 추가 (중간에 추가)
def insert_after(self, node_val, new_val):
    curr = self.head

    while curr:
        if curr.val == node_val:
            new_node = Node(new_val, curr.next)
            curr.next = new_node
            return
        curr = curr.next


# 노드 추가
l1.insert_after(6, 7)
print(l1) # 결과 : 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7
```

#### 연결 리스트 머리에 삽입
연결 리스트의 가장 앞쪽에 새로운 노드를 추가한다. 이 연산은 상수 시간복잡도 O(1)을 갖는다.
```python
def insert_at_head(self, val):
    new_node = Node(val, None)
    new_node.next = self.head
    self.head = new_node


# 노드 추가
l1.insert_at_head(0)
print(l1) # 결과 : 0 -> 1 -> 2 -> 3 -> 4 -> 5
```


### 노드 삭제
#### 연결 리스트 꼬리 삭제
연결 리스트의 마지막 노드를 삭제한다. 이 연산은 리스트를 순회하여 마지막 노드를 찾아야 하므로 일반적으로 O(n) 시간이 걸린다.
```python
def delete_at_tail(self):
    curr = self.head
    
    while curr.next.next:
        curr = curr.next
    
    curr.next = None
		
# 노드 삭제
l1.delete_at_tail()
print(l1) # 결과 : 1 -> 2 -> 3 -> 4
```

#### 연결 리스트 중간 삭제
연결 리스트의 중간에 있는 특정 노드를 삭제한다. 이때 해당 노드를 찾아야 하며, 삭제 후 앞 노드의 링크를 변경해야 한다. 일반적으로 O(n) 시간이 걸린다.
```python
def delete(self, val):
    curr = self.head
    
    # node가 없으면 return
    if not curr:
        return

    # curr가 self.head.next를 가리키게하여 self.head를 삭제
    if curr.val == val:
        curr = curr.next
        return
    
    # LinkedList를 조회하며 값 비교
    while curr.next:
        if curr.next.val == val:
            curr.next = curr.next.next
            return
        curr = curr.next

```

#### 연결 리스트 머리 삭제
연결 리스트의 맨 앞 노드를 삭제한다. 이 연산은 상수 시간복잡도 O(1)을 갖는다.
```python
    def delete_at_head(self):
        curr = self.head
        self.head = curr.next
		
# 노드 삭제
l1.delete_at_head()
print(l1) # 결과 : 2 -> 3 -> 4 -> 5
```


### 노드 조회
노드 조회는 2가지 방법을 사용했다. `__str__`을 이용하는 것과 함수를 이용하여 출력하는 방법이다.
```python
def __str__(self):
    curr = self.head
    result = []

    while curr:
        result.append(curr.val)
        curr = curr.next
    
    return " -> ".join(map(str, result))
```

    

```python
def display(self):
    current = self.head
    while current:
        print(current.val, end=" -> ")
        current = current.next
    print("None")
```



## 이중 링크드 리스트 (Doubly Linked List)
 노드가 데이터 요소와 이전 노드, 다음 노드를 가리키는 두 개의 링크를 가지는 형태이다. 이로 인해 앞뒤로 이동 및 데이터 요소 삽입/삭제가 보다 쉽다.
 
 ```python
class Node:
    def __init__(self, data):
        self.data = data
        self.prev = None
        self.next = None

class DoublyLinkedList:
    def __init__(self):
        self.head = None

    def __str__(self):
        curr = self.head
        
        result = []
        while curr != None:
            result.append(curr.data)
            curr = curr.next
        
        return " <-> ".join(map(str, result))

    # 이중 연결 리스트에 새로운 노드를 추가하는 메서드
    def append(self, data):
        new_node = Node(data)
        if self.head is None:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node
            new_node.prev = current

    # 이중 연결 리스트에서 특정 값을 삭제하는 메서드
    def delete(self, data):
        current = self.head

        while current:
            if current.data == data:
                if current.prev:
                    current.prev.next = current.next
                if current.next:
                    current.next.prev = current.prev
                if current == self.head:
                    self.head = current.next
                return
            current = current.next

    # 이중 연결 리스트를 출력하는 메서드
    def display(self):
        current = self.head
        while current:
            print(current.data, end=" <-> ")
            current = current.next
        print("None")


# Doubly Linked List 생성
dll = DoublyLinkedList()

# 노드 추가
list1 = [1, 2, 3, 4, 5]
for element in list1:
    dll.append(element)
dll.append(6)
print(dll) 
# 결과 : 1 <-> 2 <-> 3 <-> 4 <-> 5 <-> 6

# 양방향 연결 리스트 출력
dll.display()
# 결과 : 1 <-> 2 <-> 3 <-> 4 <-> 5 <-> 6 <-> None

# 양방향 연결 리스트 특정 값 삭제
dll.delete(3)
print(dll)
# 결과 : 1 <-> 2 <-> 4 <-> 5 <-> 6
```


### 노드 삽입

#### 이중 연결 리스트 꼬리에 삽입
연결 리스트의 맨 끝에 새로운 노드를 추가한다. 이것은 기존 꼬리 노드의 다음 노드와 새로운 노드 사이의 연결을 업데이트한다. O(n)의 시간 복잡도를 갖고 있다.

 ```python		
# 이중 연결 리스트에 새로운 노드를 추가하는 메서드
def append(self, data):
    new_node = Node(data)
    if self.head is None:
        self.head = new_node
    else:
        current = self.head
        while current.next:
            current = current.next
        current.next = new_node
        new_node.prev = current
```

#### 이중 연결 리스트 중간에 삽입
지정된 위치에 새로운 노드를 추가한다. 이전 노드와 다음 노드 사이에 새로운 노드를 삽입한다. 삽입 위치를 찾기 위해 O(n)의 시간복잡도를 갖고있다.
```
# 특정 값 다음에 새로운 노드를 추가하는 메서드
def insert_after(self, node_val, new_val):
    curr = self.head

    while curr:
        if curr.data == node_val:
            new_node = Node(new_val)
            new_node.prev = curr
            new_node.next = curr.next
            curr.next = new_node
            return
        curr = curr.next

# 노드 추가
dll.insert_after(3, 7)
print(dll) # 결과 : 1 <-> 2 <-> 3 <-> 7 <-> 4 <-> 5 <-> 6
```
 
#### 이중 연결 리스트 머리에 삽입
연결 리스트의 맨 앞에 새로운 노드를 추가한다. 이것은 기존 머리 노드의 다음 노드와 새로운 노드 사이의 연결을 업데이트한다. O(1)의 시간 복잡도를 갖고 있다.
```python
def insert_at_head(self, val):
    new_node = Node(val)

    if self.head:
        new_node.next = self.head
        self.head.prev = new_node
    self.head = new_node

# 노드 삽입
dll.insert_at_head(0)
print(dll) #
```

### 노드 삭제
#### 이중 연결 리스트 꼬리 삭제
연결 리스트의 꼬리 노드를 제거한다. O(n)의 시간복잡도를 갖는다.
```python
def delete_at_tail(self):
    if not self.head:
        print("연결 리스트 비어있음.")
        return

    if not self.head.next:
        # 리스트에 하나의 노드만 남았을 경우
        self.head = None
    else:
        current = self.head
        while current.next:
            current = current.next

    current.prev.next = None
```

#### 이중 연결 리스트 중간 삭제
지정된 값과 일치하는 첫 번째 노드를 삭제한다. 값을 찾기 위해 리스트를 순회 하므로 O(n)의 시간복잡도를 갖고있다.
```python
def delete(self, data):
    current = self.head

    while current:
        if current.data == data:
            if current.prev:
                current.prev.next = current.next
            if current.next:
                current.next.prev = current.prev
            if current == self.head:
                self.head = current.next
            return
        current = current.next

# 노드 삭제
dll.delete(2)
print(dll) # 결과 : 1 <-> 3 <-> 4 <-> 5 
```

#### 이중 연결 리스트 머리 삭제
연결 리스트의 머리 노드를 제거한다. O(1)의 시간복잡도를 갖는다.

```python
	def delete_at_head(self):
        curr = self.head
        self.head = curr.next
        curr.next.prev = None

# 노드 삭제
dll.delete_at_head()
print(dll) # 결과 : 2 <-> 3 <-> 4 <-> 5 
```

### 노드 조회
```python
    def __str__(self):
        curr = self.head
        
        result = []
        while curr != None:
            result.append(curr.data)
            curr = curr.next
        
        return " <-> ".join(map(str, result))
```

```python
    def display(self):
        current = self.head
        while current:
            print(current.data, end=" <-> ")
            current = current.next
        print("None")
```

## 회고

`while curr`와 `while curr.next`가 너무 헷갈렸다.
언제는 curr를 써야하고, 언제는 curr.next를 써야하는 건지 몰랐는데, 그래도 어느정도 알게되었다. 아래에 간단하게 정리해놓아야 다음에도 또 보겠지..

#### while curr

```python
curr = self.head
while curr:
    print(curr.val)  # 현재 노드의 값을 처리
    curr = curr.next  # 다음 노드로 이동
```
현재 노드부터 시작하여 루프를 돌며 현재 노드의 값을 처리하고 다음 노드로 이동한다.


#### while curr.next
```python
curr = self.head
while curr.next:
    print(curr.next.val)  # 현재 노드의 다음 노드의 값을 처리
    curr = curr.next  # 다음 노드로 이동
```
두 번째 노드부터 시작하여 끝까지 순회하며 각 노드를 처리할 때 사용된다.
    
    
어떤 패턴을 사용할지는 처리해야 하는 데이터와 작업에 따라 달라질 수 있으니, 외워서 하려하지말고, 코드를 이해해보자.

## 참고
+ [단일, 이중 연결 리스트(Python)](https://live-jh.tistory.com/52)
+ [단일 연결 리스트(Java)](https://keykat7.blogspot.com/2022/02/data-structure-singly-linked-list.html)
+ [이중 연결 리스트(Java)](https://keykat7.blogspot.com/2022/02/data-structure-doubly-linked-list.html)