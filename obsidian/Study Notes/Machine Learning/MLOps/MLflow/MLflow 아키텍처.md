
> 공식 Doc: https://mlflow.org/docs/latest/self-hosting/architecture/overview/
> - 하위 상세 페이지로 Backend Store, Artifact Store, Tracking Server 페이지 존재


3가지 구성 요소
- Backend Store
	- 실험(experiments), 실행(runs), 추적(traces) 등의 메타데이터를 저장하는 데이터베이스
	- 로컬 환경에서의 저장공간 및 DB를 활용할 수 있고, 원격 DB 등을 이용할 수 있음
- Arfifact Store
	- 상대적으로 큰 용량을 가지는 모델 가중치, 이미지, 데이터 파일 등 각 실행(run)에서 발생하는 아티팩트(=산출물)를 저장
	- 추적 백엔드(tracking backend) 내에 저장하기에는 용량이 크기 때문에 Amazon S3 등의 클라우드 스토리지에 저장하는 것이 적합함
- Tracking Server
	- 팀이 함께 사용할 수 있는 중앙 저장소 서버(클라우드를 주로 이용)
	- MLflow Tracking Server\*는 백엔드 및 아티팩트 저장소에 액세스하기 위한 REST API를 제공하고 MLflow UI를 호스팅하는 FastAPI 서버
		- \*MLflow Tracking Server: 클라우드 서버 환경의 터미널에서 `mlflow server`명령어 타이핑을 통해 실행되는 프로그램을 의미함
			- REST API 방식으로, 파이썬 코드에서 `mlflow.log_param()` 라인을 추가하여 매개변수를 기록하게 하면, 로컬에서 서버로 정보를 보냄
		- UI 호스팅: 서버에 저장된 실험 관련 메타데이터를 이용해 시각화된 웹페이지 화면을 클라이언트에게 제공

사용 방법
- 로컬 호스트와 저장공간
- 로컬 호스트 및 DB 이용
- 추적 서버를 통한 원격 추적
![[Pasted image 20251203113252.png]]

|       | <div style="width:200px">1. 로컬호스트(기본값)</div>                                                                                | <div style="width:200px">2. 로컬 데이터베이스를 통한 로컬 추적</div>                                                                                                                                                                                   | <div style="width:200px">3. MLflow 추적 서버를 통한 원격 추적</div>                                                                                                                                                    |
| ----- | --------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 대본    | 솔로 개발                                                                                                                       | 솔로 개발                                                                                                                                                                                                                                   | 팀 개발                                                                                                                                                                                                        |
| 사용 사례 | 기본적으로 MLflow는 각 실행에 대한 메타데이터와 아티팩트를 로컬 디렉터리에 기록합니다 `mlruns`. 이는 외부 서버, 데이터베이스 및 저장소를 설정하지 않고도 MLflow 추적을 시작하는 가장 간단한 방법입니다. | 데이터베이스 백엔드는 기본 파일 백엔드보다 더 나은 성능과 안정성을 제공합니다. MLflow 클라이언트 SDK는 [SQLAlchemy 호환 데이터베이스](https://mlflow.org/docs/latest/self-hosting/architecture/backend-store/) (예: SQLite, PostgreSQL, MySQL)와 연동하여 메타데이터를 관리하고 아티팩트를 로컬 파일 시스템에 저장합니다. | [MLflow 추적 서버는](https://mlflow.org/docs/latest/self-hosting/architecture/tracking-server/) 메타데이터 및 아티팩트에 대한 원격 액세스를 위한 프록시 역할을 합니다. 이는 적절한 액세스 제어를 통해 아티팩트 및 실험 메타데이터를 공유 위치에 저장하려는 팀 개발 시나리오에 특히 유용합니다.    |
| 설정    | 추가 설정 없음(기본값).                                                                                                              | 또는 환경 변수를 사용하여 추적 URI를 데이터베이스 URI(예: `sqlite:///mlflow.db`) 로 설정합니다 .`mlflow.set_tracking_uri``MLFLOW_TRACKING_URI`                                                                                                                     | [Docker Compose](https://mlflow.org/docs/latest/self-hosting/#docker-compose) 설정을 참조하세요 . 또는 인기 클라우드 제공업체의 [관리형 MLflow 서비스를](https://www.databricks.com/product/managed-mlflow) 사용하여 유지 관리 오버헤드를 피할 수 있습니다. |