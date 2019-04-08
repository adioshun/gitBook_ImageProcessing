# 스테레오 정합(Stereo matching)

## 1. 정의 

- 정의 : 스테레오 정합은 기준 영상(reference image)에서의 한 점에 대한 동일한 점(= 똑같은 점)을 목표 영상(target image)에서 찾는 과정
- 스테레오 비전을 설명할 때 나온 '시차(disparity)'를 계산하는 과정이라고 이해하면 됨


## 2. 방법 


### 2.1 전역 정합(global matching) 방법

- 원리 : 에너지 함수를 최소화하는 경우를 찾음으로써 최적의 깊이 영상을 만들어 냄
    - 영상이 가장 매끄러운, stereo vision의 제약 조건 중 하나인 smoothness를 충족하는
- 단점 : 영상의 일정 부분이 아니라 영상의 전체적인 정보를 사용하고 알고리즘이 복잡
- 장점 : 매우 정확한 깊이 영상을 추출 가능
- 종류 : Belief propagation, dynamic programming, semi-global matching, graph-cut 등
    - 일반적으로 Semi-global matching이 주로 사용됨




### 2.2 지역 정합(local matching) 방법 


- 원리 : 영역 기반(area-based) 정합 방법
    - 일정 영역(=윈도우, window)의 픽셀 정보들을 이용하여 정합점을 찾음
- 단점 : 일정 크기의 윈도우 내 픽셀 정보들만을 사용하여 정합점을 찾기 때문에 전역 정합 방법에 비해 상대적으로 부정확한 깊이 영상을 출력
- 장점 : 전역 정합 방법에 비해 알고리즘이 단순하고 빠르게 계산가능 
    - 전역 정합 방법의 초기 정합 비용(initial matching cost, 아래의 그림에서는 'matching cost cube'를 의미)을 계산하는데 사용
- 종류 : SAD(Sum of absolute difference), SSD(Sum of squared difference), NCC(normalized cross correlation), Census transform, Rank transform 등


> 지역 정합 방법은 영역 기반외 특징 기반(feature-based) 정합 방법도 있지만 최근에는 사용 하지 않음 

#### A. SAD(Sum of absolute difference), SSD(Sum of squared difference)



> [상세 내용](https://blog.naver.com/dldlsrb45/220879732646)

#### B. Census transform, Rank transform

> [상세 내용](https://blog.naver.com/dldlsrb45/220880488720)


### 3. 지역 정합 방법 통한 시차 계산 방법 (Depth map생성 방법) 

**윈도우(window)**를 기반으로 **시차 탐색 범위(disparity search range)**내의 픽셀들에 대해 **정합 비용(matching cost)**를 계산한 후, 시차를 계산

- 정합 비용(matching cost): 윈도우들간의 비유사도(similarity)의 값
- 시차 탐색 범위(disparity search range): 시차 값의 최대 범위( or 정합점을 찾는 최대 범위
- 윈도우: 커널(kernel)이라고 생각

![](https://i.imgur.com/JxsLFeh.png)

- 기준 영상(left image)의 한점과 목표 영상(right image)의 시차 탐색 범위 내 모든 픽셀에 윈도우를 설정
- 이때 좌측에 설정된 윈도우와 우측에 시차 탐색 범위만큼 설정된 윈도우들간의 정합 비용(비유사도)를 추정
    - 비유사도를 측정하는 방법은 SAD, SSD, NCC, Census transform, Rank 무엇을 사용하느냐에 따라 다름
- 계산된 정합 비용들은 아래의 그림에서 그래프 처럼 나타낼 수 있음 
    -  x축은 시차 탐색 범위 내 시차 값들, y축은 정합 비용 
- 그 중에서 가장 작은 정합 비용을 가지는 시차를 선택함(='winner-takes-all')
    - 그림에서는 'a' 라는 시차를 출력함

---

- [스테레오 정합(Stereo matching)](https://blog.naver.com/dldlsrb45/220879295400)
- Facebook Slam KR - Stereo Matching : [[Youtube]](https://youtu.be/fAotZkqusCE?t=1842), [[ppt]](http://bitly.kr/mZNRD)
