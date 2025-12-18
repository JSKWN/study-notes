# 개요 및 진행계획
- 개요: 기존 모델 학습 스크립트의 경우는 한개의 주피터노트북 파일에 경로, 데이터로딩, 모델학습 및 MLflow가 적용되어 있는 형태
	- 각 역할을 모듈(.py)파일로 분할하고 주석, docstring 잘 기록해 놓을것
	- 각종 경로 및 모델 학습 설정 등, yaml 파일로 기록
- 진행계획
	- 폴더 및 파일 구조 계획
		- 경로 및 환경설정: env_config.yaml
		- (폴더) data
			- data_loader.py
		- (폴더) model
			- model.py
			- (폴더) network
				- modules.py
				- aaa_attention.py
				- bbb_conv_block.py
			- model_config.py
				- 어텐션헤드 등의 구조마다 config를 입력받도록 구현
				- 각 모델 블록은 (1) 개별 블록 별 설정, (2) 전역 설정 등을 받도록 구현
				- model_config.py 파일 내부는 다음과 같이 구성 예정(지금은 간단하게 구현)
				 - ``` Python
							# model_config.py
							import dataclasses
							from typing import Optional
							
							# 1. 가장 작은 단위 (Sub-module) 설정
							@dataclasses.dataclass
							class AttentionConfig:
									num_heads: int = 8
									key_dim: int = 64
									value_dim: int = 64
									dropout_rate: float = 0.1
							
							@dataclasses.dataclass
							class EvoformerConfig:
									num_blocks: int = 48
									msa_row_attention: AttentionConfig = dataclasses.field(default_factory=AttentionConfig)
									msa_column_attention: AttentionConfig = dataclasses.field(default_factory=AttentionConfig)
									# Evoformer 내에서 AttentionConfig를 재사용합니다.
							
							# 2. 전역 설정 (모든 모듈이 공통으로 알아야 할 것들)
							@dataclasses.dataclass
							class GlobalConfig:
									deterministic: bool = False
									subbatch_size: Optional[int] = None
									use_remat: bool = False  # Gradient Checkpointing 여부
							
							# 3. 전체 모델 설정 (Root Config)
							@dataclasses.dataclass
							class AlphaFoldConfig:
									evoformer: EvoformerConfig = dataclasses.field(default_factory=EvoformerConfig)
									global_config: GlobalConfig = dataclasses.field(default_factory=GlobalConfig)
									
									# 팩토리 메서드로 프리셋 제공
									@classmethod
									def model_1(cls):
											return cls(
													evoformer=EvoformerConfig(num_blocks=48),
													global_config=GlobalConfig(deterministic=True))
						```

	
			