---
title: 항해99 일지(16) - heap
date: 2023-10-27 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [항해99, TIL, 자료구조, heap]
image: /assets/img/posts/logbook99.png
---


## 힙(heap)이란?
힙은 이진 트리 기반의 데이터 구조로, 각 노드는 특정한 조건을 만족시키는 조직된 형태의 데이터를 저장한다. 힙은 주로 다음과 같은 두 가지 주요 속성을 갖는다.

#### 완전 이진 트리
힙은 완전 이진 트리 구조를 갖는다. 즉, 마지막 레벨을 제외한 모든 레벨에서 노드가 가득 차 있고, 마지막 레벨에서는 노드가 왼쪽부터 순차적으로 채워진다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/3352b7ab-4f0f-4d20-b611-ab638d13423c)

위 그림은 모두 이진 트리이지만, (b)는 왼쪽부터 채우지 않아 완전 이진 트리가 아니다.    

#### 최대 힙과 최소 힙
**최대 힙(Max Heap)**    
각 노드의 값은 해당 노드의 자식 노드보다 크거나 같아야 한다. 최대값이 루트 노드에 위치한다.    
**최소 힙(Min Heap)**    
각 노드의 값은 해당 노드의 자식 노드보다 작거나 같아야 한다. 최소값이 루트 노드에 위치한다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ab4e7922-1eaf-455e-9db7-f6292bb47463)

위 그림에서 (b)는 부모 노드인 5가 자식 노드인 4보다 크므로 힙이 아니다.

## 힙 코드
코드는 최소힙 코드이다. 트리를 사용할 때, index = 0 부터 시작하여 만들어서 이것이 좀 더 익숙하여 이렇게 만들었지만, 대부분 index=1부터 시작하기 위해 `self.head = [None]`을 한다.

```python
class MinHeap:
    def __init__(self):
        self.heap = []

    def push(self, item):
        self.heap.append(item)
        self._heapify_up(len(self.heap) - 1)

    def pop(self):
        if len(self.heap) == 0:
            return None

        if len(self.heap) == 1:
            return self.heap.pop()

        root = self.heap[0]
        self.heap[0] = self.heap.pop()
        self._heapify_down(0)
        return root

    def _heapify_up(self, index):
        parent_index = (index - 1) // 2
        if index > 0 and self.heap[index] < self.heap[parent_index]:
            self.heap[index], self.heap[parent_index] = self.heap[parent_index], self.heap[index]
            self._heapify_up(parent_index)

    def _heapify_down(self, index):
        left_child_index = 2 * index + 1
        right_child_index = 2 * index + 2
        smallest = index

        if (
            left_child_index < len(self.heap)
            and self.heap[left_child_index] < self.heap[smallest]
        ):
            smallest = left_child_index

        if (
            right_child_index < len(self.heap)
            and self.heap[right_child_index] < self.heap[smallest]
        ):
            smallest = right_child_index

        if smallest != index:
            self.heap[index], self.heap[smallest] = self.heap[smallest], self.heap[index]
            self._heapify_down(smallest)

    def peek(self):
        return self.heap[0] if self.heap else None

    def size(self):
        return len(self.heap)
```

### 삽입(insert)
최악 시간 복잡도 : O(log n)    
**algorithm-1.png 과정**    
 1. 우선 트리의 맨 끝에 요소를 추가한다.
 2. 그 다음에 부모와 비교하며 필요한 경우 부모 노드와 위치를 바꾸는 과정을 반복한다.
 3. 이로인해 트리의 높이에 비례하는 O(log n)의 시간이 필요하다.

<details>
<summary>항해99 - 힙의 삽입 algorithm-1.png</summary>

<div markdown="1">

```
이 맥스 힙에서 9를 추가해보겠습니다!
      8      Level 0
    6   3    Level 1  
   4 2 1     Level 2 

1. 맨 마지막에 원소를 넣습니다.

      8      Level 0
    6   3    Level 1  
   4 2 1 9   Level 2 

2-1. 부모 노드와 비교합니다. 3보다 9가 더 크니까! 둘의 자리를 변경합니다.

      8      Level 0
    6   3    Level 1  
   4 2 1 9   Level 2 

      8      Level 0
    6   9    Level 1  
   4 2 1 3   Level 2 

2-2. 다시 부모 노드와 비교합니다. 8보다 9가 더 크니까! 둘의 자리를 변경합니다.

      8      Level 0
    6   9    Level 1  
   4 2 1 3   Level 2 

      9      Level 0
    6   8    Level 1  
   4 2 1 3   Level 2 

3. 가장 위에 도달했으므로 멈춥니다. 힙의 특성을 그대로 유지해 데이터를 삽입했습니다!

      9      Level 0
    6   8    Level 1  
   4 2 1 3   Level 2
```
</div>
</details>

### 삭제(Extract)
최악 시간 복잡도 : O(log n)    
**algorithm-1.png 과정**    
1. 루트 노드와 맨 끝에 있는 원소를 교체한다.
2. 맨 뒤에 있는 원소를 (원래 루트 노드)를 삭제한다.
3. 변경된 노드와 자식 노드들을 비교합니다. 두 자식 중 더 큰 자식과 비교해서 자신보다 자식이 더 크다면 자리를 바꿉니다.
4. 자식 노드 둘 보다 부모 노드가 크거나 가장 바닥에 도달하지 않을 때까지 3. 과정을 반복합니다.
5. 2에서 제거한 원래 루트 노드를 반환합니다.


