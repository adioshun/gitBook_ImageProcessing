## Chapter 5. Detecting and Recognizing Faces

### 5.1 Conceptualizing Haar cascades 
image detail tends to be unstable with respect to variations in lighting, viewing angle, viewing distance, camera shake, and digital noise. (이미지는 환경 요인으로 불안정할수 있다.)
 
Moreover, even real differences in physical detail might not interest us for the purpose of classification. (이미지의 Physical detail이 분류 작업에서 꼭 맞는것은 아니다. )
 Thus, some means of abstracting image detail is useful in producing stable classification and tracking results. (따라서,abstracting image detail작업이 분류나 트랙킹 하는데 중요 하다.  )
 
 The abstractions are called features,
 
 Haar-like features[^1] are one type of feature that is often applied to __real-time face tracking__. 
 
 * Each Haar-like feature describes the pattern of contrast among adjacent image regions. 
 	* For example, edges, vertices, and thin lines each generate distinctive features.
 * For any given image, the features may vary depending on the region’s size; this may be called the window size. 
 * Two images that differ only in scale should be capable of yielding similar features, albeit for different window sizes.
 	* Thus, it is useful to generate features for multiple window sizes. Such a collection of features is called a cascade. 
 
 We may say a Haar cascade is _scale-invariant_ or, robust to changes in scale. 
 
Haar cascades are __not robust__ to changes in rotation. 

> If you are curious about how these data sets are generated, refer to Appendix B, Generating Haar Cascades for Custom Targets, OpenCV Computer Vision with Python.


#### Performing face detection on a still image

```python 
import cv2
filename = '/path/to/my/pic.jpg'
def detect(filename):
    face_cascade = cv2.CascadeClassifier('./cascades/haarcascade_frontalface_default.xml')
    img = cv2.imread(filename)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, 1.3, 5)
    for (x,y,w,h) in faces:
    img = cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    cv2.namedWindow('Vikings Detected!!')
    cv2.imshow('Vikings Detected!!', img)
    cv2.imwrite('./vikings.jpg', img)
    cv2.waitKey(0)
# 코드 설명은 P176참고 
```

#### Performing face detection on a video

#### Performing face recognition

	Face databases on the Internet:
    * The Yale face database (Yalefaces): http://vision.ucsd.edu/content/yale-facedatabase
    * The AT&T: http://www.cl.cam.ac.uk/research/dtg/attarchive/facedatabase.html
    * The Extended Yale or Yale B:     http://www.cl.cam.ac.uk/research/dtg/attarchive/facedatabase.html

###### 1. Generating the data for face recognition
샘플 이미지 요구 사항

* Images will be grayscale in the .pgm format
* Square shape
* All the same size images (I used 200 x 200; most freely available sets are smaller than that)

```python
def generate():
    face_cascade = cv2.CascadeClassifier('./cascades/haarcascade_frontalface_default.xml')
    eye_cascade = cv2.CascadeClassifier('./cascades/haarcascade_eye.xml')
    camera = cv2.VideoCapture(0)
    count = 0
    while (True):
        ret, frame = camera.read()
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        faces = face_cascade.detectMultiScale(gray, 1.3, 5)
        for (x,y,w,h) in faces:
            img = cv2.rectangle(frame,(x,y),(x+w,y+h),(255,0,0),2)
            f = cv2.resize(gray[y:y+h, x:x+w], (200, 200))
            cv2.imwrite('./data/at/jm/%s.pgm' % str(count), f)
            count += 1
        cv2.imshow("camera", frame)
        if cv2.waitKey(1000 / 12) & 0xff == ord("q"):
        break
    camera.release()
    cv2.destroyAllWindows()

if __name__ == "__main__":
	generate()
```
1. cropping that region of the gray-scaled frame, resizing it to be 200x200 pixels, and saving it with a name in a particular folder (in my case, jm; you can use your initials) in the .pgm format.
2. I inserted a variable, count, because we needed progressive names for the images. 
3. Run the script for a few seconds, change expressions a few times, and check the destination folder you specified in the script. 
4. You will find a number of images of your face, grayed, resized, and named with the format, <count>.pgm.

###### 2. Recognizing faces

OpenCV 3 comes with three main methods for recognizing faces
* Eigenfaces
* Fisherfaces
* Local Binary Pattern Histograms (LBPH).

* Principal Component Analysis (PCA): A very intuitive introduction by JonathonShlens[^2]. This algorithm wasinvented in 1901 by K. Pearson, and the original paper, _On Lines and Planes ofClosest Fit to Systems of Points in Space_[^3].
* Eigenfaces: The paper, _Eigenfaces for Recognition_, M. Turk and A. Pentland, 1991[^4], 
	* Eigenfaces performs a so called PCA. It basically identifies principal components of a certain set of observations (again, your facedatabase), calculates the divergence of the current observation (the faces being detected inan image or frame) compared to the dataset, and it produces a value. The smaller the value, the smaller the difference between face database and detected face; hence, a valueof 0 is an exact match.

* Fishfaces : The seminal paper, _THE USE OF MULTIPLE MEASUREMENTS INTAXONOMIC PROBLEMS_, R.A. Fisher, 1936[^5].
	* Fisherfaces derives from PCA and evolves the concept, applying more complex logic.While computationally more intensive, it tends to yield more accurate results thanEigenfaces.

