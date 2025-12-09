`tf.keras.layers.Layer`를 상속받아 사용자 정의 레이어를 만들 때, Tensorflow의 ‘Graph Execution*’과 ‘Variable Management*’ 메커니즘을 위해 반드시 구현해야하는 메서드 존재. (그래프 실행은 속도, 성능의 이점)

### 0. __init__(self, **kwargs)
- **역할:** 객체 생성 및 하이퍼파라미터 초기화.
- **필수:** super().__init__(**kwargs) 호출 (레이어 이름, dtype 등의 처리를 위함).
- **구현:**
    - 필터 수, 커널 크기 등 레이어 동작에 필요한 설정 값을 멤버 변수로 저장 (self.filters = filters).
    - 내부에서 사용할 하위 레이어(Conv2D, Dense 등)를 정의 (self.dense = layers.Dense(32)).
### 1. build(self, input_shape)
- **역할:** 입력 텐서의 형태(input_shape)가 확정되는 시점에 호출되어, **가중치(Weight)**와 **편향(Bias)**을 생성(지연 초기화).
- **구현:**
    - **self.add_weight() 사용:** 직접 tf.Variable을 쓰지 않고 이 메서드를 써야 Keras가 가중치를 추적(Trainable/Non-trainable)하고 백엔드에 등록할 수 있음.
    - **Shape 결정:** input_shape를 기반으로 가중치의 크기를 계산하여 설정.
- **주의:** 반드시`super().build(input_shape)`를 호출하여 레이어 빌드 완료 상태(self.built = True)로 만들어야 함.

### 2. call(self, inputs, training=None)
- **역할:** 순방향 전파(Forward Pass) 로직 구현.
- **주요 인자:**
    - inputs: 입력 텐서.
    - training: 학습(True)과 추론(False) 시 동작이 다른 경우(예: Dropout, BatchNormalization) 분기 처리에 사용.
- **주의:** 연산 시 numpy 함수 대신 TensorFlow Ops (tf.matmul, tf.nn.relu 등)를 사용해야 그래프 모드 최적화가 가능함.

### 3. get_config(self)
- **역할:** 모델 저장/로드 시 레이어의 설정을 유지(직렬화)하기 위함.
- **구현:**
    - config = super().get_config()로 부모 설정 로드.
    - init__에서 받은 인자들을 config.update({...})로 추가하여 반환.


#### 참고 소스코드
```Python
import tensorflow as tf
from tensorflow.keras import layers

class MyCustomLayer(layers.Layer):
    def __init__(self, output_dim, **kwargs):
        super(MyCustomLayer, self).__init__(**kwargs)
        self.output_dim = output_dim
        # 서브 레이어가 필요하다면 여기서 정의
        # self.dense = layers.Dense(output_dim) 

    def build(self, input_shape):
        # input_shape: (batch_size, input_dim)
        # 가중치 생성 (Lazy Initialization)
        self.w = self.add_weight(
		        # shape: 내가 정의한 레이어로 들어오고 나가는 텐서의 차원수
            shape=(input_shape[-1], self.output_dim),
            initializer="random_normal",
            trainable=True,
            name="kernel"
        )
        self.b = self.add_weight(
            shape=(self.output_dim,),
            initializer="zeros",
            trainable=True,
            name="bias"
        )
        # 중요: 끝에 상위 클래스의 build 호출
        super(MyCustomLayer, self).build(input_shape)

    def call(self, inputs, training=None):
        # 순방향 연산 정의
        output = tf.matmul(inputs, self.w) + self.b
        
        # training 인자에 따른 분기 처리 예시
        # if training:
        #     output = tf.nn.dropout(output, rate=0.5)
        
        return output

    def get_config(self):
        config = super(MyCustomLayer, self).get_config()
        config.update({
            "output_dim": self.output_dim
        })
        return config

```



(참고)
- 🔧 Graph Execution, Eager Execution
	- 사용 시
		- 개발 단계(모델 구현 및 디버깅): 즉시 실행(Eager; 기본설정)을 이용하여 개발
		- 학습 단계: 한 스텝당 훈련 로직 함수에 `@tf.function`데코레이터를 사용
			```Python
				@tf.function 
				def train_step(images, labels): with tf.GradientTape() as tape:
					predictions = model(images, training=True)
					loss = loss_object(labels, predictions)
					gradients = tape.gradient(loss, model.trainable_variables)
					optimizer.apply_gradients(zip(gradients, model.trainable_variables))
					return loss
			```
	- **즉시 실행 (Eager):**
		- MatMul → Add → ReLU 연산을 수행할 때, 각각의 연산마다 GPU 커널(Kernel)을 호출하고 메모리에 쓰고 읽는 과정을 반복하며, 이는 메모리 대역폭(Memory Bandwidth)이 낭비되는 요인
		- 연산 하나가 끝날 때마다 제어권이 Python 인터프리터로 돌아옴. Python은 인터프리터 언어 특성상 속도가 느리고, GIL(Global Interpreter Lock)로 인해 멀티스레딩 효율이 떨어짐. GPU는 연산이 매우 빠른데, Python이 다음 명령을 줄 때까지 기다리는 GPU 유휴 시간(Idle time)이 발생
	- **그래프 실행 (Graph):** 
		- 컴파일러가 전체 그래프를 분석하여, 가능한 연산들을 하나로 합침. 예를 들어 MatMul + Add + ReLU를 하나의 커널로 융합(Fusion)하여 실행
		- 그래프가 생성되면 전체 계산 로직이 C++ 런타임으로 전달됨. Python 인터프리터를 거치지 않고 C++ 레벨에서 연속적으로 연산을 수행하므로, GPU에 끊김 없이 명령을 공급할 수 있음.
		- https://www.tensorflow.org/guide/intro_to_graphs?hl=ko
	- ⭐`tf.saved_model.save()` → 그래프 형태로 변환 후 저장
	

- `tf.Variable`: 가중치에 대한 상태를 저장하는 객체
	- Mutable: 텐서는 기본적으로 immutable이지만, Variable은 내부의 값이 변경 가능
	- Device Placement: `tf.Variable`은 선언되는 순간, GPU메모리(VRAM)에 할당될 수 있음
	- keras의 `Layer` 나 `Model` 클래스는 이러한 변수 추적을 자동으로 수행
	-  build() 메서드와 Variable Management:
	    - 입력 데이터의 크기가 확정된 시점에 tf.Variable을 생성하여 GPU 메모리에 공간을 할당하고, 레이어가 이 변수를 추적하도록 등록하는 과정입니다.