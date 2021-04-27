## 블록체인의 종류

블록체인은 크게 퍼블릭 블록체인, 프라이빗 블록체인, 컨소시움 블록체인 3가지로 분류할 수 있습니다.

- **퍼블릭 블록체인(Public Blockchain)**: 비트코인, 이더리움과 같이 누구나 네트워크에 참여할 수 있는 블록체인
- **프라이빗 블록체인(Private Blockchain)**: 하나의 기관에서 독자적으로 사용하는 블록체인
- **컨소시움 블록체인(Consortium Blockchain)**: 여러 기관들이 컨소시움을 이뤄 구성하고 허가된 기관만 네트워크에 참여할 수 있는 블록체인.

현재 활발하게 거래되는 대다수의 블록체인은 퍼블릭 블록체인으로 블록체인 네트워크에 참여할 수 있고 트랜잭션 내역을 검증할 수 있습니다.



## 퍼블릭 블록체인 VS 프라이빗(컨소시움) 블록체인



|                       | 퍼블릭 블록체인                                              | 프라이빗(컨소시움) 블록체인                      |
| :-------------------- | :----------------------------------------------------------- | :----------------------------------------------- |
| **읽기 권한**         | 누구나                                                       | 허가된 기관                                      |
| **거래 검증 및 승인** | 네트워크에 참여하면 거래 검증 및 승인                        | 승인된 기관 및 감독기관                          |
| **트랜잭션 생성자**   | 누구나                                                       | 법적 책임을 지는 기관                            |
| **합의 알고리즘**     | 부분 분기를 허용하는 작업증명(PoW)이나 지분증명(PoS) 알고리즘 | 부분분기를 허용하지 않는 BFT계열의 합의 알고리즘 |
| **속도**              | 7~20 TPS                                                     | 1000 TPS 이상                                    |
| **권한 관리**         | 누구나                                                       | 통제된 인원                                      |
| **예시**              | 비트코인, 이더리움                                           | IBM Fabbric, LoopChain                           |



## 하이퍼레저 프로젝트란?

