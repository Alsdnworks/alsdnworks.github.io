---
title: 데이터마이닝 제 4,5,6강[KNU 2022-2]
categories:
  - KNU
tags:
  - DataMining
toc: true
---

# 👨‍💻🏫KNU 2022-2 SW & media 데이터마이닝 필기노트 4,5,6

**Decision Tree**

- 나무모형은 분석과정을 나무구조로 도형화하여 분류분석 또는 회귀분석을 수행하는 데이터마이닝의 대표적인 기법이다.
- 목표변수가 범주형일 경우 분류분석을 수행하고, 연속형일 경우 회귀나무을 수행한다.
- 데이터를 기반으로 분류, 등급화, 세분화, 변수선택, 상호작용탐색을 수행 가능하다
- 입력변수의 개수가 많은 경우 고려해야하는 상호작용의 개수가 많아져 연산량이 많아지는 단점을 어느정도 보완할 수 있다.
  
|||
|-|-|
|장점|1. 입력변수의 형태에 제한이 적음<br>2. 이해 및 해석이 용이<br>3. 상호작용을 쉽게 포착<br>4. 결측치 처리가 용이<br>5. 분류와 예측을 빠르게 처리가능 |
|단점|단순성과 경직성으로 인한 성과 저하<br>obs가 적은 경우 데이터의 변화로 변형될 수 있음|

## 1. Decision Tree Classifier: 분할 방법

### 1.1. 불순도함수의 종류

CART방법에서는 불순도 측정지표인 Gini index를 사용한다.
그외로는 디비언스와 분류오분류율이 있다.

|t|formula|
|-|-|
|Gini index| $$ 1-\sum_{i=1}^J p(j/t)^2 $$ |
|Entropy| $$ -\sum_{i=1}^J p(j/t)log_2p(j/t)$$ |
|Deviance| $$ 1 - \max_{i=1}^J p(j/t)$$ |
|Classification error rate| $$ 1 - \max_{i=1}^J p(j/t)$$ |

### 1.2. CART방법

CART방법에서는 순도를 높이기 위하여 불순도 측정지표인 Gini index와 Entropy를 사용한다.
<br>여기서 CART는 Classification and Regression Tree의 약자로 이진분할을 사용하는 분류나무와 회귀나무를 의미한다.

> Gini index
> $$
> Gini(t) = 1 - \sum_{i=1}^J p(j|t)^2
> $$

모든 가능한 분할규칙중 분할개선도를 최대로 하는 분할규칙을 찾는다. 분할규칙은 다음과 같이 정의된다.

>분할개선도
>$$
> \Delta Gini(t) = Gini(t) - (\frac{N_{left}}{N} Gini(left) + \frac{N_{right}}{N} Gini(right))
> $$
> $t_1,t_2는\; node\; t의\; 브랜치이며\; N_t는\; node\; t의\; 관측치\; 수이다.$
>$이때\; \frac{N_{left}}{N} Gini(left) + \frac{N_{right}}{N} Gini(right) 은 \;가중평균이라고 한다.$

#### 1.2.1.예시:  연속형 입력변수의 예 (목표변수가 2개인 경우)

**CASE0**

|0|1|
|-|-|
|11개|14개|

>$$Gini(t)$$
>$$Gini(t)=1-(\frac{11}{25})^2-(\frac{14}{25})^2=0.492$$
><br><br>
>
**CASE1**

|분류|t1|s1|t2|
|-|-|-|-|
|관측치 수|13| _ |12|
|0의 수|11| _ |9|
|1의 수|2| _ |3|

>$$Gini(t_1)$$
>$$Gini(t_1)=1-(\frac{11}{13})^2-(\frac{2}{13})^2=0.26$$
>$$Gini(t_2)$$
>$$Gini(t_2)=1-(\frac{9}{12})^2-(\frac{3}{12})^2=0.375$$
>$$가중평균$$
>$$\Delta Gini(t) = 0.26 \times \frac{13}{25} +  0.444 \times \frac{12}{25}  = 0.315$$
>$따라서\; 분할개선도는\;  0.492 - 0.315 = 0.177이다.$
><br><br>
>
 **CASE2**

 |분류|t1|s2|t2|
 |-|-|-|-|
 |관측치 수|11| _ |14|
 |0의 수|11| _ |3|
 |1의 수|0| _ |11|
>
>$계산 생략$
><br> $이때,\;분할개선도는\;  0.492 - 0.189 = 0.303이다. \rightarrow s2가\; 더\; 효율적으로\; 분할함$

#### 1.2.2.예시:  범주형 입력변수의 예 (목표변수가 2개인 경우)

>가능한 모든 부분집합을 만들어서 분할규칙을 만들고 분할개선도를 계산한다.
>
|A|B|C|D|
|-|-|-|-|
|0|1|0|1|
|0|1|0|0|
|||(생략)||

|좌측분할|우측분할|지니지수|
|-|-|-|
|C|ABD|n|
|D|ABC|**min-n(가정)**|
||생략||

### 1.3. C4.5. 방법

C4.5 방법은 CART방법과 유사하다.

