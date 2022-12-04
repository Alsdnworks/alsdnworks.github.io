---
title: 문제해결프로그래밍 자전거 수요 예측[KNU 2022-2][kaggle]
categories:
  - KNU
tags:
  - ML
toc: true
---

# 👨‍💻🏫KNU 2022-2 SW & media 문제해결프로그래밍 자전거 수요 예측

## 0.  Working Pipeline

| 순서 | 이번 과제에서는.. |
|-|-|
|경진대회의 이해| 1. 문제 이해 (배경, 목적, 유형등) <br> 2. 평가지표 파악|
|탐색적 데이터 이해|1. 데이터 파악(feature aggregation) <br> 2. 데이터 시각화(각종 피쳐의 분포확인)|
|베이스라인 모델|1. 피쳐 엔지니어링(파생 피처 추가)<br> 2. 평가지표 계산함수 작성 <br> 3. 모델 훈련 및 성능 검증 <br> 4. 결과 예측 및 제출|
|성능 개선|1. 피쳐 엔지니어링 <br> 2. 하이퍼파리미터 최적화 <br> 3. 성능 검증 <br> 4. 결과 예측 및 제출|

## 1. 데이터 파악(feature aggregation)

~~~python
# %%
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# %%
train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')

pd.set_option('display.max_columns', 100)
train.head()

#이하 출력
#feature    -    explanation
#datetime - Timestamp(1T)
#season - 1. Spring, 2. Summer, 3. Fall, 4. Winter
#holiday - 0. No holiday, 1. Holiday
#workingday - 0. If day is neither weekend nor holiday, 1. Otherwise
#weather - 1.Clear, 2.Few clouds, 3.Rainy(Snowy), 4.Heavy Rainy(Snowy)
#temp - Temperature in Celsius
#atemp - "Feels like" temperature in Celsius
#humidity - Relative humidity
#windspeed - Wind speed
#casual - Number of non-registered user rentals initiated
#registered - Number of registered user rentals initiated
#count - Number of total rentals

# %%
print(train.shape,test.shape)
# %%
train.drop_duplicates()
test.drop_duplicates()
print(train.shape,test.shape)
# %%
train.info() 

#train['datetime']을 분할하여 연, 월, 일, 시간, 분, 초, 요일로 나누어 새로운 feature로 추가
# %%
from datetime import datetime
import calendar
train['year'] = train['datetime'].apply(lambda x: datetime.strptime(x.split()[0], '%Y-%m-%d').year)
train['month'] = train['datetime'].apply(lambda x: datetime.strptime(x.split()[0], '%Y-%m-%d').month)
train['day'] = train['datetime'].apply(lambda x: datetime.strptime(x.split()[0], '%Y-%m-%d').day)
train['hour'] = train['datetime'].apply(lambda x: x.split()[1].split(':')[0])
train['minute'] = train['datetime'].apply(lambda x: x.split()[1].split(':')[1])
train['second'] = train['datetime'].apply(lambda x: x.split()[1].split(':')[2])
train['weekday'] = train['datetime'].apply(lambda x: calendar.day_name[datetime.strptime(x.split()[0], '%Y-%m-%d').weekday()])

#season, weather를 문자형으로 변환
# %%
train['season'] = train['season'].map({1:'Spring', 2:'Summer', 3:'Fall', 4:'Winter'})
train['weather'] = train['weather'].map({1:'Clear', 2:'Few clouds', 3:'Rainy', 4:'Heavy Rainy'})
~~~

## 2. 데이터 시각화

### 2.1. 각 feature의 분포 확인

barplot, boxplot, jointplot, pointplot를 활용하여 각 feature의 분포를 확인한다.

~~~python
# %%
import seaborn as sns
import matplotlib as mpl
mpl.rc('font', family='Malgun Gothic')
mpl.rc('axes', titlesize=20)

# %%
train['count']=np.log(train['count'])# 편중되어 있는 count feature를 log 변환

# %%
figure,axes = plt.subplots(nrows=3,ncols=2)
plt.tight_layout()
figure.set_size_inches(10,9)

sns.barplot(data=train, x='year', y='count', ax=axes[0][0])
sns.barplot(data=train, x='month', y='count', ax=axes[0][1])
sns.barplot(data=train, x='day', y='count', ax=axes[1][0])
sns.barplot(data=train, x='hour', y='count', ax=axes[1][1])
sns.barplot(data=train, x='minute', y='count', ax=axes[2][0])
sns.barplot(data=train, x='second', y='count', ax=axes[2][1])