* Local Binary Pattern: The first paper describing this algorithm is _Performanceevaluation of texture measures with classification based on Kullback discriminationof distributions_[^6], T. Ojala, M. Pietikainen, D. Harwood 
	* LBPH instead roughly (again, from a very high level) divides a detected face into smallcells and compares each cell to the corresponding cell in the model, producing a histogramof matching values for each area. Because of this flexible approach, LBPH is the only facerecognition algorithm that allows the model sample faces and the detected faces to be ofdifferent shape and size. (저자 추천)

3방식의 공통된 처리 과정 = all methods follow a similar process; they all take a set of classifiedobservations (our face database, containing numerous samples per individual), get“trained” on it, perform an analysis of faces detected in an image or video, and determinetwo elements: whether the subject is identified, and a measure of the confidence of thesubject really being identified, which is commonly known as the confidence score.

###### 3. Preparing the training data
All face recognition algorithms take two parameters in their train() method
* 1st 인자 : an array of images 
* 2nd 인자 : array of labels. (각 사람들 ID)


###### 4. Loading the data and recognizing faces
we need to load these two resources (the array of images and CSV file) into the face recognition algorithm, so it can be trained to recognize our face. 

```python 
Code 184p
```

###### 5-1. Performing an Eigenfaces recognition
We’re ready to test the face recognition algorithm.

```python 
Code 185p
```

1. First of all,there’s an array of names declared; those are the actual names of the individual people Istored in my database of faces.
2. It’s great to identify a person as ID 0, but printing 'Joe' ontop of a face that’s been correctly detected and recognized is much more dramatic.
3. So whenever the script recognizes an ID, we will print the corresponding name in thenames array instead of an ID.
4. After this, we load the images as described in the previous function, create the facerecognition model with cv2.`createEigenFaceRecognizer()`, and train it by passing thetwo arrays of images and labels (IDs).
	* Note that the Eigenface recognizer takes twoimportant parameters that you can specify: the first one is the number of principalcomponents you want to keep and the second is a float value specifying a confidencethreshold.
5. Next up, we repeat a similar process to the face detection operation.
	* This time, though, weextend the processing of the frames by also operating face recognition on any face that’sbeen detected.
	* This happens in two steps: (1) we resize the detected face to the expected size (in mycase, samples were 200x200 pixels), 2) we call the `predict()` function on theresized region.

> 실 사진에 적용 하기 위해서는 일련의 최적화 작업이 필요 하다. : correctly aligning and rotating detected faces

###### 5-2 Performing face recognition with Fisherfaces

`model = cv2.face.createFisherFaceRecognizer()`

* Fisherface takes the same two arguments as Eigenfaces: (1) the Fisherfaces to keep, (2)the confidence threshold. 
* Faces with confidence above this threshold will be discarded.

###### Performing face recognition with LBPH

`model = cv2.face.createLBPHFaceRecognizer()`

* 기본 동작은 비슷
* 입력 인자가 많은 radius, neighbors, grid_x, grid_y, and the confidence threshold (Default :  1, 8, 8, 8, 123.0)

> Note that with LBPH, you won’t need to resize images, as the division in grids allows comparing patterns identified in each cell.

###### 6. Discarding results with confidence score
The predict() method returns a two-element array: 
* 1st 반환 : the label of the recognized individual 
* 2nd 반환 : the confidence score

All algorithms come with the option of setting a confidence score threshold, which measures the distance of therecognized face from the original model, therefore a score of `0` signifies an exact match.

There may be cases in which you would rather retain all recognitions, and then applyfurther processing, so you can come up with your own algorithms to estimate theconfidence score of a recognition; 
* for example, if you are trying to identify people in avideo, you may want to analyze the confidence score in subsequent frames to establishwhether the recognition was successful or not.

In this case, you can inspect the confidencescore obtained by the algorithm and draw your own conclusions.

> The confidence score value is completely different in Eigenfaces/Fisherfaces and LBPH.
>
> Eigenfaces and Fisherfaces will produce values (roughly) in the range 0 to 20,000, withany score below 4-5,000 being quite a confident recognition.
> 
> LBPH works similarly; however, the reference value for a good recognition is below 50,and any value above 80 is considered as a low confidence score.
> 
> A normal custom approach would be to hold-off drawing a rectangle around a recognizedface until we have a number of frames with a satisfying arbitrary confidence score, butyou have total freedom to use OpenCV’s face recognition module to tailor yourapplication to your needs.






---

[^1]: Robust Real-Time Face Detection, Paul Viola and Michael Jones, Kluwer Academic Publishers, 2001 (available at http://www.vision.caltech.edu/html-files/EE148-2005-Spring/pprs/viola04ijcv.pdf)
[^2]: http://arxiv.org/pdf/1404.1100v1.pdf
[^3]: http://stat.smmu.edu.cn/history/pearson1901.pdf
[^4]: http://www.cs.ucsb.edu/~mturk/Papers/jcn.pdf.Fisherfaces
[^5]: http://onlinelibrary.wiley.com/doi/10.1111/j.1469-1809.1936.tb02137.x/pdf
[^6]: http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=576366&searchWithin%5B%5D=%22Authors%22%3A.QT.Ojala%2C+T..QT.&newsearch=
