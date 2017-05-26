## Chapter 3. Processing Images with OpenCV 3

알려진 Color Spaces 

* Gray : A color space that effectively eliminates color information translating to shades of gray: this color space is extremely useful for intermediate processing, such as face detection
* BGR : The blue-green-red color space, in which each pixel is a three-element array, each value representing the blue, green, and red colors: web developers would be familiar with a similar definition of colors, except the order of colors is RGB
* HSV : hue is a color tone, saturation is the intensity of a color, and value represents its darkness (or brightness at the opposite end of the spectrum)


### 3.1 The Fourier Transform

* 기본 개념 : All waveforms are just the sum of simple sinusoids of different frequencies.In other words, the waveforms you observe all around you are the sum of other waveforms. 
* CV 개념 : This concept is incredibly useful when manipulating images, because it allows us to identify regions in images where a signal (such as image pixels) changes a lot, and regions where the change is less dramatic.
* The concept of _Fourier Transform_ is the basis of many algorithms used for common image processing operations, such as __edge detection__ or __line and shape detection__.

#### High pass filter
A high pass filter (HPF) is a filter that examines a region of an image and boosts the intensity of certain pixels based on the difference in the intensity with the surrounding pixels.

#### Low pass filter
If an HPF boosts the intensity of a pixel, given its difference with its neighbors, a low pass filter (LPF) will smoothen the pixel if the difference with the surrounding pixels is lower than a certain threshold. This is used in denoising and blurring.
eg., one of the most popular blurring/smoothening filters, __the Gaussian blur__, is a low pass filter that attenuates the intensity of high frequency signals.

> __Radius__ : Both high pass and low pass filters use a property called radius, which extends the area of the neighbors involved in the filter calculation.
> __Kernel__ : A kernel is a set of weights that are applied to a region in a source image to generate a single pixel in the destination image.
> * We can think of a kernel as a piece of frosted glass moving over the source image and letting through a diffused blend of the source’s light.
> * Kernel은 convolution matrix라고도 지칭 된다. 왜냐 하면, It mixes up or convolves the pixels in a region. Similarly.(kernel-based filter = convolution filter)

### 3.2 Edge detection

OpenCV는 여러 edge-finding filters들을 제공하고 있다. : `Laplacian()`, `Sobel()`, and `Scharr()`. 
하지만, 정확성을 높이기 위해서는 Noise제거 작업(blurring)이 필요 하다. 
OpenCV 제공 blurring filters : `blur()`, `medianBlur()`, and `GaussianBlur()`. 

edge-finding filters과 blurring filters모두 __Ksize__라는 인자값을 필요로 한다. 이는 represents the width and height (in pixels) of a filter’s kernel.
 
```python
def strokeEdges(src, dst, blurKsize = 7, edgeKsize = 5):
    if blurKsize >= 3:
        blurredSrc = cv2.medianBlur(src, blurKsize)
        graySrc = cv2.cvtColor(blurredSrc, cv2.COLOR_BGR2GRAY)
    else:
   		graySrc = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
    cv2.Laplacian(graySrc, cv2.CV_8U, graySrc, ksize = edgeKsize)
    normalizedInverseAlpha = (1.0 / 255) * (255 - graySrc)
    channels = cv2.split(src)
    for channel in channels:
    	channel[:] = channel * normalizedInverseAlpha, its values range from 0 to 1)
    cv2.merge(channels, dst)
```

> Canny edge detection : it’s a five-step  process that denoises the image with a Gaussian filter, calculates gradients, applies non maximum suppression (NMS) on edges, a double threshold on all the detected edges to eliminate false positives, and, lastly, analyzes all the edges and their connection to each other to keep the real edges and discard the weak ones. 
 


### 3.3 Custom kernels – getting convoluted
#### Kernel 생성 및 적용 
```python
kernel = numpy.array([[-1, -1, -1],
                      [-1, 9, -1],
                      [-1, -1, -1]])

#Here, the pixel of interest has a weight of 9 and its immediate neighbors each have a weight of -1. 
#For the pixel of interest, the output color will be nine times its input color minus the input colors of all eight adjacent pixels. 

cv2.filter2D(src, -1, kernel, dst)
```
The effect is that the image looks sharper as the contrast between the neighbors is increased.

> Kernel 
> >The weights sum up to 1 : This should be the case whenever we want to leave the image’s overall brightness __unchanged__. 
> >The weights sum up to 0 : we have an edge detection kernel that turns __edges white__ and __non-edges black__.
> Blur
> > the weights should sum up to 1 and should be positive throughout the neighborhood

### 3.4 Contour detection

* `findContours()` 사용
	* 1st 인자 : the input image
	* 2nd 인자 : hierarchy type
	* 3rd 인자 : the contour approximation method
	* 반환값(3개) : the modified image, contours, and their hierarchy
	* eg.  `image, contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)`