axes[0][0].set(ylabel='count', title='연도별 대여량')
axes[0][1].set(xlabel='month', title='월별 대여량')
axes[1][0].set(xlabel='day', title='일별 대여량')
axes[1][1].set(xlabel='hour', title='시간별 대여량')
axes[2][0].set(xlabel='minute', title='분별 대여량')#Timestamp는 시단위 데이터로 유효하지 않은 값이 나온다->제거대상
axes[2][1].set(xlabel='second', title='초별 대여량')#Timestamp는 시단위 데이터로 유효하지 않은 값이 나온다->제거대상

axes[1][0].tick_params(axis='x', rotation=90)
axes[1][1].tick_params(axis='x', rotation=90)

# %%
figure,axes = plt.subplots(nrows=2,ncols=2)
plt.tight_layout()
figure.set_size_inches(10,10)

sns.boxplot(data=train, x='season', y='count', ax=axes[0][0])
sns.boxplot(data=train, x='holiday', y='count', ax=axes[0][1])
sns.boxplot(data=train, x='workingday', y='count', ax=axes[1][0])
sns.boxplot(data=train, x='weather', y='count', ax=axes[1][1])

axes[0][0].set(ylabel='count', title='계절별 대여량')
axes[0][1].set(xlabel='holiday', title='휴일 여부에 따른 대여량')
axes[1][0].set(xlabel='workingday', title='근무일 여부에 따른 대여량')
axes[1][1].set(xlabel='weather', title='날씨에 따른 대여량')#폭우(설)데이터 하나만 존재->제거대상
#계절과 날씨데이터는 차이가 거의 없다

axes[1][1].tick_params(axis='x', rotation=90)

# %%
figure,axes = plt.subplots(nrows=2,ncols=2)
plt.tight_layout()
figure.set_size_inches(10,10)

sns.jointplot(data=train, x='temp', y='count', kind='reg', line_kws={'color':'blue'},ax=axes[0][0])
sns.jointplot(data=train, x='atemp', y='count', kind='reg', line_kws={'color':'blue'},ax=axes[0][1])
sns.jointplot(data=train, x='humidity', y='count', kind='reg', line_kws={'color':'blue'},ax=axes[1][0])
sns.jointplot(data=train, x='windspeed', y='count', kind='reg', line_kws={'color':'blue'},ax=axes[1][1])

axes[0][0].set(xlabel='temp', title='온도별 대여량')
axes[0][1].set(xlabel='atemp', title='체감온도별 대여량')
axes[1][0].set(xlabel='humidity', title='습도별 대여량')
axes[1][1].set(xlabel='windspeed', title='풍속별 대여량')#풍속이 0인 데이터가 많고 유사값이 없음 
#-> 신뢰성과 상관성이 떨어진다 -> 제거대상

# %%
plt.figure(figsize=(12,4))
sns.pointplot(data=train, x='hour', y='count', hue='weekday')#근무알에는 출퇴근시간에 대여량이 많다
~~~

### 2.2. 상관관계 분석

- 연속 변수로 측정된 두 변수간의 선형관계를 분석하는 기법
- 상관계수는 -1~1사이의 값을 가지며, 1에 가까울수록 양의 상관관계, -1에 가까울수록 음의 상관관계를 의미
- 한 변수가 증가할 때 다른 변수도 증가하는 경우 양의 상관관계, 반대의 경우 음의 상관관계
- 통계학에서의 피어슨 상관계수를 의미
- 두 변수간의 선형관계에 초점
  
1. 선형관계를 갖는가?
2. 선형관계를 갖는다면 어느 방향으로 갖는가?
3. 선형관계가 있다면 얼마나 강한가?

$$r=\frac{\sum_{i=1}^{n}(x_i-\bar{x})(y_i-\bar{y})}{\sqrt{\sum_{i=1}^{n}(x_i-\bar{x})^2}\sqrt{\sum_{i=1}^{n}(y_i-\bar{y})^2}}$$

~~~ python
# %%
corrMatt = train[['temp','atemp','humidity','windspeed','count']]
fig, ax = plt.subplots(figsize=(10,10))
fig.set_size_inches(10,10)
sns.heatmap(corrMatt.corr(), annot=True, linewidths=.3, ax=ax)
ax.set_title('피쳐간 상관관계 분석')
~~~

## 3. feature engineering

train, test 데이터를 합쳐서 전처리를 진행한다.

~~~ python
all_data = pd.concat([train, test],ignore_index=True)
~~~

