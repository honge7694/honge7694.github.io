---
title: 암호만들기 - baekjoon 1759번
date: 2023-10-24 19:02:00 +09:00
categories: [IT, Algorithm]
tags: [algorithm, 백트래킹, baekjoon]
image: /assets/img/posts/algorithm.png
---

## [1759. 암호 만들기](https://www.acmicpc.net/problem/1759)

C개의 문자를 입력받아 L길이의 암호를 만드는 문제이다. 암호는 오름차순으로 정렬되어야한다.

Example1
> **Input**    
4 6    
a t c i s w    
**Output**    
acis    
acit    
aciw    
acst    
acsw    
actw    
aist    
aisw    
aitw    
astw      
cist    
cisw    
citw    
istw    
    
    
## 나의 풀이

```python
import sys 
sys.setrecursionlimit(10**6) 

def input(): 
	return sys.stdin.readline().strip()

def dfs(index, path):
	if len(path) == L:
		result.append(path)
		return
	
	for i in range(index, C):
		if password[i] not in path:
		    dfs(index+1, path+password[i])

result = []
L, C = map(int, input().split())
password = (input().split())
dfs(0, "")
print(result)
print(len(result))
```

<details>
	<summary>결과 </summary>

<div>
	
> ['acis', 'aciw', 'acsi', 'acsw', 'acwi', 'acws', 'aics', 'aicw', 'aisw', 'aiws', 'asci', 'ascw', 'asiw', 'aswi', 'atci', 'atcs', 'atcw', 'atis', 'atiw', 'atsi', 'atsw', 'atwi', 'atws', 'awci', 'awcs', 'awis', 'awsi', 'cisw', 'ciws', 'csiw', 'cswi', 'ctis', 'ctiw', 'ctsi', 'ctsw', 'ctwi', 'ctws', 'cwis', 'cwsi', 'icsw', 'icws', 'iscw', 'itcs', 'itcw', 'itsw', 'itws', 'iwcs', 'sciw', 'scwi', 'sicw', 'stci', 'stcw', 'stiw', 'stwi', 'swci', 'tcis', 'tciw', 'tcsi', 'tcsw', 'tcwi', 'tcws', 'tics', 'ticw', 'tisw', 'tiws', 'tsci', 'tscw', 'tsiw', 'tswi', 'twci', 'twcs', 'twis', 'twsi', 'wcis', 'wcsi', 'wics', 'wsci', 'wtci', 'wtcs', 'wtis', 'wtsi']


</div>
</details>


암호를 반복하면서, 재귀를 돌리면 결과가 나올 줄 알았지만 풀이 방법이 아닌것 같아 얼른 다른 블로그를 참고하였다.

## 풀이

풀이로 참고한 글에서는 암호조건과 백트래킹 조건을 주었다.

암호 조건
1. 암호에 있는 알파벳을 증가하는 순서로 배열한다.
2. 암호에는 모음 1개 이상, 자음 2개 이상으로 이루어져 있다.

백트래킹
1. 백트래킹 알고리즘을 사용하여 알파벳이 증가하는 순서로 암호를 생성한다.
2. 암호문의 길이가 L이 되면 자음과 모음 개수를 확인한다.
3. 조건이 맞으면 암호문을 출력한다.

### 전체 코드

```python
def check(arr):
    v_count, c_count = 0, 0 # 모음 개수, 자음 개수

    for i in arr:
        if i in vowel:
            v_count += 1
        else:
            c_count += 1

    if v_count >= 1 and c_count >= 2:
        return True
    else:
        return False

def backtracking(arr):
    if len(arr) == L:
        if check(arr):
            print("".join(arr))
            return

    for i in range(len(arr), C):
        if arr[-1] < words[i]:
            arr.append(words[i])
            backtracking(arr)
            arr.pop()

vowel = ['a', 'e', 'i', 'o', 'u']
L, C = map(int, input().split())
words = input().split()
words.sort() 		
			
for i in range(C - L + 1):
    a = [words[i]]
    backtracking(a)
```

### 코드 설명

```python
vowel = ['a', 'e', 'i', 'o', 'u']
L, C = map(int, input().split())
words = input().split()
words.sort() 	
```
모음으로 이루어진 리스트를 선언하고, 주어진 문자들을 정렬한다.

```python
for i in range(C - L + 1):
    a = [words[i]]
    backtracking(a)
```
각 알파벳을 시작으로 backtracking 함수를 호출한다.
이때 암호문의 길이(L)을 만들 수 없는 알파벳은 제외한다.
예제 입력의 a t c i s w에서 정렬(a c i s t w)한 후, s가 첫 단어가 s가 들어가게 되면, 뒤에 's', 't', 'w'밖에 오지 못하므로 C - L + 1까지만 오게한다.

```python
def backtracking(arr):
    if len(arr) == L:
        if check(arr):
            print("".join(arr))
            return

    for i in range(len(arr), C):
        if arr[-1] < words[i]: # 오름차순으로 만들기 위해서
            arr.append(words[i])
            backtracking(arr)
            arr.pop()
```
암호문의 길이L과 배열 arr이 같아지면 check 함수를 통해 모음 개수와, 자음 개수를 확인하여 True와 False를 반환한다.
backtracking(arr)의 재귀함수를 완료하고 난 후, 마지막 문자를 pop하여 다음 문자도 비교할 수 있도록 한다.

예시 )  acis > 재귀통과, pop > aci > acit

```python
def check(arr):
    v_count, c_count = 0, 0 # 모음 개수, 자음 개수

    for i in arr:
        if i in vowel:
            v_count += 1
        else:
            c_count += 1

    if v_count >= 1 and c_count >= 2:
        return True
    else:
        return False
```


## 참고
+ [DFS 재귀를 이용한 글](https://aia1235.tistory.com/71)
+ [itertools combination을 이용한 글](https://jshong1125.tistory.com/16)

