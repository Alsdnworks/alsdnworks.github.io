---
title: 딥러닝을 위한 수치해석 제 1강[KNU 2022-1]
categories:
  - KNU
tags:
  - NumericalAnalysis
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 수치해석 필기노트 1

## 1. 수치해석

수치 해석은 문제의 해결순서이다.

1. 문제의 제시
2. 모델 찾기
3. 계산 알고리즘 구성
4. 계산 프로그램 구성
5. 프로그램 실행
6. 계산결과의 검토

## 2. 수치알고리즘

- 풀이방법의 효율성(big-O notation-계산비용은 시간과 메모리효율)
- 정해를 찾을수있는건 사실상 4차 방정식까지, 그 이상은 해의 유무만을 수치적 해석 가능하다
- 머신(컴퓨팅)오차의 except된 오차는 최대한 줄여야하는것
- 프로그램작성의 용이성

### 참고: 머신입실론

~~~python
import numpy as np
print(np.finfo(float).eps)
~~~

## 3. 행렬

- 장방행렬: 직사각형 행렬이다. ( `np.array(a,b).reshape(m,n)` (m!=n))
- 정방행렬: 정사각형 행렬이다. ( `np.array(a,b).reshape(m,n)` (m==n))
- 역행렬:  행렬 $A$에 대한 역원으로 $A^{-1}$이며 $A^{-1}A$는 $I$이다.
- 항등행렬: ($I$)->단위행렬: 정수 계산에서의 1과 같다.(`np.identity(n)`)
- 영행렬: `np.zeros(n,m)`
- 전치행렬: $A^t$--> 행과 열의 요소와 치를 바꿈`np.transpose`
- 대각행렬: 행렬$A$의 대각요소만이 존재하는 벡터`np.eye(n,m)`
- 삼각행렬: 정방행렬에서 주대각 상,하가 0인 행렬
- 대칭행렬: 원행렬과 전치행렬이 깉은 행렬

### 참고: 행렬곱 기호 주의사항

~~~python
a=np.array([[3,4,5],[6,7,8],[9,1,2]])
b=np.array([[8,7,6],[5,4,3],[2,1,0]])

print('product by *(scala)\n',a*b)
print('product by @\n',a@b)
~~~

&#42; case<br>
 [[24 28 30]<br>
 [30 28 24]<br>
 [18  1  0]]<br>
@ case<br>
 [[54 42 30]<br>
 [99 78 57]<br>
 [81 69 57]]<br>

&#42;로 계산시 위치에따라 곱셈을 수행, @로 계산시 행렬곱(내적)

## 4. 항등행렬이 몫일때 행렬의 나눗셈

~~두근거림~~행렬곱을 떠올리는거야!

$$C=A_{m,n}@B_{n,r} =
 \begin{pmatrix}
  a_{1,1} & a_{1,2} & \cdots & a_{1,n} \\
  a_{2,1} & a_{2,2} & \cdots & a_{2,n} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  a_{m,1} & a_{m,2} & \cdots & a_{m,n}
 \end{pmatrix}
  \begin{pmatrix}
  b_{1,1} & b_{1,2} & \cdots & b_{1,r} \\
  b_{2,1} & b_{2,2} & \cdots & b_{2,r} \\
  \vdots  & \vdots  & \ddots & \vdots  \\
  b_{n,1} & b_{n,2} & \cdots & b_{n,r}
 \end{pmatrix}
 \\
 C_{i,j}=A_{i1}B_{1j}+A_{i2}B_{2j}\cdots+A_{in}B_{nj}=\sum_{k = 1}^{n}{a_{ik}b_{kj}}
 $$

`*이 해를 구하는 과정이 행렬의 나눗셈!!*`

$1/2=x$ 를 구하는 과정은 $2*x=1$-> $(1/2)*2x=(1/2)*1$->$1*x=0.5$ 행렬에도 이 원리를 사용한다.

$$

