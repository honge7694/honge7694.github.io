---
title: "[GIT] Error: Gradle script '/home/runner/work/~/gradlew' is not executable. 해결"
date: 2023-11-21 16:00:00 +09:00
categories: [IT, Git]
tags: [TIL, Git, TroubleShooting]
image: /assets/img/posts/TroubleShooting.png
---

## 에러
Git Action을 이용한 CI 테스트 중에 아래와 같은 에러가 발생했다. 
<br/>

`Error: Gradle script '/home/runner/work/~/gradlew' is not executable.`

<br/>
이 오류는 Gradle 빌드 스크립트가 실행 가능하지 않다는 것을 나타낸다. Gradle 빌드 스크립트는 실행 가능한 파일이어야 하므로, 스크립트에 실행 권한이 없거나 Gradle을 실행할 수 없어서 문제가 발생한 것이라고 한다.

## 해결
나는 이 에러를 인텔리제이 터미널에서 해결했다
아래와 같은 명령어를 주어 권한을 주었더니 에러가 사라졌다. ~~하지만 다른 에러가 생기고 있는 중이다.~~

```bash
 git update-index --chmod=+x gradlew
```

## 해결2
다른 방법으로는 gradle.yml의 steps부분에 아래와 같이 작성하는 방법도 있다.
```
	- name: Grant execute permission for gradlew
	run: chmod +x gradlew
```
