---
title : "What is VCS"



excerpt: "VCS and git"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , VCS]
# classes : wide
toc: true
toc_sticky: true
---

몇몇 프로젝트들을 하면서 , 프로젝트 개발 보다는 개발환경 세팅, GIt관리 에 더 많은 시간이 들어가는것이 너무 안타까웠습니다. 처음에는, Git을 잘 모르기에 Git을 공부해야 겠다라고 느꼈습니다 . 유튜브에서 여러 git tutorial을 들었는데 , 들으면 들을수록 깡통이 되어가는거 같았습니다. 유튜브의 내용은 심화된 지식들이 희석되어서 내려오기 때문에 , 유튜브 내용을 듣는다고 해도 이해없이 그저 외우라고 할 뿐 이였습니다. 무료 동영상 강의의 한계라고 느꼈습니다. 그러던 와중에 , "best git book"이라고 google에 검색을 했고 , "Git Pro" 라는 책이 가장 추천받고 있었고  , 무료이면서 한글 번역판이 존재하였습니다. 따라서, 이책을 통해 Git을 공부하기로 했습니다. 그리고 , 몇 page읽고 이책은 git 의 명령어를 단순히 설명하는게 아니라, version control system의 관점에서 git을 설명함을 느꼈습니다. 따라서, 저는 이 책을 통해서 version control system의 관점에서 git을 알아볼려 합니다. 

## 내가 생각하는 VCS

Version Control System의 모든 것을 알면 좋지만, 이 내용 자체도 방대하다는 것을 느꼈습니다. 따라서 , 제가 생각하는 Version Control System을 도식화 하고 여기서 떠오르는 의문들을 해결해나가면서 공부하겠다는 방향을 잡았습니다.

