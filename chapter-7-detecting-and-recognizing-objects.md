## Chapter 7. Detecting and Recognizing Objects

용어 정리 : Detecting Vs. Recognizing
* Detecting an object is the ability of a program to determine if a certainregion of an image contains an unidentified object
* Recognizing is the ability of aprogram to identify this object. Recognizing normally only occurs in areas of interestwhere an object has been detected, 
	* for example, we have attempted to recognize faces onthe areas of an image that contained a face in the first place.

recognizing and detecting techniques 
1. Histogram of Oriented Gradients(HOG)
2. Image pyramids
3. Sliding windows

Unlike feature detection algorithms, these are not mutually exclusive techniques, rather,they are complimentary.

You can perform a __HOG__ while applying the __sliding windows__ technique.

### 7.1 HOG descriptors

Feature descriptor : HOG, SIFT, SURF, ORB

목적 : Detect objects.

동작 메커니즘 
1. an image is divided into portions and a gradient for each portion is calculated.
2. calculates histograms based on gradients.


![224]()

This is its HOG version:

![224]()


### what is HOG seeing? 

* the image is divided into cells(16x16 pixels)
* Each cell contains a visual representation of the calculated gradients of color in eight directions (N, NW, W, SW, S, SE, E, and NE).
* These eight values contained in each cell are the famous histograms.
* Therefore, a single cell gets a unique signature, which you can mentally visualize to be somewhat like this: 

![225]()

###### The extrapolation(추론) of histograms into descriptors is quite a complex process.

* First, local histograms for each cell are calculated.
* The cells are grouped into larger regions called blocks.
* These blocks can be made of any number of cells(people Detection시 2x2 cell blocks 추천)
* A block-wide vector is created so that it can be normalized, accounting for variations in illumination and shadowing (a single cell is too small a region to detect such variations).
* This improves the accuracy of detection as it reduces the illumination and shadowing difference between thesample and the block being examined.

Simply comparing cells in two images would not work unless the images are identical(both in terms of size and data).

###### 해결해야 하는 문제점 1: The scale issue
* Imagine, for example, if your sample was a detail (say, a bike) extrapolated from a larger image, and you’re trying to compare the two pictures.
* You would not obtain the same gradient signatures and the detection would fail (even though the bike is in both pictures).

###### 해결해야 하는 문제점 2: The location issue
* a potentially detectable object can be anywhere in the image, so we need to scan the entire image in portions to make sure we can identify areas of interest, and within these areas, try to detect objects.
* Even if a sample image and object in the image are of identical size,there needs to be a way to instruct OpenCV to locate this object.
* So, the rest of the image is discarded and a comparison is made on potentially matching regions.

> 문제 해결을 위해 __Pyramid__ & __Sliding windows__ 개념 이해 필요 

### Image pyramid
An image pyramid is a multiscale representation of an image.
![p226]()
 
A multiscale representation of an image(=image pyramid) resolve __different scales__ problem

여러 크기의 이미지들을 모두 만들어서 대상에 맞는 크기가 있도록 하는것

###### Image pyramid 생성 하는 절차 

1. Take an image.
2. Resize (smaller) the image using an arbitrary scale parameter.
3. Smoothen the image (using Gaussian blurring).
4. If the image is larger than an arbitrary minimum size, repeat the process from step 1.

> CascadeClassifier object를 이용한 Detecting and Recognizing Faces도 이를 응용한것임
> `detectMultiScale()` 인자 중 scaleFactor parameter가 Re-sample시 ratio를 나타내며 이 값이 작을수록 레이어도 늘어나고, 속도는 느려 진다. 

### Sliding windows

> 특징 매트릭스(윈도우)를 전체 이미지를 훝어(스캐닝)가면서 검토 하는것 

![](https://i.stack.imgur.com/Bvs2W.png)

* 동작 원리 : scanning smaller regions of a larger image,and then repeating the scanning on different scales of the same image.
* 기술 구성 : Sliding windows consists of examining the shifting portions of an image (sliding windows) + operating detection on those using image pyramids.

* 효과 : This is done so that an object can be detected at a multiscale level.

* With this technique, each image is decomposed into portions, which allows discarding portions that are unlikely to contain objects, while the remaining portions are classified.

* 문제점 : overlapping regions.
	* 예 : you’re operating face detection on an image and are using sliding windows.
	* Each window slides off a few pixels at a time, which means that a sliding window happensto be a positive match for the same face in four different positions.
	* Naturally, we don’t want to report four matches, rather only one; furthermore, we’re not interested in the portion of the image with a good score, but simply in the portion with the highest score.

Here’s where __non-maximum suppression__ comes into play: given a set of overlapping regions, we can suppress all the regions that are not classified with the maximum score.

### Non-maximum (or non-maxima) suppression

> 윈도우 겹치는 부분의 차이로 동일한 오브젝트를 여러개로 인식 하는 문제 해결 (중복 counting방지)

![](https://openi.nlm.nih.gov/imgs/512/296/3191454/PMC3191454_boe-2-10-2905-g006.png)




Non-maximum (or non-maxima) suppression is a technique that suppresses all the results that relate to the same area of an image, which are not the maximum score for a particulararea.

This is because similarly colocated windows tend to have higher scores and overlapping areas are significant, but we are only interested in the window with the bestresult, and discarding overlapping windows with lower scores.

When examining an image with sliding windows, you want to make sure to retain the bestwindow of a bunch of windows, all overlapping around the same subject.

To do this, you determine that all the windows with more than a threshold, `x`, in common will be thrown into the non-maximum suppression operation.

This is quite complex, but it’s also not the end of this process.

Remember the image pyramid? We’re scanning the image at smaller scales iteratively to make sure to detectobjects in different scales.

This means that you will obtain a series of windows at different scales, then, compute thesize of a window obtained in a smaller scale as if it were detected in the original scale,and, finally, throw this window into the original mix.


> 구현 코드 :  [Dr. Tomasz Malisiewicz](http://www.computervisionblog.com/2011/08/blazing-fast-nmsm-from-exemplarsvm.html)



###### The general approach behind non-maximum suppression is as follows:
1. Once an image pyramid has been constructed, scan the image with the sliding window approach for object detection.
2. Collect all the current windows that have returned a positive result (beyond a certain arbitrary threshold), and take a window, W, with the highest response.
3. Eliminate all windows that overlap W significantly.
4. Move on to the next window with the highest response and repeat the process for the current scale.

When this process is complete, move up the next scale in the image pyramid and repeatthe preceding process.

To make sure windows are correctly represented at the end of the entire non-maximum suppression process, be sure to compute the window size in relationto the original size of the image 

* e.g : if you detect a window at 50 percent scaleof the original size in the pyramid, the detected window will actually be four times the sizein the original image

At the end of this process, you will have a set of maximum scored windows.

Optionally,you can check for windows that are entirely contained in other windows (like we did forthe people detection process at the beginning of the chapter) and eliminate those.

Now, how do we determine the score of a window? We need a classification system thatdetermines whether a certain feature is present or not and a confidence score for thisclassification.

This is where support vector machines (SVM) comes into play.





