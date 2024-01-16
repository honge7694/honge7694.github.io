---
title: 순열 - leetcode 46번
date: 2023-10-21 23:02:00 +09:00
categories: [IT, Algorithm]
tags: [algorithm, DFS, leetcode]
image: /assets/img/posts/algorithm.png
---

이 문제는 [DFS](https://honge7694.github.io/posts/%ED%95%AD%ED%95%B499-%EC%9D%BC%EC%A7%80(10)-DFS/)정리한 후 풀려했지만 잘 안풀려서 계속해서 복습하고 작성하는 글이다.


## [순열](https://leetcode.com/problems/permutations/description/)

정수 배열이 주어 지면 가능한 모든 순열을 반환한다. 순서는 상관없다.

Example 1
> **Input**     
> nums = [1,2,3]    
**Output**    
[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]    

Example 2    
>**Input**    
>nums = [0,1]    
**Output**    
[[0,1],[1,0]]    

Example 3        
>**Input**    
nums = [1]    
**Output**    
[[1]]    


## 나의 풀이
문제를 몇번 반복해서 풀다보니 어느정도 감이 생겼다. index를 주의해야한다. index에 대한 자세한 설명은 [1, 2, 3 더하기](https://honge7694.github.io/posts/%ED%95%AD%ED%95%B499-1,-2,-3-%EB%8D%94%ED%95%98%EA%B8%B0-baekjoon/#index%EB%A5%BC-%EC%8D%BC%EC%9D%84-%EB%95%8C)이 곳에서 정리를 했으니 다시보면 좋을 것 같다.

```python
import sys 
sys.setrecursionlimit(10**6) 

def input(): 
	return sys.stdin.readline().strip()

def dfs(path):
	# 백트래킹
	if len(path) == len(nums):
		result.append(path)
		return
	
	for i in range(len(nums)):
		if nums[i] not in path:
			dfs(path + [nums[i]])


nums = [1, 2, 3]
result = []
dfs([])
print(result)
```

이 문제를 풀 때, 핵심은 백트래킹을 선언해주는 것과 재귀를 위한 for문 안에 if문을 작성하는 것 같다. `if nums[i] not in path`를 생각하지 못했을 때 1번 예시 결과는 다음과 같았다.

> **if nums[i] not in path 없을 때**    
> [[1, 1, 1], [1, 1, 2], [1, 1, 3], [1, 2, 1], [1, 2, 2], [1, 2, 3], [1, 3, 1], [1, 3, 2], [1, 3, 3], [2, 1, 1], [2, 1, 2], [2, 1, 3], [2, 2, 1], [2, 2, 2], [2, 2, 3], 
[2, 3, 1], [2, 3, 2], [2, 3, 3], [3, 1, 1], [3, 1, 2], [3, 1, 3], [3, 2, 1], [3, 2, 2], [3, 2, 3], [3, 3, 1], [3, 3, 2], [3, 3, 3]]

path에 들어가는 수를 관리하지 못했기에, path에 이미 존재하는 수가 다시 들어가는 것을 볼 수 있다. 그래서 if문을 통해 검사한 후, 재귀를 실행시키며 해결할 수 있었다.

> **정상결과**     
> [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]

    
## itertools
```python
import sys 
from itertools import permutations
sys.setrecursionlimit(10**6) 

def input(): 
	return sys.stdin.readline().strip()

def dfs():
	result = list(permutations(nums, 3))
	return result

nums = [1, 2, 3]
print(dfs())
```

`itertools.permutations`를 이용하면 매우 쉽게 풀 수 있다. 파이썬의 내장함수들을 잘 공부해서 쓸 수 있도록 하자. 

+ [파이썬 모듈](https://honge7694.github.io/posts/%ED%95%AD%ED%95%B499-%EC%9D%BC%EC%A7%80(11)-WIL/#%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%9C%A0%EC%9A%A9%ED%95%9C-%EB%AA%A8%EB%93%88)