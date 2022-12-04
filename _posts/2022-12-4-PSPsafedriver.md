---
title: 문제해결프로그래밍 안전 운전자 예측[KNU 2022-2][kaggle]
categories:
  - KNU
tags:
  - ML
toc: true
---

# 👨‍💻🏫KNU 2022-2 SW & media 문제해결프로그래밍 안전 운전자 예측

## 0.  Working Pipeline

| 순서 | 이번 과제에서는.. |
|-|-|
|경진대회의 이해| 1. 안전 운전자에게 적은 보험료, 난폭 운전자에게 많은 보험료를 부과하기 위해 <br>&nbsp;&nbsp;&nbsp;&nbsp;운전자가 보험금을 청구할 확률을 예측하는 모델 개발<br>2. 최종적으로 확률을 기반으로 운전자가 보험금을 청구할지(1) 아닐지(0) 예측 |
|탐색적 데이터 이해|1. 데이터 파악(feature aggregation)<br>2. 데이터 시각화(각종 피쳐의 분포확인)<br>&nbsp;&nbsp; 2.1. 타겟값<br>&nbsp;&nbsp; 2.2. 이진피쳐<br>&nbsp;&nbsp; 2.3. 순서형피쳐<br>&nbsp;&nbsp; 2.4. **연속형피쳐**<br> 3. 분석정리 및 모델링 전략 수립|
|베이스라인 모델|LightGBM사용|
|성능 개선|LightGBM + Improved Feature Engineering, Hyper-parameter Optimization(HPO)|

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

# %%
print(train.shape,test.shape)
# %%
train.drop_duplicates()
test.drop_duplicates()
print(train.shape,test.shape)
# %%
train.info() 

# %%
train_copy = train.copy().replace(-1, np.nan) #  카피 데이터에 결측치인 -1을 nan으로 변경
import missingno as msno
msno.bar(train_copy, figsize=(20, 8))

# %%
def resumetable(df):
    print(f"Dataset Shape: {df.shape}")
    summary = pd.DataFrame(df.dtypes, columns=['dtypes'])
    summary['Missing'] = (df==-1).sum().values #결측값
    summary['Uniques'] = df.nunique().values #고유값
    for col in df.columns:
        try:
            if 'bin' in col or col=='target': #이진형
                summary.loc[col, 'data type'] = 'binary'
            elif 'cat' in col: #명목형
                summary.loc[col, 'data type'] = 'categorical'
            elif df[col].dtype == float: #연속형
                summary.loc[col, 'data type'] = 'float'
            elif df[col].dtype == int: #순서형
                summary.loc[col, 'data type'] = 'integer'
        except:
            pass
    return summary

# %%
summary=resumetable(train)
summary
~~~

## 2. 데이터 시각화

### 2.1. 이진형 target변수의 0과 1의 비율을 확인

~~~python
# %%
import matplotlib as mpl
mpl.rc('font', family='Malgun Gothic', size=12)

plt.figure(figsize=(7,6))
ax=sms.countplot(x='target',data=train)

# 이진형 피쳐들의 분포(퍼센타일 값)를 확인해본다.
def write_percent(ax, total_size):
    for p in ax.patches:
        ax.text(p.get_x() + p.get_width() / 2.,\
             p.get_height()+total_size*0.01,\
            '{:0.1f}%'.format(100 * p.get_height()/total_size),\
            fontsize=12, color='black', ha='center', va='bottom')

write_percent(ax, len(train))
ax.set_title('Target Distribution')
~~~

### 2.2. 피쳐에서의 target값의 비를 확인

~~~python
# %%
import matplotlib.gridspec as gridspec

# %%
def plot_target_by_feature(df, feature, num_rows, num_cols, figsize=(20, 6)):
    mpl.rc('font', family='Malgun Gothic', size=12)
    plt.figure(figsize=figsize)
    gs = gridspec.GridSpec(num_rows, num_cols)
    plt.subplots_adjust(wspace=0.3, hspace=0.3)

    for idx, col in enumerate(df[feature]):
        ax = plt.subplot(gs[idx])
        sns.barplot(x=feature, y='target', data=df, ax=ax, palette='Set2')

# %%
#이진형 특성 정의        
binary_features = summary[summary['data type']=='binary'].index
plot_target_by_feature(train, binary_features, 7, 2, figsize=(20, 12))

