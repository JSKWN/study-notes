

## 관련 개념/용어 정리

---

### 1. 컨테이너 & 도커
![|685x271](image_docker_computer_system.png)


- 컨테이너: 이미지를 격리하여 독립된 공간에서 실행한 가상 환경
    - 호스트 OS 상에 마련된 논리적인 파티션(영역)이며, 애플리케이션과 필요한 라이브러리(+종속성, 시스템도구, 런타임 환경 등)를 묶은 것을 말함
- 도커: 컨테이너 기술을 구현하는 런타임 및 도구 모음.
    - 이미지: 컨테이너의 실행에 필요한 파일, 설정 값(환경 변수, 실행 명령어 등)을 포함한 읽기 전용 템플릿 → 레이어들이 모여 이미지를 구성, 이걸 실행해야 컨테이너가 됨
        - 도커 레지스트리: 도커 이미지를 저장하고 배포하는 중앙 저장소 (push pull 등)
            
        - 레이어 구조: 하나의 큰 파일이 아니라, 여러 개의 읽기 전용 레이어가 쌓인 형태, OS 파일 시스템 레이어 → 라이브러리 및 설정 파일 레이어 → 사용자의 애플리케이션 코드
            
        - 도커파일(Dockerfile): 도커 이미지를 생성(Build)하기 위해 작성하는 설정 파일(스크립트)
            
            - IaC(Infrastructure as Code): 인프라 구성을 코드로 관리하는 방식
                - 구성: 베이스 이미지 지정(From), 명령어 실행(Run), 파일 복사(COPY), 컨테이너 시작시 초기 명령(CM) 등의 인스트럭션(Instruction)으로 구성
        - 도커데몬(dockerd): 호스트 OS 백그라운드에서 실행되는 프로세스로, ‘도커 API 요청’을 수신하고 도커 객체(이미지, 컨테이너, 네트워크, 볼륨)를 관리하는 객체 → 사용자가 docker run 등의 명령어를 입력하면 도커 데몬이 받아서 컨테이너를 생성하고 실행
            
            - 흐름: Dockerfile을 도커 데몬이 읽고, 읽기전용 이미지를 생성 → 생성된 이미지를 레지스트리에 업로드 → 도커 데몬이 이미지를 가져와서 읽기/쓰기(R/W) 레이어를 얹고, 네임스페이스 및 Cgroup으로 격리하여 Container 프로세스를 실행
        - 🔧 (추가 확인 필요) Union File System: 이러한 레이어들을 합친 것을 하나의 파일 시스템처럼 보이게 만드는 기술
        
            ![[container_sturcture_example.png|참고: https://www.openmaru.io/컨테이너-파일-시스템|646x363]]
            
            - Git과 유사한 전략: 공유되는 레이어는 재사용, 이미지는 레이어 단위로 관리. (모든 시스템이 동일한 OS(예, 우분투)만 사용한다면, 하나의 우분투 레이어를 다른 컨테이너 구성에서도 활용
            - 예로, 폴더A (OS 레이어), 폴더 B(라이브러리 레이어), 폴더 C(코드 레이어) 라면 이들은 디스크 상에 따로 저장되어 있음. UFS는 따로 있는 폴더들을 겹쳐 하나로 보여주게됨
            - 겹쳐놓은 상태까지는 “도커 이미지(**≈**CD)”임, 컨테이너가 되기 위해서는 이미지를 실행 (docker run)해야, 자원(CPU 및 메모리)을 할당하고 프로그램을 작동시킴 (이미지는 ‘읽기 전용’이나, 컨테이너가 실행될 때, 이미지 레이어의 최상단에 읽기/쓰기 레이어가 추가됨)
            - 네임스페이스를 이용한 컨테이너 격리, cgroup은 자원 관리 (메모리 및 CPU, 디스크 I/O 속도 제한)

### 2. 쿠버네티스(k8s)

- 도커 지원 중단: 쿠버네티스는 수많은 컨테이너를 관리하는 도구였고, 도커와 의존성이 있었으나, 2020년 12월에 도커 지원이 중단되었음. 이제는 CRI 호환 런타임이 필요하다고 함
    - 도커 말고도 다양한 컨테이너 런타임들이 있고, CRI는여러 컨테이너들과 통신할 수 있는 표준 인터페이스임
    - 도커에 비해 CRI는 최신기술이라 호환이 안되었음
    - 현재는 컨테이너디(containerd)를 많이 사용(도커에서 분리됨)
- 여러대의 PC를 묶은 클라우드 환경을 구성하는데만 사용되는게 아니라, 어떤 작업에 대해 시스템 자원을 능동적으로 배분하고 파이프라인을 작동하는 도구로 활용될 수 있으며, 이식성 확보(다른 환경에서도 동작) 측면에서도 사용됨

참고 블로그: [https://velog.io/@todaybow/kubernetes-basic-concepts](https://velog.io/@todaybow/kubernetes-basic-concepts)
⭐(좋은 참고자료) 아마존: [https://aws.amazon.com/ko/what-is/kubernetes-cluster/](https://aws.amazon.com/ko/what-is/kubernetes-cluster/)

- 쿠버네티스는 컨테이너를 실행하는 플랫폼이자 이들의 오케스트레이션 도구
- 핵심은 쿠버네티스는 단순한 실행도구가 아닌 “선언적 API(Declarative API)”를 기반으로 작동하는 “상태 관리 시스템(State Management System)”이라는 점

### 핵심 개념

- 핵심 개념
    - 클러스터
    - 🔧 선언적 API
        - (🔧이해안됨)명령(Imperative, 예: “컨테이너를 실행하라”)이 아니라, 의도된 상태(desired stete, 예:”특정 애플리케이션의 복제본이 3개 유지되길 원한다.”)를 매니페스트(YAML)에 정의. 시스템은 현재 상태를 계속 확인하며 차이를 바로잡음
        - 쿠버네티스 클러스터의 목적은 애플리케이션 실행
        - 실행하려는 애플리케이션을 YAML 포맷으로 작성한 후, 쿠버네티스 구성요소 중 API Server를 통해 제출하면 애플리케이션이 클러스터에 배포
    - 오케스트레이션

### **쿠버네티스 클러스터 컴포넌트**
![[kubernetes cluster overview.png|공식문서: [https://kubernetes.io/ko/docs/concepts/architecture/](https://kubernetes.io/ko/docs/concepts/architecture/)|666x398]]


쿠버네티스 클러스터는 크게 관리 영역인 컨트롤 플레인(Control Plane)과 하나 이상의 워커 노드(Node)로 구성됨

- 컨트롤 플레인: 마스터 노드 위에서 돌아가는 소프트웨어 구성요소 집합(api server, etcd, scheduler 등)

A. 컨트롤 플레인(Control Plane)

- 클러스터의 상태를 관리하고 전체적인 의사결정을 내리는 마스터 영역
- **kube-apiserver**: 모든 요청의 유일한 진입점이며 인증/인가를 담당
- **etcd**: 클러스터의 모든 상태를 저장하는 저장소
    - _보안 뉘앙스_: 이곳에 저장되는 **Secret** 값은 기본적으로 암호화되지 않은 Base64 문자열입니다. 진정한 보안을 위해서는 etcd 설정에서 **Encryption at Rest(저장 시 암호화)**를 켜거나, 외부 Vault 솔루션을 연동해야 합니다.
- **kube-scheduler**: 리소스(CPU/GPU) 상태를 보고 Pod를 적절한 노드에 배치
- **kube-controller-manager**: 노드 컨트롤러 등 핵심 관리 루프를 실행
- **CoreDNS (Add-on):** 클러스터 내부의 **DNS 서버**입니다. my-database 같은 서비스 이름을 IP 주소로 변환

B. 노드

- **kubelet**: 마스터의 명령(PodSpec)을 받아 컨테이너 실행을 보장하는 에이전트
- **kube-proxy**: 노드의 네트워크 규칙(iptables 등)을 관리하여 서비스 트래픽을 라우팅
- **CNI Plugin (Container Network Interface)**:
    - **역할**: kube-proxy가 '교통 규칙'을 정한다면, **CNI**(Calico, Flannel, AWS VPC CNI 등)는 '실제 도로와 주소'를 만듭니다.
    - Pod에 IP를 할당하고, 서로 다른 노드에 있는 Pod끼리 통신할 수 있게 해주는 실질적인 네트워크 구현체입니다.
- **Container Runtime**: 컨테이너를 실행하는 엔진(containerd, Docker)입니다

### API 오브젝트

![[kubernetes objects.png|525x404]]
![[kubernetes cluster flow.png|https://developerwoohyeon.tistory.com/240|781x351]]

A. 워크로드 (workloads/compute)

애플리케이션(컨테이너)을 실행하고 관리하는 오브젝트

애플리케이션은 Pod에서 구동되는 프로그램을 의미, 컨테이너는 프로그램을 실행할 수 있는 환경을 묶은 상자

- Pod
    
    - 쿠버네티스에서 배포 가능한 가장 작은 실행단위
    - 하나 이상의 컨테이너 그룹으로 구성되며, 스토리지 및 네트워크를 공유
    - 사용자가 정의한 컨테이너 이미지들을 Pod (Yaml 명세서)에 담아 실행하는 방식 (이 Pod 안에는 A 이미지와 B 이미지를 같이 넣어 실행하라는 선언, Pod는 어떤 이미지를 실행할지를 적어놓은 주문서)
    - 예
    
    ```jsx
    apiVersion: v1
    kind: Pod
    metadata:
      name: 내-파드
    spec:
      containers:
      # 첫 번째 컨테이너 (사용자가 만든 웹서버 이미지)
      - name: main-app
        image: my-web-server:1.0
    
      # 두 번째 컨테이너 (보조용 로그 수집 이미지)
      - name: log-agent
        image: logging-agent:2.5
    ```
    
- Deployment
    
    - Pod 및 ReplicaSet을 관리하며, 배포 전략(업데이트 및 롤백 등)을 담당
    - 직접 Pod를 생성하기보단, ReplicaSet을 통해 복제본 수를 유지하고 관리하는 상위 개념
- ReplicaSet
    
    - Pod의 개수(Replica)를 일정하게 유지하는 역할
    - 보통은 Deployment가 이를 관리
- StatefulSet
    
    - 순서 및 고유성(ID)이 보장되어야 하는 애플리케이션(예: DB 등)에 사용
    - Pod의 이름이 `db-0` 또는 `db-1` 처럼 고정되어 상태(State)를 유지해야 하는 경우 적합함
- DaemonSet
    
    - 클러스터 내의 모든 노드에 해당 Pod가 반드시 실행되도록 보장
        - 애플리케이션 서비스보단, 클러스터 운영에 필요한 백그라운드 프로세스를 각 노드마다 실행할 때 사용함
        - 로그 수집, 모니터링, 스토리지 등의 데몬이 있음

B. 네트워크 (Network)

애플리케이션에 대한 접근 및 트래픽을 관리하는 오브젝트

- Service
    - 동적으로 생성/삭제되는 Pod 집합에, 고정된 IP(ClusterIP) 및 접근 포인트를 제공하는 추상화 계층
    - Pod의 환경이 바뀌어도 외부에서는 동일한 경로로 접근 가능
- Ingress (유입)
    - 외부에서 안으로 들어오는 트래픽(유입)을 의미
    - 클러스터 외부에서 내부로 들어오는 HTTPS 트래픽을 관리하는 L7 라우터 역할

C. 설정 및 스토리지 (configuration & storage)

애플리케이션의 설정값과 데이터를 관리하는 오브젝트

- ConfigMap
    - 일반적인 설정값(환경 변수, 설정 파일 내용 등)을 저장하여 Pod와 분리해 관리
- Secret
    - 비밀번호, 키 등 민감한 정보를 저장
- PV/PVC
    - 컨테이너가 재시작되어도 데이터가 사라지지 않고 저장되도록 스토리지 공간을 요청(PVC)하고 할당(PV)받는 오브젝트

D. 클러스터 관리 및 스케일링

리소스의 격리 및 자동 확장

- Namespace
    - 하나의 클러스터 내에서 리소스를 논리적으로 구분하여 격리하는 단위
    - 해당 Namespace에 여러 Pod가 속하게됨
- HPA
    - CPU 사용량이나 트래픽 등 부하에 따라 Pod의 개수를 자동으로 늘리거나 줄이는 기능을 수행

namespace: 동일한 네임스페이스에 여러 Pod가 속할 수 있음

- 동일한 네임스페이스에서는 pod의 이름만 사용해도 되지만, 다른 네임스페이스에 있을때는 전체 주소(namespace명 + pod명)을 사용해야함

예시: kubeflow-user 라는 네임스페이스, 아래와 같은 Pod가 존재할 수 있음

- notebook-server-pod (주피터 노트북)
- training-job-pod-1 (전처리 작업)
- training-job-pod-2 (모델 학습 작업)
- tensorboard-pod (시각화)


### 쿠버네티스(k8s)와 microk8s 및 k3s와의 차이점?

우려사항: kuberflow는 쿠버네티스 기반으로 운용되는데, 개인 환경에서 MLOps 구현 테스트, 환경 구축 등을 확인해보고자 할 때, 쿠버네티스는 상당히 무거운 툴임. 따라서 MicroK8s 및 K3s 등 경량화된 쿠버네티스를 활용하는게 옳은지, 그리고 이들 경량화 툴이, 기존 k8s와 뭐가 다른지 확인하고싶음.

- 쿠버네티스 작동 방식 관련
    
    1. 사용자에 Pod 할당
        - 원격에서 접속하면 사용 환경 관련 Pod(컨테이너)를 할당
        - 사용자는 마치 클라우드에 있는 개인 PC처럼, 해당 컨테이너 내부에서 코드를 작성
    2. 애플리케이션 Pipeline에 Pod 할당 (kubeflow의 경우)
        - 사용자가 파이프라인을 실행하면, 작업을 수행할 컨테이너(Worker pod?)를 자동으로 생성해서 연산을 수행하고, 작업이 종료되면 즉시 폐기
    
    - 개념 이해: “쿠버네티스 클러스터”라는, 여러대의 서버(예, 5대)를 묶어 거대한 컴퓨터 환경을 만듦
        
        - 쿠버네티스는 현재 가용한, 컴퓨터들 내부의 노드(node)에 컨테이너를 생성하고 할당, 작업이 종료되면 회수
        - microk8s 등의 경우는, 단일 노드를 이용해 이러한 클러스터 구성 가능
    - 🔧 구체적으로
        
        ### 1. 사용자에 Pod 할당 (Interactive Environment)
        
        > 이해하신 내용: "원격 접속 시 컨테이너 내부에서 코드를 작성"
        
        - **기술적 용어**: **Kubeflow Notebook Server**
        - **작동 메커니즘**:
            - 사용자가 Kubeflow UI에서 Notebook을 생성하면, K8s는 **StatefulSet**(혹은 Deployment) 리소스를 통해 **장기 실행(Long-running) Pod**를 생성합니다.
            - 이 Pod는 **Service** 객체를 통해 외부 네트워크(웹 브라우저)와 연결됩니다.
            - **중요**: 코드를 저장해야 하므로, 이 Pod는 **PVC(Persistent Volume Claim)**라는 영구 저장소와 연결되어, 컨테이너가 재시작되어도 작성한 코드가 날아가지 않게 구성됩니다.
        
        ### 2. 애플리케이션 Pipeline에 Pod 할당 (Batch Execution)
        
        > 이해하신 내용: "파이프라인 실행 시 Worker Pod 생성 -> 연산 -> 즉시 폐기"
        
        - **기술적 용어**: **Ephemeral Pods (임시/일회성 파드)**
        - **작동 메커니즘**:
            - KFP(Kubeflow Pipelines)는 내부적으로 **Argo Workflow**라는 엔진을 사용합니다.
            - 파이프라인의 각 단계(Step)는 **Task**라고 부르며, 각 Task는 독립된 **Pod**로 스케줄링됩니다.
            - 이 Pod들은 작업을 마치면 상태 코드(Exit Code 0)를 반환하고 **Completed** 상태가 되며, 리소스를 반환하고 사라집니다. 이를 **배치 처리(Batch Processing)** 방식이라고 합니다.
        
        ### 3. 클러스터와 MicroK8s (Resource Abstraction)
        
        > 이해하신 내용: "서버들을 묶어 거대한 컴퓨터로 만듦 / 가용한 노드에 할당 / MicroK8s는 단일 노드 클러스터"
        
        - **기술적 용어**: **Orchestration & Scheduling**
        - **작동 메커니즘**:
            - **추상화(Abstraction)**: K8s는 물리 서버가 1대든 100대든, 사용자에게는 하나의 **API Endpoint**로 보여줍니다. 사용자는 "어느 서버"에 띄울지 고민할 필요가 없습니다.
            - **스케줄링(Scheduling)**: K8s의 **Scheduler** 컴포넌트가 각 노드(Node)의 리소스 상태(CPU/Memory 여유분)를 판단하여 Pod를 최적의 위치에 배치합니다.
            - **MicroK8s**: 물리적으로는 1대의 머신이지만, 논리적으로 **Control Plane(관리자)**과 **Worker Node(일꾼)** 역할을 동시에 수행하도록 구성된 형태입니다.
- 비교
    
    - 기술적인 차이점은, 불필요한 기능 제거
        
        |**구분**|**Standard Kubernetes (Vanilla)**|**Lightweight Kubernetes (MicroK8s, K3s, Minikube)**|
        |---|---|---|
        |**아키텍처**|**분산 시스템 지향**. Control Plane(Master)과 Worker Node가 물리적/논리적으로 분리되어 대규모 클러스터링에 최적화됨.|**단일 노드 지향**. 모든 구성 요소(Control Plane, Worker, Network Plugin 등)를 하나의 프로세스나 바이너리, 혹은 VM 안에 패키징함.|
        |**데이터 스토어**|**etcd** 필수 사용. (분산 코디네이션을 위한 고가용성 Key-Value 저장소로, 리소스 소모가 큼)|**SQLite** 등을 주로 사용 (K3s). 혹은 etcd를 내장하되 설정 최적화. (단일 노드에서는 etcd의 합의 알고리즘 오버헤드를 줄임)|
        |**구성 요소**|클라우드 제공자(AWS, GCP 등)를 위한 방대한 드라이버 및 레거시 코드가 모두 포함됨.|불필요한 클라우드 드라이버 제거, 필수 기능만 남김. (Docker Image 사이즈 및 메모리 점유율 최소화)|
        |**배포 난이도**|kubeadm 등을 사용해 네트워크(CNI), 스토리지(CSI), 인증서 등을 일일이 설정해야 함.|snap install이나 단일 바이너리 실행으로 설치 완료. Network, Storage Class 등이 사전에 구성되어 있음.|
        |**목적**|대규모 트래픽 처리, 고가용성(HA) 프로덕션 환경|개발자 로컬 테스트, Edge Computing, IoT, CI/CD 파이프라인|
        
    - ⭐ MicroK8S와 연계가 잘되어있다고 함.
        
        - K3s의 경우는 IoT 및 Edge 디바이스를 위한 경량화 배포판임
        - Kubeflow는 최소 30~50개의 Pod로 구성
            - Istio (네트워크 게이트웨이)
            - Dex (인증)
            - Pipeline API 서버
            - MySQL, MiniIO (메타데이터 및 스토리지)
            - 주피터 노트북 컨트롤러 등
    - (🔧 검증 필요) 근데, MicroK8S를 쓴다고 해서 Istio, Dex, MySQL, Pipeline API 서버 등, 복잡한 것도 생략되는지?
        
        - No, 필수적이므로 생략하여 사용할 수는 없음. 대신 사용하기는 편함
        - 기존 쿠버네티스
            - 사용자가 직접 Istiio, Dex, Cert-Manager 버전을 Kubeflow 버전과 맞춰줘야 하며, YAML파일을 다운받고 설정(ConfigMap)을 수정해야함
        - MicroK8s
            - (⭐조사해볼것) Add-on 이라는 개념을 통해, 상호 호환성 검증이 끝난 버전을 패키지로 묶어둠
            - 터미널에 microk8s enable kubeflow 명령어를 입력하면, 스크립트가 알아서 순서에 맞춰 Istio, Dex, MySQL 등을 차례대로 설치하고 연결함

### 🔧 각 요소 Istio와 MiniIO, MySQL, Pipeline API Server들이 kubeflow에서 무슨 역할을 하는지?

- Istio: 외부에서 kubeflow 클러스터(그리고 kubeflow 대시보드 UI)로 접속할 때의 진입점
    
    - Dex와 연동하여 인증(Authentification)을 강제함. 사용자 접근을 허용/차단 등 서비스간 통신에 대한 보안 정책을 적용
- MiniIO
    
    - 데이터셋, 데이터파일, 학습된 모델파일, 파이프라인 yaml파일 등을 저장
    - (그럼 MinIO는 로컬 AWS S3 환경이라 볼 수 있고, 그대로 옮기기 편하려고 사용하는거지? 그냥 온프레미스 환경에서도 사용하다가 나중에 이관해도 되는지?)
        - 맞음, 로컬 환경에서 실행되는 개인용 AWS S3 환경이라고 봐도 됨, AWS S3 API와 호환되도록 설계된 오브젝트 스토리지임 (오브젝트 스토리지가 뭘까..)
- MySQL : 메타데이터 저장용 레이어
    
    - kubeflow의 각 컴포넌트들이 필요로 하는 구조화된 메타데이터를 저장하는 DBMS임
    - 파이프라인의 메타데이터: DAG 토폴로지, 버전정보, 설명 등
    - Run History: 파이프라인의 상태 (Running, Succeeded, Failed), 시작/종료 시간, 입력 파라미터 등
    - Katib 관련 데이터 (하이퍼 파라미터 자동 튜닝 도구): Katib를 사용할 경우, 하이퍼파라미터 튜닝 결과 저장
    - MinIO와의 차이: MinIO가 실제 파일(Data)을 저장한다면, MySQL은 그 파일이 어디에 있는지, 그리고 파일에 대한 정보(메타데이터)를 저장
- Pipeline API Server (Control Plane / Orchestration Interface)
    
    KFP(Kubeflow Pipelines)의 핵심 백엔드 서비스로, 파이프라인의 컨트롤 타워 역할을 합니다.
    
    - **API Endpoint**: Python SDK(kfp.Client())나 웹 UI로부터 요청을 받아들이는 REST API 서버입니다.
    - **Compilation & Parsing**: 전달받은 파이프라인 패키지(YAML)를 파싱하고 유효성을 검사합니다.
    - **Resource Translation**: 추상화된 파이프라인 정의를 쿠버네티스가 이해할 수 있는 리소스(구체적으로는 **Argo Workflow** Custom Resource)로 변환합니다.
    - **Execution Management**: 변환된 워크플로우를 쿠버네티스 스케줄러에 제출하여 실행시키고, 각 단계의 상태를 모니터링하여 MySQL에 업데이트합니다.



참고 블로그
- 쿠버네티스: https://brunch.co.kr/@wikibook/85