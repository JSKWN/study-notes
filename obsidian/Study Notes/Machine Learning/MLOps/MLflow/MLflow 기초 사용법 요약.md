
MLflow는 머신러닝 프로젝트의 실험, 모델 관리, 평가 및 배포까지의 전체 수명주기를 관리하는 도구

MLflow는 네 가지 component로 구성
- Tracking:
	- 파라미터, 평가 지표 로깅을 통해 실험 결과를 기록
	- 모델 파일, 데이터 셋, 그래프 등의 결과를 산출물로 저장
	- 사용한 코드 등 사용자 정의 함수를 이용하여 기록 가능
- Projects:
	- 코드 패키징
	- Conda 및 Docker를 사용하여 실행 환경 및 의존성 관리
- Models:
	- 모델을 표준화된 포맷으로 저장하고 배포하는 기능
	- Docker, Apache Spark, Azure ML 및 AWS SageMaker와 같은 플랫폼에서 배포할 수 있도록 하는 모델 패키징 도구
- Registry:
	- 중앙 모델 저장소를 이용한 모델 버전, 수명주기 관리

# 1. 설치 및 환경 설정
---
1. **설치**
	- Python 환경에서 `pip install mlflow` 명령어로 설치
	
2. **트래킹 서버(Tracking Server) 실행**
	- 로컬 환경에서 트래킹 서버를 시작하여 실험 결과 관리 가능
	1.  **실험 추적⭐:** mlflow 객체를 이용해 모델 코드를 감싸(`with mlfow.start_run():`) 추적을 시작하면 `mlruns` 폴더 생성
		- 매 실행마다 run_id 개념의 식별자를 가지는 폴더가 해당 mlruns 폴더 내부에 생성 
	2.  **웹 인터페이스 실행⭐:** 웹 ui를 확인하기 위해서는 터미널에 `mlflow ui {추가옵션}`을 입력
		- mlflow ui와 mlflow server는 웹 ui 및 API 실행이라는 동일한 기능을 가지나, 사용 목적 및 기본 설정에 차이가 있음
		1. mlflow ui: 로컬 환경에서 실험 결과 확인 목적의 뷰어
			- 기본 저장소: 명령어를 실행한 위치의 `./mlruns` 폴더를 읽음
			- 별도의 데이터베이스 설정 필요 없음, 바로 실행 및 확인가능
			- 명령어: 
				- `mlflow ui`: 기본 옵션이며, 실행 시 127.0.0.1:5000으로 ui 페이지가 실행됨. 이 경우 <u>외부 환경에서 접속은 불가능</u>
				- ``--host 0.0.0.0`` 옵션 → 외부 접속 허용할 때 사용
					- `mlflow ui –host 0.0.0.0 --port [포트번호]`
				- 리눅스 서버에서 tmux를 이용한 백그라운드 실행 시 `–backend-store-uri` 옵션 활용
					- mlflow를 적용한 소스코드(`mlflow.start_run()`등)를 기준으로 `mlruns`폴더 까지의 상대경로를 입력해야함
					- 예: 현재 소스 코드의 위치가 `experiments`폴더의 상위 디렉토리일 경우
						- `mlflow ui --backend-store-uri experiments/mlruns --host 0.0.0.0 --port 5000`
		2. mlflow server: 협업/서버 구축용. 중앙 서버에서 활용 시 사용
			 - 참고 블로그: https://mlops-for-mle.github.io/tutorial/docs/model-registry/mlflow-setup
			 - 명령어: 백엔드 DB(PostgreSQL)와 아티팩트 저장소(S3)를 연결하여 서버를 띄우는 명령어
				- `--backend-store-uri`: 메타데이터(실험 정보, 파라미터, 메트릭 등)를 저장할 관계형 데이터베이스 주소
					- 형식: postgresql://<사용자>:<비밀번호>@<호스트>:<포트>/<데이터베이스명>       
				- `--default-artifact-root` (또는 --artifacts-destination): 모델 파일, 이미지 등 대용량 파일이 저장될 위치   
					- `--artifacts-destination`: 서버가 S3 접근 권한을 가지고 클라이언트 요청을 대리(Proxy) 처리할 때 사용
					- `--default-artifact-root`: 클라이언트가 S3에 직접 접근하거나, 로컬 파일 시스템을 사용할 때 이용
					- `--host 0.0.0.0`: 로컬호스트(localhost) 뿐만 아니라 외부 네트워크에서의 접속을 허용 (Docker나 Kubernetes 환경에서 필수


# 2. MLflow 훈련 적용 및 저장 위치 설정
---
1. Tracking URI 설정: MLflow 적용 후 실험 데이터(파라미터, 메트릭, 모델 등)가 특정 위치(로컬/서버)에 기록되도록 경로/주소 설정




@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
(임시) 공식 문서상 지원 URI 형식이, 아래 3가지라는데 공식 문서에서 한번 확인할 것
#### 공식 문서상의 지원 URI 형식

MLflow는 크게 3가지 형태의 저장소 방식을 지원합니다.

1. **로컬 파일 경로 (Local File Path)** - **[작성자님이 사용하신 방식]**
    
    - **형식:** file:///절대/경로 또는 /절대/경로 (혹은 상대 경로)
        
    - **동작:** 지정된 디렉토리(예: mlruns 폴더)에 파일 형태로 실험 데이터를 저장합니다.
        
    - **특징:** 별도의 DB나 서버 구축 없이 폴더만 생성되면 되므로 **개인 연구, 로컬 개발, 디버깅**에 가장 적합합니다.
        
    - 참고: 만약 set_tracking_uri를 아예 호출하지 않으면, 기본적으로 코드를 실행하는 위치의 ./mlruns 폴더에 저장됩니다.
        
2. **HTTP/HTTPS 서버 주소 (Remote Server)** - **[글에서 본 방식]**
    
    - **형식:** http://<IP>:<PORT> 또는 https://...
        
    - **동작:** MLflow Tracking Server(별도로 띄운 서버)로 데이터를 전송합니다.
        
    - **특징:** **팀 단위 협업** 시 사용합니다. 여러 사람이 다른 컴퓨터에서 실험을 돌려도 결과가 하나의 서버(웹 UI)에 모이게 됩니다.
        
3. **데이터베이스 (Database)**
    
    - **형식:** sqlite:///mlflow.db, postgresql://user:password@host/db, mysql://...
        
    - **동작:** 실험의 메타데이터(수치, 파라미터 등)를 SQL 데이터베이스에 저장합니다.
        
    - **특징:** 파일 시스템보다 검색 속도가 빠르고 데이터 관리가 용이합니다.

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@


# 기타) (다시 확인 필요) MLflow 단계별 주요 메서드/API 정리
---
1. **실험 추적 및 관리 (Tracking & Experimentation)**
    - 파라미터, 메트릭, 아티팩트 등을 기록하고 비교하는 기능
    1. **기본 사용:**
        - mlflow.set_experiment("실험명"): 실험 단위 설정 (없으면 Default로 잡힘)
        - mlflow.start_run(): 실행 컨텍스트 시작, with문과 함께 사용
        - mlflow.log_param("key", value) / mlflow.log_metric("key", value): 하이퍼파라미터 및 평가지표 수동 기록
            
    2. **자동 로깅 (Autologging):**
        - mlflow.autolog(): 지원되는 라이브러리의 파라미터/메트릭 자동 기록
        - 프레임워크별 지정 가능
	        - mlflow.sklearn.autolog(), mlflow.pytorch.autolog() 등
	        - 
2. **GenAI & LLMOps (생성형 AI 지원)**
    - LLM 앱 개발/평가/모니터링 지원
    1. **Tracing (추적):**
        - LangChain, OpenAI API 호출 시 프롬프트, 토큰 사용량, 레이턴시 등을 자동 캡처
        - 설정: mlflow.openai.autolog() 또는 mlflow.langchain.autolog() 사용
        - 커스텀 함수 추적 시 @mlflow.trace 데코레이터 활용
            
    2. **Evaluation (평가):**
        - RAG나 챗봇 답변의 정확성/안전성 평가 (LLM-as-a-Judge 방식 지원)
        - mlflow.genai.evaluate(): 데이터셋과 예측 함수를 넣어 평가 실행
            
    3. **Prompt Engineering:**
        - 프롬프트 템플릿의 버전 관리 및 UI상에서 비교 가능
        - mlflow.genai.register_prompt()로 등록 후 애플리케이션에서 불러와 사용

3. **딥러닝 프레임워크 통합**
    - TF, PyTorch, Keras 등 주요 라이브러리 특화 로깅 지원
    - mlflow.pytorch.log_model(): PyTorch 모델 저장 시 사용
    - TensorBoard 로그와 연동하여 학습 추이 시각화 가능
        
4. **모델 관리 (Model Registry)**
    - 학습된 모델의 버전 및 배포 단계(Staging/Production) 중앙 관리
    1. **모델 등록:** mlflow.register_model() 사용
    2. **배포 관리:**
        - client.set_registered_model_alias(): 모델에 배포용 별칭(Alias) 설정 (예: @champion)
        - 프로덕션 환경에서는 특정 버전 번호보다 Alias를 통해 모델을 불러오는 것을 권장
            
    3. **서명(Signature):** mlflow.pyfunc.log_model(..., signature=signature)
        - 모델 저장 시 입출력 스키마(Input/Output 예시)를 함께 정의하여 에러 방지
            
5. **배포 및 서빙 (Deployment & Serving)**
    - 모델을 API 형태나 Docker 이미지로 배포
    1. **Local Serving:** 로컬에서 테스트용 REST API 실행
        - 명령어: mlflow models serve -m models:/<모델명>/<버전> -p <포트>
        - 실행 후 curl 등으로 예측 요청 가능
    2. **Docker 빌드:** mlflow models build-docker 명령어로 모델을 포함한 컨테이너 이미지 생성
    3. **Custom Model:** 전처리 로직 등이 포함된 경우 mlflow.pyfunc.PythonModel 클래스를 상속받아 커스텀 모델 정의 가능
        
6. **인프라 및 기타 설정**
    - **AI Gateway:** OpenAI, Anthropic 등 여러 LLM 제공자에 대한 통합 접근 포인트 설정 (mlflow gateway start)
    - **보안 설정:** mlflow server 실행 시 --app-name basic-auth 옵션으로 기본 HTTP 인증 활성화 가능
