---
layout: post
title:  "Assignment 7"
date:   2018-10-27 23:27:01 -0400
categories: assignments
---

## 1. Understanding LSTMs
### 1.
The information passed between modules is the old cell state and the output of the previous cell.
### 2.
RNN are theoretically capable of remembering past long-term information, but, in practice, it seems they can only recall 
recent information to perform future predictions. The key architecture change in LSTMs, which allow for long-term
dependencies, is the cell state. The cell state is passed along the entire recurrent chain of modules and is modified only by 
linear operations, allowing information to flow through more easily
### 3. 
The modules share the same weights (W<sub>f</sub>, W<sub>f</sub>, ..., and bias terms), but they don't share the same input (cell state, 
current information, etc.).
### 4.
A pure forget gate would be a binary 0/1 function. That's not great because it isn't continuous, and it has zero gradient 
everywhere. A better alternative is to use the sigmoid function: it squashes all values to the `[0,1]` interval and is 
continuously differentiable. In contrast, ReLu activation output values that are only bounded from below.

## 2. Text Generation
### 2.1. Complete the Code
{% highlight javascript %}
        var forwardIndex = function (G, model, ix, prev) {
            var x = G.rowPluck(model['Wil'], ix);
            if (generator === 'rnn') {
                // RNN
                var out_struct = R.forwardRNN(G, model, hidden_sizes, x, prev);
            } else {
                // LSTM
                var out_struct = R.forwardLSTM(G, model, hidden_sizes, x, prev);
            }
            return out_struct;
        }
{% endhighlight %}
### 2.2. Run the Model
1. After 10.79 epochs, the model reached a perplexity of 4.12.
2. Here are a few sentences obtained with a temperature of 0.49:
* I don't like all and the love it on the Gut beed you
* I want the will stand, and the out and the ready the while the were I'm the stand you could you and t
* I will stand stand to the down to the were come and you do
* I don't it all the are and he would me of it love in the storsted that ever go the deed and I will al
* When the like it hels the pown you more the wiside here in the all the mand that on that me a couse t  
These obviously make no sense, but they're still the best I could get. For higher temperatures, the sentences would use too many of 
the same words, and, for lower temperatures, the model would ouput even less meaningful sentences with many spelling mistakes.
3. It is hard to extract any meaning out of the sentences. That said, many of the generated sentences I observed often included the word 
`Love`, which is probably a feature extracted from Adele's song. In addition, many of the sentences 
seem to oppose `you` and `I`, which would also come from Adele's song. 
Finally, a large proportion of the sentences would start with `I`, like many of the lines in the Dr. Seuss text.

## 3. Music Generation with TensorFlow
1. [Completed Notebook](https://github.com/danhaive/6s198/blob/master/assets/assignment-8/Music Generation with RNNs.ipynb)
2. 
* [Generated Song with Training](https://github.com/danhaive/6s198/blob/master/assets/assignment-8/gen_song_learnt.mid)
* 2. [Generated Song without Training](https://github.com/danhaive/6s198/blob/master/assets/assignment-8/gen_song_notlearnt.mid)