#### Contours – bounding box, minimum area rectangle, and minimum enclosing circle

The `cv2.findContours` function in conjunction with a few other OpenCV utilities makes this(determining the bounding box of
the subject, its minimum enclosing rectangle, and its circle.) very easy to accomplish
![](126)

```python 
import cv2
import numpy as np
#0. 사전 준비
img = cv2.pyrDown(cv2.imread("hammer.jpg", cv2.IMREAD_UNCHANGED))
ret, thresh = cv2.threshold(cv2.cvtColor(img.copy(), cv2.COLOR_BGR2GRAY) ,127, 255, cv2.THRESH_BINARY)

image, contours, hier = cv2.findContours(thresh, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
for c in contours:
	# 1. find bounding box coordinates
    x,y,w,h = cv2.boundingRect(c)
    cv2.rectangle(img, (x,y), (x+w, y+h), (0, 255, 0), 2)
    
    # 2. find minimum area
    rect = cv2.minAreaRect(c)
    # calculate coordinates of the minimum area rectangle
    box = cv2.boxPoints(rect)
    
    # normalize coordinates to integers
    box = np.int0(box)
    
    # 3. draw contours
    cv2.drawContours(img, [box], 0, (0,0, 255), 3)
    
    # 4. calculate center and radius of minimum enclosing circle
    (x,y),radius = cv2.minEnclosingCircle(c)
    
    # cast to integers
    center = (int(x),int(y))
    radius = int(radius)
    
    # draw the circle
    img = cv2.circle(img,center,radius,(0,255,0),2)

cv2.drawContours(img, contours, -1, (255, 0, 0), 1)
cv2.imshow("contours", img)
```
###### 0. 사전준비
After the initial imports, we load the image, and then apply a binary threshold on a grayscale version of the original image. 
By doing this, we operate all find-contour calculations on a grayscale copy, but we draw on the original so that we can utilize color information.

###### 1. Firstly, let’s calculate a simple bounding box: `x,y,w,h = cv2.boundingRect(c)`
	
* This is a pretty straightforward conversion of contour information to the (x, y) coordinates, plus the height and width of the rectangle. 
* Drawing this rectangle is an easy task and can be done using this code: `cv2.rectangle(img, (x,y), (x+w, y+h), (0, 255, 0), 2)`

###### 2. Secondly, let’s calculate the minimum area enclosing the subject: 
```python
rect = cv2.minAreaRect(c)
box = cv2.boxPoints(rect)
box = np.int0(box)
```
> The mechanism used here is particularly interesting: OpenCV does not have a function to calculate the coordinates of the minimum rectangle vertexes directly from the contour information. 
> Instead, we calculate the minimum rectangle area, and then calculate the vertexes of this rectangle. 
> Note that the calculated vertexes are floats, but pixels are accessed with integers (you can’t access a “portion” of a pixel), so we need to operate this conversion. 


###### 3. Next, we draw the box, which gives us the perfect opportunity to introduce the `cv2.drawContours` function: `cv2.drawContours(img, [box], 0, (0,0, 255), 3)`
1. Firstly, this function—like all drawing functions—modifies the original image. 
2. Secondly, it takes an array of contours in its second parameter, so you can draw a number of contours in a single operation. 
	* Therefore, if you have a single set of points representing a contour polygon, you need to wrap these points into an array, exactly like we did with our box in the preceding example. 
3. The third parameter of this function specifies the index of the contours array that we want to draw: a value of -1 will draw all contours; otherwise, a contour at the specified index in the contours array (the second parameter) will be drawn.
4. Most drawing functions take the color of the drawing and its thickness as the last two parameters.

###### 4. The last bounding contour we’re going to examine is the minimum enclosing circle: 
```python
(x,y),radius = cv2.minEnclosingCircle(c)
center = (int(x),int(y))
radius = int(radius)
img = cv2.circle(img,center,radius,(0,255,0),2)
```
The only peculiarity(특이점) of the `cv2.minEnclosingCircle` function is that it returns a twoelement tuple, of which the first element is a tuple itself, representing the coordinates of the circle’s center, and the second element is the radius of this circle. 

After converting all these values to integers, drawing the circle is quite a trivial operation.

The final result on the original image looks like this:
![](128)

#### Contours – convex contours and the Douglas-Peucker algorithm

Most of the time, when working with contours, subjects will have the most diverse shapes(다양한 모양), including convex ones(볼록형태) . The first facility(기능) that OpenCV offers to calculate the approximate bounding polygon of a shape is `cv2.approxPolyDP`. 

> 폴리곤 : 3차원 컴퓨터그래픽에서 입체형상을 표현할 때 사용하는 가장 작은 단위인 __다각형__을 가리킨다.

`cv2.approxPolyDP` function takes three parameters:
* 1st 인자 : A contour
* 2nd 인자 : epsilon value : `cv2.arcLength`	
* 3rd 인자 : A Boolean flag signifying that the polygon is closed

