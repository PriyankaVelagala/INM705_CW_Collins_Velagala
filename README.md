# INM705: Object Detection using Mask-RCNN
Alex Collins and Priyanka Velagala 

For full report, see: [INM705-Written Report-Collins-Velagala.pdf](https://github.com/PriyankaVelagala/Object-Detection-for-Rare-Categories/blob/main/INM705-Written%20Report-Collins-Velagala.pdf)

# Dataset 
Our choice of dataset was motivated by one of the key problems discussed earlier - class imbalance. We
chose to work with the Large Vocabulary Instance Segmentation (LVIS) dataset which is designed to
encourage exploration of solutions to address this problem specifically. The dataset has ~2 million
high-quality instance segmentation masks for 1203 object categories over ~160k images(Gupta et al.,
2019).The dataset labels each category as frequent, common or rare based on the number of images with annotations
of a specific category. As the ground truths for the standard test set is not publicly available, we used a 9:1 
split on the official train set to obtain a train and validation set for training purposes and used their 
official validation set for testing purposes.

# Objective
In this work, we explore a challenging long-tailed object detection and instance segmentation task. We
evaluated model performance using mAP on object bounding boxes.

Our objective for this project is to use the Mask R-CNN for object detection in long-tailed categories in
the LVIS dataset. Our motivation in using the Mask R-CNN lies in the improved performance it has over
the Faster R-CNN due to preservation of spatial features through improved pixel-to-pixel alignment.

# Model Architecture of Mask R-CNN 
## Backbone with FPN 
The first component of the model is the backbone network, where the preprocessed images are passed
into the CNN (ResNet50) to extract key features from an image and generate feature maps. The early
layers of the network which have access to a high resolution image detect broad representations of
objects, as deeper layers of the network learn features that are semantically stronger(Lin et al., 2017).

## Region Proposal Network 
The main purpose of the Region Proposal Network is to generate regions of interest (ROI). A set number
of anchors are projected on an image where for each anchor, k boxes (“anchor boxes”) are generated
using varying scales and aspect ratios. In the case of Faster R-CNN, a common default is to use a total of
9 boxes generated for each position, with 3 scales and 3 aspect ratios (Ren et al., 2016). The purpose of
the RPN then remains that for each anchor, it predicts the objectness score associated with the anchor
(i.e. how likely it is to be an object vs. the background) and the offsets (x,y,w,h) of the region proposal
with respect to the position of the anchor.

![image](https://user-images.githubusercontent.com/26152595/181464766-97a92d73-518c-414f-9609-05a5b7ff0c80.png)


## Faster R-CNN 
The Faster R-CNN then uses the region of interest (ROI) proposals from the RPN and the feature maps
from the backbone network to extract a fixed length feature vector which is used as input for the
subsequent fully-connected layers. For this, as feature maps are much smaller in scale than region
proposals, the proposals need to be scaled down to the feature map level. This is performed through ROI
Align which in contrast to ROI Pooling has better performance as stride isn’t quantized which means
better alignment of ROI and extracted features and thus higher the quality of masks generated.

The output feature vector is then passed into the detection network composed of fully connected layers,
where the network is jointly trained on:
1. A regression problem to predict the bounding boxes of objects in the image
2. A classification problem to accurately predict the class associated with the bounding box.

![image](https://user-images.githubusercontent.com/26152595/181464850-047afd69-a33f-47e9-9dbd-dac5e6f99c70.png)

# Loss Functions 
The network is jointly trained using gradient descent. The loss is the sum of 5 distinct losses: 
![image](https://user-images.githubusercontent.com/26152595/181465230-a4d6258a-40bd-4f19-bc77-ebf6f5279fe7.png)

# Findings 
To make transfer from MS COCO pre-trained models, we selected classes that generally do not appear in
the MS COCO class list. Despite this we found that transfer of FPN weights pre-trained on the COCO
dataset was an effective way to improve performance.

Given the compute and time constraints, we trained on a very small subset of the data. Charts of train
and val loss demonstrate evidence of over-fitting. When we increased the train/val dataset size from
~500 to ~4500 images we saw the same evidence of overfitting on train data, but did see an increase in
performance at evaluation.

Presence of rare classes in the test set was very sparse. This made it hard to evaluate performance on
rare classes, so we increased rare classes from 2 to 5. This gave us a better estimate of performance and
demonstrated just how challenging detection is for rare classes.

Objects in the LVIS vocabulary can be very numerous and very small in images. We experimented with
using very small anchor boxes, with the smallest scale being 2. This was a failure. A limitation could be
that resolution of anchor points was not fine enough to allow the smallest anchor boxes to cover the
image: if there wasn’t overlap between where the small anchor boxes happened to be and where small
objects were, the model would lack training experience on positive object matches.

Our results show evidence that unbalanced data creates a significant challenge to effective inference on
rare classes, and our model is not effective at dealing with the problem. There are many potential
extensions. To name just a few:
- Upsampling of rare points, using image transforms such as flip, rotate and jitter.
- Another upsampling approach is data augmentation using Mosaic augmentation introduced by
Bochkovskiy et al, 2020. Training images are combined in such a way so that familiar objects are
seen in surprising contexts.
- When an object is detected, the sigmoid cross entropy loss for classification can lead to a
“negative” signal for categories that are not present. This leads to suppression of rare categories.
The loss function could be modified to avoid this such as in (Tang et. al, 2019).









