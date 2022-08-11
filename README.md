# 선제적 열공급량 절감 파이프라인 구축
### Prophet, Soft Voting을 이용한 열공급량/내부 온도 예측 및 스마트팜 제어 변수 모델링

--- 

## 배경

<br>

4 차 산업혁명시대가 맞물려 전 세계적으로 농업에 큰 변화가 시작되었다. 많은 국가에서 정보통신기술을 적용하여 생산성과 효율성을 높이기 위해 다각적인 기술혁신을 동원하고 있고, 스마트팜은 그 대표적인 사례 중 하나이다. 
한국농촌진흥청에서는 ‘한국형 스마트팜’을 국내 농업의 조건과 현실에 맞게 최적화시켜 농업선진국과 경쟁할 수 있는 독자적인 농업생산기술을 확보하는 것을 목표로 하고 있다. 이에 스마트팜에서 소비되는 비용과 에너지를 절감 할 수 있는 ‘에너지자립형’ 스마트팜, ‘ESS 스마트팜 전용 히트펌프’, 완전 자동화된 ‘3 세대 스마트팜' 조성 등의 솔루션들이 떠오르고 있다.

주어진 스마트팜의 복합환경제어시스템은 실시간으로 데이터를 입력받아 설정온도를 맞추기 위해 특정 threshold 를 기반으로 내부 장치를 제어한다.
이러한 방식은 계산 복잡도를 줄여 실시간 데이터 처리에 대한 오버헤드를 발생시키지 않는 단순한 방식이지만, 복잡한 물리적인 특성과 제어 변수를 가진 시스템의 상호작용을 통해 작동하는 스마트팜을 제어하기에는 미흡하다. 변동성이 크고 종류가 다양한 상호관계가 얽혀있는 제어 변수들을 조작해야 한다는 점에서 더욱 그렇다. 특히 주요 제어 변수의 설정값이 실험과 경험에 의한 판단에 의존한다는 점과 시스템의 주요 작동 변수 변화에 대응하지 못하고 일정한 설정값으로 작동한다는 점에서 새로운 복합환경제어시스템의 도입이 필요하다.

위와 같은 문제들을 해결하고 비용과 에너지를 절감하고자 열공급량 절감 파이프라인을 제안하여 차세대 한국 농촌환경 조성에 기여하고자 한다.

<br>

---
## 데이터 가공 및 전처리

<img width="578" alt="스크린샷 2022-08-11 오후 8 19 16" src="https://user-images.githubusercontent.com/68985625/184124623-742478a9-e07b-41a6-a8c2-6202c1bba5d4.png">


<br>

---

## 모델 파이프라인

Prophet은 business-time series 도구로 모델링과 결과 확인 이외의 작업을 자동화할 수 있다.

<img width="610" alt="스크린샷 2022-08-11 오후 8 19 25" src="https://user-images.githubusercontent.com/68985625/184124650-cf9abf17-a722-4e07-94a8-605da0977f0c.png">


<br>

### 1) 구조

Prophet의 모형은 트렌드(growth), 계절성(seasonality), 휴일(holiday)의 3가지로 구성되어 있으며 아래의 공식과 같이 설명할 수 있다.

<br>

<img width="504" alt="스크린샷 2022-08-11 오후 8 20 40" src="https://user-images.githubusercontent.com/68985625/184124676-bd74d1f0-fd99-45e5-9615-a68b8094c4fa.png">


<br>

다른 시계열 모델과 달리 시간에 종속적인 구조를 가지고 있지 않다. 또한 주어진 데이터를 가장 적절히 표현할 수 있는 함수식을 계산하는 방식인 curve-fitting 방식으로 문제를 해결한다.

<br>

### 2) 특징
- 계절적 요소와 트렌드에 대한 가정을 쉽게 반영할 수 있다.
- ARIMA 모델과 달리 모델을 측정 주기가 일정한 간격으로 둘 필요가 없어 결측치를 대치시키지 않아도 된다. 다만 본 보고서에서는 다른 모델과 차이 비교를 위해 이상치 대치 과정을 거쳤다.
- fitting 속도가 빨라 여러 가지 다른 모형을 고려하고 적합 시켜볼 수 있다.
- 추세, 계절성, 휴일효과와 같은 직관적인 파라미터로 구성되므로 튜닝을 통한 모델 확장이 용이하다.

<br>

### 3) Model Tuning

<img width="591" alt="스크린샷 2022-08-11 오후 8 22 55" src="https://user-images.githubusercontent.com/68985625/184124696-710ca287-254e-464a-950f-f7ecd301eeb2.png">

<br>

#### 평가기준: RMSE

#### 회귀 계수

<img width="497" alt="스크린샷 2022-08-11 오후 8 23 48" src="https://user-images.githubusercontent.com/68985625/184124708-5baed357-d436-4a1e-aba1-b5090421dd1a.png">

<br>

### 열공급량 예측 추세

<img width="525" alt="스크린샷 2022-08-11 오후 8 24 34" src="https://user-images.githubusercontent.com/68985625/184124773-f57e511c-41bb-4e67-a1ca-315c69dc8820.png">

<br>

### 열공급량 분포와 시간별 열공급량 분포

<img width="556" alt="스크린샷 2022-08-11 오후 8 24 44" src="https://user-images.githubusercontent.com/68985625/184124736-82d66be1-dbd2-48ec-bac1-6fbf927039f1.png">

<br>

---

