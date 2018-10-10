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
The image below shows the computation of the similarity between the generated font and all fonts from the embedding
database.  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-3.PNG)  
For this generated font, the closest font ID is 5953.
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-4.PNG)    
Here is font #5953. The cosine similarity method works pretty welll here!
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-5.PNG) 
Let's try on a weird generated font: 
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-6.PNG) 
### 2
The average font is the font for which each logit is calculated as the average of the corresponding logits of all other fonts. 
My function returned the font 22776. It does not quite look average to me, however, and I'm wondering if my function 
is working as intended.
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-7.PNG) 
### 3.a
#### Bolding
We apply the vector to the font shown in the image below.  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-8.PNG)  
This gives the font below.   
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-9.PNG)
We see that the vector does bold the original vector, but it also changes the original vector fundamentally (e.g. it makes 
it upper case). All the characteristics of the bolding carry over, and it would require some more font arithmetic.
#### Dotting
We apply font #5950 as a 'dotting' vector.  
Original
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-10.PNG)
New  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-11.PNG)
### 3.b
Here, I collected 10 fonts for which letters are in white on a black bakground with a given shape (heart, circle, square, etc.).
The corresponding IDs are: 3154 3078 7304 3059 82 3151 3155 3730 3077 3083.
If we apply it on this font:
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-12.PNG)
The result is a beautiful black stain font!
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-13.PNG)
Let's now try to apply a 3D effect on the same font with the fonts (I only found 8): 1059 3304 1364 1660 3305 6549 429 1363.
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-14.PNG)  
Once again, the result is disastrous...
### 3.c
Let's try it with bolding. We take the following 10 bold fonts: 7596 5890 5015 4671 5891 5889 5888 2691 7597 3654.
And the following regular fonts: 4059 6936 4257 810 6740 6746 3379 3675 1271 1528.
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-15.PNG)  
This method definitely works better than before. It's not perfect, probably because I did not pair the bold and regular fonts 
quite well, but the vector is close to the boldness direction.  
Now, let's try to find a serif direction. We'll consider the following serif fonts: 3880 5569 6889 7308 2671 640 7123 5844 7628 1789.
And the following sans serif fonts: 7362 3630 6983 2812 3955 6745 6746 811 4534 6756.  
Let's see if the vector succeeds in 'serifizing' a font.  
Before:
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-16.PNG)
After:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-17.PNG)
It worked! Serif fonts also tend to have some thinner strokes, and the result is a bit hard to read because we started 
with a light font. Still, we can clearly the little serif quirks appear at each corner of the letters!
### 4
Uppercase fonts: 7931 4270  | Lower fonts: 4157 519  
To compute the capitalize vector, we use the same technique as above (averaged difference).  
Before:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-18.PNG)  
After:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-19.PNG)
### 5
I selected 4 fonts I liked (5757 5687 4934 7631) and 4 fonts I diskliked (7933 3367 3718 888), and I applied the same 
method as above.  
Here's the result.  
Before:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-20.PNG)  
After:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-21.PNG) 
Before:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-22.PNG)  
After:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-23.PNG) 
Before:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-24.PNG)  
After:  
![img]({{ site.baseurl }}/assets/assignment-5/latentspacexplorer-25.PNG) 

### 6 

[Code](https://github.com/danhaive/6s198/blob/master/assets/assignment-5/code)

