# Chapter 15. Stereo Camera System


>  시차 영상(disparity image)= 깊이 영상(depth map)

## 1. Disparity Map 이란?

- Disparity는 Stereo 정합을 위한 두 이미지에서의 객체의 위치 상의 다른 차이
- 이 차이를 parallax 하게 만드는 것을 Disparity Map 

> 간단히 말해서 Pixel의 Disparity는 그 픽셀에 대한 최소 제곱 합들의 Shift value와 같다. 


## 2. 스테레오 비전의 3차원 거리 정보 계산

### 2.1 간단버젼 (Calculating Disparity Map)

1. 이미지들은 SSD/SAD 윈도우 연산에 용이하게 하기 위해 zero pixels의 frame으로 채워진다. 
    - SSD : Sum of Squared Differences 
    - SAD : Sum of Absolute Differences
2. 먼저 차이의 제곱이나 절대적인 차이를 화소마다 계산하고, 모든 값을 window W에 더한다.
3. 오른쪽 이미지의 각 shift value 값은 이미지 크기와 동일한 SSD / SAD map이다. 
    - Disparity Map은 3차원 공간에서부터 줄어든 2차원 Map이다. 
    - Pixel의 차이는 픽셀의 SSD/SAD 최소 시프트 값과 동일하다. 

### 2.2 기본 원리 

|![](https://i.imgur.com/0O5syLj.png)|![](https://i.imgur.com/lLTBv5r.png)|
|-|-|

3차원 거리 정보는 시차, 초점 거리(focal length), 베이스라인(baseline), 3가지 요소(factor)를 통해 획득 가능
- 시차(d:disparity): 위에서 언급했듯이, 좌/우 영상에서 동일하게 나타나는 물체에 대한 x축 위치 차이를 의미(아래 그림에서 'x' 를 의미)
- 초점 거리(f): 이미지 평면(ex. CCD, CMOS 센서)와 카메라 렌즈와의 거리
- 베이스라인(b): 좌/우 카메라의 간격

> f,b는 물리적 요소로 고정 상수, 실제 필요 한건 d (Stereo matching과정을 통해 도출)

---

## sample code

### python 

```python 
    1 import numpy as np
    2 import cv2
    3 from matplotlib import pyplot as plt
    4 
    5 imgL = cv2.imread('tsukuba_l.png',0)
    6 imgR = cv2.imread('tsukuba_r.png',0)
    7 
    8 stereo = cv2.StereoBM_create(numDisparities=16, blockSize=15)
    9 disparity = stereo.compute(imgL,imgR)
   10 plt.imshow(disparity,'gray')
   11 plt.show()
```

### c++

> https://eehoeskrap.tistory.com/103


### ROS



---

[참고자료]
- [OpenCV Manual](https://docs.opencv.org/3.1.0/dd/d53/tutorial_py_depthmap.html)
- [[Stereo Vision] Disparity Map 생성](https://eehoeskrap.tistory.com/103)
- [스테레오 비전(Stereo Vision)](https://blog.naver.com/dldlsrb45/220878488912): AnyTime2736