## 절감 파이프라인

### 선제적 예측

현재 방식에는 두 가지 문제점이 있다.
첫 번째로 급격한 변화에 적응하기 어렵다는 점이다. 실험과 경험에 기반한 몇가지 threshold 들을 넘는지를 기준으로 작동되고 있고, 항상 동일한 값을 갖기 때문 에 변동성이 큰 기상과 스마트팜 데이터에 대한 적응이 어렵다.
두 번째로는 센서에서 온도를 감지하고 그 정보를 중앙 처리 장치로 보내서 온도를 변경하는 시간이 발생하여 열공급이 필요한 때와 열공급이 되는 때 사이에 시간차가 발생한다.
이에 실시간으로 들어오는 정보들을 이용하여 특정 미 래 시점의 내부온도를 예측하고, 급박한 상황이 되기 전 미리 스마트팜을 제어할 수 있다면 변화에도 적응 할 수 있으며 기술적인 문제로 인해 발생하는 대기시간을 무시하고, 불필요한 에너지의 낭비를 줄일 수 있을 것이라 판단하였고, 이를 바탕으로 내부 온도를 예측하는 모델을 기반으로 한 절감 파이프라인을 개발하였다.

<img width="369" alt="스크린샷 2022-08-11 오후 8 28 13" src="https://user-images.githubusercontent.com/68985625/184124796-93dde292-d12d-47aa-a46d-054b06638116.png">

<br>

### 절감 파이프라인

<img width="542" alt="스크린샷 2022-08-11 오후 8 28 17" src="https://user-images.githubusercontent.com/68985625/184124809-9224105a-fc41-445c-a872-81b4617b74cd.png">

<br>

### RRS 도입

추가로 클라우드 컴퓨팅에서 사용하는 자원 활용 기술인 Auto Scaling 의 방식에서 착안하여 RRS(Resource Removal Strategy)를 도입하고, 절감량과 온도 유지적 측면을 고려하여 0.7로 설정하였다. RRS란 0 초과 1 이하의 값으로 변동성이 큰 워크로드에서 안정적으로 시스템을 가동하여 급작스러운 변화에도 쉽게 적응할 수 있도록 도와준다.

<img width="402" alt="스크린샷 2022-08-11 오후 8 28 23" src="https://user-images.githubusercontent.com/68985625/184124820-3a92fd20-4552-44e3-b78b-63dd62aed3a5.png">

<br>

<img width="232" alt="스크린샷 2022-08-11 오후 8 28 26" src="https://user-images.githubusercontent.com/68985625/184124837-ff02ccd2-b4c3-42a6-b365-29920f51298c.png">

<br>

겨울철이므로 온도가 증가하여 스마트팜 제어 변수를 온도가 감소하는 방향으로 변경해야하는 경우에만 RRS를 도입하였다. 이후 예측에 따라 지속적으로 값이 갱신되기 때문에 실제 온도를 감소시켜야할 때 감소시키지 못할 가능성은 낮을 것이라고 판단했다.
계절 특성 상 온도를 감소시키는 것에 보수적으로 작동하는 것이 열을 공급하지 않고 내부 온도를 유지할 수 있게 하여 에너지 절감에 도움을 줄 것이라 보았고, 과대추정을 방지하기 위해 증가폭에 대한 페널티를 주어 변화량을 완만하게 하여 불필요한 에너지 소비를 방지하였다.

<br>

### 모델 정보

PyCaret 의 자체적인 솔루션을 이용하여 RMSE 를 기반으로 상위 3 개의 Regressor 모델을 선택하였고. 가장 오차가 적은 Extra TreesRegressor 를 기반으로 하이퍼 파라미터를 튜닝하였다. 앙상블 중 Voting Regressor 를 채택하여 최종 모델을 선정하였다.

<img width="547" alt="스크린샷 2022-08-11 오후 8 28 53" src="https://user-images.githubusercontent.com/68985625/184124863-1982d4c7-3bf0-4c94-a2a9-f6d9836f8b1b.png">

<br>

<img width="608" alt="스크린샷 2022-08-11 오후 8 28 59" src="https://user-images.githubusercontent.com/68985625/184124878-89279437-851f-4105-a339-66d0a1471a2d.png">


<br>

### 절감 확인

#### 절감 전 팬코일/내부온도

<img width="623" alt="스크린샷 2022-08-11 오후 8 29 51" src="https://user-images.githubusercontent.com/68985625/184124890-48d8e968-df25-44ae-946b-0b13d6d271c6.png">


<br>

#### 절감 후 팬코일/내부온도

<img width="588" alt="스크린샷 2022-08-11 오후 8 29 58" src="https://user-images.githubusercontent.com/68985625/184124901-878f6e2b-02eb-4ac2-abed-f5f7b5880b06.png">

<br>

절감 파이프라인을 적용한 내부 온도가 적용하지 않은 것에 비해 팬코일의 발생이 적음에도 불구하고 일정 수준 더 높은 내부온도를 유지함을 알 수 있다. 이는 선제적으로 내부 온도를 예측하여 일정 수준에 다다르기 전 미리 필요한 방향으로 온도를 변화시켜 온도를 일정하게 유지하는 절감 파이프라인의 목적성에 부합 한다고 볼 수 있다.

실제로 절감하기 전 예측기간 동안의 열공급량은 4911(kwh)였지만 절감 모델을 통해 열공급량이 4464(kwh)로 약 450kwh가 절감되었다.

