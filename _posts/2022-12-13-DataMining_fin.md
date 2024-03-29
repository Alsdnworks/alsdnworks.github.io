---
title: 데이터마이닝 제 9부터 14강[KNU 2022-2]
categories:
  - KNU
tags:
  - DataMining
toc: true
---

# 👨‍💻🏫KNU 2022-2 SW & media 데이터마이닝 필기노트 9~14

# 1. 뉴럴네트워크

- 신경망 모형이란 데이터마이닝의 분류 및 예측 분야에서 주로 활용되는 계량적 학습방법이다
- 인간이 뇌를 통해 문제를 처리하는 방법과 유사
- 독립적인 과업을 수행하는 뉴런들이 연결되어있고 정보가 많은 신경망을 통해 분산
- ->일부 뉴런에 문제가 발생하여도 신경망 전체에 큰 영향을 주게 될 가능성이 낮다는 특징이 있다
- 뛰어난 병렬성과 결점허용 능력

시냅스는 각 수강돌기의 정보를 바로 통합하지 아니하고 그 종류에 따라 가중치를 달리하여 전달하는 특성이 있다. 종합된 정보의 값이 일정 수준보다 작으면 다른 뉴런으로 전달되지 않는다. 이러한 특성을 통해 신경망은 뛰어난 병렬성을 가지고 있다. 또한, 신경망은 결점을 허용할 수 있는 능력이 있다. 예를 들어, 뇌의 일부가 손상되어도 다른 부분으로 정보를 전달할 수 있기 때문이다. 이러한 특성은 신경망이 뇌의 구조와 유사하다는 것을 보여준다.

자극->수용체->두뇌(신경망)->효용체->반응

## 1.1. 신경망의 종류

### 1.1.1. 단층 신경망

 입력층은 3개의 변수($x_1,x_2,x_3$)로 구성되어 있고, 1로 구성된 상수항을 포함한다. 출력층은 변없이 입력변수의 가중 평균으로 표현된다 가중치는 입력노드 옆에 표시되는 $w_n$, 합성함수는 입력변수를 결합하는 함수

![picture 2](/assets/img/30148e54a8a18ac1afe648394cab112ea6815c8beb5dcf098a2b5b34b2eba739.png)  

 $y_1 = w_0+w_1x_1 + w_2x_2 + w_3x_3$

활성함수는 일반적으로 sigmoid가 주로 사용된다 sigmoid는 s자 형태의 비선형함수로서 출력값은 0과 1사이 값을 지니게 되는 미분 가능한 함수로 역전파 학습 알고리즘의 특성에 잘 맞으며 복잡한 유형의 의사 결정 문제에도 효과적으로 적용, 큰입력값에 대해서도 출력값이 급격하게 벗어나지 않으며 작은 입력값의 무시가 적다.

$$sigmoid(x) = \frac{1}{1+e^{-x}}$$

### 1.1.2. 다층 신경망

비선형적인 신경망 작동원리를 구현하기 위해서는 다층 신경망이 적절하다

![picture 1](/assets/img/09ec2842c72fd82b12fd9db32eeeba8b67deb34f3b230a8814a3f77628ff40fa.png)  

$H_1 = f_1(w_{01}+w_{11}x_1+\cdots+w_{n1}x_n)$

$H_2 = f_2(w_{02}+w_{12}x_1+\cdots+w_{n2}x_n)$

$y=g(w_{00}+w_{10}H_1+\cdots+w_{20}H_2)$

- 이때 $f_1,f_2$는 활성함수이고, $H_1$과 $H_2$는 은닉층을 의미하는 변수
- g는 은닉층 $H_1$과 $H_2$을 선형결합하여 조정하는 활성함수이다
- $x_i(i=1,\cdots,n)$는 입력정보를 의미하며, $y$는 출력변수를 의미한다

### 1.1.3. 기타 신경망

- $RBF(radial basis function)$ 신경망은 은닉층이 하나로 가중치 없이 입력값이 그대로 전달, 활성화함수로는 정규분포를 사용하는 신경망이다
- $RBF$ 신경망은 입력값이 가까울수록 활성화함수의 출력값이 커지는 특성을 가지고 있어서 입력값이 가까운 데이터들은 비슷한 값을 가지게 된다
- $EBF(elliptic basis function)$ 신경망은 $RBF$ 신경망과 비슷하지만 입력정보의 선형 결합값을 RBF 신경망에 대입하여 활성화함수를 적용하는 신경망이다.

