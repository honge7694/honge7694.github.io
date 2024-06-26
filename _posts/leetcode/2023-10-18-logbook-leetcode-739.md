---
title: 일일온도 - leetcode 739번
date: 2023-10-18 23:03:00 +09:00
categories: [IT, Algorithm]
tags: [ til, algorithm, stack, leetcode]
image: /assets/img/posts/algorithm.png
---

## [739. 일일온도(Daily Temperatures)](https://leetcode.com/problems/daily-temperatures/description/)

일일 기온을 나타내는 정수 배열 temperatures가 있다.이 정수배열을 순회하면서 더 따듯한 기온을 얻기 위해 그 날 이후 기다려야 하는 일수를 answer 배열로 반환한다. 이것이 가능한 미래의 날이 없다면 0을 포함한다.

Example 1:
> Input: temperatures = [73,74,75,71,69,72,76,73]    
> Output: [1,1,4,2,1,1,0,0]

Example 2:
> Input: temperatures = [30,40,50,60]    
> Output: [1,1,1,0]

Example 3:
> Input: temperatures = [30,60,90]    
> Output: [1,1,0]

## 접근방법
1. 2중 for문을 이용하여 하나를 고정해두고 다음 것을 돌면서하면 될 것 같았다.
이렇게 풀경우 예제는 풀리지만 제출에서는 풀리지 않았다. 너무 많은 시간이 걸리기 때문이다. (O(n²))

```python
def dailyTemperatures(temperatures):
        """
        :type temperatures: List[int]
        :rtype: List[int]
        """
        result = []
        for i in range(len(temperatures)):
            count = 0
            for j in range(i+1, len(temperatures)+1):
                count += 1
                if j == len(temperatures):
                    result.append(0)
                    break
                elif temperatures[i] < temperatures[j]:
                    result.append(count)
                    break
        return result
```

## 풀이

```python
def dailyTemperatures(self, temperatures):
	result = [0] * len(temperatures)  # 결과 리스트를 0으로 초기화
	stack = []  # 온도를 저장할 스택

	for i in range(len(temperatures)):
		# 스택이 비어있지 않고 현재 온도가 스택의 가장 위에 있는 온도보다 높다면
		while stack and temperatures[i] > temperatures[stack[-1]]:
			j = stack.pop()  # 스택에서 온도 인덱스를 꺼내서
			result[j] = i - j # 결과를 업데이트
		stack.append(i) # 현재 온도 인덱스를 스택에 추가
```

풀이의 순서를 보면,
1. 반복문을 돌며, while문 조건에 안맞으면 온도 인덱스를 stack을 쌓는다.
2. 현재온도와 스택에 상단에 있는 온도인덱스를 꺼내 비교한다.
3. while문을 통과한 스택에서 꺼내 결과를 나타낸다.
    
이 풀이도 for 안에 while이 있어 O(n²)의 시간복잡도를 갖는 줄 알았지만, while문을 한번만 순회하기때문에 O(n)이라는 시간복잡도를 갖는것이였다.
이중 for문을 사용하고 있다면 웬만해서는 잘 못 풀고 있다는 것을 명심해야겠다.
