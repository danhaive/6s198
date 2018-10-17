---
layout: post
title:  "Data Review"
date:   2018-10-17 23:27:01 -0400
categories: assignments
---

## 6.S198 Data Review
R. Danhaive & D. Pinochet

Our project is probably a little different than most other projects
 because we will rely mostly on pre-trained models to build our 
 sketch-to-shape system. As a result, we do not need to gather large
  amounts of data and store it remotely. Instead, the neural networks 
  (architecture and weights) are directly stored locally. 
  The first model is a Sketch-RNN [1] classifier, and the second one is a 3D GAN [2].
The envisioned system would work as follows: 
the user draws a sketch of an object in a web browser,
 Sketch-RNN classifies the sketch, and the returned class
  is used to sample a latent vector, which is fed to the 
  3D GAN to return a shape represented by a three-dimensional array (voxel-based representation).

### 1| Sketch-RNN Classification and QuickDraw! Data
As explained above, the user will sketch something on the browser, 
and this sketch will be the input to the neural network.
 More specifically, the sketch is represented by a sequence of points with the form (∆x,∆y,p<sub>1</sub>,p<sub>2</sub>,
 p<sub>3</sub>), 
 each representing a stroke action. The first two coordinates represent the offset of the current 
 point compared to the previous point (the first point drawn is defined as the origin). (p<sub>1</sub>,p<sub>2</sub>
 ,p<sub>3</sub>) is a 1-hot binary vector representing three possible states. The p<sub>1</sub> state 
 corresponds to pen down, meaning a line will be drawn between the current and next points. The p<sub>2</sub> state 
 corresponds to pen up, meaning no line will be drawn between the current and next points. The p<sub>3</sub> state
 indicates the drawing is finished, and all subsequent points (including the current one) will be ignored.
Our web application will record the user’s sketch following that format, 
feed the sequence to the Sketch-RNN classifier, which will predict a class.  
The QuickDraw! dataset (available here) contains 70k training sequences (and 
5k validation and test sequences) for each class (345 classes). 
The data for each class is about 20MB, and the amount of data to store 
locally will amount to less 10GB. Because we are only interested in those classes that
 have equivalents in the dataset of 3D shapes, the actual amount of data we will have
  to store locally will most likely be much more limited. 
For the classes of interest, we will use this data to build a Sketch-RNN classification model using TensorFlow for Python. We will then convert this model to TensorFlow.js. 
### 2| 3D GAN
The second part of our system is a 3D GAN, 
which was trained on CAD data from the ModelNet
 dataset. For our project, we will use a pre-trained 
 model available here. In particular, we will use the 
 generator network of this model, which takes a latent 
 vector of 200 coordinates as input and outputs a voxel-based 
 representation of a 64x64x64 array. The pretrained model 
 was built using Lua with Torch, and converting the model to TensorFlow will most likely require a significant effort.  
### 3| Connecting Each Model
The main challenge of the project is to go from a 
class prediction from Sketch-RNN to a 200-dimensional 
latent vector which will output a 3D shape from the corresponding class. 
First, it is important to note that there are far more categories in the 
QuickDraw! dataset than the number of classes the 3D GAN can generate, so 
we will inevitably be limited by the latter. Second, the structure of the 
generating latent space for the 3D GAN is unknown. For example, we do not 
necessarily know the region of the latent space that produces cars, but, 
if we did, we could sample that region to generate a 3D car when the class 
‘car’ is predicted by the Sketch-RNN classifier. The main challenge will 
thus be to learn the structure of this latent space. 

### References
[1]	D. Ha and D. Eck, “A Neural Representation of Sketch Drawings,” 2017.  
[2]	J. Wu, C. Zhang, T. Xue, W. T. Freeman, and J. B. Tenenbaum, “Learning a Probabilistic Latent Space of Object Shapes via 3D Generative-Adversarial Modeling,” no. Nips, 2016.

