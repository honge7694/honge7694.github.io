---
title: "[Git] To push the history leading to the current (detached HEAD)"
date: 2023-10-26 14:00:00 +09:00
categories: [IT, Git, TroubleShooting]
tags: [til, git, troubleshooting]
image: /assets/img/posts/trouble-shooting.png
---


## 에러 상황
`git push`를 할 때마다 나타나는 에러이다. `git checkout`으로 지난 커밋내역으로 이동했다가 이동한 커밋부터 수정했더니 생긴 에러다. 새로운 커밋을 만나면 항상 HEAD와 main이 함께 있지 못하게 되어 에러가 나타나는 것이다.  `git push origin HEAD:<name-of-remote-branch>`하면 `git push`는 되는데 push를 할 때마다 나타나니 해결방법을 알아보자.

#### git log 상태
아래와 같이 새로운 커밋이 들어오면 origin/main이 HEAD로 따라오지 못한다.
```shell
D:\gitblog\git-blog>git log
commit 86a0aa03530a441ec215bc4ba8edc5f109c50c4c (HEAD)
Author:
Date:

commit a05d6d9f4a0cd683c48b7daab05bb8ae2d7f7643 (origin/main)
Author:
Date:
```

#### git push 에러

```shell
D:\gitblog\git-blog>git push
fatal: You are not currently on a branch.
To push the history leading to the current (detached HEAD)
state now, use

    git push origin HEAD:<name-of-remote-branch>
```

## 해결 방법
수정된 코드를 커밋한 후, branch를 새로 생성하자.
```shell
git branch temp 
git checkout temp
```
위 명령어를 이용하여 브랜치를 만든 후 이동하거나 또는, `git checkout -b temp`하여 생성 후 바로 이동이 가능하다. 

```shell
git branch -f main temp
git checkout main
```
위 명령을 실행하면 main 브랜치가 temp 브랜치의 가장 최신 커밋을 가리키도록 변경된다. 이것은 주로 브랜치를 다시 기준 브랜치로 설정하거나 특정 커밋 이후의 상태로 이동할 때 사용된다. 강제로 브랜치를 이동시키기 때문에 주의가 필요하며, 커밋을 잃을 수 있으므로 신중하게 사용해야 한다.

```shell
D:\gitblog\git-blog>git checkout main
Switched to branch 'main'
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)
```
이런 메시지가 나오면 성공!!

```shell
git branch -d temp
git push
```
만든 임시 브랜치는 지워주고 새로운 변경사항을 푸시해주면 완료이다.


## 참고
+ [git push가 안 될 때 / detached HEAD 문제 해결 방법](https://aroma-dev.tistory.com/4)