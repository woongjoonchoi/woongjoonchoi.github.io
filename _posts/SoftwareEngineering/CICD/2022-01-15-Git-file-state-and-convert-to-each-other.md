## Git file state and convert to each other

저는 여태까지 git의 파일 상태를 commit 된 상태 commit이 되지 않는 상태로만 구분을 해왔습니다. 그리고 , 실제로 version의 파일들을 그렇게 관리해왔습니다. 하지만, git 의 파일들은 세분화된 상태들로 존재합니다. 사실, commit이 된 파일, commit이되지 않는 파일 두 분류로 만 구분하여 VCS(Version Control System) 을 사용해도 문제가 없을 수 있습니다. 하지만, 좀 더 detail하게 안다면 VCS를 좀 더 상황에 맞게 잘 사용할 수 있을거라 확신합니다. 오늘은 Git Pro에서의 File state 내용을 공부하고 closed book으로 작성후 , 부족한 내용을 보완했습니다. File State에 대해 자세히 설명해보도록 하겠습니다. 



## Git File State

![image](https://user-images.githubusercontent.com/50165842/149623044-613f5ea0-afe8-48bc-9724-31c8b46b6bef.png)

git 의 파일 상태는 크게 UNTRACKED, TRACKED 로 나뉜다.(이 의미는 추후 자세히 설명하겠다.) TRACKED는 git이 관리하는 파일 ,즉 이미 스냅샷에 들어가있던 파일입니다.  UNTRACKED 는 git 이 관리하지 않는 파일을 의미합니다. 이 TRACKED 에서 다시 MODIFIED, UNMODIFIED , STAGED 로 나뉩니다. UNTRACKED는 워킹 디렉토리의 파일 중에서도 Staging Area에 포함되지 않았던 파일 입니다.Git을 처음 Clone하면 파일들은 UNMODIFIED 상태입니다 . 왜냐하면, 처음 checkout  후 , 아무것도 수정이 안되었기 때문입니다. 이를 , 수정하면 MODIFIED 상태가 됩니다. MODIFIED 는 가장 최신의 버전과 비교했을 때  수정이 되었다는 것을 의미하고, UNMODIFIED 는 가장 최신의 버전과 동일함을 의미합니다.그리고, 이 MODIFIED FILE을 staged 상태로 만들어 줄 수 있습니다.  STAGED 는 git 서버에 저장하기 직전의 상태를 말합니다. STAGED 가 되면 STAGED 상태의 file을 commit하게 됩니다. 

### git status

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

`git status` 명령어로  file의 상태를 체크할 수 있습니다.  위 내용은 아무것도 수정이 되지 않았음을 의미합니다.

```bash
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)
  README
nothing added to commit but untracked files present (use "git add" to
track)

```

`README` 파일을 새로 추가하면,  UNTRACKED FILE에 `README`가 추가되었음 알 수 있습니다. 위에서, TRACKED 상태는 스냅샷에 들어가 있던 파일을 의미한다고 했습니다. UNTRACKED 는 스냅샷에 들어가 있지 않는 상태를 의미합니다.

### git add

#### UNTRCKED FILE -> STAGING AREA

```
$ git add README

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
```

`Changes to be committed` 라고 되어있는데, 이는 아래의 파일이 STAGED 상태가 되었음을 의미합니다.

git add 명령어는 이제부터 이 파일을 추적하기(TRACKED) 시작하겠다 라는 명령어입니다. git add를 사용하면 파일이 STAGING AREA에 올라가게 됩니다. argument로 file 혹은 directory를 줄 수 있는데 , directory를 주게 되면 재귀적으로 모든 파일들을 STAGING AREA에 올려 놓습니다.



#### MODIFIED FILE

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

`Changes not staged for commit` 은 TRACKED 는 되고 있는데, 변경사항이 있고 , 아직 STAGED 상태가 아님을 의미합니다 .즉, MODIFIED 된 상태를 의미합니다.  

git add 명령어는 MODIFIED FILE을 STAGED 상태로 만들 때도 사용합니다.



#### git add의 다른 용도

git add는 merge 할 때 일어나는 conflict를 해결할 때도 사용합니다. 왜냐하면, merge를 하고 commit을 할 것이기 때문입니다 .



### git add 후 modified

이번에는 `CONTRIBUTING.md` 를 git add 명령어로 STAGING AREA에 올려보도록 하겠습니다.

```bash
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
  modified: CONTRIBUTING.md
```

이를 수정하고 다시 file의 상태를 확인해보도록 하겠습니다. 

```bash
$ vim CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  new file: README
  modified: CONTRIBUTING.md
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

 `CONTRIBUTING.md` 파일이 STAGED 이면서 MODIFIED 이기도 한 것을 확인할 수 있습니다. 이 시점에서 COMMIT을 실행하게 되면, 마지막 add 시점의 파일이 커밋이 됩니다.  만약에, 수정한 파일을 commit하고 싶으면 git add를 실행해서 MODIFIED 파일을 STAGED 상태로변환해주어야 합니다 .



### git diff

git status를 사용하면 단순히 파일이 MODIFIED되었다는 사실만을 보여줍니다. 어떤 내용이 구체적으로 바뀌었는지를 확인할려면 git diff을 사용해야 합니다. git diff은  STAGED AREA에 들어가기전 MODIFIED 상태의  파일과 STAGED AREA에 들어간 후 의 내용의 차이점을 보여줍니다.

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  modified: README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

git diff을 사용해보도록 하겠습니다. 

```bash
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your
PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your
change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your
patch is
+longer than a dozen lines.
 If you are starting to work on a particular area, feel free to submit a
PR
 that highlights your work in progress (and note in the PR title that it's
```

이 명령은 WORKING DIRECTORY(MODIFIED) 에 있는 파일과 STAGED AREA에 있는 파일을 비교 합니다. 만약에, 이미 파일을 STAGING AREA에 넣어놔서 COMMIT 파일과 STAGING AREA의 파일을 비교할려면 `git diff --staged`를 사용합니다.

```bash
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

git diff은 마지막 commit 후의 수정한 것들을 전부 보여주지 않습니다. Unstaged에 있는 수정된 부분만을 보여준다는 것을 알아야합니다. 

#### 외부 tool

외부 도구로 비교하기 이 책에서는 계속 git diff 명령으로 여기저기서 써 먹는다. 즐겨 쓰거나 결과를 아름답게 보여주는 Diff 도구가 있으면 사용할 수 있다. git diff 대신 git difftool 명령을 사용해서 emerge, vimdiff 같은 도구로 비교할 수 있다. 상용 제품도 사용할 수 있다. git difftool --tool-help 라는 명령은 사용가능한 도구를 보여준다.



## Git file state revert

어떤 파일을 Commit 했는데 조그만 오타가 나서 수정을 해야 하는 경우가 생길수 도 있습니다. 그럴 때는 , 저는 보통 새로 커밋을 했는데 , 아래에서 나오는 명령어들을 사용하면 file의 상태를 변경할 수 있습니다.

### amend

가끔 어떤 사소한 내용들을 빼먹고 커밋을 했을 때 , 내용들을 추가해서 commit을 해야하는 경우가 있습니다. 그 경우 , 파일을 수정해서 STAGING AREA에 올린후 , git commit --amend 를 사용합니다. git commit --amend를 사용하면 새로 STAGED 상태가 된 파일들을 마지막 COMMIT 과 합쳐줍니다.

amend 옵션은 이전의 커밋 히스토리를 새로운 커밋 히스토리로 만드는 과정입니다. 따라서, 이전에 커밋했던 내용들은 사라지고, 새롭게 합쳐진 내용들만 커밋로그에 남게 됩니다. 

#### 응용 

저는 이를 blog post할 때 응용을 해볼려 했습니다. blog post를 github으로 하는데, 실수로 날짜를 잘못적거나 ,오타를 내면, 이를 git commit --amend 로 한번에 커밋 로그를 합치면 좋겠다고 생각했습니다. 하지만, github는 remote repository기에 제가 remote repository 에 commit log를 수정하는 방법을 모르고 , local의 commit log만 수정하는 방법만을 알기에 실패하였습니다.

### reset

이번에는 STAGED AREA에 있는 파일을 UNSTAGED(WORKING DIRECTORY)파일로  변환하는 방법을 알아보도록 하겠습니다.

파일을 두 개 수정한 다음에,  한 개만 STAGED 상태로 만들려 했는데, 실수로 gid add * 로 두 개다 STAGED 상태로 만들었다고 가정하겠습니다.

```bash
$ git add *
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README
  modified: CONTRIBUTING.md
  
```

`git reset HEAD <file>` 명령어를 사용해보도록 하겠습니다.



```
$ git reset HEAD CONTRIBUTING.md
Unstaged changes after reset:
M CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md
```

git reset HEAD <파일이름>을 사용하게 되면 HEAD 가리키는 commit 시점에서의 file을 UNSTAGED 상태로 만들어줍니다.

하지만 ,git reset 명령어는 사용할 떄  주의를 기울여야 합니다. --hard 옵션과 함께 사용하면 정말 위험합니다. 하지만, 위에서 처럼 옵션없이 사용하면 워킹디렉토리의 파일은 건들지 않습니다. (git reset 은 나중에 추후 다루도록 하겠습니다.)



### git checkout --

어떻게 하면 MODIFIED 상태의 파일을 되돌릴 수 있을까요? (가장 최근의 COMMIT 버전으로 되돌릴 수 있을까요?)

git status가 친절하게 알려줍니다.

```bash
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)
  modified: CONTRIBUTING.md

```

이대로 실행해보도록 하겠습니다.

```bash
$ git checkout -- CONTRIBUTING.md
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  renamed: README.md -> README

```

파일이 MODIFIED 이전 상태로 돌아갔음을 확인할 수 있습니다 .

git checkout 도 굉장히 위험한 명령어입니다. git은 STAGED AREA에 올라가서 커밋이 된 것들을 복구할 수가 있습니다. 하지만, STAGED AREA에 올리지 않은 것들은 한번 삭제 하면 복구할 수 가 없습니다. (나중에 추후 다루도록 하겠습니다.)정말, 중요하지 않는 내용이라 확신이 든다면 git checkout 명령어를 사용하면 됩니다 .