---
title: 항해99 일지(2) - 크롤링이란?
date: 2023-10-07 22:22:00 +09:00
categories: [IT, TIL]
tags: [항해99, til, 크롤링, javascript, jquery]
image: /assets/img/posts/logbook99.png
---


## 크롤링 (Crawling)
> 분석하기 쉽고 활용하기 쉽게 끔 데이터를 수집하는 행위를 크롤링(Crawling)이라고 한다.    
> 원하는 데이터를 추출하는 스크래핑(Scraping)과 개념이 혼동되기도 한다.    
> 페이지 안에 있는 데이터를 추출해서 가공하는게 대부분 최종 목표로 결국 **크롤링 → 스크래핑**의 과정이 된다.

진행시 설치해야 할 라이브러리
```bash
pip install requests bs4
pip install requests
```

<br/>

### 자주 쓰이는 크롤링 선택자

#### 태그 선택
`soup.select('태그명')`를 사용하여 지정한 HTML 태그를 선택한다.
```python
soup.select('a') # 모든 링크 태그를 선택한다.
```

#### 클래스 선택
`soup.select('.클래스명')`를 사용하여 지정한 클래스 이름을 가진 요소를 선택한다.
```python
soup.select('.title') # 모든 class="title"인 요소를 선택한다.
```

#### 아이디 선택
`soup.select('#아이디명')`를 사용하여 지정한 아이디를 가진 요소를 선택한다.
```python
soup.select('#header') # id="header"인 요소를 선택한다.
```

#### 자식 요소 선택
`soup.select('parent.클래스명 > child.클래스명')` 또는 `soup.select('parent > child')`를 사용하여 지정한 부모 요소의 자식 요소를 선택한다.
```python
soup.select('ul > li') # 모든 ul 태그의 자식 li 태그를 선택한다.
```

#### 속성 선택
`soup.select('[attribute=value]')`를 사용하여 지정한 속성과 값이 일치하는 요소를 선택한다.
```python
# href 속성이 "https://example.com"인 요소를 선택한다.
soup.select('[href="https://example.com"]') 
```

#### N번째 요소 선택
`soup.select('tag')[n]`을 사용하여 선택한 요소 중에서 N번째 요소를 선택한다.
```python
soup.select('a')[0] # 첫 번째 링크를 선택한다.
```


#### csS 선택자 조합
여러 csS 선택자를 조합하여 복잡한 선택 조건을 만들 수 있다.
```python
# div 클래스가 "header"인 요소 내부의 h1 태그 중 클래스가 "title"인 요소를 선택한다.
soup.select('div.header > h1.title') 
```

#### .contents
Beautiful Soup 객체에서 특정 태그의 자식 요소들을 가져오는 데 사용된다.
이 속성을 사용하면 특정 태그의 모든 자식 요소를 리스트 형태로 반환된다.
```python
# h1.title 태그를 선택하고, 해당 태그의 자식 요소들을 .contents를 사용하여 가져올 수 있다.
soup.select('div.header > h1.title') 
```

#### .select_one()
Beautiful Soup 객체에서 특정 csS 선택자로 선택한 첫 번째 요소를 가져오는 데 사용된다.
이 메서드는 하나의 요소만 반환하며, 여러 요소가 일치하는 경우 첫 번째 요소를 반환한다.
```python
# h1.title 태그 중에서 첫 번째로 일치하는 요소를 선택한다.
soup.select_one('div.header > h1.title') 
```

<br/>

### 예제

```python
import requests
from bs4 import BeautifulSoup

# 검색어 설정
query = "날씨"

# 네이버 검색 결과 페이지 URL
url = f"https://search.naver.com/search.naver?query={query}"
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36'}

# HTTP GET 요청 보내고 응답 받기
response = requests.get(url, headers=headers)

# HTTP 요청이 성공적인지 확인
if response.status_code == 200:
    # HTML 문서 파싱
    soup = BeautifulSoup(response.text, 'html.parser')

    temp = soup.select_one('.temperature_text > strong')
    temp_contents = soup.select_one('.temperature_text > strong').contents

    # 검색 결과 출력
    print(f'temp : {temp}')
    print(f'temp_contents : {temp_contents}')
else:
    print("HTTP 요청 실패")
	
# 결과
temp : <strong><span class="blind">현재 온도</span>17.7<span class="celsius">°</span></strong>
temp_contents : [<span class="blind">현재 온도</span>, '17.7', <span class="celsius">°</span>]
```
<br/>

### 파싱과 파서

#### 파싱
> 파싱은 주어진 데이터를 읽어서 해당 데이터의 구조를 이해하고 **필요한 정보를 추출하는 과정**이다. 데이터를 해석하고 분석하여 프로그램이나 애플리케이션이 이해할 수 있는 형태로 변환하는 것을 의미한다.    
예를 들어, HTML 문서를 파싱하면 웹 페이지의 구조를 이해하고 텍스트, 링크, 이미지 등을 추출할 수 있다.    

#### 파서   
> 파서는 **주어진 데이터를 읽고 해석**하는 소프트웨어나 라이브러리이다.
주로 구조화된 데이터 형식을 분석하고 처리하는 데 사용된다. 예를 들어, HTML, XML, JSON과 같은 문서를 읽고 해당 문서의 구조를 이해하며 데이터를 추출하는 데 파서가 사용된다.



















