#### MLflow UI 실행 경로 == set_tracking_uri 경로
- MLflow을 이용한 훈련 Tracking 시 중요한 경로 두 가지는, 훈련 스크립트 내의 산출물(artifacts)을 저장하기 위한 경로와 UI를 실행하기 위한 mlflow ui \-\-backend-store-uri 경로이다.
- 결론적으로는 두 경로가 동일해야 하며 훈련 스크립트 상에는 다음과 같이 구현하였다.
	- 산출물 경로 지정: `mlflow.set_tracking_uri(tracking_path.as_uri())`
		-  tracking_path: `'절대경로/mlflow/mlruns’` → 절대경로로 만들 수 있도록 Path() 객체로 변환한 뒤, reslove() 메서드를 사용하여 절대경로 변환 & 심볼릭 링크 제거.
			- 이 때 ~/mlflow/mlruns는 임의로 만든 경로이며, mlflow의 컨벤션과 무관
		- Path.uri() 메서드는 경로 문자열을 uri형식(“file://~”)로 변환해주는 역할
- 우분투 터미널에서 MLflow의 UI 실행 명령어는 다음과 같이 작성하였음.
	- `mlflow ui --backend-store-uri ‘절대경로/mlflow/mlruns’` 


#### (TF2 환경) mlflow.autolog() 사용시 사용자 정의 레이어 저장 불가
- `autolog()` 메서드는 Tensorflow의 기본 제공 레이어인 `Conv2D`, `Dense`등을 이용한 레이어는 자동으로 저장 가능하나, 모델 복원 및 수정을 위해 TF2에서 제공되는 SavedModel 형식을 지원하지 않음.
- 따라서 메서드의 인자로 `log_models = False`를 주었으며, `with mlflow.start_run()` 구문으로 블럭을 감싼 후, 텐서플로우의 SavedModel 관련 api인 model.save() 메서드를 활용함(`save_format='tf'`는 필수는 아니나 권장).
- 예시 코드
```Python
# 자동 로깅 활성화 (단, 모델 저장은 TF의 SavedModel 형식을 이용할 예정이므로, False 값 부여)
mlflow.autolog(log_models=False)

with mlflow.start_run() as run:
    # 학습 수행
    model.fit(train_ds, validation_data=val_ds, epochs=epochs)
    
    # 학습 완료 후 모델 저장
    tmp_path = SCRIPT_DIR / "tmp_model"
    model.save(str(tmp_path), save_format="tf")  # SavedModel 형식으로 저장 (디렉터리 구조)
    
    # MLflow에 모델 아티팩트 업로드 (tmp_model/ 내부 파일들이 artifacts/model/로 복사됨)
    mlflow.log_artifacts(str(tmp_path), artifact_path="model")
  ```
#### 텐서플로우 모델 저장 디렉토리 구조 및 MLflow 아티팩트 저장 위치
- 사용자 정의 레이어를 사용했기 때문에, 텐서플로우의 api인 model.save(‘절대경로’) 이용.
- 모델 저장 경로는 스크립트의 현재 위치를 기준으로 작성했기 때문에, 학습된 모델은 `모델명`으로 디렉토리가 생성되어 관리되며 구조는 아래와 같음.
	- 📂 **model_name/**
	    - 📂 **assets/**: 보조 데이터
	    - 📂 **variables/**: 가중치(Weights) 저장
	    - 📄 **saved_model.pb**: 모델 아키텍처, 훈련 구성(옵티마이저, 손실, 메트릭) 등 그래프 정보
	    - 📄 **keras_metadata.pb**: Keras 관련 메타데이터
	    - 📄 **fingerprint.pb**: 모델 식별용 지문 파일

- MLflow의 경우 `mlflow.log_artifacts(모델경로, artifact_path='model')`로 설정하면 각 시행(Run)ID의 하위 폴더인 ‘artifacts’ 폴더에 해당 SavedModel 폴더 구조가 그대로 이관되어 저장
	```Text
	mlruns/<exp_id>/<run_id>/artifacts/model/                    ← 
	  artifact_path="model"로 지정한 폴더
    ├── saved_model.pb 
    ├── variables/
    │   ├── variables.data-00000-of-00001
    │   └── variables.index
    └── assets/
    └── ... 그 외 파일들
	  
	  ```

