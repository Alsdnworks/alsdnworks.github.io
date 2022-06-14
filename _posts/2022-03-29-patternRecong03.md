---
title: 패턴인식 제 3강[KNU 2022-1]
categories:
  - KNU
tags:
  - PatternRecognition
  - openCV
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 패턴인식 필기노트 3

## 1.영상처리 과정

- 영상 파일 읽어 행렬에 저장
- 행렬 연산 과정의 원소를 직접 확인(디버깅 등)
- 처리된 행렬을 영상 파일로 저장

## 1.1. 영상파일 처리 함수

> cv2.imread(filename, [flags=])

지정한 영상파일로부터 영상을 적재한 후, 행렬로 변환한다.

- filename: 영상파일의 이름
- flags : 적재한 영상을 행렬로 반환할 때 컬러 타입을 결정하는 상수

|옵션명|설명|
|-|-|
|cv2.IMREAD_UNCHANGED|입력된 파일에 정의된 타입의 영상을 그대로 반환(알파채널 포함)|
|cv2.IMREAD_GRAYSCALE|명암도 영상으로 변환하여 반환|
|cv2.IMREAD_COLOR|컬러 영상으로 변환하여 변환|
|cv2.IMREAD_ANYDEPTH|입력 파일에 정의된 깊이에 따라 16/32비트 영상으로 변환, 살정되지 않는다면 8비트 영상으로 변환|
|cv2.IMREAD_ANYCOLOR|입력된 파일에 정의된 타입의 영상을 그대로 반환|

> cv2.imwrite(filename, img[params=])

img 행렬을 지정한 영상로 저장한다.

- filename: 저장할 영상파일의 이름
- img : 저장하고자 하는 행렬
- params : 압축 방식에 사용되는 인수 쌍

|옵션명|설명|
|-|-|
|cv2.IMWRITE_JPEG_QUALITY|JPEG 압축 방식에 사용되는 인수 쌍|
|cv2.IMWRITE_PNG_COMPRESSION|PNG 압축 방식에 사용되는 인수 쌍|
|cv2.IMWRITE_PXM_BINARY|PGM, PPM 파일의 이진 포맷 설정|

## 1.2. 동영상파일 처리 함수

cv2.VideoCapture 클래스를 사용하여 동영상 파일을 읽어 행렬로 변환한다.

> open(filename/device) #영상파일의 이름 또는 영상 시퀀스
> isOpened() #비디오 장비의 연결이 성공적으로 이루어졌는지 확인
> release() #비디오 장비의 연결을 종료
> get() #속성 식별자로 지정된 속성의 값을 반환
> set() #속성 식별자로 비디오 캡처의 속성을 설정
> read() #비디오 장비에서 한 프레임을 읽어오는 함수

cv2.imageWriter 클래스를 사용하여 비디오 파일을 저장한다.

> isOpened() #동영상 파일의 저장을 위해 VideoWriter 객체의 개방 여부를 반환
> write(image) #비디오 장비에 한 프레임을 저장하는 함수
> open(filename, fourcc, fps, frameSize) #영상을 동영상 파일의 프레임으로 저장하기 위해 동영상 파일을 개방한다.

## 2.1. 기본 배열 처리 함수

> cv2.flip(src, flipCode[, dst])

입력된 2차원 배열을 수직, 수평, 양축으로 뒤집는다.

- src : 입력된 배열
- dst : 출력된 배열
- flipCode : 수평, 수직, 양축으로 뒤집을 방향

> cv2.repeat(src, ny, nx[, dst])

입력 배열의 반복된 복사본으로 출력 배열을 채운다

- src : 입력 배열
- dst : 출력 배열
- ny,nx : 수직, 수평 반복 횟수

> cv2.transpose(src[, dst])

입력 행렬의 전치 행렬을 출력으로 반환한다.

- src : 입력 배열
- dst : 출력 배열

## 2.2. 기본 배열 처리 함수

채널 함수에 앞서 채널의 개념을 이해한다. 채널은 각 화소마다 b,g,r순으로 저장되는 값의 집합을 의미한다.

