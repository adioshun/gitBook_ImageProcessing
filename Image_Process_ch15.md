# Chapter 15. Stereo Camera System


>  시차 영상(disparity image)= 깊이 영상(depth map)

## 1. Disparity Map 이란?

- Disparity는 Stereo 정합을 위한 두 이미지에서의 객체의 위치 상의 다른 차이
- 이 차이를 parallax 하게 만드는 것을 Disparity Map 

> 간단히 말해서 Pixel의 Disparity는 그 픽셀에 대한 최소 제곱 합들의 Shift value와 같다. 

## 2. 절차 (Calculating Disparity Map)

1. 이미지들은 SSD/SAD 윈도우 연산에 용이하게 하기 위해 zero pixels의 frame으로 채워진다. 
    - SSD : Sum of Squared Differences 
    - SAD : Sum of Absolute Differences
2. 먼저 차이의 제곱이나 절대적인 차이를 화소마다 계산하고, 모든 값을 window W에 더한다.
3. 오른쪽 이미지의 각 shift value 값은 이미지 크기와 동일한 SSD / SAD map이다. 
    - Disparity Map은 3차원 공간에서부터 줄어든 2차원 Map이다. 
    - Pixel의 차이는 픽셀의 SSD/SAD 최소 시프트 값과 동일하다. 



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