# %%
#연속형 특성 정의
cont_features = summary[summary['data type']=='float'].index

plt.figure(figsize=(12, 16))
grid=gridspec.GridSpec(5,2)
plt.subplots_adjust(wspace=0.2, hspace=0.4)

for idx, col in enumerate(df[cont_features]):
    train[col]=pd.cut(train[col], 5)

    ax=plt.subplot(grid[idx])
    sns.barplot(x=cont_features, y='target', data=train, ax=ax, palette='Set2')
    ax.tick_params(axis='x', rotation=90)
~~~

1. 실행결과 명목형 피쳐의 결측값이 많은 것을 확인할 수 있다. 그러나 이러한 결측값에 타깃에 대한 예측력이 있다면 이를 고유값으로 취급하여 피쳐로 사용할 수 있다.

2. 신뢰구간이 넓어도 피쳐를 제거하지 않는 경우도 있으며 다른 특성의 신뢰구간 내에 겹침이 없이 차이가 큰 경우가 해당된다.

## 3. 피쳐 엔지니어링

### 3.0. 데이터 준비

~~~ python
#데이터 준비의 편의성을 위해 train과 test를 합친다.
# %%
all_data = pd.concat([train, test], axis=0, ignore_index=True)
all_data.drop('target', axis=1, inplace=True)
all_data.columns
~~~

### 3.1. 데이터 인코딩

- 범주형 데이터를 숫자의 형태로 바꾸는 작업
- 머신러닝 모델은 문자데이터를 인식하지 못하기에 문자형 범주데이터를 숫자로 변경해야할 필요가 있다.
- 레이블 인코딩, 원-핫 인코딩 등을 사용

#### 3.1.1. 레이블 인코딩

- 범주형 데이터를 숫자로 일대일 매핑해주는 인코딩, 범주형 데이터를 숫자로 치환
- 명목형데이터를 레이블 인코딩시 모델 성능 저하의 가능성있음(구분용으로 1,2,3과 같이 변환 될경우 1,2 또는 2,3을 1,3보다 비슷한 데이터로 여김)
- ex)

~~~python
# %%
from sklearn.preprocessing import LabelEncoder
cat = ['A', 'B', 'C', 'C']
le = LabelEncoder()
cat_lab=le.fit_transform(cat)
print(cat_lab)

#이하 출력
# 0 1 2 2
~~~

#### 3.1.2. 원-핫 인코딩

- 여러 값 중 하나만 활성화하는 인코딩
- 인코딩 하고자하는 특성의 고유값 개수를 구한다
- 특성의 고유값 갯수만큼 열을 추가한다
- 각 고유값에 해당하는 열에 1을 표시하고 나머지 열에 0을 표시
- ex)

~~~python
# %%
from sklearn.preprocessing import OneHotEncoder

ohe = OneHotEncoder()
cat_ohe = ohe.fit_transform(cat_lab.reshape(-1,1))
print(cat_ohe.toarray())

#이하 출력
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]
#  [0. 0. 1.]]
~~~

- 명목형 문자열 데이터를 원-핫 인코딩으로 변환 하기위해서는 pd.get_dummies()함수를 사용 가능
  - ex)

~~~python
# %%
pd.get_dummies(cat)

#이하 출력
#    A  B  C
# 0  1  0  0
# 1  0  1  0
# 2  0  0  1
# 3  0  0  1
~~~

#### 3.1.3. 데이터에 인코딩 적용

데이터 중 'cat'이 들어가는 특성은 모두 명목형 데이터 이므로 원-핫 인코딩을 적용한다.

~~~python
# %%
cat_features = [col for col in all_data.columns if 'cat' in col]
cat_features

# %%
encoded_cat = OneHotEncoder().fit_transform(all_data[cat_features]).toarray()

#[2.2]에서 확인한 불필요한 특성을 제거하고 남아있는 특성을 재구성한다
# %%
drop_features = [불필요한 특성]
remaining_features = [\
    col for col in all_data.columns \
    if(\
        'calc' not in col and \
        'cat' not in col and \
        col not in drop_features \
        )\
    ]

