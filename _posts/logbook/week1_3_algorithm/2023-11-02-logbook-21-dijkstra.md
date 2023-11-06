---
title: "항해99 일지(21) - 최단경로:다익스트라"
date: 2023-11-02 23:00:00 +09:00
categories: [IT, DataStructure, Algorithm, TIL]
tags: [항해99, TIL, 알고리즘 이론, 최단 경로, 다익스트라]
image: /assets/img/posts/항해99.png
---

## 다익스트라 알고리즘
다익스트라 알고리즘은 하나의 출발점에서 모든 노드까지의 최단 경로를 찾는 알고리즘이다.
이 알고리즘은 가중치가 음수가 아닌 그래프에서 작동하며, 주로 길찾기 및 네트워크 라우팅 문제에 활용된다. 시간 복잡도는 일반적으로 O(V^2)시간 복잡도를 가지지만, 최소 힙(우선 순위 큐)를 사용하면 O(ElogV)로 최적화할 수 있다. V는 노드, E는 간선의 수이다.

#### 동작 방식

1. 출발 노드 선택
	+ 출발 노드를 선택하고 해당 노드까지의 거리를 0으로 설정한다.
	+ 다른 노드까지의 거리를 무한대로 초기화 한다.
2. 현재 노드 설정
	+ 아직 처리되지 않은 노드 중에서 현재까지의 거리가 가장 짧은 노드를 선택한다.(처음은 출발 노드가 선택된다.)
3. 이웃 노드 갱신
	+ 현재 노드의 이웃 노드들을 순회하면서, 출발 노드를 통해 이웃노드에 도달하는 거리를 계산하고, 현재까지 계산된 거리보다 짧을 경우에만 거리를 업데이트한다.
4. 현재 노드 표시
	+ 현재 노드를 처리되었음(visited)으로 표시한다.
5. 반복
	+ 위의 2~4단계를 반복하여 모든 노드에 대한 최단 거리를 계산한다.


### 간단한 다익스트라 코드
다익스트라에 의해서 처음 고안되었던 알고리즘으로 최단 거리가 가장 짧은 노드를 찾기 위해서, 매번 최단 거리 테이블을 선형적으로(모든 원소를 앞에서부터 하나씩) 탐색해야 하는 과정에서만 시간 복잡도를 O(V)의 시간이 걸린다. 다음에 나올 힙 자료구조를 사용한 코드보다는 직관적으로 보이는 장점이 있지만, 느리다는 단점이 있다.

```python
import sys
input = sys.stdin.readline
INF = int(1e9) # 10억을 표현


# 방문하지 않은 노드에서 가장 최단 거리가 짧은 노드의 번호를 반환
def get_smallest_node():
	min_value = INF
	index = 0 # 가장 최단 거리가 짧은 노드(인덱스)
	for i in range(1, n + 1):
		if distance[i] < min_value and not visited[i]:
			min_value = distance[i]
			index = i
	return index

def dijkstra(start):
	# 시작 노드에 대해서 초기화
	distance[start] = 0
	visited[start] = True # 방문 표시
	for j in graph[start]:
		distance[j[0]] = j[1]
	# 시작 노드를 제외한 전체 n - 1개의 노드에서 반복
	for i in range(n-1):
		# 현재 최단 거리가 가장 짧은 노드를 꺼내서, 방문 처리
		curr = get_smallest_node()
		visited[curr] = True
		
		# 현재 노드와 연결된 다른 노드를 확인
		for j in graph[curr]:
			cost = distance[curr] + j[1]
			
			# 현재 노드를 거쳐서 다른 노드로 이동하는 거리가 더 짧은 경우
			if cost < distance[j[0]]:
				distance[j[0]] = cost
				
# 노드의 개수, 간선의 개수 입력받기
n, m = map(int, input().split())
# 시작 노드 번호 입력받기
start = int(input())
# 각 노드에 연결되어 있는 노드에 대한 정보를 담는 리스트를 만들기
graph = [[] for _ in range(n+1)]
# 방문한 적이 있는지 체크하는 목적의 리스트를 만들기
visited = [False] * (n + 1)
# 최단 거리 테이블을 모두 무한으로 초기화
distance = [INF] * (n + 1)

# 간선의 정보 받기
for _ in range(m):
	a, b, c = map(int, input().split())
	# a번 노드에서 b번 노드로 가는 비용이 c라는 의미
	graph[a].append((b, c))

# 다익스트라 알고리즘 수행
dijkstra(start)

# 모든 노드로 가기 위한 최단 거리를 출력
for i in range(1, n+1):
	# 도달할 수 없는 경우 무한이라고 출력
	if distance[i] == INF:
		print("INFINITY")
	# 도달 할 수 있는 경우 거리를 출력
	else:
		print(distance[i])
```

### 개선된 다익스트라 코드

```python
import heapq
import sys
input = sys.stdin.readline
INF = int(1e9)

def dijkstra(start):
	q = []
	
	# 시작 노드로 가기 위한 최단 경로는 0으로 설정하여, 큐에 삽입
	heapq.heappush(q, (0, start))
	distance[start] = 0
	while q: # q가 비어있지 않다면
		# 가장 최단 거리가 짧은 노드에 대한 정보 꺼내기
		dist, curr = heapq.heappop(q)
		
		# 현재 노드가 이미 처리된 적이 있는 노드라면 무시
		if distance[curr] < dist:
			continue
		
		# 현재 노드와 연결된 다른 인접한 노드들을 확인
		for i in graph[curr]:
			cost = dist + i[1]
			# 현재 노드를 거쳐서, 다른 노드로 이동하는 거리가 더 짧은 경우
			if cost < distance[i[0]]:
				distance[i[0]] = cost
				heapq.heappush(q, (cost, i[0]))


# 노드의 개수, 간선의 개수를 입력받기
n, m = map(int, input().split())
# 시작 노드 입력받기
start = int(input())
# 각 노드에 연결되어 있는 노드에 대한 정보를 담는 리스트를 만들기
graph = [[] for i in range(n + 1)]
# 최단 거리 테이블을 모두 무한으로 초기화
distance = [INF] * (n + 1)

# 모든 간선 정보를 입력받기
for _ in range(m):
	a, b, c = map(int, input().split())
	# a번 노드에서 b번 노드로 가는 비용이 c라는 의미
	graph[a].append((b, c))


# 다익스트라 알고리즘을 수행
dijkstra(start)

# 모든 노드로 가기 위한 최단 거리를 출력
for i in range(1, n+1):
	# 도달할 수 없는 경우 무한이라고 출력
	if distance[i] == INF:
		print("INFINITY")
	# 도달 할 수 있는 경우 거리를 출력
	else:
		print(distance[i])
		
# 입력 예시
"""
6 11
1 
1 2 2
1 3 5
1 4 1 
2 3 3
2 4 2
3 2 3
3 6 5
4 3 3
4 5 1
5 3 1
5 6 2
"""
```


## 회고
기술매니저님의 말씀으로는 다익스트라 문제는 잘 안나온다고 한다. 하지만 난이도가 상당히 어려워서 풀 수 있을 정도로는 복습하는게 좋아보인다. 문제를 못 풀었지만, 풀었던 문제를 다시 풀면서 다익스트라 최단 경로 문제의 뼈대는 어떤식으로 생긴지 기억하면 좋을 것 같다.

+ 코드는 "이것이 취업을 위한 코딩테스트다" 를 참고하였다.