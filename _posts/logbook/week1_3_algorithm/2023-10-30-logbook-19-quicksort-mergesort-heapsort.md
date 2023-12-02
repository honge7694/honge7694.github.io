---
title: 항해99 일지(19) - Quicksort, Mergesort, Heapsort
date: 2023-10-30 21:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [항해99, TIL, 자료구조, Sort]
image: /assets/img/posts/logbook99.png
---

## 퀵 정렬(Quicksort)
퀵 정렬은 분할 정복 algorithm-1.png으로, 배열을 분할하고 각 부분을 정렬하여 전체 배열을 정렬한다. 최선의 경우 평균 O(n log n)이지만 최악의 경우 O(n^2)이다. 빠른 정렬 algorithm-1.png 중 하나로, 추가 메모리가 거의 필요하지 않지만 최악의 경우 시간 복잡도가 높다.

![퀵 정렬](https://github.com/honge7694/honge7694.github.io/assets/76715487/3b609874-48a2-43f2-b22e-d674be59ce81)

### 퀵 정렬 코드

```python
def quicksort(lst, start, end):
	def partition(part, ps, pe):
		pivot = part[pe]
		i = ps - 1
		for j in range(ps, pe):
			if part[j] <= pivot:
				i += 1
				part[i], part[j] = part[j], part[i]
				
		# 피벗이 작은 그룹과 큰 그룹 사이에 들어가게 하는 코드
		part[i+1], part[pe] = part[pe], part[i+1] 
		
		return i + 1
	

	if start >= end:
		return None
	
	p = partition(lst, start, end) # 배열을 두 그룹으로 분할 시켜준다.
	
	quicksort(lst, start, p-1)
	quicksort(lst, p+1, end)
	
	return lst


lst = [8, 43, 46, 0, 45, 40, 0, 39, 8, 36]
print(quicksort(lst, 0, len(lst)-1))
# 결과 : [0, 0, 8, 8, 36, 39, 40, 43, 45, 46]
```

#### 동작 방식

1. **피벗 선택** : 배열에서 피벗을 선택한다. 피벗은 배열의 요소 중 하나로 선택된다. 피벗 선택 방법은 다양하며, 보통 첫 번째 요소, 마지막 요소 또는 중간 요소를 선택한다. 내 코드에서는 마지막 요소를 선택한다.
2. **분할** : 선택한 피벗을 기준으로 배열을 두 그룹으로 분할한다. 피벗 보다 작은 요소는 왼쪽 그룹으로, 큰 요소는 오른쪽 그룹으로 이동된다. 이 과정은 두 개의 포인터(왼쪽 포인터와 오른쪽 포인터)를 사용하여 수행된다.
3. **피벗 위치 결정** : 분할된 배열에서 피벗의 최종 위치를 결정한다. 이 단계를 통해 피벗은 최종적으로정렬된 위치에 올바르게 배치 된다.
4. **재귀적 정렬** : 피벗을 기준으로 분할된 두 개의 하위 배열(왼쪽 그룹과 오른쪽 그룹)에 대해 재귀적으로 퀵 정렬을 수행한다. 각 하위 배열은 독립적으로 정렬된다.
5. **합병** : 재귀적 정렬이 완료되면 배열의 모든 하위 배열이 합병되어 정렬된 배열이 생성된다.

## 병합 정렬(Mergesort)
병합 정렬은 분할 정복 algorithm-1.png으로, 배열을 반으로 나누고 이를 정렬하여 병합한다. 최선과 최악의 경우 모두 O(n log n)으로 일정하다. 안정적이며 일관된 성능을 가지지만, 추가 메모리가 필요하다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/d3a2feba-74a4-4553-a5dc-44d84f64ee3f)    

### 병합 정렬 코드

