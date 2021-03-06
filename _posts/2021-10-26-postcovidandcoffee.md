---
title: 포스트 코로나 시대에서 원두커피의 미래예측[KNU 2021-2]
categories:
  - PYTHON
tags:
  - research
toc: true
---


# 포스트 코로나 시대에서 원두커피의 미래예측

## NodeXL을 사용한 SNA분석과 지수평활예측모델을 중심으로

# 1 서론

## 1.1 연구의 배경

근 2년간 코로나 바이러스의 일상화는 사회 전반적이나 생활면으로 많은 변화를 주고 있다. 그중 감염 확산으로 말미암은 재택근무는 전례 없이 큰 규모로 경제적 구조를 바꾸고 있는 상황으로 판매품목의 변화와 생필품의 수요 증가로 이어지고 있는 상황이다. 뉴욕타임스는 코로나 바이러스가 본격화된 이후, 미국의 소비자를 대상으로 소비심리 및 행태변화를 살펴보았다. 최근의 소비패턴은 식료품점의 수요가 급증하였는데, 이는 화장지, 파스타, 밀가루, 비누를 포함한 가정 내 필수품들뿐만 아니라 가공식품이 다시 유행되고 있는 것으로 보이며 급격하게 변화하는 것으로 나타났다(1). '네이버 데이터 랩'을 사용한 키워드조사에서는 '재택근무'키워드가 인기 있던 2020년 2월 20일 전후로 생필품과 식품류에 대한 검색량이 급증한 것을 확인할 수가 있었다.

코로나 바이러스로 인해 집에 머무는 시간이 늘어나며 집에서도 프리미엄 커피를 즐길 수 있는 '홈 카페'시장이 급증하면서 가정용 에스프레소머신 47% 캡슐커피 21% 전동 그라인더 29%, 드립 커피 추출기 21%, 커피메이커 8% 등의 판매증가가 이를 뒷받침하고 있음을 알 수 있었다.(2) 또한, 거리 두기가 강화될수록 실제 정부의 거리 두기 강화 조치와 월별 커피 수입 간 상관관계를 분석하면 두 지표 모두 동행하는 모습이 뚜렷한 현상을 보이고 있다. 인스턴트커피 역시 이전에는 제품군이 믹스커피에 집중된 시장에서 스페셜티, 캡슐, 드립커피 시장으로 확장되며 원두커피의 인기는 나날이 상향 평준화 되고 있는 경향으로 보이고 있다. 이러한 원두커피의 유행은 가정용 커피추출기의 보급을 가속화시켰으며 '프리미엄 홈 카페'의 유행으로 발전하였으며 글로벌 커피 전시회인 서울 카페 쇼에서 발표한 2022년 카페산업의 6개 키워드인 'A.L.W.A.Y.S'중 Y에 '스스로 만드는 스페셜한 커피(Yourself)'를 사용할 정도로 포스트(위드) 코로나 바이러스 시대에서 원두커피가 가져올 새로운 커피시장의 트렌드가 기대되고 있는 전망이다.(1) 따라서 본 연구는 포스트 코로나 바이러스 시대 때문에 발생할 사회적 변화가 이러한 원두커피의 유행을 지속시킬 수가 있을지에 대해 빅데이터 분석을 통해 알아보고자 한다.

## 1.2 연구 절차


본 연구는 현재 코로나 바이러스로인한 커피산업의 트렌드를 분석하고 포스트 코로나 바이러스 시대의 원두커피에 대한 관심도를 알아보고자 한다. 연구 대상으로는 인스턴트 커피와 원두커피에 해당하는 키워드들을 수집하고 포털사이트 '네이버'의 '네이버 데이터 랩'을 사용하여 10~40대까지의 커피 관련 검색키워드, 쇼핑행태를 분석하였으며, MZ세대와 Z세대(15~39세)중 23.6%가 사용하는 SNS인 'Twitter'의 트윗을 SNA(Social Network Analysis)를 통해 얻은 데이터를 분석해 전반적인 커피에 대한 연관 관심사와 긍·부정분석을 시행한다. 최종적으로는 포털사이트의 원두커피 관련 키워드 통계를 사용해 가까운 미래의 원두커피에 대한 관심도를 조사하고 예측하고자 한다.


# 2 본론
## 2.1 키워드 감정 분석