## 1.2. 신경망의 학습

### 1.2.1. 개요

- 입력변수 또는 노드의 개수, 은닉층의 수와 노드의 개수, 활성함수의 종류등을 결정하는 단계
- 가중치를 추정하는 단계로 구분-신경망 학습 또는 훈련으로 통계학에서의 모수추정(parameter estimation)과 유사
- 신경망의 학습은 지도학습과 비지도학습 두가지 모두 해당

### 1.2.2. 학습과정

1. 연결강도에 대한 초기값을 설정 -> 출력정보를 계산
2. 학습을 통해 구한 출력값의 추정치와 학습자료에서 기준이 되는 출력값과 비교
3. 이 과정을 출력값과 학습값의 차이가 일정 수준이 될때까지 반복

연결강도인 가중치를 적절히 추정하기 위해서는 목적함수 objective function을 정의해야 한다

일반적으로 많이 쓰이는 목적함수로는 선형모형에서 사용되는 오차제곱합(SSE)이 있다.

$\sum_{i=1}^n(y_i-\hat{y_i})^2=\sum_{i=1}^n[w_{00}+w_{10}H_{li}+w_{20}H_{li}]^2$

분류의 경우 log-likelihood를 사용한다
$-\sum_{i=1}^n(y_i\log{(p_i)}+(1-y_i)\log{(1-p_i)})$

## 1.3. 역전파 알고리즘

신경망의 목적함수는 연결강도에 대하여 비선형적이므로 목적함수를 최소화하는 연결강도를 구하기 위해서는 수치적인 방법을 사용해야 한다. 이때 사용되는 방법이 역전파 알고리즘이다.

1. 초기 가중치와 목표함수를 최적화하는 기준을 정하고 초기 가중치를 바탕으로 예측치를 계산한다
2. 실제 출력과 예측치의 차이를 계산하여 오차를 계산한다
3. 오차를 은닉층과 입력층으로 역전파시켜 가중치를 조정한다
4. 2~3을 반복적용할때 가중치의 변화가 거의 일정해지면 반복을 멈추고 최종 가중치를 사용한다

### 1.3.1. 초기가중치와 학습률

가중치의 초기값을 정하는 방법으로는 각 모수에 난수를 이용하여 임의로 값을 지정하고 목적함수 관점에서 성능이 가장좋은 초기값을 선택하는 방법이 있다![picture 3](/assets/img/ %EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A7%88%EC%9D%B4%EB%8B%9D\images\09ec2842c72fd82b12fd9db32eeeba8b67deb34f3b230a8814a3f77628ff40fa.png)  

역전파 알고리즘3단계에서 연결 가중치의 조절 정도를 학습률(learning rate)라고 한다. 학습률이 너무 크면 최적의 가중치를 찾지 못하고 학습률이 너무 작으면 학습이 느려지는 단점이 있다.

초기 가중치와 학습율에서 학습률이 $\alpha$라고 추정할 가중치의 개수가 $n$ 일경우
학습패턴의 $p$는
$$p=\frac{N}{1-\alpha}$$

처음에는 크게, 반복수가 증가하면서 점점 작아지도록 설정한다

### 1.3.2. 신경망 모형의 작성

1. 입력 및 출력변수의 식별
2. 입력과 출력변수의 값을 적절한 값으로 변환
3. 신경망의 구조를 설정
4. 자료를 이용하여 신경망을 학습
5. train에 이용된 자료가 아닌 test 자료를 이용하여 신경망의 성능을 평가
6. 신경망 모형을 이용하여 예측

#### 1.3.2.1. 입력 및 출력변수의 식별, 적절한 값으로 변환

- 범주형 입력변수는 모든 범주에서 일정 빈도 이상의 값을 가져야함
- 연속형 입력변수인경우 입력값의 범위가 변수간에 많은 차이가 있어야함
- 연속형자료인경우 일반적으로 변수의 표준화 과정을 거침

$$moderator Var=\frac{actual-minVal}{maxVal-minVal}$$

- 필요시 연속형 변수를 범주형 자료로 전환
- 순서가 있는 자료의 경우에는 연속형 자료처럼 표준화하여 0 ~ 1사이의 값으로 변환
- 순서가 없는 범주형 자료의 경우에는 각 범주를 다른 값으로 변환
- 범주형 자료의 경우에는 모든 범주에서 일정 빈도 이상의 값을 가져야하기에 범주의 수가 많은 경우에는 범주를 병합하는 방법을 사용가능

