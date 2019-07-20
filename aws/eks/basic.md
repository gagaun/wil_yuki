# EKS 를 시작하기 전에 알아야 할 것

행동하는 개발자가 되기 위해 짬이나는 시간마다 작업하여 AWS 에 서버 어플리케이션을 올리고, 플러터로 모바일 개발도 입문해서 간단한 앱을 런칭해 보고 싶었다.
컨테이너 배포, 관리는 쿠버네티스로 하면 쉽겠지? (인프라가 넘나 잘갖춰진 사내에서는 적어도 그랬다..) 라고 생각했다. 하지만.. 

시작 : 아마존에 쿠버네티스 사용해서 서비스 올려야지! 문서 읽어보면 한 이틀이면 되지 않을까?

현재:  IAM 무엇..? EKS 무엇..? VPC..? EKS쓰면 k8s control palne을 관리 안해도 된다고 아하~! (그게몬데..?) ㅎ..

하.. 떨리는 손을 부여잡고 일단 AWS에서 쿠버네티스를 효율적으로 사용하기 위해, 정확히 알지 못하는 용어는 정리해가며 여유를 갖고 보기로했다.

---


### 클라우드 컴퓨팅
https://aws.amazon.com/ko/types-of-cloud-computing/?WICC=tile&tile=types_of_cloud

아마존 AWS 같은 클라우드 서비스 제공업체가 네트워크 연결 하드웨어를 소유 및 유지 관리를 담당하고, 사용자는 필요한 IT 리소스를 프로비저닝하여 사용하는 개념. 필요한 만큼 리소스에 빠르게 액세스하고, 사용한만큼 비용을 지불한다. 
개발자가 구매,유지, 용량 계획같은 획일적 작업을 피할 수 있는 역량을 제공한다. 사용자의 특정 필요를 충족하는 모델 및 배포전략을 사옹해야 효율적 이용이 가능. 

#### 클라우드 컴퓨팅 모델
https://brunch.co.kr/@leedongins/60

##### IaaS (Infrastructure as a Service)
클라우드 서비스에서 IaaS는 확장성이 높고 자동화된 컴퓨팅 리소스를 가상화하여 제공한다. 컴퓨팅, 네트워킹, 데이터 스토리지 공간을 제공

##### PaaS (Platform as a Service)
어플리케이션 개발에 필요한 플랫폼을 제공. 기본 인프라 (하드웨어, 운영체제) 를 관리할 필요가 없어 애플리케이션 개발과 관리에 집중할 수 있다.

##### Saas (Software as a Service)
완전한 제품을 고객에게 제공


#### AWS EC2 
https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/concepts.html

##### AWS의 대표적인 IaaS 서비스 컴포넌트 
* 가상 컴퓨팅 환경 인스턴스를 제공하고
*  설정된 OS 와 소프트웨어셋이 포함된 제품을 제공하거나 AMI (Amazon Machine Image) 를 사용해 적절히 구성된 템플릿으로 인스턴스를 쉽게 만들 수 있다.
* 프로토콜, 포트, 소스 IP 범위를 지정하는 방화벽 기능 제공
* 동적 클라우드 컴퓨팅을 위한 고정. IPv4 주소 제공
* VPC를 이용한 네트워크 구성 : https://www.44bits.io/ko/post/understanding_aws_vpc

#### 아마존에서 쿠버네티스 사용하기
https://aws.amazon.com/ko/eks/
AWS에서 Kubernetes 를 사용하는데는 두 가지 방법이 있다. 

1) EC2 인스턴스에서 직접 실행하거나 

2) AWS에서 제공하는 관리형 서비스 EKS 를 사용하는 방법 

서울 리전에서는 2019년 1월 EKS를 사용할 수 있게 되었다. 

Amazone eks workshop : https://eksworkshop.com/
Document for Eks:  https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/what-is-eks.html

두 문서를 훑어보니.. EKS 를 사용하기 위해 AWS 관련 사전 지식이 필요하다. 
- AWS Virtual Private Cloud (VPC)
 - https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/what-is-amazon-vpc.html
- ColudFormation 
 - https://docs.aws.amazon.com/ko_kr/AWSCloudFormation/latest/UserGuide/Welcome.html
- AWS Identity and Access Management (IAM)
 - https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/introduction.html

간단히, 뭔지, 왜 EKS 클러스터 구성에 필요한지만 알아보자

### AWS VPC 
### CloudFormation
### AWS IAM