키워드 긍·부정 분석, 평가를 사용하는 소비자 인사이트 분석은 감정데이터를 사용해 키워드에 대한 긍·부정평가 통계를 제공하는 '(주)바이브컴퍼니'의 웹서비스인 '썸트렌드'의 긍·부정 분석과 연관어 분석을 통해 진행되었다. 조사대상 키워드는 원두커피에 대한 키워드와 믹스커피에대한 키워드를 사용하였으며 조사 대상 기간은 2021년 10월 5일부터 11월 5일까지의 데이터를 활용하였다. 믹스커피에 대한 썸트렌드 소셜 긍정 부정 키워드 조사결과 최대 31퍼센트의 부정 비율 9.1퍼센트의 중립 키워드와 76.1퍼센트의 긍정반응을 확인할 수가 있었다. 부정적 키워드에서는 1위로 '최악', 2위로 '갑질', 2, 3위로 '싫다', 4위로 '피곤하다', 5위로는 '좋지않다', 6위로는 '텁텁하다'의 부정적 키워드가 나타났으며, 긍정적 키워드로는 1위로 '맛있다', 2위로 '좋다', 3위로 '저가', 4위로 '달다', 5위로 '부드럽다', 6위로 무료의 긍정반응이 나타났다. 믹스커피에 대한 높은 부정적 평가를 조사하기 위해 연관어 분석을 사용했으며 연관어로는 '돈', '직원', '회사', '탕비실', 'imf'와 같은 회사, 사회생활과 연관된 키워드로 나타났다. 이는 회사생활에서 겪는 '갑질'이나 '피곤하다'의 감정으로 나타나며 회사 탕비실 등에 비축된 믹스커피와 맞물려 이러한 부정적인 분석결과를 만들어낸 것으로 볼 수 있었다. 



반면 원두커피에 대한 썸트렌드 소셜 긍부정조사결과에서는 최대 16.7퍼센트의 부정 비율 9.7퍼센트의 중립 비율과 83.7퍼센트의 긍정 반응률을 확인할 수 있었다. 부정적 키워드에서는 1위 '비싸다', 2위 '지치다', 3위 '춥다', 4위 '좋지않다', 5위 '종말'의 총 5개의 부정적 키워드만이 나타났으며 긍정적 키워드로는 1위 '맛있다', 2위 '고소하다', 3위 '행복', 4위 '진심', 5위'따뜻하다'등등의 다양한 긍정적 반응을 확인할 수 있었다. 부정적 키워드에서 '종말'은 홍보성 이벤트 트윗의 리트윗으로 말미암은 키워드 과대평가로 본 평가에서 제외하면 단 4개의 부정적 반응을 확인하였다.




## 2.2 키워드 평가 로직 개발

원두커피에 대한 전반적인 키워드 조사에서는 키워드분석에서의 오류와 같은 불필요한 리트윗 결과를 제외하기 위해 키워드 평가를 위한 개량된 로직 개발의 필요성이 제시되어 NodeXL을 사용해 데이터 추출방식에 변화를 주는 방식으로 진행하였다. 트위터에서 일상적인 대화나 개인적인 블로깅 트윗만을 추출하기 위하여 인용 RT나 개별 답글이 많아 노드의 네트워크 크기가 큰 경우를 필터링하는 방식을 사용하였으며 자체 개발된 단어분석기를 사용해 단어와 빈도수 순위 데이터를 얻은 후 이를 python으로 처리하였다. 



데이터 수집은 NodeXL의 TwitterSearchNetwork를 사용하여 5일간 2000개씩의 트윗을 수집하여 1만개의 트윗을 수집하였다. 수집된데이터는 Graph Metrics와 그래프의 크기로 Cluster그룹화하는 방식을 사용해 연구 목적에 부합하는 트윗만을 얻을수있었다. 정리된 트윗은 CSV파일로 저장되어 형태소 분석 처리되었다. 이 과정에서 Python의 Pandas라이브러리를 사용하였으며 자료정리는 Dictionary자료구조로 실행되었다. 도식화(플로우차트)된 프로그래밍 로직은 다음과 같다. 



최종적으로 WordCloud패키지를 사용하여 정리된 자료를 보기좋게 단어구름의 형태로 시각화 하였다. 프로그램 플로우차트의 키는 단어에 해당되며, value는 단어의 반복수이다. 단어 구름 출력 결과에서는 value를 순위로 변경하여 입력하였으며,  최종적으로 시각화된 단어구름의 결과물은 다음과 같다.
  


## 2.3 키워드 검색량 예측

본 연구에서는 '네이버 데이터랩'을 통해 얻은 시계열데이터를 통해 가까운 미래의 키워드 검색량을 예측하였다. 데이터는 총 3개로 샘플링된다. (분류1: 4년간 수집 데이터 ,분류2: 1년간 수집 데이터, 분류3: 1달간 수집데이터) 안정적인 데이터 분석을 위해 검색통계의 변동량(CV)가 작은 분류2의 데이터를 통해 예측한다. 통계의 자료는 7일의 계절성을 가지므로 예측모델은 지수평활법(ETS)을 사용하며 예측모델 평가지표는 직관적인 분석이 가능한 MAE(Mean-Absolute-Error), RMSE(Root-Mean-Square-Error)을 사용한다. MAE는 모델의 예측값의 차이를 모두 더하는 방식을 사용하는 평균절대 오차 값이며, 그 결과 값은 절댓값을 취하기 때문에 가장 직관적으로 오차를 파악할 수 있는 지표이다. RMSE는 예측값과 실제 관측값 간의 차이 측정 메트릭으로 표준편차의 값을 사용하는 평균제곱근 오차이다. 다음은 7일간의 검색예측을 진행한 결과이다.