![](https://github.com/ryanlee5646/hyperledger_fablic/blob/main/images/hyperledger1.png?raw=true)

리눅스 재단에서 주관해 세계 기업들이 공동으로 참여하고 있는 '범산업용 분산원장 표준화 프로젝트'이다. 하이퍼레저는 12가지 블록체인 프로젝트가 진행되고 있으며, 분산원장을 위한 6개의 프레임워크와 6개의 블록체인 툴로 구성돼 있다.



## 하이퍼레저 프레임워크 종류

#### **하이퍼레저 소투소**

  \- 인텔의 Inel Distributed Ledger 기반

  \- PoET(경과시간증명) 컨센서스 알고리즘이 적용

  \- 인텔의 SGX 기술을 기반으로 구현

#### **하이퍼레저 이로하**

  \-  모바일 어플리케이션 개발에 초점

  \- c++ 디자인 기반 

  \- BET 컨센서스 알고리즘 

#### **하이퍼레저 버로우**

 \- EVM(Ethereaum Virtual Machine) 기반 스마트 컨트랙트 인터프리터를 내장해 블록체인 클라이언트 서비스 제공

#### **하이퍼레저 인디** 

 \- 인증에 특화된 프로젝트

 \- 블록체인에 기반한 독립적인 디지털 아이덴티티 레코드(개인 식별자)를 생성하고 사용 할 수 있게 툴 및 라이브러리, 재사용 컴포넌트 제공

#### **하이퍼레저 패브릭**

 \- 모듈러 아키텍처 기반의 어플리케이션/솔루션 개발

 \- 컨센서스 알고리즘이나 멤버십 서비 등 핵심 기술 요소를 플러그 앤 플레이 방식으로 구현할 수 있도록 지원

하이퍼레저 컴포저

하이퍼레저 캘리퍼



## 하이퍼레저 패브릭 목표

 \- 허가된 참여자를 대상으로 하는 비즈니스  응용환경에 맞는 블록체인

 \- 다양한 요구사항을 가진 분산 응용 서비스를 효율적으로 지원할 수 있는 개발 플랫폼

 \- 모듈러 아키텍처 기반 분산 응용 플랫폼 (컨센서스 알고리즘, 멤버쉽 서비스 등의 모듈을 필요에 따라 교체 가능)



## 하이퍼레저 패브릭 특징

 **Permissioned BlockChain(퍼머션드 블록체인)**

  \- 허가된 사용자만 접근을 허용, 접근 권한 제어

  \- 폐쇠망 형태의 프라이빗 블록체인을 구성하는데 최적화 되어있다.

 **일반 프로그래밍 언어 사용**

  \- Go, Java 등

  \- 스마트컨트랙트의 결과가 항상 동일하다고 보장할 수는 없지만 내부 키의 상태 변환 값에 의해 비 결정적 오류를 해결해 준다.

 **높은 성능**

  \- 서로 다른 엔도싱 피어 노드에게 체인코드를 실행시키도록 할 수 있다. 이로 인해 키에 대한 버전 관리를 통해 동시 처리에 다른 비 결정적 실행 문제점을 해결해 높은 성능을 낼 수 있다. 

**교체 가능한 모듈러 아키텍처**

 \- 모듈형 구조이기 때문에 오더링 서비스 노드에서 순서화를 통해 3가지 컨센서스 알고리즘(Solo, Kafka, PBET)을 선택할 수 있다. 

****

**멀티 블록체인 지원**

 \- 채널이라는 분할된 네트워크로 멀티 블록체인을 지원한다.



하이퍼레저 패브릭 아키텍처

 **신원확인**

 \- 네트워크에 접속하려면 신원확인을 가장먼저 해야한다.

 \- 프라이빗 블록체인을 지향하며 권한을 가진 참여자만 기록,수정,삭제를 할 수 있기 때문에

 \- **MSP(Membership Service Provider)**를 통해 신원확인

****

 **Ledger(원장)**

  \- 트랜잭션 정보가 실제로 저장되는 공간

  \- 데이터를 관리하는 분산원장 데이터베이스이며, 이를 처리하기위한 기



 **Transaction**

  \- 체인코드를의 실행을 의미

****

 **Chaincode**

  \- 스마트 컨트랙트를 의미

  \- 프라이빗 블록체인에서 기업 및 컨소시엄으로 구성된 서비스에 맞게 블록체인을 활용할 수 있도록 비즈니스 로직을 구현할 수 있다.





하이퍼레저 패브릭 구성요소

Channel  

  \- 네트워크에서 구성요소 간 그룹을 나눠 트랙잭션을 수행해야 할 때 사용

  \- 트랜잭션의 접근 권한을 그룹별로 설정(프라이빗 블록체인 기술요소)



Organization

  \- 네트워크는 조직 단위로 구성, 조직별로 피어노드 관리 및 권한 부여, 보증 정책 등을 수행

  \- 클라이언트(네트워크 참여자)의 접근 권한도 관리



Peer Node

  \- 가장 기본적인 네트워크 구성 요소

  \- 네트워크를 유지하고 트랜잭션의 제안 및 응답을 처리

  \- 원장과 체인코드를 관리하고 저장하는 역할



● 피어노드의 역할에 따른 분류

 \- Endorsing Peer Node : Endorsing Policy(보증정책)에 따라 요청된 트랜잭션을 먼저 실행해보는 검토역할을 수해  한 후 트랜잭션에 보증사인을 첨부하는 역할

 \- Commiting Peer Node :  Endorsing Peer Node가 실행한 트랙잭션 결과에 문제가 없으면 트랜잭션을 확정하고 그 내용을 블록체인에 업데이트하는 역할

 \- Anchor Peer Node : 채널 내에 대표 역할을 하는 Peer Node

 \- Leader Peer Node : 조직에서 모든 Peer Node를 대표 



**Orderer(오더링 서비스 노드)\**\***

 \- 피어 노드에게 트랜잭션을 전달에 전체 블록체인 네트워크가 작동하도록 컨트롤

 \- 채널에 대한 구성정보를 소유하고 정보를 기반으로 전체시스템의 관리자 역할

 \- 트랜잭션을 받으면 시간 순서에 따라 정리해 Peer Node에 전달

 \- 트랜잭션 정보를 네트워크에 반영해 새로운 블록을 추가하고 업데이트



오더링 서비스 알고리즘 

 Solo Ordering Service

 \- 하나의 중앙집중형 오더링 서비스가 모든 트랜잭션의 순서를 결정

 \- 가용성, 확장성을 고려하지않음(테스트용으로 적합)



 Kafka Ordering Service

 \- Pub/Sub 구조의 메시지큐 미들웨어인 카프카 기반 오더링 서비스

 \- 결함으로 인해 피어 노드가 멈췄을 때 다른 분산 오더링 서비스를 제공할 수 있다.

 \- 높은 처리량과 고가용성을 제공하지만 비잔티움 문제에 노출돼 있는 한계를 가짐



 RAFT Ordering Service

 \- CFT(Crash Fault Tolerant) 알고리즘 기반 오더링 서비스

\- 채널당 선출된 리더 노드가 모든 트랜잭션을 처리하고 그 결과가 나머지 노드에게 복제되는 '리더 및 팔로워'모델에 기반한다.

\- 비잔티움 운제를 해결하기 위한 BET계열 알고리즘

\- 패브릭 자체 내장 기술



CFT(Crash fault tolerance)란

일부 시스템 구성 요소들이 작동하지 않더라도 올바른 합의에 도달할 수 있는 성질을 말한다. 기존 퍼블릭 블록체인에서 주로 사용되는 BFT(Byzantine fault tolerance) 시스템은 시스템 구성요소의 기능적 문제뿐 아니라 악의적인 공격(Malicious attack)까지 고려하므로 CFT 시스템과 비교했을 때 더욱 복잡하며 느리다.



MSP(Membership Service Provider)- 멤버쉽 서비스 제공자

 \- 네트워크에 인증 서비스를 제공하는 역할

 \- 네트워크 참여자에게 책임 부여

 \- 행위를 추적하는 책임성 보장

 \- 트랜잭션 익명성 및 트랜잭션 비연결성을 보장하는 프라이버시도 보장

 \- 노드들의 신원, 역할, 소속, 권한을 관리하고 조직 구조 설계



CA(Certification Authority)

  \- MSP에서 암호화 인증을 위해 필요한 인증기관

  \- 공개키 인증서 및 이에 대응하는 개인 키를 발급

  \- 네트워크를 구성하는 조직에는 루트인증서(Root Certificate)를, 네트워크 접속 사용자에게는 신원등록 인증서(Enrollment Certificate, Ecert)를 발급

  \- 1.2버전 전 까지 X.509, 이후 아이덴티티 믹서가 추가돼 익명성 보장 암호화기법으로 신원 증명



## 하이퍼레저 환경설정



> ### 개발 환경
>
> - 가상화 환경 소프트웨어
>   - Oracle Virtual Box 6.0.14
> - 리눅스 게스트 OS
>   - Ubuntu 16.04.x
> - 필요 도구 및 소프트웨어
>   - cURL
>   - Docker Community Edition 17.06 이상
>   - Docker Compose 1.14.0 이상
> - Golang 1.11.x
> - Git 2.9.x 이상
> - Python
> - Node.js



## 1. VirtualBox에 우분투 리눅스 설치

> Ubuntu 18.04.1 LTS

### (1) cURL 설치

cURL은 URL을 통해 데이터를 전송할 수 있는 도구이다.

HTTP, HTTPS 뿐만 아니라 FTP, SMTP 등 많은 프로토콜을 지원한다.

```null
$ sudo apt install curl
$ curl -v
```



### (2) Docker 설치

도커는 컨테이너 기반의 오픈소스 가상화 플랫폼이다. 애플리케이션을 컨테이너라는 계층으로 격리시켜 OS에 관계없이 컨테이너 안에서 개발, 배포, 운영할 수 있도록 제공하고, 어느 환경에서도 동일하게 실행한다.

```null
$ curl -fsSL https://get.docker.com/ | sudo sh

// user 계정 추가
$ sudo usermod -aG docker $USER
$ sudo reboot

$ docker -v
```



### (3) Docker Compose 설치

도커 컴포즈는 여러 개의 도커 컨테이너를 정의하고 실행하는 개발자 편의 도구이다. YAML 파일을 사용해 각 컨테이너들의 설정 정보를 쉽게 정의할 수 있으며, 컨테이너를 명령어로 간단히 생성하고 시작할 수 있다.

```null
$ sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose


// 실행 권한 설정
$ sudo chmod +x /usr/local/bin/docker-compose
```



### (4) Golang

```null
$ cd /usr/local
$ sudo wget https://storage.googleapis.com/golang/go1.11.1.linux-amd64.tar.gz
$ sudo tar -C /usr/local/ -xzf go1.11.1.linux-amd64.tar.gz

// 환경변수 설정
GOPATH 등 설정
```

- GOPATH : Go로 개발시 필요한 작업 공간과 같은 개념으로, 외부 라이브러리나 패키지, 툴 소스 등을 받아노느 위치를 지정한다.



### (5) Git

```null
sudo apt-get install git
git --version
```

- 책은 오타같다^^



### (5) Python

> Python 2.7버전 설치

```null
sudo apt install -y python
python --version
	Python 2.7.17
```



### (5) Node.js, npm

**Hyperledger Fabric SDK**에서 Node.js를 사용한다. Node.js는 확장성 있는 네트워크 애플리케이션 개발에 사용되는 소프트웨어 플랫폼이다.

**Node.js**는 자체적으로 HTTP 서버 라이브러리를 포함하고 있기 때문에 아파치와 같은 별도의 웹 서버가 없어도 웹 서비스를 제공한다.

```null
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
$ sudo reboot

// nvm 커맨드 명령어로 설치
$ nvm install 8

// node.js 버전 8 선택
$ nvm use 8
```



**npm**은 Node Package Manager의 약자로 자바스크립트 프로그래밍 언어를 위한 패키지 관리자이다. Node.js를 설치하면 npm이 설치된다.

```null
$ npm install npm@5.6.0 -g

$ node -v
v8.16.2
$ npm -v
5.6.0node 
```



### (6) VSCode 설치

우분투에 설치



### (7) JAVA JDK 설치

> 자바 버전 8 설치

자바로 체인코드 개발을 위한 자바 설치

```null
$ sudo add-apt-repository ppa:openjdk-r/ppa
$ sudo apt update
$ sudo apt install openjdk-8-jdk openjdk-8-jre
```



### (8) Gradle

자바 빌드 툴

```null
sudo apt install gradle
```



### (9) IntelliJ IDEA

우분투에 설치

