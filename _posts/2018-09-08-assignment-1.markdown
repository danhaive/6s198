---
layout: post
title:  "Assignment 1"
date:   2018-09-08 23:11:01 -0400
categories: assignments
---
Renaud Danhaive  
danhaive@mit.edu
## 4 Examining the Confidence Levels

### 4.1 Button Functionality
Implementing the functionality requested is just a few lines of codes.

{% highlight javascript %}
// Instantiate global variables with adequate arrays
var globConf = [0, 0, 0];
var globNCounts = [0, 0, 0];
// Modify onSpecialButtonClick
function onSpecialButtonClick() {
  alert('Number of the top ' + TOPK + ' closest matches in each class: ' + globNCounts
    + '\nConfidence for which the image matches each class: ' + globConf);}
{% endhighlight %}

### 4.2 Some Experiments with Confidence Values

#### Experiment 1: Can the machine differentiate my face from a plant and the background?
Yes! And it does so with high confidence (100%).
#### Me
![img1](/assets/assignment-1/tm_test_4_1_1.PNG)
#### My plant (it's a money tree)
![img2](/assets/assignment-1/tm_test_4_1_2.PNG)
#### The background (note the nice MIT ceilings!)
![img3](/assets/assignment-1/tm_test_4_1_3.PNG)

#### Experiment 2: What if I try to confuse the machine on purpose?
Here, I "trained" the model on what is essentially the same image. The result is unstable predictions, and low confidence 
(55% or 11/20 nearest neighbors for the top class). 
This makes complete sense: the images are all located in the same region of the 1000-dimension logits space.

![img4](/assets/assignment-1/tm_test_4_2_1.PNG)

#### Experiment 3: Does the model learn what I want to teach it?

For example, analogously to the Russian tank parable, I try to teach the machine two different hand configurations. 
However, I'm not careful: I train the first hand configuration at my desk and the second one in a conference room. 
Does the machine learn the context backgrounds or the hand configurations?  
As the images below show, the model clearly learns the background and lighting conditions instead of the hand configuration. 
It is not suprising given that most of the image is affected by its background rather than by the shape of my hand. 
Nonetheless, I was suprised by the high confidence of the classifier. Because of the change in context, 
the images I trained and tested the model on must be in regions that are well separated in terms of the distance metric 
chosen (cosine similarity) in the 1000-dimensional logits space.

#### Training Class 1: Open Fist <-> Cat
I'm at my desk while training the model.

![img1](/assets/assignment-1/tm_test_4_3_1.PNG)
#### Training Class 2: Closed Fist <-> Pomeranian
I'm in a conference room while training the model.

![img2](/assets/assignment-1/tm_test_4_3_2.PNG)
#### Testing: Open Fist Gives Pomeranian (WRONG)
![img3](/assets/assignment-1/tm_test_4_3_3.PNG)
#### Testing: Closed Fist Gives Cat (WRONG)
![img3](/assets/assignment-1/tm_test_4_3_4.PNG)

#### Experiment 4: Will the machine be confused if I train it with a bike helmet on and test it without the bike helmet?

In this case, I trained three classes with different face and hand configurations while wearing a helmet—my
go-to fashion accessory. I wanted to know if the KNN classifier would fare well if I tested it without the helmet on. 
Intuitively, I thought it should: if the model classifies based on the (dis)similarity between 
images, only the information that changes across the classes matter. Since the helmet is present in all of 
my training data, it has little to no bearing in the classification and removing it does not affect the model. 
That was the case in practice, although the predictions were a tad more unstable, which also makes sense 
if you think of the problem in terms of the distance metric. Still, it's really cool that the model only 
accounts for the differences between the classes by design.

#### Training Class 1 with Helmet: Tongue Out + Thumb Up <-> Cat
![img1](/assets/assignment-1/tm_test_4_4_1.PNG)
#### Training Class 2 with Helmet: All Good <-> Pomeranian
![img2](/assets/assignment-1/tm_test_4_4_2.PNG)
#### Training Class 3 with Helmet: Hello <-> Bunny
![img3](/assets/assignment-1/tm_test_4_4_3.PNG)
#### Testing without Helmet: Tongue Out + Thumb Up <-> Cat (CORRECT)
![img3](/assets/assignment-1/tm_test_4_4_4.PNG)
#### Testing without Helmet: All Good <-> Pomeranian (CORRECT)
![img3](/assets/assignment-1/tm_test_4_4_5.PNG)
#### Testing without Helmet: Closed Fist Gives Cat (CORRECT)
![img3](/assets/assignment-1/tm_test_4_4_6.PNG)

