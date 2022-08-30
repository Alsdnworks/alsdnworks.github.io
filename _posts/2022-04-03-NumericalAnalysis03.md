---
title: 딥러닝을 위한 수치해석 제 3강[KNU 2022-1]
categories:
  - KNU
tags:
  - NumericalAnalysis
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 수치해석 필기노트 3

## 3. 선형 연립 방정식 계의 해(이어서)

### 예제1. 크레머 방식으로 선형방정식 계의 해 구하기

$$2x_{1}+4x_{2}+x_{3}=-2,\\x_{1}+2x_{2}+x_{3}=0.5,\\3x_{1}+4x_{2}+2x_{3}=2$$

1. inv: 역행렬 계산
2. det: 행렬식 계산
3. solve: 선형 연립방정식계의 해 찾기 (cf. scipy.linalg 에 포함된 함수)

~~~python
import numpy as np
A = np.array([[2, 4, 1], 
              [1, 2, 1],
              [3, 4, 2]])
Y = np.array([-2, 0.5, 2])
print("AX=Y의 좌변 행렬: ")
print(A)
print("AX=Y의 우변 벡터: ")
print(Y)
print("행렬 A의 행렬식: ", np.linalg.det(A))
# 방법1: 계산된 역행렬 이용
X1 = np.linalg.inv(A) @ Y
#X1 = np.linalg.inv(A) @ Y.T
#X1 = np.round(np.linalg.inv(A) @ Y,16)
print(X1)
# 방법2: 역행렬 계산과 다른 방법 (예: 행렬 분해 기반의 방법)
X2 = np.linalg.solve(A,Y)
#X2 = np.linalg.solve(A,Y.T)
#X2 = np.round(np.linalg.solve(A,Y),16)
print(X2)
diffX = X1 - X2   # 2개의 다른 알고리즘(inv, solve)으로 구현된 함수를 이용하여 찾아진 해 비교(X1 vs X2)
print(diffX)
~~~

$$ x_{1}=\frac{det(A_{(1)})}{det(A)}=\frac{2}{2}=1\\x_{2}=\frac{-3.5}{2}=1.75\\x_{3}=\frac{6}{2}=3 $$


### 2. Gaussian Elimination

ex) 다음 대각 행렬을 보자

$$
A= \begin{pmatrix}
  a & 0  & 0 \\
  0 & b  & 0 \\
  0 & 0  & c \\ 
 \end{pmatrix}
 \begin{pmatrix}
  x_{1} \\
  x_{2} \\
  x_{3}
 \end{pmatrix}
 =
  \begin{pmatrix}
  y_{1} \\
  y_{2} \\
  y_{3}
 \end{pmatrix}\\
 x1=\frac{y1}{a}\\
 x2=\frac{y2}{b}\\
 x3=\frac{y3}{c}\\
 $$

그러면 다음 경우는 어떨까? '?'는 0이 아닌 특정값이다.

$$
A= \begin{pmatrix}
  a & ?  & ? \\
  0 & b  & ? \\
  0 & 0  & c \\
 \end{pmatrix}
 \begin{pmatrix}
  x_{1} \\
  x_{2} \\
  x_{3}
 \end{pmatrix}
 =
  \begin{pmatrix}
  y_{1} \\
  y_{2} \\
  y_{3}
 \end{pmatrix}
 $$
연립이지만 아래에서부터 올라가면 각각을 분리 가능하다!

$$
x3*c=y3 \Rightarrow \textcolor{red}{x3}=\frac{y3}{c}\\
(x2*b)+(\textcolor{red}{x3}*?)=y2 \Rightarrow \textcolor{green}{x2}= ??? \\
(x1*a)+(\textcolor{green}{x2}*?)+(\textcolor{red}{x3}*?)=y1 \Rightarrow x1= ??? \\
$$

> $AX=y$
> $\Rightarrow$
> ForwardElimination(전방소거) <br>
> $\Rightarrow$ $\coprod X=Z$
> $\Rightarrow$
> BackwardSubstitution(후방대입) <br>
> $\Rightarrow$ 해( $X$ ) 찾기

#### 1. 전방소거

##### 1.연립방정식의 일반형

$$
a_{11}x_{1}+a_{12}x_{2}+a_{13}x_{3}+\cdots +a_{1n}x_{n}=y_{1},\\
a_{21}x_{1}+a_{22}x_{2}+a_{23}x_{3}+\cdots +a_{2n}x_{n}=y_{2},\\
a_{31}x_{1}+a_{32}x_{2}+a_{33}x_{3}+\cdots +a_{3n}x_{n}=y_{3},\\
\vdots\\
a_{n1}x_{1}+a_{n2}x_{2}+a_{n3}x_{3}+\cdots +a_{nn}x_{n}=y_{n}
$$

##### 2. $a_{i1}(i \geqq )$들의 전방 소거 결과 연립방정식계

