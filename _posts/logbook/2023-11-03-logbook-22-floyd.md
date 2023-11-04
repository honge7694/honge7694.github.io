---
title: "항해99 일지(22) - 최단경로:플로이드-와샬"
date: 2023-11-03 23:00:00 +09:00
categories: [DevOps, Algorithm, TIL]
tags: [항해99, TIL, 알고리즘, 최단 경로, 플로이드 와샬]
image: /assets/img/posts/항해99.png
---


## 플로이드-와샬 알고리즘
플로이드-와샬 알고리즘은 그래프 이론에서 **모든 노드 쌍 간의 최단 경로**를 찾는데 사용되며, 특히 가중치가 음수 가중치를 처리할 수 있는 장점을 가진다. 시간 복잡도는 O(V^3)이며, 모든 노드 쌍 간의 최단 경로를 계산하는데 사용된다. 도로 네트워크, 통신 네트워크, 비행 경로 계획 등 다양한 응용 분야에서 사용 가능하다.


#### 동작 방식

1. 노드와 간선을 입력 받는다.
2. 노드+1만큼의 2차원 그래프를 그려준다.
3. 자기 자신으로 가는 비용은 0으로 초기화 해준다.
4. 간선을 입력 받는다.
5. **점화식**에 따라 알고리즘을 수행시킨다.
6. 조건에 맞게 출력해준다.

#### 점화식 : **`dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`**

### 예시 코드
```python
import sys
input = sys.stdin.readline
INF = int(1e9)


# 노드, 간선
n, m = map(int, input().split())
# 2차원 리스트
graph = [[INF] * (n+1) for _ in range(n+1)]

# 자기 자신에서 자기 자신으로 가는 비용은 0으로 초기화
for a in range(1, n+1):
	for b in range(1, n+1):
		if a == b:
			graph[a][b] = 0

# 간선 입력
for _ in range(m):
	# A와 B가 서로에게 가는 비용은 1이라고 설정
	a, b = map(int, input().split())
	graph[a][b] = 1
	graph[b][a] = 1
	
# 소개팅 x와 목적지 k
x, k = map(int, input().split())

# 점화식에 따라 플로이드 위셜 알고리즘을 수행
for k in range(1, n+1):
	for a in range(1, n+1):
		for b in range(1, n+1):
			graph[a][b] = min(graph[a][b], graph[a][k] + graph[k][b])

# 수행된 결과를 출력
distance = graph[1][k] + graph[k][x]

# 도달 할 수 없는 경우, -1을 출력
if distance >= INF:
	print("-1")
# 도달 할 수 있는 경우, 최단 거리를 출력
else:
	print(distance)


"""
입력 예제
5 7
1 2
1 3
1 4
2 4
3 4
3 5
4 5
4 5
"""
```

이코테 책 256p에 있는 미래 도시를 가져왔다. 


### 플로이드-와샬과 다익스트라의 차이

![다익스트라와 플로이드의 차이](https://github.com/honge7694/honge7694.github.io/assets/76715487/4a56bc7c-bde0-4d51-a13c-62e6530732fb)

## 회고
알고리즘 문제에서는 보통 점화식에 조건을 주거나, 수행된 결과물을 출력하는 부분을 수정해서 풀어야 하곤 했다. 시간 복잡도가 O(V^3)라서 노드가 많은 문제에서는 못 쓰지만, 노드가 적은 문제에서 쓴다면 쉽게 풀어낼 수 있다.