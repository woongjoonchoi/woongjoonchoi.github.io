---
title : "커밋은 무엇이고 ,브랜치는 무엇인가? 약간 디테일하게 "

excerpt: "Commit and Branch "

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Git,VCS,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

git을 사용한다면 , 브랜치는 누가나 한 번쯤은 들어보게 된다. 각자의 brach를 만들고 협업을 진행한다. 협업을 하게 되면 , 서로의 작업내용이 지워지고 없어지기도 한다. 이러한 일은 브랜치로 Version Control를 어떻게 하는지 공식화해서 할 수 없기 때문이다. 더 자세하게 말하면, 커밋이 무엇인지 모르기 때문이다. 필자도 그러했다. 앞으로 작성할 몇개의 글에서 브랜치와 커밋에 대해서 알아보려 한다.

# 커밋

예전에 , git은 데이터를 변경사항이 아닌 스냅샷의 스트림으로 취급한다고 언급한 적이 있다. 이 정보들이 git의 database인 .git에 있고 이러한 정보들에 접근할 수 있는 key값들이 바로 commit이라 볼 수 있다.(나중에 , low-level , 즉 git 내부에서 어떻게 작동하는지도 다룰 예정이다. ) 

## 커밋을 하면 어떻게 되는가?

커밋을 하게 되면, 어떤 것들이 생겨나는지는 알아야 할 필요가 있다. 커밋 이전에 , 워킹 디렉토리 내부의 파일들을 변경해야 할 것이다.변경을 하게 되고  staging area에 추가하면 변경사항들이 blob 파일의 형태로 .git 에 저장이 된다. 이게, 스냅샷이다. 그리고, 이에 대한 체크섬들이 저장이 된다.  commit을 하게 되면 루트 디렉토리와 하위 디렉토리의 이름으로 구성된 트리 게체를 만들게 된다. 그리고 , 이 트리 개체는 blob 파일들에 대한 포인터이다. 그리고, 저자, 이전 커밋에 대한 포인터 , 메세지등을 포함한 커밋개체를 만들게 된다. 

이전 커밋에 대한 포인터는 1개일 수도 있고 , 여러 개일 수도 있다. 그리고, 최초 커밋에 대한 포인터는 없다. (이 부분도 추후 브랜치 설명에서 자세히 다룰 예정이다.)

git add , commit을 하게 되면 아래와 같은 것들이 생성되게 된다.

- blob
- 커밋개체 
  - 저자
  - 이전 커밋에 대한 포인터
  - 트리 개체 루트 포인터
- 루트 디렉토리와 하위 디렉토리의 트리 개체

## 예시

좀 더  , 구체적인 예시를 들어서 설명을 해보겠다. 

> 파일이 3개 있는 디렉토리 가 있다. 이를 Staging Area에 저장하고 Commit 을한다. 

```

$ git add README test.rb LICENSE
$ git commit -m 'The initial commit of my project'
```



우선 Staging Area에 저장을 하게 되면 git 저장소에 Blob이라는 파일을 저장하게 된다.  그리고  , 체크섬을 staging area에 저장하는데 , 이 체크섬은 SHA키 이다.  그리고 , 커밋 개체를 만들고 메타데이터와 루트 디렉토리를 가리키는 포인터 게체를 커밋 개체에 저장한다.

