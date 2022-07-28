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

#Objective
Our objective for this project is to use the Mask R-CNN for object detection in long-tailed categories in
the LVIS dataset. Our motivation in using the Mask R-CNN lies in the improved performance it has over
the Faster R-CNN due to preservation of spatial features through improved pixel-to-pixel alignment.





