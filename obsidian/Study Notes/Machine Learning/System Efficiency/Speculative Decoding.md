---
title: "Speculative Decoding"
date: 2025-12-01 09:07:53
categories: []
tags: []
keywords: []
---
25-11-28 초안작성

# 추론 속도 향상 기법
---
## 추측 디코딩(Speculative Decoding)
- 구글 리서치 (https://research.google/blog/looking-back-at-speculative-decoding/)
- 엔비디어 디벨로퍼 (https://developer.nvidia.com/ko-kr/blog/an-introduction-to-speculative-decoding-for-reducing-latency-in-ai-inference/)
- 블로그 논문 리뷰 (https://velog.io/@2mini/paper-review-Unlocking-Efficiency-in-Large-Language-Model-Inference-A-Comprehensive-Survey-of-Speculative-Decoding)
- 내용 요약 (https://developer.nvidia.com/ko-kr/blog/an-introduction-to-speculative-decoding-for-reducing-latency-in-ai-inference/)
	- 개요: 자기회귀 방식은 토큰을 하나씩 순차적으로 생성 → 매 토큰마다 전체 모델을 다시 실행, 가중치 로딩 등 비효율적임.
	- Speculative Decoding은 추론 성능을 최적화하기 위한 기법.
		- 여러 토큰을 한번에 미리 예측하고 검증하는 방식
		- 고성능 모델에 가벼운 드래프트 메커니즘을 결합
			- 드래프트 모델(경량 모델)이 여러개의 다음 토큰들을 빠르게 제안하면, 대상 모델은 한번의 포워드 패스로 검증
			- 여러개의 토큰을 제안한 뒤, 고성능 모델은 일부 접두어(prefix)를 수용하고 그 이후에 생성을 이어감
			- 마치 연구소의 수석 과학자가 조수에게 반복적인 실험을 맡기고 자신은 검증 및 방향설정에 집중하는 형태와 유사
	- Draft-Target 방식
		- speculative decoding의 전통적인 구현, 두 개의 모델이 함께 작동하는 방식
		- 동일한 데이터 분포로 학습된 소형 모델이, 초안(draft)를 생성. 소형 모델을 ‘드래프트 모델’이라고 표현![[speculative-decoding-draft-target-approach-0186.jpg]]
		- `드래프트 생성` → `병렬 검증` → `거절 샘플링` 단계를 거침
			1. 드래프트 생성
				- 소형 모델(=드래프트 모델)이 여러 개의 후보 토큰(3~12개)을 생성
				- 드래프트 모델은 고성능 모델(Target Model)의 출력을 정답(ground truth)으로 취급
			2. 병렬 검증
				- 타겟 모델은 ‘입력 시퀀스’ + ‘드래프트 토큰들’을 한번의 포워드 패스로 동시에 처리하며, 각 위치에 대한 확률 분포를 계산
				- KV 캐시를 이용하므로, 이전 토큰들(prefix)의 Value는 이미 계산되어 저장되어 있으므로, 새로 제안된 토큰의 계산비용만 발생
			3. 거절 샘플링: 
			- ![[speculative-decoding-verification-phase-target-model.jpeg]]
			- 거절 샘플링은 타겟 모델이 확률 분포를 계산한 이후에 진행되는 단계
			- 핵심은 수락 판단 로직(acceptance logic), 이는 드래프트 모델이 제안한 확률 P_draft와 타겟 모델이 실제로 계산한 확률 P_target을 비교함
			- 첫 두개의 토큰인 [Brown]과 [Fox]의 경우, P_target이 P_draft보다 높으므로 토큰이 수락됨
			- 하지만 [Hopped]는 P_target이 P_draft보다 현저하게 낮으므로 (타겟 모델이 확률이 낮다고 판단했으므로), 거절되며 이후의 토큰인 [Over]도 함께 폐기
				- 거절된 토큰 이후의 모든 드래프트 토큰 폐기
			- 모델은 마지막 수락된 토큰인 [Fox]부터 다시 표준 자기회귀 방식으로 생성을 이어감

- EAGLE 방식의 Speculative Decoding이란
- ![[Pasted image 20251201151828.png]]
	- EAGLE은 타겟 모델의 출력 헤드 직전의 hidden state로부터 feature를 추론하는 방식의 speculative decoding 기법
		- Draft-Target 방식이 별도의 드래프트 모델에 의존해 토큰을 제안하는 것과 차이
		- 두번째 모델을 훈련하거나 실행하는 오버헤드를 없애고 하나의 포워드 패스로 여러개의 토큰 후보 검증 가능
		- EAGLE-3는 낮은 - 중간 - 높은 수준의 임베딩을 드래프트 헤드에 직접 전달하고, 여러 예측 후보들을 제안함
	- EAGLE 헤드는, 별도의 소형모델을 사용하는 대신, 모델 내부에 경량 레이 블록을 구축

- 그 밖에, DeepSeek-R1의 Multi-Token Prediction
	- MTP는 DeepSeek에서 사용되는 speculative decoding 기법
	- 다음 토큰 하나만 예측하는 것이 아니라, 여러개의 미래 토큰을 한번에 예측하도록 모델을 학습시키는 방식
		- 첫번째 헤드는 첫번째 드래프트 토큰
		- 두번째 헤드는 그 다음, 세번째 헤드는 또 그 다음 토큰 예측..
		- 메인(타겟) 모델은 이 제안들을 순서대로 검증
		- 메인 모델이 자신의 예측과 가장 길게 일치하는 토큰(prefix)까지만 수용
	- MTP는 멀티 토큰 예측에 특화된 여러 헤드를 사용, EAGLE은 하나의 헤드가 내부 feature 상태를 기반으로 후보를 구성
