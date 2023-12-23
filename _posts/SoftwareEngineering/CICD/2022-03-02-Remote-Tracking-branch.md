---
title : "Remote Branch:origin/master는 무엇일까요? "

excerpt: "git remote server"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---
협업을 하게 된다면 , 필수적으로 리모트 저장소를 이용해서 협업을 하게 됩니다. 리모트 서버로 대표적인 예시가 github가 있습니다. github를 사용을 많이 해보았지만, github  서버에 반영을 할 때, 어떻게 반영이 될지는 정확히 알지 못했습니다. 그저, push,pull 명령어를 사용해서 오류가 안나면 괜찮다고 생각했습니다. github 서버에 update를 할 때 , 어떻게 update가 되는지 알고 그리고 github 서버에서 local에 반영할 때 어떻게 반영이 되는지 안다면 좀 더 나은 협업을 할 수 있을 것입니다.(Reference는 블로그 하단 참조)(progit의 Remote Tracking branch part를 읽고 정리해보았습니다.)



# Remote Tracking branch

## Property

리모트 브랜치는 리모트 서버에 있는 브랜치를 의미합니다. 리모트 서버에 있는 repository를 local에 반영했을때 , 리모트 브랜치를 tracking 하는 리모트 트래킹 브랜치가 자동으로 생성됩니다. 

이 리모트 트래킹 브랜치는 아래와 같은 특징을 갖습니다.

1. 임의로 이동할 수 없다.
2. `<remote><branch>` 라는 이름을 갖는다.

`1` 을 설명하자면, 리모트 트래킹 브랜치는 가장 최근에 리모트 저장소와 연결되었던 시점의 data를 반영합니다. 왜냐하면 , 리모트 트래킹 브랜치는 리모트 저장소의 브랜치를 추적하는 레퍼런스입니다. 레퍼런스이기에 리모트 저장소가 update 됨에 따라 리모트 트래킹 브랜치가 움직입니다. 브랜치가 움직인 시점은 가장 최근에 저장소와 연결되었던 시점입니다. 

`2` 를 설명하기 위해 예시를 들어보겠습니다. 예를 들어 , 어떤 이슈가 생겨서 제가 iss12 브랜치를 서버에 push를 했습니다. 저도, iss12 브랜치를 local에 가지고 있습니다. 그러면, 서버에서 iss12가 가리키는 브랜치는 local에서 `origin/iss12` 가 됩니다.

참고로 말하자면, origin이라는 이름에는 별 의미가 없습니다. 처음에 , remote 저장소를 clone할 때 , default 값이 origin입니다. 





## Scenario

progit에 scenario를 잘 설명해서 이를 가지고 설명해보도록 하겠습니다.





