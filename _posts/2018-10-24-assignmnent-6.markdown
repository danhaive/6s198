---
layout: post
title:  "Assignment 6"
date:   2018-10-24 23:27:01 -0400
categories: assignments
---

## 1. Reward Scheme
Each run ends when you reach the goal or fall in the freezing water. According to the doc, you receive 1 point when you
reach the goal, and zero otherwise.

## 2. Q-Learning Table Size
There are `4` possible actions (U, R, D, L), and `2*2*2*2=16` possible states. As a result, the table size is `4*16=64`.

## 3. Reward Improvement
The reward improves, or, more precisely, the frequency of success increases quickly as seen in the plot of the reward for the 
2000 games played (the two possible outcomes being 0 and 1).
This was expected given Q-learning maximizes a proxy of the probability of successfully reaching the goal.
![img]({{ site.baseurl }}/assets/assignment-7/success-frequency.png)

## 4. Effect of Hyperparameters
To understand the effect of the learning rate and discount factor on the performance factor, I plotted the ratio of successful games for 
each learning run of 2000 games and different values of hyperparameters. 
This indicates that the discount factor influences performance the most dramatically. 
The response surface is somewhat noisy because of the inherent randomness of each run, 
and the result was obtained for a single training run for each set of hyperparameters instead of being averaged over many runs.

{% include_relative plots/assignment6-plot.html %}