
## OpenCV People Counter

Building a “people counter” with OpenCV and Python. Using OpenCV, we’ll count the number of people who are heading “in” or “out” of a department store in real-time.


**Required Python libraries for people counting:**
- NumPy
- OpenCV
- dlib
- imutils

To implement our people counter we’ll be using both OpenCV and dlib. We’ll use OpenCV for standard computer vision/image processing functions, along with the deep learning object detector for people counting. We’ll then use **dlib** for its implementation of correlation filters. We could use OpenCV here as well; however, the dlib object tracking implementation was a bit easier to work with for this project.

###  Object Detection vs. Object Tracking

When we apply **object detection** we are determining where in an image/frame an object is. An object detector is also typically more computationally expensive, and therefore slower, than an object tracking algorithm.

**Examples of object detection algorithms include**:
- Haar cascades, 
- HOG + Linear SVM, 
- Deep learning-based object detectors such as **Faster R-CNNs**, **YOLO**, and **Single Shot Detectors (SSDs)**.

An **object tracker**, on the other hand, will accept the input (x, y)-coordinates of where an object is in an image and will:
- Assign a unique ID to that particular object
- Track the object as it moves around a video stream, predicting the new object location in the next frame based on various attributes of the frame (gradient, optical flow, etc.)

**Examples of object tracking algorithms include**:
- MedianFlow
- MOSSE
- GOTURN
- Kernalized correlation filters 
- Discriminative correlation filters

### Combining both Object Detection and Object Tracking

Highly accurate object trackers will combine the concept of object detection and object tracking into a single algorithm, typically divided into two phases:

#### **Phase 1 (Detecting)** 

During the detection phase we are running our computationally more expensive object tracker to:
1. Detect if new objects have entered our view. 
2. See if we can find objects that were “lost” during the tracking phase.  

For each detected object we create or update an object tracker with the new bounding box coordinates. Since our object detector is more computationally expensive we only run this phase once every N frames.
           
#### **Phase 2 (Tracking)**

When we are not in the “detecting” phase we are in the “tracking” phase. For each of our detected objects, we create an object tracker to track the object as it moves around the frame. Our object tracker should be faster and more efficient than the object detector. We’ll continue tracking until we’ve reached the N-th frame and then re-run our object detector. The entire process then repeats.

**The benefit of this hybrid approach is that we can apply highly accurate object detection methods without as much of the computational burden.**

### Centroid Tracking

Along with dlib’s object tracking implementation, we’ll also be using the centroid tracking. To build a simple object tracking via centroids script with Python:

**STEP 1**:  Is to accept **bounding box coordinates** and use them to **compute centroids**:

![]()

The bounding boxes themselves can be provided by either:
1. An object detector (such as HOG + Linear SVM, Faster R- CNN, SSDs, etc.)
2. Or an object tracker (such as correlation filters)

**STEP 2**: Compute the Euclidean distance between any new centroids (yellow) and existing centroids (purple):

![]()

Compute the Euclidean distance between each pair of original centroids (red) and new centroids (green). The centroid tracking algorithm makes the assumption that pairs of centroids with minimum Euclidean distance between them must be the same object ID.

In the example image above we have two existing centroids (purple) and three new centroids (yellow), implying that a new object has been detected (since there is one more new centroid vs. old centroid). The **arrows** then represent computing the Euclidean distances between all purple centroids and all yellow centroids.

**STEP 3**:  Associate object IDs

![]()

You can see that our centroid tracker has chosen to associate centroids that minimize their respective Euclidean distances. But what about the point in the bottom-left?

It didn’t get associated with anything. What do we do about the object in the bottom-left though?


**STEP 4**: Perform registering new objects:

Registering simply means that we are adding the new object to our list of tracked objects by:

1. Assigning it a new object ID.
2. Storing the centroid of the bounding box coordinates for the new object.

**STEP 5**: In the event that an object has been lost or has left the field of view, we can simply **deregister the object**. Exactly how you handle when an object is “lost” or is “no longer visible”. But for our **people counter**, we will deregister people IDs when they cannot be matched to any existing person objects for 40 consecutive frames. 
