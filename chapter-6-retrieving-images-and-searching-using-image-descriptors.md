## Chapter 6. Retrieving Images and Searching Using Image Descriptors

OpenCV can detect the main features of an image and extract these into so-called image __descriptors__. 

These features can then be used as a database, enabling image-based searches. 

Moreover, we can use __keypoints__ to stitch images together and compose a bigger image


### 6.1 Feature detection algorithms
Algorithms that can be used to detect and extract features
* Harris: This algorithm is useful to detect corners
* SIFT: This algorithm is useful to detect blobs
* SURF: This algorithm is useful to detect blobs
* FAST: This algorithm is useful to detect corners
* BRIEF: This algorithm is useful to detect blobs
* ORB: This algorithm stands for Oriented __FAST__ and Rotated **BRIEF**

Matching features can be performed with the following methods:
* Brute-Force matching
* FLANN-based matching

Spatial verification can then be performed with __homography__.


> 자세한 내용은 추후 Update 


