## Docker Swarm Cluster

### 1. Docker Swarm 개요

#### (1) 컨테이너 오케스트레이션 개요

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
$ docker swarm init --advertise-addr 192.168.99.100

# docker-machine 목록에서 manager1 URL 확인(local bash에서 조회)
$ docker-machine ls
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
manager1   -        virtualbox   Running   tcp://192.168.99.100:2376           v19.03.12
worker1    -        virtualbox   Running   tcp://192.168.99.101:2376           v19.03.12
worker2    -        virtualbox   Running   tcp://192.168.99.102:2376           v19.03.12

# 다음과 같은 결과가 나오면 정상적 실행

docker@manager1:~$ docker swarm init --advertise-addr <IP Addr>

Swarm initialized: current node (je05gpdrkd3397v2ons9y2m4f) is now a manager.

To add a worker to this swarm, run the following command:

### 아래의 명령어를 통해 작업자노드를 docker swarm cluster에 Join 한다.
#####################################################
    docker swarm join --token SWMTKN-1-*************************************** <IP Addr:2377>
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

#### (5) Docker Swarm 컨테이너 수 up/down

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
$ curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml

$ docker stack deploy -c portainer-agent-stack.yml portainer

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



## 가상서버 구축(AWS)

### 1. AWS 클라우드를 이용한 Virtual Machine 환경 구축

#### (1) AWS 계정 생성

AWS 계정 가입하고 로그인 후 메인화면에서 **[가상 머신 시작]** 클릭 

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws1.png?raw=true)

#### (2) Ubuntu 가상머신 생성

**Ubuntu Server 18.04 LTS (HVM)** 선택

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws2.png?raw=true)

#### (3) 가상머신 인스턴스 유형 선택

표시된 사양으로 선택 후 다음(인스턴스 세부 구성)으로 넘어감

❗️❗️현재 이미지에서는 `t2.medium`으로 선택이 되어있어  과금이 발생할 수 있다.

(이틀 정도 인스턴스 켜놓고 8000원 과금...ㅠㅠ)

**고로 사용자가 프리티어 기간이라면  `t2.micro` 를 사용하는 것을 추천!!** 

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws3.png?raw=true)

#### (4) 인스턴스 세부 구성

인스턴스 개수를 3개로 수정 후 다음(스토리지 추가)로 넘어감

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws4.png?raw=true)

#### (5) 스토리지 추가

스토리지 용량을 30GB으로 수정 후 다음(태그 추가)로 넘어감

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws5.png?raw=true)

#### (6) 태그 추가 및 보안그룹 구성

태그 추가는 옵션 사항으로 이므로 넘어가고 보안그룹구성 또한 기본 설정 후 넘어간다. 

#### (7) 인스턴스 시작을 위한 키페어 생성

키페어는 AWS 서버에 SSH 접속을 위해 필요한 보안키이므로 [새 키 페어 생성]을 눌러 키페어 이름은 `docker-swarm` 으로 하여 키페어를 다운로드 한다.

❗️**키페어를 다운로드하고 [인스턴스 시작]을 눌렀을 때 오류가 뜨는건 리전(지역)을 인증하는데 시간이 걸리기 때문이다. 그런경우 10~20분 후 정상적인 인스턴스 실행 가능하다.**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws6.png?raw=true)

#### (8) 인스턴스 네이밍

manager, worker1, worker2

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws7.png?raw=true)



### 2. 터미널에서 SSH로 VM(가상머신) 접속 후 환경 설정

#### (1) SSH에서 VM 접속 (Manager, Worker1, Worker2)

-> 인스턴스 ID를 클릭한다.

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws8.png?raw=true)



-> 연결을 클릭한다.

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws9.png?raw=true)



-> 인스턴스 키를 비공개화하고 나서 SSH 접속(❗️총 3개의 각각의 터미널에서 **manager**, **worker1**, **worker2**에 해당하는 명령어를 동일하게 실행시켜 준다.)

```bash
# 프라이빗 키 비공개화
# 인스턴스 생성시 발급받은 프라이빗 키의 경로를 잘 잡아 줘야한다.
chmod 400 docker-swarm.pem

# SSH 접속(Manager 노드)
ssh -i "docker-swarm.pem" ubuntu@ec2-13-124-76-144.ap-northeast-2.compute.amazonaws.com
```

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws10.png?raw=true)



-> SSH로 접속한 총 3개의 노드

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws11.png?raw=true)



#### (2) VM에 Docker 설치 (모든 노드에 동일하게 실행)

참고 : https://docs.docker.com/engine/install/ubuntu/