<details>
<summary>항해99 - 힙의 삭제 algorithm-1.png</summary>

<div markdown="1">

```
이 맥스 힙에서 원소를 제거해보겠습니다! (항상 맨 위의 루트 노드가 제거 됩니다.)
      8      Level 0
    6   7    Level 1  
   2 5 4 3   Level 2 

1. 루트 노드와 맨 끝에 있는 원소를 교체한다.

      8      Level 0
    6   7    Level 1  
   2 5 4 3   Level 2 

      3      Level 0
    7   6    Level 1  
   2 5 4 8   Level 2 

2. 맨 뒤에 있는 원소를 (원래 루트 노드)를 삭제합니다. 
이 값이 기존 맥스힙에 있던 가장 큰 값입니다. 따라서 이 값을 마지막에는 반환해줘야 합니다!

      3      Level 0
    6   7    Level 1  
   2 5 4 X   Level 2 

3-1. 변경된 노드를 더 큰 자식 노드와 비교해야 합니다. 
우선 부모와 왼쪽 자식을 비교합니다. 그리고 부모와 오른쪽 자식을 비교합니다.
그리고 부모 보다 큰 자식 중, 더 큰 자식과 변경해야 합니다.
왼쪽 자식인 6과 오른쪽 자식인 7 중에서 7이 더 크고, 부모인 3보다 크니까 둘의 자리를 변경합니다.

      3      Level 0
    6   7    Level 1  
   2 5 4     Level 2 

      7      Level 0
    6   3    Level 1  
   2 5 4     Level 2 

3-2. 다시 자식 노드와 비교합니다. 
우선 부모와 왼쪽 자식을 비교합니다.
왼쪽 자식인 4는 부모인 3보다 더 크니까 둘의 자리를 변경합니다.

      7      Level 0
    6   3    Level 1  
   2 5 4     Level 2 

      7      Level 0
    6   4    Level 1  
   2 5 3     Level 2 


4. 가장 아래 레벨에 도달했으므로 멈춥니다. 힙의 특성을 그대로 유지해 데이터를 삭제했습니다!

      7      Level 0
    6   4    Level 1  
   2 5 3     Level 2 

5. 그리고, 아까 제거한 원래 루트 노드, 8을 반환하면 됩니다!
```
</div>
</details>

#### 최대/최소값 검색(Search)
최악 시간 복잡도 : O(1)
**algorithm-1.png 과정**    
1. 최대 힙에서는 최대값은 항상 루트 노드에 있으므로 상수 시간에 검색할 수 있다. 최소 힙에서도 최소값은 상수 시간에 검색 가능하다.




## 파이썬 힙 모듈
모듈의 힙은 교과서 힙 algorithm-1.png과 다릅니다: (a) 우리는 **0부터 시작하는 인덱싱을 사용**합니다. 이것은 노드의 인덱스와 자식의 인덱스 사이의 관계를 약간 덜 분명하게 만들지만, 파이썬이 0부터 시작하는 인덱스를 사용하기 때문에 더 적합합니다. (b) pop 메서드는 가장 큰 항목이 아닌 **가장 작은 항목을 반환**합니다 (교과서에서는 “최소 힙(min heap)”이라고 합니다; “최대 힙(max heap)”은 제자리 정렬에 적합하기 때문에 텍스트에서 더 흔합니다).

#### 리스트를 최소 힙으로 변환 (heapify)

```python
data = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
heapq.heapify(data)
print("data : ", data)

# 출력: data: [1, 1, 2, 3, 3, 9, 4, 6, 5, 5, 5]
```

#### 요소 추가(heappush) 및 최소값 추출(heappop)

```python
heapq.heappush(data, 0)
print("data : ", data)
# 출력: data : [0, 1, 2, 3, 1, 9, 4, 6, 5, 3, 5, 5]

min_value = heapq.heappop(data)
print("min_value : ", min_value)
# 출력:data : [1, 2, 3, 1, 9, 4, 6, 5, 3, 5, 5]
```

#### 가장 작은 n개의 요소를 추출(nsmallest)

```python
smallest_values = heapq.nsmallest(3, data)
print("Smallest 3 values:", smallest_values)
# 출력: Smallest 3 values: [3, 3, 4]
```

#### 가장 큰 n개의 요소를 추출(nlargest)

```python
largest_values = heapq.nlargest(3, data)
print("Largest 3 values:", largest_values)
# 출력: Largest 3 values: [9, 6, 5]
```

#### 여러 개의 정렬된 iterable을 병합(merge)

```python
iterable1 = [1, 4, 7]
iterable2 = [2, 5, 8]
iterable3 = [3, 6, 9]

merged = heapq.merge(iterable1, iterable2, iterable3)
print("Merged and sorted iterable:", list(merged))

# 출력: Merged and sorted iterable: [1, 2, 3, 4, 5, 6, 7, 8, 9]
```


## 참고

+ [이미지 출처 - 위키독스](https://wikidocs.net/194445)
+ [파이썬 문서](https://docs.python.org/ko/3/library/heapq.html)