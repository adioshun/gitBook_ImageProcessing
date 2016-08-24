## Chapter 8. Super Resolution(SR)

SR is the process of obtaining the higher resolution information from low resolution observations

Super Resolution(SR) = Bandwidth Extrapolation

SR is 은 Interpolation(중간 삽입->확대), Restoration(복원)과는 다름


> 최근은 카메라의 화소수가 늘어 나면서 중요성이 많이 줄어듬


### 8.1 Reconstruction Based SR 
* Based on sampling theories
* 저 해상도 카메라로 특정 대상을 여러번 찍음(약간의 움직임 포함) -> 여러 샘플링을 얻음 
* 제약 : HR images must be very close to the input images when smoothed and down-sampled
* SR is an ill-posed inverse problem -> __Regularization__ is needed --> Regularization makes too smooth
* 대부분의 SR 알고리즘이 Reconstruction 기반이다. [1]
	* POCS Approach 
	* Frequency Domain Approach 


### 8.1 Learning Based SR 
* 머신러닝을 이용하여 고해상도에 대한 학습을 수행후 저 해상도을 획득시 고 해상도를 유추함
* 제약사항 : Train data는 저해상도의 유사한(=동일한) 대상의 고해상도 사진 필요/활용 (eg. 카페트의 윗부분 패턴= 고해상도 Train data)
	* 해결 제안 : single imagse SR(하나의 사진에서 고해상도, 저해상도 동시 추출)[2]
* 활용 예 
	* Hallucinating(환상) Face in video
	* Satellite Imageing
	* Low Bit rate video transmission 
	* video encryption (고해상도 이미지를 나누어 가짐)



---
[1]: M.G Kang, "Super resolution image reconstruction", IEEE signal processing magazone, pp. 21-36, May 2003
[2]: D. Glasner, "Super resolution from a single image", 2009 CVPR