데이터에서 새로운 피쳐를 추출하고 사용되는 feature만 추출한다.

~~~ python

# %%
from datetime import datetime
import calendar

#train['datetime']을 분할하여 연, 월, 시간, 요일로 나누어 새로운 feature로 추가
#일은 요일과 같은 데이터이므로 미사용
#분, 초는 모두 0이므로 미사용
#weather가 4인 데이터는 제거
all_data['year'] = all_data['datetime'].apply(lambda x: datetime.strptime(x.split()[0], '%Y-%m-%d').year)
all_data['month'] = all_data['datetime'].apply(lambda x: datetime.strptime(x.split()[0], '%Y-%m-%d').month)
all_data['hour'] = all_data['datetime'].apply(lambda x: x.split()[1].split(':')[0])
all_data['weekday'] = all_data['datetime'].apply(lambda x: calendar.day_name[datetime.strptime(x.split()[0], '%Y-%m-%d').weekday()])
all_data['weather']=all_data[all_data['weather']!=4]
#필요없는 나머지 feature 제거
all_data.drop(['datetime','casual','registered','windspeed'], axis=1, inplace=True)
#중복성을 띄우는 월과 계절중 하나를 제거
 del all_data['month']
~~~

훈련, 예측 데이터를 다시 분리한다. 기존 train 8708중 제거된 행은 하나이므로 8707개의 행을 가진다.

~~~ python
X_train= all_data[:8707]
X_test= all_data[8707:]
y_train= X_train[['count']]
y_test= X_test[['count']]
del X_train['count']
del X_test['count']
~~~

## 4. 선형 모델링

### 4.1 선형회귀

#### 4.1.1 평가지표

본 문제는 RMSLE를 사용하여 평가하였다. RMSLE는 MSLE에 로그를 취한 값이다.

~~~ python

##########################################
#간략한 설명
from sklearn.metrics import mean_squared_log_error
def RMSLE(y_true, y_pred,convertExp):
    if convertExp:
        y_predExp=np.exp(y_pred)
        y_trueExp=np.exp(y_true)
        msle=mean_squared_log_error(y_trueExp, y_predExp)
    else:
        msle=mean_squared_log_error(y_true, y_pred)
    return np.sqrt(msle)
##########################################
# 실제 수업시간에 사용한 코드
def RMSLE(y_true, y_pred,convertExp):
    if convertExp:
        y_pred=np.exp(y_pred)
        y_true=np.exp(y_true)
    log_true=np.nan_to_num(np.log(y_true+1))
    log_pred=np.nan_to_num(np.log(y_pred+1))
    calc=np.sqrt(np.mean(np.power(log_true-log_pred,2)))
    return calc
~~~

#### 4.1.2. 모델 작성

y= ax+b 형태의 선형회귀 모델을 사용한다. 선형회귀 모델은 다음과 같은 특징을 가진다.

$$\hat{y}=w_0x_0+w_1x_1+...+w_nx_n+b$$

|||
|-|-|
|$x_0 ... x_p$|데이터의피쳐, 피쳐수는 p+1|
|$w,b$|모델이 학습하는 파라미터|
|$\hat{y}$|예측으로 입력 특성에 가중치를 곱해서 더한 가중치의 합|

~~~ python
#선형회귀 모델을 사용하여 학습
# %%
from sklearn.linear_model import LinearRegression
Lin_model=LinearRegression()

log_y_train=np.log(y_train)
log_y_test=np.log(y_test)
Lin_model.fit(X_train,log_y_train)

print(Lin_model.coef_)#선형회귀 결과의 계수
print(Lin_model.intercept_)#선형회귀 결과의 상수항(절편)

print(Lin_model.predict(X_test))
print(Lin_model.predict(X_test))

#결과를 그래프로 확인해본다(train)
# %%
plt.figure(figsize=(10,5))
x=np.linspace(0,7)
y=x
lines=plt.plot(Lin_model.predict(X_train),log_y_train,'o',x,y,'k')
plt.setp(lines[0],markersize=5)
plt.setp(lines[1],linewidth=4,color='r')
plt.xlabel('Predicted')
plt.ylabel('Observed')
plt.show()

#결과를 그래프로 확인해본다(test)
plt.figure(figsize=(10,5))
x=np.linspace(0,7)
y=x
lines=plt.plot(Lin_model.predict(X_test),log_y_test,'o',x,y,'k')
plt.setp(lines[0],markersize=5)
plt.setp(lines[1],linewidth=4,color='r')
plt.xlabel('Predicted')
plt.ylabel('Observed')
plt.show()
~~~

