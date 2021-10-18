﻿---
title: 컴퓨터네크워크 제 2강[KNU 2021-2]
categories:
  - KNU
tags:
  - Network
  - modulation
toc: true
---  

# 👨‍💻🏫KNU 2021-2 SW & media 컴퓨터네크워크 필기노트 2

## 1. 데이터 통신의 개요

정보원->전송매체->수신체

메세지는 전송매체를 통해 전송된다.

## 2. 변조(modulation)

전송 신호(baseband signal)를 고주파의 정현파인 반송파(carrier signal)에 싣는 과정


**아날로그 변조** 

| 이름 | 변조 방식 | 예 |
|--|--|--|
| AM | 진폭변조 |  |
| FM | 주파수변조 |  |
| PM | 위상변조 |  |

![cn1](/assets/img/Amfm.gif)

**디지털 변조**

| 이름 | 변조 방식 | 예 |
|--|--|--|
| ASK | 진폭편이변조 |  |
| FSK | 주파수편이변조 |  |
| PSK | 위상편이변조 |  |


## 3. 펄스
매우 짧은 시간 진행되는 네모꼴의 전자기 파형
`진폭`, `폭`, `시간에 따른 상대적 위치` 

### 3.1. 펄스코드 변조
아날로그 신호를 디지털 신호로 바꾸는 과정(PCM)
- 표본화 
- 양자화
- 부호화

![cn2](/assets/img/pcm.jpg)

## 4. 전송코드

|전송코드|비트수|설명|
|-|-|-|
|Baudot코드| 2<sup>5</sup>|텔렉스코드로 불리며 알파벳을 표현가능하다.|
|ASCII코드| 2<sup>7</sup>|미국표준 정보교환코드로 숫자, 영문 대소문자와 특수문자를 표현가능하다. LSB에 1개 패리티비트를 포함해 8비트로도 사용된다.|
BCD코드| 2<sup>4</sup>|10진수용으로 0~9를 표현하기위한 최소 비트수, 컴퓨터 내부코드로 사용된다.| 
|EBCDIC코드| 2<sup>8</sup>|IBM 컴퓨터 내부코드로 사용된다.|
|유니코드| 2<sup>16</sup>|만국 공용 문자코드로 26언어 문자와 특수기호를 지원한다.|