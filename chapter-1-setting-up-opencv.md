## Chapter 1. Setting Up OpenCV

[TOC]

#### 관련 라이브러리 

* NumPy: This library is a dependency of OpenCV’s Python bindings. It provides numeric computing functionality, including efficient arrays.
* SciPy: This library is a scientific computing library that is closely related to NumPy. It is not required by OpenCV, but it is useful for manipulating data in OpenCV images.
* OpenNI(옵션): This library is an optional dependency of OpenCV. It adds the support for certain depth cameras, such as Asus XtionPRO.
* SensorKinect(옵션): This library is an OpenNI plugin and optional dependency of OpenCV. It adds support for the Microsoft Kinect depth camera.

## Chapter 2. Handling Files, Cameras, and GUIs

### 2.1 Basic I/O scripts
#### Reading/writing an image file
we can create a black square image from scratch by simply creating a 2D NumPy array: `img = numpy.zeros((3,3), dtype=numpy.uint8)`

If we print this image to a console, we obtain the following result:
```python
array([[0, 0, 0],
	   [0, 0, 0],
	   [0, 0, 0]], dtype=uint8)
```
Each pixel is represented by a __single 8-bit__ integer, which means that the values for each pixel are in the __0-255 range__.

Let’s now convert this image into Blue-green-red (BGR) using cv2.cvtColor:`img = cv2.cvtColor(img, cv2.COLOR_GRAY2BGR)`
Let’s observe how the image has changed:
```python
array([[[0, 0, 0],
	   [0, 0, 0],
	   [0, 0, 0]],

	   [[0, 0, 0],
	   [0, 0, 0],
	   [0, 0, 0]],

	   [[0, 0, 0],
	   [0, 0, 0],
	   [0, 0, 0]]], dtype=uint8)
```
As you can see, each pixel is now represented by a __three-element array__, with each integer representing the __B, G, and R channels__.


You can check the structure of an image by inspecting the `shape` property, which returns rows, columns, and the number of channels
```python
img = numpy.zeros((3,3), dtype=numpy.uint8)
img.shape # 반환값 : 3,3 
img = cv2.cvtColor(img, cv2.COLOR_GRAY2BGR)
img.shape # 반환값 : 3,3,3
``` 

Images can be loaded from one file format and saved to another. 
```python
image = cv2.imread('MyPic.png')
cv2.imwrite('MyPic.jpg', image)
```

By default, `imread()` returns an image in the __BGR color__ format even if the file uses a grayscale format.
Optionally, we may specify the mode of imread() to be one of the following enumerators:

* IMREAD_ANYCOLOR = 4
* IMREAD_ANYDEPTH = 2
* IMREAD_COLOR = 1
* IMREAD_GRAYSCALE = 0
* IMREAD_LOAD_GDAL = 8
* IMREAD_UNCHANGED = -1

```python
grayImage = cv2.imread('MyPic.png', cv2.IMREAD_GRAYSCALE) # load a PNG file as a grayscale image
cv2.imwrite('MyPicGray.png', grayImage) #save it as a grayscale PNG image
```

> __조심__ : `imread()` discards any alpha channel (transparency).

### 2.2 Converting between an image and raw bytes
An 8-bit grayscale image is a 2D array containing byte values. 
A 24-bit BGR image is a 3D array, which also contains byte values.

We may access these values by using an expression, such as `image[0, 0]` or `image[0, 0, 0]`. 

* The first index is the pixel’s y coordinate or row, 0 being the top. 
* The second index is the pixel’s x coordinate or column, 0 being the leftmost. 
* The third index (if applicable) represents a color channel.

> For example, in an 8-bit grayscale image with a white pixel in the upper-left corner, image[0, 0] is 255. 
> For a 24-bit BGR image with a blue pixel in the upper-left corner, image[0, 0] is [255, 0, 0].

we can cast and then reshape ___bytearray___ to get a ___numpy.array type___ that is an image:

```python
grayImage = numpy.array(grayByteArray).reshape(height, width)
bgrImage = numpy.array(bgrByteArray).reshape(height, width, 3)
```
### 2.3 Accessing image data with numpy.array
##### 기본적 방법
Manipulate a pixel at the coordinates, __(0, 0)__, of a BGR image and turn it into a __white__ pixel :`img[0,0] = [255, 255, 255]`

##### Numpy.array 활용 방법

