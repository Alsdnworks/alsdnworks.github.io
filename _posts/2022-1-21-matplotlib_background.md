---
title: Matplotlib imshow를 사용해 차트의 배경 그리기

categories:
  - PYTHON

tags:
  - analyze
  - data

toc: true
---

"다양한 기법을 사용하는 리스크 매트릭스 제작" 포스팅에서 R과 Python을 사용한 scatterchart를 제작하였습니다. R에서는 geom_rect을 통해 사각형을 그리는 방식으로 차트의 배경을 그렸는데, Python에서는 imshow와 필요에 따라 pandas의 구간화 함수인 cut을 통해 이와 같은 scatterchart를 그릴 수 있습니다. <br>

![img1](/assets/img/plot_risk.png)

## 1. Heatmap 그리기

https://matplotlib.org/stable/gallery/images_contours_and_fields/image_annotated_heatmap.html

다음을 참고하여 heatmap을 생성하면 알 수 있다시피 array의 value에 맞게 색상이 변하는 것을 확인할 수 있습니다.

색상은 imshow(data,cmap='Colors')의 Colors부분을 변경함으로써 바꿀 수 있습니다. 색상표는 사이트에서 확인 가능합니다.

https://matplotlib.org/stable/tutorials/colors/colormaps.html

예제의 색으로 볼 때 가장 적합한 색상표인 RdYlGn_r을 사용합니다.  

![img1](/assets/img/pl7.png)

다음은 matplotlib예제에 RdYlGn_r을 적용한 후, 위의 차트와 유사한 그리드를 그리기 위한 코드입니다.

~~~python

import numpy as np
import matplotlib.pyplot as plt

#그리드의 바운더리 값
bound=[0,0.5,2,4.25,7.125,10]

#그리드의 값 
legend_name= ['','V.Low','Low','Medium','High','V.High']

#컬러 맵에 적용될 색상 데이터
colors = np.array([[2, 3, 3, 5, 5],
                    [1, 2, 3, 4, 5],
                    [1, 2, 3, 4, 4],
                    [1, 2, 3, 3, 4],
                    [1, 1, 2, 3, 3]])

plt.figure(figsize=(10,10))
plt.xlim(0,10)
plt.ylim(0,10)
plt.xlabel('Time impact',fontsize=15)
plt.ylabel('Probabilty',fontsize=15)
plt.xticks(bound,legend_name)
plt.yticks(bound,legend_name)
plt.grid(True,color='k', linestyle='-', linewidth=1)
im=plt.imshow(colors,cmap='RdYlGn_r',extent=[0,10,0,10])
plt.show()

~~~

![img1](/assets/img/erroutput.png)


## 2. 차트 선에 맞게 heatmap조정

위에서 생성된 그래프는 2개 그래프를 겹친 것뿐이므로 그리드와 히트맵은 각기 다른 형태로 출력됩니다. 이를 해결하기 위해 히트맵을 더욱 잘게 쪼개야 함을 알수있습니다. 경우에 따라 다르지만 그리드가 동적으로 변경되는 프로그램의 경우 히트맵을 픽셀 단위까지 쪼개야 최고 품질의 결과물을 얻을 수 있습니다. 이 분할 값을 알기 위해서는 matplotlib의 figsize가 사용하는 단위를 알 필요가 있습니다.

![img1](/assets/img/sq_5.png)

figsize는 인치 단위로 조정됩니다. 또한 플롯을 저장할 때 dpi(dots-per-inch) 파라미터를 사용해 이미지로 내 보낼 수 있습니다. 이때 dpi가 300일 때 현재 사용 중인 figsize는 (10,10)이므로 (300x10) x(300x10)인 3000x3000=9000000 px임을 알수있습니다. <br>

이를 위해 9000000개 컬러 버퍼를 채울 필요는 없습니다.
판다스에서 제공하는 구간화 함수를 사용해 현재 사용 중인 컬러 버퍼를 3000x3000 어레이에 저장해보겠습니다. 

~~~PYTHON
#보더에 맞게 값을 조정하기 위해 pandas의 cut을 사용할 것이다.
import pandas as pd

#픽셀 라인을 생성
main=np.linspace(min(bound),max(bound),3000)

#보더의 값에 맞추어 x방향 색상을 적용
matrix_x= []
for i in range(len(colors)):
    matrix_x.append(list(pd.cut(list(main),bins=list(bound),labels=list(colors[i]),ordered=False,duplicates='raise')))
out=np.array(matrix_x)

#어레이를 시계방향으로 회전시키고 nan값 제거
out=np.rot90(out,-1)
out=np.nan_to_num(out)

#보더의 값에 맞추어 y방향 색상을 적용(회전하였으므로 실제로는 x방향으로 계산됨)
#x, y보더가 다른 경우 값이 다른 보더를 생성해 적용하면 된다.
matrix_y= []
for i in range(len(out)):
    matrix_y.append(list(pd.cut(list(main),bins=list(bound),labels=list(out[i]),ordered=False,duplicates='raise')))    
out=np.array(matrix_y)

#어레이를 반시계 방향으로 회전시켜 원래대로 복귀
out=np.rot90(out,1)

#보더에 맞게 색상 데이터가 적용된 어레이를 cmap과 출력
im=plt.imshow(out,cmap='RdYlGn_r',extent=[0,10,0,10])
~~~

## 3. Patch 적용하기

~~~ PYTHON
#matplotlib의 patch를 사용하기 위해 import 한다.
import matplotlib.patches as mpatches

#scatter chart 데이터 a=x, b=y, c=scatter의 ID 겸 컬러 데이터
a = np.random.randint(0, 10, 20)
b = np.random.randint(0, 10, 20)
c = np.linspace(0,20,20)

#색상 데이터의 unique값을 사용해 색상을 추출하여 legend에 적용될 패치를 생성
values = np.unique(colors.ravel())
values = values[np.logical_not(np.isnan(values))]
color_tags = [ im.cmap(im.norm(value)) for value in values]   
patches = [ mpatches.Patch(color=color_tags[i], label=" ".format(l=values[i]) ) for i in range(len(values)) ]
for i in range(len(values)):
    patches[i]._label=legend_name[i+1]

#위의 차트와 비슷한 scatter차트와 레이아웃을 작성
plt.scatter(a,b, c=c, s=1000,linewidths=1,cmap='RdYlGn_r',edgecolors='black')
for i in range(len(a)):
    plt.annotate('ID'+'%d'% c[i],(a[i]-0.15, b[i]-0.10),fontsize=10)
plt.legend(title='COST',handles=patches,loc='lower center',ncol=5,fontsize=10,bbox_to_anchor=(0.5, -0.15),fancybox=True)

plt.show()
~~~

![img1](/assets/img/outfig.png)

