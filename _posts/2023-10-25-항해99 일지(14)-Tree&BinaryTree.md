---
title: 항해99 일지(14) - Tree & Binary Tree
date: 2023-10-25 14:00:00 +09:00
categories: [DevOps, TIL, Data Structure]
tags: [항해99, TIL, 자료구조, 이진 트리]
image: /assets/img/posts/항해99.png
---

## 트리란?
계층적인 구조를 나타내는 자료구조로, 순환하는 길이 없는 그래프로 정의한다. 트리는 다양한 종류와 형태가 있지만, 일반적으로 하나의 루트 노드에서 시작하여 각 노드들이 0개 이상의 자식 노드를 가질 수 있는 구조를 갖는다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/4b21698b-414a-4304-916e-e6ce781f6d7f)

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
|레벨(Level)|깊이를 기반으로, 동일한 깊이의 노드들을 레벨이라고 한다.|
|깊이(Depth)|루트 노드에서 어떤 노드까지의 계층적 깊이를 나타낸다. |

<div markdown="1">
	<div style="display: flex; justify-content: space-between;">
		<img src="https://github.com/honge7694/honge7694.github.io/assets/76715487/14b698f2-77ef-449c-b5ad-7716096e39ec" alt="이미지 1" width="50%" />
		<img src="https://github.com/honge7694/honge7694.github.io/assets/76715487/2df9cc4c-87ec-413b-9454-d2f7881a2975" alt="이미지 2" width="50%" />
	</div>
</div> 

## 참고

+ [inyoung_pang velog - 트리](https://velog.io/@inyong_pang/17%EA%B0%95-%ED%8A%B8%EB%A6%ACTrees)
+ [inyoung_pang velog - 이진 트리](https://velog.io/@inyong_pang/18%EA%B0%95-%EC%9D%B4%EC%A7%84-%ED%8A%B8%EB%A6%ACBinary-Trees)
+ [metterian velog - 트리](https://velog.io/@metterian/Day2-%ED%8A%B8%EB%A6%ACTrees)