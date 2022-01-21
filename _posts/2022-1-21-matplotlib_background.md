---
title: Matplotlib imshow를 사용해 차트의 배경 그리기(작성중)

categories:
  - PYTHON

tags:
  - analyze
  - data

toc: true
---

"다양한 기법을 사용하는 리스크 매트릭스 제작" 포스팅에서 R과 Python을 사용한 scatterchart를 제작하였습니다. R에서는 geom_rect을 통해 사각형을 그리는 방식으로 차트의 배경을 그렸는데, Python에서는 matplotlib imshow와 scatter을 통해 이와 같은 scatterchart를 그릴수 있습니다.

![img1](/assets/img/plot_risk.png)
Ptyhon으로 다음과 같은 scatterchart를 그리는 방법을 알아보겠습니다.

결과물은 용역회사에 제공되는 프로그램으로 제작되어 전체 코드는 공개할수없으며 도식화된 방식과 matplotlib 레퍼런스로 제공되는 코드를 사용해 과정을 설명하겠습니다. 

1. Heatmap그리기

https://matplotlib.org/stable/gallery/images_contours_and_fields/image_annotated_heatmap.html

다음을 참고하여 heatmap을 생성하면 알 수있다시피 array의 value에 맞게 색상이 변하는 것을 확인할 수 있습니다.

색상은 imshow(data,cmap='Colors')의 Color부분을 변경함으로서 바꿀수있습니다. 색상표는 사이트에서 확인 가능합니다.

https://matplotlib.org/stable/tutorials/colors/colormaps.html

예제의 색으로 볼때 가장 적합한 색상표인 RdYlGn_r을 사용합니다.  

![img1](/assets/img/pl7.png)

다음은 matplotlib예제에 RdYlGn_r을 적용한 후 
vegetables, farmers, harvest를 편집한 결과입니다.

![img1](/assets/img/figure_1.png)

~~~python
vegetables = ["cucumber", "tomato", "lettuce", "asparagus",
              "potato"]
farmers = ["Farmer Joe", "Upland Bros.", "Smith Gardening",
           "Agrifun", "Organiculture"]

harvest = np.array([[2, 3, 3, 5, 5],
                    [1, 2, 3, 4, 5],
                    [1, 2, 3, 4, 4],
                    [1, 2, 3, 3, 4],
                    [1, 1, 2, 3, 3]])

fig, ax = plt.subplots()
im = ax.imshow(harvest,cmap='RdYlGn_r')
~~~

2. scatterplot그리기

3. 차트에 선넣기

4. 차트 선에 맞게 heatmap조정