## 5 Scaling the Confidence Values
### 5.1 Modifying the Computation of Confidence Values
#### Original (Weighted) Method
The snippet of code below is the portion of `computeConfidences()` that originally performed the computation of confidence values.
{% highlight javascript %}
let nCounts = [0, 0, 0];
let confidences = [];
for (let index = 0; index < CLASS_COUNT; index += 1) {
    nCounts[index] = classTopKMap[index];
    const probability = classTopKMap[index] / kVal;
    confidences[index] = probability;}
{% endhighlight %}
#### Unweighted Method
With this method, a probability of 1 is assigned to the class with most nearest neighbors and zero probabilities are 
assigned to all other classes.
{% highlight javascript %}
let confidences = [0,0,0]; // initialize all confidences to 0
let nCounts = classTopKMap;
var maxKindex = classTopKMap.reduce((iMax, val, index, arr) => val > arr[iMax] ? index : iMax, 0); // get class with max number of nearest neighbors
confidences[maxKindex] = 1; // assign confidence of 1 to class with max nearest neigbors
{% endhighlight %}
#### Other Method: Any Class with a Nearest Neighbor is Predicted with 100% Confidence
With this method, a probability of 1 is assigned to any class that has at least one nearest neighbor.
{% highlight javascript %}
let confidences = [];
for (let index = 0; index < CLASS_COUNT; index += 1) {
    nCounts[index] = classTopKMap[index];
    confidences[index] = classTopKMap[index] > 2 ? 1 : 0;}
{% endhighlight %}
### 5.2 Computing the Confidence Values
#### Unweighted Method
Here, I modified the snippet of code that computes the confidence value for each class. The reported confidence
values are unweighted, i.e. the class with the max number of samples among the top K nearest neighbors has a 
reported confidence value of 1 whereas the others have a confidence value of 0. I don't clearly see a reason 
why that would really help the model report more accurate classification in that it is essentially reporting 
the same information---the class that is the most likely to correspond to that image---but it is doing 
so in a more clear-cut fashion. As a result, it does not help with the 'confusing' examples, and it has the side-effect 
of making the machine looking 'dumber' than it is: the machine is wrong, and it is convinced it is wrong.  
So, is this method for computing the confidence values ever useful?
#### Other Method
This one is much more interesting. I modified the code such that any class that has at least one sample as one 
of the top K nearest neighbors is predicted with 100% confidence. Why can that be useful? Consider the following 
example: I train three classes on the same image---me staring blankly at the camera---and subsequently 
test the model. With the previous weighted calculation, the model would---theoretically---predict each 
class with a confidence of about 33%. With the unweighted one, it gets worse: the machine would erratically jump 
from one class to another with 100% confidence. That does not make much sense given that the three classes are 
in fact happening at the same time. As a result, predicting as likely any class with a nearest neighor works 
well in this case. It simply tells us: *"Yes, these three things are happening all at once."*

![img](/assets/assignment-1/tm_test_5_1_1.PNG)

### 5.3 Second Method for Object Detection
The second method can be useful for object detection. In a sense, the reported confidence values do not make sense from a probabilistic point of view because the class 
scores may add up to a number larger than 1. To accomodate this issue, simply rephrase the question from 
*"What is the (conditional) probability that this image belongs to this class"* to *"What is the (conditional) probability 
that this class is present in this image?"*. Now, this is a good question for object detection. Consider this: 
I train the machine to recognize me sticking out my tongue (class 1) and my keys (class 2). Again, with the previous 
methods for computing confidence, if stick out my tongue while holding my keys, the machine would most likely 
be quite confused. However, with the proposed method, the machine tells me: *"Yes, I can see very well that you're 
doing both things at the same time"*. That's what I call a smart machine!

### Training Class 1: Tongue Out <-> Cat
![img](/assets/assignment-1/tm_test_5_1_2.PNG)
### Training Class 2: Keys <-> Pomeranian
![img](/assets/assignment-1/tm_test_5_1_3.PNG)
### Testing: Tongue Out + Keys <-> Cat + Pomeranian (Correct)
Note the confidence levels!
![img](/assets/assignment-1/tm_test_5_1_4.PNG)

