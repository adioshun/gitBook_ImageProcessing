# 배경 제거 [[출처]](http://sams.epaiai.com/220664036092)

OpenCV는 배경제거를 편리하게 해주는 3가지 알고리즘을 지원합니다.
- BackgroundSubtractorMOG
- BackgroundSubtractorMOG2
- BackgroudSubtractorGMG : 가장 좋은 성능을 보임 



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