1. **`apt-get` 패키지 설치**

``` bash
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

2. **Docker 공식 GPG키 추가**

```bash
# 강의에서 나오는 명령어
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 현재 기준(2021.05.20) 공식문서 명령어
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

```

3. **저장소 설정**

```bash
$ sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) \
stable"
```

4. **Docker Engine 설치**

```bash
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

5. **Docker를 root가 아닌 사용자로 이용**

```bash
$ sudo usermod -aG docker ${USER}
```

6. **원활한 적용을 위해 VM 리부팅**

❗️리부팅 되는데 3~5분 정도가 소요됌

```bash
# 리부팅
$ sudo reboot

# ssh 접속(각자 노드에 맞는 EC2로 접속)
$ ssh -i "docker-swarm.pem" ubuntu@ec2-13-124-217-83.ap-northeast-2.compute.amazonaws.com
```



#### (3) Docker Swarm 네트워크 생성

Docker-Machine으로 VM을 설치해서 테스트 한거랑 동일하게 설정

1. **Manager 노드에서 Swarm 클러스터 생성**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws12.png?raw=true)

```bash
# Manager 노드의 IP로 클러스터 생성
$ docker swarm init --advertise-addr 172.31.15.21
```

❗️ swarm cluster를 다시 생성해야하거나 node에서 벗어 나려면

**`docker swarm leave --force`**

2. **Worker 노드를 Swarm에 조인**

```bash
# Swarm 클러스터 생성시 나오는 명령어를 실행
$ docker swarm join --token SWMTKN-1-0r39oz1gl5uvj5u54j9z5pk97zerspdo98xzdmynl1fckmwhfo-b4ztmb7inq8vx3zgzy1lqgvs9 172.31.15.21:2377
```

❗️❗️❗️ 혹시나 아래와 같은 에러가 발생한다면 방화벽을 열어 줘야한다.

```bash
Error response from daemon: Timeout was reached before node joined. The attempt to join the swarm will continue in the background. Use the "docker info" command to see the current swarm status of your node.
```

**도커 공식 문서에 의하면 다음과 같이 방화벽을 열어줘야한다.**

(1) **TCP:2377** => Cluster Management(Swarm Join)에서 사용

(2) **TCP/UDP:7946** => 노드간 통신

(3) **UDP:4789** => 오버레이 네트워크 간 트래픽 통신

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws13.png?raw=true)



**다음과 같이 나오면 정상적 실행**

```bash
$ docker node ls

ID                            HOSTNAME           STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
5mctdk57f6w366awfmysn48tg     ip-172-31-4-8      Ready     Active                          20.10.6
u3v6hclgcygp5alcowg0ucdoa     ip-172-31-11-117   Ready     Active                          20.10.6
wreeb0vzh7cxsn5eaiym2rft2 *   ip-172-31-15-21    Ready     Active         Leader           20.10.6
```



3. **Docker Service 생성**

``` bash
# docker service create --name <Service Name> -p 80:80 --replicas <container num> nginx
$ docker service create --name web --publish 80:80 --replicas 3 nginx

# 다음과 같이 나오면 정상적 실행
lhojtmjwzetd2du416ajn3cuv
overall progress: 3 out of 3 tasks
1/3: running
2/3: running
3/3: running
verify: Service converged

# Docker Service 조회
$ docker service ls

ID             NAME      MODE         REPLICAS   IMAGE          PORTS
lhojtmjwzetd   web       replicated   3/3        nginx:latest   *:80->80/tcp

# Docker 컨테이너 리스트
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS     NAMES
d6a2fd7c5406   nginx:latest   "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    web.3.k9i4iqc7xa4qg3bc6qe8wlwwv
```

**Nginx Web 서버 접속 확인(퍼블릭 IPv4주소)**

Manager 노드: **`54.180.***.**:80`**

Worker1 노드: **`3.34.***.***:80`**

Worker2 노드: **`13.124.***.**:80`**

**다음과 같이 나오면 정상적 실행**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws14.png?raw=true)



#### (4) Portainer 설정

[환경설정] - [Portainer 설치](#(2)-Portainer-설치)

❗️❗️❗️ 접속이 되지 않는다면 EC2 방화벽을 열어줘야한다.

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/aws15.png?raw=true)

**Portainer에서는 컨테이너수를 늘리고 줄일 수가 있다.**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/portainer2.png?raw=true)



#### (5) Visual Studio Code와 AWS EC2 인스턴스 연동

해당 블로그 참고: https://vlee.kr/4810



## 패프릭 인증기관(Certificate Authority, CA) 구축

