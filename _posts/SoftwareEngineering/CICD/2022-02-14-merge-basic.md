---
title : "git에서의 merge pattern "

excerpt: "branch and merge basic "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---
git에서 feature를 새로 개발하면 , 브랜치를 새로 만들게 됩니다.  보통, 대부분의 개발자라면 이 사실을 잘 알고 있습니다. 하지만, 임의의 두 브랜치의 관계에 pattern이 있다는 사실을 잘 모릅니다. 이 pattern에 따라 merge의 방식도 달라지게 됩니다. 

# Fast-forward pattern

![image](https://user-images.githubusercontent.com/50165842/153867718-67a869a3-4a99-4a6e-9dad-383990a437b1.png)

개발을 하다보면 개발과정에서 issue가 생길 수 있습니다.  이럴 때는 새로운 issue 브랜치를 만들어서 작업을 진행하게 됩니다. 

```
git checkout -b iss53
```

![image](https://user-images.githubusercontent.com/50165842/153867835-8b1e2f79-9daa-415f-bb4f-229deed2c400.png)

서비스를 제공하는 와중에 갑작스런 버그가 발견되어 hotfix를 해야하는 상황이 생겼습니다. 하지만, 아직 issue가 해결이 되지 않았기에 , hotfix를 하는 브랜치와 issue 브랜치가 섞이면 안됩니다. 이럴 때에는 , issue branch에서 master 브랜치로 이동하면 됩니다.

```
$ git checkout master
Switched to branch 'master'

$ git checkout -b hotfix
Switched to a new branch 'hotfix'
$ vim index.html
$ git commit -a -m 'fixed the broken email address'
[hotfix 1fb7853] fixed the broken email address
 1 file changed, 2 insertions(+)


```

![image](https://user-images.githubusercontent.com/50165842/153867936-c958f820-c20d-47d1-911a-ad79f34a4d79.png)

여기서, hotfix 가 완료가 되면 ,hotfix를 최종적으로 production 환경에 배포하기 위해서 합쳐야 합니다. 이 때, merge를 사용합니다. 

```
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)

```



Fast-forward 방식으로 Merge가 되었다고 메세지가 출력됩니다. A브랜치에서 B브랜치로 merge를 하고자 할 때 , B 브랜치가 A브랜치 이후의 커밋을 가리키고 있으면 A 브랜치가 B가 가리키는 커밋으로 이동하게 됩니다. 이러한 패턴을 Fast-forward 패턴이라고 합니다. 

![image](https://user-images.githubusercontent.com/50165842/153868290-65d6e81c-c982-4483-91ff-a1b204e5317f.png)



이제 hotfix 브랜치는 필요가 없어졋으므로 삭제를 하게 됩니다.

```
$ git branch -d hotfix
Deleted branch hotfix (3a0874c).

```



# 3-way-merge pattern

![image](https://user-images.githubusercontent.com/50165842/153869254-67ddcd96-eb51-4f8f-a3af-d0bbdcfb67ae.png)

이제 다시 iss53 브랜치로 돌아가서 issue를 처리하게 됩니다 . 

```
$ git checkout iss53
Switched to branch "iss53"
$ vim index.html
$ git commit -a -m 'finished the new footer [issue 53]'
[iss53 ad82d7a] finished the new footer [issue 53]
1 file changed, 1 insertion(+)
```



현재는 hotfix에서 처리한 내용이 iss53 에 영향을 끼치지 않는다는 것이 중요합니다. issue를 처리하고 나면 바로 master 브랜치를 iss53 브랜치에 merge하게 됩니다. 

```
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html | 1 +
1 file changed, 1 insertion(+)
```

![image](https://user-images.githubusercontent.com/50165842/153869598-5ad24d3a-3fa5-454b-aa55-26e98249e445.png)

이 때 ,iss53 브랜치는 master 브랜치 이후의 커밋을 가리키지 않습니다 .여기서 , merge pattern은  조금 달라지게 됩니다. 우선 두 브랜치의 공통 ancestor를 찾게 됩니다. 공통 anscestor, master , iss53 을 가지고 새로운 commit을 만들어서 iss53 브랜치에 merge하게 됩니다.  3개의 commit을 이용하므로 3-way merge라 부르게 됩니다. 

## merge conflict



![image](https://user-images.githubusercontent.com/50165842/153964680-0996a4ae-a6d0-4866-9e99-b0342720585f.png)



```
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```



3개의 커밋을 이용하여 merge를 만들기 때문에  같은 부분을 수정할 수도 있습니다. 이 때 , 같은 부분을 동시에 수정하게 되면 git은 자동으로 merge를 할 수가 없습니다. 



git status를 이용하여 어떤 부분에서 merge가 되지 않았는지 볼 수 있습니다.

```
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
Unmerged paths:
  (use "git add <file>..." to mark resolution)
  both modified: index.html
no changes added to commit (use "git add" and/or "git commit -a")

```

```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

충돌이 난 부분은 이렇게 표시가 되는데, ==== 위쪽은 HEAD 포인터가 가리키는 부분이고, 아래쪽은 iss53이 가리키는 부분의 파일입니다. 수정을 하고 , <<<<.=====,>>>> 를 지운 다음에  git add 명령어로 다시 git에 저장을 하면 해결이 됩니다. 

```
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)
Changes to be committed:
  modified: index.html

```

conflict가 해결이 되었다고 나옵니다. 이제 git commit을 이용해서 merge를 commit 히스토리에 포함시키면 아래와 같이 커밋 메세지가 작성이 됩니다.

```
Merge branch 'iss53'
Conflicts:
  index.html
#
# It looks like you may be committing a merge.
# If this is not correct, please remove the file
# .git/MERGE_HEAD
# and try again.
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# All conflicts fixed but you are still merging.
#
# Changes to be committed:
# modified: index.html
```

어떻게 충동을 해결했고, 왜 그렇게 했고, 좀 더 확인해야 하는 부분은 무엇인지 기록을 하게 되면 나중에 다시 이 메세지를 보았을 때 이해하기 쉬어집니다. 
