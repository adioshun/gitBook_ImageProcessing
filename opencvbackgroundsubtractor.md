
# 배경 제거 

## 2D에서 배경 제거 

고정된 물체와 움직이는 물체 구분과 같음 

OpenCV는 배경제거를 편리하게 해주는 3가지 알고리즘을 지원합니다.
- BackgroundSubtractorMOG
- BackgroundSubtractorMOG2
- BackgroudSubtractorGMG

> [OpenCV-Python 강좌 49편 : 비디오 또는 동영상에서 배경 제거하기](http://sams.epaiai.com/220664036092)


### [5.3.1 Background subtraction](http://www.ijaerd.com/papers/finished_papers/A%20Review%20of%20Object%20Detection%20and%20Tracking%20Methods-IJAERDV04I1045913.pdf)

Background subtraction [1-3] requires regular updating of the **background model** to adapt to gradual or fast illumination changes and motion changes. 

```
[1] S. Kumar and J. S. Yadav, “Video Object Extraction and its Tracking using Background Subtraction in Complex Environments”, Perspectives in Science vol.8, 317-322, 2016.
[2] S.H Shaikh, K. Saeed, N Chaki, “Chapter-2, Moving Object Detection Approaches, Challenges and Object Tracking”, Moving Object Detection using Background Subtraction, springerBriefs in Computer Science, , ISBN 978-3-319-07386-6, 2014.
[3] O Barnich and M. V. Droogenbroeck,” ViBe: A Universal Background Subtraction Algorithm for Video Sequences”, IEEE Transactions on Image Processing, 20(6), 1709-1724, 2011.
```


In [52], important background models developed up to 2014 are discussed. 
- 분류 기반 수학 모델 제안 `The authors presented a classification based on mathematical models.`

```
[52] T. Bouwmans, “Traditional and Recent Approaches In Back-Ground Modeling for Foreground Detection: An Overview” Computer Science Review, II-I2 31-66, 2014
```



An approach based on a self organizing method without any prior knowledge about the involved patterns is used
to generate the background in [53]. 
- The background model is updated by automatically learning background variations using a neural network. 

```
[53] L. Maddalena and A. Petrosino, A Self-Organizing Approach to Background Subtraction for Visual Surveillance Applications, IEEE Transactions on Image Processing, Vol. 17, No. 7, 2008.
```


To handle effectively both sudden and gradual background changes, in [54], authors used a learning based background subtraction approach. 
- They formulated the background modeling step as a dictionary learning problem, and the foreground detection step as a modified sparse coding problem. 

```
[54] C. Zhao, X. Wang, W. Cham, „Background Subtraction via Robust Dictionary Learning”, EURASIP Journal on Image and Video Processing, 2011: 972961,2011
```


In [55], the background is modelled by **rectangular regions**, described by a **color histogram** and a **texture measure**. 
- The current frame in which motion is to be detected is modelled in the same way as the background. 
- The motion is detected by comparing the corresponding rectangular regions computed at the coarsest scale to the finest scale. 
- The Guassian mixture method is finally applied at the finest scale.


```
[55] P.D Z. Varcheie, M. Sills-Lavoie G. Bilodeau, “A Multiscale Region-Based Motion Detection and Background Subtraction Algorithm”, Sensors, 10, 1041-1061, 2010.
```


대부분의 알고리즘은 모델링을 위한 초기 프레임을 활용 한다. `Most of the algorithms require a few initial frames to initialize the background. `


A technique, called “ViBe” (for “VIsual Background Extractor”) presented in [13], requires only a **single frame** to initialize the background. 
- This frees from the need to wait for several seconds to initialize the background model. 
- The authors modelled each background pixel with a set of samples and the current value of a pixel is compared to its closest samples within the collection of samples. 
- Since a background is non-static, the background model should be updated regularly. 
- For updating, the authors selected a value to be replaced randomly, in contrast to other approaches, where the oldest value is replaced first.

```
[13] O. Barnich and M. V. Droogenbroeck, “ViBe: A Universal Background Subtraction Algorithm for Video Sequences”, IEEE Transactions on Image Processing, 1709-1724, 2011.
```

To adapt to sudden or gradual illumination changes, a **probabilistic background** model based on kernel density estimation is proposed in [56]. 
- In this, the background is modelled as a probabilistic model. The kernel density estimation Guassian model is updated at every frame by controlling the learning rate according to the situation. 
- The updating method automatically adapts to various environments and stochastically deletes non-background information or adds new-background values.

```
[56] J. Lee, M. Park, “An Adaptive Background Subtraction Method Based on Kernel Density Estimation”, 12, 12279-12300, Sensors 2012.
```


A background modeling and object extraction module to enhance object extraction process is proposed by Kumar et. al.[1]. 
- The method uses a Kalman filter for tracking, and it consists of two stages. 
    1. The first stage uses the background subtraction and temporal difference mechanism to derive an approximate motion field and calculates regional entropy to get the actual moving pixels that have low entropy. 
    2. The second stage uses the Kalman filtering for object tracking. 
- Their method is found to be effective to eliminate ghost and aperture distortion.



```
[1] S. Kumar and J. S. Yadav, “Video Object Extraction and its Tracking using Background Subtraction in Complex Environments”, Perspectives in Science vol.8, 317-322, 2016.
```

---


# 배경 제거 [[출처]](http://sams.epaiai.com/220664036092)


## 0. 기본적 방법 (두 프레임간의 비교)

```python
import cv2
import numpy as np
camera = cv2.VideoCapture(0)
es = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (9,4))
kernel = np.ones((5,5),np.uint8)
background = None
while (True):
   ret, frame = camera.read()
     if background is None:
     background = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
     background = cv2.GaussianBlur(background, (21, 21), 0)
     continue

   gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
   gray_frame = cv2.GaussianBlur(gray_frame, (21, 21), 0)

   diff = cv2.absdiff(background, gray_frame)  ## 두 이미지의 차이 분석
   diff = cv2.threshold(diff, 25, 255, cv2.THRESH_BINARY)[1]
   diff = cv2.dilate(diff, es, iterations = 2)
   image, cnts, hierarchy = cv2.findContours(diff.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

   for c in cnts:
   if cv2.contourArea(c) < 1500:
     continue
   (x, y, w, h) = cv2.boundingRect(c)
   cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)

   cv2.imshow("contours", frame)
   cv2.imshow("dif", diff)
   if cv2.waitKey(1000 / 12) & 0xff == ord("q"):
     break
cv2.destroyAllWindows()
camera.release()

```
하지만 이 방식은 `학습 기반`이 아님, `그림자 인식`을 못함

---

OpenCV는 배경제거를 편리하게 해주는 알고리즘을 지원합니다.
- K-Nearest Neighbors (KNN) : ` BackgroundSubtractorKNN`
- BackgroundSubtractorMOG
- BackgroundSubtractorMOG2( Mixture of Gaussians)
- BackgroudSubtractorGMG( Geometric Multigrid) : 가장 좋은 성능을 보임 , 학습을 위한 프레임 지정(Default = 120frames)



## 1. BackgroundSubtractorMOG

> P.Kadew TraKuPong, R.Bowden,  'An improved adaptive background mixture model for real-time tracking with shadow detection', 2010

- 가우시안 믹스쳐 기반 배경/전경 분할 알고리즘
- K값이 3 또는 5인 가우시안 분포 믹스쳐를 배경 픽셀에 적용함으로써 배경 제거를 수행합니다. 믹스쳐에 대한 가중치는 영상에서 배경 제거를 위한 특정 픽셀이 동일한 장소에 머물고 있는 시간 비율을 나타냅니다.


## 2 BackgroundSubtractorMOG2

- 가우시안 믹스쳐 기반 배경/전경 분할 알고리즘
- 특징 : 각 픽셀에 적절한 가우시안 분포값을 선택 (cf. MOG는 k=3 또는 5를 가우시안 분포값으로 취함)
    - MOG2 알고리즘은 조명 상태의 변화로 장면이 변하더라도 제대로 배경제거를 해줍니다.
- 옵션으로 그림자 검출을 할지 말지를 지정할 수 있습니다. 그림자 검출을 하고자 하면 detectShadow = True


## 3. BackgroundSubtractorGMG

> Andrew B. Godbehere, Akihiro Matsukawa, Ken Goldberg, 'Visual Tracking of Human Visitor under varable-Lighting Condition for a Responsive Audio Art Installation' 2012

- 통계적 배경 이미지 제거와 픽셀 단위 베이지안 분할(Bayesian segmentation)을 결합한 것으로  

- GMG 알고리즘은 최초 몇 프레임(보통 120 프레임)을 배경 모델링을 위해 사용하며, 배경이 아닌 전경이나 움직이는 객체를 추출하기 위해 베이지안 추론을 이용합니다.

- GMG는 노이즈를 제거하기 위해 opening 기법을 적용하는 것이 더 좋다고 알려져 있습니다.

```python

import numpy as np
import cv2

def backSubtraction():
    cap = cv2.VideoCapture()

    kernel = cv2.getStructureingElement(cv2.MORPH_ELLIPSE, (3,3))
    fgbg = cv.bgsegm.createCackgroudSubtractorGMC()

    wile True:
        ret, frame = cap.read()
        fgmask = fgbg.apply(frame)
        fgmask = cv2.orphologyEx(fgmask, cv2.MORPH_OPEN, kernel)

        cv2.imshow("mask", fgmask)
        k = cv2.waitKey(1) & 0XFF
        if k ==27:
            break

    chap.release()
    cv2.destroyAllwindows()

```
