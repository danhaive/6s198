---
layout: post
title:  "Assignment 4"
date:   2018-10-09 23:27:01 -0400
categories: assignments
---

## Dimensionality Reduction of MNIST with t-sne
By visualizing the high-dimensional space of the MNIST dataset in 3D using dimensionality reduction, we observe that 
the different digits mostly appear in distinct clouds.  

![img]({{ site.baseurl }}/assets/assignment-5/mnist-tsne-1.PNG)  
Some digits, however, are grouped but may be scattered in multiple clusters (see images of 4 below) and are generally
less distinct than the others.

![img]({{ site.baseurl }}/assets/assignment-5/mnist-tsne-2.PNG)  
On the contrary, other digits (6 for example: see below) are very well separated from the rest of the data.  
![img]({{ site.baseurl }}/assets/assignment-5/mnist-tsne-3.PNG)  
In addition, most digits have outliers that overlap with other regions, and, as a result, some portions of the space are 
simply a mess. 
![img]({{ site.baseurl }}/assets/assignment-5/mnist-tsne-4.PNG)  
Interestingly, digits that looks similar (0 and 6 for example: see below) are grouped in close clusters.
![img]({{ site.baseurl }}/assets/assignment-5/mnist-tsne-5.PNG)  

## Word Geometry 
Let's see where words related to politics are situated on the good (left) to bad (right) spectrum according to the Word2Vec model:    
![img]({{ site.baseurl }}/assets/assignment-5/word2vec-1.PNG)
I personally don't think the results are convincing here. For example, 'fascism' is better than 'philosophy' and 
'democratic'. Or maybe this just demonstrates the current state of the world!  
Let's try with the Word2Vec All.  
![img]({{ site.baseurl }}/assets/assignment-5/word2vec-2.PNG)
The result is perhaps slightly better, but still underwhelming. I find that 'right' being close to the 'good' end of 
the spectrum to be interesting. In this case, 'right' is oft-used term in politics, but it also a synonym of 'good'. 
This shows that two meanings of the word are mapped to the same point. I assume there must exist more powerful NLP 
models that can account for that.

## Word Arithmetic
* King + (Woman - Man) = Queen (of course!!)
* North + (Antarctic - Arctic) = South
* China + (Japan - America) = Chinaware (makes sense)
* Harvard + (Stanford - MIT) = USC

## Font Embeddings

### Groupings from PCA
* Stylized: 4169 7863 5225 904
* Striped: 4141 1115 6554 1948

### Groupings from t-sne (3789 iterations)
* Dynamic: 2436 2444 2450 2439
* Dots: 5950 5954 5959 5936

###
1764:
 5715 5230 2619 2162 5163 5162 3042 1764 853 5255 2452 1757
 
## Latent Space Explorer
### 0
 ![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-2.PNG)
### 1