$$
a_{11}x_{1}+a_{12}x_{2}+a_{13}x_{3}+\cdots +a_{1n}x_{n}=y_{1}\\
a_{22}'x_{2}+a_{23}'x_{3}+\cdots +a_{2n}'x_{n}=y_{2}'\\
a_{32}'x_{2}+a_{33}'x_{3}+\cdots +a_{3n}'x_{n}=y_{3}'\\
\vdots\\
a_{n2}'x_{2}+a_{n3}'x_{3}+\cdots +a_{nn}'x_{n}=y_{n}'
$$

> '는 소거로 인해 변화된 값이며 $a_{ij}'=a_{ij}-(a_{i1}/a_{11})a_{ij},\\ y_{i}'=y_{i}-(a_{i1}/a_{11})y_{1}$임

##### 3. $a_{i1}(i \geqq )$들의 전방 소거 결과 연립방정식계

$$
a_{11}x_{1}+a_{12}x_{2}+a_{13}x_{3}+\cdots +a_{1n}x_{n}=y_{1}\\
a_{22}'x_{2}+a_{23}'x_{3}+\cdots +a_{2n}'x_{n}=y_{2}'\\
a_{33}''x_{3}+\cdots +a_{3n}''x_{n}=y_{3}''\\
\vdots\\
a_{nn}^{(n-1)}x_{n}=y_{n}^{(n-1)}
$$

#### 2. 후방대입

$$x_{n}=y_{n}^{(n-1)}/a_{nn}^{(n-1)},\\x_{n-1}=(y_{n-1}^{(n-2)}/a_{n-2,n-1}^{(n-1)})/a_{n-1,n-1}^{(n-1)} \\ \vdots \\x_{1}=(y_1- \Sigma^{n}_{i=2} a_{1i}x_i)a_{11} $$

### 예제2. 가우스 소거법으로 선형방정식 계의 해 구하기

$$
A= \begin{pmatrix}
  1 & 2  & 3 \\
  2 & -3  & 2\\
  3 & 1  & -1 \\
 \end{pmatrix}
 \begin{pmatrix}
  x_{1} \\
  x_{2} \\
  x_{3}
 \end{pmatrix}
 =
  \begin{pmatrix}
  6 \\
  14 \\
 -2 
 \end{pmatrix}
 $$

풀이 과정

$$
AX=Y\\
A^{1,1}X=Y^{1}\\
A^{2,1}X=Y^{2}\\
\vdots\\
\coprod X=Z
$$

A를 확장행렬로 나타낸 결과

$$
\begin{pmatrix}
  1 & 2  & 3 & : & 6 \Rightarrow R1\\
  2 & -3  & 2 & : & 14 \Rightarrow R2\\
  3 & 1  & -1 & : & -2 \Rightarrow R3\\
 \end{pmatrix}
$$

2,3,을 0으로 만들기 위해 빼줄것이다. $a_{1,1}$은 1이므로 상수배를 하면 소거 가능하다 $m_{y,x}= a{y,x}$를 0으로 만드는 수

$$
m_{2,1}=\frac{a_{2,1}}{a_{1,1}}=2 \Rightarrow R_2-m_{2,1}R1 \Rightarrow R_2-2*R1\\
m_{3,1}=\frac{a_{3,1}}{a_{1,1}}=3 \Rightarrow R_3-m_{3,1}R1 \Rightarrow R_3-3*R1\\
\\
=
\\
\begin{pmatrix}
  1 & 2  & 3 & : & 6 \Rightarrow R1\\
  2-(2*1) & -3-(2*2)  & 2-(2*3) & : & 14-(2*6) \Rightarrow R2\\
  3-(3*1) & 1-(3*2)  & -1-(3*3) & : & -2-(3*6) \Rightarrow R3\\
 \end{pmatrix}\\
 =
\\
\begin{pmatrix}
  1 & 2  & 3 & : & 6 \Rightarrow R1\\
  0 & -7  & -4 & : & 2 \Rightarrow R2\\
  0 & -5  & -10 & : & -20 \Rightarrow R3\\
 \end{pmatrix}
$$

$$
m_{3,2}=\frac{a_{3,2}}{a_{2,2}}=\frac{-5}{-7}=\frac{5}{7} \cdots
$$

계산결과

$$
\begin{pmatrix}
  1 & 2  & 3 \\
  0 & -7  & 4\\
  0 & 0  & -\frac{50}{7} \\
 \end{pmatrix}
 \begin{pmatrix}
  x_{1} \\
  x_{2} \\
  x_{3} \\
 \end{pmatrix}
 =
  \begin{pmatrix}
  6 \\
  2 \\
\frac{-150}{7}
 \end{pmatrix}
$$

$$
  x_{1}=1 \\
  x_{2}=-2 \\
  x_{3}=3 \\
$$