### 1. 오버레이(Overlay) 네트워크 이해

참고자료1: https://watch-n-learn.tistory.com/49

참고자료2:  https://code-machina.github.io/2019/08/08/Docker-Swarm-Overlay-Network-Part-1.html

#### (1) 정의

1. **Ingress 네트워크**: 네트워크는 서비스의 노드들 간에 로드 밸런싱을 수행하는 Overlay 네트워크이다. Docker Swarm에서는 서비스를 외부에 쉽게 노출하기 위해 모든 노드가 ingress라는 가상 네트워크에 속해있다. Docker swarm의 모든 노드가 노출된 포트로 클라이언트에서 요청을 하면, 해당 요청을 IPVS라는 모듈로 전달한다. IPVS는 해당 서비스에 참여하는 모든 IP주소를 추적하고 그 중 하나를 선택한 뒤, 요청을 해당 경로로 라우팅한다. 

   **"서비스에서 포트를 오픈하면, 모든 노드에서 포트가 오픈되고 어떤 노드에 요청을 보내도 실행 중인 컨테이너에 자동으로 전달한다."** 

2. **Overlay 네트워크**: 오버레이 네트워크는 Docker Swarm에 참여하는 Docker Daemon 간의 통신을 관리한다. 독립 실행 컨테이너의 네트워크를 생성하는 방법과 동일한 방식으로 생성할 수 있다. 또한 생성한 오버레이 네트워크에 Swarm service를 연결시켜 service 간에 통신을 활성화 할 수 있다. 이러한 오버레이 네트워크는 Overlay Network Driver를 사용한다.

   **오버레이 네트워크를 사용하면 컨테이너는 외부에 포트를 오픈하지 않아도 되고 연결되는 다른 컨테이너와(예를들어 웹과 디비) 다른 노드에 있어도 같은 서버에 있는 것처럼 통신할 수 있다.** 

#### (2) 오버레이(Overlay) 네트워크 생성

```bash
# 오버레이 네트워크 생성
$ docker network create --driver=overlay --attachable test

# 네트워크 조회(test 네트워크 확인)
$ docker network ls
NETWORK ID     NAME                      DRIVER    SCOPE
4610a87ad2ea   bridge                    bridge    local
8aa238da04bf   docker_gwbridge           bridge    local
b2a8979568f8   host                      host      local
w5l0mu9eu8q2   ingress                   overlay   swarm
27cc17887d59   none                      null      local
8jz7wbwgme2a   portainer_agent_network   overlay   swarm
0x4bx818li9w   test                      overlay   swarm

```

#### (3) Docker 노드 내용 수정

1. **Label 추가**

```bash
$ docker node update --label-add name=manager <Manager 컨테이너명>
$ docker node update --label-add name=worker1 <Worker1 컨테이너명>
$ docker node update --label-add name=worker2 <Worker2 컨테이너명>
```

2. **컨네이너 상세조회**

```bash
$ docker node inspect <컨테이너명>

# Label에 내용이 수정되었음
"Spec": {"Labels":{
						"name":"manager"
						}
					}
					
```

### 2. CA(Certificate Authority) 설치

####(1) CA 도커 컴포저 파일 배포 (docker-compose-ca.yaml)

```bash
# ../hlf-docker-swarm/test-network 위치에서 실행
$ docker stack deploy -c docker/docker-compose-ca.yaml hlf

Ignoring deprecated options:

container_name: Setting the container name is not supported.

Creating service hlf_ca_org1
Creating service hlf_ca_org2
Creating service hlf_ca_org3
Creating service hlf_ca_orderer
```

***생성된 CA 컨테이너**

Manage 노드 : **`hlf_ca_orderer`, `hlf_ca_org1`**

Worker1 노드: **`hlf_ca_org2`**

Worker2 노드: **`hlf_ca_org3`**

#### (2) CA를 통한 인증 및 피어와 주문자(Orderer) 생성

**`registerEnroll.sh`** 을 실행시킨다.

1. 조직 구성원 생성 및 인증(CA, Org admin, peer)

```bash
# 모든 명령어는 /hlf-docker-swarm/test-network 경로에서 실행

# registerEnroll.sh의 메서드를 사용하도록 터미널창에 적용
$ source ./organizations/fabric-ca/registerEnroll.sh

##### 1.Manager 노드 #####
# Manager 노드에서 Org1 구성원 생성 메서드 실행
$ createOrg1
# Manager 노드에서 Orderer 구성원 생성 메서드 실행
$ createOrderer

##### 2.Worker1 노드 #####
# Worker1 노드에서 Org2 구성원 생성 메서드 실행
$ createOrg2

##### 3.Worker2 노드 #####
# Worker2 노드에서 Org3 구성원 생성 메서드 실행
$ createOrg3


```