~~~python
[
[[b,g,r],[b,g,r],[b,g,r]],
[[b,g,r],[b,g,r],[b,g,r]],
[[b,g,r],[b,g,r],[b,g,r]],
]
~~~

> cv2. merge(src[, dst])

여러개의 단일 채널 배열을 다채널 배열로 병합한다.

- src : 합성될 입력 혹은 벡터로 합성될 단일 채널 배열들의 크기와 깊이가 동일해야함
- dst : 출력 배열로 입력 배열과 같은 크기와 깊이를 가져야함
 
> cv2. split(src[, dst])

다채널 배열을 여러개의 단일 채널 배열로 분리한다.

- m : 입력되는 다채널 배열
- dst : 분리되어 반환되는 단일 채널 배열들의 벡터

## 2.3. 연산 함수

> cv2.add(src1, src2[, dst[, mask[, dtype]]])

두개의 배열 혹은 배열과 스칼라의 각 원소간 합을 계산한다. 입력 인수 src1, src2중 하나는 스칼라일수있다.

- src1 : 첫번째 배열 혹은 스칼라
- src2 : 두번째 배열 혹은 스칼라
- dst : 계산된 결과 배열
- mask : 연산 마스크로 0이 아닌 마스크의 원소의 위치에서만 연산 수행(배열의 크기는 같아야함)
- dtype : 계산된 결과의 자료형

> cv2.subtract(src1, src2[, dst[, mask[, dtype]]])

두개의 배열 혹은 배열과 스칼라의 각 원소간 차분을 계산한다. 입력 인수 src1, src2중 하나는 스칼라일수있다.

- src1 : 첫번째 배열 혹은 스칼라
- src2 : 두번째 배열 혹은 스칼라
- dst : 계산된 결과 배열
- mask : 연산 마스크로 0이 아닌 마스크의 원소의 위치에서만 연산 수행(배열의 크기는 같아야함)
- dtype : 계산된 결과의 자료형
  
> cv2.multiply(src1, src2[, dst[, scale[, dtype]]])

두개의 배열 혹은 배열과 스칼라의 각 원소간 곱을 계산한다

- scale : 두 배열의 원소 간 곱할때 추가로 곱해주는 비율

> cv2.divide(src1, src2[, dst[, scale[, dtype]]])

두개의 배열의 각 원소 간  나눗셈을 수행한다

> cv2.addWeighted(src1, alpha, src2, beta, gamma[, dst[, dtype]])

두 배열의 각 원소에 가중치를 곱한 후에 각 원소간 합 즉 가중된 합을 계산한다.

- alpha : src1의 각 원소에 대한 가중치
- beta : src2의 각 원소에 대한 가중치
- gamma : 계산된 두 배열의 각 원소간 합에 추가로 더하는 스칼라 값

> cv2.bitwise_and(src1, src2[, dst[, mask]])
> cv2.bitwise_or(src1, src2[, dst[, mask]])
> cv2.bitwise_xor(src1, src2[, dst[, mask]])
> cv2.bitwise_not(src[, dst[, mask]])

src1, src2의 각 원소에 대해서 연산을 수행한다.
dst는 연산 결과를 저장하는 배열이다.
mask 마스크 연산 수행 (마스크 배열의 원소가 0 이 아닌 좌표에만 연산 수행)

> cv2.absdiff(src1, src2[, dst])

두 배열간 각 원소간 각 원소간 차분 절대값을 계산한다 src1, src2중 하나는 스칼라값이 될 수 있다.

- src1, src2 : 첫 번째 입력 배열, 두 번째 입력 배열
- dst : 계산된 결과 출력 배열

> cv2.convScaleAbs(src, [, dst[, alpha[, beta]]])

입력 배열의 각 원소에 alpha를 곱하고, beta를 더한 결과를 출력 배열에 저장한다.

- alpha : 입력 배열 원소에 곱해지는 스케일 팩터
- beta : 스케일된 값에 더해지는 델타 옵션

> cv2.min(src1, src2[, dst])

두 입력 배열의 각 원소에 대해서 최소값을 계산한다.