#### 1.3.2.2. 신경망의 구조 설정

- 입출력 정보가 정해진 상태에서는 은닉층의 마디수 를 정하는 과정에서 특정한 모형 선택기준을 이용한다-> 적정도 함수의 최소화 E는 적합도, $\lambda$는 가중치 제곱항의 벌칙항, $W_{ij}$는 가중치(연결강도)

$$E+\lambda\sum{W^{2}_{ij}}$$

신경망의 경우 목표변수의 출력값은 연속형으로 산출되므로 분류의 경우 출력값을 범주형 출력값으로 환산

#### 1.3.2.3. 신경망의 train

- 역전파 알고리즘 비선형적인 추정의 방법과 추정방법을 통해 가중치를 추정
- 일반적으로 n개 노드의 입력변수와 h개 노드의 은닉층 1개 노드의 출력변수를 가지는 신경망의 경우에는 h(n+1)+h+1개의 가중치를 가짐
신경망의 과적합은 은닉층의 수 및 은닉마디가 지나치게 클 경우에 발생

### 1.3.3. 민감도 분석

신경망은 regression과 classification에서 우수한 성능을 보이지만, 신경망의 불투명성(blackbox)으로 인해 신경망의 구조를 결정하는데 어려움이 있음

- 신경망의 불투명성을 완화하기위해 민감도 분석을 실시한다
- 입력변수의 상대적 중요도를 간접적으로 파악하는 과정으로 각 입력변수의 평균에서 목표변수값을 정한뒤 각 입력변수의 값이 변할때마다 출력변수의 변화를 측정한다. 이로 각 입력변수의 상대적인 중요도 측정이 가능하다

## 1.4. 신경망의 장단점

### 1.4.1. 장점

- 예외적인 경우도 있으나 대체로 신경망은 다른 모형에 비해 우수한 성능을 보인다.
- 입력변수들과 출력변수들 간의 관계가 복잡한 비선형 형태일때 유용
- 견고성에 의해 자료의 잡음에 영향을 덜 받으며 계량적인 변수뿐만아니라 정성적인 변수도 신속한 처리가 가능하다
- 기존의 통계적 방법과는 달리 여러가지 통계적 가정을 필요로 하지 않기에 자료의 활용이 자유롭다

### 1.4.2. 단점

- 학습의 과정이 투명하지 않고 복잡하여 해석이 어렵다
- 실무적인 측면에서 신경망 가중치의 등의 설정은 전문성을 요구하므로 비전무가가 쉽게 이용하기에 문제가 발생
- 잘못된 입력정보에 둔감하다는 장점이 나중에의 문제를 야기할 수 있다

## 1.5.딥러닝

- 머신러닝의 일종으로 신경망을 기반으로 한다
- 신경망의 특징인 병렬처리를 통해 빠른 학습이 가능하다
- 빅데이터와 *(뉴럴)*클라우드 컴퓨팅의 발전으로 딥러닝의 활용이 확대되고 있다

# 2. 모형평가

## 평가모형

목표변수가 존재할때 다양한 방법으로 모령을 구축하는 경우 예측값이 실제값과 동일하거나 유사하다면 예측력이 높다고 판단할 수 있다. 이러한 예측력을 평가하기 위해 모형의 성능을 평가하는 방법이 필요하다. 이러한 평가방법을 평가모형이라고 한다.

|평가모형|설명|
|:---:|:---:|
|연속형 목표변수| 각 객체의 목표변수를 예측한다|
|이항형 목표변수| 각 범주를 취할 확률을 예측한다|

## 평가측도

모형을 선택할때 예측력, 해석력 효율성과 안정성등 다양한 측면을 고려한다(단 응용분야에 따라서 어떤 요소가 중요한지 도려해 종합적으로 판단해야 한다). 이러한 요소를 평가측도라고 한다.

### 연속형 목표변수

연속형 목표변수의 평가측도는 대표적으로 PMSE가 주로 사용된다.
PMSE가 작을수록 예측력이 높다고 판단할 수 있다.
$$PMSE = \frac{1}{n}\sum_{i=1}^{n}(y_i - \hat{y_i})^2$$

### 이항형 목표변수

실제범주와 예측범주가 일치할 확률을 평가하는 방법으로는 민감도(sensitivity), 특이도(specificity), 예측정확도(accuracy), 오분류율(misclassification rate) 등이 있다.