\begin{pmatrix}
  2 & 0 \\
  0 & 1 \\
\end{pmatrix}
\begin{pmatrix}
  ? & ? \\
  ? & ? \\
\end{pmatrix}=
\begin{pmatrix}
  1 & 0 \\
  0 & 1 \\
\end{pmatrix}
$$

$$
\begin{pmatrix}
  2 & 0 \\
  0 & 1 \\
\end{pmatrix}
\begin{pmatrix}
  2^{-1}=\frac{1}{2} & 0 \\
  0 & 1 \\
\end{pmatrix}=
\begin{pmatrix}
  1 & 0 \\
  0 & 1 \\
\end{pmatrix}
$$

y가 단위행렬일때 x 역행렬 $x^{-1}$ 임을 알수있다.

### 참고: 역행렬 공식

$$\begin{pmatrix}a, b\\ c, d\end{pmatrix}^{-1}=\frac{1}{ad-bc}\begin{pmatrix}d, -b\\ -c, a\end{pmatrix}$$

## 5. 행렬과 변환

x,y평면에 함수그래프로 표현된 이미지가있다. 이를 x축기준으로 1/2로 변환하기위해선 다음과 같은 방식을 사용 할수있다.

`이거 컴퓨터그래픽스 수업때 배웠음!`

$$
\begin{pmatrix}
  0.5 & 0 \\
  0 & 1 \\
\end{pmatrix}
\begin{pmatrix}
  x \\
  y \\
\end{pmatrix}
=
\begin{pmatrix}
  0.5x \\
  y \\
\end{pmatrix}
$$

마찬가지로 시계방향으로 일정 각도 기울인 도형을 그리기 위해선 다음과 같은 방식을 사용 할수있다.

$$
R\theta=
\begin{pmatrix}
  \cos\theta & -\sin\theta \\
  \sin\theta & \cos\theta \\
\end{pmatrix}
\begin{pmatrix}
  x \\
  y \\
\end{pmatrix}
=
\begin{pmatrix}
  (\cos\theta*x)+(-\sin\theta*y) \\
  (\sin\theta*x)+(\cos\theta*y) \\
\end{pmatrix}
$$

참고 할 만한 자료: [위키백과](https://ko.wikipedia.org/wiki/%EB%B3%80%ED%99%98%ED%96%89%EB%A0%AC)

## 6. 행렬과 관계

행렬은 연립 방정식으로 표현가능하다

### 6.1. 하나의 교점을 가지는 연립방정식

$$
\begin{pmatrix}1,  -1\\ 1,  +1\end{pmatrix}
*
\begin{pmatrix}x\\y\end{pmatrix}
=
\begin{pmatrix}-1\\ 1\end{pmatrix}
=
\begin{cases}x-y=-1\\x+y=1\end{cases}
$$

### 6.2. 절편으로 평행하거나 일치하는 연립방정식

$$
\begin{pmatrix}1,  -1\\ 1,  -1\end{pmatrix}
*
\begin{pmatrix}x\\y\end{pmatrix}
=
\begin{pmatrix}-1\\ ?\end{pmatrix}
=
\begin{cases}x-y=-1\\x-y=?\end{cases}
$$

### 6.3. 계산예시

$$
\begin{pmatrix}1,  2\\ 2,  3\end{pmatrix}
*
\begin{pmatrix}x\\y\end{pmatrix}
=
\begin{pmatrix}5\\ 8\end{pmatrix}
\\

\begin{pmatrix}1,  2\\ 2,  3\end{pmatrix}^{-1}
*
\begin{pmatrix}5\\ 8\end{pmatrix}
=
\begin{pmatrix}x\\y\end{pmatrix}
\\
\begin{pmatrix}-3,  2\\ 2,  -1\end{pmatrix}
*
\begin{pmatrix}5\\ 8\end{pmatrix}
=
\begin{pmatrix}x\\y\end{pmatrix} \\
x=1,y=2
$$
