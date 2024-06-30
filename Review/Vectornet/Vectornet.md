# VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation

---

 ### 1. 무엇을 위한 연구인가?

 + 복잡한 교통환경에서 교통 참여자인 객체(사람, 차량, 대중교통)에대해 경로(주행 의도)를 예측하고자한다.
 + Object Detection, Tracking과 같은 방식으로 수집한 객체에대한 동역학적인 정보(위치, 속도)와 사전 지식으로 주어진 HD Map에대하여 정보를 통합해서 표현하는 법을 찾고자한다.

---

### 2. 기존 연구는 무엇이 문제였는가?
![multimodal_4](https://github.com/DeepJaeHoon/Awesome-Trajectory-Prediction/assets/174041317/8546a447-aed6-4093-bcdf-8744d97fdb8d)


+ 기존의 딥러닝 기반 모델들은([[Paper]](https://proceedings.mlr.press/v87/casas18a.html), [[Paper]](https://ieeexplore.ieee.org/abstract/document/8793868)) 예측한 경로에대해 확률적인 해석을 제공하기위해서, 주행 궤적과 HD Map을 Encoding 구조를 설계해야한다.
+ 하지만, 차선과 신호등같이 조직화된 구조를 가진 HD Map에 대해서 임의로 색상을 부여하는 BEV 관점의 Resterized Image로 표현하여 제한된 수용능력(Receptive Field)를 가지는 CNN을 사용하는 방식이었다.
---
### 3. 기존 연구의 문제를 어떻게 해결하는가?

![vectorized](https://github.com/DeepJaeHoon/Awesome-Trajectory-Prediction/assets/174041317/f9fae4b0-09a3-4bb2-8ac2-78c08c0f4ddd)

+ 기존의 BEV 관점의 Resterized 방식에서 Vectorized 표현으로 바꾼다.
+ 차선의 경우 여러 제어점(control point)이 있는 Spline으로 표현 가능하고 정지선은 단일 점(Single Point), 횡단보도는 Poly line으로 표현가능하듯이 HD Map은 기하하적인 표현이 가능하다.
+ 또한, 객체의 동역학적인 움직임도 Poly Line으로 근사한(approximated) 방식으로 표현가능하다.
+ 도로 상황을 위의 방식으로 Vector로 표현한 후, 각 Vector를 Node로 취급하여 GNN(Graph Neural Network)를 적용하는 방식이다.
---
### 4. Contribution이 무엇인가?

+ HD Map과 주행 객체의 동역학적 정보를 Vecotorized 표현으로 통합하여 경로를 예측하는 첫 방식이다.
+ 계층적 그래프 모델인 Vectornet과 node completion auxiliary task을 제안한다.
+ Model size를 70%를 줄였음에도 성능을 유지하거나 오히려 더 좋은 성능을 낼 수 있다.

Auxiliary Task란 본 task는 아니지만, 본 task에서의 성능이 더 잘 나올 수 있도록 도와주는 보조 task를 의미한다.


Node Completion은 주변 Node의 관계와 특징을 활용하여 불완전한 정보를 가진 node의 정보를 예측하거나 복원하는 작업이다.

---
### 5. 관련 연구

1. Behavior prediction for autonomous driving
2. Forecasting multi-agent interactions
3. Representation learning for sets of entities
4. Self-supervised context modeling
---
### 6. Vecotrized 방식으로 표현하는 법




