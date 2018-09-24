---
layout: post
title:  "Assignment 2"
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



















