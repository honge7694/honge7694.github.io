---
title: "항해99 일지(14) - Tree, Binary Tree"
date: 2023-10-25 14:00:00 +09:00
categories: [DevOps, DataStructure, TIL]
tags: [항해99, TIL, 자료구조, 이진 트리]
image: /assets/img/posts/항해99.png
---

## 트리란?
계층적인 구조를 나타내는 자료구조로, 순환하는 길이 없는 그래프로 정의한다. 트리는 다양한 종류와 형태가 있지만, 일반적으로 하나의 루트 노드에서 시작하여 각 노드들이 0개 이상의 자식 노드를 가질 수 있는 구조를 갖는다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/557cdd34-6fc1-49c6-a98c-4e750eb58fae)

#### 트리 용어

|용어| 설명|
|:---:|:---|
|루트(Root)|트리의 시작 노드로 다른 모든 노드는 루트를 향해 계층적으로 연결된다.|
|노드(Node)|트리의 기본 단위로 데이터와 하나 이상의 자식 노드를 갖는다.|
|간선(edge)|노드 사이의 연결을 나타낸다.|
|부모(Parent)노드|어떤 노드의 상위에 있는 노드를 가리킨다.|
|자식(Child)노드|어떤 노드의 하위에 있는 노드를 가리킨다.|
|리프(Leaf)노드|자식 노드가 없는 노드로, 트리의 가장 하위에 위치한다.|
|차수(Degree)|특정 노드가 가지고 있는 자식 노드의 개수를 의미한다. |
|서브 트리(SubTree)|트리에서 어떤 노드와 그 노드의 모든 자손 노드로 이루어진 트리를 카리킨다.|
|레벨(Level)|깊이를 기반으로, 동일한 깊이의 노드들을 레벨이라고 한다. 루트는 0 부터 레벨을 갖는다.|
|깊이(Depth)|루트 노드(depth=1)에서 어떤 노드까지의 계층적 깊이를 나타낸다.최대 수준 level + 1 |


## 이진 트리 (Binary Tree)
이진 트리(Binary Tree)는 계층 구조를 가지는 트리 자료 구조 중 하나로, 각 **노드가 최대 두개**의 자식 노드를 가질 수 있는 구조를 말한다. 이진 트리는 노드가 전혀 없는 빈 트리일 수도 있다. 즉, 노드가 없는 경우와 하나의 루트 노드만 있는 경우, 그리고 그 외에도 다양한 형태의 이진 트리가 존재한다.

![이진 트리](https://github.com/honge7694/honge7694.github.io/assets/76715487/a2a7128e-aac5-4738-801e-13e3aa5ff19e)

### 포화 이진 트리(Perfect Binary Tree)
모든 레벨에 노드가 가득차 있는 이진 트리이다. 포화 이진 트리의 노드 개수가 정확히 2^k-1 개여야 한다. 여기서 k는 트리의 높이다. 

![포화 이진 트리](https://github.com/honge7694/honge7694.github.io/assets/76715487/1b37fd2a-455f-49b5-a9ea-74596a102591)

### 완전 이진 트리(Complete Binary Tree)
루트에서 마지막 레벨(깊이)을 제외한 모든 레벨에 노드가 가득 차 있고, 마지막 레벨은 왼쪽부터 노드가 순서대로 채워진 이진 트리이다. 트리의 높이가 h일때 2^(h-1) ~ 2^h-1 개의 노드를 가질 수 있고, 완전 이진 트리는 배열을 사용해 효율적으로 표현이 가능하다.

![완전 이진 트리](https://github.com/honge7694/honge7694.github.io/assets/76715487/36fd4cad-ec8c-4004-8f0c-dee459f89340)
높이 k인 완전 이진트리는 레벨 k−2까지는 모든 노드가 2개의 자식을 가진 포화 이진트리로 구성    
단, 마지막 레벨 k-1에 풀로 채워져 있지 않더라도 왼쪽부터 노드가 순차적으로 채워져 있다면
완전 이진 트리이다.

## 이진 트리 코드

```python
class Node:
	def __init__(self, val, left=None, right=None):
		self.val = val
		self.left =left
		self.right = right

class Solution:
	def __init__(self):
		self.root = None
		
	def __str__(self):
		if self.root:
			return f"Solution (root={self.root.val})"
		else:
			return "Solution (no root)"
			
	def make_tree_by(self, lst, index):
		parent = None
		
		if len(lst) > index:
			value = lst[index]
			
			if value is None:
				return
			
			parent = Node(value)
			parent.left = self.make_tree_by(lst, 2 * index + 1)
			parent.right = self.make_tree_by(lst, 2 * index + 2)
			
		if index == 0:
			self.root = parent
		
		return parent
	
lst = [1, 2, 3, 4, 5]

s1 = Solution()
s1.make_tree_by(lst, 0)
print(s1)
```

`parent.left = self.make_tree_by(lst, 2 * index + 1)`    
`parent.right = self.make_tree_by(lst, 2 * index + 2)`     

위 두개의 코드에 왜 left는 `index + 1` right는 `index + 2`가 붙냐면, 리스트 [1, 2, 3, None, None, 6, 7]이 있으면 아래의 그림처럼 되어 자식 노드는 부모 노드의 왼쪽이면 인덱스가  `2 * index + 1`, 오른쪽이면 인덱스가  `2* index + 2` 값을 갖기 때문에 위의 식이 나온다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/92d86dae-a6ab-4731-95a0-bbd0983db195)

## 참고

+ [inyoung_pang velog - 트리](https://velog.io/@inyong_pang/17%EA%B0%95-%ED%8A%B8%EB%A6%ACTrees)
+ [inyoung_pang velog - 이진 트리](https://velog.io/@inyong_pang/18%EA%B0%95-%EC%9D%B4%EC%A7%84-%ED%8A%B8%EB%A6%ACBinary-Trees)
+ [metterian velog - 트리](https://velog.io/@metterian/Day2-%ED%8A%B8%EB%A6%ACTrees)