---



### 쿠버네티스 기본 개념과 용어
https://kubernetes.io/docs/concepts/


Kubernetes API objects 로 클러스터 state 를 설정해야한다.
- container, Image
- replica set count, network, disk...

kubectl 이용해서 state 설정하면, 클러스터의 현재상태를 이에 맞게 일치시키는게 Kubernetes Control Plane 이다.



#### Kubernetes Control Plane

쿠버네티스 마스터와 kubelet 프로세스와 같은 쿠버네티스 컨트롤 플레인의 다양한 구성 요소는 쿠버네티스가 클러스터와 통신하는 방식을 관장한다. 
컨트롤 플레인은 시스템 내 모든 쿠버네티스 오브젝트의 레코드를 유지하면서, 오브젝트의 상태를 관리하는 제어 루프를 지속적으로 구동시킨다. 컨트롤 플레인의 제어 루프는 클러스터 내 변경이 발생하면 언제라도 응답하고 시스템 내 모든 오브젝트의 실제 상태가 사용자가 바라는 상태와 일치시키기 위한 일을 한다.

예를 들어, ***쿠버네티스 API를 사용해서 디플로이먼트를 만들 때에는, 바라는 상태를 시스템에 신규로 입력해야한다. 쿠버네티스 컨트롤 플레인이 오브젝트 생성을 기록하고, 사용자 지시대로 필요한 애플리케이션을 시작시키고 클러스터 노드에 스케줄링한다. 그래서 결국 클러스터의 실제 상태가 바라는 상태와 일치하게 된다.***

##### 쿠버네티스 마스터
클러스터에 대해 바라는 상태를 유지할 책임은 쿠버네티스 마스터에 있다. 
kubectl 커맨드라인 인터페이스와 같은 것을 사용해서 쿠버네티스로 상호 작용할 때에는 쿠버네티스 마스터와 통신하고 있는 셈이다.

“마스터”는 클러스터 상태를 관리하는 프로세스의 묶음이다. 
주로 모든 프로세스( kube-apiserver, kube-controller-manager, kube-scheduler)는 클러스터 내 단일 노드에서 구동되며, 이 노드가 바로 마스터이다. 
마스터는 가용성과 중복을 위해 복제될 수도 있다.

##### Kubernetes Node
클러스터 내 마스터 노드가 아닌 각각의 노드는 다음 두 개의 프로세스를 구동시킨다.
- 쿠버네티스 마스터와 통신하는 kubelet.
- 각 노드의 쿠버네티스 네트워킹 서비스를 반영하는 네트워크 프록시인 kube-proxy.

--- 

### 다시 EKS 문서를 읽어본다..
Amazon Elastic Kubernetes Service(Amazon EKS)는 **Kubernetes 제어 플레인을 설치하고 운영할 필요 없이 AWS에서 Kubernetes를 손쉽게 실행**하도록 하는 관리형 서비스입니다

#### 그래서 EKS 가 해주는것 (내가 신경 안써도 되는 것)
- 여러 가용 영역에서 Kubernetes 제어 플레인 인스터스를 실행
- 비정상 제어 플레인 인스턴스를 자동으로 감지하고 교체
- 버전 업그레이드 및 패치

#### EKS Control Plane Architecture
##### Single tenant Kubernetes control plane for each cluster
Kubernetes RBAC 정책에 따라 control plane 구성요소(마스터-워커 노드)간의 트래픽을 단일 클러스터내로 제한한다.
- 이를 위해서 **Amazon 네트워크 정책 VPC 가 사용**된다.
- Control Palne은 리전 내 세 개의 가용 영역에서 실행되는 세 개의 etcd 노드와 두 개 이상의 API 서버 노드로 구성





#### EKS 작동방식
![eks workflow](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/images/what-is-eks.png)
이렇게 읽어보면 또 간단하다.. 

1. aws console 이나 aws cli로 eks 클러스터 생성 (마스터 노드 생성)
2. aws CloudFormation 템플릿으로 자동 구성되는 노드 셋을 eks 클러스터에 워커노드로 등록 
3. kubectl 로 클러스터와 통신
4. 애플리케이션 배포 및 관리 

----
참고자료

서비스 중단 없이 Amazon EKS로 옮긴 이야기
http://engineering.vcnc.co.kr/2019/02/eks-migration/

번개장터 기술블로그
https://bunjang.github.io/2019/02/20/bunjang-k8s-1.html





