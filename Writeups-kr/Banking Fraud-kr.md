# 은행거래 사기

미국에서는 매년 수십억건의 은행 거래가 발생합니다. 이 중 매우 소수가 사기와 관리되지만 만약 그 거래가 잘 탐지되지 않았을 때 피해는 매우 큽니다. 피해 비용은 고객과 기업의 손실, 그리고 거래의 유효성을 확인해야 하는 금융 규제기관에 이르기까지 막대합니다. 


## 사기 탐지
오늘 우리의 작업은 자동으로 사기 거래를 탐지하는 시스템을 설계하는 것입니다. 제공된 레이블 데이터를 사용하여 다양한 접근을 시도해 보십시오. 문제의 정의는 정확한 분류를 위한 모델이 되거나 가장 의심스러운 거래를 찾아내는 모델이 되거나 또는 이런 모델들의 조합일 수도 있습니다. 

## 사용할 데이터
Kaggle 계정을 생성하고 아래 링크를 통해서 데이터를 다운로드하십시오. 
* https://www.kaggle.com/ntnu-testimon/paysim1 

11개의 컬럼으로 6백만건의 레코드를 가지는 192MB의 데이터입니다. 이 데이터셋은 금융 응용프로그램의 연구를 돕기위해 설계된 시뮬레이션입니다. 각 레코드는 하나의 거래를 의미하며 입금(cash in), 출금(cash out), 직불(debit), 신용(credit), 또는 송금(transfer)을 나타냅니다. 각 거래는 금액과 거래주체(name of origin) 등을 가집니다. 여러분은 2개의 목표 컬럼을 가집니다. 하나는 사기여부를 나타내는 isFraud이며 수작업으로 레이블된 값입니다. 다른 하나는 룰 기반으로 세팅된 값이며 200,000 이상의 값을 가지는 거래를 표시합니다. 

두 컬럼을 모두 살펴보십시오. 대부분의 금융회사에서 룰기반 시스템을 운용하고 있으며, 복잡하고 거대한 룰엔진 시스템으로부터 머신러닝 시스템의 사용을 검토하는 것은 매우 일반적입니다. 

![alt text](Images/rcf_datahead.png "rcf_datahead")

## Classifiers
### 분류하기

데이터는 하나의 파일로 제공됩니다. 여러분은 이 파일을 학습셋, 테스트셋, 검증셋으로 나누어야 할 것입니다. 모델을 최적화 할 때 검증 세트를 이용하시고, 모델에 대한 최종 보고서를 작성해야 할 경우 테스트셋을 별도로 분리하십시오. 

최초접근으로 SageMaker의 빌트인 알고리즘인 XGBoost를 권장합니다.

## Feature Engineering

Feature engineering을 위해 다음 단계를 고려하십시오. 
- 원핫인코딩(one hot encoding)을 통하여 명목형(categorical) 문자 변수를 모델에서 사용할 수 있는 형태로 변형하십시오.
- sci kit learn의 min-max scaler를 이용하여 숫자형 컬럼의 스케일을 조정하십시오.
- 시간 단계(timestep)가 중요한가요? 의미있는 정보를 가지고 있나요? 해당 값이나 그것의 곱이 목표 변수와 관련이 있나요?

## 솔루션 확장하기 (고급)

XGBoost로 어느 정도 의미있는 결과를 도출했다면, 이를 확장하여 다른 모델을 만들어 봅니다. Linear Learner와 KNN을 이용한 분류를 적용해 보십시오. 

아래의 참조코드를 이용하여 여러 모델을 병렬로 학습해 보십시오. 멀티쓰레드 방식으로 실행하면 다음 실험을 시작하기 전에 이전 실험이 끝나기를 기다릴 필요가 없습니다.

결과를 분석할 때 각 모델에 대하여 두 개의 엔드포인트를 생성하고 모두에게 데이터를 보내 보십시오. 실행결과를 취합한 후 최대 예측값을 찾아보고 recall을 구해봅니다. XGBoost로 실행한 처음 시도에 비해서 결과가 좋아졌나요?


## 참조 코드
- 다음 XGBoost 예제를 참고하십시오. --> https://github.com/mullue/xgboost  
    - 0.xgboost simple - XGBoost를 이용한 모델개발의 기본 코드를 이해하는 용도입니다.
    - 1.xgboost direct marketing - SageMaker를 처음 실행하시는 분들을 위한 참조코드입니다.
    - 2.multiprocess ensembler - 앙상블을 이용하는 심화 예제입니다.
- 위 예제코드는 다음 코드에 대한 한글 번역입니다. 
    - https://github.com/awslabs/amazon-sagemaker-examples/blob/master/introduction_to_applying_machine_learning/xgboost_customer_churn/xgboost_customer_churn.ipynb 
    - [Starter-Code/Multithreaded_Ensemble_Modeling.ipynb](Starter-Code/Multithreaded_Ensemble_Modeling.ipynb)

# References

* 시뮬레이션 데이터셋에 대해 설명 Paper 
    * https://www.researchgate.net/publication/313138956_PAYSIM_A_FINANCIAL_MOBILE_MONEY_SIMULATOR_FOR_FRAUD_DETECTION/download 

