# SageMaker Machine Learning을 이용한 예지정비

### 소개

여러분은 제조회사에서 조립라인을 모니터링해야하는 책임자입니다. 각 조립라인은 여러 종류의 기계로 이루어져 있으며 각 공정은 생산부터 출하까지 모두 연결되어 습니다. 공정상의 기계들에 부착된 IoT센서들은 전력사용량, 소음, 진동, 온도 등 여러 값들을 정량 측정하여 각 기계장치들을 모니터링하고 있습니다. 만약 이들 기계에서 고장이 발생하면 여러 공정라인은:

1. 공정에 다운타임이 발생하고 제품생산과 출하에 지연이 발생하게 됩니다.
1. 공정 지연에 따른 비용이 발생하며 고장을 일으킨 기계의 부품을 교체하는 수리를 위한 작업을 운영 엔지니어에게 의뢰해야 합니다.

당신은 이전부터 머신러닝을 이용하여 이런 문제를 풀 수 있을지에 대해서 평소 궁금해하고 있었고 *예지 정비* 기술의 조사 임무를 할당받았습니다. 최근 여러분의 경쟁사인 Advantech, Inc.에서 다음 보고서(http://www.advantech.com/industrial-automation/industry4.0/pms#my_cen)를 출시하였고 이 또한 임무의 배경이 되었습니다. 여러분 팀의 합동 연구 노트는 여러분이 작업하게 될 잠재적인 PoC(Proof-of-concept)에 대하여 다음과 같이 설명하고 있습니다.

## 대응적(Reactive), 에지적(Predictive), 또는 예방적(Preventive) 유지보수

많은 수의 기계가 관여하게 되는 제조나 에너지 회사에서 통상적인 유지보수 스케줄은 대응적(Reactive) 또는 예방적(Preventive)으로 진행합니다. 대응적 유지보수는 기계에서 장애 또는 운영성능 저하가 발생할 경우 스케줄됩니다. 예방적 유지보수는 정해진 기간 또는 정해진 사용량을 기반으로 실행됩니다. 예를 들어 자동차의 경우 대응적 유지보수는 부품에 문제가 있는 경우 (엔진이 정지하거나 타이어에 펑크가 있는 등) 발생하는 반면 예방적 유지보수는 꼭 문제가 있지 않더라도 (예를 들어 타이어의 교환이나 10000 마일당 오일을 교체하는 것와 같이) 정해진 기간별로 이루어집니다. 

예지적(Predictive) 유지보수는 미래에 시스템 장애가 발생할 가능성을 예측하여 유지보수 스케줄을 적용하는 것입니다. 이 방법은 대응적 또는 예방적 유지보수의 일반적인 문제점을 해결합니다. - 1. 대응적 유지보수는 장애가 발생한 이후에 유지보수 작업자가 할당되므로 프로젝트 일정에서 불필요한 시간과 비용이 발생하게 됩니다. 2. 예방적 유지보수는 불필요하게 많은 유지보수를 하여 사용자의 시간과 비용을 증가시키게 됩니다. 현재 예지적 유지보수 기술은 간단히 임계값을 모니터링 하거나 센서 데이터로부터 통계 기술을 이용하여 이상치를 식별하는 방식을 사용합니다. 하지만, 이런 기술은 소수의 기술 전문가에 의해 제한적으로 실행되게 되며 사람의 갭려적 판단에 의존하게 됩니다. 머신러닝을 이용하면 센서 데이터로부터 이상패턴을 탐지하는 모델을 학습할 수 있습니다. 학습된 머신러닝 모델은 프로그래밍 또는 룰기반의 임계값을 필요로하지 않으며 반복적으로 대량의 데이터를 분석하여 사람의 개입 없이 생성할 수 있습니다.

## NASA 터보팬 엔진 결함 데이터셋

배경

NASA의 Prognostic Center of Excellence는 예지정비 알고리즘과 예지적 유지보수를 벤치마킹을 하기 위한 위한 데이터셋의 저장소를 구축했습니다. 이들 데이터셋 중에는 터보팬 엔진 시뮬레이션 모델 C-MAPPS(Commercial Modular Aero Propulsion System Simulation)의 데이터가 포함됩니다. Reference 부분을 보면 이 데이터셋을 사용하는 100개가 넘는 출판을 확인할 수 있습니다. C-MAPPS는 시뮬레이션되는 터보팬 엔진으로부터 엔진파라미터와 상태정보를 생성하는 툴입니다. 사용자 정의 코드를 이용하여 인공적인 결함을 추가하였고 센서값의 시트림에 지속적으로 감소하는 트랜드를 반영하였습니다. 이 데이터에 대한 몇가지 특징은 다음과 같습니다.

1. 수집된 데이터는 터보팬 엔진의 시뮬레이션 결과이지만 실제 엔진가 유사한 매우 신뢰성있는 값입니다.
1. 실제상황을 보다 잘 모사하기 위해 인공적인 노이즈가 추가되었습니다.

다음 링크에서 데이터셋을 다운로드하십시오. - https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan.


### 더 자세히 살펴보기

데이터셋은 다변량 시계열데이터로 구성되어 있습니다. 각 데이터셋은 학습용 및 테스트용 데이터셋으로 나누어져 있습니다. 각 시계열은 서로다른 엔진으로부터 생성된 값이며, 엔진의 타입은 동일합니다. 각 엔진은 서로 다른 내구성과 변화율을 가지는 초기 상태에서 시작하며 시작 단계에서는 정상 상태입니다. 엔진은 세가지 중요한 세팅값을 가지며 이는 엔진 성능에 큰 영향을 줍니다. 이 세팅값은 데이터에 함께 포함되어 있습니다. 센서값은 노이즈가 함께 반영되어 있습니다.

각 시계열 데이터는 정상 상태로 시작하지만 시간이 지나면서 결함을 발생시킵니다. 학습데이터셋에서 결함은 계속 커져가고 시스템에 장애가 발생하면 시계열이 끝납니다. 테스트셋에서는 시스템이 실패하기 이전의 임의의 시점까지로 구성됩니다. 예측의 목표는 테스트셋에서 시스템이 실패하기까지 남아있는 운영 사이클을 구하는 것입니다. 곧, 엔진의 마지막 상태에서 얼마의 사이틀을 더 운영할 수 있는지 예측하는 것입니다. 그리고 실제 RUL(Remaining Useful Life, 잔존 수명) 값이 제공됩니다.

데이터는 압축된 zip파일로 제공되며 공백으로 구분된 26개의 숫자 컬럼을 가집니다. 각 row는 하나의 운영 사이클로부터 얻어진 스냅샷이며 각 컬럼은 다른 변량을 가집니다. 컬럼의 구성은 다음과 같습니다.  
1) unit number (일련번호)  
2) time, in cycles (시간, 사이클)  
3) operational setting 1 (운영세팅1)  
4) operational setting 2 (운영세팅2)  
5) operational setting 3 (운영세팅3)  
6) sensor measurement 1 (센서측정값1)  
7) sensor measurement 2 (센서측정값2)  
...  
26) sensor measurement 26 (센서측정값26)  