## 6 Limiting the Number of Training Examples per Class
### 6.1 Implementation
For the implementation, I chose a quick, scrappy approach: register a new constant variable in the form of an array 
storing the maximum number of samples per class. Hard-coding is never great, but, for our current purposes, this 
will do fine. A couple of quick changes to `animate()` did the trick, namely add a conditional statement for the alert 
and modify the condition for the portion of the code that adds images to the training data.

{% highlight javascript %}
// Maximum # of Training Samples per Class
const MAX_NSAMPLES = [200, 200, 200] // global scope

// Modify animate()
if (this.isDown && (this.current.imagesCount >= MAX_NSAMPLES[this.current.index])) 
    {
    setTimeout(function() { alert('The maximum number of samples allowed for this class has been reached.'); });
    this.isDown = false;
    this.timer = requestAnimationFrame(this.animate.bind(this));
    }
else if (this.isDown && (this.current.imagesCount < MAX_NSAMPLES[this.current.index])) {
    ... // rest is the same
{% endhighlight %}
### 6.2 Unbalanced Number of Training Samples
#### How do uneven limits on the allowed number of samples per class affect the model?
To verify this, I chose the following limits: 200 for class 1 (cat), 20 for class 2 (pomeranian), and 20 for 
class 3 (bunny). Going under 20 samples for any of the classes would necessarily have catastrophically affected 
performance given `K = 20`. I trained class 1 with images of my face, class 2 with my face still there and my left thumb up, and 
class 3 with the background only. In this setting, the classifier should easily differentiate between classes 1 and 3. 
Classes 1 and 2 are a bit harder to differentiate, but the model was able to capture these differences reasonably well. 
As the images below show, the model still works pretty well. However, predictions in favor of class were definitely more unstable 
than previously observed.

### Testing Class 1: Me <-> Cat (CORRECT)
![img](/assets/assignment-1/tm_test_6_1_1.PNG)
### Training Class 2: Me + Thumb Up <-> Pomeranian (CORRECT)
![img](/assets/assignment-1/tm_test_6_1_2.PNG)
### Training Class 2: Background Only <-> Bunny (CORRECT)
![img](/assets/assignment-1/tm_test_6_1_3.PNG)

I then tried something harder, only with two classes this time. For the training, 
I closed my left and right eyes for class 1 and 2, respectively.

### Testing Class 1: Left Eye Closed <-> Cat (CORRECT)
![img](/assets/assignment-1/tm_test_6_1_4.PNG)
### Training Class 2: Left Eye Closed <-> Cat  (INCORRECT)
![img](/assets/assignment-1/tm_test_6_1_5.PNG)

With such a nuanced difference and because of the imbalance in number of training samples per class, the system does not work well.

## 7 Further Explorations
### Adjust K

This one could not be easier to implement.
A simple change in the value of the global constant TOPK and we're good to go. 
To see the effect of K on prediction, I am going to think about the two ends of the spectrum. 
Let's start with the lower end: K=1. That does not sound like a very good idea. 
Why? Because the classifier predicts based on the class of only one nearest neighbor. 
As a result, all you need is one outlier or one misclassified training sample to completely throw off the model. 
In a sense, this is a form of overfitting. I can replicate this fairly easily:
       
![img](/assets/assignment-1/tm_test_7_1_1.PNG)

Here, I trained class 1 with "thumb up" and class 2 ith "no thumb up". I 'accidentally' trained class 1 with a couple of
"no thumb up" images, and it did indeed throw off the model as expected.  
Let's now look at large values of K. Evidently, if K is equal or larger than the total number of training samples, 
the model will simply predict the ratio of each class among your training samples. As opposed to K=1, the model is underfitting. 
Not very interesting. On the one hand, one should pick K such that the model is robust to outliers and can provide confidence 
values with enough granularity. On the other hand, K should be small enough to give meaningful results.

### Remove a Class
To do this, I simply tinkled around with the html to remove the interactions with a third class, and I changed a couple 
global constants here and there.
![img](/assets/assignment-1/tm_test_7_1_2.PNG)
I also considered adding a class for a moment, but, upon inspection of the code, which contained 
hard-coded constants and variables all over the place, I preferred to call it a day!
 
## Conclusion
All in all, a fun assignment!