![image](https://user-images.githubusercontent.com/50165842/151635920-a21cfc56-399f-41f1-a7ff-d37df4ee3ada.png)

위의 예제의 경우 5개의 파일이 새로 생기는데 tree개체와 commit 개체 , 그리고 blob 파일 3개가 새로 생기게 된다.

![image](https://user-images.githubusercontent.com/50165842/151636244-06a4ab30-c663-484d-b36e-b05ac119ce62.png)

계속 커밋을 하게 되면 이전 커밋을 가리키는 커밋을 저장하게 되면서 위와 같은 모습을 띄게 됩니다. 

# 브랜치

모든 버전 관리 시스템은 브랜치를 지원한다.  개발을 하다 보면 여러 버전의 커드를 사용해야 하는 경우가 생기는데 , 코드를 통째로 복사후 원래 코드와는 상관없이 개발할 수 있는 기능을 브랜치가 지원해준다.Git은 특히 다른 VCS 처럼 변경사항을 저장하는 것이 아닌 스냅샷을 저장하기에 브랜치 간의 이동이 빠르다.브랜치란 커밋 사이를 이동할 수 있게 해주는 포인터이다. 즉, 커밋을 가리키는 포인터이다.

![image](https://user-images.githubusercontent.com/50165842/151636342-9a656aad-94f6-4022-af94-0c0691fe0f87.png)

여기서, v1.0 , master가 바로 브랜치이다.

## 브랜치 생성

![image](https://user-images.githubusercontent.com/50165842/151636560-f1395a0a-ce5b-4cd7-8303-ada29f51c6b5.png)

```
git branch testing
```

git에서 브랜치를 생성하려면 git branch 명령어로 생성을 하게 된다. 

![image](https://user-images.githubusercontent.com/50165842/151636655-c9856336-bc20-4dca-93ea-ea4e23d599da.png)

git으로 작업을 하다보면 HEAD라는 글자를 보게 된다. HEAD라는 포인터는 현재 작업하고 있는 로컬 브랜치를 의미합니다.



```
$ git log --oneline --decorate
f30ab (HEAD -> master, testing) add feature #32 - ability to add new
formats to the central interface
34ac2 Fixed bug #1328 - stack overflow under certain conditions
98ca9 The initial commit of my project
```

git log 에 --decorate옵션을 사용해서 브랜치가 어떤 커밋을 가르키는지 확인할 수도 있습니다. 이는 master, testing이라는 브랜치가 f30ab 커밋을 가리키고 있고 , HEAD 포인터가 master 브랜치를 가리키고 있음을 의미합니다 .

## 브랜치 이동

브랜치는 포인터임으로 각 브랜치 사이에서 이동할 수 있습니다.  브랜치를 이동한다는 의미는 작업하고 있는 브랜치를 , 즉 HEAD브랜치가 다른 브랜치를 가리키게 하는 것입니다.

```
git  checkout testing	
```

![image](https://user-images.githubusercontent.com/50165842/151637548-afabf189-bed4-4996-8410-7cea25f6e3b3.png)

이제 커밋을 해보겠다.

![image](https://user-images.githubusercontent.com/50165842/151637623-54af9e86-eb57-4487-b530-afc31b8e1cc3.png)

```
$ vim test.rb
$ git commit -a -m 'made a change'
```

새로 커밋을 하게 되면 HEAD 가리키고 있는 testing 브랜치가 하나 앞선것을 확인할 수 있습니다. master 브랜치는 여전히 그대로인것을 확인할 수 있습니다 .

![image](https://user-images.githubusercontent.com/50165842/151637715-58f5ac95-91bc-44c8-a60d-7074c105e69a.png)

```
git checkout master
```

HEAD가 master 포인터를  가리킴을 확인할 수 있습니다. git의 checkout은 2가지 일을 합니다.

- HEAD가 다른 브랜치를 가리키게 한다.
- 원래 디렉토리의 파일도 그 시점으로 돌린다.

커밋을 하게 되면 master, testing은 별개의 브랜치임으로 독립적으로 진행되기 때문에 , 임시로 testing에서 작업을 하다가 master브랜치로 이동하는 식의 워크플로가 가능하다. 

## 갈라지는 브랜치

![image](https://user-images.githubusercontent.com/50165842/151637986-33e847c5-28a3-470f-a84e-d90bfa75d988.png)

```
vim test.rb
git commit -a -m "mad other chagnes"
```

파일을 수정하고 다시 commit을 하게 되면 ,프로젝트 히스토리는 분리되서 진행되게 된다. 아까도 말했듯이, c2b9e , 87ab2 의 작업내용은 각 브랜치가 가리키는 커밋에 독립적으로 존재하게 된다. 

```
 git log --oneline --decorate --graph --all
 
 * c2b9e (HEAD, master) made other changes
| * 87ab2 (testing) made a change
|/
* f30ab add feature #32 - ability to add new formats to the
* 34ac2 fixed bug #1328 - stack overflow under certain conditions
* 98ca9 initial commit of my project

```

위 명령어로 git commit이 어떻게 갈라졌는지와 어떤 브랜치가 어떤 커밋을 가리키는지 확인할 수 있다 . git의 브랜치를 수도 없이 만들어도 프로젝트를 복사하는 것이 아닌 어떤 스냅샷을 가리킬지 포인터를 바꾸는 것이기 때문에 브랜치간의 전환이 다른 VCS보다 빠른 편이다. 