## 참조 코드 

본 문제를 회귀(regression) 문제로 접근할 것을 권장합니다. 다음 XGBoost 알고리즘을 사용하는 회귀분석 코드를 참고하십시오. 
- https://github.com/awslabs/amazon-sagemaker-examples/blob/master/introduction_to_amazon_algorithms/xgboost_abalone/xgboost_abalone.ipynb  

다음 예제코드는 Tensorflow와 LSTM을 이용하는 예제입니다. 
- https://github.com/mullue/predmaint/blob/master/SM-pred-present.ipynb

### 기타 다른 참조  

터보팬 엔진 데이터셋을 이용하는 예지정비 문제는 다음 머신러닝 문제중 하나로 다루어져 왔습니다. 

1. 분류 문제
    1. 엔진이 특정 시간구간(time window)내에 실패할 것인지 예측하기 (yes / no)
    1. 엔진이 특정 시간구간에 실패할지 여부를 예측하기 (예: windon 0, window 3, and window 4)
1. 회귀 문제
    1. 실패시간을 예측하기 (TTF, Time to Failure)
    1. 잔존수명을 예측하기 (RUL, Remaining Useful Life)

### 고려사항 

제공된 데이터는 예지정비용 ML 문제를 해결하기에 적합한 포맷으로 구성되어 있지 않습니다. 어떻게 데이터를 모델에 맞게 가공할 수 있을까요?

모든 특성(feature)가 중요한가요? 몇가지 특성이 결합되거나 합쳐질 수 있나요?

(분류문제로 접근한다면) 분류 항목(class)에 대한 분포가 균일한가요? 그렇지 않다면 어떻게 이 분포의 밸런스를 맞출 수 있을까요?

모델의 정확도를 어떻게 측정할까요? 모델을 사용하는 가상의 터보 팬 엔진 제조업체의 입장에서 혼동 행렬(confusion matrix)의 어떤 셀이 가장 큰 영향을 미치나요?


References
* https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/publications/#turbofan
* https://ntrs.nasa.gov/archive/nasa/casi.ntrs.nasa.gov/20150007677.pdf
* A. Saxena, K. Goebel, D. Simon, and N. Eklund, “Damage Propagation Modeling for Aircraft Engine Run-to-Failure Simulation”, in the Proceedings of the Ist International Conference on Prognostics and Health Management (PHM08), Denver CO, Oct 2008.
