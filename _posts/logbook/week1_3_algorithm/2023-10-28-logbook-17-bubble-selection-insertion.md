---
title: Bubble, Selection, Insertion
date: 2023-10-28 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [ til, 자료구조, sort]
image: /assets/img/posts/til.png
---

## 버블 정렬(Bubble Sort)
간단하고 이해하기 쉬운 정렬 알고리즘으로,  최선, 평균, 최악의 모두 O(n^2)의 시간 복잡도를 가진다. 구현이 간단하며 작은 배열에 대해 효과적일 수 있지만, 대규모 배열에 대해 비효율적이며, 다른 정렬 알고리즘보다 느리다.

### 버블 정렬 코드

```python
def bubble_sort(nums):
	for i in range(len(nums)):
		for j in range(len(nums)-i-1):
			if nums[j] > nums[j+1]:
				nums[j], nums[j+1] = nums[j+1], nums[j]
				
	return nums
```

#### 동작 방식
1. 배열의 처음부터 끝까지 인접한 두 원소를 비교한다.
2. 만약 왼쪽 원소가 오른쪽 원소보다 크다면 두 원소를 교환한다.
3. 이러한 비교와 교환을 배열의 끝까지 계속한다. 이 때, 가장 큰 원소가 맨 오른쪽으로 이동한다.
4. 다음으로, 가장 큰 원소를 제외하고 남은 배열에 대해 위의 과정을 반복한다.
5. 배열이 정렬될 때까지 위의 과정을 반복한다.

## 선택 정렬(Selection Sort)
선택 정렬은 가장 작은(또는 큰) 원소를 선택하고 배열의 맨 앞(또는 맨 뒤)원소와 교환하여 정렬하는 방식의 간단한 정렬 알고리즘이다. 최선, 평균, 최악의 경우 모두 O(n^2)의 시간복잡도를 가진다. 구현이 간단하며, 작은 배열에 효과적일 수 있지만, 대규모 배열에 대해 비효율적이며, 다른 정렬 알고리즘보다 느리다.

### 선택 정렬 코드

```python
def choose_sort(nums):
	for i in range(len(nums)):
		minimum = i
		for j in range(i+1, len(nums)):
			if nums[minimum] > nums[j]:
				minimum = j
		
		if minimum != i:
			nums[i], nums[minimum] = nums[minimum], nums[i]
	
	return nums
```

#### 동작 방식

1. 배열의 처음부터 끝까지 순회하면서 최소값(또는 최대값)을 찾는다.
2. 최소값(또는 최대값)을 찾으면 그 값을 현재 위치와 교환한다.
3. 이 과정을 배열의 처음부터 끝까지 반복하며 배열이 정렬될 때까지 진행한다.

## 삽입 정렬(Insertion Sort)
현재 원소를 이미 정렬된 배열 부분에 삽입하여 정렬하는 알고리즘이다. 배열의 처음에는 정렬된 부분이 없고, 정렬되지 않은 부분을 하나씩 정렬된 부분에 삽입하면서 배열이 정렬된다. 최선의 경우(이미 정렬된 배열)O(n), 평균 및 최악의 경우 모두 O(n^2)의 시간 복잡도를 갖는다.
구현이 간단하며, 정렬되지 않는 배열의 일부분이 이미 정렬되어 있는 경우에 효율적이지만, 대규모 배열에 대해 비효율ㅈ거이며, 다른 정렬 알고리즘보다 느리다.

### 삽입 정렬 코드

```python
def insertion_sort(nums):
	for i in range(1, len(nums)):
		for j in range(1, i + 1):
			cmp = i - j
			
			if nums[cmp] > nums[cmp+1]:
				nums[cmp], nums[cmp+1] = nums[cmp+1], nums[cmp]
			else:
				break
	
	return nums
```

#### 동작 방식

1. 정렬되지 않은 배열의 첫 번째 원소를 정렬된 부분의 첫 번째 원소와 비교한다.
2. 정렬된 부분에서 현재 원소보다 큰 원소를 찾을 때까지 비교를 반복한다.
3. 큰 원소를 오른쪽으로 이동시키고, 현재 원소를 삽입한다.
4. 이 과정을 배열의 끝까지 반보갛면서 배열을 정렬한다.

## 정리

#### 버블 정렬
+ 인접한 원소끼리 비교하여 교환하는 방식
+ 셋 중에 제일 느리지만 단순하다.

#### 선택 정렬
+ 최솟값을 찾아서 맨 앞으로 이동하는 방식
+ 버블 정렬보다 좋다.

#### 삽입 정렬
+ 앞에서부터 차례대로 이미 정렬된 부분과 비교하여 교환하는 방식
+ 셋 중에 제일 빠르지만 배열이 길어질수록 효율성이 떨어진다.


## 참고

+ [애니메이션을 활용한 시각화](https://visualgo.net/en/sorting)
+ [노마드 코더 정렬 알고리즘](https://www.youtube.com/watch?v=Bor_CRWEIXo)

