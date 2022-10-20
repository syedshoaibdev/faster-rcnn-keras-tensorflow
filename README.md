# Faster R-CNN for Open Images Dataset by Keras
## Introduction
The original code of Keras version of Faster R-CNN I used was written by yhenon (resource link: GitHub .) He used the PASCAL VOC 2007, 2012, and MS COCO datasets. For me, I just extracted three classes, “Person”, “Car” and “Mobile phone”, from Google’s Open Images Dataset V4. I applied configs different from his work to fit my dataset and I removed unuseful code. Btw, to run this on Google Colab (for free GPU computing up to 12hrs), I compressed all the code into three .ipynb notebooks. Sorry for the messy structure.

I wrote my exploring and experiment results for Faster R-CNN in this [article](https://medium.com/@rockyxu399/faster-r-cnn-object-detection-implemented-by-keras-for-custom-data-from-googles-open-images-125f62b9141a) in Medium. If you are in China, you cannot directly access [Medium](https://medium.com). So I make a copy in [here](https://zhuanlan.zhihu.com/p/50360414).

## Project Structure
`Object_Detection_DataPreprocessing.ipynb` is the file to extract subdata from Open Images Dataset V4 which includes downloading the images and creating the annotation files for our training. I run this part by my own computer because of no need for GPU computation. `frcnn_train_vgg.ipynb` is the file to train the model. The configuration and model saved path are inside this file. `frcnn_test_vgg.ipynb` is the file to test the model with test images and calculate the mAP (mean average precision) for the model. If you want to run the code on Colab, you need to give authority to Colab for connecting your Google Drive. Then, you need to upload your annotation file  and training images to the Google Drive and change my path to your right path in the notebook.

## Result for some test images
<p float="left">
    <img src="Assets/e2f4a864682b4645.jpg" width="425"/> 
    <img src="Assets/28cc7decbcc56aa1.jpg" width="425"/>
</p>
<p>
    <img src="Assets/96b74d5aaadc2259.jpg" width="425"/> 
    <img src="Assets/c3ca8496d6a9f2de.jpg" width="425"/> 
</p>

## Have fun

As the images shown below, they are three porcoelainous monks made by China. I just named them according to their face look (not sure about the sleepy one). They are definitely not included in the Open Images Dataset V4. Except for the three classes I used in Open Images Dataset V4, I create my own six-classes dataset ('Apple Pen', 'Lipbalm', 'Scissor', 'Sleepy Monk', 'Upset Monk' and 'Happy Monk') for fun and train another detector to find out these objects. If you want to know how to do this, just see this [article](https://medium.com/@rockyxu399/faster-r-cnn-object-detection-implemented-by-keras-for-custom-data-from-googles-open-images-125f62b9141a) for more details.
<p>
<img src="Assets/Result_customdata3.png" width="425"/>  
<img src="Assets/Result_customdata2.jpg" width="425"/> 
</p>
<img src="Assets/Result_customdata1.jpg" width="425"/>
The most widely used state of the art version of the R-CNN family — Faster R-CNN was first published in 2015. This article, the third and final one of a series to understand the fundamentals of current day object detection elaborates the technical details of the Faster R-CNN detection pipeline. For a review of its predecessors, check out these summaries: Regions with CNN (R-CNN) and Fast R-CNN.

In the R-CNN family of papers, the evolution between versions was usually in terms of computational efficiency (integrating the different training stages), reduction in test time, and improvement in performance (mAP). These networks usually consist of — a) A region proposal algorithm to generate “bounding boxes” or locations of possible objects in the image; b) A feature generation stage to obtain features of these objects, usually using a CNN; c) A classification layer to predict which class this object belongs to; and d) A regression layer to make the coordinates of the object bounding box more precise.

The only stand-alone portion of the network left in Fast R-CNN was the region proposal algorithm. Both R-CNN and Fast R-CNN use CPU based region proposal algorithms, Eg- the Selective search algorithm which takes around 2 seconds per image and runs on CPU computation. The Faster R-CNN [3] paper fixes this by using another convolutional network (the RPN) to generate the region proposals. This not only brings down the region proposal time from 2s to 10ms per image but also allows the region proposal stage to share layers with the following detection stages, causing an overall improvement in feature representation. In the rest of the article, “Faster R-CNN” usually refers to a detection pipeline that uses the RPN as a region proposal algorithm, and Fast R-CNN as a detector network.

Region Proposal Network (RPN)

Figure 1: The architecture of the region proposal network or RPN
Architecture
The region proposal network (RPN) starts with the input image being fed into the backbone convolutional neural network. The input image is first resized such that it’s shortest side is 600px with the longer side not exceeding 1000px.
The output features of the backbone network (indicated by H x W) are usually much smaller than the input image depending on the stride of the backbone network. For both the possible backbone networks used in the paper (VGG, ZF-Net) the network stride is 16. This means that two consecutive pixels in the backbone output features correspond to two points 16 pixels apart in the input image.
For every point in the output feature map, the network has to learn whether an object is present in the input image at its corresponding location and estimate its size. This is done by placing a set of “Anchors” on the input image for each location on the output feature map from the backbone network. These anchors indicate possible objects in various sizes and aspect ratios at this location. The figure below shows 9 possible anchors in 3 different aspect ratios and 3 different sizes placed on the input image for a point A on the output feature map. For the PASCAL challenge, the anchors used have 3 scales of box area 128², 256², 512² and 3 aspect ratios of 1:1, 1:2 and 2:1.
