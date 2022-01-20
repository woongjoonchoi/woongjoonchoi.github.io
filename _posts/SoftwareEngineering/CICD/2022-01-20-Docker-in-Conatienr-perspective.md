---
title : "What is Container? ,Docker in Container perspective"

excerpt: "Docker and Container"

categories:
  - CI/CD
tags:
  - [SoftwareEngineering , Docker,Container,CI/CD]
# classes : wide
toc: true
toc_sticky: true
---

Docker를 많이 들 프로덕션,테스트 환경에서 환경을 통일하기 위해 개발환경을 구축하는데 사용합니다.  그리고 ,대부분의 설명이 VM과는 다르게 OS를 안 띄우고 사용하면 된다라고 합니다. 실제로 Docker tutorial을 몇 번 따라해보면 , 간단한 흉내를 낼 수 있습니다. 개발 환경을 통일하기 위해서 사용을 한다라고는 많이들 알고 있지만 , 내부에서 어떤 원리에 의해서 작동하는지는 잘 다루지는 않는다. Docker만 쓸거면 Docker tutorial만 보고 , Docker에서의 각 명령어가 어떤 동작을 하는지 확인하고 , 많이 쓰는 docker image를 기억하면 충분합니다. 이는 마치 , github를 쓸거면 github만 공부하는 것과 같은 것입니다. 각 tool의 동작에 집중하지 말고 , 이에 기반하는 기본 개념과 동작 원리를 분해해서 이해하면 , 이와 같은 기술을 사용하는 다른 플랫폼이나, 다른 기술이 나왔을 때 쉽게 전이지식(Transfer learning) 을 사용해서 쉽게 배울 수 있을 것입니다.

# Container

## What is Container

### Definition

Container란 무엇일 까요? Container를 이해하기 위해서는 우선 linux machine 부터 시작해야 합니다. 



