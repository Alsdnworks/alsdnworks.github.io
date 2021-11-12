---
title: 컴퓨터 네트워크 제9,10강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
toc: true
---  

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터 네트워크 필기 노트 9,10

## 1. TCP/IP 개요 및 구조

Transmission Control Protocol/Internet Protocol으로 1970년 개발되어 1982년 미군(DARPA)에서 표준 네트워킹으로 제정되었다.
DoD모델을 표준으로 사용하며 이는 4계층 모델이라고도 불린다.

4계층 모델 사진 그려서 넣을것

TCP/IP의 주요 특징은 다음과 같다

공통의 응용프로그램 제공
동적 경로 할당
패킷교환
연결형 및 비연결형 서비스 제공

||||
|-|-|-|
|응용계층|응용프로세스
|
TCP: FTP,SMTP,Telnet
/n
UDP: TFTP,DNS,BOOTP
/n
IP:Traceroute
/n
ICMP: ping
|
|전송계층|호스트간 데이터전송 지원|TCP,UDP|
|인터넷계층|패킷 라우팅|IP,ICMP,IGMP|
|데이터링크계층|네트워크 인터페이스|ARP,RARP|

호스트식별
|주소|설명|
|-|-|
|물리주소(MAC)|네트워크 인터페이스 주소로 네트워크내에서 호스트를 식별하는데 사용|
|인터넷주소(IP)|서로 다른 네트워크간 호스트 식별 논리주소|
|포트주소|프로세스를 식별하는 포트 번호|

캡슐화
데이터에 각 계층 정보를 추가하는 것
그림 넣을것


IP 개요


비연결 서비스

데이그그램

IP단편화

인터넷에서의 라우팅

UDP

TCP