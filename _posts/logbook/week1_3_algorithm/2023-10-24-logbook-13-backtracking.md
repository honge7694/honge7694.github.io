---
title: 항해99 일지(13) - Backtracking 
date: 2023-10-24 19:00:00 +09:00
categories: [DevOps, DataStructure, TIL]
tags: [항해99, TIL, 자료구조, Backtracking]
image: /assets/img/posts/항해99.png
---

## 백트래킹이란?
백트래킹은 주어진 조건을 만족하는 모든 해를 찾는 알고리즘 기법 중 하나이다. 주로 조합과 순열 문제, 스도쿠, N-퀸 문제 등에서 활용된다. 백트래킹은 완전 탐색의 한 형태로, 가능한 모든 경우의 수를 확인하면서 조건에 맞지 않는 경우 더 이상 진행하지 않고 이전 단계로 돌아가는 방법을 사용한다.    
    
주로 문제풀이에서는 **DFS 등으로 모든 경우의 수를 탐색하는 과정**에서, 조건문을 걸어서 답이 절대로 될 수 없는 상황을 정의하고, 그런 상황일 때는 **탐색을 중지시킨 뒤 다시 이전 단계로 돌아가서 다른 경우를 탐색하도록 구현**할 수 있다.

### 백트래킹의 구현

1. **선택**
	주어진 문제에서 어떤 선택을 할 것인지 결정한다.
2. **제약**
	선택에 제약 조건을 설정한다.
3. **목표**
	선택한 조합이 목표 조건을 만족하는지 확인한다.
4. **해 제거**
	선택한 조합이 목표 조건을 만족하지 않는 경우, 이전 단게로 돌아가서 다른 선택을 시도한다.

예시로 오늘 푼 문제 중 하나인 [1, 2, 3 더하기](https://honge7694.github.io/posts/%ED%95%AD%ED%95%B499-1,-2,-3-%EB%8D%94%ED%95%98%EA%B8%B0-baekjoon/)의 풀이 코드 중 일부이다.

```python
def dfs(target, path):
	global count
	
	# 백트래킹
	if target == 0:
		result_path.append(path)
		count += 1
		return
		
	if target < 0:
		return
	
	for i in range(len(nums)):
		dfs(target-nums[i], path+[nums[i]])
```

위 코드에서 `target == 0`이 되거나 `target < 0`이면, 더이상 탐색을 하지않고 중지시킨다.

## 회고
아직 백트래킹을 어떤 조건을 줘야 잘 이용할 수 있을지 모르겠다. 좀 더 연습이 필요할 것같고, 문제들을 정리해보면서 어떤 상황에 어떻게 사용하는지 이해해야겠다.