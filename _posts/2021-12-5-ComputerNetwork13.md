---
title: 컴퓨터 네트워크 제13강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
toc: true
---  

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터 네트워크 필기 노트 13

## 1. LAN참조모델

|OSI|LAN|LAN->OSI|
|-|-|-|
|응용|-|-|
|표현|-|-|
|세션|-|-|
|전송|-|-|
|네트워크|논리링크제어(LLC)|데이터링크|
|데이터링크|매체접근제어(MAC)|데이터링크|
|물리|매체접근 유니트(MAU)/케이블(매체)|물리|

<div class="notice">
  <h4>LAN->OSI은 LAN 계층이 속하는 OSI계층을 나타냄</h4>
</div>


|계층|설명|
|-|-|
|논리링크제어(LLC)| MAC에의해 확보된 데이터 전송권한을 통해 흐름/오류제어, 순서화 및 연결관리수행,<br> 데이터링크 계층의 일부로 다중접근돠 인접 노드간 데이터전송을 수행| 
|매체접근제어(MAC)| MAU/LLC간 데이터 전송매체의 사용권을 모든 노드에 균등하게 분배,<br> CSMA/CD, 토큰링, 토큰버스가 속함|
|매체접근장치(MAU)| signaling, encoding 기능과 매체를 다루는 기능을 수행|
|물리매체 (케이블)| 전기적 신호를 전달하는 역할을 수행(꼬임선, 동축케이블, 광섬유, 전파)|

## 2. 무선LAN

적외선이나 전파를 사용해 노드들을 연결한 LAN

### 2.1. 특징

**장점**

- 이동중 통신
- 빠른 네트웤 구축
- 노드 배치 형태의 영향없음
- 배선의 번거로움 없음

**단점**

- 보안 취약성
- 간섭현상 발생
- 데이터 전송 속도 저하 

### 2.2. 무선랜표준

|종류|전송속도|대역|
|-|-|-|
|802.11a|54Mbps|5.7GHz|
|802.11b|11Mbps|2.4GHz|
|802.11g|54Mbps|2.4GHz|
|802.11n|600Mbps|2.4/5GHz|

### 2.3. 애드혹

![img1](/assets/img/093016_2128_Modesofoper4.png)

(https://www.tech.windelslife.com/modes-of-operation/)

- 무선 전파 도달범위내 노드끼리 직접 통신하는 방식
- IBSS(Independent Basic Service Set)단위로 네트웤 구성
- IBSS내에서 노드간 통신이 가능하나 다른 IBSS와는 통신불가

### 2.4. 인프라스트럭쳐

![img1](/assets/img/093016_2128_Modesofoper2.png)

- 허브나 라우터가 장착된 AP(AccessPoint)로 무선 전파를 통신하는 방식
- BSS(Basic Service Set)는 단일 AP를 사용해 네트웤 구성한 무선 LAN
- ESS(Extended Service Set) BSS 집합을 하나의 BSS처럼 관리하는 무선 LAN

## 3. 고속 LAN

기존 프로토콜을 사용하며 고속도로 데이터 전송을 수행하는 LAN

### 3.1. 고속 이더넷

전송 가능한 케이블의 최대길이를 단축함으로 100Mbps이상으로 속도를 향상<br>
CSMA/CD(802.3`u`)<br>

- 100BASE-TX – 주로 성형 LAN; CAT.5 UTP (최대 100m)
- 100BASE-T4 – 100BASE-TX와 유사; CAT.3, 4 UTP (최대 100m)
- 100BASE-FX – 광섬유 (최대 400m)

**매체(꼬임선 케이블)용어**

|용어|뜻|
|-|-|
|CAT|Category(CAT.5E등)|
|STP|shielded twisted pair(꼬임선마다 쉴드처리)|
|S/UTP|shielded twisted pair(외부 쉴드처리)|
|S/STP|shielded twisted pair(외부, 꼬임선 쉴드처리)|
|UTP|unshielded twisted pair|
|FTP|foiled twisted pair|

### 3.2. 기가비트 이더넷

1Gbps 이상의 속도를 유지하는 이더넷

- 1000 BASE-X (IEEE 802.3z)
- 1000BASE-SX (광섬유, 단파장 레이저)
- 1000BASE-LX (광섬유, 장파장 레이저)
- 1000BASE-CX (동축케이블, 25m이내 단거리 전송)
- 1000 BASE-T (IEEE 802.3ab,최대 100m)
- 10 Gbps Ethernet (광섬유 이용, 802.3ae)

### 3.3. FDDI

100Mbps를 지원하는 Fiber Distributed Data Interface로 ANSI표준에서 ISO표준으로 변경됨<br>
멀티토큰을 사용하는 반대 방향의 광섬유 2중링을 token passing 방식으로 운용된다.