|측도|설명|계산식|
|:---:|:---:|:---:|
|민감도|실제범주가 1일때 예측범주가 1일 확률|$Pr(\hat{Y}=1\vert Y=1)=\frac{TP}{TP+FN}$|
|특이도|실제범주가 0일때 예측범주가 0일 확률|$Pr(\hat{Y}=0\vert Y=0)=\frac{TN}{TN+FP}$|
|예측정확도|실제범주와 예측범주가 일치할 확률|$Pr(\hat{Y}=1, Y=1)+Pr(\hat{Y}=0, Y=0)=\frac{TP+TN}{TP+TN+FP+FN}$|
|오분류율|실제범주와 예측범주가 일치하지 않을 확률|$Pr(\hat{Y}\neq 1, Y=1)+Pr(\hat{Y}\neq 0, Y=0)=\frac{FP+FN}{TP+TN+FP+FN}$|

- 민감도와 특이도는 임게치에 따라 달라지고 임계치는 상황에따라 다르게 결정
- 여러 가능한 임계치에 대해 (1-특이도)와 민감도를 그래프로 그리면 ROC곡선이 그려진다.
- 민감도와 특이도가 모두 높을수록 예측력이 높다고 판단할 수 있다.(ROC곡선 아래 면적이 클수록)

## 데이터 분할에 의한 타당도 평가

- 수집한 데이터를 이용해 어떤 모형을 구축하게 되고 이를 비교평가해 최종 모형을 선택
- 이미 모형을 구축하기 위해 사용한 데이터를 재사용하면 모형의 성능이 과대평가될 수 있으므로 데이터를 분할하여 평가하는 방법을 사용한다.
- 데이터를 v개 부분집합으로 분할하여 첫 번째 부분집합을 검증용으로 남겨두고 나머지 V-1개 부분집합을 학습용으로 사용하여 모형을 구축한다. 이후 검증용 부분집합을 이용해 모형의 성능을 평가
- 두 번째 부분집합을 검증용으로 남겨두고 나머지 V-1개 부분집합을 학습용으로 사용하여 모형을 구축한다. 이후 검증용 부분집합을 이용해 모형의 성능을 평가
- 이를 V번 반복하여 V개의 성능을 종합하여 평가한다.

# 군집분석

-관측값이 개체를 의미있는 그룹으로 분류하는 방법
-대표적인 자율학습(unsupervised learning) 방법으로 특성이 유사한 개별 개체끼리 스스로 클러스터(cluster)를 형성, 이들의 특징을 사후적으로 분석(상이성 규명 등)
-군집분석은 자료의 사전정보없이 자료를 파악하는것으로 분석자의 주관에따라 결과가 달라질 수 있다.

## 비유사성 측도

개체의 개수가 $n$이고 변수의 개수가 $p$개 일때의 자료구조는 $n * p$ 행렬이다. 행렬$X$의 행은 개체 열은 변수일때 $X_{ij}$는 $i$번째 개체에서 $j$번째 연속형변수의 관측값을 나타낸다.

다음은 군집분석의 기본적인 개념을 나타낸다.

$$X = \begin{bmatrix} X_{11} & X_{12} & \cdots & X_{1p} \\ X_{21} & X_{22} & \cdots & X_{2p} \\ \vdots & \vdots & \ddots & \vdots \\ X_{n1} & X_{n2} & \cdots & X_{np} \end{bmatrix}= \begin{bmatrix} x_1 \\ x_2 \\ \vdots \\ x_n \end{bmatrix}, x_i=(x_{i1},x_{ij},\cdots x_{1j}\cdots,x_{ip})$$

두 개체가 연속형 변수로 표현 가능하다면 이들의 dissimilarity는 다음과 같이 정의된다.

i번째 개체와 k번째 개체를 나타내는 두 연속형 변수

$$x_i = (x_{i1},x_{ij},\cdots x_{1j}\cdots,x_{ip})$$  
$$x_k = (x_{k1},x_{kj},\cdots x_{1j}\cdots,x_{kp})$$

 라고 하자. 이때 두 개체의 dissimilarity는 다음과 같이 정의 가능.

$$ Euclidean\;distance\;d_{ik} = \sqrt{\sum_{j=1}^p(x_{ij}-x_{kj})^2} $$
$$ Manhattan\;distance\;d_{ik} = \sum_{j=1}^p|x_{ij}-x_{kj}| $$
$$ Minkowski\;distance\;d_{ik} = \left(\sum_{j=1}^p|x_{ij}-x_{kj}|^m\right)^{1/m} $$

