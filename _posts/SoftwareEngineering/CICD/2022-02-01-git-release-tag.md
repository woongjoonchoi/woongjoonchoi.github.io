---
title : "software release를 관리할려면 git에서는 tag를 사용한다. "

excerpt: "tag and release "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

다른  VCS처럼 git도 tag를 지원한다. tag는 software release, 즉 배포 단계에서 사용한다. 예를 들어 , `v.12.4. 패치노트` 처럼 사용할 수 있을 것이다. git에서 이 tag에 대해 지원하는 operation에 대해 알아보도록 하겠다.

## tag 생성

git의 tag에는 2가지 종류가 있다. 하나는 , Annotated tag 이고 다른 하나는 lightweight tag이다.  이 2가지를 어떻게 생성하는지 알아보겠습니다.

### lightweight tag

```
$git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5

```

git에서 lightweight tag를 생성할려면 `git tag <tag name>` 명령어를 사용하면 됩니다.  lightweight tag는 특정 commit에 대한 포인터일 뿐입니다. 따라서 , 임시 tag나 다른 추가적인 정보가 필요없을 경우 사용하게 됩니다.

```
$ git show v1.4-lw
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700
  changed the version number

```

이 tag는 단순히 commit에 대한 정보만을 보여줌을 알 수 있습니다.

### annotated tag

```
$git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

annotated tag를 만들 때 `-a`  옵션을 사용해서 만들 수 있습니다. `-m`  옵션을 사용하면 메세지를 지정할 수 있습니다. 

annotated tag는 태그 저자 , 이메일 , 태그 생성 날짜 , 메세지 등의 정보를 git database에 저장합니다. GPG(GNU privacy guard)로 서명도 할 수 있습니다. 일반적으로 tag를 만들때 , 이 모든 정보를 사용할 수 있게 annotated tag로 만드는 것이 좋습니다. 

```
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date: Sat May 3 20:19:12 2014 -0700
my version 1.4
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date: Mon Mar 17 21:52:11 2008 -0700
  changed the version number
```

Lightweight와는 다르게  다른 정보들을 보여주는 것을 알 수 있습니다.

### tag on past commit

git에서는 commit의 checksum을 알고 있다면, 이전 commit에 대해서 tag를 만들 수 있습니다. `git tag -a <tag이름> <commit체크섬>` 명령어로 만들 수 있습니다.

```
$ git tag -a v1.2 9fceb02
```



## tag 조회

```
$ git tag
v0.1
v1.3

```

tag를 조회할려면 git tag명령어를 사용하면 된다. 이 명령어는 알파벳 순서대로 모든 tag를 보여준다. 굉장히 release가 많이 일어난 프로젝트라면 command line에 전부 표시할 수 없을 것이다.

```
$ git tag -l "v.1.8.5*"
v1.8.5
v1.8.5-rc0
v1.8.5-rc1
v1.8.5-rc2
v1.8.5-rc3
v1.8.5.1
v1.8.5.2
v1.8.5.3
v1.8.5.4
v1.8.5.5
```

`-l` 옵션에 검색패턴 , 즉 regular expression을 사용하면 pattern에 해당하는 tag들을 찾을 수 있다.

## 공유

```
$ git push origin v1.5
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag] v1.5 -> v1.5

```

```
$ git push origin --tags
Counting objects: 1, done.
Writing objects: 100% (1/1), 160 bytes | 0 bytes/s, done.
Total 1 (delta 0), reused 0 (delta 0)
To git@github.com:schacon/simplegit.git
 * [new tag] v1.4 -> v1.4
 * [new tag] v1.4-lw -> v1.4-lw
```



git에서 tag를 생성한다고 해도 리모트서버에 자동으로 반영되지 않습니다. `git push <리모트저장소> <태그이름>` 명령어로 서버에서 push를 해야합니다 .이 명령어는 단일 tag를 push할 수 있습니다. 복수의 tag를 push할려면 `git push <리모트저장소> --tags` 명령어를 사용하면 됩니다. 이 명령어는 리모트 서버에 없는 모든 태그를 서버에 반영하게 됩니다.

## 체크아웃

```
$ git checkout 2.0.0
Note: checking out '2.0.0'.
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.
If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:
  git checkout -b <new-branch>
HEAD is now at 99ada87... Merge pull request #89 from schacon/appendixfinal
$ git checkout 2.0-beta-0.1
Previous HEAD position was 99ada87... Merge pull request #89 from
schacon/appendix-final
HEAD is now at df3f601... add atlas.json and cover imag
```

```
$ git checkout -b version2 v2.0.0
Switched to a new branch 'version2'

```



특정 태그를 체크아웃 할 수 있습니다. 만약에, 브랜치가 가리키고 있는 태그를 체크아웃 하는 것이 아니라면, 현재 작업하는 커밋이 `detached HEAD` 상태가 된다.  `detached HEAD` 상태에서 작업을 하게 되면 , tag는 그대로고 , 새로운 커밋이 하나 쌓이게 됩니다. 하지만, 이 커밋의 hash값을 알고있는 것이 아니라면, 접근할 수가 없습니다. 만약에 , 이 커밋이 의미를 가지는 커밋이라면 브랜치를 만드는 것이 좋습니다.

