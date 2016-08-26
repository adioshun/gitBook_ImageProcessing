## Chapter 18. Object Tracking

### 18.1 Main Issues in Tracking 
* Statets : What to define as object tracking
	* Postiotion, size, shape, pose, ... 
* Likelihood Model : How to measure the similarity
	* Template(block) matching, color histogram, shape, ...
* Prior Model : How to model the motion trakectory
	* Prediction models
	* 이전의 위치를 가지고 다음 위치, 방향을 예측 
* Where to search for the object in the next frame 
	* Full search, sampling, ...
	* Particle filtering 

### 18.2 Tree categories (Tracking 방법론)
* Kalman filter method : 이동 지역을 예측하여 탐색/트래킹 (최고 높은값활용)
* Particle filter method : 주변을 랜덤하게 잡아 물체가 있는지 탐색/트래킹(평균 값 활용)
* CAMSHIFT : 다음장(_Chapter19_)에  추가 설명

> 강사의견 : 어느 필터를 사용해도 성능은 비슷 


### 18.3 Tracker
A Complete module with observation and prior model

* Illumination
* Deformation 

> Multiple trackers are designed in a tracking system 


### 18. 4 Basic Step
1. Detection 
	* Reference model 
2. Prediction (Optional: Kalman은 수행, Particle은 미수행)
	* Prior modeling
	> 이동하고자 하는 위치를 예측하고, 해당 위치에 detection된 물체가 있는지 탐색 
	> 주변을 다 검색하면 되지 왜 예측 하는가? -> 동일한 물체가 주변에 있을경우 구부 어려움, 움직임이 빠를경우 탐색할 주변이 넓어짐 

3. Matching
	* Observation model 
	* Likelihood model
	* Maximization 
4. Update (optional)
	* States updates
	* Reference model 
	* Expectation 