![image](https://user-images.githubusercontent.com/50165842/155986736-bb4396dc-6b7b-4be9-8942-3b5780024e4d.png)



git clone을 하게 되면 git.ourcomapany.com 에 있는 remote저장소를 local에 다운로드 받게 됩니다. 따라서,   local 에 remote 트래킹 브랜치가`orign/master` 가 되고 ,  local에 `master` 라는 브랜치가 생깁니다. 

![image](https://user-images.githubusercontent.com/50165842/155987281-14dcacf6-0817-431e-b4f1-bd113ff2811e.png)

local에 있는 `master` 브랜치에서 작업을 하고 commit을 합니다.  local의 `master` 브랜치가 이동을 하게 됩니다. 

다른 팀원이 remote repository에 push를 하게 됩니다. remote repository의 `master` 브랜치가 이동을 하게 됩니다. 다른 팀원과 서버의 repository 와 저의 repository는 달라지게 됩니다. remote repository와 local의 repository는 최신 정보를 update하지 않는 한 독립적이기에 , 다른 state를 가질 수 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/155987816-1b7491f1-2b42-4d8b-a418-57f22427ccbd.png)

local에서 remote 서버의 repository를 반영하기 위해서 git fetch origin을 사용해보도록 하겠습니다. f4265 commit을 기준으로 2개의 commit이 local에 반영이 됩니다.  `origin/master`  를 이동시킵니다. 즉, local에 없는 새로운 정보를 받아서 local에 update하고, 리모트 브랜치를 가장 최근 위치로 이동시킵니다.  



![image](https://user-images.githubusercontent.com/50165842/155988403-b8a514a3-1475-4501-b6b9-7336bea1790a.png)





단순히 로컬  , 메인 저장소로 나누기 보다 1개의 저장소를 더 추가해서 애플리케이션을 운영한다고 가정을 해보겠습니다. 그 때 추가하는 저장소를 git.team1.ourcompany.com 이라 하겠습니다. 



![image](https://user-images.githubusercontent.com/50165842/156175544-5cff8002-c388-4e4b-8f8f-3fdf7426014c.png)

새로운 리모트 서버(team1) 에서 최신 데이터를 반영하지만 로컬에 이미 있는 데이터(커밋)이기 때문에 , 커밋로그가 변하지는 않습니다. `teamone/master` 라는 리모트 트래킹 브랜치 한개가 추가가 됩니다. 



# 리모트 서버에 업데이트 하기

git에서 remote 서버에 update하기 위해서 사용하는 command는 바로 `git push` 입니다. 이 command에 대해서 detail하게 알아볼려고 합니다.

## git push	

git push의 general한 format은 `git push <reomte>  <branch>` 입니다. local에 저장되어 있는 remote server의 alias에 local에 있는 브랜치를 remote 서버에 반영하겠다는 의미입니다. 

git의 내부 동작을 간략하게 설명하면,  `git push <remote> master` 에서 master는 `refs/heads/master:refs/heads/master` 를 의미합니다.  이는 , local의 master 라는 이름의 branch를 remote server에 push하는데 remote server의 master라는 브랜치로 update를 한다는 의미입니다. 따라서, 이는 `git push <remote>  master:master` 라는 command로 다시 쓰일 수 있습니다 .  이는 local 브랜치의 이름과 update하고자 하는 remote server의 브랜치의 이름이 다를때 사용할 수 있습니다.  예를 들어 , `git push <remote> master:dev` 라 하면 "local의 master 브랜치를 리모트 server의 dev라는 브랜치로 update 해라"라는 의미입니다.  

# 리모트 서버에서 받기

git에서 remote 서버에 반영을 하는 법에 대해 알아보았으니, 이번에는 remote 서버에서 받아오는 법을 알아보도록 하겠습니다.

## git fetch

`git fetch <remote>`  command는 remote 서버에서 로컬에 없는 data를 내려받게 됩니다. 이 때 , 리모트 트래킹 브랜치는 임의로 이동할 수 없는 브랜치임을 기억해야 합니다.

리모트 트래킹 브랜치를 현재 브랜치에 merge 할려면 `git merge origin/master` 같은 명령어로 merge를 하면 됩니다. 

새로운 브랜치를 만든다면 `git checkout -b newma origin/master ` 같은 command를 사용할 수 있을 것입니다 .



## 트래킹 브랜치

트래킹 브랜치라는 것은 리모트 트래킹 브랜치를 로컬 브랜치로  checkout 하게 되면 자동으로 생기게 됩니다. 이 트래킹 브랜치에서 pull을 하게 되면 , 자동으로 리모트 저장소로 부터 data를 받아와서 트래킹 브랜치에 merge를 진행하게 됩니다. 

git clone을 하게 되면 자동적으로 리모트 트래킹 브랜치 `origin/master` 와 트래킹 브랜치 `master` 가 생성됩니다.

트래킹 브랜치를  직접 만드는 것도 가능합니다. `git checkout -b <branch> <remote>/<branch>` 명령어를 사용하면 직접 트래킹 브랜치를 만들 수 있습니다. 

`git --track <remote><branch>` 같은 명령어를 사용하여 자동으로 트래킹 브랜치 이름을 생성할 수 있습니다.

입력한 브랜치가 있는 리모트가 하나 있고 , 로컬에는 없으면 `git checkout <branch>` 를 사용하면 마찬가지로 트래킹 브랜치를 생성해줍니다.

현재 브랜치를 트래킹 브랜치로 사용하고 싶으면 `git branch -u <remote><branch>` 명령어를 사용하면 , 현재 브랜치를 트래킹 브랜치로 설정하게 됩니다. 이때 , `-u` 대신에 `--set-upstream-to` 라는 옵션을 사용해도 됩니다. 한개의 리모트 브랜치에 대해서 여러개의 트래킹 브랜치를 설정할 수 있습니다.

트래킹 브랜치로 설정한 브랜치에 대해 트래킹을 취소할려면 `git branch <branch> --unset-upstream` 명령어를 사용하면 됩니다.

트래킹 브랜치에 대한 정보를 보기 위해서는 `git branch -vv` 라는 명령어를 사용하면 됩니다.  이는 로컬 트래킹 브랜치 목록들을 보여주는데 , 로컬 트래킹 브랜치가 앞서가는지 뒤쳐지는지를 보여줍니다. 




## git pull

git pull 명령어에 대해서 주의할 점은 이 명령어가 git fetch + git merge를 합한 동작을 수행하는 명령어라는 것입니다. git fetch는 서버로 부터 로컬에 없는 최신 데이터를 받아옵니다. 그리고, git merge는 이 데이터를 현재 브랜치와 merge를 하게 됩니다. 

트래킹 브랜치가 설정이 되면 git pull 명령어는 리모트 서버로 부터 데이터를 받아와서 리모트 트래킹 브랜치를 업데이트 하고 , 트래킹 브랜치와 리모트 트래킹 브랜치를 merge 하게 됩니다.

명시적인 operation을 위해서 git fetch 후 git merge를 하는것이 책에서는 더 좋다고 추천을 합니다.



# Reference

[Git - 리모트 브랜치 (git-scm.com)](https://git-scm.com/book/ko/v2/Git-브랜치-리모트-브랜치)

