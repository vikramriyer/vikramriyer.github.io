---
title: Implement a Multilayer Perceptron from scratch using Numpy
date: 2019-01-30 19:30:50
categories: deep_learning
permalink: /:categories/neural_network_from_scratch
---

By the end of this post, I want you to have the below takeaways
- Understanding the intuition behind neural networks
- Understanding the simple math behind neural networks
- To be able to convert above understanding into code so as to solve a problem

We will look at the Bike Render ridership by analysing [Bike Share](https://github.com/vikramriyer/Neural_Network_from_Scratch_Numpy/tree/master/Bike-Sharing-Dataset) dataset made available by "Laboratory of Artificial Intelligence and Decision Support (LIAAD), University of Porto".

**Caveat**: I will not be pasting the entire code here but only the parts that I tend to explain. If you are interested in the complete project, please check this [notebook](https://github.com/vikramriyer/Neural_Network_from_Scratch_Numpy/blob/master/Your_first_neural_network_v1.ipynb) which has all the code sections and exploration of the dataset.

### Part 1: Basic Exploration and Preprocessing
A Data Science project is never complete without a basic exploration of the dataset. Another critical step in working with neural networks is preparing the data correctly. Variables on different scales make it difficult for the network to efficiently learn the correct weights.

Let me mention again that you should definitely understand this part of the project and I urge you to go checkout the notebook which has all the relevant code and explanation as well. Lets go to the next section now.

### Part 2: Build the Network
Below is a visual representation of how our network will look. We will emulate this in our code.

![Network](/assets/images/deep_learning/implement_multilayer_perceptron_from_scratch/neural_network.png)

Here are some observations based on the network.
- The network has two layers including the hidden and the output layer. The input layer is actually not considered a layer.
- To add non-linearities, we will use the sigmoid activation function at the hidden layer.
- We will pass some values from the input layer, which is to the left most in the above image and work it through to the last layer of the neural network. This process is called ___forward propogation___.
- At the output layer, we will calculate how well our values predicted the outcome we wanted. Then, we send the errors calculated in the output layer back through the hidden layer. This is probably the most important part which actually helps the network ___LEARN___. This is also called ___backpropagation___.

#### Neural Network Class

{% gist ab66247028390dee7b550cae241b321f %}

Lets see a few things about the class above
- First we set the input, output and hidden nodes. These are going to be some values depending on how many nodes we plan to use in out network. The input nodes should match our input and output should match the type of output we are expecting. In this case, a regression problem and hence a single node. However, the hidden layer can have as many nodes as you want.
> So, is there a number that will work universally? Absolutely not! This is usually found out by repetitively setting values and measuring the network performance.

#### Forward Pass

{% gist ac6639c6236748338f19baca6098d8eb %}

#### Backward Propogation

{% gist ab6a1ea5ec4afcda13e9eeeeda6013cc %}

### Part 3: Math
... in progress

### Part 4: Intuition
--- in progress

## References
