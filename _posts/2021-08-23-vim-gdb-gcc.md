---
title: 리눅스에서 C, C++ 컴파일하고 디버깅하기"
categories:
  - linux
tags:
  - 컴파일러
  - 디버거
---

#리눅스에서 C, C++ 컴파일하고 디버깅하기

**1. 컴파일러 설치**

gcc, g++, gdb설치

~~~
sudo apt-get install -y gcc #c컴파일러 설치
sudo apt install -y g++   #c++ 컴파일러 설치
sudo apt-get install -y gdb #gdb 디버거 설치
~~~
-y는 설치관련 모든 경고나 알림에 yes를 사용하는 접미사
 
**2. 소스코드 컴파일** 

테스트를 위한 코드를 작성해 test.c나 test.cpp로 저장해둔다.
bool 함수를 통해 a+b가 falsy한 경우에 hello_world를 출력하는 간단한 예제를 만들었다.
<details>
<summary>타이핑하기 귀찮다면</summary>
<div markdown="1">

~~~
bool func(int a, int b){
if (a+b==true)
	return true;
else return false;	
}

void main(){
a=1; b=0;
if(func(a,b))
	printf("hello_world");
}	
~~~

</div>
</details>
.c의 경우

>gcc test.c -g -o src

.cpp의 경우

>g++ -g -o src test.cpp

위의 명령어는 다음의 명령을 수행한다.
test.c(pp)를 -o뒤의 파일명인 src라는 바이너리파일로 컴파일하며 -g는 디버그 정보사용으로 저장한다.

**3. GDB로 디버깅하기**
ls로 실행파일이 생성되었는지 확인하고 src를 디버깅해봅시다.

gdb src -gdb

(gdb 대기화면)

run을 입력하면 전체 코드가 실행된다.

(true 리턴화면)

break 함수명

(함수에서 break된 모습)

함수의 시작점에서 중단점이 적중되면 다음과 같은 화면을 볼 수있다.

step을 입력해 한줄씩 실행하면 조건문에서 합이 1이기에 true값을 리턴하는것을 확인가능하다.

gdb에서 quit를 입력해 터미널로 나가 vi에서 함수가 false를 반환하도록 수정후 다시 gdb에서 제대로된 값을 반환하는지 확인해본다.

참고할만한 문서 [https://blankspace-dev.tistory.com/224](https://blankspace-dev.tistory.com/224)

> Written with [StackEdit](https://stackedit.io/).