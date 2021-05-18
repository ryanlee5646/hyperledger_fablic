## Docker Swarm Cluster

### 1. Docker Swarm 개요

####(1) 컨테이너 오케스트레이션 개요

도커 컨테이너의 갯수가 꾸준히 늘어나면 필요한 자원도 지속적으로 늘어나기 마련이다 때문에 서버 또한 여러대로 늘어날 수 있는데 한대 두대의 수준이 아니라 몇 십 몇 백대의 서버로 늘어났다고 가정 해보자 이 많은 서버들을 일일이 접근하여 명령어 날려주고 컨테이너 올리고 "어? 이건 또 왜 내려갔어?" 하다가 시간은 시간대로 흘러버리고 정신을 차려보면 라꾸라꾸 침대가 본인 의자옆에 있는것을 발견 할 수 있을것이다. 결론적으로 이 많은 서버들과 컨테이너를 소수의 인원으로 관리하기에는 상당히 어렵고 이 문제를 효율적으로 관리하기 위해 컨테이너 오케스트레이션 툴들이 나오게 되었다.

#### (2) 컨테이너 오케스트레이션 툴 소개

![img](https://blog.kakaocdn.net/dn/bFns6f/btquDM0P3RF/021SIEED8x1PgVe6KxLYHk/img.png)Docker Swarm / Kubernetes / Apache Mesos

컨테이너 오케스트레이션 툴의 기능에는 단순 컨테이너의 배포 뿐만이 아닌 하나의 서비스를 관리하고 유지보수 하기 위한 많은 기능들을 포함하고 있고 툴마다 기능에 대한 편차는 있으나 주요 기능은 아래와 같다.

####  (3) 오케스트레이션 툴의 기능

- 노드 클러스터링
- 컨테이너 로드 밸런싱
- 컨테이너의 배포와 복제 자동화
- 컨테이너 장애 복구기능
- 컨테이너 자동 확장 및 축소
- 컨테이너 스케쥴링
- 로깅 및 모니터링

#### (4) Docker Swarm

도커 스웜은 도커 컨테이너 플랫폼에 통합 된 컨테이너 오케스트레이션 툴이다. 최초에 도커 스웜은 도커와 별개로 개발 되었으나 도커 1.12 버전부터 도커 스웜 모드라는 이름으로 합쳐졌다.

**장점**

- 도커 명령어와 도커 컴포즈를 포함한 도커의 모든 기능이 내장되어 있다.
- 도커 이외의 별도의 툴 설치가 필요하지 않다.
- 타 오케스트레이션 툴에 비해 복잡하지 않고 다루기 쉽다.

**단점**

- 타 오케스트레이션 툴에 비해 기능이 단순하여 세부적인 설정이 어려움
- 초대형 노드 클러스터링에는 무리가 있다.

#### (5) Docker Swam 구성

![img](https://blog.kakaocdn.net/dn/bs0fPT/btqKC1HQa0F/RlDeKuiPV65kEG8umPh8b1/img.png)

##### 1. 매니저 노드(manager node)

매니저 노드는 아래의 업무를 통해 도커 클러스터를 관리한다.

- 클러스터의 상태를 유지 : 뗏목 알고리즘 사용
- 스케줄링 서비스 : 작업자 노드(worker)에게 컨테이너를 배포한다. 특정 노드에게만 배포하거나, 모든 노드에 하나씩 배포할 수도 있다.
- HTTP API EndPoint
- 스웜 모드 제공 : `docker swarm init`

매니저는 [뗏목(Raft) 합의 알고리즘](https://roseline124.github.io/kuberdocker/2019/07/31/docker-study09.html)을 이용해 스웜 전체와 거기서 돌아가는 서비스들이 일관된 상태를 유지할 수 있도록 한다.  뗏목 합의 알고리즘은 쉽게 말해 여러 서버 중 일부에 장애가 생겨도 나머지 서버가 정상적인 서비스를 할 수 있도록 하는 것이다(장애가 생긴 서버는 전체 서비스를 중단하지 않고도 복구할 수 있다.).

이 때문에 하나의 매니저로만 스웜을 운영하면 서비스는 제 기능을 하겠지만, 복구를 위해 새로운 클러스터를 하나 더 만들어야 한다. 따라서 장애가 생겨도 계속 서비스를 유지하려면 여러 개(그것도 홀수로!)의 노드를 운영하는 것이 좋다. 도커에서는 최대 수를 7개로 잡고 있으며 그 이상의 노드는 성능 저하를 일으킬 수 있다고 경고한다.



##### 2. 작업자 노드(worker node)

도커에서 **일반적으로 컨테이너를 실행하는 노드**를 작업자 노드라고 한다. 일반 사원에게 관리직을 맡기지 않는 것처럼 작업자 노드에게 매니저 노드가 하는 일(스케줄링, 합의)을 맡기지 않는다.

작업자 노드들의 클러스터는 반드시 하나 이상의 관리자 노드를 가져야 한다. **매니저 노드 역시 작업자에 속하긴 한다.** 단일 매니저를 둔 노드 클러스터에서도 `docker service create`라는 명령어로 도커 스웜을 실행할 수 있다.

도커 스웜을 실행할 때, 스케줄러가 매니저 노드에게 작업자 노드가 하는 Task(컨테이너를 배포하고 관리)를 실행시키지 않기 위해서는 아래의 명령어로 매니저 노드의 가용성을 `drain`(배수, 빼내다 등의 의미)으로 설정한다. 스케줄러는 `drain` 상태의 노드에는 task를 맡기지 않고 `active` 상태의 노드에게만 task를 할당한다.

```bash
# 노드 상태를 drain으로 변경한다.
docker node update --availability drain <노드ID>

# 노드의 가용성을 확인한다.
docker node inspect --pretty <노드ID>
```



## 환경설정

### 1. Docker-machine 설치

```bash
$ base=https://github.com/docker/machine/releases/download/v0.16.0 \
  && curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/usr/local/bin/docker-machine \
  && chmod +x /usr/local/bin/docker-machine
```

다음과 같은 결과가 나오면 설치 성공

```bash
$ docker-machine
Usage: docker-machine [OPTIONS] COMMAND [arg...]

Create and manage machines running Docker.

Version: 0.16.0, build 702c267f

Author:
  Docker Machine Contributors - <https://github.com/docker/machine>

Options:
 .
 .
 .
```

### 2. VM Machine 설치

#### (1) 관리자(manager) 노드 생성

```bash
# manager node 가상머신 설치
$ docker-machine create --driver=virtualbox manager1
```

다음과 같은 결과가 나오면 설치 성공

```bash
$ docker-machine ls

NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12
```

#### (2) 작업자(worker) 노드 생성

``` bash
# worker node 가상머신 설치
$ docker-machine create --driver=virtualbox worker1
$ docker-machine create --driver=virtualbox worker2
```

다음과 같은 결과가 나오면 설치 성공

```bash
$ docker-machine ls
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v19.03.12
```

#### (3) Docker Swarm Cluster 생성

1. **관리자 노드 SSH 접속**

```bash
$ docker-machine ssh manager1
```

2. **관리자 노드에서 마스터 노드 지정 명령어 실행**

```bash
docker@manager1:~$ docker swarm init --advertise-addr 192.168.99.100

# docker-machine 목록에서 manager1 URL 확인(local bash에서 조회)
$ docker-machine ls
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v19.03.12
worker2    -        virtualbox   Running   tcp://192.168.99.102:2376           v19.03.12

# 다음과 같은 결과가 나오면 정상적 실행

docker@manager1:~$ docker swarm init --advertise-addr 192.168.99.100

Swarm initialized: current node (je05gpdrkd3397v2ons9y2m4f) is now a manager.

To add a worker to this swarm, run the following command:

### 아래의 명령어를 통해 작업자노드를 docker swarm cluster에 Join 한다.
#####################################################
    docker swarm join --token SWMTKN-1-0jhlkq7r9aaexiz9id2kr0wndl3ktp8gg5l87w6dmp6310k1sx-1tnyh1zeh326sdbprusdxlo1s 192.168.99.100:2377
#####################################################
To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```

3. **작업자 노드 SSH 접속**

```bash
# 각각의 새 터미널창을 띄우는 것이 좋다
$ docker-machine ssh worker1
$ docker-machine ssh worker2
```

4. **작업자 노드(worker1,worker2)에서 Swarm Cluster 조인**

```bash
docker@worker1:~$ docker swarm join --token SWMTKN-1-0jhlkq7r9aaexiz9id2kr0wndl3ktp8gg5l87w6dmp6310k1sx-1tnyh1zeh326sdbprusdxlo1s 192.168.99.100:2377

# 다음과 같은 결과가 나오면 정상적 실행
This node joined a swarm as a worker.

# docker node 조회(manager 노드에서)
docker@manager1:~$ docker node ls

ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
je05gpdrkd3397v2ons9y2m4f *   manager1            Ready               Active              Leader              19.03.12
3xm0jz56dg7n5je6in4undbxd     worker1             Ready               Active                                  19.03.12
ni66nq05ef5uxltq9xxs9ajug     worker2             Ready               Active                                  19.03.12

# Manager 노드의 MANAGER STATUS를 보면 LEADER임을 확인 할 수 있다.
```

#### (4) Docker Swarm안에 서비스 컨테이너 생성

```bash
# 해당 명령어는 manager node에서 실행

$ docker service create --replicas 3 -p 80:80 --name web nginx

# --replicas는 생성할 컨테이너수를 의미한다. 현재까지 노드가 총 3개이므로 3개의 컨테이너 생성

# 다음과 같은 결과가 나오면 정상적 실행
$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
1ut8mc9xl6is        web                 replicated          3/3                 nginx:latest        *:80->80/tcp
```

다음 URL 접속 연결 확인

```bash
# manager node
192.168.99.100:80

# worker node
192.168.99.101:80
192.168.99.102:80
```

다음과 같이 나타나면 정상적인 연결

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/docker-swarm1.png?raw=true)

```bash
# Manager Node
# 실행중인 docker service 확인 [서비스명:web]
docker@manager1:~$ docker service ps web

# 다음과 같이 나옴
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE         ERROR               PORTS
pvf6kp3dt49j        web.1               nginx:latest        manager1            Running             Running 4 hours ago
et7y9rd0nc60        web.2               nginx:latest        worker1             Running             Running 4 hours ago
pdsaxg4emgvo        web.3               nginx:latest        worker2             Running             Running 4 hours ago

```



####(5) Docker Swarm 컨테이너 수 up/down

1. **컨테이너 수 증가**

```bash
# Manager Node에서 실행
# scale은 컨네이너 수를 말함
docker@manager1:~$ docker service scale web=4

# docker <web> service replicated가 4/4가 됨
docker@manager1:~$ docker service ls

ID                  NAME                  MODE                REPLICAS            IMAGE                           PORTS
j94g52dnrw2l        portainer_agent       global              3/3                 portainer/agent:latest
1ishb3qemsv0        portainer_portainer   replicated          1/1                 portainer/portainer-ce:latest   *:8000->8000/tcp, *:9000->9000/tcp
1ut8mc9xl6is        web                   replicated          4/4                 nginx:latest                    *:80->80/tcp

# docker <web> service 세부 내용 조회
# worker2의 노드에 컨테이너가 하나 더 생김
docker@manager1:~$ docker service ps web

ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
pvf6kp3dt49j        web.1               nginx:latest        manager1            Running             Running 14 hours ago
et7y9rd0nc60        web.2               nginx:latest        worker1             Running             Running 14 hours ago
pdsaxg4emgvo        web.3               nginx:latest        worker2             Running             Running 14 hours ago
t7s4vg763ia4        web.4               nginx:latest        worker2             Running             Running about a minute ago
```

2. **컨테이너 수 감소**

```bash
# Manager Node에서 실행
docker@manager1:~$ docker service scale web=2

# docker <web> service replicated가 2/2가 됨
docker@manager1:~$ docker service ps web

ID                  NAME                  MODE                REPLICAS            IMAGE                           PORTS
j94g52dnrw2l        portainer_agent       global              3/3                 portainer/agent:latest
1ishb3qemsv0        portainer_portainer   replicated          1/1                 portainer/portainer-ce:latest   *:8000->8000/tcp, *:9000->9000/tcp
1ut8mc9xl6is        web                   replicated          2/2                 nginx:latest                    *:80->80/tcp
```





### 3. Portainer 설치

#### (1) Portainer란?

portainer는 Docker Web 관리 Tool 이다. 간단하게 말해 현재 실행되고있는 Docker 관련된 컨테이너, 이미지, 볼륨, 네트워크 등을 web에서 관리할 수 있게 도와준다. 

무엇보다 로컬상의 Docker 뿐만아니라 다른 노드의 Docker도 agent를 통해 관리가 가능하다.

공식홈페이지를 읽어보면 컨테이너를 관리할수있는 GUI를 제공해주고 도커뿐만아니라 Kubernetis, Docker Swarm도 지원해준다고 나와있다.

Url : [www.portainer.io/](https://www.portainer.io/)

#### (2) Portainer 설치

```bash
# Manager 노드에서 실행
docker@manager1:~$ curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml

docker@manager1:~$ docker stack deploy -c portainer-agent-stack.yml portainer

# 다음과 같이 나오면 정상적 실행
Creating network portainer_agent_network
Creating service portainer_agent
Creating service portainer_portainer

# 현재 실행 중인 docker 목록
docker@manager1:~$ docker service ls

# portainer와 portainer_agent가 추가된 것을 확인
ID                  NAME                  MODE                REPLICAS            IMAGE                           PORTS
j94g52dnrw2l        portainer_agent       global              3/3                 portainer/agent:latest
1ishb3qemsv0        portainer_portainer   replicated          1/1                 portainer/portainer-ce:latest   *:8000->8000/tcp, *:9000->9000/tcp
1ut8mc9xl6is        web                   replicated          3/3                 nginx:latest                    *:80->80/tcp
```

####(3) Portainer 접속

```bash
URL : 192.168.99.100:9000 (Manager 노드)
```

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/portainer1.png?raw=true)

#### (4) Portainer 계정 생성 

**id : admin**

**pw: admin123**

계정 생성 후 접속



## AWS 클라우드를 이용한 Virtual Machine 환경 구축

### 1. AWS 계정 생성

AWS 계정 가입하고 로그인 후 메인화면에서 **[가상 머신 시작]** 클릭 

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws1.png?raw=true)

### 2. Ubuntu 가상머신 생성

**Ubuntu Server 18.04 LTS (HVM)** 선택

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws2.png?raw=true)



### 3. 가상머신 인스턴스 유형 선택

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws3.png?raw=true)







