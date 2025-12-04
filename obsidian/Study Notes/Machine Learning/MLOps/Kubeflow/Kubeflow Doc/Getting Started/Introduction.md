
- [[#Kubeflow 소개|Kubeflow 소개]]
- [[#Kubeflow란 무엇인가요? (What is Kubeflow)|Kubeflow란 무엇인가요? (What is Kubeflow)]]
- [[#Kubeflow 프로젝트란 무엇인가요? (What are Kubeflow Projects)|Kubeflow 프로젝트란 무엇인가요? (What are Kubeflow Projects)]]
- [[#Kubeflow AI 레퍼런스 플랫폼이란 무엇인가요? (What is the Kubeflow AI Reference Platform)|Kubeflow AI 레퍼런스 플랫폼이란 무엇인가요? (What is the Kubeflow AI Reference Platform)]]
- [[#Kubeflow 개요 다이어그램 (Kubeflow Overview Diagram)|Kubeflow 개요 다이어그램 (Kubeflow Overview Diagram)]]
- [[#Kubeflow 영상 소개 (Kubeflow Video Introduction)|Kubeflow 영상 소개 (Kubeflow Video Introduction)]]
- [[#Kubeflow의 미션 (The Kubeflow Mission)|Kubeflow의 미션 (The Kubeflow Mission)]]
- [[#역사 (History)|역사 (History)]]

### 1. Kubeflow란 무엇인가요? (What is Kubeflow)

Kubeflow는 쿠버네티스(Kubernetes) 상에서 AI 플랫폼을 구축하기 위한 도구들의 기반(foundation)입니다.

- AI 플랫폼 팀은 각 프로젝트를 독립적으로 사용하거나, 전체 <u>AI 레퍼런스 플랫폼(AI reference platform)</u> 을 배포하여 특정 요구 사항에 맞게 Kubeflow를 기반으로 시스템을 구축할 수 있습니다. 
- Kubeflow <u>AI 레퍼런스 플랫폼</u>은 조합 가능하고(composable), 모듈식이며(modular), 이식성이 뛰어나고(portable), 확장 가능(scalable)하며, AI 라이프사이클의 모든 단계를 포괄하는 <u>쿠버네티스 네이티브 프로젝트 생태계의 지원</u>을 받습니다.

### 2. Kubeflow 프로젝트란 무엇인가요? (What are Kubeflow Projects)

- Kubeflow는 AI 라이프사이클의 서로 다른 측면을 다루는 여러 오픈 소스 프로젝트들로 구성되어 있습니다. 이러한 프로젝트들은 독립적으로 사용하거나 Kubeflow AI 레퍼런스 플랫폼의 일부로서 함께 사용할 수 있도록 설계되었습니다. 
- 이는 전체 엔드투엔드(end-to-end) AI 플랫폼 기능이 필요하지 않은 사용자가 모델 학습(model training)이나 모델 서빙(model serving)과 같은 특정 기능만 활용하고자 할 때 유연성을 제공합니다.

|                         |                         |                               |
| ----------------------- | ----------------------- | ----------------------------- |
| 쿠브플로우 프로젝트 이름           | 모듈 이름(소스 코드)            | 핵심 역할 (Function)              |
| Kubeflow KServe         | kserve/kserve           | 모델 서빙 (Model Serving)         |
| Kubeflow Katib          | kubeflow/katib          | 자동화된 머신러닝 (AutoML) 및 튜닝       |
| Kubeflow Model Registry | kubeflow/model-registry | 모델 버전 및 메타데이터 관리              |
| Kubeflow Notebooks      | kubeflow/notebooks      | 대화형 개발 환경 (Jupyter Notebooks) |
| Kubeflow Pipelines      | kubeflow/pipelines      | ML 워크플로우 오케스트레이션              |
| Kubeflow Spark Operator | kubeflow/spark-operator | 데이터 처리 (Spark 작업 관리)          |
| Kubeflow Trainer        | kubeflow/trainer        | 분산 학습 (Distributed Training)  |
- Kserve (모델 서빙): 학습 완료된 ML 모델을 배포하여 실제 추론(Inference) 서비스를 제공하는 도구
- Katib (하이퍼파라미터 최적화): 모델 성능 개선을 위한 하이퍼파라미터 자동 최적화 도구
- Model Registry (모델 버전 관리): 모델 파일, 버전 관리, 모델의 메타데이터 등을 관리
- Notebook (주피터노트북 환경): 주피터노트북 환경을 쿠버네티스 클러스터 위에서 생성하고 관리할 수 있도록 하는 도구
- 
- 



### Kubeflow AI 레퍼런스 플랫폼이란 무엇인가요? (What is the Kubeflow AI Reference Platform)

Kubeflow AI 레퍼런스 플랫폼은 Kubeflow의 모든 프로젝트 제품군과 추가적인 통합 및 관리 도구들을 하나로 묶은 것을 의미합니다. Kubeflow AI 레퍼런스 플랫폼은 전체 AI 라이프사이클을 위한 포괄적인 툴킷을 배포합니다. 이 플랫폼은 '패키지 배포판(Packaged Distributions)' 또는 'Kubeflow 매니페스트(Kubeflow Manifests)'를 통해 설치할 수 있습니다.

|   |   |
|---|---|
|Kubeflow AI 레퍼런스 플랫폼 도구|소스 코드|
|**Central Dashboard**|kubeflow/dashboard|
|**Profile Controller**|kubeflow/dashboard|
|**Kubeflow Manifests**|kubeflow/manifests|

### Kubeflow 개요 다이어그램 (Kubeflow Overview Diagram)

다음 다이어그램은 쿠버네티스 상에서 AI 라이프사이클의 각 단계를 담당하는 Kubeflow 프로젝트들을 보여줍니다. Kubeflow 프로젝트들이 AI 라이프사이클에 어떻게 부합하는지 알아보려면 아키텍처 개요를 읽어보세요.

(Kubeflow 개요 다이어그램 이미지 위치)

### Kubeflow 영상 소개 (Kubeflow Video Introduction)

Kubeflow에 대한 소개를 제공하는 다음 영상을 시청하세요.

(영상 위치)

### Kubeflow의 미션 (The Kubeflow Mission)

우리의 목표는 쿠버네티스가 가장 잘하는 기능을 활용하여, AI 모델을 확장하고 프로덕션 환경에 배포하는 것을 최대한 간단하게 만드는 것입니다:

- **다양한 인프라에서의 쉽고, 반복 가능하며, 이식성 있는 배포** (예: 노트북에서 실험 후 온프레미스 클러스터나 클라우드로 이동).
    
- **느슨하게 결합된(loosely-coupled) 마이크로서비스의 배포 및 관리.**
    
- **수요에 따른 확장.**
    

AI 실무자들은 매우 다양한 도구를 사용하기 때문에, 핵심 목표 중 하나는 사용자 요구 사항에 따라 (합리적인 범위 내에서) 스택을 커스터마이징하고 시스템이 "지루한 작업"을 대신 처리하도록 하는 것입니다. 우리는 좁은 범위의 기술 세트로 시작했지만, 추가적인 도구를 포함하기 위해 다양한 프로젝트들과 협력하고 있습니다.

궁극적으로 우리는 쿠버네티스가 실행 중인 곳이라면 어디서나 사용하기 쉬운 AI 스택을 제공하고, 배포되는 클러스터에 맞춰 스스로 구성(self configure)될 수 있는 간단한 매니페스트 세트를 갖추고자 합니다.

### 역사 (History)

Kubeflow는 구글이 내부적으로 TensorFlow를 실행하던 방식(TensorFlow Extended라는 파이프라인 기반)을 오픈 소스화하면서 시작되었습니다. 처음에는 쿠버네티스에서 TensorFlow 작업을 더 간단하게 실행하는 방법으로 시작했지만, 이후 쿠버네티스에서 AI 워크로드를 실행하기 위한 도구들의 기반으로 확장되었습니다.

Kubeflow 로고는 쿠버네티스 로고의 7각형 안에 있는 문자 K와 F를 나타내며, 이는 **쿠버네티스(클라우드 네이티브)**와 **플로우(머신 러닝)**라는 두 커뮤니티를 의미합니다. 이 문맥에서 '플로우(flow)'는 TensorFlow뿐만 아니라, 모델/알고리즘 구현의 표준 형태로 데이터플로우 그래프(Dataflow Graph)를 사용하는 모든 ML 프레임워크를 지칭합니다.