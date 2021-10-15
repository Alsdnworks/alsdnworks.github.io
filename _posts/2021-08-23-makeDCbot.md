---
title: 간단한 디스코드 봇 만들기
categories:
  - python
tags:
  - bot
  - discord
toc: true
classes: wide
---

<br>

항공사진을 찍는 지인으로부터 특정 날짜에서 특정 공항의 이착륙하는 항공기리스트를 출력하는 프로그램을 만들어 달라는 의뢰를 받았다. 이미 깃허브에 이를 조회하는 파이썬 코드가있으나 이를 매번 파이썬으로 실행 시키는 힘들어 디스코드로 입출력을 받을수있게 해달라는 의뢰였다. discod.py를 사용해 입출력 구조만을 바꿔 쉽게 만들수있었고 이를 나중에 참고 가능하도록 포스팅한다.

## 1. 패키지 설치

cmd 관리자권한 실행후

> pip install discord
 
로 디스코드 패키지를 설치한다. 본 패키지는 conda에서 기본지원하지 않으므로 별도로 설치해줘야했다.

## 2. 디스코드 명령어 함수 작성 

패키지 임포트후 디스코드 명령과 관련된 부분을 작성한다. 데코레이터를 통해 디스코드 패키지 내부적을 사용될 기능을 정의한다.


- @client.event의 on_ready():<br>
  프로그램 실행시 실행될 함수이다. 내가 굳이 파이썬화면을 안보고있어도 정상 실행이 되었는지 확인이 가능하다.


- @client.command(): <br>
  사용자 함수를 정의할수있다. 입력값을 함수로 전달하는 방식에대해서는 뒤에서 설명한다.


- client.run(token): <br>
디스코드 봇에서 사용자를 식별하고 디스코드에서 봇과 서버를 연결하기 위해 사용이되는 토큰을 발급받아 입력한다. 이 토큰은 외부 Github와 같은 공개 저장소에 노출되면 사용할수없게되므로 주의 해야한다. 

~~~python

import discord
from discord.ext import commands

global result

#UBIKAIS#
client = commands.Bot(command_prefix='*')

@client.event
async def on_ready():
    print("봇 실행중")
    print('------')
    await client.change_presence(status=discord.Status.online, activity=discord.Game("***********"))

@client.command()
async def 버전(ver):
    await ver.send('****** BOT/ver *.*.* (beta)')

@client.command()
async def 출력(pln):
    await pln.send(process(str(pln.message.content)[4:]))

##################################################
#def process is here
##################################################

token=0##토큰입력
client.run(token) 

~~~

사용자 정의함수는 

> client = commands.Bot(command_prefix='*')

의 명령어 전처리자에 의해 실행된다. 함수명은 디스코드에서 전처리자와 입력되는 함수명으로 인자명.명령어를 통해 함수내의 명령을 작성가능하다.  디스코드로 출력하는 메세지는 인자명.send()를 통해 사용가능하다.

별도로 정의 되는 process함수의 리턴값을 출력하기 위해 `*출력(공백)인자`에서 앞의 4글자를 지워야했는데 이는 pln의 입력값인 pln.message.content에서 [4:]를 사용하는것으로 해결했다.

그렇게되면 process는 명령어를 제외한 입력값을 받을 수있게된다.

## 3. 사용자 함수 정의

process는
https://github.com/harrydrippin/airyday
의 코드를 바탕으로 작성되었다. 여러 입력값 대신 `-`로 구분되는 한개의 입력값을 받고 제외할 항공사 리스트를 받는것 외에 크게 다를건 없다.  

~~~python
def process(Ubikais):
    import requests
    import re
    import sys
    #제외필터
    tuple_string=("제외할 항공사 리스트")
    #명령어 분리
    search=(Ubikais.split('-')[0])
    Airport=(Ubikais.split('-')[1])
    Date=(Ubikais.split('-')[2])
    ###################
    #이하 airyday.py와 동일
    ###################
    return result            
    ##EOF
~~~