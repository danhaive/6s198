---
layout: post
title:  "Assignment 2"
date:   2018-09-16 23:27:01 -0400
categories: assignments
---

Building Models with [Model Builder](https://courses.csail.mit.edu/6.s198/spring-2018/model-builder/src/model-builder/)
## Problem 1
### Why Is the Model Invalid?
The model is invalid because the data fed into the network has dimensions `[28, 28, 1]` but the 
softmax layer requires an input of dimensions `[10]`. 

### Why Is the Model Not Performing Well (Yet)?
The weights, which dictate the predictions made by the model, has been randomly initialized, and the network has not been
trained yet. As a result, the network is randomly guessing the class of any given image. Asymptotically, we can thus 
expect an accuracy of `0.1` given that there are 10 different labels, observing the predictions in real-time 
tend ton confirm that. Similarly, we can expect that the correct class will be in on of the top three classes with `0.3` 
probability. Again, that seems to be confirmed upon brief inspection of the predictions.

## Problem 2: Training the Model
### 2.1 Training Log for MNIST and Fashion MNIST
#### MNIST
* Examples Trained: 5056
* Examples/Sec: 1210
* Training Accuracy: 79.7%
* Testing Accuracy: 69.2%
* Inferences/Sec: ~1100

#### Fashion MNIST
* Examples Trained: 5440
* Examples/Sec: 1410
* Training Accuracy: 71.9%
* Testing Accuracy: 68.8%
* Inferences/Sec: ~1000

Most of the metrics for both models are somewhat similar, although the Fashion MNIST model performs more poorly on the training data. 
Testing accuracy is similar for both models which means that there is larger gap between the testing and training accuracies for MNIST. 
The accuracy graphs for MNIST indeed show a larger dip in the testing accuracy after a certain amount of training, 
which indicate the MNIST model overfits the data more.

### 2.2 CIFAR 10
* Examples Trained: 73792
* Examples/Sec: 1040
* Training Accuracy: 29.7%
* Testing Accuracy: 26.6%
* Inferences/Sec: ~900

### 2.3 Improving MNIST Model by Adding More Fully Connected Layers
Indeed, this does not work well, and, soon enough, we start seeing these `NaN` values.

![img]({{ site.baseurl }}/assets/assignment-2/nan_1_2_4.PNG)

### 2.4 Why Does It Get Worse?
I imagine this has something to do with the gradient used by the optimization. The gradient explodes, which in turn 
makes the numbers in the neural network blow up because the learning rate is not adaptive.

## Problem 3
### Improving the Model By Adding a ReLu Activation
Even though, we have introduced non-linearity, the model still performs poorly---it performs worse than our first model! 
In fact, with a testing accuracy of about 10%, it performs as well as random guessing.
![img]({{ site.baseurl }}/assets/assignment-2/3_1_1.PNG)

### Improving the Model by Adding More Hidden Units to the 1st Layer (and Keeping the ReLu)
This makes a huge difference! With more hidden units, the testing accuracy shoots up to 82.9% after about 5000 training samples. 
This is more than 10% better than the very first model we tried.

![img]({{ site.baseurl }}/assets/assignment-2/3_1_2.PNG)

## Problem 4
### 4.1 Exprimenting with the Number of Layers
There is some overfitting happening for all architectures---except the first one---as the training accuracy asymptotically converges. 
This is particularly apparent for the second model. That said, the testing accuracy of a few models dip before shooting back up and reaching a 
new maximum value, which lend to think than an early-stopping criterion perhaps is not always the ideal solution for reducing overfitting. 
Based on the testing accuracy, the 4<sup>th</sup>---3 FC layers with ReLu activations on top of the final layer---model 
seems to perform the best. We also observe that more complex models tend to process samples more slowly---no suprise there. 
The images below show some training figures for each model.
#### Model with 1 Fully Connected Layer with 10 Hidden Units 
We've seen this one before. Here are some training stats: 
* Examples/sec: 1390
* Examples trained: 4992
* Total time: 4.4 sec
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_1_1.png)
#### Model with 2 Fully Connected Layers (1 Top Layer with 100 Hidden Units and ReLu Activation)
* Examples/sec: 748
* Examples trained: 32640
* Total time: 43.3 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_1_2.png)
#### Model with 3 Fully Connected Layers (2 Top Layers with 100 Hidden Units and ReLu Activations)
* Examples/sec: 728
* Examples trained: 25536
* Total time: 42.1 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_1_3.png)
#### Model with 4 Fully Connected Layers (3 Top Layers with 100 Hidden Units and ReLu Activations)
* Examples/sec: 556
* Examples trained: 25664
* Total time: 50.8 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_1_4.png)
#### Model with 5 Fully Connected Layers (4 Top Layers with 100 Hidden Units and ReLu Activations)
* Examples/sec: 524
* Examples trained: 26240
* Total time: 61.4 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_1_5.png)
### 4.2 Experimenting with Layer Width
Both models achieved a similar testing accuracy, although the second one required longer training, which seems due to instability in 
the optimization procedure at the beginning. In general, I would have expected the second architecture to do worse. 
Intuitively, putting the narrow layer first means that more information about the incoming data is lost because it is smushed in fewer 
hidden units. 
#### Model 1: Top Layer with 100 HUs and Second Layer with 20 HUs
* Testing Accuracy: 90.7%
* Examples/sec: 674
* Examples trained: 15424
* Total time: 27.3 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_2_1.png)
#### Model 2: Top Layer with 20 HUs and Second Layer with 100 HUs
* Testing Accuracy: 90.6%
* Examples/sec: 525
* Examples trained: 28736
* Total time: 57.2 sec.
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_4_2_2.png)
### 4.3 Test on Other Datasets
I tried the first experiment on CIFAR 10. In particular, with 5, 4, 3, 2 FC layers, and after about 15,000 samples, 
the model achieved a testing accuracy of respectively 39.1%, 40.6%, 45.3%, and 35.9%. In other words, they were all similarly terrible, 
most likely because this classification task is harder and requires the use of CNNs.
I tested experiment 4.2 on the the Fashion MNIST dataset, and the result was similar, i.e. both models achieved a similar testing accuracy 
at the end, but the narrow-top architecture was slower to converge.

