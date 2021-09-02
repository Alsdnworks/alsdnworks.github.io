---
title: 리눅스에서 C, C++ 컴파일하고 디버깅하기
categories:
  - linux
tags:
  - compiler
  - debugger
toc: true
classes: wide
---

<br>

## 1. 컴파일러 설치
<br>

gcc, g++, gdb설치
~~~
sudo apt-get install -y gcc #c컴파일러 설치
sudo apt install -y g++   #c++ 컴파일러 설치
sudo apt-get install -y gdb #gdb 디버거 설치
~~~
-y는 설치관련 모든 경고나 알림에 yes를 사용하는 접미사이다.

<br> 

## 2. 소스코드 컴파일 
<br>

테스트를 위한 두가지 코드를 작성해 test.c 나 test.cpp로 저장해둔다.<br>
1. bool 함수를 통해 a+b가 falsy한 경우에 hello_world를 출력하는 예제 
2. 수(n)를 입력받아 n번째 피보나치수를 출력하는 예제 (BOJ 2747)


.c의 경우

>gcc test.c -g -o src

.cpp의 경우

>g++ test.cpp -g -o src 

위의 명령어는 다음의 명령을 수행한다.<br>

| 명령어 | 기능 |
|--|--|
|g++(gcc)| 컴파일러 호출|
|(파일명)|  |
|-g| 디버그 정보 사용으로 저장한다.|
|-o src| src라는 파일로 컴파일한다. |

<br>
<br>

## 3. GDB로 디버깅하기
<br>

ls로 실행파일이 생성되었는지 확인하고 src를 디버깅해보도록한다.

>gdb src

아래의 표는 이틀간 사용해보며 정리해본 필수 명령들이다.

| 명령어(약어) | 기능 | 사용예시 및 붙임 |
|--|--|-- |
|list (l)| 소스코드 10줄씩 출력 | set참조 |
|run (r)| 디버깅실행 | |
|break (b)| 중단점 설정 | b 라인번호 또는 함수명 |
|clear (cl)| 옵션해제 | cl 라인번호 또는 함수(변수)명 |
|delete (d)| 모든옵션해제 | 중단점, 감시점 등등 일괄해지 |
|step (s) | VS의 '한 단계 실행' | s n 으로 n 단계만큼 실행  |
|next (n)|VS의 '프로시저단위 실행'(함수통과) | |
|cont (c)| VS의 '계속' | |
|print (p)| 변수의 값 표시 | p 변수명, p (자료형) 변수명, p *포인터명 |
|kill (k)| 디버깅중인 프로그램 종료 | |
|quit (q)| 종료 | 디버깅중에는 k 와 같은 기능 수행 |
|set| 변수 및 기능설정 | 변수의 값 수정: set variable 변수명=값<br>list의 출력범위 설정: set listsize 값 |
|display| 변수값 상시표시 | 사용례는 p 와같음  |
|watch| 변수값 변동시 전/후값 출력(감시) | 사용례는 p 와같음 |


<br>*예제1.*<br>
step을 입력해 한줄 씩 실행해서 조건문내 합이 1이기에 true값을 리턴하게 된다. 문자열은 false일때 출력되므로 a를 0으로 바꾸어 falsy한 값을 출력시켜 문자열이 출력되도록 해보자 

func함수에 break를 걸고 변수 값을 변경한뒤 진행시킨다. print로 변수의 값을 확인 가능하다. <br>

>set variable a=0<br>
>c

![img1](/assets/img/2_img1.png)

a+b는 0로 falsy하므로 원하는 출력이 나오고 있음을 확인 가능하다.


<br>*예제2.*<br>
반복문을 사용해 fibo&#91;i&#93;의 값은 fibo&#91;i-1&#93;+ fibo&#91;i-2&#93;이므로 점점 커지는걸 확인 해보도록 한다. 

여기에서 fibo&#91;i&#93;을 확인하기위해 watch를 사용해보겠다.<br> 
반복문이시작되는 8번라인에 중단점을 걸고 fibo&#91;i&#93;를 관측하면.<br>
fibo&#91;i&#93;가 나타내는 값이 0으로 초기화 된 이후 메모라이징됨을 확인 가능하다.
>break 8<br>
>run<br>
>10<br>
>watch fibo&#91;i&#93;<br>
>c<br>

함수가 끝나 watchpoint가 해제되면 continue하여 출력을 확인해본다.<br>
원하는 값이 나오도록 계산되고 있음을 확인가능하다.<br>
비쥬얼 스튜디오와 마찬가지로 반복문에서 걸린 break는<br>
c(계속, 최초 사용 후 엔터로 사용가능)를 누를때마다 변화된 값을 출력한다.

![img2](/assets/img/2_img2.png)

>참고한 문서 
http://korea.gnu.org/manual/release/gdb/gdb.html

> Written with [StackEdit](https://stackedit.io/).