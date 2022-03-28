---
title: 패턴인식 제 2강[KNU 2022-1]
categories:
  - KNU
tags:
  - PatternRecognition
  - openCV
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 패턴인식 필기노트 2

## 1.영상처리란

- 2차원 행렬에대한 연산
- 연산과정에서 행렬 원소의 변경
- 전체 영상에서 변화 인지하기 어려움

## 2.openCV 기초 함수 정리

### 2.1.openCV 창 제어

#### 윈도우 이름으로 윈도우 생성

> cv2.namedWindow(winname,[flags])

- winname : 윈도우 이름(str)
- flags : 윈도우 생성 시 사용할 플래그(bool)

|옵션명|설명|값|
|-|-|-|
|cv2.Window_AUTOSIZE|크기에 맞추어 윈도우 창의 크기를 자동 조정한다.|0|
|cv2.WINDOW_NORMAL|윈도우 크기 재조정 가능.|1|

#### 윈도우 이름에 해당하는 윈도우에 mat의 행렬을 표시

>cv2.imshow(winname,mat)

- winname : 윈도우 이름(str)
- mat : 표시할 행렬(numpy.ndarray)

#### 윈도우 이름에 해당하는 윈도우를 제거

>cv2.destroyWindow(winname)

- winname : 윈도우 이름(str)

#### 모든 윈도우를 제거

>cv2.destroyAllWindows()

#### 윈도우 이름에 해당하는 윈도우의 위치를 조정

>cv2.moveWindow(winname,x,y)

- winname : 윈도우 이름(str)
- x,y: 모니터상의 좌표

#### 윈도우 이름에 해당하는 윈도우의 크기를 조정

>cv2.resizeWindow(winname,width,height)

- winname : 윈도우 이름(str)
- width, height : 윈도우의 크기

#### 윈도우 제어 실습

~~~python
import numpt as np
import cv2

image = np.zeros((200,400),np.unit8) #8비트의 200*400 행렬생성
image.fill(200) #행렬의 모든 원소를 200으로 설정

title1, title2 = 'pos1', 'pos2'
cv2.namedWindow(title1,cv2.WINDOW_NORMAL) #윈도우 생성 flag의 가본값은 auto
cv2.moveWindow(title1,100,100) #윈도우 이름에 해당하는 윈도우의 위치를 조정
cv2.imshow(title1,image) #윈도우 이름에 해당하는 윈도우에 행렬을 표시
cv2.waitKey(0) #키보드 입력을 대기
cv2.destroyAllWindows() #모든 윈도우를 제거
~~~

### 2.2.콜백함수를 통한 이벤트 처리

#### 키보드 입력을 대기->반환(1byte)

>cv2.waitKey(delay)

|옵션명|설명|
|-|-|
|delay<=0|키 이벤트 무한 대기|
|delay>0|지연시간 키입력 없을시 -1 리턴|

#### 키보드 입력을 대기(2byte 지원으로 화살표키등 입력 가능)

>cv2.waitKeyEx(delay)

#### 키보드 콜백 실습

~~~python
#생략
case_dict={
ord('q'):"q키입력됨.",
ord('w'):"w키입력됨.",
ord('e'):"e키입력됨.",#ord()함수는 아스키코드를 숫자로 변환
0x72:"r키입력됨.",
0x250000:"좌측 화살표키 입력됨.",
2490368:"상단 화살표키 입력됨.",#십진수
0x270000:"우측 화살표키 입력됨.",
0x280000:"하단 화살표키 입력됨."
}
#생략
while True:
    key = cv2.waitKeyEx(100)
    if key == 27:#esc키 입력
        break
    try:
        result= case_dict[key]
    except KeyError:
        result = "입력된 키가 없습니다."
    print(result)
~~~

#### 사용자 정의 마우스 콜백 함수 등록

> cv2.setMouseCallback(winname,onMouse,param=None)

|옵션명|설명|
|-|-|
|winname|이벤트 발생을 확인할 윈도우 이름, 문자열|
|onMouse|마우스 이벤트를 처리하는 콜백함수 이름(콜백함수)|
|param|이벤트 처리 함수로 전달할 추가적 사용자 정의 인수|

#### 이벤트 처리, 제어 구현

>onMouse(event,x,y,flags,param=None)

|옵션|설명|
|-|-|
|event|마우스 이벤트 종류|
|x,y|마우스 이벤트가 발생한 좌표|
|flags|마우스 이벤트와 동시에 특수키(Shift,Alt,Crtl)를 눌렀는자의 여부확인|
|param|콜백함수로 전달하는 추가적인 사용자 정의 인수|