# 3 결론
## 3.1 연구 결과

본 연구에서는 키워드 감정분석과 키워드 평가 로직, 키워드 검색량 예측의 3가지 방식을 통해 현재 원두커피에 대한 인식 분석과 수치통계 측면에서 가까운 미래 예측 결과를 도출하였다. 사회과학적 측면에서 수치통계를 사용해 먼 미래의 관심도를 측정하는 방식은 사실상 불가능하다. 실제로 믹스커피에 대한 관심도와 평행한 선을 그리던 원두커피는 예측 못한 코로나 바이러스로 인해 큰 상승폭을 보였다. 다만 사람들의 관심사와 감정을 분석하여 외부 영향이 없는 이상 이 수치가 거의 정확할 것이라는 추정만을 낼 수 있다. 감정 분석에서는 현재 믹스커피에 대한 대체적으로 부정적인 인식돠 함께 원두커피에 대한 긍정적인 인식에 대해 알아볼 수가 있었다. 자체 로직을 사용한 키워드 검색량 예측에서는 감정요소와 관련 키워드를 통합하여 개개인의 커피에대한 생각을 알아보았으며 이 두 가지 접근에서 사람들의 인식을 파악 할 수 있었다. 키워드 평가 로직에서는 원두커피에 관심이 있는 사람들은 단순히 커피를 음료만으로 보지 않고 하나의 문화로 보고 있다는 생각을 알 수 있었다. 로스팅 과정에 관한 관심으로부터 시작해 커피 로스터스에 대한 관심을 확인할 수 있었고 산미와 블렌드, 냄새와 같이 원두커피의 품질과 특성에 관한 관심과 콜드 브루, 드립 등에서 커피 추출 방식에 관한 관심을 확인할 수 있었다. 두가지 방식을 종합해 믹스커피는 점점 싼 맛으로 피곤함을 극복하기 위한 수단으로 여겨진다는 인사이트를 추정할 수 있었으며, 원두커피는 음료에서 벗어나 하나의 문화로 정착되고 있다는 인사이트를 추정할 수 있었다. 결론적으로는 현재 MZ세대, Z세대 커피트렌드는 원두커피이며 이들은 적극 원두커피에 대한 관심을 두고 있음을 확인할 수 있었다. 이는 코로나바이러스로 인한 재택근무로 프리미엄 홈 카페에 대한 관심이 생겨났고 이들은 앞으로도 큰 외적인 요소가 발생하지 않는 한 포스트 코로나 시대에도 원두커피에 꾸준한 관심을 둘 것이라는 전망을 보이고 있다. 커피시장 역시 지금과 같이 프리미엄 원두에 대한 소비자의 수요를 받아들여 꾸준한 커피의 고급화 연구와 다양한 니즈를 위해 드립 방식의 변화 등으로 관심을 충족시켜준다면 원두커피에 대한 관심이 점점 높아질 것으로 생각된다.

# 참고문헌

(1) L. Lauren & G. David. (2020. 4. 11). 
How the Virus Transformed the Way Americans Spend Their Money.
(https://www.nytimes.com/interactive/2020/04/11/business/economy/coronavirus-us-     economy-spending.html)

(2) 지피코리아(2020. 3 .20). ‘강제 홈족’ 증가에 홈카페 용품 판매 늘어
http://www.gpkorea.com/news/articleView.html?idxno=62688

(3) 뉴시스(2021. 10 .28). 서울카페쇼, 2022 커피 산업 키워드 'ALWAYS' 선정
https://newsis.com/view/?id=NISX20211028_0001630063


# PPT

![](/assets/img/covandcof/1.JPG)
![](/assets/img/covandcof/2.JPG)
![](/assets/img/covandcof/3.JPG)
![](/assets/img/covandcof/4.JPG)
![](/assets/img/covandcof/5.JPG)
![](/assets/img/covandcof/6.JPG)
![](/assets/img/covandcof/7.JPG)
![](/assets/img/covandcof/8.JPG)
![](/assets/img/covandcof/9.JPG)
![](/assets/img/covandcof/10.JPG)
![](/assets/img/covandcof/11.JPG)
![](/assets/img/covandcof/12.JPG)
![](/assets/img/covandcof/13.JPG)
![](/assets/img/covandcof/14.JPG)
![](/assets/img/covandcof/15.JPG)
![](/assets/img/covandcof/16.JPG)
![](/assets/img/covandcof/17.JPG)
![](/assets/img/covandcof/18.JPG)
![](/assets/img/covandcof/19.JPG)
![](/assets/img/covandcof/20.JPG)
![](/assets/img/covandcof/21.JPG)
![](/assets/img/covandcof/22.JPG)
![](/assets/img/covandcof/23.JPG)
![](/assets/img/covandcof/24.JPG)
![](/assets/img/covandcof/25.JPG)
![](/assets/img/covandcof/26.JPG)
![](/assets/img/covandcof/27.JPG)