#이후 남아있는 특성과 인코딩한 결과를 합쳐 CSR행렬로 재구성한다
# COO, CSR 행렬은 희소행렬을 표현하는 방법, 참고(https://wooono.tistory.com/29)
# %%
from scipy import sparse
all_data_sparse = sparse.hstack([\
    sparse.csr_matrix(all_data[remaining_features]),\
    encoded_cat\
    ],\
    format='csr')
~~~

### 3.2. 피쳐스케일링

- 서로 다른 특성 값의 범위(MIN-MAX)가일치하도록 조정하는 작업
- 값의 범위가 데이터마다 다르면, 모델 훈련이 제대로 안될 가능성있음
- 종류로는 Min-Max normalixation, standardization이 있음
- 트리기반 모델에서는 영향을 받지 않음->데이터 크기보더 대소관계 영향이 크기 때문

#### 3.2.1. Min-Max normalixation

$$x_{scaled}=\frac{x-x_{min}}{x_{max}-x_{min}}$$

- ex)

~~~python
# %%
df=pd.DataFrame({\
    'x':[1.7,1.5,1.8]\
    'y':[75,55,60]\
    })

# %%
from sklearn.preprocessing import MinMaxScaler

# %%
scaler_M = MinMaxScaler()
df_scaled_M = scaler_M.fit_transform(df)
df_scaled_M

#이하 출력
# array([[0.66666667, 1.        ],
#        [0.        , 0.        ],
#        [1.        , 0.5       ]])
~~~

#### 3.2.2. Standardization

정규분포를 따르는 데이터는 표준화 스케일링을 사용하는게 좋다

- $\bar{x}$: 특성의 평균
- $\sigma$: 특성의 표준편차

$$x_{scaled}=\frac{x-\bar{x}}{\sigma}$$

- ex)

~~~python
# %%
from sklearn.preprocessing import StandardScaler

# %%
scaler_S = StandardScaler()
df_scaled_S = scaler_S.fit_transform(df)
df_scaled_S

#이하 출력
# array([[ 0.26726124,  1.37281295],
#        [-1.33630621, -0.98058068],
#        [ 1.06904497, -0.39223227]])
~~~

### 3.3. 데이터 완성

~~~ python
num_train = len(train)
X_train = all_data_sparse[:num_train]
X_test = all_data_sparse[num_train:]
y_train = train['target'].values
y_test = test['target'].values
~~~

## 4. Boosting

