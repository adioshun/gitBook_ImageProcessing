# [3D Pose Estimation](http://blog.naver.com/samsjang/220664805818)

- 컴퓨터 비전 영역에서 Pose란 객체의 위치와 방향을 말합니다. (센서의 위치 방향 아님)
- 3D Pose Estimation이란 3차원 객체를 표현하고 있는 2차원 이미지에서 이 객체가 어떻게 변화되는가에 관한 문제로 귀결됩니다.


> Camera Calibration의 왜곡 현상 제거를 위한 정보를 3D Pose Estimation에 활용 가능 


## 1. 절차 

1. Camera Calibration 값을 저장한 파일에서 mtx, dist의 값을 얻습니다.
2. cv2.findChessboardCorners()로 체스판의 격자점들의 정보를 얻습니다.
3. 2번 절차가 성공적으로 수행되면 cv2.cornerSubPix()로 체스판 격자점에 대한 정확도를 높여줍니다.
4. cv2.solvePnPRansac()을 이용해 객체의 회전 및 이동에 관한 정보, 즉 Pose를 계산합니다.
5. cv2.projectPoints()를 이용해 실제 공간상의 객체의 Pose 정보를 2차원 화면상의 이미지의 Pose 정보로 변환합니다.
6. 화면상에 계산된 Pose 정보를 표현합니다.

![](https://i.imgur.com/M1RDHhl.png)

--- 

## Tip 

OpenCV보다 OpenGL을 이용해 보다 세밀한 객체를 그려서 활용하길 권장 