## YOLO Object detection with OpenCV

DEMO:  [![Nbviewer](https://github.com/jupyter/design/blob/main/logos/Badges/nbviewer_badge.svg)](https://nbviewer.jupyter.org/github/shejz/Object-Detection/blob/main/YOLO%20Object%20Detection/yolo_object_detection.ipynb)

- Apply the YOLO object detector to images
- Apply YOLO to video streams


I’ll be using YOLOv3 in this project, in particular, pre-trained (on the COCO dataset) model files. 

**The COCO dataset consists of 80 labels, including, but not limited to**:

- People
- Bicycles
- Cars and trucks
- Airplanes
- Stop signs and fire hydrants
- Animals, including cats, dogs, birds, horses, cows, and sheep, to name a few
- Kitchen and dining objects, such as wine glasses, cups, forks, knives, spoons, etc.

You can find a full list of what YOLO trained on the COCO dataset can detect using this [link](https://github.com/pjreddie/darknet/blob/master/data/coco.names).

When it comes to deep learning-based object detection, there are three primary object detectors you’ll encounter:

1. R-CNN and their variants, including the original R-CNN, Fast R- CNN, and Faster R-CNN
2. Single Shot Detector (SSDs)
3. You Only Look Once (YOLO)

#### **R-CNNs** 

Two-stage detector:
1. In the first R-CNN publication, Rich feature hierarchies for accurate object detection and semantic segmentation, (2013) Girshick et al. proposed an object detector that required an algorithm such as **Selective Search** (or equivalent) to propose candidate bounding boxes that could contain objects. These regions were then passed into a CNN for classification, ultimately leading to one of the first deep learning-based object detectors.
2. These regions were then passed into a CNN for classification, ultimately leading to one of the first deep learning-based object detectors.

"The problem with the standard R-CNN method was that it was painfully **slow** and not a complete end-to-end object detector".

#### **Fast R-CNN**

The Fast R-CNN algorithm made considerable improvements to the original R-CNN, namely increasing accuracy and reducing the time it took to perform a forward pass; however, the model still relied on an external region proposal algorithm.

While R-CNNs tend to very accurate, the biggest problem with the R-CNN family of networks is their speed — they were incredibly slow, obtaining only 5 FPS on a GPU.

To help increase the speed of deep learning-based object detectors, both Single Shot Detectors (SSDs) and YOLO use a one-stage detector strategy. These algorithms treat object detection as a regression problem, taking a given input image and simultaneously learning bounding box coordinates and corresponding class label probabilities.

**In general, **single-stage detectors** tend to be less accurate than **two-stage detectors** but are significantly faster. YOLO is a great example of a single stage detector.**

### YOLO object detection in images
YOLO does not apply **non-maxima suppression** for us, so we need to explicitly apply it. Applying non-maxima suppression suppresses significantly overlapping bounding boxes, keeping only the most confident ones.


### Limitations and drawbacks of the YOLO object detector
Arguably the largest limitation and drawback of the YOLO object detector is that:

- It does not always handle small objects well
- It especially does not handle objects grouped close together

**The reason for this limitation is due to the YOLO algorithm itself:**

- The YOLO object detector divides an input image into an SxS grid where each cell in the grid predicts only a single object.
- If there exist multiple, small objects in a single cell then YOLO will be unable to detect them, ultimately leading to missed object detections.

Therefore, if you know your dataset consists of many small objects grouped close together then you should not use the YOLO object detector.

- In terms of small objects, Faster R-CNN tends to work the best; however, it’s also the slowest.
- SSDs can also be used here; however, SSDs can also struggle with smaller objects (but not as much as YOLO). SSDs often give a nice tradeoff in terms of speed and accuracy as well.

**If you’re using the pre-trained deep learning object detectors OpenCV supplies you may want to consider using **SSDs over YOLO**. 
1. I have found SSDs much easier to train and their performance in terms of accuracy almost always outperforms YOLO (at least for the datasets I’ve worked with).
2. YOLO may have excellent results on the COCO dataset; however, I have not found that same level of accuracy for my own tasks.


#### Use the following guidelines when picking an object detector for a given problem:
1. If I know I need to detect small objects and speed is not a concern, I tend to use Faster R-CNN.
2. If speed is absolutely paramount, I use YOLO.
3. If I need a middle ground, I tend to go with SSDs.

**NOTE**: In most of my situations I end up using SSDs or RetinaNet — both are a great balance between the YOLO/Faster R-CNN.

### Sample output

![](https://github.com/shejz/Object-Detection/blob/main/YOLO%20Object%20Detection/output/car_crash.gif)
![](https://github.com/shejz/Object-Detection/blob/main/YOLO%20Object%20Detection/output/airport.gif)

