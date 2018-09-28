---
layout: post
title:  "Data Gathering Workshop"
date:   2018-09-27 23:27:01 -0400
categories: assignments
---


## Downloading and Viewing Data

There's a lot of superfluous data in the Airbnb Booking dataset, and we should choose our features carefully before dumping all the 
data in a model. For example, in the `*_users.csv` files, the first browser---used to sign up for Airbnb, I imagine---
is recorder. While it is possible that Safari users prefer sunnier destination than Mozilla Users, 
I find that quite unlikely.  
Superflous data is not a big deal unto itself, we can simply ignore it. What's more concerning to me is that 58% of the 
 training data is actually useless because it is labelled as `NDF` or No Destination Found. Perhaps, that can help us 
 predict if a user is not gonna make a booking, but that is not the primary goal of this challenge.  
 In addition, most users are associated with a single destination. I can easily imagine users visiting different countries. 
 Their behavior might also change over time, e.g. they may become more comfortable with Airbnb and start using for 
 more distant destinations, which will be hard/impossible to model with this data.  
Finally, there are lots of missing values.
There are other problems with this data, and these are a few instances that are somewhat concerning.

## Data Processing and Kaggle Kernels
Let's see if there are differences in the preferred destination across genders by creating a bar plot for each gender 
category (`NaN` values are ignored) with the percentages of each country destination.  
The code is as follows:

{% highlight python %}
male = sum(users.loc[users['gender'] == 'MALE', 'country_destination'].value_counts())
female = sum(users.loc[users['gender'] == 'FEMALE', 'country_destination'].value_counts())
other = sum(users.loc[users['gender'] == 'OTHER', 'country_destination'].value_counts())

male_destinations = users.loc[users['gender'] == 'MALE', 'country_destination'].value_counts() / male * 100
female_destinations = users.loc[users['gender'] == 'FEMALE', 'country_destination'].value_counts() / female * 100
other_destinations = users.loc[users['gender'] == 'OTHER', 'country_destination'].value_counts() / other * 100

plt.figure(figsize=(15,5))
width = 0.3

other_destinations.plot(kind='bar',  color='#53FF00', width=width, position=0, label='Other', rot=0)
male_destinations.plot(kind='bar',  color='#0F7CFE', width=width, position=1, label='Male', rot=0)
female_destinations.plot(kind='bar', color='#00FEB8', width=width, position=2, label='Female', rot=0)

plt.legend()
plt.xlabel('Destination Country')
plt.ylabel('Percentage')

sns.despine()
plt.show()
{% endhighlight %}

And we get the following visualization. Not very illuminating...  
![img]({{ site.baseurl }}/assets/assignment-4/gender_country_plot.png)


## Brainstorm Data Collection Strategy
For our project, Diego (Pinochet) and I still need to discuss what is the best idea going forward. That said, 
we have thought about potential data collection strategies for each idea to evaluate their feasibility.  
  
#### Idea 1: Generating Images of Fake Architecture
For this project, we would need to collect a large number of images from the Internet. Scraping architecture websites 
for images and metadata (e.g. building type, architect, etc.) is probably the best way to go about it. To do so, we 
can use BeautifulSoup. This might take a while however, because we will probably need to collect hundreds of 
thousands of images. There's also an important risk of collecting a lot of junk.

#### Idea 2: Lego Recipe
There's a Lego dataset on Kaggle, and that's probably what we would use. We haven't looked in detail at the dataset, so 
we'll have to confirm the data is usable.

#### Idea 3: Skecth to 3D Geometry by Adapting Sketch-RNN and 3D GANs
This one's also fairly easy. The 3D GAN model and data is available, and so is the Quick Draw! dataset used to build 
Sketch-RNN.







