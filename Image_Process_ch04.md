## Chapter 4. Edge Detection 

### 4.1 Canny Edge Dectetor [1]
절차 
1. Smoothed imges(=Gaussin filter) : 노이즈 제거
2. First-difference approximation : 미분 연산을 통해 intensity가 급격한 곳 찾기
3. Compute gradient magnitude and orientation 
	* 4개의 섹터로 나눔 : Horizontal, vertical, diagonal, anti-diagonal
4. Nonmaximal suppression 
5. Thresholding and connected edge preserving 

### 4.2 SUSAN Edge Dectetor 
* Smallest Univalue Segment Assimilating Nucleus

![](http://users.fmrib.ox.ac.uk/~steve/susan/susan/img9.gif)

* Necleus : 중심 값 

---

## Coutour Detection 

Contour란 같은 값을 가진 곳을 연결한 선 (eg. 등고선)

이미지 Contour란 동일한 색 또는 동일한 색상 강도(Color Intensity)를 가진 부분의 가장 자리 경계를 연결한 선

사전 작업 : 정확한 이미지 Contour를 확보하기 위해 바이너리 이미지를 사용합니다. (eg. 희색과 검은색에서 그 경계선을 찾는 알고리즘)
 - 즉, 이미지에서 Contour를 찾기 전에 우리가 이미 배웠던 `threshold`나 `canny edge detection`을 적용

|OpenCV에서 Contour 찾기는 검정색 배경에서 흰색 물체를 찾는 것과 비슷합니다. <br> . 따라서 Contour를 찾고자 하는 대상은 흰색으로, 배경은 검정색으로 변경해야 함을 꼭 기억하세요!!|
|-|

```python
#Gray 스케일로 변환시킨 imgray를 thresholding 하여 그 값을 thresh로 합니다. 
ret, thr = cv2.threshold(imgray, 127, 255, 0) 


contours, hierarchy = cv2.findContours(threshold_image, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
    """
    Goal : 윤곽선들 찾기
    첫 번째 인자로 Contour를 찾을 행렬화된 이미지를 받는다. (중요 : 바이너리 이미지)
    두 번째 인자로 Contour 찾는 알고리즘을 준다. 
    . cv2.RETR_EXTERNAL: 이미지의 가장 바깥쪽의 contour만 추출
    . cv2.RETR_LIST: contour 간 계층구조 상관관계를 고려하지 않고 contour를 추출
    . cv2.RETR_CCOMP: 이미지에서 모든 contour를 추출한 후, 2단계 contour 계층 구조로 구성함. 1단계 계층에서는 외곽 경계 부분을, 2단계 계층에서는 구멍(hole)의 경계 부분을 나타내는 contour로 구성됨
    . cv2.RETR_TREE: 이미지에서 모든 contour를 추출하고 Contour들간의 상관관계를 추출함
    세 번째 인자로 찾은 컨투어를 저장할 때 방식을 준다. 
    .cv2.CHAIN_APPROX_NONE: contour를 구성하는 모든 점을 저장함.
    . cv2.CHAIN_APPROX_SIMPLE: contour의 수평, 수직, 대각선 방향의 점은 모두 버리고 끝 점만 남겨둠. 예를 들어 똑바로 세워진 직사각형의 경우, 4개 모서리점만 남기고 다 버림
    . cv2.CHAIN_APPROX_TC89__1: Teh-Chin 연결 근사 알고리즘(Teh-Chin chain approximation algorithm)을 적용함
    Return : contour들과 계층관계가 저장됨
    """


cv2.drawContours(original_image, [contours[ci]], 0, (255, 0, 255), 3)
    """
    Goal : 가장 큰 윤곽선을 지닌 Contour를 그린다.
    첫 번째 인자로 Countour를 그릴 도화지가 될 이미지를 받고
    두 번째 인자로 그릴 Contour를 받는다.
    . 이 값은 cv2.findContours() 함수의 2번째 리턴 값으로 리스트형 자료임. i번째 contour의 첫 번째 픽셀 좌표는 contours[i][0]과 같이 접근 가능
    세 번째 인자로 그려질 Contour의 인덱스 번호를 지정한다./ img에 실제로 그릴 contour 인덱스 파라미터
    . 여기선 Contour의 인덱스번호를 미리 지정해서(ci) 주었으므로 0으로 준다.
    . 이 값이 음수이면 모든 contour를 그림
    네 번째 인자로 그려질 선의 색깔을 지정하고 (B,G,R)
    다섯 번째 인자로 그려질 선의 굵기를 지정한다.
    """

```

### Contour활용 

도형의 특징 정보 도출 : 이미지 모멘트 이용
    - 이미지 모멘트 : 객체의 무게중심, 객체의 면적 등과 같은 특성을 계산할 때 유용(`cv2.moments`)
    

    모멘트 종류는 3가지이며 아래와 같이 총 24개의 값을 가집니다.
    1. 공간 모멘트(Spatial Moments) : m00, m10, m01, m20, m11, m02, m30, m21, m12, m03
    2. 중심 모멘트(Central Moments) : mu20, mu11, mu02, mu30, mu21, mu12, mu03
    3. 평준화된 중심 모멘트(Central Normalized Moments) : nu20, nu11, nu02, nu30, nu21, nu03
    



---

## 특징 검출 
#### 점(코너) 기반 접근

1) Moravec\`s Corner Detector -> 1977년 : Moravec이 코너점 찾는방법을 시작(1977)
2) Harris Corner Detector -> 1988년 : 해리스코너 방법으로 Harris/Plessey 코너검출기 제안됨
3) Shi and Tomasi Corner Detector ~
4) SUSAN(Smallest Univalue Segment Assimilating Nucleus) Corner Detector
5) Trajkovic and Hedley Corner Detector
6) Wang and Brady Corner Detector
7) High-Speed Corner Detector
8) FAST(Features from Accelerated Segment Test) Corner Detector -> openSURF에서 이방법으로 동작하는 예제가 있음

#### 영역 기반 접근
1) Harris Laplace Detector
2) Harris Affine Detector
3) Hessian Affine Detector
4) EBR(Edge-Based Region) Detector
5) IBR(Intensity-Based Region) Detector
6) MSER(Maximally Stable Extremal Region) Detector
7) Salient Region Detector

http://www.youtube.com/watch?v=ce2fXbCG3kw














---

[1]: john canny, "A Computational Approach to Edge Detection, 1986