|event옵션|설명|값|
|-|-|-|
|cv2.EVENT_MOUSEMOVE|마우스 움직임|0|
|cv2.EVENT_LBUTTONDOWN|왼쪽 버튼 누르기|1|
|cv2.EVENT_RBUTTONDOWN|오른쪽 버튼 누르기|2|
|cv2.EVENT_MBUTTONDOWN|중간 버튼 누르기|3|
|cv2.EVENT_LBUTTONUP|왼쪽 버튼 떼기|4|
|cv2.EVENT_RBUTTONUP|오른쪽 버튼 떼기|5|
|cv2.EVENT_MBUTTONUP|중간 버튼 떼기|6|
|cv2.EVENT_LBUTTONDBLCK|왼쪽 버튼 더블클릭|7|
|cv2.EVENT_RBUTTONDBLCK|오른쪽 버튼 더블클릭|8|
|cv2.EVENT_MBUTTONDBLCK|중간 버튼 더블클릭|9|
|cv2.EVENT_MOUSEWHEEL|마우스휠 돌리기|10|
|cv2.EVENT_MOUSEHWHEEL|마우스 가로휠|11|

|flags옵션|설명|값|
|-|-|-|
|cv2.EVENT_FLAG_LBUTTON|왼쪽 버튼 누르기|1|
|cv2.EVENT_FLAG_RBUTTON|오른쪽 버튼 누르기|2|
|cv2.EVENT_FLAG_MBUTTON|중간 버튼 누르기|4|
|cv2.EVENT_FLAG_CTRLBUTTON|Ctrl키 누르기|8|
|cv2.EVENT_FLAG_SHIFTBUTTON|Shift 키 누르기|16|
|cv2.EVENT_FLAG_ALTBUTTON|Alt키 누르기|32|

#### 마우스 콜백 실습

~~~python

#생략

~~~

### 2.3.트랙바 생성

#### 트랙바를 생성한 후 지정한 윈도우에 추가하는 함수

>cv2.createTrackbar(trackbarName,winname,value,count,onChange)

|옵션명|설명|
|-|-|
|trackbarName|윈도우에 생성 트랙바 이름|
|winname|트랙바의 부모 윈도우(트랙바 이벤트 발생을 체크하는 윈도우)|
|value|트랙바 슬라이더의 위치를 반영하는 값(정수)|
|count|트랙바 슬라이더의 최댓값, 최솟값은 항상 0|
|onChange|트랙바 슬라이더의 값이 변경될때 호출되는 콜백 함수|

#### 트랙바 슬라이더의 위치가 변경 될때마다 호출된다. createTrackbar()의 마지막 인수와 이름이 같아야한다

>onChange(pos)

pos(슬라이더 위치)

#### 지정한 트랙바의 슬라이더 위치를 반환한다

>cv2.getTrackbarPos(trackbarName,winname)

#### 지정한 트랙바의 슬라이더 위치를 설정한다

>cv2.setTrackbarPos(trackbarName,winname,pos)

### 2.4.도형 생성

#### 직선을 생성하는 함수

~~~python
cv2.line(img, pt1, pt2, color, thickness=1)
#안티 앨리어싱 적용
cv2.line(img, pt1, pt2, color, thickness=1, cv2.LINE_AA)
~~~

#### 직사각형을 생성하는 함수

~~~python
pt1,pt2=(50,50),(250,150)
roi=(50,200,200,100)

cv2.rectangle(img, pt1, pt2, color, thickness=1,)
cv2.rectangle(img, pt1, pt2, color, thickness=1, cv2.LINE_4)#4방향 연결선
cv2.rectangle(img, roi, color, green, cv2.FILLED)#내부 채움
~~~

#### 원을 생성하는 함수

~~~python
cv2.circle(img, center, radius, color,[thickness[, lineType[, shift]]])
~~~

|옵션명|설명|
|-|-|
|img|이미지|
|center|원의 중심 좌표|
|radius|원의 반지름|
|color|색상|
|thickness|선의 굵기|
|lineType|선의 형태|
|shift|좌표에 대한 비트 쉬프트 연산|

#### 타원을 생성하는 함수

~~~python
cv2.ellipse(img, center, axes, angle, startAngle, endAngle, color[, thickness[, lineType[, shift]]])
~~~

|옵션명|설명|
|-|-|
|img|이미지|
|center|원의 중심 좌표|
|axes|타원의 반지름|
|angle|타원의 각도|
|startAngle|(호)시작 각도|
|endAngle|(호)끝 각도|
|color|색상|
|thickness|선의 굵기|
|lineType|선의 형태|
|shift|좌표에 대한 비트 쉬프트 연산|

![pc](/assets/img/cv2Ecl.png)
![gist](https://user-images.githubusercontent.com/79889482/159846760-44c2107d-f7d3-47e7-99b3-b82b5b72f025.png)

#### 텍스트를 생성하는 함수

~~~python
cv2.putText(img,text,org,fontFace,FontScale,color[,thickness[,lineType[,bottomLeftOrigin]]])
~~~

|옵션명|설명|
|-|-|
|img|이미지|
|text|문자열|
|org|시작좌표|
|fontFace|폰트|
|fontScale|확대비율|
|color|색상|
|thickness|선의 굵기|
|lineType|선의 형태|
|bottomLeftOrigin|원점좌표|