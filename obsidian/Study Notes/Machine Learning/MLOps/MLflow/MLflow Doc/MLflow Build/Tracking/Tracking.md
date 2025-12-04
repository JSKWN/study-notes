<br>

# 요약
---

>MLflow Tracking은 ML 실험의 실행 과정을 기록 및 시각화 하는 기능을 담당
>1. MLflow Tracking: MLflow가 적용된 코드 실행 시 파라미터, 코드 버전, 평가 지표, 아티팩트(=산출물, 모델 및 플롯 등)을 로깅하고 시각화하는 API 및 UI 도구
>2. 주요 요소:
	>	- Runs(실행): 단일 실행 단위이며 메타데이터 및 아티팩트(산출물)를 기록
	>	- Experiments(실험): Run 및 모델들의 그룹
>3. 로깅 방법
	>	- 수동 로깅: `mlflow.log_param()`, `mlflow.log_metric()`등을 사용해 원하는 대상을 기록
	>	- 자동 로깅: `mlflow.autolog()`함수를 이용해 주요 라이브러리(Tensorflow, PyTorch 등)의 파라미터 및 메트릭을 자동 기록
>4. MLflow 3에서 변경된 기능
	>	- 모델 체크포인트 및 추적 강화: 단일 Run 내에서 여러 체크포인트를 저장하고 각 체크포인트의 메트릭을 추적
	>	- 프로그래밍 방식 모델 검색: mlflow.search_logged_models()를 통해 SQL 유사 문법으로 모델 검색 및 필터링 가능
	>	- 모델 ID 기반 uri: 기존 Run ID 기반(runs:/)이었으나, 고유 모델 ID 기반(models:/<model_id>)의 uri를 사용하여 모델에 대한 관리가 용이
>5.  구성 요소 및 환경 설정:
	>	- backend store: 메타데이터(파라미터, 메트릭 등) 저장
	>	- artifact store: 대용량 파일(모델 가중치 및 이미지) 저장
	>	- tracking server: 팀 협업을 위한 원격 서버 구성

<br>

