## Chapter 3. Image Enhancement

### 1. Contrast Enhancement

#### 1.1 linear contrast stretching
* Thresholding을 정하고 크면 1, 작으면 0 등으로 변환 -> 사람의 간섭(쓰레쉬홀드 지정) 필요
![](https://thilinasameera.files.wordpress.com/2011/03/clip_image002.gif)

#### 1.2 Histogram Equalization [^1] 
* 색상 분포(Histogram)를 평활화 함 -> 완전 자동화, 사람의 간섭(파라미터 입력) 없음
* 단점 : 노이즈도 커지게 됨 -> 노이즈 제거 기술을 같이 사용함
![](http://www.cs.utah.edu/~jfishbau/improc/project2/images/crowd_hist_compare.png)

* Color Histogram Equalization은 RGB대신 HSV로 변환 하여 주로 사용

* Clipping Histogram Equaztion : 확률적으로 낮은 Intensity의 경우 해결 방법 -> 윗 부분을 잘라서 다른곳에 보상
![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5f/Clahe-redist.svg/300px-Clahe-redist.svg.png)



### 2. High Pass Filter
* Sharpenning Images = Edge Detection 
* 뺄셈 연산 


### 3. Unsharp Mask
* High Pass filter의 문제점 해결 
  (노이즈 생김 -> 노이즈 제거 하기 위해 Low pass fiter하면 Sharp가 없어짐) 

#### 3.1 Laplacian operator 

> 변곡점 = 기울기가 최대 = 두번 미분 한것이 0
> ![](http://docs.opencv.org/2.4/_images/Laplace_Operator_Tutorial_Theory_ddIntensity.jpg)
> 위로 볼록(Convex), 아래로 볼록(concave)

![](http://nullprogram.com/img/sharpen/example.png)

1. Orginal signal을 Moving Average(Lowpass filter)하여 Blurred signal 을 만듬 
2. 아래 볼록이면 -, 위로 복록이면 + 하여 Unsharp Mask 생성 -> Laplacian operator
3. Original signal과 Uncharp mask를 적용하여 Sharpened Signal 도출 


### 4. Fog(Haze) Removal
* 안개 제거 






---
[^1]: http://dsp.stackexchange.com/questions/13834/why-is-histogram-equalization-called-as-equalization