- 연속변수에서의 CART방법은  분할규칙을 만들기 위해 Gini index를 사용했다면 C4.5방법에서는 엔트로피를 사용한다.
- 범주형 변수에서는 각 범주가 하나의 브랜치를 가지며 불순도 감소량을 **정보이익;information gain**이라고 한다.
- $$informationGain = entropy(t)- \sum_{j} ^K \frac{N_{t_i}}{N_t} entropy(t)$$
- 정보이익은 브랜치가 많을 수록 증가하는 경향이 있어 분할가지가 많은 경우에는 정보이익을 보정해야 한다. 이를 위해 정보이익을 보정한 것이 **정보이익비;gain ratio**이다.
- 이는 정보이익에 **내재정보;intrinsic information**의 역수를 곱한 것이다.
- $$gainRatio = \frac{informationGain}{intrinsicInfo}$$
- $$intrinsicInfo = -\sum_{j} ^K \frac{N_{t_i}}{N_t} log_2 \frac{N_{t_i}}{N_t}$$

### 1.4. 기타방법

> - CHAID
> - QUEST
> - CRUISE
> - 등등(시간날때 정리함)

## 2. Decision Tree Classifier: 크기 선택방법
  
### 2.1. 분할정지방법

단계마다 분할이 꼭 필요한것인지 통계적 유의성을 이용해 평가. 빠른시간내에 나무 모형을 구축.

### 2.2. 가지치기방법

계속해서 분할하다가 분할이 더이상 유의미하지 않을때 분할을 중단하는 방법

#### 2.2.1. 가지치기이론

CART 방법에서는 가지치기를 위해 비용복잡함수를 사용한다.

>$$
>costComplexity(a) = errorRate(T) + \alpha \times |T|
>$$
>$T:\; Tree,\; |T|:\; number\; of\; terminal\; nodes,\\ a:\; penaltyParameter\; in\; the\; complexity\; of\; Tree$

$a$값이 증가하며 구축되는 나무를 $T_1, T_2, T_3, ... , T_n$이라고 할때  10-fold cross validation을 이용해 $a$값에 따른 오분류율을 구하고 가장 작은 오분류율을 가지는 $a$값을 선택한다.

$$
ErrorRate(T^*) = min[ErrorRate(T_0),ErrorRate(T_1)....ErrorRate(T_n)]
$$

$1.s.e.$법칙은 $ErrorRate(T^*)+s.e(T^*)$의 값보다 작은 오분류율을 가지면서 크기가 가장 작은 나무 구조를 선택하는 방법

## 3. Decision Tree Regression : 분할 방법

### 3.1. CART방법

$불순도(t)=MSE$

$분할개선도(t)=MSE(t) - \frac{N_{t_1}}{N_t}MSE(t1) - \frac{N_{t_2}}{N_t}MSE(t2)$

### 3.2. GUIDE방법

- CART의 변수 선택 편향성을 보완하기 위해 제안된 방법
- 변수선택: 목표변수 입력변수를 통해 다중 선형회귀분석을 수행하고 잔차1분할표를 이용해 카이제곱검정을 수행한다.
- 분할점: CART의 뷴할점 선택방법, 연속형인 입력변수인경우 중위수 채택
- 장점: 매우 다양한 회귀나무를 생성 가능하며 예측 정확도도 우수함

## Decision Tree: Ensemble method

- 의사결정 나무를 사용하는 방식으로 앙상블 방식이 있다.
- 이는 주어진 데이터를 이용하여 여러개의 서로 다른 예측모형을 생성한 후 예측결과들을 종합하여 하나의 최종 예측결과를 도출해내는 방법이다.

> 용어정리
> 
> |원어|뜻(머신러닝에서의)|
> |-|-|
> |residual|잔차|
> |error|데이터간의 오차|
> |crosstabulation|교차표|
> |missclassification|오분류율|
> |crossvaildation|교차검증|
> |accuracy|러닝 결과의 정확도|

|/|분류모형|수치형|
|-|-|-|
|데이터형|trainingData|fittingData|
|정확도평가척도|Accuracy|Missclassification|
|오차평가척도|Residual|Error|
|평가데이터|testingData|modeltestData|

앙상블모형의 수식화 Y^=b_0+b_1x_1+b_2x_2+b_3x_3+b_4x_4+...... (x는 newdaata이며 b는 weight)..
<br>=>supervisedLearning의 목적!!, - weight는 오분류율의 역수

앙상블분류기(T_i) 유사하지 않고 매우 다양하다! Bootstrap이라는 반복이있는 확률임의추출 방법을 사용하는데 이는
<br>나무 모형을 적합시킬때 분할방법에 변화를 주어 나무모형이 다양해지도록 만드는 방법이다. 
<br>훈련데이터(의양)L은 변동이 없어도 나무모형의 임의성을 가미한다

- 중간마디에서 분할 후보점을 선발할때 전체변수가 아닌 임의변수의 부분집합중에서 선발하고 그중에서**분할개선도**를 최대화 시키는 분할점을 탐색
- (랜덤포레스트)----**분할개선도의 최적화가 필요**

### Ensemble method: Bagging

