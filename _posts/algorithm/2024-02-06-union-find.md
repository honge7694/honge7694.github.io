---
title: "유니온 파인드(Union Find)란?"
date: 2024-02-06 18:00:00 +09:00
categories: [IT, Algorithm, TIL]
tags: [til, algorithm]
image: /assets/img/posts/algorithm.png
---


## 유니온 파인드(Union Find)란?

+ 유니온 파인드는 **그래프 알고리즘**으로 여러개의 노드가 존재할 때 두개의 노드를 선택해서 현재 이 두 노드가 서로 같은 그래프에 속하는지 판별하는 알고리즘이다.
+ 서로소 집합, 상호 베타적 집합(Disjoint-Set)으로도 불린다.
+ **노드를 합치는 Union 연산**과 노드의 **루트 노드를 찾는 Find연산**으로 이루어진다.
+ 트리 구조로 이루어진 자료구조 중 한가지로 생각되기도 한다.

> **왜 이름이 Union Find인가?**    
> **Union** : 서로 다른 두 개의 집합을 하나의 집합으로 병합하는 연산을 말한다. 이 자료구조에서는 상호 베타적 집합만을 다루므로 Union연산은 합집합 연산과 같다.    
> **Find** : 하나의 원소가 어떤 집합에 속해있는지를 판단한다.
{: .prompt-info }

<br/>

### 유니온 파인드 예제


![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/8cbc14ab-a8b1-43f4-94b2-1c3d4a84fb2e)

위와 같이 여러 개의 노드가 서로 연결되지 않고 각자 자기 자신만을 집합의 원소로 가지고 있을 때 다음과 같이 나타낼 수 있다. 모든 값이 자기 자신을 가리키도록 마든 것이다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ba87a98c-b83b-4085-81a2-236fb77c4f3d)

첫째 행은 노드 번호, 두 번째 행은 부모 노드 번호를 의미한다. 자신이 어떠한 부모에 포함되어 있는지를 나타낸 것이다.


![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/da33da7b-21d5-4e0c-b99f-e652abfe39f1)

이 때 위와 같이 1과 2를 그리고 4 5 6을 연결을 시켜본다. 이러한 연결성을 우리가 프로그래밍 언어로 `Union-Find`라고 생각하면 이해가 쉽다. 이를 다시 표로 표현한다면 아래와 같다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/39f0fff0-84e8-4c70-8d9e-3cbd5036e775)

2번째 인덱스의 값에 1이 들어간다. 부모를 합칠 때는 일반적으로 **더 작은 값 쪽으로 합친다**. 이것을 **합침(Union)** 이라고 한다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/4022bb07-e941-4acc-a990-bcd046d06f7c)

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ac0c1850-bac1-4b61-909c-6815382f58a8)

나머지 4, 5, 6도 합치면 위와 같이 될 것이다. 다만, 여기에서 한 가지 문제가 발견된다.
5와 6은 연결이 되었는지를 어떻게 파악할 수 있는지이다. 5와 6은 부모가 각각 4와 5로 다르기 때문에 부모 노드만 보고는 한번에 파악할 수 없다. 그렇기 때문에 **재귀 함수**가 사용된다.

<br/>

6의 부모를 찾기 위해서는 먼저 6이 가리키고 있는 5를 찾고, 5가 가리키고 있는 4를 찾아야 6의 부모는 4라고 알 수 있다. 이러한 과정은 재귀적으로 수행될 때 가장 효과적이고 직관적이다. 
그래서 Union의 연산이 끝나면 아래와 같은 표가 된다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/f58e10d3-c8bf-425f-a4e0-4acc59e87297)

노드 1, 2는 부모가 1이기 때문에 같은 그래프에 속한다고 할 수있고, 4, 5, 6 또한 마찬가지이다. Find 알고리즘은 두 개의 노드의 부모 노드를 확인하여 **현재 같은 집합에 속하는지 확인**하는 알고리즘이다.     
에를 들어 5와 6 노드는 같은 그래프 안에 있다고 말할 수 있지만, 2와 5는 같은 그래프 안에 있다고 할 수 없다.


## 코드

```python
# 특정 원소가 속한 집합을 찾기
def find(x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        return find(parent[x])
    return x

# 두 원소가 속한 집합을 합치기
def union(a, b):
    a = find(a)
    b = find(b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 노드의 개수와 간선(Union 연산)의 개수 입력 받기
v, e = map(int, input().split())
parent = [0] * (v + 1) # 부모 테이블 초기화하기

# 부모 테이블상에서, 부모를 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

# Union 연산을 각각 수행
for i in range(e):
    a, b = map(int, input().split())
    union(a, b)

# 각 원소가 속한 집합 출력하기
print('각 원소가 속한 집합: ', end='')
for i in range(1, v + 1):
    print(find(i), end=' ')

print()

# 부모 테이블 내용 출력하기
print('부모 테이블: ', end='')
for i in range(1, v + 1):
    print(parent[i], end=' ')
	
	
# 결과
8 3
1 2
4 5
5 6
각 원소가 속한 집합: 1 1 3 4 4 4 7 8 
부모 테이블: 1 1 3 4 4 4 7 8
```

## 참고

+ [나동빈 유튜브](https://www.youtube.com/watch?v=AMByrd53PHM)
+ [begin_fill 블로그](https://ip99202.github.io/posts/%EC%9C%A0%EB%8B%88%EC%98%A8-%ED%8C%8C%EC%9D%B8%EB%93%9C(Union-Find)/)