#### 4.1.3 성능평가

위에서 작성한 RMSLE함수를 사용하여 성능을 평가한다.

~~~python
# %%
print(RMSLE(log_y_train,Lin_model.predict(X_train),True))
print(RMSLE(log_y_test,Lin_model.predict(X_test),True))
~~~

 이하 기말고사 범위---------------------------------------------------------

### 4.2. 릿지 선형 회귀(Ridge Regression)

- 선형모델 + 가중치 최소화
- 최소적합적 예측함수
- w(가중치): 결과 출력에 미치는 중요도를 조절하는 매개변수
- 설명변수의 수가 증가할 경우 설명변수간 다중공선성이 생길 가능성이 존재하며 모델이 지나치게 복잡해진다.
- 회귀계수의 축소(the shirinkage penalty) 보다 적은 변수의 사용으로 모델의 간결화

#### 4.2.1. norm

- 벡터의 크기는 원점에서 부텅 벡터 좌표까지의 거리(Magnitude)
  - p: norm의 차수
  - n: 대상 벡터의 요소 수
- 각 요소별로 요소 절대값을 p번 곱한 값의 합을 p제곱근 값

$$L_p=(\sum_{i}^{n}|x_i|^p)^\frac{1}{p} ... L_2=\sqrt{\sum_{i}^{n}x_i^2}$$

#### 4.2.2. 가중치 최소화

- 가중치의 절대값을 가능한 작도록 규제/ 제약(Regularization)하여 모든 원소가 0에 가깝게 만들어 모든 특성이 출력에 주는 영향을 최소한으로해 과대 적합이 되지않도록 모델을 강제로 제한

- 조절모수 $\lambda$ (the tuning parameter, Penalized Regression)사용: 

1. 조절 모수가 클수록 coefficient를축소(shrinkage)
2. 과적합을 막기위해 잔차를 계산하는 cost함수에 penalty를부여
3. 순수한 오차값에 과하게 반응하지 않도록하여 과적합을 방지
4. 회귀추정치와 관련된 영향을 조절

|$\lambda \Rightarrow 0$|$\lambda \Rightarrow \infty$|
|:---:|:---:|
|penalty항은 효과가 없다|the shrinkage penalty의 영향이 커짐|
|최소 제곱의 추정치(복잡도의 증가, 과대적합 가능)|계수 추정치는 0에 가까워짐(복잡도 감소, 과소적합의 가능)|

|$\lambda$ 가 클수록|$\lambda$ 가 작을수록|
|:--:|:--:|
|계수의 편차가 좁다|계수 편차가 크다(과대적합)|
|계수를 0에 더 가깝게|계수에 거의 제한을 두지 않는다|
|훈련데이터의 성능이 나빠진다|선형모델에 근사해진다|
|일반화에 도움|..|

#### 4.2.3. 모델작성

~~~python
from sklearn.linear_model import Ridge
ridge = Ridge(alpha=1.0)#페널티항의 가중치
ridge.fit(X_train, log_y_train)

print('가중치: ', ridge.coef_)
print('절편: ', ridge.intercept_)

ridge_pred_train = ridge.predict(X_train)
ridge_pred_test = ridge.predict(X_test)

print('RMSLE train: ', rmsle(log_y_train, ridge_pred_train))
print('RMSLE: test', rmsle(log_y_test, ridge_pred_test))
~~~

### 4.3. 라쏘(Lasso)회귀

- 가중치의 영향을 최소화
  - 가중치가 0이 될 수있다
  - 계수의 편차를 검소
  - 특성을 선별해서 해석력을 보인다
- loss+penalty
  - L1-norm

|$\lambda$ 가 클수록|$\lambda$ 가 작을수록|
|:--:|:--:|
|L1비중 증가로 과소적합(복잡도 감소)|과대적합으로 복잡도가 증가하여 선형회귀와 유사한 형태를 보인다|
|패널티의 효과증대(가중치의 감소)|..|

- $\lambda$에따라서 사용되는 특성의 개수를 조절가능하다.
- 특징 중 일부가 중요할때 사용
- 완전히 제외되는 특징이 발생한다

#### 4.3.1. 모델작성

~~~python
# %%
from sklearn.linear_model import Lasso
lasso = Lasso(alpha=1.0)
lasso.fit(X_train, log_y_train)
print('가중치: ', lasso.coef_)
print('절편: ', lasso.intercept_)

lasso_pred_train = lasso.predict(X_train)
lasso_pred_test = lasso.predict(X_test)

