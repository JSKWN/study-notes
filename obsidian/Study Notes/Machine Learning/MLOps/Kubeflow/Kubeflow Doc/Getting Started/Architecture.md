Kubeflow 프로젝트들과 각 프로젝트가 AI 생명주기의 어느 단계에 해당하는지를 소개

- [[#1. Kubeflow 생태계|1. Kubeflow 생태계]]
- [[#2. AI 생명주기소개|2. AI 생명주기소개]]
- [[#3. 프로덕션 및 개발 단계를 위한 AI 생명주기|3. 프로덕션 및 개발 단계를 위한 AI 생명주기]]
- [[#4. AI 생명주기 내의 Kubeflow 프로젝트|4. AI 생명주기 내의 Kubeflow 프로젝트]]
- [[#5. Kubeflow 인터페이스 (Kubeflow Interfaces)|5. Kubeflow 인터페이스 (Kubeflow Interfaces)]]
	- [[#5. Kubeflow 인터페이스 (Kubeflow Interfaces)#Kubeflow 대시보드 (Kubeflow Dashboard)|Kubeflow 대시보드 (Kubeflow Dashboard)]]
	- [[#5. Kubeflow 인터페이스 (Kubeflow Interfaces)#Kubeflow API 및 SDK (Kubeflow APIs and SDKs)|Kubeflow API 및 SDK (Kubeflow APIs and SDKs)]]

### 1. Kubeflow 생태계
(Kubeflow Ecosystem)

다음 다이어그램은 Kubeflow 생태계의 개요와 이러한 생태계가 쿠버네티스 및 AI 환경과 어떻게 연관되는지를 보여줍니다. Kubeflow는 AI 플랫폼을 배포, 확장 및 관리하기 위한 시스템으로서 쿠버네티스 위에 구축되었습니다.

![[obsidian/Attachments/Pasted image 20251204160009.png]]

### 2. AI 생명주기소개
(Introducing the AI Lifecycle)

AI 애플리케이션을 개발하고 배포할 때, AI 생명주기는 일반적으로 여러 단계로 구성됩니다. AI 시스템 개발은 이러한 단계를 반복적으로 거쳐가게 됩니다. 모델이 필요한 결과를 지속적으로 도출할 수 있도록 하려면, AI 생명주기의 다양한 단계에서 나오는 결과물을 평가하고 필요에 따라 모델과 파라미터에 변경 사항을 적용해야 합니다.

다음 다이어그램은 AI 생명주기 단계를 순서대로 보여줍니다:

![[obsidian/Attachments/Pasted image 20251204160643.png]]

각 단계(초록색)를 더 자세히 살펴보면:

1. **데이터 준비(Data Preparation)** 단계에서는 <u>원본 데이터를 수집(ingest)하고, 특성(feature) 엔지니어링(feature engineering)을 수행</u>하여 오프라인 피처 스토어에 저장할 ML 피처를 추출하며, 모델 개발을 위한 <u>학습 데이터를 준비</u>합니다. 일반적으로 이 단계는 Spark, Dask, Flink 또는 Ray와 같은 데이터 처리 도구와 연관되어 있습니다.
	- 특성 엔지니어링이란?: 원시 데이터를 ML 모델이 읽을 수 있는 형식으로 전처리하며, 관련 특성을 선택 및 변환하여 모델 성능을 최적화하는것.
		- 전처리(결측값 처리, 이상치 제거, 데이터 정규화) - 피처 스케일링 - 피처 생성(변형 및 조합) - 차원 축소
    
2. **모델 개발(Model Development)** 단계에서는 ML 프레임워크를 선택하고, <u>모델 아키텍처를 개발</u>하며, BERT나 Llama와 같이 파인 튜닝(fine-tuning)을 할 수 있는 기존의 사전 학습된(pre-trained) 모델들을 탐색합니다.
    
3. **모델 학습(Model Training)** 단계에서는 대규모 컴퓨팅 환경에서 모델을 학습시키거나 파인 튜닝합니다. 단일 GPU가 모델 크기를 감당할 수 없는 경우 분산 학습(distributed training)을 사용해야 합니다. 모델 학습의 결과물은 학습된 <u>모델 아티팩트(artifact)</u>이며, 이를 <u>Model Registry에 저장</u>할 수 있습니다.
    
4. **모델 최적화(Model Optimization)** 단계에서는 모델 하이퍼파라미터를 최적화하고, 신경망 아키텍처 탐색(NAS)이나 모델 압축(model compression)과 같은 다양한 AutoML 알고리즘을 사용하여 모델을 최적화합니다. 모델 최적화 과정 중에 ML 메타데이터를 Model Registry에 저장할 수 있습니다.
    
5. **모델 서빙(Model Serving)** 단계에서는 <u>온라인 또는 배치 추론(inference)</u>을 위해 모델 아티팩트를 서빙합니다. 사용 사례에 따라 모델은 예측형(predictive) 또는 생성형(generative) AI 작업을 수행할 수 있습니다. 모델 서빙 단계에서는 온라인 피처 스토어를 사용하여 피처를 추출할 수도 있습니다. 모델 성능을 모니터링하고, 그 결과를 AI 생명주기의 이전 단계로 피드백합니다.
    

### 3. 프로덕션 및 개발 단계를 위한 AI 생명주기
(AI Lifecycle for Production and Development Phases)

AI 애플리케이션의 AI 생명주기는 개념적으로 <u>개발(development) 단계</u>와 <u>프로덕션(production) 단계</u>로 나눌 수 있으며, 이 다이어그램은 각 단계에 어떤 과정이 포함되는지 보여줍니다:

![[obsidian/Attachments/Pasted image 20251204161836.png]]

### 4. AI 생명주기 내의 Kubeflow 프로젝트
(Kubeflow Projects in the AI Lifecycle)

다음 다이어그램은 Kubeflow 프로젝트들이 AI 생명주기의 각 단계에 어떻게 부합하는지 보여줍니다:

![[obsidian/Attachments/Pasted image 20251204161922.png]]

각 Kubeflow 프로젝트에 대한 자세한 정보는 다음 항목을 참조해주세요:

- **Kubeflow Spark Operator**는 <u>데이터 준비 및 피처 엔지니어링</u> 단계에 사용할 수 있습니다.
- **Kubeflow Notebooks**는 <u>모델 개발</u> 및 대화형 데이터 과학을 통해 <u>AI 워크플로우를 실험</u>하는 데 사용할 수 있습니다.
- **Kubeflow Trainer**는 <u>대규모 분산 학습이나 LLM 파인 튜닝</u>에 사용할 수 있습니다.
- **Kubeflow Katib**은 다양한 AutoML 알고리즘을 사용한 <u>모델 최적화 및 하이퍼파라미터 튜닝</u>에 사용할 수 있습니다.
- **Kubeflow Model Registry**는 <u>ML 메타데이터와 모델 아티팩트를 저장</u>하고, 프로덕션 서빙을 위해 모델을 준비하는 데 사용할 수 있습니다.
- **KServe**는 <u>모델 서빙 단계에서 온라인 및 배치 추론</u>에 사용할 수 있습니다.
- **Feast**는 피처 스토어로서 오프라인 및 온라인 피처를 관리하는 데 사용할 수 있습니다.
- **Kubeflow Pipelines**는 <u>AI 생명주기의 각 단계를 구축, 배포 및 관리</u>하는 데 사용할 수 있습니다.
    

AI 플랫폼 팀은 각 프로젝트를 독립적으로 사용하거나 전체 AI 레퍼런스 플랫폼을 배포하여 특정 요구 사항에 맞게 Kubeflow를 기반으로 시스템을 구축할 수 있습니다.

### 5. Kubeflow 인터페이스 (Kubeflow Interfaces)

이 섹션에서는 Kubeflow 프로젝트와 상호 작용하는 데 사용할 수 있는 인터페이스를 소개합니다.

#### Kubeflow 대시보드 (Kubeflow Dashboard)

Kubeflow Central Dashboard(중앙 대시보드)의 모습은 다음과 같습니다:

![[obsidian/Attachments/Pasted image 20251204161935.png]]

Kubeflow AI 레퍼런스 플랫폼에는 클러스터에서 실행 중인 컴포넌트들의 UI를 노출하여 AI 플랫폼 및 도구들의 허브 역할을 하는 **Kubeflow Central Dashboard**가 포함되어 있습니다.

#### Kubeflow API 및 SDK (Kubeflow APIs and SDKs)

다양한 Kubeflow 프로젝트들이 API와 Python SDK를 제공합니다.

다음 레퍼런스 문서들을 참조하세요:

- Kubeflow Pipelines 도메인 특화 언어(DSL)를 포함한 Kubeflow Pipelines API 및 SDK에 대한 **Pipelines 레퍼런스 문서**.
    
- Kubeflow Trainer API와 상호 작용하고 TrainJob을 관리하기 위한 **Kubeflow Python SDK**.
    
- Python API를 사용하여 Katib 하이퍼파라미터 튜닝 실험(Experiments)을 관리하기 위한 **Katib Python SDK**.