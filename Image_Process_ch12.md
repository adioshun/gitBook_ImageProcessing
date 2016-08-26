## Chapter 12. SIFT

### 12.1 How to make (invariant) feature
1. Scale-space extreme point detection
2. Key point localization 
3. Orientation assignment
4. Keypoint descriptor

###### 1. Scale-space extreme point detection

![](https://sensblogs.files.wordpress.com/2011/08/dog-computation.jpg)


1. 원본 크기의 gaussian 레이어를 여러게 만듬(??) -> 서로 다른 해상도(blur)된 것들도 탐지 하려고 

2. 두개의 guassian의 차를 구하여 Difference of Gaussian 생성 -> Laplacian of gaussian 연산 대신(이연산은 왜하나?) 

3. 원본대비 크기가 다른(1/2) 각 레이어들(Octave) 생성 -> 크기가 다른 그림도 탐지 할려고 

###### 2. Key point localization 

Local extrema detextion of DOG

###### 3. Orientation Assignment 

대상 이미지가 원본 이미지 대비 몇도 회전 하였는지 모르므로 






---

> 추천 저널 : IJCV / IEEE PAM(?)(pattern analysis machine?)