**구성원 및 인증키들이 생성(Manager 노드 기준)**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/ca1.png?raw=true)

#### (3) Manager 노드에 Org2, Org3 인증서 복사

1. **파일질라(Filezilla) 설치 및 AWS EC2 인스턴스와 연동**

블로그 참고: https://babamba-playground.tistory.com/27

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/ca2.png?raw=true)

2. **Org2(Worker 1노드), Org2(Worker2 노드)에 있는 인증서 Manager 노드로 복사** 

`/home/ubuntu/hlf-docker-swarm/test-network/organizations/peerOrganizations`

경로에  있는 폴더 다운로드 후 Manager노드에 업로드

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/ca3.png?raw=true)

3. **Manger 노드에 있는 `ordererOrganizations` 폴더 다운로더 후 Worker1, Worker2 노드로 복사**

`/home/ubuntu/hlf-docker-swarm/test-network/organizations/ordererOrganizations`

4. **최종 생성 확인**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/ca4.png?raw=true)



## 채널(Channel) 구성요소 생성

### 1. configtx.yaml 설정 파일 분석

블로그 참고자료: [configtx.yaml 이해]( https://velog.io/@jaehyuen/Hyperledger-Fabric-2.2-%EC%8B%A4%EC%8A%B5-3-Fabric-%ED%8A%B8%EB%A0%8C%EC%A0%9D%EC%85%98-%EB%B0%8F-Genesis-block-%EC%83%9D%EC%84%B1#configtx.yaml-%EC%84%A4%EC%A0%95-%ED%8C%8C%EC%9D%BC-%EB%B6%84%EC%84%9D)

Hyperledger Fabric 네트워크 구축시 필요한 여러가지 트랜젝션(채널 생성, 앵커피어 설정) 들과 genesis.block 을 Fabric에서 제공하는 `configtxgen` 툴을 사용하여 생성합니다.

### 2. 제네시스 블록(Genesis block) 생성

#### (1) 제네시스 블록이란?

**제네시스블록**(genesis block)은 블록체인에서 생성된 **첫 번째 블록**을 말한다. 첫 번째 블록이 생성된 이후에 다음 블록이 지속적으로 생성되어 마치 체인처럼 이전 블록에 연결되기 때문에, 제네시스블록이 생성되었다는 것은 해당 **블록체인 네트워크가 시작되었다는 상징적 의미**를 가지고 있다.

#### (2) 제네시스 블록 생성

```bash
# ../hlf-docker-swarm/test-network 에서 실행
# 1. 제네시스 블록생성
$ ./scripts/createGenesis.sh

# 2. createGenesis.sh 소스
# configtxgen tool을 이용한 제네시스블록 생성
function createConsortium() {

  which configtxgen
  if [ "$?" -ne 0 ]; then
    fatalln "configtxgen tool not found."
  fi

  infoln "Generating Orderer Genesis block"

  # Note: For some unknown reason (at least for now) the block file can't be
  # named orderer.genesis.block or the orderer will fail to launch!
  set -x
  configtxgen -profile TwoOrgsOrdererGenesis -channelID system-channel -outputBlock ./system-genesis-block/genesis.block
  res=$?
  { set +x; } 2>/dev/null
  if [ $res -ne 0 ]; then
    fatalln "Failed to generate orderer genesis block..."
  fi
}
```

**생성된 블록 확인**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/genesis1.png?raw=true)



### 3. 채널(Channel)  및 앵커피어(Anchor Peer) Artifact 생성 

#### (1) 채널 및 앵커피어 Artifact 생성

```bash
# ../hlf-docker-swarm/test-network 에서 실행
# 1. 채널 artifact 생성 트랜잭션 실행
$ ./scripts/createChannelTx.sh

# 2. createChannelTx.sh 소스

createChannelTx() {
	set -x
	configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/${CHANNEL_NAME}.tx -channelID $CHANNEL_NAME
	res=$?
	{ set +x; } 2>/dev/null
	if [ $res -ne 0 ]; then
		fatalln "Failed to generate channel configuration transaction..."
	fi

}

createAncorPeerTx() {
	for orgmsp in Org1MSP Org2MSP Org3MSP; do

	infoln "Generating anchor peer update transaction for ${orgmsp}"
	set -x
	configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/${orgmsp}anchors.tx -channelID $CHANNEL_NAME -asOrg ${orgmsp}
	res=$?
	{ set +x; } 2>/dev/null
	if [ $res -ne 0 ]; then
		fatalln "Failed to generate anchor peer update transaction for ${orgmsp}..."
	fi
	done
}
```

