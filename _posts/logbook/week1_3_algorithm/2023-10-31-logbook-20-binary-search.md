---
title: 항해99 일지(20) - Binary Search
date: 2023-10-31 23:00:00 +09:00
categories: [IT, DataStructure, TIL]
tags: [항해99, til, 자료구조, binary search, python-library, bisect]
image: /assets/img/posts/logbook99.png
---

## 이진 탐색(Binary Search)
이진 탐색은 **정렬된 배열**에서 원하는 항목을 빠르게 찾는 알고리즘이다. 반복하여 배열을 반으로 나누고 비교하면서 찾고자 하는 값이 배열에서 발견될 때까지 계속 반복한다. 이진 탐색은 평균적으로 O(log n) 시간 복잡도를 가지므로 대용량 데이터에서 빠르게 검색이 가능하며, 정렬에 드는 비용을 감수하고 검색시간을 절약할 수 있다. 하지만 정렬되어 있어야 한다는 제약이 있고, 배열 구조를 유지하려면 삽입 및 삭제 연산에 추가 작업이 필요하다.

### 이진 탐색 코드

```python
def search(lst, target, start, end):
	if start > end:
		return False
	
	mid = (start + end) // 2
	
	# mid와 target이 같은 경우 
	if lst[mid] == target:
		return True
	# mid가 target보다 큰 경우
	elif lst[mid] > target:
		return search(lst, target, start, mid-1)
	# mid가 target보다 작은 경우
	else:
		return search(lst, target, mid+1, end)

lst = [[1,4,7,11,15],[2,5,8,12,19],[3,6,9,16,22],[10,13,14,17, 24],[18,21,23,26,30]]
target = 5
temp = True

for l in lst:
	if search(l, target, 0, len(l)-1):
		print(True)
		break
```

위 코드는 [리트코드 240번](https://leetcode.com/problems/search-a-2d-matrix-ii/description/) 문제이다. target을 찾는 코드인데, 위의 예시처럼 사용하면 된다.

#### 동작 방식

1. 시작
	+ 정렬된 배열을 사용하거나 또는 배열을 정렬한다. 배열을 왼쪽과 오른쪽 끝을 가리키는 두 개의 포인터로 초기화 한다.
2. 중간 요소 확인
	+ 포인터의 중간 지점을 찾아 그 위치의 요소를 확인한다.
	+ 중간 요소를 찾는 가장 일반적인 방법은 `(start + end) // 2`를 사용하는 것이지만, 오버플로우 및 정확도 문제를 피하기 위해 `(start + (end - start)) // 2`와 같은 안전한 방법을 사용할 수 있다.
3. 비교
	+ 중간 요소와 찾고자 하는 값을 비교한다.
	+ 만약 중간 요소가 찾고자 하는 값과 같다면, 검색이 성공하고 종료된다.
4. 배열 반으로 나누기
	+ 중간 요소와 찾고자 하는 값을 비교하여 중간 요소가 타겟보다 더 작은 경우, 찾고자 하는 값은 중간 요소의 오른쪽 부분에 있다.
	+ 중간 요소가 타겟보다 더 큰 경우, 찾고자 하는 값은 중간 요소의 왼쪽 부분에 있다.
5. 포인터 이동
	+ 찾고자 하는 값이 왼쪽 또는 오른쪽 부분에 있을 경우, 포인터를 이동하여 배열의 해당 부분에 집중한다.
	+ 이동한 포인터 위치에서 다시 중간 요소를 확인하고 비교를 반복한다.
6. 반복
	+ 2에서 5까지의 단계를 반복하고, 원하는 값을 찾을 때까지 이진 탐색을 수행한다.
	+ 만약 찾고자 하는 값이 배열에 없으면, 검색이 실패하고 종료된다.

## bisect 모듈
정렬된 시퀀스(주로 리스트)에서 이진 탐색을 수행하는데 사용된다. 삽입 지점을 찾거나 정렬된 순서를 유지하는데 도움을 준다.    
 `lo`, `hi`는 옵션으로, 탐색 범위를 제한하는데 사용된다.
 
#### bisect_left(a, x, lo=0, hi=len(a))
정렬된 배열 `a`에서 `x`가 들어갈 **위치(왼쪽 끝)** 를 반환한다.


#### bisect_right(a, x, lo=0, hi=len(a))
정렬된 배열 `a`에서 `x`가 들어갈 **위치(오른쪽 끝)** 를 반환한다.

#### insort_left(a, x, lo=0, hi=len(a)):
정렬된 배열 a에서 x를 삽입하고 정렬 순서를 유지한다.

#### insort_right(a, x, lo=0, hi=len(a)):
정렬된 배열 a에서 x를 삽입하고 정렬 순서를 유지한다.

### bisect 예시 코드

```python
import bisect

arr = [1, 3, 3, 5, 7, 9]
x = 3

# bisect_left 사용
index_left = bisect.bisect_left(arr, x)
print(f"Insert {x} at index {index_left}")
# 결과 : Insert 3 at index 1

# bisect_right 사용
index_right = bisect.bisect_right(arr, x)
print(f"Insert {x} at index {index_right}")
# 결과 : Insert 3 at index 3
```

## 참고

+ [파이썬 공식 문서](https://docs.python.org/ko/3.7/library/bisect.html)