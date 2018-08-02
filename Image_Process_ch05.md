## Chapter 5. Image Morphology

정의 : Set operation to handle shapes and bundaries in the image

활용 분야

* Binary Image Processing
* OCR Preprocessing 
* Fingerprint noise reduction
* Smoothing object boundaries
* Medical Image: Abnormal detection 
* Skeletonized\(뼈대 모양으로 만들기\) & Outline Detection

### 5.1 Erosion Operation \(침식연산\)

흰색 부분을 침식\(줄이는\) 작업, 필터 내부의 가장 낮은(어두운) 값으로 변환(and) - 침식연산

효과 : Reduction of Impulsive noise in the object

### 5.2 Dilation Operation \(확산작업\)

흰색 부분을 팽창\(확장\)하는 작업, 필터 내부의 가장 높은(밝은) 값으로 변환(or) - 팽창연산

효과 : 이미지가 전체적으로 밝아님

![](http://what-when-how.com/wp-content/uploads/2012/07/tmp26dc186.png)

두 연산은 순서에 따라 서로 다른 기능을 한다. 
 . Erode - Dilate = Opening 연산 : 주로 작은 노이즈들을 제거하는데 사용한다.
 . Dilate - Erode = Closing 연산 : 보통 한 객체를 추출했을 때 두개 이상의 작은 부분으로 나올 경우 큰 객체로 합칠 때 사용한다. 

출처: http://dyndy.tistory.com/252 [DY N DY]

### 5.3 Opening Operation

* Erosion followd by dilation
* Smoothing object Boundaries
* Eliminating pin-like\(뾰족하게 튀어나온\) regions

### 5.4 Closing Operation

* Dilation followed by Erosion
* Connectiong small, and holes
* Smoothing Object boundaries

![](http://www.xplorimaging.com/MorphologicalFilters.jpg)

### 5.5  Top-Hat Operation

### 5.6 Well Operation



