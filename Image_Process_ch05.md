## Chapter 5. Image Morphology

정의 : Set operation to handle shapes and bundaries in the image

활용 분야

* Binary Image Processing
* OCR Preprocessing
* Fingerprint noise reduction
* Smoothing object boundaries
* Medical Image: Abnormal detection
* Skeletonized\(뼈대 모양으로 만들기\) & Outline Detection

![](http://what-when-how.com/wp-content/uploads/2012/07/tmp26dc186.png)

### 5.1 Erosion Operation \(침식연산\)

흰색 부분을 침식\(줄이는\) 작업, 필터 내부의 가장 낮은(어두운) 값으로 변환(and) - 침식연산

효과 : Reduction of Impulsive noise in the object

```python
erosion = cv2.erode(img, kernel, iterations=1)
  """
  img: erosion을 수행할 원본 이미지
  kernel: erosion을 위한 커널
  iterations: Erosion 반복 횟수
  """
```

### 5.2 Dilation Operation \(확산작업\)

흰색 부분을 팽창\(확장\)하는 작업, 필터 내부의 가장 높은(밝은) 값으로 변환(or) - 팽창연산

효과 : 이미지가 전체적으로 밝아님

```python
dilation = cv2.dilate(img, kernel, iterations=1)

```
---

  두 연산은 순서에 따라 서로 다른 기능을 한다. [[출처]](http://dyndy.tistory.com/252)
  - Opening 연산(Erode -> Dilate)  : 주로 작은 노이즈들을 제거하는데 사용한다.
  - Closing 연산(Dilate -> Erode) : 보통 한 객체를 추출했을 때 두개 이상의 작은 부분으로 나올 경우 큰 객체로 합칠 때 사용한다.


### 5.3 Opening Operation

* Erosion followd by dilation
* Smoothing object Boundaries
* Eliminating pin-like\(뾰족하게 튀어나온\) regions

### 5.4 Closing Operation

* Dilation followed by Erosion
* Connectiong small, and holes
* Smoothing Object boundaries

`cv2.morphologyEx()`` 함수는 **erosion**과 **dilation** 알고리즘을 이용해서 보다 향상된 이미지 변형 기능을 제공합니다.

```python
opening = cv2.morphologyEx(img1, cv2.MORPH_OPEN, kernel)
  """
  src: 이미지 변형을 적용할 원본 이미지. 예제 코드에서는 img1
  operation: 이미지 변형 오퍼레이션 종류
  - cv2.MORPH_OPEN: Opening을 수행
  - cv2.MORPH_CLOSE: Closing을 수행
  - cv2.MORPH_GRADIENT: Dilation 이미지와 Erosion 이미지의 차이를 나타냄
  - cv2.MORPH_TOPHAT: 원본 이미지와 opening한 이미지의 차이를 나타냄
  - cv2.MORPH_BLACKHAT: closing한 이미지와 원본 이미지의 차이를 나타냄
  kernel: 적용할 커널 매트릭스  
  """
```

커널 매트릭스 만들기 : 직사각형 모양으로 nxn 크기의 커널 매트릭스를 생성합니다.


```python
M1 = cv2.getStructuringElement(cv2.MORPH_RECT, (n, n)) #n=5
  """
  cv2.MORPH_ELLIPSE: 타원 모양으로 매트릭스를 생성
  cv2.MORPH_CROSS: 십자 모양으로 매트릭스를 생성
  """

M1 =  [[1 1 1 1 1]
      [1 1 1 1 1]
      [1 1 1 1 1]
      [1 1 1 1 1]
      [1 1 1 1 1]]
```







---

### 5.5  Top-Hat Operation

### 5.6 Well Operation