# 반환 형태reshape
lasso_pred_train = lasso_pred_train.reshape(len(lasso_pred_train), 1)
lasso_pred_test = lasso_pred_test.reshape(len(lasso_pred_test), 1)

print('RMSLE train: ', rmsle(log_y_train, lasso_pred_train))
print('RMSLE: test', rmsle(log_y_test, lasso_pred_test))
~~~

#### 4.3.2. 릿지, 라쏘, 람다 정리

-> 릿지와 라쏘 회귀는 선형 회귀모델로 패널티를 이용하여 계수의 영향력을 조절
L1/L2패널티를 이용, $\lambda$로 영향력 조절

|릿지|라쏘|
|-|-|
|W의 모든 원소가 0에 가깝게|w이 0 이 될 수 있다|
|계수의 편차를 갑소시킨다다|특징을 선별하여 해석력을 보인다|
|L2norm|L1norm|
|설명변수가 비슷한 중요도를 가진다다|설명변수들중 일부가 중요하게 된다|
|모든 특징들의 영향역이 감소|완전히 제외되는 특징|
|일반적으로 선호됨|분석과 이해가 쉬운 모델|

|$\lambda$가 작으면|$\lambda$가 크면|
|-|-|
|MSE비중 증가|L1/L2가 비중 증가|
|과대 적합|과소 적합|
|복잡도의 증가|복잡도의 감소|
|선형회귀와 유사|패널티 효과가 증가|
|계수 편차가 크다|가중치가 감소|
|계수의 제한이 없다|일반화에 도움|

## 5. 결정트리(Decision tree)

분류/회귀에 사용. 결정에 이르기위해 가능한한 적은 Y/N질문 사용

(ex: 분류의 형태)
![[32F3A479-5910-4CC4-912F-6BC0B11C3BA1.jpeg]]

- 불확실성

> 한 노드의 샘플이 같은 클래스에 속해있다면 이 노드는 순수
> 0에서 1사이의 값을 사용한다
> 값이 클수록 순도가 감소하고 불확실성이 커짐
> 대표적인 계산법으로 지니불순도(계수) 사용  - $p_{i}^{2},k$: i번째 노드 샘플중 클래스 k에속할 확률 $G_i = 1- \sum_{k=1}^{n}p_{i}^{2},k$

~~~python
# %%
from sklearn.tree import DecisionTreeRegressor, plot_tree

DT_model = DecisionTreeRegressor(max_depth=2)# 깊이에 따라 모델의 복잡도가 달라짐
DT_model.fit(X_train, log_y_train)

DT_pred_train=RF_model.predict(X_train)
DT_pred_test=RF_model.predict(X_test)

print(RMSLE(log_y_train,DT_pred_train,True),RMSLE(log_y_test,DT_pred_test,)True)

#Tree를 시각화
# %%
plot_tree(DT_model, feature_names=X_train.columns, filled=True)
~~~

### 5.1. 앙상블 기법

여러개의 모델을 조화롭게 학습시켜 더 정확한 예측값을 도출하는 기법

- 배깅 기법(**B**ootstrap **Agg**regation)
  - 샘플을 여러개 뽑아(Bootstrap) 각모델을 학습시켜 결과물을 집계(Aggregation)하는 방법
- 부스팅 기법

|장점|단점|
|-|-|
|1. 회귀와 분류에서 가장 널리 사용되는 알고리즘<br>2. 뛰어난 성능과 매개변수 튜닝 부담이 적음(기본값)<br>3. 데이터 스케일 조정 불필요<br>4. 큰 데이터셋 적용 가능|1. 많은 트리가 생성되므로 자세한 분석이 어렵다<br>2. 트리가 깊어지는 경향을 보임<br>3. 차원이 크고 희소한 데이터에 성능이 좋지않음(예: 텍스트 데이터)<br>4. 선형모델보다 메모리사용량이 큼<br>5. 훈련과 예측이 느림|

#### 5.1.1. 랜덤 포레스트

배깅의 대표적인 알고리즘. 여러개의 결정트리를 만들고 각 트리의 예측을 집계하여 최종 예측을 만드는 방법

~~~python
# %%
from sklearn.ensemble import RandomForestRegressor
RF_model=RandomForestRegressor(n_estimators=10)#샘플을 10개 뽑아서 10개의 트리를 만듬
RF_model.fit(X_train,log_y_train)

RF_pred_train=RF_model.predict(X_train)
RF_pred_test=RF_model.predict(X_test)

