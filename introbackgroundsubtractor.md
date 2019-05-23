Background Subtraction

background subtraction을 하기 위한 첫 단계는 Background Modelling이다. 배경 추출 알고리즘의 핵심은 배경 모델링이 움직이는 객체에를 충분히 인식해야 한다는 점이다. 배경 모델링을 하는데 좋은 방법은 두 가지 정도 있는데 Mean filter와 Median filter가 있다. 이는 배경 모델링에서 가장 많이 사용하고 있는 방법이다. 배경 추출 방법은 현재 이미지와 움지기는 객체를 탐지하기 위한 배경 이미지의 차이를 이용하는 방법을 사용하게 된다. 그러나 주위 외부 환경 변화에 대해 굉장히 민감한 특징이있다. 하지만 이는 배경에 대한 정보를 알고 있는 경우, 객체의 대한 정보를 완벽하게 얻을 수 있다. 배경 추출 방법은 2가지 정도의 접근법이 존재한다.



1. Recursive Algorithm 

2. Non-recursive Algorithm



또한, 대표적인 방법으론 Gaussian of Mixture(가우시안 혼합모델), Approximate median, Adaptive background가 있다. 



가우시안 혼합 모델은 여러 개의 가우시안 확률밀도함수로 데이터의 분포를 모델링 하는 방법이다. 영상에서는 각각의 픽셀 값을 의미하며 (Gray scale, 0~255), 배경의 학습을 통해 배경 모델이 형성된다. 이를 이용해 배경을 따로 추출해 낼 수 있는 것이다. 배경에 대한 학습(Learning)이 이루어지는 과정에서 배경과 객체를 분리하고, 깔끔한 객체를 따내기 위해선 Median Filter를 적용하는 과정을 거치면 된다. 



출처: https://eehoeskrap.tistory.com/90 [Enough is not enough]