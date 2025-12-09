
- [[#개요|개요]]
- [[#차이점 비교: tf.data.Dataset 와 np.load()|차이점 비교: tf.data.Dataset 와 np.load()]]
	- [[#차이점 비교: tf.data.Dataset 와 np.load()#데이터 로딩방식|데이터 로딩방식]]
	- [[#차이점 비교: tf.data.Dataset 와 np.load()#구체적 예시|구체적 예시]]
- [[#tf.data.Dataset 객체의 주요 메서드|tf.data.Dataset 객체의 주요 메서드]]

## 개요
- 왜 해당 API 내용을 정리했는지?
	- 학습 데이터를 RAM에 로딩할 때, 기존에 사용하던 np.load() 방식을 이용하면 용량 문제가 발생함.
	- 텐서플로우 자체적으로 데이터를 다루는 Dataset 및 TFRecordDataset 클래스에서는 lazy-loading을 사용하고, prefetch(미리 불러오기) 등을 이용하므로 용량문제에서 자유로움.
- 기타
	- Dataset 클래스 형태로 API가 제공
	- tf.data.TFRecordDataset은 tf.data.Dataset을 상속받은 클래스 (그러나 둘다 Dataset 객체로 취급됨)


## 차이점 비교: tf.data.Dataset 와 np.load()
### 데이터 로딩방식
>결론: 수 GB 미만의 데이터셋은 tf.data.Dataset.from_tensor_slices()를 사용하는게 나음

**A. In-Memory 방식** (메모리 의존)
- np.load():
    - .npy 파일을 읽는 순간 데이터셋 전체가 RAM에 적재됨.
    - 한계: RAM 용량 부족 시 OS에 의해 프로세스 강제 종료 (OOM Error).
- tf.data.Dataset.from_tensor_slices():
    - np.load()와 동일하게 이미 메모리에 로드된 데이터를 파이프라인으로 감싸는 역할.
    - 주의: 데이터가 메모리에 존재해야 하므로 대용량 데이터 처리에는 부적합.

**B. Streaming 방식** (디스크 의존 - Lazy Loading)
- tf.data.Dataset.from_generator() (Python 기반):
    - 메커니즘: 파이썬 yield를 통해 데이터를 필요할 때만 읽어옴 (OOM 해결).
    - 병목(Bottleneck):
        1. Python GIL: 파이썬 인터프리터 실행으로 인한 오버헤드 및 멀티스레딩 제약.
        2. Random I/O: 개별 파일을 열고 닫는 과정에서 디스크 탐색 시간 소요.
- tf.data.TFRecordDataset (C++ 기반 - 권장):
    - 메커니즘: 직렬화된(Serialized) 바이너리 포맷(Protobuf)을 스트리밍.
    - 장점:
        1. C++ Runtime: 파이썬 인터프리터를 거치지 않고 텐서플로우 C++ 엔진이 직접 고속 처리.
        2. Sequential I/O: 대용량 파일을 순차적으로 읽으므로 디스크 처리량(Throughput) 극대화.
        3. Prefetching: GPU 학습 중에 CPU가 데이터를 미리 준비하는 병렬 처리에 최적화.


### 구체적 예시
- 요약
	- .from_tensor_slices() 메서드를 사용하려면 np.load()가 선행되어야함
	- .from_generator() 메서드를 사용하려면 제너레이터 함수를 먼저 작성
	- TFRecordDataset API를 활용하려면 데이터가 미리 TFRecord 포맷으로 변환되어 있어야 함

1. tf.data.Dataset.from_tensor_slices()
	```Python
			data = np.load("data.npy")
			dataset = tf.data.Dataset.from_tensor_slices(data)
	```
	- 넘파이 배열이나 텐서를 메모리에 로드한 후 사용
	- 작은~중간 크기 데이터에 적합
	- np.load()가 선행되어야 하며, 전체 데이터를 메모리에 로드
	
2. tf.data.Dataset.from_generator()
	```Python
		def data_generator():
				for i in range(num_samples):
						# 필요할 때마다 데이터 로드
						data = np.load(f"sample_{i}.npy")
						yield data
		
		dataset = tf.data.Dataset.from_generator(
				data_generator,
				output_signature=tf.TensorSpec(shape=(None, 224, 224, 3), dtype=tf.float32))
	```
	- 대용량 데이터를 배치 단위로 메모리에 로드
	- 제너레이터 함수(yield)로 필요한 만큼만 로드 (메모리 효율적)


3. tf.data.TFRecordDataset
	```Python
		dataset = tf.data.TFRecordDataset(["data.tfrecord"])
		dataset = dataset.map(parse_tfrecord_fn)
	```
	- 매우 큰 데이터셋에 최적화
	- 데이터를 TFRecord 포맷으로 미리 변환해야 함
	- 가장 빠른 I/O 성능

## tf.data.Dataset 객체의 주요 메서드
1. .batch(batch_size)
	- 데이터를 배치 단위로 묶음
	- 굳이 사용하지 않더라도, model.fit() 단계에서 `batch_size=32` 등의 인자로 줄 수 있음
	```Python
		dataset = dataset.batch(32)  # 32개씩 묶음
	```


2. .shuffle(buffer_size)
	- 데이터를 랜덤하게 섞음 (학습 시 필수)
	```Python
		dataset = dataset.shuffle(buffer_size=1000)  # 1000개 샘플을 버퍼에 로드해 섞음
	```

2. .map(function)
	- 각 샘플에 전처리 함수 적용
	```Python
		def normalize(x, y):
		    x = x / 255.0  # 정규화
		    return x, y
		    
		dataset = dataset.map(normalize)
	```


3. .prefetch(buffer_size)
	- 다음 배치를 미리 로드 (성능 향상)
	```Python
		dataset = dataset.prefetch(tf.data.AUTOTUNE)  # 자동으로 최적 버퍼 크기 결정
	```

4. .cache()
	- 데이터를 메모리 또는 디스크에 캐싱 (반복 학습 시 속도 향상)
	```Python
		dataset = dataset.cache()  # 메모리에 캐싱
		dataset = dataset.cache("/path/to/cache")  # 디스크에 캐싱
	```


5. .element_spec
	- 각 요소의 shape와 dtype을 보여주는 메서드
		``` Python
			dataset = tf.data.Dataset.from_tensor_slices(
					(cleaned_mac_xs_voxel_4d_data, cleaned_boc_reactivity_data, cleaned_cycle_length_data)
			)
			dataset.element_spec
			# 결과 
			# TensorSpec(shape=(24, 5, 5, 10), dtype=tf.float64, name=None),
			# TensorSpec(shape=(), dtype=tf.float64, name=None),
			# TensorSpec(shape=(), dtype=tf.float64, name=None))
		```