## Problem 5
Increasing the number of batches essentially amount to increasing the number of iterations for the optimizer. 
Unsurprisingly, the objective---the training loss---is further reduced with more batches.  

* `BATCH_SIZE = 20`, `NUM_ BATCHES = 50`: training loss = 1.92
* `BATCH_SIZE = 20`, `NUM_BATCHES = 100`: training loss = 1.58
* `BATCH_SIZE = 20`, `NUM_BATCHES = 200`: training loss = 1.33
* `BATCH_SIZE = 20`, `NUM_BATCHES = 400`: training loss = 0.92  

Increasing the batch size stabilizes the convergence of the optimizer, and the training loss graph is less noisy.
As the batch size increases, the optimizer tends to a 'true' gradient descent algorithm.

## Problem 6

### 6.1 Examples of Poor Performance
The model misclassifies the digit below as an 8 (probability: 0.159) instead of 1 (probability: 0.145), most likely because 
the digit is drawn slanted and ressembles one of the diagonals of a typical 8.
![img]({{ site.baseurl }}/assets/assignment-2/6_1_1.png)  
This one is correctly classified as a 1 (probabilty: 0.173), even if the model still thinks it could be an 8 with 0.142 
probability.
![img]({{ site.baseurl }}/assets/assignment-2/6_1_3.png)  
The digit below is classified as a 2 (probability: 0.198) instead of 5 (probability: 0.109).
![img]({{ site.baseurl }}/assets/assignment-2/6_1_2.png) 

### 6.2 Changing the Batch Size and the Number of Batches
* `BATCH_SIZE = 20`, `NUM_BATCHES = 100`: accuracy = 55%
* `BATCH_SIZE = 20`, `NUM_BATCHES = 400`: accuracy = 80% (great improvement)
* `BATCH_SIZE = 50`, `NUM_BATCHES = 400`: accuracy = 84% (best)
* `BATCH_SIZE = 100`, `NUM_BATCHES = 500`: accuracy = 82% (a little worse)
* `BATCH_SIZE = 50`, `NUM_BATCHES = 500`: accuracy = 82% (a little worse)
* `BATCH_SIZE = 50`, `NUM_BATCHES = 1000`: accuracy = 84% (a little worse)

Increasing the number of batches has a dramatic improvement on the testing accuracy, but, past a certain point, it does not make much 
of a difference because the model is too limited.

### 6.3 Construct Model
Because we already experimented a lot with Model Builder in the beginning of the asssignment, I chose to implement one of the best 
architectures I have explored, i.e. a multilayer perceptron with 3 top dense layers with 100 hidden units and relu activation 
and a bottom layer with 10 hidden units and 
softmax activation. 
{% highlight javascript %}
const model = tf.sequential();

// First flatten the image
// First layer must have an input shape defined.
model.add(tf.layers.flatten({
  inputShape: image_shape
}))