이외 Mahalanobis distance, cosine distance 등이 있다.

## 계층적 군집분석

계층적 군집분석에는 가까운 관측값끼리 먼저 군집을 형성하는 응집(agglomerative) 방법과 가장 먼 관측값끼리 먼저 군집을 형성하는 분할(divisive) 방법이 있다.

### 응집분석

1. 각 개체를 하나의 군집으로 n개의 군집을 형성
2. 각 군집간 dissimilarity를 기준으로 가장 가까운 군집을 병합해 n-1개의 군집을 형성
3. 이들중 가장 가까운 군집을 병합해 n-2개의 군집을 형성
4. 반복하여 하나의 군집이 될때까지 진행

자료의 i번째 개체와 k번째 개체의 dissimilarity를 $d_{ik}$라고 하고 군집p와 q의 dissimilarity를 $d_{pq}$라고 할때 개체 간의 dissimilarity는 다음과 같이 정의된다.

![picture 4](/assets/img/8f90d23563ea9a431a8f3969e28340bf9b69fcf6dc0dd9bcb8a32b40693c5850.png)  

|연결법|방법|
|:---|:---|
|단일 연결법<br>(Single linkage)|P,Q군집은 P에 속한 개체와 Q에 속한 개체를 뽑았을 때 가장 가까운 두 개체 거리<br> $d(P,Q)=min_{i\in P,k\in Q}d(i,k)$|
|완전 연결법<br>(Complete linkage)|P,Q군집은 P에 속한 개체와 Q에 속한 개체를 뽑았을 때 가장 먼 두 개체 거리 <br> 단일연결법에 비해 잡음의 영향을 덜 받으며 밀집되어 있는 군집을 구별하는데에 사용 <br> $d(P,Q)=max_{i\in P,k\in Q}d(i,k)$|
|평균 연결법<br>(Average linkage)|P,Q군집은 P에 속한 개체와 Q에 속한 모든 개체 간의 거리 평균 <br> $d(P,Q)=\frac{\sum_{i \in p, k \in Q}d(i,k)}{num(p)*num(q)}$
|중심 연결법<br>(Centroid linkage)|P,Q군집은 P에 속한 개체와 Q에 속한 개체의 중심간의 거리 <br> $d(P,Q)=\sqrt{\sum_{j=1}^p(\bar{x}_{pj}-\bar{x}_{qj})^2}$|

> 중요! 단일 연결법

### 분할분석

1. 군집Q에 속한 각 개체에서 다른 개체 까지의 평균거리를 계산하여 평균거리가 가장 긴 개체를 선택, 이 개체를 군집 제거해 군집 A를 만들고 나머지 개체를 군집 B로 만든다.
2. 군집 A와 군집 B를 각각 분할하여 군집 A1(new), A2(old), B1, B2로 만든다.
3. A2에서 다른 개체와 평균거리가 가장 긴 개체를 선택해 i로 정의
4. 개체 i와 군집 B2에 속한 개체 간 평균거리 계산
5. 개체 i와 A2에서 다른 개체와의 평균거리가 가장 긴 개체 i와 군집 B2의 평균 거리를 뺀 값이 0보다 크면 군집 A2에서 개체 i를 제거해 군집 A1(new)에 추가한다. B2에서 개체 i를 추가해 군집 B1(new)에 추가한다. 2~4반복

거리의 차이가 0 이하면 군집 형성 중지, A2, B2를 최종 군집으로 선택

## 비계층적 군집분석

K-평균 군집분석 군집의 수 결정과 초기값의 설정이 중요

### 군집수 결정방법

각 군집의 수와 산출된 평균거리를 사용

### 초기값 설정

- 다양한 초기값을 가지고 주어진 군집수에 대한 K-mean 군집화를 수행하고 최종 군집에서 중심점까지의 평균 거리를 계산해 가장 작은 값을 선택
- 계층적 군집화를 시행하고 게층적 군집화로부터 군집수와 형성된 군집으로부터 centroid를 계산해 K-mean 군집화를 수행
- 주성분을 이용

## 군집분석의 특징

- 자료간의 거리를 이용허기에 자료의 단위가 결과에 큰 영향을 미치므로 자룔를 표준화하는 경우가 있다

- 각 변수의 중요도가 다를 경우에는 가중치를 이용하여 각 변수의 중요도를 조절. 가중치는 대부분의 경우 단위변환(표준화) 후 부여

### 장점