![vcs drawio (1)](https://user-images.githubusercontent.com/50165842/148645299-21277e70-e099-4236-9019-2fa9d19fbb0c.png)



이게 제가 생각하는 VCS입니다. main branch가 있고 , 여러 명이 각자의 local branch에서 main branch에 작업한 내용들을 commit으로 반영합니다.(실제로는 code review, test,build 등의과정을 거치고 반영될 것입니다.) 그리고, 각자의 branch끼리 commit 로그를 가져오기도 하고 commit 을 내보내기도 합니다. 



![single_repo drawio (1)](https://user-images.githubusercontent.com/50165842/148645565-d58ea675-5793-46e4-9d3c-c1cd9a8e00c7.png)

그리고 , 제가 생각하는 개인의 Repo main branch의 Commit history입니다.  여기서, WoongjoonChoi repo의 소유자인 저는 이 commit history를 보고 issue가 생길때 마다 이전 버전들로 이동해야 합니다. 이 version들에는 main repo 의 commit뿐만 아니라 다른 팀원들의 commit도 있어야 합니다. 

그리고 , 이 그림과 여러 유튜브 강의를 보면서 제 안에서 해결되지 않은 의문들을 글로 적어보았습니다.

1. 왜 Pointer가 이전 commit log를 가리키게 표현하는가?
2. HEAD Pointer의 용도는?
3. 각 branch pointer는 어떤 의미?
4. local branch는 표현할 때 / 로 표현하는게 안되는건가? remote만 origin/main 이렇게 표현을 하는건가?
5. Untracking File, branch tracking에서 각각 tracking은 어떤 의미로 쓰이는가?
6. .git은 local의 것만 저장을 하는 것인가?
7. working Directory에 있는거랑 Staging Directory에 있는거랑 정확하게 어떤 차이야?
8. local에서 commit이 되면 local(working directory) 의 commit history에는 반영이 된거야, 내 github는 push를 해야만 commit이 반영이 되는데 ,local , github repo랑 차이가 무엇이지?
9. git push vs git push -u  옵션의 차이는 무엇이지?

대략 , 이렇게 10개정도의 의문이 나왔던거 같습니다. 이러한 의문을 해결해 나가는것을 중점으로 git을 공부할려고 합니다. 

## VCS란?

VCS는 Version Control System의 약자입니다. VCS는 파일의 변화 기록을 시간에 따라 기록해 놓았다가 이전 버전을 참조할 수 있게 만든 것입니다. 어떤 소프트웨어의  domain에서든 VCS는 필수적이라고 생각합니다. 우리는  이를 통해 프로젝트단위로 통째로 이전 version으로 되돌릴 수 있고 , 어떤 파일이 변경되었을 때 , 이전 version의 파일과 차이점을 비교할 수 있고 , 문제가 일어났을때 누가 문제를 일으켰는지 도 알 수 있고, 누가 언제 이슈를 만들었는지 알 수 있습니다. Version Control System을 사람이 일일이 이런 것들을 기록하고 관리하는 대신 컴퓨터를 이용해서 대신하므로 값싼 비용을 지불하고 이러한 편의를 누릴 수 있는것은 정말 훌륭합니다.

## VCS의 역사

위의 글을 읽다보면 git 이전에도 VCS가 존재했음을 알 수 있습니다. 이전에는 VCS를 어떻게 구현해서 사용했는지 알아보도록 하겠습니다.



### local version

가장 간단한 형태의 version 관리라 볼 수 있습니다 디렉토리를 복사해서 local에 저장합니다. 생각이 있는 프로그래머라면 어떠한 구별자를 넣어둘 것입니다.(timestamp) . 하지만 , 일일이 복사 붙여넣기 를 하다보면 실수로 디렉토리가 누락되고 삭제되는 경우도 발생할 수 있습니다. 

![image](https://user-images.githubusercontent.com/50165842/148646196-a379089b-b618-433f-af79-5debe2c756f2.png)

따라서 , 사람들은 local VCS 라는 것을 만들었습니다. local에 DB를 만들어서 파일이 어떻게 변화했는지 기록한 것입니다. 그리고 , 책에 따르면 많이 쓰는 VCS 도구중에 RCS(revision control system) 라고 부르는 것이 있다고 합니다. 오늘까지도 많은 회사들이 사용을 하고 있다고 합니다. RCS는 기본적으로 Patch Set(파일이 변경되는 부분)을 관리하고  , 이 Patch Set은 특별한 파일 형식으로 저장을 합니다. 그리고 , 일련의 Patch Set을 적용해서 모든 파일을 특정 시점으로 되돌릴 수 있다고 합니다.

### centralized version

하지만, 여러 명의 개발자가 협업을 할 경우에는 중앙 서버에 File을 관리하는 것이 편할 수 있습니다. 이러한 요구를 해결하고자 CVCS(중앙집중버전관리시스템) 이 만들어졌습니다. CVCS 의 대표적인 시스템으로는 CVS, Subversion, Perforce 가 있습니다 .이 CVCS 시스템들은 file을 관리하는 별도의 서버를 지정합니다. 그리고 , 클라이언트가 중앙 서버에서 파일을 체크아웃해서 사용합니다.  

CVCS는 협업을 할 때 local version 보다 여러 장점을 갖고 있습니다. 중앙 서버에서 파일을 관리하기에 관리자가 다른 개발자들이 무엇을 하는지 쉽게 파악을 할 수 있습니다. 누가 에러를 내고 , 이슈를 만들었는지도 쉽게 파악이 가능합니다. local version의 경우 여러 서버의 database를 관리를 해야하는데 , Centralized version은 서버 1개만 관리를 하면 됩니다. 

완벽했다면 git은 나오지 않았을 겁니다.  역시 , 단점이 존재합니다. main server가 down 이 되면 그 동안 개발자들을 일을 진행할 수 가 없습니다. main server에서 다른 개발자가 작업을 해놓은 것을 실시간으로 받아오면서 해야하는데 , main server에서 file의 변경사항을 받을 수 없습니다. 비유를 하자면, Cloud Service Storage를 multi region이 아닌 single region으로 설정을 해놓으면 그 지역에 재해가 났을 때 소통할 서버가 없으므로 CLoud Server가 다운되는 것이랑 마찬가지입니다. 

그리고 , main server에 있는 하드디스크에 문제가 생긴다면 이를 백업을 할 방법이 없습니다. local server에 있는 스냅샷들은 괜찮을 지라도 , main server의 하드디스크가 망가지면 복구할 방법이 없습니다. 이 문제는 local 의 관점에서 보았을 떄도 , local의 하드디스크가 고장이 난다면 백업본을 미리 준비해두지 않는 한 복구할 방법이 없습니다.

### distributed version



![image](https://user-images.githubusercontent.com/50165842/148646951-ae0ccd4f-c8a0-4c71-b252-0ea4a38ca71a.png)

이러한 문제를 해결하는 방법은 간단하다 , 중앙화에서 탈중앙화가 되면 되는 것이다. 마치 , 은행의 집중관리형 시스템의 문제점을 극복하기 위해서 BlockChain으로 갈아타는 것과 마찬가지라고 저는 생각합니다. 이를 DVCS(distributed VCS) 라고 합니다 .DVCS에 대표적인 tool로는 git,mercurial,Bazaar, Darcs가 있습니다. 이들은 , 기존의 CVCS처럼 마지막 snapshot만을 checkout 하지 않습니다.  이들은 저장소 전체와 모든 history를 그대로 복제(clone) 합니다. 서버에 문제가 생기면 다른 서버로 부터 저장소와 history 전체를 복제(clone) 해서 다시 작업을 하면 됩니다. 

그리고 , 대부분의 DVCS는 remote repository가 있습니다. 이 remote repository는 한 개가 아니라 더 많을수 있습니다. 따라서 , 중앙에서 관리되는 CVCS에서는 할 수 없었던 workflow로 개발자들이 협업을 할 수 있습니다. 예를들어 , 제가 위에서 그렸던, 여러 repo의 pointer들이 제 개인 repo의 commit history를 pointing 하게 하는 형식의 작업을 할 수 있을 것입니다. 



