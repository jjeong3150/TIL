# You Only Look Once: Unified, Real-Time Object Detection (YOLO v1)

> Yolo v1에 대한 학습을 시작하려고 한다. 첫 논문으로는 어려운 주제일 수도 있지만, 끝까지 해낼 수 있다면 크게 성장할 수 있지 않을까? 이를 시작으로 Real-time Object Detection을 위한 논문 및 기술을 지속적으로 탐구해볼 생각이다. 어렵겠지만 끝까지 해내고 싶다. 
> 
> 단순히 참고용으로 만들 생각이기 때문에 아주 자세히는 쓰지 않을 것 같다. 단순 요약 및 강조하여 정리할 생각이다. 필요시 자세히 정리할 수 있다. 만약 내 능력이 되고, 여유가 된다면 자세하게 정리한 논문 리뷰를 작성하고싶다. 부디 끝까지 무사히 마무리 할 수 있기를... (2022.11.25)

## Abstract

-  localization과 classification을 하나의 문제로 정의하여 network가 동시에 두 task를 수행하도록 설계됨

- Single neural network

- end-to-end DL, Pipeline이 single network이기 때문에 한번의 평가로 전체 이미지에서 직접 Bounding box와 class 의 확률을 예측함.



## 1. Introduction

- 인간은 휙 훑어보는 것만으로 사물의 크기, 종류, 움직임을 파악할 수 있음. 또한 운전과 같은 복잡한 일도 가능

- 정확하고 빠른 Algorithm은 real-time 시각 정보를 특별한 센서나 보조장치 없이 처리할 수 있도록 할 것임

- sliding window 방식이 쓰이는 DPM(이미지의 모든 부분을 sliding 하기 때문에 연산량이 많을듯?)

- region proposal methods를 사용한 R-CNN은, 잠재적인 bounding box를 생성 후, 후처리를 통해 bounding box를 세분화하고 중복을 제거하며 객체를 기반으로 bounding box의 score를 매김

- 그러나 위의 방법은 매우 느리고 어려움. 각 개별 구성요소를 개별적으로 train 해야하기 때문에 느리고 최적화하기 힘듦
  
  
  ### several benefits

- YOLO는 복잡한 파이프라인이 필요없고, 별도의 batch processing 없이 45fps를 달성했음(Titan X GPU)

- YOLO는 매우 빠름. fast version의 YOLO는 150fps로 비디오와 같은 real-time 정보를 처리할 수 있음. 그래서 다른 real-time system과 비교하여 두배 이상의 정확도를 달성했음

- YOLO는 다른 Object detection 시스템과 달리 이미지 전체를 training과 test에 씀. 그래서 Fast R-CNN보다 더 작은 background error를 발생시킴

- 자연 이미지 뿐만아니라 예술작품에도 적용할 수 있는 등 넓은 범위의 일반화 가능. 새로운 사물, 입력이 들어와도 일반화 능력 탁월

- 하지만 여전히 최신 detection system과 비교했을 때 정확도면에서는 뒤떨어짐. 특히 작은 사물을 탐지할 때 단점이 도드라짐. 이러한 tradeoff 는 논문을 통해 설명하겠음



## 2. Unified Detection





각 그리드 셀은 B 경계 상자와 해당 상자에 대한 신뢰도 점수를 예측합니다. 이러한 신뢰도 점수는 모델이 상자에 개체가 포함되어 있다고 확신하는 정도와 상자가 예측하는 것이 얼마나 정확하다고 생각하는지를 반영합니다. 공식적으로 신뢰도를 Pr(Object) * IOU로 정의합니다. 해당 셀에 개체가 없으면 신뢰도 점수는 0이어야 합니다. 그렇지 않으면 우리는 신뢰도 점수가 예측된 상자와 ground truth 사이의 IOU(교차로 합집합)와 같기를 원합니다. 
