---
title: 컴퓨터 네트워크 제10강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
toc: true
---  

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터 네트워크 필기 노트 10

## 1. TCP/IP 기본 구조 

![img0](/assets/img/image.png)

Transmission Control Protocol/Internet Protocol으로 1970년 개발되어 1982년 미군(DARPA)에서 표준 네트워킹으로 제정되었다.
DoD모델을 표준으로 사용하며 이는 4계층 모델이라고도 불린다.


TCP/IP의 주요 특징은 다음과 같다

- 공통의 응용프로그램 제공
- 동적 경로 할당
- 패킷교환
- 연결형 및 비연결형 서비스 제공

(중요해서 한번 더 나오는거다)

## 2. TCP/IP 계층의 역할

> 데이터 링크 계층: 물리적 통신매체를위한 서로 다른 제어절차를 제공, 네트워크 인터페이스

대표적인 프로토콜로는<br>
물리주소를 IP주소로 변환하는 프로토콜인 RARP(Re: Address Resolution Protocol) <br>
IP주소를 물리주소로 변환하는 프로토콜인 ARP(Address Resolution Protocol)

>인터넷 계층: 네트워크 상에서 패킷의 이동을 처리(Packet Routing)

대표적인 프로토콜로는<br>
IP(Inernet Protocol)<br>
ICMP(Internet Control Message Protocol)<br>
IGMP(Internet Group Management Protocol)<br>

>전송 계층: 호스트간의 데이터의 전송을 처리

대표적인 프로토콜로는<br>
TCP(Transmissional Control Protocol)<br>
UDP(User Datagram Protocol)<br>

>응용계층: 응용프로그램의 제어를 처리

대표적인 프로토콜로는<br>
TCP: FTP, SMTP<br>
UDP: TFTP,DNS,BOOTP<br>
IP : traceroute<br>
ICMP: ping<br>

## 3. 인터넷 주소

인터넷에 연결되어있는 호스트를 식별하기위해 3종류의 주소를 사용가능하다.
|주소| |
|-|-|
|물리주소|네트워크 내에서 호스트를 식별하는 물리적 하드웨아 주소로 네트워크 인터페이스의 주소이다.|
|인터넷주소(IP주소)|서로 다른 네트워크 간에 호스트를 식별하는 주소|
|포트 주소|프로세스를 식별하는 포트 번호|

**IP주소** <br>4바이트로 구성되며, 이는 네트워크 인터페이스에서 호스트를 식별하는 주소이다.<br>
**포트주소** <br>2바이트로 구성되며, TCP,UDP에의해 응용프로그램을 식별.<br>

OSI 모델에서 네트워크 계층의 기능으로서 인터넷의 개별 네트워크 구조를 숨겨 모든 호스트를 연결하는 1개의 가상네트워크로 보이도록한다.

비연결형서비스: 인터넷 계층의 투명성으로 경유 데이터링크,라우터정보 무시, 호스트의 주소와 데이터그램을 전송하는 것을 말한다.

## 4. 데이터그램

![img1](/assets/img/ip-v4-datagram-header.png)

위 그림은 IP계층의 패킷 데이터그램으로 데이터부분을 제외한 헤더부분에 패킷에 대한 정보가 들어있다.(metadata)

데이터그램은 네트워크 링크에서 허용되는 프레임당 데이터의 최대길이(MTU;Maximum Transmission Unit)에 적합하게 분할되었다가 목적지 호스트에서 재조립된다. 이를 IP단편화라한다.

## 5. 라우팅

IP 데이터그램이 목적지 호스트까지 진행하면서 경유할 경로를 결정하는 것

IP : 송신자, 수신자 그리고 그 사이의 경로 상에 있는 모든 라우터들이 IP 데이터그램 전달에 관여

TCP : 송신자와 수신자만 TCP 세그먼트 전달에 관여


