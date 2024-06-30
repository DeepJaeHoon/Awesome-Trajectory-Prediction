# VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation

---

 ### 1. 무엇을 위한 연구인가?

 + 복잡한 교통환경에서 교통 참여자인 객체(사람, 차량, 대중교통)에대해 경로(주행 의도)를 예측하고자한다.
 + Object Detection, Tracking과 같은 방식으로 수집한 객체에대한 동역학적인 정보(위치, 속도)와 사전 지식으로 주어진 HD Map에대하여 정보를 통합해서 표현하는 법을 찾고자한다.

---

### 2. 기존 연구는 무엇이 문제였는가?
+ 기존의 딥러닝 기반 모델들은(  [[Paper]](https://proceedings.mlr.press/v87/casas18a.html), [[Paper]](https://ieeexplore.ieee.org/abstract/document/8793868)) 예측한 경로에대해 확률적인 해석을 제공하기위해서, 주행 궤적과 HD Map을 Encoding 구조를 설계해야한다.
+ 하지만, 차선과 신호등같이 조직화된 구조를 가진 HD Map에 대해서 임의로 색상을 부여하는 BEV 관점의 Resterized Image로 표현하여 CNN을 사용하는 방식이었다.
+ 
