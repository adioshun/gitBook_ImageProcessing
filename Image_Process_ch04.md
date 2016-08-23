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