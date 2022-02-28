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



어떤 issue를 해결해야 해서 