> 상위 개념은 다음을 참고[[자전거 대여 수요 에측#앙상블 기법]]

가중치를 활용하여 분류성능이 약한 모델을 강하게 만드는 기법!

[[446808CF-9BA4-462E-B30E-8AF2E26EF2E7.jpeg]]

대표적인 종류로는 XGBoost와lightGBM등이 있다.

- XGBoost - 균형 중심 분할로 병렬 배치
- LightGBM - 말단노드 중심 분할로 직렬배치(속도가 빠르지만 데이터 수가 적을때는 과적합되기 쉽다)

~~~python
# 그리드 서치가 적용된 LightGBM 실습
# %%
from lightgbm import LGBMClassifier
from sklearn.model_selection import GridSearchCV

# %%
param_grid={
    'max_depth':[range(10,30,10)],\
    'random_state':[42],\
}

# 학습모델, 서치할 파라미터, cv횟수, 사용할 cpu개수 (n_jobs=-1: 모든 cpu사용)
grid = GridSearchCV(LGBMClassifier(), param_grid, cv=5, n_jobs=-1)
grid.fit(X_train, y_train)

# 가장 좋은 파라미터 출력
# %%
grid.best_params_

# 모델 재정의 및 예측
# %%
LGBMmodel=LGBMClassifier(max_depth=grid.best_params_['max_depth'], random_state=grid.best_params_['random_state'])
LGBMmodel.fit(X_train, y_train)

pred_train = LGBMmodel.predict(X_train)
pred_test = LGBMmodel.predict(X_test)
~~~

## 5. 분류문제의 평가지표

1. 오차행렬
2. 로그손실
3. ROC-Curve와 AUC

### 5.1. 오차행렬

| | | |
|-|-|-|
|*실제음성*|**참음성**|**거짓양성**|
|*실제양성*|**거짓음성**|**참양성**|
|            |*예측음성*|*예측양성*|

이를 통하여  4가지 평가지표를 얻을 수있다.
  
- 정확도: 실제값과 예측값이 일치되는 비율. 높은 정확도가 우수한 모델을 나타내지는 않음

$$\frac{TP+TN}{TP+FP+FN+TN}$$

- 정밀도: 양성 예측의 정확도. 음성을 양성으로 오판하면 문제가 발생하는 경우 사용

$$\frac{TP}{TP+FP}$$

- 재현율: 실제 양성 값의 정확도. 양성을 음성으로 오판하면 문제가 발생하는 경우 사용

$$\frac{TP}{TP+FP}$$

- F1score: 정밀도와 재현율의 조화평균. 정밀도와 재현율중 어느 하나에 편중하지 않은 적절 조합에 사용

$$2 * \frac{precision * recall}{precision + recall}$$

~~~python
# %%
from sklearn.metrics import confusion_matrix, classification_report
confustion_matrix(y_train, pred_train)
# 이하 출력
# array([[  참음성,  거짓양성],
#        [ 거짓음성, 참양성]])
# array([[ 458812,  2],
#        [ 17345, 10]])

# %%
print(classification_report(y_train, pred_train))
# 이하 출력
#               precision    recall  f1-score   support
#            0 -> 음성기준  
#            1 -> 양성기준
#     accuracy -> 정확도    
#    macro avg -> 단순평균
# weighted avg -> 샘플에 숫자 가중치 적용된 평균     
~~~

### 5.2. 로그손실

$$logloss = -\frac{1}{N}\sum_{i=1}^{N}(y_{i}log(\hat{y}_i)+(1-y_{i})log(1-\hat{y}_i))$$

y는 실제확률, $\hat{y}$는 타깃값일 예측확률

### 5.3. 피드백을 위한 결과 해석

예측 양성은 전체 케이스 476169명 중 12명만이 보험금을 받을것이라고 예측한다.
이때 정확도는 96%로 높게 나왔지만, 이것이 좋은 모델인지는 알 수 없다.

이를 해석하기 위해 보험금을 받는 케이스를 분석한다

~~~python
# %%
proba=LGBMmodel.predict_proba(X_train)[:,1]

#보험금을 탈 확률 그래프
# %%
sns.distplot(proba)

#확률분포에서 0.5를 기준으로 0.5보다 크면 1, 작으면 0으로 예측
# %%
len(proba[proba>0.5])
# 출력:12
~~~

### 5.4. ROC-Curve와 AUC

ROC곡선의 AUC는 위와 같은 음성 편중을 해결하기위한 평가 지표로 사용된다.

- ROC는 참양성비에 대한 거짓양성비율을 나타낸 곡선이며 AUC는 그 아래면적이다
- 참양성률(TPR) = 실제양성의 정확도 = 재현률 = 민감도(sensitivity)=$\frac{TP}{TP+FN}$
- 거짓양성률(FPR)= 1 - 실제음성의 정확도 = 1 - 특이도(specificity)=$\frac{FP}{FP+TN}=1-\frac{TN}{FP+TN}$
- ex)  train 대상

~~~python
from sklearn.metrics import roc_curve, roc_auc_score
fig = plt.figure(figsize=(10,10))
fpr, tpr, thresholds = roc_curve(y_train, proba, pos_label=1)

roc_auc = auc(fpr, tpr)
plt.plot(fpr, tpr, label='(AUC = {0:0.2f})'.format(roc_auc))

plt.plot([0,1],[0,1],'k--')
plt.plot([0,0,1],[0,1,1],'g--')

plt.xlim([-0.05,1.05])
plt.ylim([-0.05,1.05])
plt.xlabel('1-Specificity')
plt.ylabel('Sensitivity')

plt.legend(loc="lower right")
plt.show()
~~~

### 5.5. 가중치 부여

양성 예측이 가중치를 가지도록 구현한다.

~~~python
# %%
LGBMmodel=LGBMClassifier(max_depth=grid.best_params_['max_depth'], random_state=grid.best_params_['random_state'],
scale_pos_weight=20)

LGBMmodel.fit(X_train, y_train)

pred_train = LGBMmodel.predict(X_train)
pred_test = LGBMmodel.predict(X_test)

# %%
confustion_matrix(y_train, pred_train)

# %%
print(classification_report(y_train, pred_train))
~~~
