---
title: concurrent로 파이썬에서 멀티프로세싱 적용하기
categories:
  - PYTHON
tags:
  - concurrent
  - capstone
toc: true
---


졸업작품(캡스톤디자인)에 사용할 데이터를 만들기위해 구글어스로 표시되는 Keyhole Markup Language(.kml)에서 좌표만을 출력해 CSV 파일로 저장하고자 한다.
kml은 xml기반이라 BeautifulSoup의 html.parser를 통해 파싱하는 것이 가능하다.

## 1. 누구나 그럴싸한 계획을 갖고 있다. 처맞기 전까지는

그야말로 계획은 좋았다. 그러나 며칠간 크롤링된 kml 데이터의 양이 지금까지 처리해본 데이터에 비해서 너무 큰 데이터였다는 것이었다. 물론 빅데이터 엔지니어에게는 한 줌 데이터였겠지만 내 컴퓨터로는 이틀이 지나도 끝이 나지 않았고 설상가상으로 버튼 한번 잘못 눌렀다가 꺼져버렸다는 것이다!
~~아 오늘 회식이라고요?? (전원버튼꾹)~~

가장 큰 문제는 단일코어로 번갈아 가며 수행하고 있기에 자원이 너무 낭비된다는 것! 파이선이 겨우 CPU의 18%도 사용하지 못하고 있는 상황이었다.

![idiot](/assets/img/idiot.png)

~~뭐,,하세요,,?~~

## 2. 이번에는 이렇게 하면 되겠다&#33;

![Enlightenment](/assets/img/Enlightenment.jpg)

우선 처리된 데이터는 pass 하는 방식으로 재작성했고 그 과정에서 지금껏 시도해본 적 없는 멀티프로세싱을 적용해보기로 했다.

> 참고자료:

자료에서는 `concurrent.futures`를 통해 `map()`으로 멀티프로세싱을 적용하는 방식을 제시하고 있다.
자료를 따라 glob에서 만들어진 각각의 파일을 읽어서 analysis에 전달하는 방식으로 수정한 결과는 다음과 같다

~~~python
    #기존 방식
    import glob

    imported_files = glob.glob("*.kml")
    for file in imported_files:
        analysis(file)
########################################
    #멀티스레딩 적용
    import concurrent.futures
    import glob

    with concurrent.futures.ProcessPoolExecutor() as executor:
        imported_files = glob.glob("*.kml")
        executor.map(analysis, imported_files)
~~~

외부코드 허용 옵션으로 인해 살짝 코드 밖에서 수정할 사항이 있었지만 vscode의 경우 단순히 `launch.json`에 `"justMyCode": false`를 추가하면 된다. 사실 이거 출력에 안 떠서 어디가 문제인지 애를 먹긴 했다.

![asd](/assets/img/161269103-764e305f-2a37-4239-a33c-862bc4f2512b.png)

퇴근이다!

## 3. 윈도우에 적용하기

일단 잘 되는것같아 깃헙에 올려두고 기숙사에와서 실행해보는데,, 안된다!!

~~~python
RuntimeError: 
이전에 새 프로세스를 시작하려고 시도했습니다. 현재 프로세스가 부트스트랩 단계를 완료했습니다. 이것은 아마도 당신이 시작하기 위해 포크를 사용하지 않는다는 것을 의미합니다 자식 프로세스와 적절한 관용구를 사용하는 것을 잊었습니다. 메인 모듈에서: __name__ == '__main__'인 경우: freeze_support()…. 프로그램이 다음과 같은 경우 "freeze_support()" 행을 생략할 수 있습니다. 실행 파일을 생성하기 위해 고정되지 않습니다.
~~~

이게 무슨 소리인가.. fork?? 깃에 문제가 있나 싶었지만, fork()는 새 프로세스를 만드는 작업이며, 윈도에서는 지원이 안 되는 것이라고 이해 했다. 해결법은 다음 블로그를 참고했다. 참고로 우분투에서는 다음 코드 블록이 있건 없건 문제없이 돌아간다.

> 참고자료: <https://purplechip.tistory.com/39>

~~~python

from multiprocessing import freeze_support
if __name__=='__main__':
    freeze_support()
    #이하 main 스크립트
~~~

## 4. 결과물

일단 문제없이 돌아가긴 한다만, 기왕이면 좀 더 좋은 환경에서 분석하는 것이 좋을 것 같아, 조만간 과 사무실에 있는 제온 서버컴을 사용 허가받아서 분석할 예정이다.

~~~python
#파일 분석기 

#멀티프로세싱을 위한 패키지
import concurrent.futures
#윈도우용 예외처리를 위한 패키지
from multiprocessing import freeze_support

#분석용 패키지
import os
import glob
from bs4 import BeautifulSoup as bs
import pandas as pd

#분석 수행 함수
def analysis(file):
    if os.path.isfile('proc/'+str(file[:-4])+'.csv')==False:
        df=pd.DataFrame()
        fr = open(str(file), 'r') 
        lines =fr.read() 
        fr.close()
        xmls=bs(lines, 'html.parser')
        cords=xmls.findAll('coordinates')
        for cord in cords:
            cord.string=cord.string.lstrip('<coordinates>').rstrip('</coordinates>')
            coord=cord.string.split(',')
            df = df.append({'y':coord[0].strip(),'x':coord[1].strip()}, ignore_index=True)
            df.to_csv('proc/'+str(file[:-4])+'.csv', index=False)
    else:
        print('proc/'+str(file[:-4])+'.csv already exists')  
    #생략

#_______________________________________________________________________________  
##MAIN_start

#윈도우용 하위 프로세스를 재귀적으로 생성하지 않으려면 이것이 필요하다
if __name__=='__main__':
    freeze_support()

    #출력물 저장위치 설정
    if(os.path.isdir('proc/')==False):
        os.makedirs('proc/')  

    #멀티스레딩적용
    with concurrent.futures.ProcessPoolExecutor() as executor:
        imported_files = glob.glob("*.kml")
        executor.map(analysis, imported_files)
~~~