**생성된 채널 및 앵커피어 확인**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/channel1.png?raw=true)

#### (2) 생성된 채널 및 앵커피어 Artifact를 다른 노드에 복사

FileZilla를 이용하여 Manager 노드에 있는 `channel-artifacts` 폴더를 Worker1, Worker2노드에 동일 한 경로에 복사

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/channel2.png?raw=true)



## 피어(Peer) 및 주문 서비스(Ordering Service) 시작

 ### 1. Docker-compose를 통한 피어, 주문자, CouchDB 컨테이너 생성

#### (1) 피어, 주문자, CouchDB 생성

```bash
$ docker stack deploy -c docker/docker-compose-test-net.yaml -c docker/docker-compose-couch.yaml hlf

```

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/order1.png?raw=true)

#### (2) 현재까지 생성된 패브릭 구성

Orderer CA: 1개 (Manager 노드)

Orderer(순서지정자) : 5개 (Manager 노드)  

Org CA : 3개 (각 노드당 하나)

Peer : 3개 ( 각 노드당 하나)

CouchDB : 3개 ( 각 노드당 하나)

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/order2.png?raw=true)



## 채널 운영(Channel Operation)

### 1. 채널 생성

#### (1) Docker-compose를 이용하여 `docker-cli` 컨테이너 생성

cli를 통하여 `fablic-tool`의 커맨드를 사용할 수 있다. 

```bash
$ docker stack deploy -c docker/docker-compose-cli.yaml hlf
```

  ![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/cli1.png?raw=true)

**각 노드에 생성된 `cli` 컨테이너 확인**

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/cli2.png?raw=true)



#### (2) docker cli 컨테이너 bash 접속 

```bash
# Fablic-tool를 사용하기 위해서 cli 컨테이너 bash 접속
# docker exex -it <cli 컨테이너 ID> bash

$ docker exec -it 0eefd780193c bash
```

다음과 같이 실행되면 정상

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/cli3.png?raw=true)

#### (3) 채널(Channel) 생성

```bash
# cli 컨테이너 내에서 채널명을 사용할 수 있도록 선언
export CHANNEL_NAME=mychannel

# 확인
echo $CHANNEL_NAME
mychannel

# 채널 생성 스크립트 실행
./scripts/create_app_channel.sh
```

채널 생성 후 `channel-artifacts` 에 `my channel.block` 이 생긴걸 확인 할 수 있다.

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/cli4.png?raw=true)

#### (4) 피어(Peer)를 채널(Channel)에 참가

❗️`./channel-artifacts/mychannel.block` 를 Worker1, Worker2 노드에 동일한 경로에 복사해준다.

```bash
# 피어를 채널에 참가(모든 노드에서 실행)
peer channel join -b ./channel-artifacts/mychannel.block

# 다음과같이 나오면 정상적 실행
2021-05-24 07:43:32.142 UTC [msp.identity] Sign -> DEBU 02a Sign: digest: 96AC2C8CACAF29D56EA7276A43A4B857AE94414628DBFA0D47DD6BA2498DCA8F
2021-05-24 07:43:32.472 UTC [channelCmd] executeJoin -> INFO 02b Successfully submitted proposal to join channel
```



####(5) 앵커 피어(Ancor Peer) 설정하기

**앵커피어**는 조직 간의 피어들에 대한 **정보 교환의 대리인**으로 사용된다. 서로에 대한 위치를 알게 되어 아무 조직의 Peer에 Proposal을 보내도 모두에 적용될 수 있게 되며, MSP에 대한 공유도 가능해진다. **적어도 하나의 앵키피어가 채널 설정시 정의되야하며**, 채널에 참여하는 모든 피어들은 제네시스 블록안에 기록된 앵커피어에 대한 정보를 공유하게 된다. (앵커피어가 1개일 경우 서로 다른 B,C의 조직은 A조직의 그 앵커피어를 통해서 서로에 대해 알게되고 MSP를 직접 교환하게 된다)

```bash
# 앵커피어 설정
# ./scripts/updateAnchorPeer.sh <채널명> <조직명MSP>

# 1. Manager 노드
./scripts/updateAnchorPeer.sh mychannel Org1MSP

# 2. Worker1 노드
./scripts/updateAnchorPeer.sh mychannel Org2MSP

# 3. Worker2 노드
./scripts/updateAnchorPeer.sh mychannel Org3MSP
```

