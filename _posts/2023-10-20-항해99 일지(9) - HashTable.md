---
title: 항해99 일지(9) - HashTable
date: 2023-10-20 23:00:00 +09:00
categories: [DevOps, DataStructure, TIL]
tags: [항해99, TIL, 자료구조, HashTable]
image: /assets/img/posts/항해99.png
---

## 해시 테이블
해시 테이블은 (Key, Value)로 데이터를 배열형태로 저장하는 자료구조 중 하나로 빠르게 데이터를 검색할 수 있는 자료구조이다. 
각 데이터는 고유한 키(Key)를 가지고 있으며, 해시 함수를 사용하여 해당 키를 배열의 인덱스로 변환한다. 이렇게 변환된 인덱스에 데이터를 저장하거나 검색할 수 있다.     
     
쉽게 설명하면 다음과 같다.     
#### 1. 고유한 키와 데이터의 저장
각 데이터는 고유한 키와 값을 가진다. 이 키는 데이터를 식별하는 역할을 한다.
#### 2. 해시 함수 활용
해시 함수는 데이터의 키를 받아서 고유한 인덱스를 생성한다. 이 인덱스를 통해 데이터가 배열의 어떤 위치에 저장될지 결정된다.
#### 3. 데이터 저장 및 검색
해시 함수가 생성한 인덱스를 사용하여 데이터를 배열에 저장하거나 검색한다. 이 과정은 매우 빠르게 이루어진다.
#### 4. 버킷 또는 슬롯
데이터가 저장되는 장소를 "버킷" 또는 "슬롯"이라고 부른다. 각 버킷은 하나의 인덱스를 가리키며, 해당 인덱스에 저장된 데이터를 관리한다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/6f15c309-e5eb-4fc5-a73c-8dbd71df2de5)

예를 들어 우리가 (Key, Value)가 ("John Smith", "521-1234")인 데이터를 크기가 16인 해시 테이블에 저장한다고 하자. 그러면 먼저 index = hash_function("John Smith") % 16 연산을 통해 index 값을 계산한다. 그리고 array[index] = "521-1234" 로 전화번호를 저장하게 된다.
이러한 해싱 구조로 데이터를 저장하면 Key값으로 데이터를 찾을 때 해시 함수를 1번만 수행하면 되므로 매우 빠르게 데이터를 저장/삭제/조회할 수 있다. 해시테이블의 평균 시간복잡도는 O(1)이며, 공간복잡도는 O(n)인데 여기서 n은 키의 개수이다.    

### 해시란?
단방향 암호화 기법으로 **해시 함수를 이용**하여 임의의 크기의 데이터를 고정된 크기의 고유한 값 또는 해시 코드(또는 해시 값)로 변환하는 과정을 의미한다. 이때, 원래 데이터를 "키(key)"라고 하고, 해시 함수를 통해 얻어진 값을 "해시 값(hash value)" 또는 "해시 코드(hash code)"라고 한다.    

### 해시 함수
 해시 함수는 위에서 말한 것처럼 임의의 크기의 데이터를 입력으로 받아 고정된 크기의 해시 코드(또는 해시 값)를 생성 후 데이터를 버킷에 할당하는 데 사용된다.     
     
## 해시값이 충돌하는 경우
해시 함수가 두 개 이상의 서로 다른 입력 데이터에 대해 동일한 해시 값을 생성하는 상황을 의미한다. 예를들어 "John Smith"를 해시 함수에 돌려 나온 값과 "Sandra Dee"를 해시 함수에 돌려 나온 값이 동일(중복된 인덱스 값이 생기는 경우)하다면 어떻게 해야 할까?   
    
## 해시 충돌 해결 방법

### 분리 연결법(Separate Chaining)
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/fdb3579b-b8fa-4e41-a0c8-6173b6799fc9)
각 버킷(슬롯)에 연결 리스트(링크드 리스트)를 사용하여 충돌된 항목을 저장한다. 동일한 버킷에 여러 항목이 존재할 수 있으며, 동일한 해시 코드를 가진 항목은 리스트에 추가된다.

### 개방 주소법 (Open Addressing)
충돌이 발생하면 다른 버킷(슬롯)을 검색하여 데이터를 저장하는 방식이다. 선형 탐사, 이차 탐사, 이중 해시 등의 방법이 있다. 이 방법은 메모리를 효율적으로 사용할 수 있지만 충돌이 발생할 경우 데이터를 다른 위치로 이동시키는 작업이 필요하다.

#### 이중 해시(Double Hashing)