![img](https://www.098.co.kr/wp-content/uploads/2020/09/container-what-is-container.png)

OS라는 것은 간단히 말하자면 여러 Hardware에 대한 interface들을 우리가 신경쓰지 않고 encapsulation한 다음에 abstraction해서 사용할 수 있도록 도와주는 software package라 볼 수 있습니다. 이 때 각 process들은 memory 같은 resource들을 share해서 사용합니다. 이러한, process들을 격리하고(os에서 말하는 isolation이랑은 다릅니다.) 싶을 수 있습니다 . process namespace안에 특정 process들과 namespace  그리고 croups을 같이 결합해서 격리해놓은 것을 container in runtime 이라고 합니다. 이게 바로 정의입니다. process들은 namespace안에 묶여있기에 같은 namespace안에 있는 process들만을 볼 수 있습니다.(python의 LEGB scope rule을 떠올리시면 편합니다.)  각각의 Container는 Runtime에서 sandbox라 볼 수 있습니다. 

### Container Life Cycle

Container라는 것은 process들이 격리되어 있는 것입니다. Container 가 시작해야 Process 가 시작을 합니다. 그리고 , Process가 특정 작업을 수행하고 종료됩니다. 그리고 , Container는 더 이상 존재할 이유가 없기에 종료됩니다. 즉, Container의 lifecyle은 Process의 lifecycle 과 완전히 일치함을 알 수 있습니다 .  



## Image Tree

### pull

저희가 Container를 얘기할 때 주로 Container라 지칭하는 것은 Image입니다. Image는 Tree 입니다.

![image](https://user-images.githubusercontent.com/50165842/150354604-1ad8bb22-f4e5-4036-a373-8fa0abaf54f2.png)



Docker Image는 Tree 구조입니다. 위의 예시로 설명을 들면, scratch 부터 busybox를 쌓아올리고 , 그다음에 sshd , 그다음에 ,app을 쌓아올립니다. App을 실행하고 싶을때 app branch를 pull 하고 실행을 하면 됩니다.  즉, 전체 Tree를 가져올 필요 없이 원하는 특정 branch만 가져올 수 있습니다. 

### share

![Docker Image Tree-Page-2 drawio](https://user-images.githubusercontent.com/50165842/150355019-82a937fd-64b5-4949-a027-1054c764ee87.png)



그리고 , 다른 노드가 내가 실행하고 있는 것을 공유해서 사용할 수 있습니다. 예를들어 , 다른 노드가 app tree에서 sshd까지 branch를 가져와서 app2 라는 노드를 만들 수 있습니다.   .Tree를 활용해서 이미지를 효과적으로 관리할 수 있습니다.

### Weak point analysis

계층구조(Tree) 이기에 어떤 노드(ex.busybox, sshd,)에서  문제점이 생기면 어떤 노드가 자식 Node인지 알기에 어떤 Node가 취약점에 영향을 받는지 쉽게 알 수 있습니다. 







# Docker file, image, container

Container의 Definition에서 자원들을 격리하는 지를 알았고 , Tree 구조를 통해서 어떻게 효과적으로 이미지를 관리하는지 알 수 있었습니다. 그렇다면, container는 어떤 과정을 통해서 만들어지는 것일까요 ? 이를 알기 위해서는 Docker File, Image, container 의 관계에 대해서 알 필요가 있습니다.

## DockerFile

DockerFile은 Docker 가 유명하기에 Dockerfile을 예시로 가져왔습니다. Docker File이란 어떤 txt파일을 의미합니다.

```dockerfile
FROM python:3.8.7-slim-buster

COPY . /app
WORKDIR /app
ENV PYTHONPATH=/app
ENV PYTHONUNBUFFERED=1

RUN pip install pip==21.2.4 && \
    pip install -r requirements.txt

CMD ["python", "main.py"]
```

DockerFile에서 우리는 무엇이든지 얼마든지 RUN 할 수 있습니다 . Docker File에서 docker build라는 명령어로 이미지를 만들게 됩니다.

## Image

![Docker Image Tree-Page-2 drawio](https://user-images.githubusercontent.com/50165842/150355019-82a937fd-64b5-4949-a027-1054c764ee87.png)

Image 는 template입니다 . Template의 instance를 수도 없이 생성할 수 있습니다. Template의 Instance를 container runtime이라 부릅니다   

## Container

![img](https://www.098.co.kr/wp-content/uploads/2020/09/container-what-is-container.png)

Image로 부터 만들어진 instance입니다. Container가 시작되면서 process가 시작되고 process가 종료되면 container 도 종료됩니다.



## Relationship

이들 관의 관계는  아래와 같습니다.

- Docker File -> Image -> Container
- Container -> Image

Docker File로 부터 Image가 생성이 되고 , Image로부터 Container가 생성이 됩니다. 하지만, 반드시 Docker file로 부터 시작할 필요는 없습니다.  Container로 작업을 수행한 후 commit을 이용해서 Image로 만들 수 있습니다.

## Not install dependency

Container, DockerFile, Image는 위와 같은 관계를 가지고 있고 , Container를 만들 때 꼭 시작점이 Dockerfile일 필요는 없다는 것을 알아보았습니다. 보통, process를 실행할려면 각각 다른 version의 library들이 필요합니다. 우리는 이를 apt-get을 이용해서 설치를 해줍니다.하지만, Container를 이를 설치할 필요없이 모든 것을 다 포함하고 있습니다 .이를 다르게 말하자면, Image는 어떤 software들의 package이고 , 이 pacakage로 부터 process를 만들게 됩니다. 다른 라이브러리 버전이 필요한 app을 단일 OS에서 실행할 수 있습니다. Image로부터 Container Instance를 만들고 작업이 종료되면 Container가 사라집니다. 이 이미지를 삭제하면 전부 사라지게 됩니다. 즉 , 어떠한 dependency 도 설치할 필요가 없습니다.



# Docker

Image가 Tree 구조이기에 효율적으로 관리될 수 있고 , Container는 각  container들 끼리 격리되어 있다는 것을 확인했습니다. 그리고 , Dockerfile , Image, Contianer간의 관계에 대해서 알아보았습니다. 그렇다면, 이 모든것들을 다 연결해보도록 하겠습니다.

## Docker Host

![img](https://data-flair.training/blogs/wp-content/uploads/sites/2/2018/10/Docker-Architecture-vol.1-01.jpg)

Docker Host라는게 있습니다. 이 안에 image cache가 있고 , Registry라는 것이 있습니다. Docker Host는  Registry로부터 필요한 bit만을 docker pull 을통해서 받기도하고 , push하기도 합니다. 

### Docker Push & Pull

docker pull은 Image cache안에 얼마나 parent node를 가지고 있는지를 확인합니다. 그리고 , 어떤 파생물을 가지고 와서 자식노드를 구성해야 하는지 판단합니다. 이미 , image cache안에 paraent node를 가지고 있기에 , app을 build하기 위해 필요한 node만 가져오면 됩니다.

docker push는 오직 Image cache안에 가지고있는 tree와의 차이점을 registry에 push합니다. 즉, 스냅샷을 기록하는 것입니다.(git이랑유사합니다.) 이미 , registiry 안에 차이점 외에는 들어가있기에 , 차이점만을 push 하는 것입니다.

git 처럼 스냅샷을 기록하기에 한 image로부터 다른 image를 만드는게 빠르다는 장점이 있습니다.



## Docker Client

Docker Client라는 것이 있습니다.  Docker Client는 단순하게 말하면 Docker pull , Docker create, Docker run, Docker commit을 수행하는 주체입니다.

Docker Host 안에 deamon이라는 것이 있는데 , Docker Client가 deamon에게 말을 겁니다. 그러면, deamon은 Client에게 API를 노출하게 됩니다. 

Docker Client의 역할은 2가지라 볼 수 있습니다.

1. Docker Host의 container lifecycle을 관리한다.
2. Docker Host의 Infrastrucutre를 configure한다.

여기서, 저희는 이 2번에 주목을 해야 합니다. 

이 말은 Docker가 Network와 Storage를 관리한다는 말과 같습니다. 여기서, Network에 대한 설명은 아직 배우질 않아서 생략하도록 하겠습니다. 

### Storage

Docker의 Storage는 기본적으로 preemtible합니다. Container가 종료가 되면 안에서 작업하던 것들도 사라지게 됩니다. 그렇다면, 이를 persistent disk로 만들수는 없을까요? Docker 에서 Persistent 한 disk를 Volume이라고 합니다. Container가 Container의 lifetime이 끝나도 데이터(혹은 state) 를 보관(저장)하고 싶으면  Volume을 사용합니다.



