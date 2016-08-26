## Chapter 17. Shape Matching



### 17.1 Chamfer Matching

Edge(템플릿)를 기반으로 매칭 하는것 -> Egde의 내부 크기의 합이 같은것 찾기
* Distance Transform 
* 숨은 그림 찾기와 같음 -> 원본에서 추출한 Edge를 기반으로 타겟그림을 훝어 보면서 찾아봄 
* 단점 : 크기, 회전에 약함, 
* 장점 : Rubust to clutter(배경)


![](http://cfile25.uf.tistory.com/image/1809E30F4B2759C21A76BB)

### 17.2 Histogram of Oriented gradient 
* Originated by SIFT descriptor
	* SIFT : Texture modeling
	* HOG : Object shapes modeling
* eg. human modeling, 보행자 인식 

![](http://www.mathworks.com/help/examples/vision/DetectPeopleExample_01.png)