- 자료에대한 사전정보 없이 의미있는 자료의 형태를 찾아내는데 유용
- 다양한 데이터에 적용가능
- 분석방법의 적용이 용이함

### 단점

- 복잡한 데이터에는 적용이 어려움(이상치 영향을 받기때문)
- 변수의 개수가 많은 경우에는 적용이 어려움
- 가중치와 거리 정의가 어려움
- 초기 군집의 수(k)를 결정하는 것이 어려움

# 연관성분석

- Association Analysis은 연관성 규칙을 통해 하나의 거래나 사건에 포함되어있는 둘 이상의 품목간 상호 연관성을 발견하는 것
- 연관 규칙은 어떤 사건이 얼마나 자주 동시에 발생하는가를 표현하는 규칙이나 조건
- 장바구니분석이라고도 한다

## 연관규칙의 특징

- 목적변수 없이 특성의 조합으로 규칙을 표현한다 따라서 연관성분석은 자율학습에 속한다.
- 연관규칙이 서로 영항을 주지 않기 때문에 하나의 변수는 여러 규칙에 해당 가능하다.
- A가 발생시 B가 발생한다는 규칙은 $ A \rightarrow B $로 표현
- 많은 품목의 관게속에서 의미있는 결과를 찾기위해서는 결과해석에 앞서 연관성의 내용이 일반화 가능한 내용인지 판단 가능하도록 각 연관 규칙을 비교평가 가능한 정량적 평가 기준이 필요하다

|시도|결과|
|:-|:-|
|1|A,B,C|
|2|A,C,D|
|3|B,C,D|
|4|A,D,E|
|5|A,C,E|

위와 같은 데이터가 있다고 가정할때

1. 행과 열의 교차표를 만든다
2. 각 교차점에 공통적 품목의 수를 기록한다
3. 규칙성의 파악

## 연관성분석의 종류

### 지지도(Support)

- 연관 규칙의 유용성을 파악하는 척도
- AB의 지지율은 전체 거래중 AB가 동시에 발생한 거래의 비율(단위 %)
- $P(A \cap B) = \frac{AB동시 포함 시도 수}{전체 시도 수}$
- 표본수가 적을수록 통계적 유의성을 증명하기는 힘듦
- 투자시간, 비용에 대해 판매량을 증가시키는 기여도가 작다

### 신뢰도(Confidence)

- 원인이 발생할때 결과가 발생할 가능성
- 지지율과 다르게 기준이 되는 이벤트는 특정 결과가 발생한 경우로 한정
- A->B의 신뢰도는 A를 발생한 경우 B가 발생할 확률
- $\frac{P(A \cap B)}{P(A)}=\frac{AB동시 포함 시도 수}{A포함 시도 수}$
- A->B의 신뢰도와 B->A의 신뢰도는 일반적으로 상이

### 향상도(Lift)

- 규칙을 모를때에 비하여 규칙을 알았을때의 결과의 변화
- 향상도는 품목 B의 발생확률이 품목 A의 발생확률에 비해 얼마나 증가하는지를 나타내는 지표

### 시차연관성 분석

- 시간적인 흐름에 따라 발생하는 연관성을 분석하는 방법
- 일반적으로 비대칭적

$$A->B 지지율 = \frac{A 후 B 발생 시도 수}{전체 시도 수}$$
$$A->B 신뢰도 = \frac{A 후 B 발생 시도 수}{A 발생 시도 수}$$
$$A->B 향상도 = \frac{A -> 신뢰도}{B 포함 발생 확률}$$

## 연관성 분석의 절차

1. 품목과 수준의 선택
2. 연관 규칙의 생성<br> 지지율이 낮아도 신뢰도가 높은 규칙을 선택 <br> 구매순서 정보가 있다면 시차 연관성 분석을 수행
3. 연관규칙의 분석 <br> 지지율,신뢰도,향상도를 이용하여 연관규칙을 후보군을 선정<br> 후보의 의미분석(우연성을 배재하기위함)

## 연관성 분석의 장단점

- 장점
  - 연관 규칙이 조건->반응의 형태로 이해와 적용의 용이성
  - 비교적 단순한 알고리즘
  - 목표변수없는 객관적인 분석
  - 거대자료의 탐색에 적합
- 단점
  - 품목수의 증가에 따라 연산량이 기하급수적으로 증가
  - 유사한 품목의 범주화가 어려움
  - 수많은 연관규칙의 발생으로 인한 규칙의 해석이 어려움
  - 연속형 변수의 경우 연관성 분석이 어려움