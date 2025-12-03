## 목차
- [[#1. 실험 추적 방식 선택 (Choose Your Approach)|1. 실험 추적 방식 선택 (Choose Your Approach)]]
	- [[#1. 실험 추적 방식 선택 (Choose Your Approach)#🤖 자동 로깅 (Automatic Logging) - 설정 제로, 최대 범위|🤖 자동 로깅 (Automatic Logging) - 설정 제로, 최대 범위]]
	- [[#1. 실험 추적 방식 선택 (Choose Your Approach)#🛠️ 수동 로깅 (Manual Logging) - 완전한 제어, 사용자 정의 워크플로우|🛠️ 수동 로깅 (Manual Logging) - 완전한 제어, 사용자 정의 워크플로우]]
- [[#2. 핵심 로깅 함수 (Core Logging Functions)|2. 핵심 로깅 함수 (Core Logging Functions)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#설정 및 구성 (Setup & Configuration)|설정 및 구성 (Setup & Configuration)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#런 관리 (Run Management)|런 관리 (Run Management)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#데이터 로깅 (Data Logging)|데이터 로깅 (Data Logging)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#아티팩트 관리 (Artifact Management)|아티팩트 관리 (Artifact Management)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#모델 관리 (MLflow 3 신규 기능)|모델 관리 (MLflow 3 신규 기능)]]
	- [[#2. 핵심 로깅 함수 (Core Logging Functions)#활성 모델 관리 (Active Model Management - MLflow 3 신규 기능)|활성 모델 관리 (Active Model Management - MLflow 3 신규 기능)]]
- [[#3. 언어별 API 지원 범위 (Language-Specific API Coverage)|3. 언어별 API 지원 범위 (Language-Specific API Coverage)]]
- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)|4. 고급 추적 패턴 (Advanced Tracking Patterns)]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)|기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)]]
		- [[#기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)#외부 모델 생성 및 관리|외부 모델 생성 및 관리]]
		- [[#기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)#고급 모델 수명 주기 관리|고급 모델 수명 주기 관리]]
		- [[#기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)#기록된 모델 검색 및 조회|기록된 모델 검색 및 조회]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#정밀한 지표 추적 (Precise Metric Tracking)|정밀한 지표 추적 (Precise Metric Tracking)]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#실험 구성 (Experiment Organization)|실험 구성 (Experiment Organization)]]
		- [[#실험 구성 (Experiment Organization)#부모-자식 관계를 가진 계층적 런 (Hierarchical Runs)|부모-자식 관계를 가진 계층적 런 (Hierarchical Runs)]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#병렬 실행 전략 (Parallel Execution Strategies)|병렬 실행 전략 (Parallel Execution Strategies)]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#정리를 위한 스마트 태깅 (Smart Tagging for Organization)|정리를 위한 스마트 태깅 (Smart Tagging for Organization)]]
	- [[#4. 고급 추적 패턴 (Advanced Tracking Patterns)#시스템 태그 참조 (System Tags Reference)|시스템 태그 참조 (System Tags Reference)]]
- [[#5. 자동 로깅과의 통합 (Integration with Auto Logging)|5. 자동 로깅과의 통합 (Integration with Auto Logging)]]

# MLflow Tracking APIs

MLflow Tracking은 머신러닝 실험을 캡처하기 위해 여러 프로그래밍 언어에 걸쳐 포괄적인 API를 제공합니다. 자동화된 계측(instrumentation)을 선호하든 세부적인 제어를 선호하든, MLflow는 귀하의 워크플로우에 맞춰 조정됩니다.


## 1. 실험 추적 방식 선택 (Choose Your Approach)

MLflow는 실험 추적을 위한 두 가지 주요 방법을 제공하며, 각각 다른 사용 사례에 최적화되어 있습니다.
### 🤖 자동 로깅 (Automatic Logging) - 설정 제로, 최대 범위

빠르게 시작하거나 지원되는 ML 라이브러리를 사용할 때 완벽합니다. 한 줄만 추가하면 MLflow가 모든 것을 자동으로 캡처합니다.

codePython

```
import mlflow

mlflow.autolog()  # 이걸로 끝입니다!

# 기존 훈련 코드는 변경 없이 작동합니다
model.fit(X_train, y_train)
```

**자동으로 기록되는 항목:**

- 모델 파라미터 및 하이퍼파라미터
    
- 훈련 및 검증 지표 (Metrics)
    
- 모델 아티팩트 및 체크포인트
    
- 훈련 플롯 및 시각화 자료
    
- 프레임워크별 메타데이터
    

**지원되는 라이브러리:** Scikit-learn, XGBoost, LightGBM, PyTorch, Keras/TensorFlow, Spark 등.

→ 자동 로깅(Auto Logging) 살펴보기

### 🛠️ 수동 로깅 (Manual Logging) - 완전한 제어, 사용자 정의 워크플로우

사용자 정의 훈련 루프, 고급 실험, 또는 추적 대상에 대한 정밀한 제어가 필요할 때 이상적입니다.

- Python
    
- Java
    
- R
    

codePython

```
import mlflow

with mlflow.start_run():
    # 파라미터 기록
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_param("batch_size", 32)

    # 훈련 로직
    for epoch in range(num_epochs):
        train_loss = train_model()
        val_loss = validate_model()

        # 스텝(step) 추적과 함께 지표 기록
        mlflow.log_metrics({"train_loss": train_loss, "val_loss": val_loss}, step=epoch)

    # 최종 모델 기록
    mlflow.sklearn.log_model(model, name="model")
```

## 2. 핵심 로깅 함수 (Core Logging Functions)

### 설정 및 구성 (Setup & Configuration)

|                            | <div style="width:200px"></div><br> |                                                    |
| -------------------------- | ----------------------------------- | -------------------------------------------------- |
| 함수                         | 목적                                  | 예시                                                 |
| mlflow.set_tracking_uri()  | 트래킹 서버 또는 데이터베이스 연결                 | mlflow.set_tracking_uri("http://localhost:5000")   |
| mlflow.get_tracking_uri()  | 현재 트래킹 URI 가져오기                     | uri = mlflow.get_tracking_uri()                    |
| mlflow.create_experiment() | 새 실험 생성                             | exp_id = mlflow.create_experiment("my-experiment") |
| mlflow.set_experiment()    | 활성 실험 설정                            | mlflow.set_experiment("fraud-detection")           |

### 런 관리 (Run Management)

|                          | <div style="width:200px"></div> |                                     |
| ------------------------ | ------------------------------- | ----------------------------------- |
| 함수                       | 목적                              | 예시                                  |
| mlflow.start_run()       | 새 런 시작 (컨텍스트 관리자 사용)            | with mlflow.start_run(): ...        |
| mlflow.end_run()         | 현재 런 종료                         | mlflow.end_run(status="FINISHED")   |
| mlflow.active_run()      | 현재 활성 런 가져오기                    | run = mlflow.active_run()           |
| mlflow.last_active_run() | 마지막으로 완료된 런 가져오기                | last_run = mlflow.last_active_run() |

### 데이터 로깅 (Data Logging)

|                                            | <div style="width:200px"></div> |                                              |
| ------------------------------------------ | ------------------------------- | -------------------------------------------- |
| 함수                                         | 목적                              | 예시                                           |
| mlflow.log_param() / mlflow.log_params()   | 하이퍼파라미터 기록                      | mlflow.log_param("lr", 0.01)                 |
| mlflow.log_metric() / mlflow.log_metrics() | 성능 지표 기록                        | mlflow.log_metric("accuracy", 0.95, step=10) |
| mlflow.log_input()                         | 데이터셋 정보 기록                      | mlflow.log_input(dataset)                    |
| mlflow.set_tag() / mlflow.set_tags()       | 메타데이터 태그 추가                     | mlflow.set_tag("model_type", "CNN")          |

### 아티팩트 관리 (Artifact Management)

|                           | <div style="width:200px"></div> |                                  |
| ------------------------- | ------------------------------- | -------------------------------- |
| 함수                        | 목적                              | 예시                               |
| mlflow.log_artifact()     | 단일 파일/디렉터리 기록                   | mlflow.log_artifact("model.pkl") |
| mlflow.log_artifacts()    | 전체 디렉터리 기록                      | mlflow.log_artifacts("./plots/") |
| mlflow.get_artifact_uri() | 아티팩트 저장 위치 가져오기                 | uri = mlflow.get_artifact_uri()  |

### 모델 관리 (MLflow 3 신규 기능)

|                                  | <div style="width:200px"></div> |                                                                       |
| -------------------------------- | ------------------------------- | --------------------------------------------------------------------- |
| 함수                               | 목적                              | 예시                                                                    |
| mlflow.initialize_logged_model() | PENDING 상태로 기록된 모델 초기화          | model = mlflow.initialize_logged_model(name="my_model")               |
| mlflow.create_external_model()   | 외부 모델 생성 (아티팩트가 MLflow 외부에 저장됨) | model = mlflow.create_external_model(name="agent")                    |
| mlflow.finalize_logged_model()   | 모델 상태를 READY 또는 FAILED로 업데이트    | mlflow.finalize_logged_model(model_id, "READY")                       |
| mlflow.get_logged_model()        | ID로 기록된 모델 검색                   | model = mlflow.get_logged_model(model_id)                             |
| mlflow.last_logged_model()       | 가장 최근에 기록된 모델 가져오기              | model = mlflow.last_logged_model()                                    |
| mlflow.search_logged_models()    | 기록된 모델 검색                       | models = mlflow.search_logged_models(filter_string="name='my_model'") |
| mlflow.log_model_params()        | 특정 모델에 파라미터 기록                  | mlflow.log_model_params({"param": "value"}, model_id)                 |
| mlflow.set_logged_model_tags()   | 기록된 모델에 태그 설정                   | mlflow.set_logged_model_tags(model_id, {"key": "value"})              |
| mlflow.delete_logged_model_tag() | 기록된 모델에서 태그 삭제                  | mlflow.delete_logged_model_tag(model_id, "key")                       |
|                                  |                                 |                                                                       |

### 활성 모델 관리 (Active Model Management - MLflow 3 신규 기능)

|                              | <div style="width:200px"></div> |                                          |
| ---------------------------- | ------------------------------- | ---------------------------------------- |
| 함수                           | 목적                              | 예시                                       |
| mlflow.set_active_model()    | 트레이스(trace) 연결을 위한 활성 모델 설정     | mlflow.set_active_model(name="my_model") |
| mlflow.get_active_model_id() | 현재 활성 모델 ID 가져오기                | model_id = mlflow.get_active_model_id()  |
| mlflow.clear_active_model()  | 활성 모델 해제                        | mlflow.clear_active_model()              |

## 3. 언어별 API 지원 범위 (Language-Specific API Coverage)

|   |   |   |   |   |
|---|---|---|---|---|
|기능 (Capability)|Python|Java|R|REST API|
|기본 로깅 (Basic Logging)|✅ 전체 지원|✅ 전체 지원|✅ 전체 지원|✅ 전체 지원|
|자동 로깅 (Auto Logging)|✅ 15개 이상 라이브러리|❌ 미지원|✅ 제한적 지원|❌ 미지원|
|모델 로깅 (Model Logging)|✅ 20개 이상 Flavor|✅ 기본 지원|✅ 기본 지원|✅ 아티팩트를 통해 지원|
|기록된 모델 관리 (Logged Model Management)|✅ 전체 지원 (MLflow 3)|❌ 미지원|❌ 미지원|✅ 기본 지원|
|데이터셋 추적 (Dataset Tracking)|✅ 전체 지원|✅ 기본 지원|✅ 기본 지원|✅ 기본 지원|
|검색 및 조회 (Search & Query)|✅ 고급 기능|✅ 기본 지원|✅ 기본 지원|✅ 전체 지원|

> **api-parity**  
> Python API가 가장 포괄적인 기능 세트를 제공합니다. Java와 R API는 핵심 기능을 제공하며 릴리스마다 기능이 추가되고 있습니다.

<br>


## 4. 고급 추적 패턴 (Advanced Tracking Patterns)

### 기록된 모델 다루기 (Working with Logged Models - MLflow 3 신규 기능)

MLflow 3는 런(Runs)과 독립적으로 모델을 추적할 수 있는 강력한 기록된 모델(Logged Model) 관리 기능을 도입했습니다.

#### 외부 모델 생성 및 관리

MLflow 외부에 저장된 모델(배포된 에이전트나 외부 모델 아티팩트 등)의 경우:

codePython

```
import mlflow

# 아티팩트를 MLflow에 저장하지 않고 추적을 위한 외부 모델 생성
model = mlflow.create_external_model(
    name="chatbot_agent",
    model_type="agent",
    tags={"version": "v1.0", "environment": "production"},
)

# 이 모델에 특정한 파라미터 기록
mlflow.log_model_params(
    {"temperature": "0.7", "max_tokens": "1000"}, model_id=model.model_id
)

# 자동 트레이스 연결을 위해 활성 모델로 설정
mlflow.set_active_model(model_id=model.model_id)


@mlflow.trace
def chat_with_agent(message):
    # 이 트레이스는 자동으로 활성 모델에 연결됩니다
    return agent.chat(message)


# 트레이스들이 이제 외부 모델에 연결되었습니다
traces = mlflow.search_traces(model_id=model.model_id)
```

#### 고급 모델 수명 주기 관리

커스텀 준비 또는 검증이 필요한 모델의 경우:

codePython

```
import mlflow
from mlflow.entities import LoggedModelStatus

# PENDING 상태로 모델 초기화
model = mlflow.initialize_logged_model(
    name="custom_neural_network",
    model_type="neural_network",
    tags={"architecture": "transformer", "dataset": "custom"},
)

try:
    # 커스텀 모델 준비 로직
    train_model()
    validate_model()

    # 표준 MLflow 모델 로깅을 사용하여 모델 아티팩트 저장
    mlflow.pytorch.log_model(
        pytorch_model=model_instance,
        name="model",
        model_id=model.model_id,  # 기록된 모델에 연결
    )

    # 모델을 READY 상태로 확정
    mlflow.finalize_logged_model(model.model_id, LoggedModelStatus.READY)

except Exception as e:
    # 문제 발생 시 모델을 FAILED로 표시
    mlflow.finalize_logged_model(model.model_id, LoggedModelStatus.FAILED)
    raise

# 기록된 모델을 검색하여 작업
final_model = mlflow.get_logged_model(model.model_id)
print(f"Model {final_model.name} is {final_model.status}")
```

#### 기록된 모델 검색 및 조회

codePython

```
# 모든 프로덕션 준비 완료된 트랜스포머 모델 찾기
production_models = mlflow.search_logged_models(
    filter_string="tags.environment = 'production' AND model_type = 'transformer'",
    order_by=[{"field_name": "creation_time", "ascending": False}],
    output_format="pandas",
)

# 특정 성능 지표를 가진 모델 검색
high_accuracy_models = mlflow.search_logged_models(
    filter_string="metrics.accuracy > 0.95",
    datasets=[{"dataset_name": "test_set"}],  # 테스트 세트 지표만 고려
    max_results=10,
)

# 현재 세션에서 가장 최근에 기록된 모델 가져오기
latest_model = mlflow.last_logged_model()
if latest_model:
    print(f"Latest model: {latest_model.name} (ID: {latest_model.model_id})")
```

### 정밀한 지표 추적 (Precise Metric Tracking)

커스텀 타임스탬프와 스텝(step)을 사용하여 지표가 기록되는 시점과 방식을 정확하게 제어하세요.

codePython

```
import time
from datetime import datetime

# 커스텀 스텝(훈련 반복/에포크)으로 기록
for epoch in range(100):
    loss = train_epoch()
    mlflow.log_metric("train_loss", loss, step=epoch)

# 커스텀 타임스탬프로 기록
now = int(time.time() * 1000)  # MLflow는 밀리초 단위를 예상합니다
mlflow.log_metric("inference_latency", latency, timestamp=now)

# 스텝과 타임스탬프 모두 사용하여 기록
mlflow.log_metric("gpu_utilization", gpu_usage, step=epoch, timestamp=now)
```

**스텝(Step) 요구사항:**

- 유효한 64비트 정수여야 함
    
- 음수이거나 순서가 뒤섞일 수 있음
    
- 시퀀스 내에 간격을 허용함 (예: 1, 5, 75, -20)
    

### 실험 구성 (Experiment Organization)

비교와 분석이 용이하도록 실험을 구조화하세요.

codePython

```
# 방법 1: 환경 변수
import os

os.environ["MLFLOW_EXPERIMENT_NAME"] = "fraud-detection-v2"

# 방법 2: 명시적 실험 설정
mlflow.set_experiment("hyperparameter-tuning")

# 방법 3: 커스텀 설정으로 생성
experiment_id = mlflow.create_experiment(
    "production-models",
    artifact_location="s3://my-bucket/experiments/",
    tags={"team": "data-science", "environment": "prod"},
)
```

#### 부모-자식 관계를 가진 계층적 런 (Hierarchical Runs)

하이퍼파라미터 스윕(sweep)이나 교차 검증과 같은 복잡한 실험을 구성하세요.

codePython

```
# 전체 실험을 위한 부모 런
with mlflow.start_run(run_name="hyperparameter_sweep") as parent_run:
    mlflow.log_param("search_strategy", "random")

    best_score = 0
    best_params = {}

    # 각 파라미터 조합을 위한 자식 런
    for lr in [0.001, 0.01, 0.1]:
        for batch_size in [16, 32, 64]:
            with mlflow.start_run(
                nested=True, run_name=f"lr_{lr}_bs_{batch_size}"
            ) as child_run:
                mlflow.log_params({"learning_rate": lr, "batch_size": batch_size})

                # 훈련 및 평가
                model = train_model(lr, batch_size)
                score = evaluate_model(model)
                mlflow.log_metric("accuracy", score)

                # 부모 런에서 최고 설정 추적
                if score > best_score:
                    best_score = score
                    best_params = {"learning_rate": lr, "batch_size": batch_size}

    # 부모 런에 최고 결과 기록
    mlflow.log_params(best_params)
    mlflow.log_metric("best_accuracy", best_score)

# 자식 런 조회
child_runs = mlflow.search_runs(
    filter_string=f"tags.mlflow.parentRunId = '{parent_run.info.run_id}'"
)
print("Child run results:")
print(child_runs[["run_id", "params.learning_rate", "metrics.accuracy"]])
```

### 병렬 실행 전략 (Parallel Execution Strategies)

다양한 병렬화 접근 방식을 사용하여 여러 런을 효율적으로 처리하세요.

- 순차적 런 (Sequential Runs)
    
- 멀티프로세싱 (Multiprocessing)
    
- 멀티스레딩 (Multithreading)
    

간단한 하이퍼파라미터 스윕이나 A/B 테스트에 완벽합니다.

codePython

```
configs = [
    {"model": "RandomForest", "n_estimators": 100},
    {"model": "XGBoost", "max_depth": 6},
    {"model": "LogisticRegression", "C": 1.0},
]

for config in configs:
    with mlflow.start_run(run_name=config["model"]):
        mlflow.log_params(config)
        model = train_model(config)
        score = evaluate_model(model)
        mlflow.log_metric("f1_score", score)
```

### 정리를 위한 스마트 태깅 (Smart Tagging for Organization)

태그를 전략적으로 사용하여 실험을 정리하고 필터링하세요.

codePython

```
with mlflow.start_run():
    # 필터링을 위한 설명적 태그
    mlflow.set_tags(
        {
            "model_family": "transformer",
            "dataset_version": "v2.1",
            "environment": "production",
            "team": "nlp-research",
            "gpu_type": "V100",
            "experiment_phase": "hyperparameter_tuning",
        }
    )

    # 문서를 위한 특별 노트 태그
    mlflow.set_tag(
        "mlflow.note.content",
        "Baseline transformer model with attention dropout. "
        "Testing different learning rate schedules.",
    )

    # 훈련 코드...
```

태그로 실험 검색하기:

codePython

```
# 모든 트랜스포머 실험 찾기
transformer_runs = mlflow.search_runs(filter_string="tags.model_family = 'transformer'")

# 프로덕션 준비 완료된 모델 찾기
prod_models = mlflow.search_runs(
    filter_string="tags.environment = 'production' AND metrics.accuracy > 0.95"
)
```

### 시스템 태그 참조 (System Tags Reference)

MLflow는 실행 컨텍스트를 캡처하기 위해 자동으로 여러 시스템 태그를 설정합니다.

|                          | <div style="width:200px"></div> |                     |
| ------------------------ | ------------------------------- | ------------------- |
| 태그                       | 설명                              | 설정 시점               |
| mlflow.source.name       | 소스 파일 또는 노트북 이름                 | 항상                  |
| mlflow.source.type       | 소스 유형 (NOTEBOOK, JOB, LOCAL 등)  | 항상                  |
| mlflow.user              | 런을 생성한 사용자                      | 항상                  |
| mlflow.source.git.commit | Git 커밋 해시                       | git 저장소에서 실행 시      |
| mlflow.source.git.branch | Git 브랜치 이름                      | MLflow Projects만 해당 |
| mlflow.parentRunId       | 중첩된 런의 부모 런 ID                  | 자식 런에만 해당           |
| mlflow.docker.image.name | 사용된 Docker 이미지                  | Docker 환경           |
| mlflow.note.content      | 사용자 편집 가능 설명                    | 수동 전용               |

> **pro-tip**  
> mlflow.note.content를 사용하여 실험 인사이트, 가설 또는 결과를 MLflow UI에 직접 문서화하세요. 이 태그는 런 페이지의 전용 Notes 섹션에 표시됩니다.

## 5. 자동 로깅과의 통합 (Integration with Auto Logging)

두 방식의 장점을 모두 활용하기 위해 자동 로깅과 수동 추적을 결합하세요.

codePython

```
import mlflow
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report

# 자동 로깅 활성화
mlflow.autolog()

with mlflow.start_run():
    # 자동 로깅이 모델 훈련을 자동으로 캡처합니다
    model = RandomForestClassifier(n_estimators=100)
    model.fit(X_train, y_train)

    # 커스텀 지표 및 아티팩트 추가
    predictions = model.predict(X_test)

    # 커스텀 평가 지표 기록
    report = classification_report(y_test, predictions, output_dict=True)
    mlflow.log_metrics(
        {
            "precision_macro": report["macro avg"]["precision"],
            "recall_macro": report["macro avg"]["recall"],
            "f1_macro": report["macro avg"]["f1-score"],
        }
    )

    # 커스텀 아티팩트 기록
    feature_importance = pd.DataFrame(
        {"feature": feature_names, "importance": model.feature_importances_}
    )
    feature_importance.to_csv("feature_importance.csv")
    mlflow.log_artifact("feature_importance.csv")

    # 추가 처리를 위해 자동 기록된 런에 접근
    current_run = mlflow.active_run()
    print(f"Auto-logged run ID: {current_run.info.run_id}")

# 완료된 런 접근
last_run = mlflow.last_active_run()
print(f"Final run status: {last_run.info.status}")
```