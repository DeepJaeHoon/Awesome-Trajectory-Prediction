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
+ 차선의 경우 여러 제어점(control point)이 있는 Spline으로 표현 가능하고 정지선은 단일 점(Single Point), 횡단보도는 Polyline으로 표현가능하듯이 HD Map은 기하하적인 표현이 가능하다.
+ 또한, 객체의 동역학적인 움직임도 PolyLine으로 근사한(approximated) 방식으로 표현가능하다.
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

HD Map의 대부분의 요소들은 Spline(차선), Closed Shape(교차로), Point(신호등, 표지판)으로 표현할 수 있다. 
또한, 현재 상황에대한 속성값(차선에 설정된 속도제한, 신호등 정보)도 가지고 있다.
주행 객체의 경우에는 시간에따라 방향을 가지는 Spline으로 표현할 수 있다.
즉, 위의 표현들을 vector의 Sequence로 표현할 수 있다. 


HD Map의 경우에는 시작점과 방향을 정한후에 Spline(차선)에서 등간격으로 정보를 가져와(Sampling) 인접한 Spline끼리 Vector를 연결한다.
주행 객체인 Agent의 경우에는 t = 0 sec에서 시작해서 특정 Time Step마다 Spline에서 정보를 가져와서(Sampling) t = 0부터 vecotor를 연결해준다.
이때, Sampling 간격이 작을수록 더 정확하게 근사가 가능하다.


![수식1](https://github.com/DeepJaeHoon/Awesome-Trajectory-Prediction/assets/174041317/3c675e16-0952-43e9-9a56-b8cde0e145d6)


이러한 방식은 vecotor화 표현이 순서가 정렬되지 않더라도, 연속적인 주행 경로와 HD Map 요소를 one-to-one mapping(일대일 함수)을 해준다. 
각 vector V는 Poly Line에 속하며, Graph Node의 역할을 한다. 

위의 수식 V는 vector의 시작점과 끝점, 속성 정보(속도 제한, 신호 정보, 시간 정보, 차량 정보), Integer ID로 구성된다.
Integer ID는 어떠한 PolyLine에 속해있는지 나타내는 지표이다. 위의 정보들이 Graph Node Feature의 역할을 한다.

예시로 HD Map의 경우에, 1번 index를 가지는 차선안에 10개의 중앙선 정보가 있고 3번, 4번 index를 가지는 차선과 서로 차선 변경이 가능하다고 하자.
1번 index의 첫 번째 중앙선 좌표부터 끝 지점까지 vecotor화 표현과 Graph edge를 연결하고, 3번과 4번 중앙선에도 인접하니깐 Graph edge를 연결한다.

Agent의 경우에는 10hz 단위로 1초의 주행 기록이 있다면, 총 10개의 주행 좌표가 있는 것이다.
동일한 Agent에 대해서 t = 0부터 시작해서 t = 0과 t = 0.1끼리,  t = 0.1과 t = 0.2끼리,...., t = 0.9와 t = 1끼리 vector화해서 시간의 흐름 방향으로 Graph edge를 연결한다.

Poly Line의 ID는 각 차선의 ID 또는 Agent의 고유 식별 ID가 될 것이고, V는 각 Polyline안에 있는 정보들을 vector화 표현한 것이다.

예측하고자하는 주행 객체의 위치에따라 Node Feature의 변화를 막기위해서(Position Invariant 성질을 위해서) 모든 vector를 주행 객체의 마지막 관측 시점에서의 좌표계로 정규화한다. 

---

### 7. PolyLine Sub Graph 설계하기

spatial, semantic locality를 가지는 Node에 대해서 feature extraction을 위해, Vector level에서 동일한 PolyLine에 속하는 Vector들은 서로 연결돼있다고 가정한 계층적 구조를 가지는 Sub Graph 방식을 택한다.



![수식2](https://github.com/DeepJaeHoon/Awesome-Trajectory-Prediction/assets/174041317/edc062e9-935a-4e7a-9bd2-90328a49ab9c)

SubGraph의 순전파 방식을 위의 수식과 같이 정의한다.


 
