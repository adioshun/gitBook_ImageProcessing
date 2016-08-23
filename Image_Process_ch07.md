## Chapter 7. Image Restoration (복원)

### 7.1 Median Filter(Denoise filter)

필터 중간 값으로 대체 

### 7.2 Bilateral Filter(Denoise filter)
양방향 필터 

파라미터 = Domain(=x,y좌표) + Range(=color)

> 강사 추천 알고리즘 

* Adaptive Bilateral Filfer : 엣지의 방향을 고려한 개선 버젼 

### 7.3 Deblurring(Restoration)
Remove or minimize the degradation in image

Unsharp mask로 처리 불가 

Deconvolution/Deblurring problem 

#### Methodology 
1. Inverse filtering
2. Wiener filtering (Minimum mean square Error)
3. Constrained Least Squares(Regularization)
	* Regularized(High pass operator)
	* Bayesian(Prior model)


#### Degradation Function
* Blurring + noise
* PSF: Point Spread Function을 구하는 것이 중요함
	* Blurring models 
	* $NxN$ uniform/Gaussian matrix 
* Film gain Noise

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c2/Convolution_Illustrated_eng.png/270px-Convolution_Illustrated_eng.png)

#### (1) Inverse Filter
Reciprocal filter in the frequency domain 

#### (2) Wiener filtering
Minimum mean square Error

#### (3) Least Squares Method

> Pseudo-inverse Restoration??