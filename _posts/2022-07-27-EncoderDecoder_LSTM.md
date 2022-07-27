---
title: Encoder-Decoder LSTM 개발 방법
categories:
  - DL
  - PYTHON
tags:
  - translate
toc: true
classes: wide
---

**원문링크: [http://ling.snu.ac.kr/class/cl_under1801/EncoderDecoderLSTM.pdf](http://ling.snu.ac.kr/class/cl_under1801/EncoderDecoderLSTM.pdf)**

`본 자료는 서울대학교 컴퓨터언어학 연구실 강의자료 번역본으로 모든 저작권은 Hyopil Shin (Dept. of Linguistics, Seoul National University)에게 있음을 알림.`

`자료 번역은 기계번역과 사람의 번역을 섞어서 진행하였으므로 번역에 오류가 있을 수 있으니, 오류가 있을 경우에는 언제든지 수정을 요청해주시면 감사하겠습니다.`

## 0. Encoder-Decoder LSTM 개발 방법

본 수업의 목표는 Encoder-Decoder LSTM Model을 개발하는것이다.
과정을 끝내면 다음을 알 수있다.

- Encoder-Decoder LSTM architecture와 케라스에서 이를 구현하는 방법
- 덧셈 seq2seq 예측의 문제점
- 덧셈 seq2seq 예측 문제를 위한 인코더-디코더 LSTM을 개발하는 방법.

## 1. 과정 톺아보기

 과정은 7가지 파트로 나뉩니다.

1. Encoder-Decoder LSTM
2. 덧셈 예측 문제
3. 모델 정의 및 컴파일
4. 모델 Fit
5. 모델 평가
6. 모델을 사용한 예측
7. 완전한 예제

## 2. Encoder-Decoder LSTM

### 2.1. seq2seq예측의 문제점

시퀀스예측은 실제 값으로 부터 다음의 값을 예측하는 방법이다. 입력된 시퀀스에 대하여 클래스레이블을 출력하며 이는 보통
a sequence of one input time step to one output time step(일대일) 또는 multiple input time steps to one output time step(일대다) 타입의 시퀀스 예측 문제로 프레임화 된다.

시퀀스 입출력으로 예측하는 더 도전적인 유형의 시퀀스 예측 문제가 있다. 이를 시퀀스 대 시퀀스라고 한다(줄여서 seq2seq).
이를 난제로 만드는 모델링의 문제는 입력 및 출력 시퀀스의 길이가 다를 수 있다는 것이다.
입력 시간 단계와 출력 시간 단계가 여러 개 있다는 것을 고려할 때, 이러한 형태의 문제를 다대다형 시퀀스 예측 문제라고 한다.

### 2.2. 아키텍쳐

seq2seq 예측문제에대해 가장 효과적인것으로 입증된 일반적인 아키텍쳐는 Encoder-Decoder LSTM이다.
이 아키텍쳐는 입력 시퀀스를 읽기 위한 두가지 모델로 구성된다. 하나는 입력 시퀀스를 읽고 고정 길이 벡터로 인코딩하는 것이고, 다른 하나는 고정 길이 벡터를 디코딩해 예측된 시퀀스를 출력한다.
모델 사용의의 공통됨은 Encoder-Decoder LSTM이다.

~~~text
... RNN 인코더-디코더, 두 개의 반복 신경망(RNN)으로 구성되어 있다.
인코더와 디코더 쌍의 역할을 합니다. 인코더는 가변 길이 소스를 매핑합니다.
고정 길이 벡터에 대한 시퀀스, 그리고 디코더는 벡터 표현을 매핑한다.
가변 길이 대상 시퀀스로 돌아갑니다.
— 통계 머신를 위한 RNN 인코더-디코더를 이용한 학습구 표현
번역, 2014.
~~~

인코더-디코더 LSTM은 다음과 같은 자연어 처리 문제를 위해 개발되었다. 그것은 특히 텍스트 번역 분야에서 최첨단 성능을 보여주었다. 이 아키텍처의 혁신은 고정 크기의 사용이다.
입력 시퀀스가 읽히고 출력되는 모델의 중심부에 있는 내부 표현 시퀀스를 읽는다. 이러한 이유로, 이 방법은 시퀀스 임베딩이라고 지칭될 수 있다.
영어-프랑스어 번역에 대한 아키텍처의 첫 번째 응용중 하나에서 인코딩된 영어 구문의 내부 표현이 시각화되었다. 플롯은 번역 작업에 사용되는 구문의 분석적으로 의미 있는 학습 구조를 보여주었다.

~~~text
제안된 RNN인코더-디코더는 연속공간의 자연스러운 구문을 생성한다. [중략] 시각화로부터 RNN Encoder-Decoder는 구문의 의미롸 구조를 모두 가지고있음이 명확해졌다.
— 통계 머신를 위한 RNN 인코더-디코더를 이용한 학습구 표현
번역, 2014.
~~~

번역 작업에서 모델은 입력괴는 문장이 역으로 되어있을때 더 효과적임이 입증된었다. 더 나아가 이 모델이 매우 긴 입력문장일때도 효과적임을 보여주었다.

~~~text
원문에서는 어순을 반대로 잡았지만 교육 및 테스트 세트에서는 대상 문장이 아니었기 때문에 긴 문장을 잘 할 수 있었다. 타고
그렇게 함으로써, 우리는 최적화를 만든 많은 단기 의존성을 도입했다.
문제는 훨씬 간단하다. 출처의 단어들을 뒤집는 간단한 속임수 문장은 이 작업의 주요한 기술적 기여 중 하나이다.
— Sequence to Sequence Learning with Neural Networks, 2014
~~~

이러한 접근법은 CNN에서 입력 이미지에서 형상의 추출로 사용되며 그런 다음 디코더 LSTM에 의해 read된다.

~~~text
... 우리는 심층 CNN(Convolution Neural Network) 인코더 RNN으로 대체하면서 이 우아한 방식을 따를 것을 제안한다.
[...] CNN을 이미지 "인코더"로 사용하는 것은 자연스러운 일입니다.
먼저 이미지 분류 작업을 위해 사전 훈련하고 마지막 숨겨진 레이어를 문장을 생성하는 RNN 디코더에 대한 입력으로 사용한다.
— Show and Tell: A Neural Image Caption Generator, 2014.
~~~

|Architecture|
|-|
|**Input**|
|**↓**|
|**Encoded_Model**|
|**↓**|
|**Decoder_Model**|
|**↓**|
|**Dense**|
|**↓**|
|**Output**|

`Figure 9.1: Encoder-decoder LSTM Architecture.`

### 2.3. 응용

이하의 리스트는 Encoder-Decoder LSTM의 흥미로운 응용분야입니다.

- **기계번역**: e.g. English to French translation of phrases.
- **실행하는 법 배우기**: e.g. generating a text description for images.
- **이미지 캡셔닝**: e.g. generating answers to textual questions.
- **행동 분류**: e.g. generating a sequence of commands from a sequence of gestures.

### 2.4. 구현

인코더-디코더 LSTM은 케라스에서 직접 구현될 수 있다. 이는 인코더와 디코더라는 두 가지 핵심 부분으로 구성된다.
먼저, 입력 시퀀스는 한 번에 하나의 인코딩된 문자를 네트워크에 보여준다. 입력 시퀀스의 단계 간의 관계를 배우고 이러한 관계의 내부 표현을 개발하기 위해 인코딩 레벨이 필요하다. 하나 또는 그 이상의 LSTM레이어가 인코더모댈을 구현하는데에 사용된다. 그 출력은 입력 시퀀스의 내부를 대표하는 고정사이즈벡터이다.

~~~python
    #Example of a Vanilla LSTM model.
    model=Sequential()
    model.addLSTM(input_shape=(None, num_encoder_tokens),
                  output_dim=num_decoder_tokens,
                  return_sequences=True)
~~~

`Listing 9.1: Example of a Vanilla LSTM model.`

디코더는 반드시학습된 내부의 대표 입력 시퀀스를 올바른 출력 시퀀스로 변형해야한다.
하나 또는 그 이상의 LSTM레이어가 디코더 모델을 구현하는데에 사용된다.
이 모델은 인코더 모델로부터의 고정크기 출력을 읽어온다. 기본 LSTM에서 Dense 레이어는 네트워크의 출력으로 사용된다.
동일한 가중치를 출력 시퀀스의 각 시간 스텝에 사용하려면 TimeDistributed 래퍼를 사용하여 Dense 레이어를 재구성한다.

~~~python
model.add(LSTM(..., return_sequences=True))
model.add(TimeDistributed(Dense(...)))
~~~

`Listing 9.2: Example of a LSTM model with TimeDistributed wrapped Dense layer.`

여기에서도 문제가 발생한다. 인코더와 디코더를 연결해야 하고, 이들이 일치하지 않는다는 것이다. 인코더는 2차원의 행렬을 생성하며 길이는 레이어의 메모리 cell 수에 의해 정의된다.
디코더는 3차원[샘플, 시간 스텝, 요소] 입력을 예측하는 LSTM 레이어이며 이러한 다른 길이를 정의하는 문제에 의해 생성된 시퀀스를 출력한다.
만일 당신이 강제로 이들을 합치려고 한다면 오류는 디코더의 출력이 2D와 3D 입력을 필요로 한다는 것을 나타낼것이다. 우리는 이를 RepeatVector 레이어로 해결할 수 있다.

이 레이어는 단순히 주어진 2D입력을 반복해 3D 출력을 생성한다.
RepeatVector 레이어는 인코더와 디코더 부분을 합치기 위해 사용될 수 있다.
우리은 출력 시퀀스에서 각 시간 단계 RepeatVector을 사용하여 고정 길이 백터를 한번 반복시킬수록 설정 가능하다.

~~~python
model.add(RepeatVector(...))
~~~

`Listing 9.3: Example of a RepeatVector layer.`

이들을 투입할때 우리는 다음 과정을 수행한다:

~~~python
model = Sequential()
model.add(LSTM(..., input_shape=(...)))
model.add(RepeatVector(...))
model.add(LSTM(..., return_sequences=True))
model.add(TimeDistributed(Dense(...)))
~~~

`Listing 9.4: Example of an Encoder-Decoder model.`

요약하자면 RepeatVector는 인코더의 고정 크기 2D 출력을 디코더의 다른 길이와 3D 입력에 적합시키는 어뎁터로서 사용된다.
TimeDistributed 래퍼는 출력 시퀀스의 각 요소에서 사용될 출력 레이어를 사용할 수 있다.

## 3 덧셈 예측 문제

덧셈 문제는 시퀀스대 시퀀스(seq2seq)예측의 문제이다.
이는 Wojciech Zaremba 와 Ilya Sutskever의 2014년 연구에서 “Learning to Execute”에서 제시된 소형 프로그램의 출력을 계산하는 아키텍쳐에 사용되었다.
문제는 두 개의 입력 숫자의 합을 계산하는 것을 정의한다.
이는 문자로 주어진 각 숫자와 수식에 도전하는것으로 예상되는 출력 역시 문자이다.
예를 들어 입력 10+6에 대한 출력 16은 시퀀스로 10, +, 6, 16이 된다.

~~~text
Input: ['1', '0', '+', '6']
Output: ['1', '6']
~~~

`Listing 9.5: Example of input and output sequences for the addition problem.`

모델은 자연어 숫자를 배워야하는것 뿐만아니라 수식을 수행하는 방법을 배워야한다.
이제 시퀀스가 중요하다는 점에 주목하자. 입력을 랜덤으로 섞으면 출력에 관련되지 않는 무언가의 시퀀스를 생성할 수 있다.
또한 입력과 출력의 숫자의 개수가 다를 수 있다는 점에 주목하자.
기술적으로 이것은 다대다 모델을 처리해야 하는 덧셈 예측 문제를 시퀀스 대 시퀀스 문제로 만듭니다.

| | | | | |
|-|-|-|-|-|
||||1|6|
||||↑|↑|
|→|→|→|→|→|
|↑|↑|↑|↑||
|1|0|+|6||

`Figure 9.2: Addition prediction problem framed with a many-to-many prediction model.`

우리는 두 숫자의 합을 구하는것을 쉽게 해볼수있다. 그러나 우리는 모델에 학습되고 일반화 되도록 어떻게 다양한 숫자의 의미와 수학 기호로 확장?(번역실패)
이 문제는 파이썬으로 구현될수있다. 우리는 이를 다음과 같은 단계로 나눌것이다.

1. 합계의 쌍(Sum Pairs)을 생성.
2. 숫자를 패딩된 문자열로 만들기.
3. 정수를 시퀀스로 인코딩하기
4. 시퀀스를 원-핫 인코딩하기.
5. 시퀀스로 파이프라인 제작.
6. 시퀀스 디코딩.

### 3.1. 합계의 쌍(Sum Pairs)을 생성

랜덤 정수시퀀스를 생성하고 그 합을 구하는 첫번째 단계로 아래와 같은 random sum pairs()함수를 정의한다.

~~~python
from random import seed
from random import randint
# generate lists of random integers and their sum
def random_sum_pairs(n_examples, n_numbers, largest):
  X, y = list(), list()
  for _ in range(n_examples):
    in_pattern = [randint(1,largest) for _ in range(n_numbers)]
    out_pattern = sum(in_pattern)
    X.append(in_pattern)
    y.append(out_pattern)
    return X, y

seed(1)
n_samples = 1
n_numbers = 2
largest = 10
# generate pairs
X, y = random_sum_pairs(n_samples, n_numbers, largest)
print(X, y)
~~~

이 함수를 작동시키면 1 부터 10까지의 두 숫자의 합을 구하는 예를 생성할수있다.

### 3.2. 정수를 문자열로 변환

다음으로 정수를 문자열로 변환한다. 입력되는 문자열은 '10+10'의 형태이고 출력되는 문자열은 '20'의 형태이다. 이 함수의 핵심은 입력과 출력시퀀스의 글자수가 같도록 패딩하는것이다.
패딩 문자는 데이터와 다르게 하여 모델이 이를 무시하도록 배우게 할수있다.
이런 케이스에서 우리는 패딩 문자를 공백으로 설정하고 왼쪽에서 패딩을 하고 오른쪽에서 정보를 보존하도록 한다.
다른 방법으로 패딩을 할수있다. 예를 들어 각 용어별로 패딩을 할수있다.
시행해보고 성능이 좋아지는지 확인해보도록 한다. 패딩을 위해서는 가장 긴 시퀀스의 길이를 알아야한다.
최대 정수를 생성할수있는 log10()을 사용하여 최대 정수의 길이를 계산할수있으며 그것을 올림하여 우리는 각 숫자별로 몇개의 글자가 필요한지 알아볼수있다.
We add 1 to the largest number to ensure we expect 3 chars instead of 2 chars for the case of a round largest number, like 200 and take the ceiling of the result `(e.g. ceil(log10(largest+1)))`.
We then need to add the right number of plus symbols (e.g. `n numbers - 1`).
(이해못함)

~~~python
max_length = int(n_numbers * ceil(log10(largest+1)) + n_numbers - 1)
~~~

`Listing 9.8: Example of calculating the maximum length of input sequences.`

만들어진 예를 통해 단어의 갯수를 구체화 시킬수있다.
(n numbers) 은 3 이고 가장 큰 값인 (largest) 은 10이다.

~~~python
max_length = n_numbers * ceil(log10(largest+1)) + n_numbers - 1
max_length = 3 * ceil(log10(10+1)) + 3 - 1
max_length = 3 * ceil(1.0413926851582251) + 3 - 1
max_length = 3 * 2 + 3 - 1
max_length = 6 + 3 - 1
max_length = 8
~~~

`Listing 9.9: Worked example of maximum input sequence length.`

직관적으로 2개의 공백을 각 용어별로 곱한 3개의 용어(e.g. [‘1’,‘0’])를 가지는 시퀀스를 예를 들어 생성할수있다.
또는 최대 시퀀스의 길이는 6개의 공백을 가지는 시퀀스를 생성하는데 추가로 2개의 공백을 필요로한다.
최대 가능한 심볼의 시퀀스의 길이는 8자리이다(e.g. [‘1’,‘0’,‘+’,‘1’,‘0’,‘+’,‘1’,‘0’]). 이는 예제에서도 볼수있다.
비슷한 과정은 출력 시퀀스에서 반복된다. 물론 연산자는 제거된다.

~~~python
max_length = int(ceil(log10(n_numbers * (largest+1))))
~~~

`Listing 9.10: Example of calculating the length of output sequences.`

다시 위의 예와 예상된 최대 시퀀스 길이를 전체 단어수(n_numbers)가 3이고 가장큰 값(largest)이 10인 계산해 구체화할수있다.

~~~python
max_length = ceil(log10(n_numbers * (largest+1)))
max_length = ceil(log10(3 * (10+1)))
max_length = ceil(log10(33))
max_length = ceil(1.5185139398778875)
max_length = 2
~~~

`Listing 9.11: Worked example of maximum output sequence length.`

다시 직관적으로 가장 큰 덧셈의 값은 10+10+10이고 예상된 값은 30임을 알수 있다.
이것은 최대 길이가 2개야 하며 예제에서 볼수있다.
아래 예에서는 string() 함수를 추가하고 단일 입력/출력 쌍으로 사용하는 방법을 보여 줍니다.

~~~python
from random import seed
from random import randint
from math import ceil
from math import log10
# generate lists of random integers and their sum
def random_sum_pairs(n_examples, n_numbers, largest):
  X, y = list(), list()
  for _ in range(n_examples):
    in_pattern = [randint(1,largest) for _ in range(n_numbers)]
    out_pattern = sum(in_pattern)
    X.append(in_pattern)
    y.append(out_pattern)
  return X, y
# convert data to strings
def to_string(X, y, n_numbers, largest):
  max_length = int(n_numbers * ceil(log10(largest+1)) + n_numbers - 1)
  Xstr = list()
  for pattern in X:
    strp = '+'.join([str(n) for n in pattern])
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    Xstr.append(strp)
  max_length = int(ceil(log10(n_numbers * (largest+1))))
  ystr = list()
  for pattern in y:
    strp = str(pattern)
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    ystr.append(strp)
  return Xstr, ystr

seed(1)
n_samples = 1
n_numbers = 2
largest = 10
# generate pairs
X, y = random_sum_pairs(n_samples, n_numbers, largest)
print(X, y)
# convert to strings
X, y = to_string(X, y, n_numbers, largest)
print(X, y)
~~~

`Listing 9.12: Example of converting a sequence pair to padded characters`

이 예시를 실행하면 처음으로 시퀀스의 정수 시퀀스와 같은 시퀀스의 패딩 문자열 반환을 확인할수있다.

~~~text
[[3, 10]] [13]<br>
[' 3+10'] ['13']
~~~

`Listing 9.13: Example of output of converting a sequence pair to padded characters.`

### 3.3. 시퀀스를 정수로 인코딩하기

다음으로 각 문자를 정수 값으로 인코딩해야한다.
숫자를 긴경망에서 사용하기 위해서는 문자열이 아닌 숫자로 작업해야한다.
정수 인코딩은 문제를 출력 시퀀스가 11개의 가능한 값으로 각각 나누어진 분류 문제로 변화시킨다.
이것은 단지 일부 순서 관계가있는 정수(첫 10개의 클래스 값)에 불과하다.
이 인코딩을 수행하기위해서는 우리는 다음과 같이 전체 알파벳이 문자열 인코딩에서 나타날 수있는 기호를 지정해야한다 :

~~~text
alphabet = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', ' ']
~~~

`Listing 9.14: Example of defining a character alphabet.`

정수 인코딩은 간단한 과정이다. 문자열에서 문자를 정수로 변환하는 테이블을 만들고 각 문자를 하나씩 변환하는 것이다.
아래의 예시는 정수 인코딩을 위한 함수를 제공하고 어떻게 사용하는지 보여준다.

~~~python
from random import seed
from random import randint
from math import ceil
from math import log10
# generate lists of random integers and their sum
def random_sum_pairs(n_examples, n_numbers, largest):
  X, y = list(), list()
  for _ in range(n_examples):
    in_pattern = [randint(1,largest) for _ in range(n_numbers)]
    out_pattern = sum(in_pattern)
    X.append(in_pattern)
    y.append(out_pattern)
  return X, y
# convert data to strings
def to_string(X, y, n_numbers, largest):
  max_length = int(n_numbers * ceil(log10(largest+1)) + n_numbers - 1)
  Xstr = list()
  for pattern in X:
    strp = '+'.join([str(n) for n in pattern])
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    Xstr.append(strp)
  max_length = int(ceil(log10(n_numbers * (largest+1))))
  ystr = list()
  for pattern in y:
    strp = str(pattern)
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    ystr.append(strp)
  return Xstr, ystr
# integer encode strings
def integer_encode(X, y, alphabet):
  char_to_int = dict((c, i) for i, c in enumerate(alphabet))
  Xenc = list()
  for pattern in X:
    integer_encoded = [char_to_int[char] for char in pattern]
    Xenc.append(integer_encoded)
  yenc = list()
  for pattern in y:
    integer_encoded = [char_to_int[char] for char in pattern]
    yenc.append(integer_encoded)
  return Xenc, yenc
seed(1)
n_samples = 1
n_numbers = 2
largest = 10
# generate pairs
X, y = random_sum_pairs(n_samples, n_numbers, largest)
print(X, y)
# convert to strings
X, y = to_string(X, y, n_numbers, largest)
print(X, y)
# integer encode
alphabet = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', ' ']
X, y = integer_encode(X, y, alphabet)
print(X, y)
~~~

`Listing 9.15: Example of integer encoding padded sequences.`


이 예제를 실행 하면 각 문자열의 인코딩 된 패턴으로 정수 인코딩 된 버전이 출력된다.
space 문자(‘ ’)는 11로 인코딩되고 문자(‘3’)는 3으로 인코딩되었음을 알 수 있다.

~~~text
[[3, 10]] [13]
[' 3+10'] ['13']
[[11, 3, 10, 1, 0]] [[1, 3]]
~~~

`Listing 9.16: Example output from integer encoding input and output sequences.`

### 3.4. 시퀀스 One-Hot 인코딩

다음으로 해야하는 과정은 정수 인코딩 시퀀스를 이진 인코딩하는 것이다.
이는 각 정수를 알파벳과 동일한 길이의 이진 벡터로 변환하고 특정 정수를 1로 표시하는 것을 포함한다.
 예로 0 정수는 ‘0’ 문자를 나타내고 11 요소 벡터의 0 번째 위치에 1이 있는 이진 벡터로 인코딩된다:

[1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0].

아래의 예시는 one hot encode() 함수를 정의하고 이진 인코딩을 어떻게 사용하는지 보여준다.

~~~python
# generate lists of random integers and their sum
def random_sum_pairs(n_examples, n_numbers, largest):
  X, y = list(), list()
  for _ in range(n_examples):
    in_pattern = [randint(1,largest) for _ in range(n_numbers)]
    out_pattern = sum(in_pattern)
    X.append(in_pattern)
    y.append(out_pattern)
  return X, y
# convert data to strings
def to_string(X, y, n_numbers, largest):
  max_length = int(n_numbers * ceil(log10(largest+1)) + n_numbers - 1)
  Xstr = list()
  for pattern in X:
    strp = '+'.join([str(n) for n in pattern])
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    Xstr.append(strp)
  max_length = int(ceil(log10(n_numbers * (largest+1))))
  ystr = list()
  for pattern in y:
    strp = str(pattern)
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    ystr.append(strp)
  return Xstr, ystr
# integer encode strings
def integer_encode(X, y, alphabet):
  char_to_int = dict((c, i) for i, c in enumerate(alphabet))
  Xenc = list()
  for pattern in X:
    integer_encoded = [char_to_int[char] for char in pattern]
    Xenc.append(integer_encoded)
    yenc = list()
  for pattern in y:
    integer_encoded = [char_to_int[char] for char in pattern]
    yenc.append(integer_encoded)
  return Xenc, yenc
# one hot encode
def one_hot_encode(X, y, max_int):
  Xenc = list()
  for seq in X:
    pattern = list()
    for index in seq:
      vector = [0 for _ in range(max_int)]
      vector[index] = 1
      pattern.append(vector)
    Xenc.append(pattern)
  yenc = list()
  for seq in y:
    pattern = list()
    for index in seq:
      vector = [0 for _ in range(max_int)]
      vector[index] = 1
      pattern.append(vector)
    yenc.append(pattern)
  return Xenc, yenc
seed(1)
n_samples = 1
n_numbers = 2
largest = 10
# generate pairs
X, y = random_sum_pairs(n_samples, n_numbers, largest)
print(X, y)
# convert to strings
X, y = to_string(X, y, n_numbers, largest)
print(X, y)
# integer encode
alphabet = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', ' ']
X, y = integer_encode(X, y, alphabet)
print(X, y)  
# one hot encode
X, y = one_hot_encode(X, y, len(alphabet))
~~~

`Listing 9.17: Example of one hot encoding an integer encoded sequences.`

예시를 실행시키면 각각의 정수를 이진으로 인코딩한 시퀀스를 출력한다.
몇개의 새로운 줄을 추가하여 압출력 이진 인코딩을 더 멸확하게 만들었다.
당신은 볼 수있다 단일 덧셈 패턴이 11개의 요소로 된 5개의 이진 인코딩 벡터로 이루어진 시퀀스가 된다.
출력이나 합계는 다시 11개의 요소를 가진 2개의 이진 인코딩 벡터로 이루어진 시퀀스가 된다.

~~~python

[[3, 10]] [13]
[' 3+10'] ['13']
[[11, 3, 10, 1, 0]] [[1, 3]]
[[[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1],
[0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0],
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0],
[0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
[1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]]]
[[[0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
[0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0]]]

~~~

### 3.5. 시퀀스 생성 파이프라인

아래를 보면 이러한 단계를 generate data()라는 함수로 묶을 수 있다.
주어진 숫자의 샘플, 항의 수, 각 항의 가장 큰 값, 그리고 가능한 문자의 알파벳, 이 함수는 입력과 출력 시퀀스의 집합을 생성한다.

~~~python
# generate an encoded dataset
def generate_data(n_samples, n_numbers, largest, alphabet):
  # generate pairs
  X, y = random_sum_pairs(n_samples, n_numbers, largest)
  # convert to strings
  X, y = to_string(X, y, n_numbers, largest)
  # integer encode
  X, y = integer_encode(X, y, alphabet)
  # one hot encode
  X, y = one_hot_encode(X, y, len(alphabet))
  # return as NumPy arrays
  X, y = array(X), array(y)
  return X, y
~~~

### 3.6. 시퀀스 디코딩

최종적으로 인코딩을 역으로 하여 출력 벡터를 숫자로 변환해 예상되는 출력 정수를 예측된 정수와 비교할 수 있도록 해야한다.
아래의 invert함수는 이 작업을 수행한다. 방법은 argmax()함수를 사용하여 이진 인코딩을 정수로 다시 변환하는 것이다.
그리고 그 정수를 알파벳의 문자로 역으로 매핑하여 문자로 다시 변환한다.

~~~python
# invert encoding
def invert(seq, alphabet):
  int_to_char = dict((i, c) for i, c in enumerate(alphabet))
  strings = list()
  for pattern in seq:
    string = int_to_char[argmax(pattern)]
    strings.append(string)
  return ''.join(strings)
~~~

`Listing 9.20: Function for deciding an encoded input or output sequence.`

이제 이 예제를 위한 데이터를 준비하기 위해 필요한 모든 것을 가지고 있다.

## 4. 모델 정의 및 컴파일

첫번째 단계는 시퀀스 예측 문제의 세부사항을 지정하는것이다.
입출력 시퀀스 샘플 생성을 위해 위의 generate_data()함수에 입력할 3개의 매개변수를 지정해야한다.

- n_terms: 수식의 항의 수, (예를 들어 10+10의 경우 2)
- largest: 각 항의 가장 큰 수치 값 (예를 들어 1-10 사이의 값의 경우 10)
- alphabet: 입력과 출력 시퀀스를 인코딩하는데 사용되는 기호 (예를 들어 0-9, +, ‘’)

문제가 적당한 복잡도를 가지도록 설정을 사용할것이다.
각 인스턴스는 3개의 항으로 구성되며 각 항의 최대값은 10이다. 알파벳은 설정에 관계없이 0-9, ‘+’, ‘ ’의 값으로 고정된다.

~~~python
# number of math terms
n_terms = 3
# largest value for any single input digit
largest = 10
# scope of possible symbols for each input or output time step
alphabet = [str(x) for x in range(10)] + ['+', ' ']
~~~

`Listing 9.21: Example of configuring the problem instance.`

네트워크는 3개 설정이 필요하며 이는 덧셈 분제의 상세에 의해 결정된다.

- n_chars: 각 시간 단계에 대한 알파벳의 크기 (예를 들어 0-9, ‘+’와 ‘ ’의 경우 12)
- n_in_seq_length: 인코딩된 입력 시퀀스의 시간 단계 수 (예를 들어 ‘10+10+10’의 경우 8)
- n_out_seq_length: 인코딩된 출력 시퀀스의 시간 단계 수 (예를 들어 ‘30’의 경우 2)
  
n_chars변수는 각 입력과 출력 시간 단계에 대한 입력층과 출력층의 특징의 수를 정의하는데 사용된다.

n_in_seq_length 변수는 네트워크 입력레이어의 시간 단계를 정의하는데에 사용된다.

n_out_seq_length는 인코딩된 입력을 반복하는 RepeatVector의 반복 횟수를 정의하는데 사용된다. 이는 출력 시퀀스를 생성하는데 사용되는 디코더에 피드되는 시퀀스의 길이를 정의한다.

n_in_seq_length, n_out_seq_length의 정의는 정수 시퀀스를 문자열로 매핑하는데 사용된 to string()함수에서 사용된 코드를 사용한다.

~~~python
# size of alphabet: (12 for 0-9, + and ' ')
n_chars = len(alphabet)
# length of encoded input sequence (8 for '10+10+10)
n_in_seq_length = int(n_terms * ceil(log10(largest+1)) + n_terms - 1)
# length of encoded output sequence (2 for '30')
n_out_seq_length = int(ceil(log10(n_terms * (largest+1))))
~~~

`Listing 9.22: Example of defining network configuration based on the problem instance`

이제Encoder-Decoder LSTM를 정의할 준비를 마쳤습니다.
단일 LSTM레이어를 인코더에 사용하고 단일 레이어를 디코더에 사용할 것이다.
인코더는 75개의 메모리 셀로 정의 되고 디코더는 50개의 메모리 셀로 정의된다.
메모리 셀의 수는 약간의 시행착오를 통해 찾아졌다.

인코더와 디코더의 레이어 크기 비대칭은 입력 시퀀스가 출력 시퀀스보다 상대적으로 길다는 점을 감안할 때 자연스러운 구성으로 보임을 알수있다.
출력 계층은 예측할 수 있는 12개의 가능한 클래스에 대해 범주형 로그 손실을 사용한다.
gradient descent의 효율적인 Adam 구현이 사용되며 정확도는 훈련 및 모델 평가 중에 계산됩니다.

~~~python
# define LSTM
model = Sequential()
model.add(LSTM(75, input_shape=(n_in_seq_length, n_chars)))
model.add(RepeatVector(n_out_seq_length))
model.add(LSTM(50, return_sequences=True))
model.add(TimeDistributed(Dense(n_chars, activation='softmax')))
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
model.summary()
~~~

`Listing 9.23: Example of defining and compiling the Encoder-Decoder LSTM.`

예제를 실행하면 네트워크 구조의 요약이 출력된다.
우리는 주어진 입력 시퀀스에 대해 75의 길이를 가진 고정 크기 벡터를 출력하는 인코더를 볼 수 있다.
이 시퀀스는 2번 반복되어 디코더에 75개의 특징을 가진 2개의 시간 단계의 시퀀스를 제공한다.
디코더는 TimeDistributed 래퍼를 통해 한 번에 하나씩 처리하는 Dense 출력 레이어에 2개의 시간 단계의 50개의 특징을 출력한다.

~~~text_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
lstm_1 (LSTM)                (None, 75)                26400
_________________________________________________________________
repeat_vector_1 (RepeatVecto (None, 2, 75)             0
_________________________________________________________________
lstm_2 (LSTM)                (None, 2, 50)             25200
_________________________________________________________________
time_distributed_1 (TimeDist (None, 2, 12)             612
=================================================================
Total params: 52,212
Trainable params: 52,212
Non-trainable params: 0
_________________________________________________________________
~~~

`Listing 9.24: Example output from defining and compiling the Encoder-Decoder LSTM.`

## 5. 모델 피팅하기

모델은 랜덤하게 생성된 입출력 쌍을 75,000개의 single epoch로 피팅한다.
시퀀스의 수는 훈련 에포크를 대신한다. 총 75,000과 배치 사이즈 32는 약간의 시행착오를 통해 찾아졌고 최적의 구성은 아니다.

~~~python
# fit LSTM
X, y = generate_data(75000, n_terms, largest, alphabet)
model.fit(X, y, epochs=1, batch_size=32)
~~~

`Listing 9.25: Example of fitting the defined Encoder-Decoder LSTM.`

모델을 피팅하면 진행도 바가 loss와 accuracy를 배치의 끝 마다 보여준다.
모델은 CPU에서 피팅하는데 시간이 걸리지 않는다.
진행도 바가 개발 환경에 방해가 되면 fit() 함수에서 verbose=0을 설정하여 끌 수 있다.

~~~text
75000/75000 [==============================] - 37s - loss: 0.6982 - acc: 0.7943
~~~

`Listing 9.26: Example output from fitting the defined Encoder-Decoder LSTM.`

## 6. 모델 평가하기

100 가지 다른 랜덤 입-출력 쌍을 생성하여 모델을 평가할 수 있습니다
결과는 일반적으로 랜덤으로 생성된 예제에 대한 모델의 성능을 제공합니다.

~~~python
# evaluate LSTM
X, y = generate_data(5000, n_terms, largest, alphabet)
result = model.predict(X, batch_size=32, verbose=0)
decoded = decode_sequence(X, y, result, alphabet)
print('Accuracy: %.2f%%' % (decoded * 100))
~~~

`Listing 9.27: Example of evaluating the defined Encoder-Decoder LSTM.`

예시를 실행하면 모델의 로그 손실과 정확도가 출력된다.
구체적인 값은 신경망의 확률적인 성격 때문에 다를 수 있지만 모델의 정확도는 90% 이상이어야 한다.

~~~text
Loss: 0.128379, Accuracy: 100.00%
~~~

`Listing 9.28: Example output from evaluating the fit Encoder-Decoder LSTM.`

## 7. 모델을 사용하여 예측하기

피팅된 모델을 사용하여 예측을 만들 수 있다.
우리는 하나의 예측을 만들고 디코딩된 입력, 예상 출력, 예측된 출력의 요약을 제공할 것이다.
디코딩된 출력은 문제와 모델성능에 대한 더 구체적인 연결을 제공한다.
여기 우리는 10개의 새로운 랜덤 입출력 시퀀스 쌍, 각각에 대해 피팅된 모델을 사용하여 예측을 만들고, 모든 시퀀스를 디코딩하고 화면에 출력한다.

~~~python
# predict
for _ in range(10):
  # generate an input-output pair
  X, y = generate_data(1, n_terms, largest, alphabet)
  # make prediction
  yhat = model.predict(X, verbose=0)
  # decode input, expected and predicted
  in_seq = invert(X[0], alphabet)
  out_seq = invert(y[0], alphabet)
  predicted = invert(yhat[0], alphabet)
  print('%s = %s (expect %s)' % (in_seq, predicted, out_seq))
~~~

`Listing 9.29: Example of making predictions with the fit Encoder-Decoder LSTM.`

실행 결과 모델이 대부분의 시퀀스를 올바르게 예측하는 것을 확인할 수 있다.
생성한 특정 시퀀스와 모델의 능력은 10개의 예제에 따라 다를 수 있다.
모델의 행동을 잘 이해하기 위해 예측 부분을 몇 번 실행해 보자.

~~~text
9+10+9 = 27 (expect 28)
9+6+9 = 24 (expect 24)
8+9+10 = 27 (expect 27)
9+9+10 = 28 (expect 28)
2+4+5 = 11 (expect 11)
2+9+7 = 18 (expect 18)
7+3+2 = 12 (expect 12)
4+1+4 = 9 (expect 9)
8+6+7 = 21 (expect 21)
5+2+7 = 14 (expect 14)
~~~

`Listing 9.30: Example output from making predictions with the fit Encoder-Decoder LSTM.`

## 8. 완성된 예제

완전성을 위해 전체 코드가 아래에 제공된다.

~~~python

from random import randint
from numpy import array
from math import ceil
from math import log10
from numpy import argmax
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
from keras.layers import TimeDistributed
from keras.layers import RepeatVector
# generate lists of random integers and their sum
def random_sum_pairs(n_examples, n_numbers, largest):
  X, y = list(), list()
  for _ in range(n_examples):
    in_pattern = [randint(1,largest) for _ in range(n_numbers)]
    out_pattern = sum(in_pattern)
    X.append(in_pattern)
    y.append(out_pattern)
  return X, y
# convert data to strings
def to_string(X, y, n_numbers, largest):
  max_length = int(n_numbers * ceil(log10(largest+1)) + n_numbers - 1)
  Xstr = list()
  for pattern in X:
    strp = '+'.join([str(n) for n in pattern])
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    Xstr.append(strp)
  max_length = int(ceil(log10(n_numbers * (largest+1))))
  ystr = list()
  for pattern in y:
    strp = str(pattern)
    strp = ''.join([' ' for _ in range(max_length-len(strp))]) + strp
    ystr.append(strp)
  return Xstr, ystr
# integer encode strings
def integer_encode(X, y, alphabet):
  char_to_int = dict((c, i) for i, c in enumerate(alphabet))
  Xenc = list()
  for pattern in X:
    integer_encoded = [char_to_int[char] for char in pattern]
    Xenc.append(integer_encoded)
    yenc = list()
  for pattern in y:
    integer_encoded = [char_to_int[char] for char in pattern]
    yenc.append(integer_encoded)
  return Xenc, yenc
# one hot encode
def one_hot_encode(X, y, max_int):
  Xenc = list()
  for seq in X:
    pattern = list()
    for index in seq:
      vector = [0 for _ in range(max_int)]
      vector[index] = 1
      pattern.append(vector)
    Xenc.append(pattern)
  yenc = list()
  for seq in y:
    pattern = list()
    for index in seq:
      vector = [0 for _ in range(max_int)]
      vector[index] = 1
      pattern.append(vector)
    yenc.append(pattern)
  return Xenc, yenc
# generate an encoded dataset
def generate_data(n_samples, n_numbers, largest, alphabet):
  # generate pairs
  X, y = random_sum_pairs(n_samples, n_numbers, largest)
  # convert to strings
  X, y = to_string(X, y, n_numbers, largest)
  # integer encode
  X, y = integer_encode(X, y, alphabet)
  # one hot encode
  X, y = one_hot_encode(X, y, len(alphabet))
  # return as numpy arrays
  X, y = array(X), array(y)
  return X, y
# invert encoding
def invert(seq, alphabet):
  int_to_char = dict((i, c) for i, c in enumerate(alphabet))
  strings = list()
  for pattern in seq:
    string = int_to_char[argmax(pattern)]
    strings.append(string)
  return ''.join(strings)
# configure problem
# number of math terms
n_terms = 3
# largest value for any single input digit
largest = 10
# scope of possible symbols for each input or output time step
alphabet = [str(x) for x in range(10)] + ['+', ' ']
# size of alphabet: (12 for 0-9, + and ' ')
n_chars = len(alphabet)
# length of encoded input sequence (8 for '10+10+10)
n_in_seq_length = int(n_terms * ceil(log10(largest+1)) + n_terms - 1)
# length of encoded output sequence (2 for '30')
n_out_seq_length = int(ceil(log10(n_terms * (largest+1))))
# define LSTM
model = Sequential()
model.add(LSTM(75, input_shape=(n_in_seq_length, n_chars)))
model.add(RepeatVector(n_out_seq_length))
model.add(LSTM(50, return_sequences=True))
model.add(TimeDistributed(Dense(n_chars, activation='softmax')))
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
print(model.summary())
# fit LSTM
X, y = generate_data(75000, n_terms, largest, alphabet)
model.fit(X, y, epochs=1, batch_size=32)
# evaluate LSTM
X, y = generate_data(100, n_terms, largest, alphabet)
loss, acc = model.evaluate(X, y, verbose=0)
print('Loss: %f, Accuracy: %f' % (loss, acc*100))
# predict
for _ in range(10):
  # generate an input-output pair
  X, y = generate_data(1, n_terms, largest, alphabet)
  # make prediction
  yhat = model.predict(X, verbose=0)
  # decode input, expected and predicted
  in_seq = invert(X[0], alphabet)
  out_seq = invert(y[0], alphabet)
  predicted = invert(yhat[0], alphabet)
  print('%s = %s (expect %s)' % (in_seq, predicted, out_seq))

~~~

`Listing 9.31: Complete examples of the Encoder-Decoder LSTM model on the Addition Prediction prediction problem.`

## 9. 참고자료

본 섹션은 추가적인 자료를 제공합니다.

### 9.1. Encoder-Decoder LSTM에 대한 논문

- [Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation, 2014.](https://arxiv.org/abs/1406.107)
- [Sequence to Sequence Learning with Neural Networks, 2014.](https://arxiv.org/abs/1409.3215)
- [Show and Tell: A Neural Image Caption Generator, 2014.](https://arxiv.org/abs/1411.4555)
- [Learning to Execute, 2015.](http://arxiv.org/abs/1410.4615)
- [A Neural Conversational Model, 2015.](https://arxiv.org/abs/1506.05869)

### 9.2. Keras API

- [RepeatVector Keras API.](https://keras.io/layers/core/#repeatvector)
- [TimeDistributed Keras API.](https://keras.io/layers/wrappers/#timedistributed)
  
## 10. 심화

- EncoderDecoder LSTM 아키텍처의 이점을 얻을 수 있는 시퀀스 간 예측 문제 10가지를 나열합니다.
- 항의 수 또는 자릿수를 늘리고 모델을 조정하여 100% 정확도를 얻기.
- 모델 크기를 문제 복잡성(용어 및/또는 숫자)과 비교하는 연구를 설계합니다.
- 주어진 인스턴스에서 다양한 수의 용어를 지원하도록 예제를 업데이트하고 100% 정확도를 얻을 수 있는 모델.
- 빼기, 나누기 및 곱셈과 같은 다른 수학 연산에 대한 지원을 추가.

## 11. 요약

본 강의에서는 Encoder-Decoder LSTM model 의 개발 방법을 배웠습니다.

- Encoder-Decoder LSTM 아키텍쳐와 Keras에서 구현하는 방법을 배웠습니다.
- 덧셈sequence-to-sequence 예측 문제