# 공식 문서 내용
---
- [MLflow Tracking](#MLflow%20Tracking)
- [개념 (Concepts)](#개념%20(Concepts))
	- [](#개념%20(Concepts)#개념%20(Concepts)#Runs%20(런)|Runs%20(런))
	- [](#개념%20(Concepts)#개념%20(Concepts)#Models%20(모델)|Models%20(모델))
	- [](#개념%20(Concepts)#개념%20(Concepts)#Experiments%20(실험)|Experiments%20(실험))
- [런 추적 (Tracking Runs)](#런%20추적%20(Tracking%20Runs))
- [프로그래밍 방식으로 기록된 모델 검색 (Searching Logged Models Programmatically)](#프로그래밍%20방식으로%20기록된%20모델%20검색%20(Searching%20Logged%20Models%20Programmatically))
- [프로그래밍 방식으로 런 조회 (Querying Runs Programmatically)](#프로그래밍%20방식으로%20런%20조회%20(Querying%20Runs%20Programmatically))
- [모델 추적 (Tracking Models)](#모델%20추적%20(Tracking%20Models))
	- [](#모델%20추적%20(Tracking%20Models)#모델%20추적%20(Tracking%20Models)#모델%20체크포인트%20기록%20(Logging%20Model%20Checkpoints)|모델%20체크포인트%20기록%20(Logging%20Model%20Checkpoints))
	- [](#모델%20추적%20(Tracking%20Models)#모델%20추적%20(Tracking%20Models)#지표를%20모델%20및%20데이터셋에%20연결%20(Linking%20Metrics%20to%20Models%20and%20Datasets)|지표를%20모델%20및%20데이터셋에%20연결%20(Linking%20Metrics%20to%20Models%20and%20Datasets))
	- [](#모델%20추적%20(Tracking%20Models)#모델%20추적%20(Tracking%20Models)#모델%20체크포인트%20검색%20및%20순위%20매기기%20(Searching%20and%20Ranking%20Model%20Checkpoints)|모델%20체크포인트%20검색%20및%20순위%20매기기%20(Searching%20and%20Ranking%20Model%20Checkpoints))
	- [](#모델%20추적%20(Tracking%20Models)#모델%20추적%20(Tracking%20Models)#MLflow%203의%20모델%20URI%20(Model%20URIs%20in%20MLflow%203)|MLflow%203의%20모델%20URI%20(Model%20URIs%20in%20MLflow%203))
- [데이터셋 추적 (Tracking Datasets)](#데이터셋%20추적%20(Tracking%20Datasets))
- [런, 모델 및 결과 탐색 (Explore Runs, Models, and Results)](#런,%20모델%20및%20결과%20탐색%20(Explore%20Runs,%20Models,%20and%20Results))
	- [](#런,%20모델%20및%20결과%20탐색%20(Explore%20Runs,%20Models,%20and%20Results)#런,%20모델%20및%20결과%20탐색%20(Explore%20Runs,%20Models,%20and%20Results)#Tracking%20UI|Tracking%20UI)
- [MLflow Tracking 환경 설정 (Set up the MLflow Tracking Environment)](#MLflow%20Tracking%20환경%20설정%20(Set%20up%20the%20MLflow%20Tracking%20Environment))
	- [](#MLflow%20Tracking%20환경%20설정%20(Set%20up%20the%20MLflow%20Tracking%20Environment)#MLflow%20Tracking%20환경%20설정%20(Set%20up%20the%20MLflow%20Tracking%20Environment)#구성%20요소%20(Components)|구성%20요소%20(Components))
		- [](#구성%20요소%20(Components)#구성%20요소%20(Components)#MLflow%20Tracking%20APIs|MLflow%20Tracking%20APIs)
		- [](#구성%20요소%20(Components)#구성%20요소%20(Components)#Backend%20Store%20(백엔드%20저장소)|Backend%20Store%20(백엔드%20저장소))
		- [](#구성%20요소%20(Components)#구성%20요소%20(Components)#Artifact%20Store%20(아티팩트%20저장소)|Artifact%20Store%20(아티팩트%20저장소))
		- [](#구성%20요소%20(Components)#구성%20요소%20(Components)#MLflow%20Tracking%20Server%20(선택%20사항)|MLflow%20Tracking%20Server%20(선택%20사항))
	- [](#MLflow%20Tracking%20환경%20설정%20(Set%20up%20the%20MLflow%20Tracking%20Environment)#MLflow%20Tracking%20환경%20설정%20(Set%20up%20the%20MLflow%20Tracking%20Environment)#일반적인%20설정%20(Common%20Setups)|일반적인%20설정%20(Common%20Setups))
- [자주 묻는 질문 (FAQ)](#자주%20묻는%20질문%20(FAQ))

## MLflow Tracking

MLflow Tracking은 머신러닝 코드를 실행할 때 파라미터, 코드 버전, 지표(metrics), 출력 파일을 기록하고 추후 결과를 시각화하기 위한 API 및 UI입니다. MLflow Tracking은 Python, REST, R, Java API를 제공합니다.

![Pasted image 20251203092522](obsidian/Attachments/Pasted%20image%2020251203092522.png)

## 개념 (Concepts)

### Runs (런)

MLflow Tracking은 **런(run)** 개념을 중심으로 구성됩니다. 런은 데이터 과학 코드의 실행 단위(예: train.py 파이썬 스크립트의 단일 실행)입니다. 각 런은 **메타데이터**(지표, 파라미터, 시작 및 종료 시간 등 런에 대한 다양한 정보)와 **아티팩트**(모델 가중치, 이미지 등 런에서 생성된 출력 파일)를 기록합니다.

### Models (모델)

모델은 런 실행 중에 생성된 학습된 머신러닝 아티팩트를 나타냅니다. 기록된 모델(Logged Models)은 런과 유사하게 고유한 메타데이터와 아티팩트를 포함합니다.

### Experiments (실험)

실험은 특정 작업을 위해 런과 모델을 그룹화한 것입니다. CLI, API, 또는 UI를 사용하여 실험을 생성할 수 있습니다. MLflow API와 UI를 통해 실험을 생성하고 검색할 수도 있습니다. 런을 실험으로 구성하는 방법에 대한 자세한 내용은 '실험으로 런 정리하기(Organizing Runs into Experiments)'를 참조하세요.

## 런 추적 (Tracking Runs)

MLflow Tracking API는 런을 추적하기 위한 일련의 함수를 제공합니다. 예를 들어 mlflow.start_run()을 호출하여 새 런을 시작하고, mlflow.log_param() 및 mlflow.log_metric()과 같은 로깅 함수를 호출하여 각각 파라미터와 지표를 기록할 수 있습니다. API 사용에 대한 자세한 내용은 [Traking API](obsidian/Study%20Notes/Machine%20Learning/MLOps/MLflow/MLflow%20Doc/MLflow%20Build/Traking%20API.md)를 참조하세요.

codePython

```
import mlflow

with mlflow.start_run():
    mlflow.log_param("lr", 0.001)
    # 머신러닝 코드
    ...
    mlflow.log_metric("val_loss", val_loss)
```

대안으로, **자동 로깅(Auto-logging)**은 MLflow 추적을 시작하기 위한 초고속 설정을 제공합니다. 이 강력한 기능을 사용하면 명시적인 로그 구문 없이도 지표, 파라미터, 모델을 기록할 수 있습니다. 훈련 코드 실행 전에 mlflow.autolog()만 호출하면 됩니다. 자동 로깅은 Scikit-learn, XGBoost, PyTorch, Keras, Spark 등 인기 있는 라이브러리를 지원합니다. 지원되는 라이브러리 및 각 라이브러리에서 자동 로깅 API를 사용하는 방법은 '자동 로깅 문서(Automatic Logging Documentation)'를 참조하세요.

codePython

```
import mlflow

mlflow.autolog()

# 훈련 코드...
```

> **참고 (Note)**  
> 별도의 서버/데이터베이스 구성이 없는 기본 상태에서 MLflow Tracking은 데이터를 로컬의 mlruns 디렉터리에 기록합니다. 팀과 결과를 공유하기 위해 원격 데이터베이스나 클라우드 스토리지 같은 다른 위치에 런을 기록하고 싶다면, 'MLflow Tracking 환경 설정' 섹션의 지침을 따르세요.

## 프로그래밍 방식으로 기록된 모델 검색 (Searching Logged Models Programmatically)

MLflow 3는 mlflow.search_logged_models()를 통해 강력한 모델 검색 기능을 도입했습니다. 이 API를 사용하면 SQL과 유사한 구문을 사용하여 성능 지표, 파라미터, 모델 속성을 기반으로 실험 전반에 걸쳐 특정 모델을 찾을 수 있습니다.

codePython

```
import mlflow

# 실험 전반에 걸쳐 고성능 모델 찾기
top_models = mlflow.search_logged_models(
    experiment_ids=["1", "2"],
    filter_string="metrics.accuracy > 0.95 AND params.model_type = 'RandomForest'",
    order_by=[{"field_name": "metrics.f1_score", "ascending": False}],
    max_results=5,
)

# 배포를 위한 최적 모델 가져오기
best_model = mlflow.search_logged_models(
    experiment_ids=["1"],
    filter_string="metrics.accuracy > 0.9",
    max_results=1,
    order_by=[{"field_name": "metrics.accuracy", "ascending": False}],
    output_format="list",
)[0]

# 최적 모델 직접 로드하기
loaded_model = mlflow.pyfunc.load_model(f"models:/{best_model.model_id}")
```

**주요 기능:**

- **SQL 유사 필터링**: metrics., params., 속성 접두사를 사용하여 복잡한 쿼리 작성.
    
- **데이터셋 인식 검색**: 공정한 모델 비교를 위해 특정 데이터셋을 기반으로 지표 필터링.
    
- **유연한 정렬**: 여러 기준을 사용하여 정렬하여 최적의 모델 찾기.
    
- **직접 모델 로드**: 새로운 models:/<model_id> URI 형식을 사용하여 모델에 즉시 접근.
    

포괄적인 예제와 고급 검색 패턴은 '기록된 모델 검색 가이드(Search Logged Models Guide)'를 참조하세요.

## 프로그래밍 방식으로 런 조회 (Querying Runs Programmatically)

MlflowClient를 사용하여 Tracking UI의 모든 기능에 프로그래밍 방식으로 접근할 수도 있습니다.

예를 들어, 다음 코드 조각은 실험의 모든 런 중에서 검증 손실(validation loss)이 가장 좋은 런을 검색합니다.

codePython

```
client = mlflow.tracking.MlflowClient()
experiment_id = "0"
best_run = client.search_runs(
    experiment_id, order_by=["metrics.val_loss ASC"], max_results=1
)[0]
print(best_run.info)
# {'run_id': '...', 'metrics': {'val_loss': 0.123}, ...}
```

## 모델 추적 (Tracking Models)

MLflow 3는 향상된 모델 추적 기능을 도입하여 단일 런 내에서 여러 모델 체크포인트를 기록하고 서로 다른 데이터셋에 대한 성능을 추적할 수 있게 합니다. 이는 다양한 훈련 단계에서 모델 체크포인트를 저장하고 비교하려는 딥러닝 워크플로우에 특히 유용합니다.

### 모델 체크포인트 기록 (Logging Model Checkpoints)

모델 로깅 함수의 step 파라미터를 사용하여 훈련 중 여러 단계에서 모델 체크포인트를 기록할 수 있습니다. 기록된 각 모델은 나중에 참조할 수 있는 고유한 모델 ID를 받습니다.

codePython

```
import mlflow
import mlflow.pytorch

with mlflow.start_run() as run:
    for epoch in range(100):
        # 모델 훈련
        train_model(model, epoch)

        # 10 에포크마다 모델 체크포인트 기록
        if epoch % 10 == 0:
            model_info = mlflow.pytorch.log_model(
                pytorch_model=model,
                name=f"checkpoint-epoch-{epoch}",
                step=epoch,
                input_example=sample_input,
            )

            # 이 특정 모델 체크포인트에 연결된 지표 기록
            accuracy = evaluate_model(model, validation_data)
            mlflow.log_metric(
                key="accuracy",
                value=accuracy,
                step=epoch,
                model_id=model_info.model_id,  # 지표를 특정 모델에 연결
                dataset=validation_dataset,
            )
```

### 지표를 모델 및 데이터셋에 연결 (Linking Metrics to Models and Datasets)

MLflow 3에서는 지표를 특정 모델 체크포인트 및 데이터셋에 연결할 수 있어 모델 성능의 추적성을 높일 수 있습니다.

codePython

```
# 데이터셋 참조 생성
train_dataset = mlflow.data.from_pandas(train_df, name="training_data")

# 모델 및 데이터셋 링크와 함께 지표 기록
mlflow.log_metric(
    key="f1_score",
    value=0.95,
    step=epoch,
    model_id=model_info.model_id,  # 특정 모델 체크포인트에 연결
    dataset=train_dataset,  # 특정 데이터셋에 연결
)
```

### 모델 체크포인트 검색 및 순위 매기기 (Searching and Ranking Model Checkpoints)

mlflow.search_logged_models()를 사용하여 성능 지표를 기반으로 모델 체크포인트를 검색하고 순위를 매길 수 있습니다.

codePython

```
# 런 내의 모든 모델을 검색하고 정확도 순으로 정렬
ranked_models = mlflow.search_logged_models(
    filter_string=f"source_run_id='{run.info.run_id}'",
    order_by=[{"field_name": "metrics.accuracy", "ascending": False}],
    output_format="list",
)

# 최고 성능 모델 가져오기
best_model = ranked_models[0]
print(f"Best model: {best_model.name}")
print(f"Accuracy: {best_model.metrics[0].value}")

# 추론을 위해 최적 모델 로드
loaded_model = mlflow.pyfunc.load_model(f"models:/{best_model.model_id}")
```

### MLflow 3의 모델 URI (Model URIs in MLflow 3)

MLflow 3는 런 ID 대신 모델 ID를 사용하는 새로운 모델 URI 형식을 도입하여 보다 직접적인 모델 참조를 제공합니다.

codePython

```
# 새로운 MLflow 3 모델 URI 형식
model_uri = f"models:/{model_info.model_id}"
loaded_model = mlflow.pyfunc.load_model(model_uri)

# 이는 기존의 런 기반 URI 형식을 대체합니다:
# model_uri = f"runs:/{run_id}/model_path"
```

이 새로운 접근 방식은 다음과 같은 이점을 제공합니다.

- **직접적인 모델 참조**: 런 ID나 아티팩트 경로를 알 필요가 없습니다.
    
- **더 나은 모델 수명 주기 관리**: 각 모델 체크포인트가 고유 식별자를 가집니다.
    
- **향상된 모델 비교**: 동일한 런 내의 다른 체크포인트들을 쉽게 비교할 수 있습니다.
    
- **강화된 추적성**: 모델, 지표, 데이터셋 간의 명확한 연결 고리를 제공합니다.
    

## 데이터셋 추적 (Tracking Datasets)

MLflow는 모델 훈련 이벤트와 관련된 데이터셋을 추적하는 기능을 제공합니다. 데이터셋과 관련된 메타데이터는 mlflow.log_input() API를 사용하여 저장할 수 있습니다. 자세한 내용은 MLflow data 문서를 참조하세요.

## 런, 모델 및 결과 탐색 (Explore Runs, Models, and Results)

### Tracking UI

Tracking UI를 사용하면 페이지 상단에 표시된 것처럼 실험, 런, 모델을 시각적으로 탐색할 수 있습니다.

- 실험 기반 런 목록 및 비교 (여러 실험 간의 런 비교 포함)
    
- 파라미터 또는 지표 값으로 런 검색
    
- 런 지표 시각화
    
- 런 결과 다운로드 (아티팩트 및 메타데이터)
    

이러한 기능들은 아래와 같이 모델에 대해서도 사용할 수 있습니다.

![Pasted image 20251203092903](obsidian/Attachments/Pasted%20image%2020251203092903.png)

로컬 mlruns 디렉터리에 런을 기록하는 경우, 해당 디렉터리의 상위 경로에서 다음 명령어를 실행한 후 브라우저에서 http://127.0.0.1:5000에 접속하세요.

codeBash

```
mlflow ui --port 5000
```

대안으로, **MLflow Tracking Server**는 동일한 UI를 제공하며 런 아티팩트의 원격 저장을 가능하게 합니다. 이 경우, 추적 서버에 연결할 수 있는 모든 머신에서 http://<MLflow 추적 서버 IP 주소>:5000으로 UI를 볼 수 있습니다.

## MLflow Tracking 환경 설정 (Set up the MLflow Tracking Environment)

> **참고 (Note)**  
> 실험 데이터와 모델을 단순히 로컬 파일에 기록하고 싶다면 이 섹션은 건너뛰어도 됩니다.

MLflow Tracking은 개발 워크플로우를 위한 다양한 시나리오를 지원합니다. 이 섹션에서는 특정 사용 사례에 맞게 MLflow Tracking 환경을 설정하는 방법을 안내합니다. 전체적인 관점에서 MLflow Tracking 환경은 다음과 같은 구성 요소로 이루어집니다.

### 구성 요소 (Components)

#### MLflow Tracking APIs

ML 코드 내에서 MLflow Tracking API를 호출하여 런을 기록하고 필요시 MLflow Tracking Server와 통신할 수 있습니다.

#### Backend Store (백엔드 저장소)

백엔드 저장소는 런 ID, 시작 및 종료 시간, 파라미터, 지표 등 각 런의 다양한 메타데이터를 유지합니다. MLflow는 로컬 파일 기반(파일 시스템)과 데이터베이스 기반(PostgreSQL 등) 두 가지 유형의 저장소를 지원합니다.

또한 Databricks나 Azure Machine Learning과 같은 관리형 서비스를 사용하는 경우, 직접 접근할 수 없는 외부 관리형 REST 기반 백엔드 저장소와 인터페이스 하게 됩니다.

#### Artifact Store (아티팩트 저장소)

아티팩트 저장소는 모델 가중치(예: 피클된 scikit-learn 모델), 이미지(예: PNG), 모델 및 데이터 파일(예: Parquet 파일)과 같은 각 런의 (주로 용량이 큰) 아티팩트를 보존합니다. MLflow는 기본적으로 로컬 파일(mlruns)에 아티팩트를 저장하지만, Amazon S3 및 Azure Blob Storage와 같은 다양한 저장소 옵션도 지원합니다.

MLflow 아티팩트로 기록된 모델의 경우 models:/<model_id> 형식의 모델 URI를 통해 모델을 참조할 수 있습니다. 여기서 'model_id'는 기록된 모델에 할당된 고유 식별자입니다. 이는 기존의 runs:/<run_id>/<artifact_path> 형식을 대체하며 더 직접적인 모델 참조를 제공합니다.

모델이 **MLflow Model Registry**에 등록된 경우 models:/<model-name>/<model-version> 형식의 URI를 통해서도 참조할 수 있습니다. 자세한 내용은 MLflow Model Registry를 참고하세요.

#### MLflow Tracking Server (선택 사항)

MLflow Tracking Server는 백엔드 및/또는 아티팩트 저장소에 접근하기 위한 REST API를 제공하는 독립형 HTTP 서버입니다. 추적 서버는 어떤 데이터를 제공할지 설정하고, 접근 제어, 버전 관리 등을 관리할 수 있는 유연성을 제공합니다. 자세한 내용은 MLflow Tracking Server 문서를 읽어보세요.

### 일반적인 설정 (Common Setups)

이러한 구성 요소를 적절히 설정하면 팀의 개발 워크플로우에 적합한 MLflow Tracking 환경을 만들 수 있습니다. 다음은 MLflow Tracking 환경을 위한 몇 가지 일반적인 설정입니다.

![Pasted image 20251203093425](obsidian/Attachments/Pasted%20image%2020251203093425.png)


1. **Localhost (기본값)**
    
    - **시나리오**: 개인 개발
        
    - **사용 사례**: 기본적으로 MLflow는 각 런의 메타데이터와 아티팩트를 로컬 디렉터리인 mlruns에 기록합니다. 외부 서버, 데이터베이스, 스토리지 설정 없이 MLflow Tracking을 시작하는 가장 간단한 방법입니다.
        
    - **튜토리얼**: QuickStart
        
2. **Local Tracking with Local Database (로컬 데이터베이스를 이용한 로컬 추적)**
    
    - **시나리오**: 개인 개발
        
    - **사용 사례**: MLflow 클라이언트는 백엔드로 SQLAlchemy 호환 데이터베이스(예: SQLite, PostgreSQL, MySQL)와 인터페이스 할 수 있습니다. 메타데이터를 데이터베이스에 저장하면 서버 설정의 번거로움 없이 실험 데이터를 더 깔끔하게 관리할 수 있습니다.
        
    - **튜토리얼**: Tracking Experiments using a Local Database
        
3. **Remote Tracking with MLflow Tracking Server (MLflow 추적 서버를 이용한 원격 추적)**
    
    - **시나리오**: 팀 개발
        
    - **사용 사례**: MLflow Tracking Server를 아티팩트 HTTP 프록시로 설정하여, 사용자가 기본 객체 저장소(S3 등) 서비스와 직접 상호 작용하지 않고도 추적 서버를 통해 아티팩트 요청을 처리하게 할 수 있습니다. 이는 적절한 접근 제어 하에 아티팩트와 실험 메타데이터를 공유 위치에 저장하려는 팀 개발 시나리오에 특히 유용합니다.
        
    - **튜토리얼**: Remote Experiment Tracking with MLflow Tracking Server
        

**MLflow Tracking Server를 이용한 기타 구성**  
MLflow Tracking Server는 다른 특수한 사용 사례를 위한 맞춤 설정을 제공합니다. 기본 설정 학습을 위해 'Remote Experiment Tracking with MLflow Tracking Server'를 따른 후, 필요에 맞는 고급 구성을 위해 다음 자료를 참고하세요.

- Local Tracking Server
    
- Artifacts-only Mode
    
- Direct Access to Artifacts
    

**로컬에서 MLflow Tracking Server 사용하기**  
물론 로컬에서도 MLflow Tracking Server를 실행할 수 있습니다. 로컬 파일이나 데이터베이스를 직접 사용하는 것보다 큰 이점은 없지만, 팀 개발 워크플로우를 로컬에서 테스트하거나 컨테이너 환경에서 머신러닝 코드를 실행할 때 유용할 수 있습니다.

## 자주 묻는 질문 (FAQ)

**여러 런을 병렬로 실행할 수 있나요?**  
네, MLflow는 멀티 프로세싱/스레딩과 같이 여러 런을 병렬로 실행하는 것을 지원합니다. 자세한 내용은 'Launching Multiple Runs in One Program'을 참조하세요.

**많은 MLflow 런을 깔끔하게 정리하려면 어떻게 해야 하나요?**  
MLflow는 런을 정리하는 몇 가지 방법을 제공합니다:

- **실험으로 런 정리하기**: 실험은 런을 담는 논리적 컨테이너입니다. CLI, API 또는 UI를 사용하여 실험을 생성할 수 있습니다.
    
- **자식 런(Child runs) 생성하기**: 하나의 부모 런 아래에 자식 런들을 생성하여 그룹화할 수 있습니다. 예를 들어, 교차 검증(cross-validation) 실험의 각 폴드(fold)마다 자식 런을 생성할 수 있습니다.
    
- **런에 태그 추가하기**: 각 런에 임의의 태그를 연결하여, 태그를 기반으로 런을 필터링하고 검색할 수 있습니다.
    

**Tracking Server를 실행하지 않고 원격 저장소에 직접 접근할 수 있나요?**  
네, 팀 개발 워크플로우에서는 아티팩트 접근을 위해 MLflow Tracking Server를 프록시로 사용하는 것이 모범 사례이지만, 개인 프로젝트나 테스트용으로 사용하는 경우에는 필요하지 않을 수 있습니다. 다음 해결 방법을 통해 이를 수행할 수 있습니다:

1. MLflow Tracking Server와 마찬가지로 자격 증명 및 엔드포인트와 같은 아티팩트 구성을 설정합니다.
    
2. 명시적인 아티팩트 위치를 지정하여 실험을 생성합니다.
    

codePython


```
experiment_name = "your_experiment_name"
mlflow.create_experiment(experiment_name, artifact_location="s3://your-bucket")
mlflow.set_experiment(experiment_name)
```

이 실험 하의 런들은 아티팩트를 원격 저장소에 직접 기록하게 됩니다.

**MLflow Tracking을 Model Registry와 어떻게 통합하나요?**  
MLflow Tracking과 함께 Model Registry 기능을 사용하려면 PostgreSQL과 같은 데이터베이스 기반 저장소를 사용해야 하며, 해당 모델 유형(flavor)의 log_model 메서드를 사용하여 모델을 기록해야 합니다. 모델이 기록되면 UI나 API를 통해 Model Registry에서 모델을 추가, 수정, 업데이트 또는 삭제할 수 있습니다. 워크플로우에 맞게 백엔드 저장소를 적절히 구성하는 방법은 'Backend Stores and Common Setups'를 참조하세요.

**런에 대한 추가 설명 텍스트를 어떻게 포함하나요?**  
시스템 태그인 mlflow.note.content를 사용하여 이 런에 대한 설명 노트를 추가할 수 있습니다. 다른 시스템 태그들은 자동으로 설정되지만, 이 태그는 기본적으로 설정되지 않으며 사용자가 런에 대한 추가 정보를 포함하기 위해 덮어쓸 수 있습니다. 이 내용은 런 페이지의 'Notes' 섹션에 표시됩니다.