> An __epsilon__ is the maximum difference between the approximated polygon’s perimeter and the original contour’s perimeter. 
> The lower the value, the closer the approximated value will be to the original contour

		You may ask yourself why we need an approximate polygon when we have a contour that is already a precise representation. 
        The answer to this is that a polygon is a set of straight lines, and the importance of being able to define polygons in a region for further manipulation and processing is paramount in many computer vision tasks.


Now that we know what an epsilon is, we need to obtain contour perimeter information as a reference value. This is obtained with the `cv2.arcLength` function of OpenCV: 
```python
epsilon = 0.01 * cv2.arcLength(cnt, True)
approx = cv2.approxPolyDP(cnt, epsilon, True)
```

Effectively, we’re instructing OpenCV to calculate an approximated polygon whose perimeter can only differ from the original contour in an epsilon ratio.

OpenCV also offers a `cv2.convexHull` function to obtain processed contour information for convex shapes and this is a straightforward one-line expression: 
```python
hull = cv2.convexHull(cnt)
```

Let’s combine the original contour, approximated polygon contour, and the convex hull in one image to observe the difference between them. 
To simplify things, I’ve applied the contours to a black image so that the original subject is not visible but its contours are: 

### 3.5 Line and circle detection

Detecting edges and contours은 기본적이면서도 모든 동작들의 근본이 된다. Lines and shape detection역시 edge and contour detection와 긴밀한 관계가 있다. 

The theory behind lines and shape detection has its foundation in a technique called the __Hough transform__[^1] 

#### Line detection
`HoughLines` and `HoughLinesP`(=probabilistic) functions. 활용


The HoughLinesP(=probabilistic version) : only analyzes a subset of points and estimates the probability of these points all belonging to the same line. 
```python
 import cv2
 import numpy as np
 img = cv2.imread('lines.jpg')
 
 gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
 edges = cv2.Canny(gray,50,120)
 minLineLength = 20
 maxLineGap = 5
 lines = cv2.HoughLinesP(edges,1,np.pi/180,100,minLineLength,maxLineGap) 
 for x1,y1,x2,y2 in lines[0]:
 	cv2.line(img,(x1,y1),(x2,y2),(0,255,0),2)
 
 cv2.imshow("edges", edges)
 cv2.imshow("lines", img)
 cv2.waitKey()
 cv2.destroyAllWindows()
```
> HoughLines function takes a _single channel_ binary image, processed through the Canny edge detection filter. 

The parameters of `HoughLinesP` are as follows:
* 1st 인자 : The image we want to process.
* 2nd 인자 : The geometrical representations of the lines, rho and theta, which are usually 1 and np.pi/180.
* 3rd 인자 : The threshold, which represents the threshold below which a line is discarded. 
	* The Hough transform works with a system of bins and votes, with each bin representing a line, so any line with a minimum of the <threshold> votes is retained, the rest discarded.
* 4th 인자 :  MinLineLength, minimum line length (shorter lines will be discarded)
* 5th 인자 :  MaxLineGap, the maximum size of a gap in a line before the two segments start being considered as separate lines.

#### Circle detection
`HoughCircles` function활용 

> HoughLines와 유사, but where minLineLength and maxLineGap were the parameters to discard or retain lines, HoughCircles has a minimum distance between circles’ centers, minimum, and maximum radius of the circles.  

```python
import cv2
import numpy as np
planets = cv2.imread('planet_glow.jpg') 
gray_img = cv2.cvtColor(planets, cv2.COLOR_BGR2GRAY) 
img = cv2.medianBlur(gray_img, 5)

cimg = cv2.cvtColor(img,cv2.COLOR_GRAY2BGR) 
circles = cv2.HoughCircles(img,cv2.HOUGH_GRADIENT,1,120, param1=100,param2=30,minRadius=0,maxRadius=0) 

circles = np.uint16(np.around(circles)) for i in circles[0,:]:
 # draw the outer circle
 cv2.circle(planets,(i[0],i[1]),i[2],(0,255,0),2) 
 # draw the center of the circle
 cv2.circle(planets,(i[0],i[1]),2,(0,0,255),3) 

cv2.imwrite("planets_circles.jpg", planets) 
cv2.imshow("HoughCirlces", planets)
cv2.waitKey()
cv2.destroyAllWindows()
```

#### Detecting shapes
* The detection of shapes with the Hough transform is limited to circles; 

* however, we already implicitly explored detecting shapes of any kind, specifically when we talked about approxPolyDP. 

* This function allows the approximation of polygons, so if your image contains polygons, they will be quite accurately detected, combining the usage of `cv2.findContours` and `cv2.approxPolyDP`. (두 함수의 혼합 사용으로 활용 가능)
 

---
[^1]: invented by Richard Duda and Peter Hart, who extended (generalized) the work done by Paul Hough in the early 1960s.
