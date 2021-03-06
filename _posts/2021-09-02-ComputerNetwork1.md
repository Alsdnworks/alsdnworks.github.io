---
title: 컴퓨터네크워크 제 1강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
toc: true
---

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터네크워크 필기노트 1

## 1. 통신 기술과 데이터 통신망🌐🤖

### 1.1. 컴퓨터 시스템사이의 통신 
![img1](/assets/img/network_1.png)
컴퓨터구조론에서 지겹게 본 그것 맞다.

			
### 1.2. 분산시스템(distributed systems)
컴퓨팅 자원을 `공유` 하고 `확장 축소`에 용이하게 하며 <br> 
오류에대한 `신뢰도`를 높일 수 있도록 자원을 분산키켜 작업을 처리하는 시스템<br>
- 강연결 분산시스템 (CPU 내부의 레지스터 버스, 회로기판..) 
- 약연결 분산시스템 (PAN💁, LAN🏠, MAN🏙, WAN🌏..)

### 1.3. 데이터 통신망의 정의

컴퓨터 시스템에 의한 `데이터 처리 기술`과 통신 시스템에 의한 `데이터 전송 기술`이 결합된<br> 데이터 통신 기술과 망기술의 융합으로 
`약연결 분산 시스템`에 속한다.(=컴퓨터통신망)

### 1.4. 컴퓨터통신망의 목적
- 자원의 공유
- 처리기술의 분산 (1개의 고성능 컴퓨터 <<< AWS) 
- 신뢰도 향상 안전성 보장 (분산저장! 블록체인!!⛓)
- 호환성 확대

## 2. 데이터 통신 시스템📞

### 2.1. 데이터 통신 시스템의 구성

| 구성 요소 | 장치 |
|--|--|
|단말장치|Data Terminal Equipment(DTE)|
| 데이터 전송회선 | -신호변환장치(DCE): 모뎀(DAC), DSU <br> -통신회선 |
|통신제어장치|CCE, CCP|
|컴퓨터|  |

### 2.2. 데이터 통신 시스템의 기능

- 전송 시스템 활용
- 접속
- 동기화
- 교환관리
- 오류검출과 정정
- 흐름제어
- 주소지정
- 라우팅
- 복구
- 메세지형식화
- 보호
- 시스템 관리

### 2.3. 통신 소프트웨어

데이터 전송회선과 통신제어장치를 사용하여 컴퓨터와 단말기 사이 정보 송수신을 위한 프로그램


## 3. 통신 프로토콜🤝

### 3.1. 통신 프로토콜의 정의
통신을 원하는 개체간 어떻게 통신할지 정해놓은 규약이다.

- 구문(무엇을) : 데이터 형식이나 신호수준 정의
- 의미(어떻게) : 전송의 조정, 패리티비트와 같은 제어정보 
- 타이밍(언제) : 전송속도 조절, 전송 순서 조정 등을 포함
  
### 3.2. 컴퓨터 통신망 구조 
컴퓨터 통신을 위한 프로토콜은 초기 여러 회사에서 선점 시도하였으나 현재는 국제표준기관인 ISO의 OSI7계층모델을 사용한다.<br>
Computer Network Architecture<br>
IBM- SNA<br>
DEC- DNA<br>
. . .<br>
ISO- `OSI 7 Layered Reference Model`<br>
![img1](/assets/img/network_2.png)

> Written with [StackEdit](https://stackedit.io/).