- bootsrtaping aggregatig의 약어
- 훈련데이터로부터 부트스트랩데이터를 B번 생성하여 각 붓스트랩데이터마다 분류기를 생성한후 그 예측결과를 앙상블하는 방법

1. 훈련데이터 $L={x_1,x_2,x_3,....,x_L,\; x_i:입력변수\; 벡터, y_i는\; 목표변수}$의 정의
2. L로부터 B개의 부트스트랩데이터 $L_1,L_2,L_3,....,L_B$를 생성
3. 각 부트스트랩데이터 $L_i,...L_B$에 대해 분류기 $T_i,...T_B$를 생성
4. B개의 분류기를 결합시켜 최종 예측모형 $\hat{f}(x)$를 생성, 단 $\hat{f}(x)=argmax_j [ \frac{1}{B}\sum_{i=1}^B T_i(x), j=1...j]$이며 x는 예측하고자하는 관찰치의 입력변수 벡터값, 분류모형인 경우 $\hat{f}(x)$는 다수결에 따라 집단을 분류하는것과 같음

배깅은 **불안정한 분류방법->단일의사결정나무**의 예측력을 향상시킨다

<br> ---> 특히 이 경우 prunning을 사용하지 않은 최대나무가 가장 예측정확도가 좋다.
<br> ---> 왜? prunning을 하지않은 경우 불안정한 상태의 의사결정 나무이기 때문 **중요**

### Ensemble method: Boosting

- 부스팅은 배깅에 비하여 분류기 생성과 종합하는 방식의 차이가있다.
- 오분류율을 낮추는 방식 -> (오분류율의 역수인)w를 개선시킨다
- 예측력이 약한 분류모형들을 결합하여 강한 예측모형을 만드는 과정
- 대표적으로 아다부스트가 있다

1. 훈련데이터 $L={x_1,x_2,x_3,....,x_L,\; x_i:입력변수\; 벡터, y_i는\; 목표변수}$의 정의
2. 가중치 $w_i=\frac{1}{n} , i=1...n$를 초기화
3. $b=1,...B$까지 이하의 과정을 반복
<br>3.1. 가중치 $w_i$를 사용하여 분류기 $T_b$를 생성
<br>3.2. 분류기 $T_b(x)$를 L에 적용하여 각 데이터의 오분류 여부 판별
<br>3.3. 오분류율 $\epsilon_b$를 계산 $\epsilon_b=\sum_{i}^n w_iI(x)[y_i \neq T_b(x_i) ]$
<br>3.4. 분류기의 중요도 $\alpha_b$를 계산 $\alpha_b=\frac{1}{2}log(\frac{1-\epsilon_b}{\epsilon_b})$
<br>3.5. 가중치 $w_i$를 업데이트 $w_i=\frac{w_i}{Z}exp(\alpha_b I [y_i \neq T_b(x_i)]-a_bI[y_i=T_b(x_i)]),i=1...n$
<br>여기서 $Z$는 $W_i$의 합이 1이 되도록 만드는 상수
4.  생성된 B개의분류기를 결합하여 최종 예측모형 $\hat{f}(x)= argmax_j(\sum_{b=1}^B I(T_b(x)=j),j=1...j)$ 를 생성. 이때, x는 예측하고자하는 관찰치의 입력변수벡터이다.

- classification 모형인 경우 $\hat{f}(x)$ 는 $a_b$의 가중치를 반영한 가중투표의 방식으로 집단을 분류하는것과 같다
- 뿌리노드를 한번만 분할하는 생성하는 하나의 *stump* 또는 *깊이가 2인 Tree*를 fitting하는 방식이여도 예측 정확도가 높다.

## Ensemble method: Random Forest

- 배깅과 부스팅보다 더 정확한 에측력을 가지고있다. 입력변수의 종류가 많을때 특히 예측력이 좋다.
- 입력변수를 랜덤하게 추출하여 (+)리니어컴비네이션 으로 나무를 결합한다.

1. 훈련데이터 $L={x_1,x_2,x_3,....,x_L,\; x_i*:입력변수\; 벡터, y_i는\; 목표변수}$의 정의
<br>입력변수는 p개라 가정, 즉 $x_i=(x_{i1},x_{i2},...,x_{ip})'$ 이며 입력변수를 랜덤추출한 벡터 $x_{i*}는 x_i에서 M개의 변수로 구성된 입력변수집합이다. 
2. x_i로부터 B개의 붓스트랩 데이터 $L_1...L_B$ 를 생성
3. L_b로부터 B개의 분류기 $T_1,...,T_B$를 생성, 단 중간노드마다 x_i가 아닌 x_{i*}를 사용
4. 분류기 $T_b$를 결합하여 최종 예측모형 $\hat{f}(x)= argmax_j(\sum_{b=1}^B I(T_b(x)=j),j=1...j)$ 를 생성. 이때, x는 예측하고자하는 관찰치의 입력변수벡터이다.

- 분류모형인경우 $\hat{f}(x)$ 는 다수결 투표에 따라 집단을 분류하는 것과 같음
- 배깅과 마찬가지로 랜덤포레스트 방법은 prunning을 거치치 않은 최대나무를 사용하는것이 좋다.