RF_pred_train=RF_pred_train.reshape(len(RF_pred_train),1)
RF_pred_test=RF_pred_train.reshape(len(RF_pred_test),1)

print(RMSLE(log_y_train,RF_pred_train,True),RMSLE(log_y_test,RF_pred_test,)True)

#시각화
# %%
feature_importance=pd.DataFrame(RF_model.feature_importances_,index=X_train.columns,columns=['importance'])
~~~

## 6. 하이퍼 파라미터의 최적화

1. 그리드서치: 주어진 하이퍼파리미터를 모두 순회하여 가장 좋은 서은을 발휘한 ㄴ값을 찾는 방법
2. 랜덤서치: 하이퍼파리미터를 무작위로 탐색하여 가장 좋은 성능을 발휘하는 값을 찾는 방법
3. 베이지안 최적화: 사전 정보를 바탕으로 최적의 하이퍼 파라미터 값을 확률적으로 추정하며 탐색하는 방법

**랜덤포레스트를 대상으로 그리드서치 실습**

~~~python
# %%
from sklearn.model_selection import GridSearchCV
from sklearn import metrics#평가지표

RMSLE_scorer=metrics.make_scorer(RMSLE,greater_is_better=False)#평가지표는 작을수록 이득
RF_params={
    'n_estimators':list(range(100,200,50)),
    'max_depth':list(range(5,10)),
    'n_estimators':list(range(50,300,50))
}

GS_RF_model=GridSearchCV(\
                                            estimator=RF_model,\
                                            param_grid=RF_params,\
                                            scoring=RMSLE_scorer,\
                                            cv=5\
                                            )
GS_RF_model.fit(X_train,log_y_train)


print(GS_RF_model.best_params_)
print(GS_RF_model.best_estimator_.predict(X_train))

print(RMSLE(log_y_train,GS_RF_model.best_estimator_.predict(X_train),True))
print(RMSLE(log_y_test,GS_RF_model.best_estimator_.predict(X_test),True))
~~~

## 7. 교차검증

train->test의 단점: 모델의 과적합을 방지하고 모델의 성능을 미리 확인하기 힘들다
이를 위한 vaildation이 추가되어야한다

### 7.1. K-Fold Cross Vaildation

![[F3F29782-7418-4F52-B9A4-0CE4AFF18248.jpeg]]

~~~python
# %%
from sklearn.model_selection import KFold
data=np.array(range(10))
exam_folds=KFold(n_splits=5,shuffle=False) #셔플의 여부를 결정
for train_index,test_index in exam_folds.split(data):
    print('train_index:',data[train_index],'test_index:',data[test_index])
~~~

#### 7.2.1. Stratified K-Fold Cross Vaildation

사용목적: 불균형한 데이터셋을 교차검증할 때 사용

![[F3F29782-7418-4F52-B9A4-0CE4AFF18248.jpeg]]

~~~python
# %%
from sklearn.model_selection import StratifiedKFold
data=np.array(range(10))
label=np.array([0,0,0,0,0,1,1,1,1,1])
exam_folds=StratifiedKFold(n_splits=5,shuffle=False) #셔플의 여부를 결정
for train_index,test_index in exam_folds.split(data,label):
    print('train_index:',data[train_index],'test_index:',data[test_index])
~~~

### 7.2. 실습

~~~python
# %%
from sklearn.model_selection import KFold
folds=KFold(n_splits=5,shuffle=True)#5개의 fold로 나누고 셔플을

#교차검증은 np.array형태로만 가능해 np.array로 변환
X=np.array(X_train)
y=np.array(log_y_train)

RMSLE_history=[]#검증결과를 저장할 리스트
result_history=pd.DataFrame()#검증결과를 저장할 데이터프레임

num=0
for train_index,test_index in folds.split(X):
    X_train_fold,X_test_fold=X[train_index],X[test_index]
    y_train_fold,y_test_fold=y[train_index],y[test_index]

    RF_model2= RandomForestRegressor(n_estimators=175,max_depth=18)

    y_pred=RF_model_2.predict(X_test_fold)
    RMSLE_history.append(RMSLE(y_test_fold,y_pred))

  result_history['test'+str(num)]=RF_model_2.predict(X_test)
  num+=1

#5개의 교차 검증 test 예측 결과의 평균과 RMSLE도출
# %%
print(np.array(result_history).mean(axis=1).reshape(len(X_test),1))

print(RMSLE(log_y_test,np.array(result_history).mean(axis=1).reshape(len(X_test),1)))
~~~
