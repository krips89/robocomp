---
layout: post
title: GSoc-Idea: Computer vision components and libraries management
categories: [GSoC15]
tags: [kripasindhu_sarkar]
description: This blog introduces the author, describes the project idea and discusses the approachs taken to work towards the project idea
---
#GSoC: Computer vision components and libraries management

##About me

Hello, I am Kripasindhu Sarkar, a new PhD student at German Research Center for Artificial Intelligence (DFKI), Kaiserslautern working in the topic of Object Detection in simple and depth images. 
I am extremely interested in the topic of object detection and computer vision; specificaly by using theories from human cognition and perception to simulate human way of visualizing the problem. 
But for now, I am focused on getting a very good grasp at the exsisting engineering (mostly) techniques in the field of computer vision and object detection. 
Before joining here as a PhD student I worked as a Software Engineering at Paypal for 2 years and, prior to that I did my masters and graduation from Indian Institute of Technology Kharagpur (IIT Kharagpur).


###Computer vision components and libraries management

The project is about designing and implementing a system for object detection and recognition in 3D point clouds and 2D images, and come up with a structured library with a good and easy-to-use APIs.
There has been a good amount of research in this direction and my work was to cherrypick important ideas and present them as usable components. I'll now explain in details the various methods I chose to
use as a part of this project.

##Local feature based on 2D images: 
The idea is the find local features (like SIFT/SURF/ORB etc) in images of the object to be detected and the given test image. If enough matches are found between the descriptors of the to images an object is defined to be found. Important assumption is that the object to be detected must have textures. Advantage is that we get the complete 6 DOF of the object which might be useful for grasping. This comes in several flavors. 
1. Planner objects: If we know the object is planner, we can directly compute its tomography (pose) after the match.
2. Random objects: If the object is of arbitrary shape it is quite difficult to detect an object with its pose but can be done in a tricky offline phase [1]. A 3D reconstruction is performed through bundle adjustments with the object to be detected to find the 2D - 3D correspondences. On the run time, given an input image, If enough matches are found, the object is detected with its full pose by solving PnP problem. 

##Dense feature based on 2D images:
The idea is the find features over a grid or a region of an image encoding the properties of that region and use that feature in some classification algorithm to perform detection. Naturally, we need to calculate dense feature over all possible region size over the image and apply the classifier; and thus it is bit slow as well. Also object pose is not identified in this type. Few of them are:
3. HOG based simple classification (well known).
Difficulty in implementation: Moderate; HOG implementation with multiscale detector is present in OpenCV; but the training has to be performed separately using 3rd party tool like libsvm/matlab etc (but is straightfwd).
4. HOG based Part Based Model: This is the famous and legendary and state of art (not anymore) object detector which uses LSVM.
Difficulty in implementation: Difficult; OpenCV has the detection code, but not that good. Training LSVM is not straight fwd and we need to use the original Matlab implementation of the authors. 
5. Wevlet based face detector with adaboost: This is also welknown face detector algorithm used widely.
Difficulty in implementation: Easy; though the concept is not that straight fwd, it is readily avilable in OpenCV.

##Detection/Recognition on Depth Images
If we can get the Point Cloud with some laser scan or Kinect, there are plenty of algorithms to detect object with its pose. Again we have local feature based and global feature based algorithms described below:
6. Direct object with local and global features [4]:
Very similar to that of RGB image based algorithm with difference in the types of features. Local features have the advantage that preprocessing steps like segmentation is not required but tends to be slow. On the other hand we need to do segmentation to apply global features in the clusters. But once the segmentation (like identifying planes, etc and different clusters) of the scene is done, we can use the results subsequently. 
Difficulty in implementation: Easy; components of pipeline is available in PCL.
7. Object matching using classifiers: 
Global features readily available in PCL and found it to have similar results to a current benchmark but faster (10 seconds for classification testing in the benchmark [2] which uses sliding window based classification on all scales using HOG like descriptors). 
Difficulty in implementation: Moderate; It can be easy, but I have not executed something on the similar line in PCL; would try love to though. 
There might be other recent depth based object detection which I don't have much knowledge (and not available in PCL). Some very recent image only algorithms are available, which I will not recommend (a single implementation will take 2 - 3 months/or I have less knowledge about them).


----------
[1] Master Thesis, Universidad de Extremadura, Escuela Politécnica de Cáceres. Mercedes Paoletti Ávila. 'Cinemática Inversa en Robots Sociales'. Directed by Pablo Bustos and Luis Vicente Calderita. July 2014. Download in https://robolab.unex.es/index.php?option=com_remository&Itemid=53&func=startdown&id=143
[2] inverse kinematics component repository: https://github.com/robocomp/robocomp-ursus/tree/master/components/inversekinematics
[3] C. Suárez Mejías, C. Echevarría, P. Núñez, L. Manso, P. Bustos, S. Leal and C. Parra. 'Ursus: A Robotic Assistant for Training of Patients with Motor Impairments'. Book, Converging Clinical and Engineering Research on Neurorehabilitation, Springer series on BioSystems and BioRobotics, Editors, J.L Pons, D. Torricelli and Marta Pajaro. Springer, ISBN 978-3-642-34545-6, pages 249-254. January 2012. Download in https://robolab.unex.es/index.php?option=com_remository&Itemid=53&func=startdown&id=128
[4] Lourakis, M. I., Argyros, A. (2009). SBA: A Software Package for Generic Sparse Bundle Adjustment. Article of ACM Transactions on Mathematical Software, volume 36, issue 1, pages 1-30. Download in http://doi.acm.org/10.1145/1486527