- src1, src2 : 첫 번째 입력 배열, 두 번째 입력 배열
- dst : 계산된 결과 출력 배열

> cv2.max(src1, src2[, dst])

두 입력 배열의 각 원소에 대해서 최대값을 계산한다.

> cv2.minMaxLoc(src1, [, dst])

입력 배열에서 최솟값과 최댓값을 계산한다. 최솟값과 최댓값을 가지는 원소의 위치를 리턴한다.

- src : 입력 배열
- minVal, maxVal: 최솟값, 최댓값
- minLoc, maxLoc : 최솟값의 원소의 위치, 최댓값의 원소의 위치

> cv2.sumElems(src)

배열의 각 채널별로 원소들의 합인 N을 계산하여 스칼라 값으로 반환한다.

-src : 1개에서 4개 채널을 가지는 입력 배열

> cv2.mean(src[, mask])

배열의 각 채널별로 원소들의 평균을 계산하여 스칼라 값으로 반환한다.

- src : 1개에서 4개 채널을 가지는 입력 배열
- mask : 마스크 연산 수행 (마스크 배열의 원소가 0 이 아닌 좌표에만 연산 수행)

> cv2.meanStdDev(src[, mask])

배열 원소들의 평균과 표준편차를 계산한다.

- src : 1개에서 4개 채널을 가지는 입력 배열
- mean : 계산된 편균이 반환되는
- stddev : 계산된 표준편차가 반환
- mask : 마스크 연산 수행 (마스크 배열의 원소가 0 이 아닌 좌표에만 연산 수행)

> cv2.reduce(src, dim, op[, dst[, mask]])

행렬을 열방향 축방향으로 옵션 상수에 따라 축소한다.

- src : 입력 배열
- dst : 출력 벡터, 감소방향과 타입은 dim, dtype에 따라 달라진다.
- dim : 행렬이 축소될 때 차원 감소 절차
- rtype : 축소 연산 종류
- dtype : 감소된 벡터의 자료형
  
| 옵션 상수 | 설명 |
|-|-|
| cv2.REDUCE_SUM | 행렬의 각 열의 합을 계산 |
| cv2.REDUCE_AVG | 행렬의 각 열의 평균을 계산 |
| cv2.REDUCE_MAX | 행렬의 각 열의 최댓값을 계산 |
| cv2.REDUCE_MIN | 행렬의 각 열의 최솟값을 계산 |

> cv2.sort(src[, dst[, flags]])

행렬의 각 행렬의 각 열의 방향으로 정렬한다.

- src : 단일 채널을 가지는 입력 배열
- dst : 정렬된 출력파일
- flag : 연산 플래그 다음의 상수를 사용할 수 있다.

> cv2.sortidx(src[, dst[, flags]])

행렬의 각 행렬의 각 열의 방향으로 정렬한다.

- src : 단일 채널을 가지는 입력 배열
- dst : 정렬된 출력파일
- flag : 연산 플래그 다음의 상수를 사용할 수 있다.

> cv2.gemm(src1, src2, alpha, src3, beta, dst[, flags])

- src1, src2 : 행렬 곱을 위한 입력 배열
- alpha : 행렬 곱에 대한 가중치
- src3 : 행렬 곱에 더해지는 델타 행렬
- beta : src3 행렬에 곱해지는 가중치
- dst : 출력 행렬
- flag : 연산 플래그 옵션을 조합하여 입력 행렬들을 전치

> cv2.perspectiveTransform(src, m[, dst])

입력 벡터들에 대해서 투영 변환 m을 수행한다.

- src : 입력 벡터 배열
- dst : src와 같은 크기와 타입의 출력 배열
- m : 3x3, 4x4 부동소수점의 투영 변환 행렬

> cv2.invert(src[, dst[, flags]])

행렬의 역행렬을 계산한다.(입력 행렬이 정방 행렬이 아니면 의사 역행렬 계산)

- src : 입력(MxN) 행렬
- dst : src와 크기같은 출력 행렬
- dst : src와 크기같은 출력 행렬


####################################################################

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