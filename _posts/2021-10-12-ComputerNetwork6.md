---
title: 컴퓨터 네트워크 제6강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
toc: true
---  

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터 네트워크 필기 노트 6

## 1. 주소지정 방식과 종류

| | |
|-|-|
|계층 수|단일, 복수계층|
|서브 네트워크 주소|물리, 논리 주소|
|동일 주소사용 지국|단일지국, 복수지국(모든:BROADCASTING, FLOODING주소,그룹:복수, 그룹주소)|
|주소할당 모드|STATIC,DYNAMIC|

- MAC 주소: 데이터링크 계층에서 사용되는 48bit(16진수) 주소
- IP 주소: 네트워크계층에서 사용되는 32(10진수(PIV 4))bit 주소

## 2. 오류와 오류율

### 2.1 오류제어

오류란 간섭으로 인해 송신데이터와 수신데이터가 일치하지 않는 것으로 `잔류오류율`은 (오류가 있는 비트 수) / (전송된 총 비트 수)이다.
이러한 오류율을 낮추기 위해 `오류제어`를 사용하며 방식으로는 오류 검출 후 재전송하는 `후진 오류제어`, 오류검출 수정하는 `전진 오류제어`를 사용한다.

### 2.2. 오류검출 방식

- 귀환오류제어
 
지국 사이의 역방향 채널을 사용하는 오류제어방식으로 `결정 귀환(ARQ)` `정보귀환` `복합귀환`으로 나뉜다.

|검출법|설명|
|-|-|
|ARQ|오류 검출 위치가 수신 측에 있는 경우 오류가 있으면 REJ 아닐 경우 ACK를 보내 재송을 요청한다.|
|정보귀환| BCK에코를 통해 송신 측이 전송한 데이터를 재수신해 오류 여부를 검사한다 |
|복합귀환|결정 귀환/정보귀환 또는 결정 귀환/전진  오류제어를 복합 사용하여 오류를 검출한다|

|ARQ|설명|
|-|-|
|STOP-WAIT|1개의 데이터 프레임 송신 후 ACK, REJ 또는 시간 초과 까지 대기|
|GO-BACK-N(연속적)| 여러 데이터프레임 전송 후 대기, NAK(N)으로 블록의 특정 칸에서 오류 발생 시 그 부분부터 다시 보낸다;슬라이딩 윈도 방식. |
|Selective-Repeat(연속적)|여러 데이터프레임 전송 후 대기, REJ수신된 프레임만 재송, GO-BACK-N보다 효율이 높으나 재배열이 필요한 단점이 있다.|
|적응적|오류 발생률을 송수신 측이 주고받으며 프레임의 길이를 동적으로 변경한다. 구현 방법이 복잡하다.|

- 전진 오류제어
 
 수신 측에서 오류검출, 정정한다. 역방향 채널 제공이 어려운 경우 주로 사용되며, 데이터프레임에 해밍코드 같은 오류 정정 코드를 사용해 오류를 수정 가능하다. 단 오류 정정 코드(잉여비트) 크기만큼 전송효율이 떨어지는 단점이 있다.  
  
- 순환잉여검사
 
비트 블록 단위로 검사하는 방식으로 비트의 다항식 표현을 M(X)로 나타내며 생성다항식G(X)로 부호화한다. 


1. M'(X)=M(X)xX<upper>m<upper> (G(X)=X<upper>2<upper>+X+1이면 최대차수가 m이므로 2이다)
2. M'(X)/G(X)=M"(X)..R(X)
3. F(X)=M'(X)+R(X)
   
이러한 과정으로 생성된 F(X)/G(X)의 R(X)가 없다면 오류가 없는 것으로 간주한다
  
- 체크섬 검사
  데이터블록을 세금만 특화 하여 2진수 화한 후 만들어진 합을 1의 보수화시켜 만들어진 검사 합을 보낸다. 수신 측에서는 합을 체크섬과 더했을 때 그 합의 1의 보수 값이 0이면 오류가 없는 것으로 간주한다. 

- 패리티 비트
 단순 패리티 검사에서는 블록의 비트 중 1의 비트 수가 짝수(짝수 패리티) 또는 홀수(홀수 패리티)가 되도록 패리티 비트를 추가한다. 블록 테이블을 통해 2차원 좌표에서 패리티를 검출하는 2차원 패리티 검사방식도 사용된다. 