![이중해시 이미지](https://github.com/honge7694/honge7694.github.io/assets/76715487/40264fc4-59ab-4fb4-b002-5b5fb7857db1)

충돌이 발생할 경우 두 번째 해시 함수를 사용하여 데이터를 다른 위치로 이동


#### 선형 탐사 (Linear Probing)

![선형탐사 이미지](https://github.com/honge7694/honge7694.github.io/assets/76715487/20f2f678-8ea7-49a9-8791-33ac4509ba77)

충돌이 발생하면 다음 슬롯을 검색하여 데이터를 저장한다. 이 과정을 반복하며 빈 슬롯을 찾을 때까지 진행한다. 충돌이 발생할 때 데이터를 연속적으로 저장하므로 클러스터링(Clustering) 문제가 발생할 수 있다.    
    
#### 이차 탐사 (Quadratic Probing)

![이차탐사 이미지](https://github.com/honge7694/honge7694.github.io/assets/76715487/987a47dd-2760-446a-8a45-ed70a43d2ab6)

충돌이 발생하면 제곱 수를 더해 다음 슬롯을 검색하여 데이터를 저장한다. 이 방법은 선형 탐사의 클러스터링 문제를 완화할 수 있다.     
> 제곱수     
> 1, 4, 9, 16, 25, ...

## 파이썬 코드 - 개방 주소법
```python
class MyHashMap:
    # 초기화
    def __init__(self):
        # 기본 사이즈 설정
        self.size = 1000000
        
        # 존재하지 않는 키를 조회할 경우 자동으로 기본 딕셔너리를 생성해주는 defaultdict 사용 -> 왜?
        self.table = collections.defaultdict(ListNode)

    def put(self, key: int, value: int) -> None:
        index = key % self.size # 테이블의 인덱스, 모듈로 연산
		
        # defaultdict 생성, collections.defaultdict(ListNode)로 생성했기 때문에
        # 해당 인덱스와 매칭되는 value에 빈 ListNode가 생성됨
        if self.table[index].value is None: 
            self.table[index] = ListNode(key, value) # 비어있는 공간에 요소 추가
            return

        p = self.table[index]
        while p: # 인덱스의 첫번째 값

            # 종료 조건 1 - 테이블에 노드가 있을 때
            # 키 값이 같은 노드 찾기
            if p.key == key:
                # 키 값이 같은 노드에 값 업데이트 후 종료
                p.value = value
                return
            # 종료조건 2 - 다음 노드에 값이 없을 때
            # 이 코드가 없으면 p = p.next가 됐을 때, while문을 탈출하고, p.next에는 아무것도 없기 때문에
            # 다음 코드(p.next = ListNode(key, value))로 넘어갔을 때 에러가 난다.
            if p.next is None:
                break
            p = p.next

        p.next = ListNode(key, value)


    def get(self, key: int) -> int:
        index = key % self.size
        # 찾을 키가 없으면 -1 리턴
        if self.table[index].value is None:
            return -1

        p = self.table[index]
        while p:
            # 키 값과 매칭되는 요소 찾기
            if p.key == key:
                return p.value
            p = p.next

        # 찾아봤는데도 없다면 -1 리턴
        return -1

    def remove(self, key: int) -> None:
        index = key % self.size
        if self.table[index].value is None:
            return

        p = self.table[index]

        # 인덱스의 첫번째 노드일 때. while로 p.next를 해주지 않았으니 첫번째 노드만 비교한다.
        if p.key == key:
            # 인덱스에 들어있는 노드가 하나뿐이면 해당 노드를 빈 노드로 바꿔준다.
            if p.next is None: # p.next가 None이라는 말이 첫번째 노드라는 말과 같다.
                # 빈 노드로 바꿔주는 이유? None으로 바꾸면 if self.table[index].value is None 코드에서 table[index] 자체가 None이기 때문에 .value를 수행할 수 없어서 에러가 남
                # 해시 테이블 객체를 생성하거나 추가하려는데 키 값이 없어서 if self.table[index].value is None 조건문을 탈 때에 self.table[index]는 자동으로 ListNode()로 생성되기 때문에
                # 이 조건문에서 현재의 노드를 처음 상태로 되돌려주는 것이다.
                self.table[index] = ListNode()
                return


        # 연결 리스트 노드 삭제
        prev = p
        while p:
            # 해당 키를 검색.
            if p.key == key:
                # 현재 노드의 다음노드와 현재 노드의 이전 노드를 연결해 줌으로써 현재노드의 연결을 끊는다. 왜 이렇게 되나요? 첫번째 노드는 이미 위에서 걸러지고,
                # 2번째부터는 값이 있다고 했을 때, prev에는 반복문 돌기 전 현재노드 즉, 다음 반복문에서는 이전노드가 담기고 p에는 다음 노드가 담기기 때문이다.
                prev.next = p.next
            prev = p
            p = p.next

# 개별 체이닝 방식을 이용하기 위해 연결 리스트 클래스 구현
class ListNode:
    def __init__(self, key=None, value=None):
        self.key = key
        self.value = value
        self.next = None
```




## 참고
- [노마드 코더 강의](https://www.youtube.com/watch?v=HraOg7W3VAM&t=281s)
- [블로그 참고](https://mangkyu.tistory.com/102)
- [해쉬 테이블 이미지](https://ko.wikipedia.org/wiki/%ED%95%B4%EC%8B%9C_%ED%85%8C%EC%9D%B4%EB%B8%94)
- [충돌 해결 이미지](https://courses.cs.washington.edu/courses/cse326/00wi/handouts/lecture16/sld025.htm)




