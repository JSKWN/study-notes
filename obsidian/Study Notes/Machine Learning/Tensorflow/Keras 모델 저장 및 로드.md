(공식 문서 참조, https://www.tensorflow.org/guide/keras/save_and_serialize?hl=ko)

- [[#1. Keras 모델 구성 요소|1. Keras 모델 구성 요소]]
- [[#2. 모델 저장 및 로딩 방법|2. 모델 저장 및 로딩 방법]]
- [[#3. 저장되는 정보 (아티팩트)|3. 저장되는 정보 (아티팩트)]]
- [[#4. SavedModel 형식 구조|4. SavedModel 형식 구조]]
- [[#5. 사용자 객체가 처리되는 방법|5. 사용자 객체가 처리되는 방법]]
		- [[#저장 메커니즘|저장 메커니즘]]
		- [[#로딩 방식|로딩 방식]]
		- [[#예시|예시]]

## 1. Keras 모델 구성 요소
모델은 크게 다음 세 가지 요소로 구성
- **아키텍처 (Architecture):** 레이어의 종류 및 각 레이어의 연결 방법
- **가중치 (Weights):** 학습된 모델의 상태 (Parameter)
- **옵티마이저 (Optimizer):** 학습 진행 상태 및 설정

TensorFlow SavedModel 형식으로 모든 요소를 단일 아카이브에 저장하며, 이는 텐서플로우의 표준 저장/로딩 방법.

## 2. 모델 저장 및 로딩 방법
기본 API
- **저장:** model.save() 또는 tf.keras.models.save_model()
- **로드:** tf.keras.models.load_model()

``` Python
# 1. 저장
model = ... # 학습된 모델 객체
model.save("path/to/model_folder")

# 2. 로드
from tensorflow import keras
model = keras.models.load_model("path/to/model_folder")

```
## 3. 저장되는 정보 (아티팩트)
전체 모델을 단일 아티팩트로 저장할 수 있으며 아래를 포함.
- 모델의 아키텍처 및 구성
- 학습된 모델의 가중치 값
- 모델의 컴파일 정보 (compile()이 호출된 경우)
- 옵티마이저 및 그 상태 (훈련 중단 지점에서 재시작 가능)

## 4. SavedModel 형식 구조
SavedModel은 모델 아키텍처, 가중치 및 호출 함수에서 추적된 그래프를 저장하는, 포괄적인 저장형식. 사용된 내장 레이어 및 사용자 정의 레이어 등을 모두 복원 가능

`model.save(“model_name”)`을 실행하면 이름에 해당하는 폴더를 생성하며, 아래와 같이 구성
- 📂 **model_name/**
    - 📂 **assets/**: 보조 데이터
    - 📂 **variables/**: 가중치(Weights) 저장
    - 📄 **saved_model.pb**: 모델 아키텍처, 훈련 구성(옵티마이저, 손실, 메트릭) 등 그래프 정보
    - 📄 **keras_metadata.pb**: Keras 관련 메타데이터
    - 📄 **fingerprint.pb**: 모델 식별용 지문 파일

## 5. 사용자 객체가 처리되는 방법
#### 저장 메커니즘
SavedModel은 모델과 레이어를 저장할 때 **클래스 이름, 호출 함수(Call function), 손실 및 가중치**를 저장.

- **호출 함수:** 모델/레이어의 계산 그래프를 정의하며, 모델 복원 시 사용.
- **Config 구현:** 사용자 정의 모델/레이어 제작 시 get_config() 및 from_config() 메서드를 정의해야 직렬화(Serialization) 가능.

#### 로딩 방식
1. **정적 로딩 (Custom Object):**
    - 인자로 원래의 클래스 객체를 제공하여 로드.
    - custom_objects={"ClassName": Class} 인자 사용.
2. (그냥 로딩) **동적 로딩:**
    - 추가 인자 없이 그냥 로드
    - 저장된 그래프 정보를 바탕으로 동적으로 클래스를 생성하여 로드.

#### 예시
```Python
class CustomModel(keras.Model):
    def __init__(self, hidden_units):
        super(CustomModel, self).__init__()
        self.hidden_units = hidden_units
        self.dense_layers = [keras.layers.Dense(u) for u in hidden_units]

    def call(self, inputs):
        x = inputs
        for layer in self.dense_layers:
            x = layer(x)
        return x

    def get_config(self):
        return {"hidden_units": self.hidden_units}

    @classmethod
    def from_config(cls, config):
        return cls(**config)


model = CustomModel([16, 16, 10])
# Build the model by calling it
input_arr = tf.random.uniform((1, 5))
outputs = model(input_arr)
model.save("my_model")

# Option 1: Load with the custom_object argument.
loaded_1 = keras.models.load_model(
    "my_model", custom_objects={"CustomModel": CustomModel}
)

# Option 2: Load without the CustomModel class.

# Delete the custom-defined model class to ensure that the loader does not have
# access to it.
del CustomModel

loaded_2 = keras.models.load_model("my_model")
np.testing.assert_allclose(loaded_1(input_arr), outputs)
np.testing.assert_allclose(loaded_2(input_arr), outputs)

print("Original model:", model)
print("Model Loaded with custom objects:", loaded_1)
print("Model loaded without the custom object class:", loaded_2)

```

>Model Loaded with custom objects: <__main__.CustomModel object at 0x7f56e0e9a820>
>Model loaded without the custom object class: <keras.saving.legacy.saved_model.load.CustomModel object at 0x7f56e0e31b50>

첫번째 모델은 custom_object={“CustomModel”: CustomModel} 인자를 제공하여 로드.
두번째 모델의 경우, 인자 없이 그냥 load_model(“모델명”)으로 로드.

동적으로 사용자 정의 객체가 포함된 클래스를 로딩하긴 하지만, 이를 위해서는 사용저 정의 레이어/모델에 `get_config`/`from_config` 메서드가 정의되어야함


