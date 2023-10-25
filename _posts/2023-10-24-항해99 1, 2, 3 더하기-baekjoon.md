---
title: 1, 2, 3 더하기 - baekjoon 9095번
date: 2023-10-24 19:01:00 +09:00
categories: [DevOps, Algorithm]
tags: [항해99, 알고리즘, 백트래킹, baekjoon]
image: /assets/img/posts/알고리즘.png
---

## [9095. 1, 2, 3 더하기](https://www.acmicpc.net/problem/9095)

정수 4를 1, 2, 3의 합으로 나타내는 방법은 총 7가지가 있다. 합을 나타낼 때는 수를 1개 이상 사용해야 한다.
1+1+1+1
1+1+2
1+2+1
2+1+1
2+2
1+3
3+1
정수 n이 주어졌을 때, n을 1, 2, 3의 합으로 나타내는 방법의 수를 구하는 프로그램을 작성하시오.

example1
> **Input**    
> 3    
4    
7    
10    
**Output**    
7    
44    
274    

## 나의 풀이
처음 봤을 때 그동안 풀던 `DFS`로 풀 수 있을 것 같았다. 이와 유사한 문제는 [leetcode 39. 조합합계](https://leetcode.com/problems/combination-sum/)이 문제였다. 요즘 풀었던 문제 계속 풀었더니 외워서 푸는 느낌이 강하지만 그래도 풀려서 기분이 좋았다.

### index를 안썼을 때

```python
import sys 
sys.setrecursionlimit(10**6) 

def input(): 
	return sys.stdin.readline().strip()

def dfs(target, path):
	global count
	
	# 백트래킹
	if target == 0:
		result_path.append(path)
		count += 1
		return
	if target < 0:
		return
	
	# 재귀를 통해, target을 빼고, path를 구한다.
	for i in range(len(nums)):
		dfs(target-nums[i], path+[nums[i]])


T = int(input()) # 테스트케이스 입력
nums = [1, 2, 3]

for _ in range(T):
	target = int(input())
	count = 0
	result_path = [] # 무엇을 더했는지 확인용
	dfs(target, [])
	print(count, result_path)
```

> **Input**    
> 1    
4    
**Output**    
7 [[1, 1, 1, 1], [1, 1, 2], [1, 2, 1], [1, 3], [2, 1, 1], [2, 2], [3, 1]]

### index를 썼을 때

```python
import sys 
sys.setrecursionlimit(10**6) 

def input(): 
	return sys.stdin.readline().strip()

def dfs(target, index, path):
	global count
	if target == 0:
		result_path.append(path)
		count += 1
		return
	if target < 0:
		return

	for i in range(index, len(nums)):
		dfs(target-nums[i], i, path+[nums[i]])


T = int(input())
nums = [1, 2, 3]
result_path = []
for _ in range(T):
	target = int(input())
	count = 0
	dfs(target, 0, [])
	print(count, result_path)
```

> **Input**    
> 1    
4    
**Output**    
7 [[1, 1, 1, 1], [1, 1, 2], [1, 3], [2, 2]] 

처음에 이 문제를 풀며, 나는 path를 이용해 어떤 수가 덧셈이 안되는지 확인을 하였다. 왜냐하면 나의 답은 [[1, 1, 1, 1], [1, 1, 2], [1, 3], [2, 2]] 이렇게 나오는데 원하는 결과는 [[1, 1, 1, 1], [1, 1, 2], [1, 2, 1], [2, 1, 1], [1, 3], [2, 2], [3, 1]] 였기 때문이다. path를 통해 내가 놓치고 있는 수가 무엇인지 알 수 있었다.    
    
    
그리고 또 언제 index를 써야하는지 알 수 있었다. index를 쓰는 경우에는 다음 수, 그러니까 [2, 1, 1] **이런식으로 반복문의 i 보다 큰 수는 재귀의 index 매개변수 자리에 i를 넣으므로 nums[i]보다 작은 수가 들어갈 수 없었다.** 이걸 통해 언제 index를 사용하고 사용하지않고를 알 수 있게 되었다.

## 제출 코드

```python
import sys 

def input(): 
	return sys.stdin.readline().strip()

def dfs(target):
	global count
	if target == 0:
		count += 1
		return
	if target < 0:
		return

	for i in range(len(nums)):
		dfs(target-nums[i])


T = int(input())
nums = [1, 2, 3]
for _ in range(T):
	target = int(input())
	count = 0
	dfs(target)
	print(count)
```


