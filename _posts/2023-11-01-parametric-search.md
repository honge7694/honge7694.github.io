---
title: 파라메트릭 서치란?
date: 2023-11-01 17:00:00 +09:00
categories: [DevOps, Algorithm, TIL]
tags: [항해99, TIL, parametric]
image: /assets/img/posts/알고리즘.png
---


## 파라메트릭 서치(Parametric Search)
파라메트릭 서치는 **최적화 문제를 결정 문제**로 변환하여 해결하는 알고리즘 기법이다. 이것을 쉽게 말하면 구하고자 하는 값의 범위가 주어질 때, 그 중 하나의 값을 하나 잡고 조건을 만족하는지 묻는 것이다. 파라메트릭 서치는 **이진 탐색과 연관이 깊어 이진 탐색을 기반**으로 하면서도 다양한 응용 분야에서 활용된다. 시간 복잡도는 O(log n)이다.

### 파라메트릭 서치의 핵심 개념

#### 파라메터(Parameter)
+ 파라 메트릭 서치에서 중요한 역할을 하는 변수 또는 파라메터이다.
+ 최적화 문제에서 이 파라메터를 조절하면서 최적해를 찾는다.
+ 예를 들어, 나무 자르기 문제에서 톱의 높이(최대 높이)인 `h`는 파라메터로 사용된다.

#### 결정 문제(Decision Problem)
+ 최적화 문제를 해결하기 위해 파라메터를 이용하여 변환된 문제이다.
+ 파라메터 값에 대한 "예" 또는 "아니오"의 결정을 내리는 문제로 변환된다.
+ 예를 들어, "주어진 높이 `h`로 나무를 자르면 `M`미터를 얻을 수 있나?"와 같은 질문이 결정문제이다.

#### 이진 탐색(Binary Search)
+ 파라메트릭 서치에서 주로 사용되는 알고리즘이다.
+ 파라메터 값의 범위를 반복적으로 이진 탐색을 통해 줄여나가면서 최적 값을 찾는다.
+ 파라메터 값의 범위가 넓고 연속적인 경우, 이진 탐색을 사용하여 효율적으로 파라메터 공간을 탐색한다.


## 코드

```python
import sys
input = sys.stdin.readline

def search(lst, budget):
	start = 1 # 0을 주면 end가 1일 때, division error가 나타난다.
	end = max(lst)
	
	while start <= end:		
		total = 0
		mid = (start + end) // 2
		
		for x in lst:
			# 제출 예산(x) or 책정될 정부 예산(mid)을 더한다.
			total += min(x, mid)
		
		# total(지방 총 예산) 값이 정부 예산보다 적다.
		if total <= budget:
			start = mid + 1

		# total(지방 총 예산) 값이 정부 에산보다 많다.
		elif total > budget:
			end = mid - 1
	
	return end


# N = int(input())
# cities = list(map(int, input().split()))
# budget = int(input())
cities = [120, 110, 140, 150]
budget = 485

print(search(cities, budget)) # 127
```

위 문제는 파라메트릭 서치의 대표적인 문제 [백준 2512. 예산](https://www.acmicpc.net/problem/2512)이다. 
코드를 보면 어렵다고 느껴지는 코드는 없다. 다만, if문으로 조건을 주는 부분과 for문 안을 문제 유형에 따라 어떻게 처리할지 다르다.
이 부분을 좀 더 생각하면서 문제를 풀면 될 것이다.

## 회고
이진 탐색으로 풀어야해서 처음에는 정렬이 필요한 문제인 줄 알았다. 하지만 정렬되지 않은 데이터에서 최소 또는 최대값을 찾는 경우 파라메트릭 서치를 사용할 수 있다는 것을 보게되었다.
코드를 확인해 봤는데 생각보다 어렵지는 않았다. 하지만, 문제는 `if total < budget` 여기서 경계값을 어떻게 줘야하는 것이 가장 큰 고민이었다. 문제를 푸는데 맞게 푼 것 같았지만 계속해서 원하는 결과가 나오지 않았기 때문이다. 혹시나 경계를 쉽게 생각하는 글이 있을까 해서 찾아봤지만 이해가 딱 하고 오는 글이 없었다. 아직은 내가 경험이 부족해서 그런 것 같으니 좀 더 풀어보면서 경계를 생각해봐야겠다.

## 문제
- [백준 2512. 예산](https://www.acmicpc.net/problem/2512)
- [백준 2805. 나무 자르기](https://www.acmicpc.net/problem/2805)
- [백준 1654. 랜선 자르기](https://www.acmicpc.net/problem/1654)