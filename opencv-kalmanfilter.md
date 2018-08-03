# Kalman filter

기본 칼만 필터가 구현되어 있음 :The class implements a standard Kalman filter http://en.wikipedia.org/wiki/Kalman_filter, [Welch95]. 

확장 칼만 필터는 일부 matrix 수정을 통해 가능 : However, you can modify `transitionMatrix`, `controlMatrix`, and `measurementMatrix` to get an extended Kalman filter functionality.

## 생성 

```python
cv.CreateKalman(dynam_params, measure_params, control_params=0) 
""" The full constructor.
Parameters:	
- dynamParams – Dimensionality of the state.
- measureParams – Dimensionality of the measurement.
- controlParams – Dimensionality of the control vector.
- type – Type of the created matrices that should be CV_32F or CV_64F.
"""

kalman = cv.KalmanFilter(2, 1, 0)
kalman.transitionMatrix = np.array([[1., 1.], [0., 1.]])
kalman.measurementMatrix = 1. * np.ones((1, 2))
kalman.processNoiseCov = 1e-5 * np.eye(2)
kalman.measurementNoiseCov = 1e-1 * np.ones((1, 1))
kalman.errorCovPost = 1. * np.ones((2, 2))
kalman.statePost = 0.1 * np.random.randn(2, 1)

```

## Prediction


```python 
cv.KalmanPredict(kalman, control=None)
"""Computes a predicted state.
Parameters:
-Control – The optional input control
"""

prediction = kalman.predict()

```
## Update

```python 
cv.KalmanCorrect(kalman, measurement)
"""Updates the predicted state from the measurement.
Parameters:
-Control – The optional input control
"""

measurement = kalman.measurementNoiseCov * np.random.randn(1, 1)
measurement = np.dot(kalman.measurementMatrix, state) + measurement
kalman.correct(measurement)

```