// Add 3 fully connected (dense) layers with ReLu Activation
model.add(tf.layers.dense({
    units: 100,
    kernelInitializer: 'varianceScaling',
    activation: 'relu'
}));
model.add(tf.layers.dense({
    units: 100,
    kernelInitializer: 'varianceScaling',
    activation: 'relu'
}));
model.add(tf.layers.dense({
    units: 100,
    kernelInitializer: 'varianceScaling',
    activation: 'relu'
}));
// Add a fully connected (dense) layer
model.add(tf.layers.dense({
  units: 10, 
  kernelInitializer: 'varianceScaling'
}));

model.add(tf.layers.softmax());

model.compile({
  optimizer: 'sgd', 
  loss: 'categoricalCrossentropy',
  metrics: ['accuracy']
});
{% end highlight%}
For MNIST, the improvement is not dramatic, and the testing accuracy after 1000 batches of 100 samples peaks at 86%.  
Testing Accuracy for MNIST with MLP
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_6_3_1.png)
  
Testing Accuracy for Fashion MNIST with MLP
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_6_3_2.png)
  
Testing Accuracy for CIFAR-10 with MLP
![img]({{ site.baseurl }}/assets/assignment-2/accuracy_6_3_3.png)

MLP's are not good at image classification, because they do not easily extract meaningful features from image data.  
To improve performance, I also implemented a simple CNN.
{% highlight javascript %}
const model = tf.sequential();

// First flatten the image
// First layer must have an input shape defined.
model.add(tf.layers.conv2d({
    inputShape: image_shape,
    strides: [1,1],
    filters: 8,
    kernelSize:[5,5],
    padding: "valid",
    activation: 'relu'
}))
model.add(tf.layers.maxPooling2d({
    inputShape: [image_shape[0]-4,image_shape[1]-4, 8],
    strides: [2,2],
    poolSize: [2,2]
}))

model.add(tf.layers.conv2d({
    inputShape: [(image_shape[0]-4)/2,(image_shape[1]-4)/2, 16],
    strides: [1,1],
    filters: 16,
    kernelSize:[3,3],
    padding: "valid",
    activation: 'relu'
}))

model.add(tf.layers.maxPooling2d({
    inputShape: [(image_shape[0]-4)/2-2,(image_shape[1]-4)/2-2, 16],
    strides: [2,2],
    poolSize: [2,2]
}))

model.add(tf.layers.flatten({
  inputShape: [((image_shape[0]-4)/2-2)/2,((image_shape[1]-4)/2-2)/2, 16]
}))
// Add a fully connected (dense) layer
model.add(tf.layers.dense({
  units: 10, 
  kernelInitializer: 'varianceScaling'
}));

model.add(tf.layers.softmax());

model.compile({
  optimizer: 'sgd', 
  loss: 'categoricalCrossentropy',
  metrics: ['accuracy']
});
{% end highlight %}
The architecture of the CNN is simple, but its performance is superior to the MLP on the MNIST dataset (96% accuracy).
![img]({{ site.baseurl }}/assets/assignment-2/6_3_4.png) 
However, the performance worsens for Fashion MNIST. The model architecture may be too simplistic.
![img]({{ site.baseurl }}/assets/assignment-2/6_3_5.png) 
## Problem 7
* [index.js for MLP](https://github.com/danhaive/6s198/blob/master/assets/assignment-2/index_MLP.txt)
* [index.js for CNN](https://github.com/danhaive/6s198/blob/master/assets/assignment-2/index_CNN.txt)

## Style Transfer
### Kandinskian Desert
![img]({{ site.baseurl }}/assets/assignment-2/kandinsky_style_desert.jpg) 
#### Original
![img]({{ site.baseurl }}/assets/assignment-2/desert.jpg) 
#### Style: Blue Segment, Kandisnky, 1921
![img]({{ site.baseurl }}/assets/assignment-2/kandinsky.jpg) 
### Monet's Winter
![img]({{ site.baseurl }}/assets/assignment-2/monet_style_winter.jpg) 
#### Original
![img]({{ site.baseurl }}/assets/assignment-2/winter-scene-snow-sun-trees-winter.jpg) 
#### Style: The Magpie, Monet, 1869
![img]({{ site.baseurl }}/assets/assignment-2/monet-themagpie.jpg)
### Seurat's California
![img]({{ site.baseurl }}/assets/assignment-2/california_seurat_styl.jpg) 
#### Original
![img]({{ site.baseurl }}/assets/assignment-2/IMG_2392.jpg) 
#### Style: The Seine and la Grande Jatte, Seurat, 1888
![img]({{ site.baseurl }}/assets/assignment-2/seurat_georges_3.jpg)














