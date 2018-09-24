---
layout: post
title:  "Assignment 3"
date:   2018-09-23 23:27:01 -0400
categories: assignments
---
## 1.4 Visualizing Convolutional Neural Networks
Let's try a few confusing examples and see how the CNN does.  
  
First, a simple 1: nothing hard here and the CNN does well.  

![img]({{ site.baseurl }}/assets/assignment-3/1_4_1.PNG)  
  
What if I write a 1 in a different style? This 1 definitely looks relatively weird, but I'm confident a human would 
recognize this is a 1. However, the CNN predicts a 6.  

![img]({{ site.baseurl }}/assets/assignment-3/1_4_3.PNG)  
  
Let's do a very slanted 1. Again, that's too confusing for the network, which predicts an 8.    

![img]({{ site.baseurl }}/assets/assignment-3/1_4_4.PNG)
  
 What about a 2 with parts missing? No problem, thanks to the max pooling.
![img]({{ site.baseurl }}/assets/assignment-3/1_4_5.PNG)

## 3.5 Fast Style Transfer

### Original: Stata
![img]({{ site.baseurl }}/assets/assignment-3/stata.jpg){:width="100%"}

### Filters
#### La Muse, Picasso
![img]({{ site.baseurl }}/assets/assignment-3/la_muse.jpg){:width="100%"}

#### Rain Princess, Afremov
![img]({{ site.baseurl }}/assets/assignment-3/rain_princess.jpg){:width="100%"}

#### Udnie, Picabia
![img]({{ site.baseurl }}/assets/assignment-3/udnie.jpg){:width="100%"}

### Results
#### Stata + Udnie
![img]({{ site.baseurl }}/assets/assignment-3/2_1.png){:width="100%"}
#### Stata + 2x Udnie
The effect is accentuated (more weight given to the style vs content), and we can see the black border growing thicker. 
This padding is, I assume, the effect of using same padding in the fast style transfer network.
![img]({{ site.baseurl }}/assets/assignment-3/2_2.png){:width="100%"}
#### Stata + 2x Udnie + La Muse
The padding effect becomes even more apparent. The superposition of style, albeit not obvious, is noticeable.
![img]({{ site.baseurl }}/assets/assignment-3/2_3.png){:width="100%"}
#### Stata + 2x Udnie + La Muse + Rain Princess
Same remark as above regarding the padding effect. The initial content is still perceptible, but the image is increasingly 
abstract.
![img]({{ site.baseurl }}/assets/assignment-3/2_4.png){:width="100%"}

## 3.6 Building CNNs with Code
### Hyperparameters
* `BATCH_SIZE = 64`
* `NUM_BATCHES = 5000`

### Model Architectures
#### Model 1
* Convolutional Layer with 16 3x3 Filters with ReLu Activation, and Stride of 3
* Max Pooling with Pool Size of 2 and Stride of 2 
* Convolutional Layer with 32 3x3 Filters with ReLu Activation, and Stride of 3
* Max Pooling with Pool Size of 2 and Stride of 2 
* Convolutional Layer with 32 3x3 Filters with ReLu Activation, and Stride of 3
* Flattening Layer
* Dense Layer with 64 Units
* Dense Layer with 10 Units and Softmax Activation
{% highlight javascript %
const model = tf.sequential();
model.add(tf.layers.conv2d({
    inputShape: image_shape,
    kernelSize: 3,
    filters: 16,
    activation: 'relu'
}));
model.add(tf.layers.maxPooling2d({poolSize: 2, strides: 2}));
model.add(tf.layers.conv2d({kernelSize: 3, filters: 32, activation: 'relu'}));
model.add(tf.layers.maxPooling2d({poolSize: 2, strides: 2}));
model.add(tf.layers.conv2d({kernelSize: 3, filters: 32, activation: 'relu'}));
model.add(tf.layers.flatten({}));
model.add(tf.layers.dense({units: 64, activation: 'relu'}));
model.add(tf.layers.dense({units: 10, activation: 'softmax'}));

model.compile({
  optimizer: 'sgd', 
  loss: 'categoricalCrossentropy',
  metrics: ['accuracy']
});

{% endhighlight %}

Results for MNIST (stopped early)  
![img]({{ site.baseurl }}/assets/assignment-3/3_2.png)

Results for Fashion MNIST (stopped early)  
![img]({{ site.baseurl }}/assets/assignment-3/3_1.png)

#### Model 2
Let's increase the number of filters:

### Why is CIFAR 10 harder to learn?
First of all, CIFAR-10 images are larger and have 3 channels, and that larger amount of data may explain a slower training. 
That said, this is not the fundamental reason behind the training complexity of CIFAR 10. Whereas Fashion MNIST and MNIST both encode 2D information
---a hand-written digit or the outline of a piece of clothe---in a 2D medium, CIFAR 10 is constituted of images of real-life 3D things, 
viewed from different viewpoints, cropped, with different lighting conditions, which are all factors that contribute to making CIFAR 10 harder to train.


## Code
* [CNN 1](https://github.com/danhaive/6s198/blob/master/assets/assignment-3/index_CNN1.js)