* `item()` 
	* 1st 인자 : x (or left) position
	* 2nd 인자 : y (or top)
	* 3rd 인자 : index within the array at (x, y) position  
	> Remember that in a BGR image, the data at a certain position is a three-element array containing the B, G, and R values in this order
	* 반환값 : the value at the index position
	* eg: `print img.item(150, 120, 0)`

* `itemset()` : sets the value of a particular channel of a particular pixel to a specified value
	* 1st 인자 : a three-element tuple (x, y, and index) 
	* 2nd 인자 : the new value
	* eg: `img.itemset( (150, 120, 0), 255)`



		* print img.shape : NumPy returns a tuple containing the width, height, and—if the image is in color—the number of channels.
		* print img.size : This property refers to the size of an image in pixels.
		* print img.dtype : This property refers to the datatype used for an image



### 2.4 Reading/writing a video file
##### 지원 동영상 

|코드|코덱| 확장자|
|-|-|-|
|cv2.VideoWriter_fourcc('I','4','2','0')|uncompressed YUV encoding, 4:2:0 chroma subsampled|AVI|
|cv2.VideoWriter_fourcc('P','I','M','1')|MPEG-1|AVI|
|cv2.VideoWriter_fourcc('X','V','I','D')|MPEG-4|AVI|
|cv2.VideoWriter_fourcc('T','H','E','O')|Ogg Vorbis|OGV|
|cv2.VideoWriter_fourcc('F','L','V','1')|Flash video|FLV|

```Python
# reads frames from one AVI file and writes them to another with a YUV encoding
import cv2
videoCapture = cv2.VideoCapture('MyInputVid.avi')
fps = videoCapture.get(cv2.CAP_PROP_FPS)
size = (int(videoCapture.get(cv2.CAP_PROP_FRAME_WIDTH)),int(videoCapture.get(cv2.CAP_PROP_FRAME_HEIGHT)))
videoWriter = cv2.VideoWriter('MyOutputVid.avi', cv2.VideoWriter_fourcc('I','4','2','0'), fps, size)
success, frame = videoCapture.read()
while success: # Loop until there are no more frames.
    videoWriter.write(frame)
    success, frame = videoCapture.read()
```

```python 
#captures 10 seconds of video from a camera and writes it to an AVI file
import cv2
cameraCapture = cv2.VideoCapture(0)
fps = 30 # an assumption
size = (int(cameraCapture.get(cv2.CAP_PROP_FRAME_WIDTH)),int(cameraCapture.get(cv2.CAP_PROP_FRAME_HEIGHT)))
videoWriter = cv2.VideoWriter('MyOutputVid.avi', cv2.VideoWriter_fourcc('I','4','2','0'), fps, size)
success, frame = cameraCapture.read()
numFramesRemaining = 10 * fps - 1
while success and numFramesRemaining > 0:
    videoWriter.write(frame)
    success, frame = cameraCapture.read()
    numFramesRemaining -= 1
cameraCapture.release()
```

### 2.5 Displaying images in a window

* `imshow()` 
	* 1st 인자 : the name of the frame
	* 2nd 인자 : image itself

>  `imshow()`는 비디어 프레임을 실시간으로 Refresh하기 위해 개발된 것이라 이미지 출력시 바로 사라짐 
> `waitKey()`와 `destroyAllWindows()` 와 조합 하여 사용 하여야 함

### 2.6 Displaying camera frames in a window
```python 
#show the frames of a live camera input
import cv2
clicked = False
def onMouse(event, x, y, flags, param):
    global clicked
    if event == cv2.EVENT_LBUTTONUP:
   	 clicked = True
c
ameraCapture = cv2.VideoCapture(0)
cv2.namedWindow('MyWindow')
cv2.setMouseCallback('MyWindow', onMouse)

print 'Showing camera feed. Click window or press any key to stop.'
success, frame = cameraCapture.read()
while success and cv2.waitKey(1) == -1 and not clicked:
    cv2.imshow('MyWindow', frame)
    success, frame = cameraCapture.read()

cv2.destroyWindow('MyWindow')
cameraCapture.release()
```

### [실습] Cameo 프로젝트
본 교재에서 배운 내용을 실습 하기 위하여 Cameo라는 프로그램을 만들 것이다. 이 프로그램은 아래와 같은 기능들을 가지고 있다. 

* real-time facial merging. 
* Given two streams of camera input 
* the application will superimpose faces from one stream onto faces in the other. 
* Filters and distortions will be applied to give this blended scene a unified look and feel.





