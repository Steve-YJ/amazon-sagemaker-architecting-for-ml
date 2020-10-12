# [Intermediate,Personalize] 영화 추천 엔진 (+ 에어라인 추천 엔진)

---
## 소개
개인화 서비스의 추천 엔진을 만드는 것을 이 과제에서 진행 합니다. 사용하는 데이터는 Movielens 데이터를 기본으로 사용을 하고, 확장으로서 에어라인 리뷰 데이터를 사용 합니다.<br>
알고리즘으로서 팩토라이제이션 머신(Factorization Machines) 및 KNN(K-Nearest Neighbor)를 사용합니다. 추천 엔진을 만드신 이후에는 Amazon Personalize 의 관리형 서비스로 확장을 하셔서, 빠르고 성능 좋게 서비스 구현을 해보실 수 있습니다.

## 비즈니스 사례 
영화는 많은 세계 사람들에게 사랑 받고 있습니다.오늘 당신은 네플릭스의 데이터 사이언스팀에서 일하게 되었습니다. 당신의 목표는 각 유저에게 가장 적당한 영화를 추천 하는 겁니다.

## 머신 러닝 방법
이 프로젝트에서 여러분은 팩토라이제이션 머신(Factorization Machines)을 배우실겁니다.
2010년에 Steffen Rendel이 만든 상당히 확장 가능한 알고리즘 입니다.
이 알고리즘은 테러바이트 규모의 아주 큰 데이터 셋도 선형 속도 (Linear time)의 증가로 학습을 할 수 있습니다.

![alt text](Images/recommender_1.png ) 

기본적으로 팩토라이제이션 머신 모델은 유저와 아이템간의 내적(dot product)을 계산 합니다. 
이 정보를 바탕으로 모델을 업데이트 하게 됩니다.

![alt text](Images/recommender_2.png ) 

이 작업을 완료하기 위해서 여러분은 데이타를 이벤트 형태로 재구성 합니다. 
고객이 각 아이템(예: 영화)과 상호작용(예: 등급 매기기)을 했을때에, 여러분이 만드는 가장 최종 데이터 셋의 형태는 이와 같습니다. 각 라인은(Row) 하나의 단일 내용으로 나타내는 것이 필요 합니다 (To be a single point in time). 각각의 컬럼은 아이템, 유저 혹은 다른 피쳐의 이진 지시자(binary indicator)인 0 혹은 1이 될 것 입니다. 


## 데이터 셋 설명
- 우리는 Movie Lens데이타를 적극 권장 합니다. 이는 추천의 비즈니스 사례에 아주 적합합 데이터 이고, 공용 데이터 소스로서 사용할 수 있습니다.
- [역자 추가] 추가적인 데이터 세트로서 에러라인 추천 데이타를 활용 합니다.
    - 에어라인 리뷰 데이터
        - 데이터는 비행기 에어라인의 리뷰에 대한 데이타 셋 입니다. <br>
        평가에 대한 데이터가 안락함, 청결, 음료, 음식, 화장실, 직원 서비스, 종합에 대한 평가가 있습니다. 여기서는 "종합"에 대한 평가 점수를 사용합니다.
            - Skytrax User Reviews Dataset (August 2nd, 2015)
            - https://github.com/quankiquanki/skytrax-reviews-dataset

## FM 이후의 확장 작업
여러분이 사용할 내장 알고리즘은 이진 분류 혹은 회귀 문제만을 처리할 수 있게 되어 있습니다.
KNN을 사용하여 다중 분류의 문제로 확장할 수 있습니다.<br>
(1) 먼저 FM을 사용을 하여 시작하시고, FM 모델의 가중치(Matrix)을 추출하여 KNN 모델을 만들어 보세요.<br>
(2) 아래 김대근님의 시작 코드는 Movielens 데이터를 사용하고 있습니다. <br>
(3) 에어라인 리뷰 데이터로 확장하기 위해서는 아래 역자의 시작 코드를 참고 하세요.<br>
## 시작 코드
- 김대근님의 Movielens 데이타를 통한 FM-KNN 노트북
    - https://github.com/daekeun-ml/recommendation-workshop/blob/master/0.Recommendation-System-FM-KNN.ipynb
- [역자 추가] Starter-Code-kr 의 Airline-Recommend-FM-KNN.ipynb 참조    
- architectng for ml의 공식 예제
    - https://github.com/aws-samples/amazon-sagemaker-architecting-for-ml/blob/master/Starter-Code/Recommendation-System-FM-KNN.ipynb

## 추가 확장
여러분이 이 과정들을 마친 후에, 관리형 추천 서비스인 Amazon Personalize 를 사용하여 솔류션을 확장을 해보세요. 참고할 샘플은 아래 git repo를 참고 하세요.
- Amazon Personalize 공식 샘플 코드
    - https://github.com/aws-samples/amazon-personalize-samples 
- 문곤수님의 퍼스널라이즈 워크샵
    - https://github.com/gonsoomoon-ml/personalize

## 레퍼런스

* In Python: https://www.datacamp.com/community/tutorials/recommender-systems-python
* Intuitively: https://www.analyticsvidhya.com/blog/2018/01/factorization-machines/ 
* Formally: https://www.csie.ntu.edu.tw/~b97053/paper/Rendle2010FM.pdf 