```python
def merge(arr1, arr2):
	result = []
	i = j = 0
	while i < len(arr1) and j < len(arr2):
		if arr1[i] < arr2[j]:
			result.append(arr1[i])
			i += 1
		else:
			result.append(arr2[j])
			j += 1
	
	while i < len(arr1):
		result.append(arr1[i])
		i += 1
		
	while j < len(arr2):
		result.append(arr2[j])
		j += 1
	
	return result

def mergesort(lst):
	if len(lst) <= 1:
		return lst
	
	mid = len(lst) // 2
	L = lst[:mid]
	R = lst[mid:]
	
	return merge(mergesort(L), mergesort(R))


lst = [4, 6, 2, 9, 1]
print(mergesort(lst))
# 결과 : [1, 2, 4, 6, 9]
```

#### 동작 방식

1. **분할 (Divide)** : 배열을 두 개의 하위 배열로 분할한다.
2. **정복 (Conquer)** : 각 하위 배열에 대해 병합 정렬을 재귀적으로 적용한다. 이로써 각 하위 배열이 정렬된다.
3. **병합 (Merge)** : 정렬된 하위 배열을 병합하여 하나의 정렬된 배열을 생성한다. 이때, 두 하위 배열의 요소를 비교하여 정렬하면서 병합한다.
4. **재귀 종료 조건** :배열의 크기가 1이거나 비어 있으면 정렬이 완료되었으므로 더 이상 분할하지 않고 반환한다.

## 힙 정렬(Heapsort)
힙 정렬은 비교 기반의 정렬 algorithm-1.png으로, 최대 힙을 구성하여 요소를 교환하면서 정렬한다.
시간 복잡도는 항상 O(n log n)으로 일정하다. 추가 메모리가 필요하지 않으며, 안정적이지 않지만 빠른 속도를 갖고 있다.

### 힙 정렬 코드

```python
def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
	# 선택 노드의 양 자식 중 가장 큰 값을 찾는 과정
    if left < n and arr[left] > arr[largest]:
        largest = left
    
    if right < n and arr[right] > arr[largest]:
        largest = right
    
	# 선택 노드와 자식 노드의 위치가 바뀌어야 한다면
    if largest != i:
		# 부모 자식 위치 변경
        arr[i], arr[largest] = arr[largest], arr[i]
		# 부모가 자식으로 내려간 이후에도, 그 자식과 바뀔 여지가 있는지 재귀로 체크
        heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    
    # 최대 힙을 구성
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # 루트 노드를 하나씩 추출하여 정렬
    for i in range(n - 1, 0, -1):
        arr[i], arr[0] = arr[0], arr[i]  # 루트와 마지막 요소 교환
        heapify(arr, i, 0)  # 힙을 다시 구성
    
    return arr


arr = [12, 11, 13, 5, 6, 7]
sorted_arr = heap_sort(arr)
print(sorted_arr)
```


#### 동작 방식

1. **힙 구성** : 정렬되지 않은 배열을 최대 힙 또는 최소 힙으로 구성한다. 최대 힙을 사용하면 배열의 가장 큰 요소가 루트 노드에 위치하게 되고 최소 힙을 사용하면 가장 작은 요소가 루트 노드에 위치하게 된다.
2. **힙 정렬** : 최대 힙 또는 최소 힙에서 루트 노드를 추출하고 배열의 끝과 교환한다. 이로써 루트 노드는 정렬된 위치에 놓이게 된다. 그리고 힙 크기를 감소시킨 후 다시 힙 속성을 유지하도록 조정(heapify)한다. 이 작업을 힙이 빌 때까지 반복한다.
3. **반복** : 위 단계를 바놉갛여 배열의 모든 요소가 정렬될 때까지 진행한다.

## 회고
생각보다 쉽다고 생각했는데, 문제를 보면 또 어렵다. 그래도 문제를 풀 때 최대한 파이썬 내장 함수를 사용하지않고, 정렬 algorithm-1.png을 이용하여 풀려고 했는데 생각보다 잘 풀렸다. 하지만 sort를 이용하면 너무 간단하게 풀리는 문제들이 많아 기존 정렬들의 존재에 의문이 든다...
