# Part3. Software에서의 Physical AI 개발

- 로봇 장비 없이 Physical AI 개발 흐름을 이해하고 개발 도구와 데이터로 시작하는 방법
- Physical AI는 데이터수집, 시뮬레이션, 학습, 최적화, 배포, 재학습의 전체 파이프라인을 설계하는 일

## Chapter 09. 행동 데이터 수집 및 가공
- 

## Chapter 10. 

## Chapter 11. 합성 데이터 생성
=> 합성 데이터는 실세계 데이터의 빈틈을 보완하는 것이다

- 합성 데이터 생성의 핵심은 실제 로봇 데이터가 부족한 지점을 파악하고, 그 빈틈을 시뮬레이션과 생성 모델, 인간 시범 증식으로 보완하는 것
- 좋은 합성 데이터는 단순히 다양한 데이터가 아니라 로봇이 실제로 배워야 할 자겅ㅂ 구조와 물리적 제약을 담아야 한다
- 합성 데이터는 아무 단계에나 무작정 섞어 넣는 것이 아니라, 모델이 배우려는 능력에 맞춰 배치되어야 한다.
- AI 학습 파이프라인에서 합성 데이터는 독립적으로 존재하는 데이터 묶음이 아니라, 사전학습, 사후학습, 추론으로 이어지는 모델 학습 구조 안에서 각 단계의 목적에 맞게 배치되어야 한다.
- 합성 데이터는 실제 로봇 데이터를 완전히 대체하는 것이 아닌 보완하는 것이다.(다양한 조건과 경험을 제공)

(논문)
- [MimicGen](https://arxiv.org/abs/2310.17596): 합성 데이터 생성 방식을 보여주는 연구
- [π0.5](https://arxiv.org/abs/2504.16054): 어떤 데이터로, 어떤 단계에서 학습시키는가

## Chapter 12. Fine-Tuning과 학습 전략
=> 학습 전략은 데이터와 목표 작업에 따라 달라진다

- "모델을 어느 정도까지 fine-tuning해야 하는가?"
- 한 가지 행동을 아주 잘하게 만드는 것은 imitation learning 방식
- 장면 다양성, 언어 조건, 다중 물체 문백이 들어가면 강한 백본을 가진 VLA를 fine-tuning
- target scene에 맞게 visual feature 자체를 다시 적응시키는 것이 중요

- imitation learning은 기본 행동 분포와 인간 시연의 문법을 빠르게 흡수하는 단계에 강점
- reinforcement learning은 정밀한 최적화, 강한 접촉, 장기적 보상, 희귀한 성공 전략 탐색이 필요한 구간에서 강점
- heterogeneous data(이종 데이터)의 이질성을 unified action space(통합 행동 공간)로 끌여들여 학습
  - 데이터의 생김새가 다르더라도 행동의 의미가 통할 수 있도록 만드는 것
- RL에서 divide-and-conquer는 단순히 더 잘게 쪼개면 되는 문제가 아니라, 학습 효율과 일반화 사이의 균형을 설계하는 문제

(논문)
- [OpenVLA](https://arxiv.org/abs/2406.09246): 97만 개의 real-world demonstration으로 사전학습된 generalist VLA 제시
- [π0.5](https://arxiv.org/abs/2504.16054): VLM 백본 위에 action expert를 덧대는 2단계 구조 사용
- [NaVILA](https://arxiv.org/abs/2412.04453): 이미지 기반 VLM을 내비게이션에 맞게 supervised fine-tuning하는 방식 채택
- [RDT-1B](https://arxiv.org/abs/2410.07864)
- [Sim-to-Real Reinforcement Learning for Vision-Based Dexterous Manipulation on Humanoids](https://arxiv.org/abs/2502.20396): 강화학습이 실제 로못으로 옮겨가기 위해서는 정교한 엔지니어링 레시피가 필요함을 보여줌
- [π∗0.6](https://arxiv.org/abs/2511.14759): RECAP 방법을 통해, VLA가 실제 배포 경험과 사람의 수정 데이터를 바탕으로 개선되는 구조 제시

## Chapter 13. 온디바이스 AI 설계 전략
=> 실제 로봇에서는 성능만큼 속도와 비용이 중요

- on-device와 edge 환경
- quantization, pruning, distillation, PEFT(parameter-efficient fine-tuning)
- Physical AI에서는 모델 크기를 줄이는 것과 실제 제어 루프 안에서 어떻게 동작하는지까지 함께 설계

- 작은 backbone 선택후, 제어에 맞는 구조로 분리해서 설계하는 전략
- on-device에 올리려 할 때, 모델이 똑똑한지보다 한 번의 제어 주기 안에 추론이 끝나는지가 더 중요
- on-device 설계시 가장 먼저 봐야할 지표는 모델 파라미터 수보다 latency, throughput, 그리고 속도에서 성공률이 유지되는지이다
- 기존 VLA가 느린 이유는 '7B 이상의 큰 비전-언어 모델에 의존'과 '행동을 autoregressive 방식으로 한 차원씩 순차 생성하는 점'
- 지각과 언어 이해를 담당하는 백본과 실제 제어 출력을 담당하는 헤드를 분리해 각 부분을 속도 친화적으로 다시 설계
- 작고 빠른 멀티모달 백본을 선택하고, 연속 제어에 맞는 별도 행동 출력을 붙이며, 양자화도 단순한 메모리 절약 관점이 아니라 제어 루프 속도 관점에서 선택해야 한다.

- 액션 생성 구조 자체를 autogressive 방식에서 벗어나 제어 친화적으로 바꾸는 전략
- 성능과 실시간성을 동시에 높이려면 autoregressive 액션 생성보다 parallel decoing으로 넘어가야 하며, 여기에 action chunking, continuous action, L1 regressionㅇ을 경합해야 한다
- causal mask를 없애고 bidirectional attention 구조로 변환 -> 한 번의 forward pass만으로 전체 행동 시퀀스를 생성
- continuous action 표현 사용으로 실제 제어값 자체를 바로 예측하게 하는 방식

(on-device Physical AI 설계 핵심)
1) 제한된 하드웨어에 맞는 backbone 선택
2) 행동 출력 구조를 제어 친화적으로 설계
3) 양자화와 경량화도 단순한 모델 압축이 아니라 실제 로봇의 제어 루프 안에서 평가해야 한다

(논문)
- [TinyVLA](https://arxiv.org/abs/2409.12514)
- [OpenVLA-OFT](https://arxiv.org/abs/2502.19645): OpenVLA 기반으로 어떤 fine-tuning 방법이 실제 배포에 유리한지
- []()

## Chapter 14. 

## Chapter 15. 

## Chapter 16. 

## Chapter 17. 
