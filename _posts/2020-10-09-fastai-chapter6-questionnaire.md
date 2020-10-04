---
title: Fastbook (FastAI) Chapter 6 Questionnaire
date: 2020-10-09 19:30:50
comments: true
share: true
related: true
excerpt: Computer Vision Problems
categories: machine_learning
---

## QnA

### Q1. How could multi-label classification improve the usability of the bear classifier?

__A.__

By adding another class which is _not a bear_, we can now accommodate the condition where the user inputs anything other than a bear. The classifier earlier strictly predicted that the input image was one of the bear types. With the multi-label classifier, this need not be the case. The classifier is free to say that the image belongs to all the classes, or a few or none.

### Q2. How do we encode the dependent variable in a multi-label classification problem?

__A.__

One-hot encoding.

So, if there are a total of 3 labels in the dataset and a certain image is first 2 of those, we can treat the dependent variable as a vector of 0's and 1's where the value is 1 for the original labels and 0 otherwise.

|label1|label2|label3|
|:---:|:---:|:---:|
|0|1|1|
|1|0|0|

In tha above example, we can say that, the first row data belongs to both label2 and label3, the second one belongs to label1.

### Q3. How do you access the rows and columns of a DataFrame as if it were a matrix?

__A.__

Suppose we have the below data and each row is a person.

```python
>>> df = pd.DataFrame({'age': [10, 20, 30],
                       'weight': [35, 65, 85]})
>>> df
    age  weight
0   10      35
1   20      65
2   30      85
```

So, if we want to find out the weight of 2nd (index starting at 0) person, we can write,

```python
>>> df.iloc[2,1]
85
```

### Q4. How do you get a column by name from a DataFrame?

__A.__

For the same example from above,

```python
>>> df = pd.DataFrame({'age': [10, 20, 30],
                       'weight': [35, 65, 85]})
>>> df
    age  weight
0   10      35
1   20      65
2   30      85
```

So, if we want to get the age column, we can write,

```python
>>> df['age']
0    10
1    20
2    30
Name: age, dtype: int64
```

### Q5. What is the difference between a Dataset and DataLoader?

__A.__

__Dataset__: Vanilla object that has entire dataset bundled into a tuple of dependent and independent variables.

Ex: Let's take an example of house prices.

|Index|#bedrooms|#bathrooms|price|
|:---:|:---:|:---:|:---:|
|0| 2| 2| 100|
|1| 2| 1| 80|
|2| 3| 2| 120|
|3| 1| 1| 50|

In the above example (think dataset), `Index` is just a serial number and can be ignored. `#bedrooms` and `#bathrooms` are independent variables, price is a dependent variable.

So the `Dataset` object will have _(dependent_vars, independent_vars)_.

__DataLoader__: Advanced object that is an iterator on top of `Dataset` object that streams over mini-batches.

Why mini-batches? <br>
There are many reasons but we will go over the 2 important ones.
1. Suppose your RAM can fit only 2 data points from the entire dataset that has 4 data points. Then this mini-batch can have 2 data points at a time, train and move on to the next batch which has the remaining 2 data points.
2. If we use Gradient Descent for training (which we will for sure), we might not want to take in the entire dataset into memory; for reason "#1" and also for the reason that we want updates to our model after training on a smaller batch than after going through all the examples. Hence, it is always a practice to use mini-batches. A more detailed explanation is not in the scope of this answer, but using mini-batches improves the performance of our model.

So, DataLoader provides us this mini-batch which we can use for training.

__Note__: This question addresses `Dataset` and `DataLoader` not `Datasets` and `DataLoaders`. Notice the `s` (plural) at the end.

### Q6. What does a Datasets object normally contain?

__A.__

Once you have understood `Dataset` and `DataLoader` from question "#5", we can answer this question.

Datasets object is an iterator that has the _training_ Dataset and _validation_ Dataset.

Well, what's the difference? <br>

A: We can have a `Dataset` object for training data and a separate `Dataset` object for validation data. A `Datasets` object is an iterator over both traind and validation Dataset.

### Q7. What does a DataLoaders object normally contain?

__A.__

Once you have understood Dataset and DataLoader from question "#5", we can answer this question.

DataLoaders object is an iterator that has the training DataLoader and validation DataLoader.

Well what's the difference? <br>

A: Similar to how `Datasets` works over both training and validation `Dataset` `DataLoaders` is a wrapper around both _validation_ and _train_ `DataLoader`.

### Q8. What does lambda do in Python?

__A.__

Suppose we do not want to reuse a function and just use it on the run, this is where we use a lambda function.

Example:

Suppose we have a DataFrame where one of the columns named "sentiment" is a string value. The problem is that there is no case-consistency in the values. For example, the values are _Happy, HAPPY, happy, HaPpY,_ etc and we want all these values to be the same. A useful thing would be to convert these to one form i.e. happy. How do we do this?

We can use a pandas apply method and write the code like so.

```python
def make_sentiment_lower_case(x):
    return x.lower()

df['sentiment'] = df['sentiment'].apply(make_sentiment_lower_case)
```

This is a possible approach but we won't be using the method later so what's the point of creating it? Can we be innovative such that we use something on the run and not store it?

Here is how you do it with lambda.

```python
df['sentiment'] = df['sentiment'].apply(lambda x: x.lower())
```

__Legit argument__: So what if we create one extra method, would it hurt us?

Absolutely not! It's just a choice, use it or leave it. However, IMO it's handy and aesthetic to a developer.

### Q9. What are the methods to customize how the independent and dependent variables are created with the data block API?

__A.__

__get_x()__ gets all the independent variables

__get_y()__ gets all the dependent variables

### Q10. Why is softmax not an appropriate output activation function when using a one-hot-encoded target?

__A.__

The softmax function goes something like this, when implemented in numpy,

```python
>>> def softmax(x):
>>>  exp_x = np.exp(x - np.max(x))
>>>  return list(exp_x / np.sum(exp_x))

>>> x = [1, 4, 5, 2, 9]
>>> softmax(x)
[0.0003268657544188321,
 0.006565274179305044,
 0.01784626550045627,
 0.0008885132405822681,
 0.9743730813252376]
```

If noticed carefully, the sum of the output of softmax sums up to 1.

```python
>>> sum(softmax(x))
1.0
```

So what does the `softmax` do anyway? <br>
It helps scale the values between 0 and 1 such that the largest value is closer to 1 and smallest value is closest to 0. Finally, in practice, we choose the largest value from the result to decide the prediction label.

Now, this means, the softmax assumes that the labels are mutually exclusive i.e. it tries to push one value to be greater than the others. With, multi-label classification this is not the case. The labels might not be interdependent at all. In practice, for such problems it might not be a wise choice to squish the values to 1 because of the independence between the labels.

__Final Note__: Using a softmax will not give errors. It might just be bad choice for the problem due to its assumption of dependence between the values.


### Q11. Why is nll_loss not an appropriate loss function when using a one-hot-encoded target?

__A.__

The `nll_loss` returns a single value for the input and hence is absolutely not useful in case of multi-laebl classification.

### Q12. What is the difference between nn.BCELoss and nn.BCEWithLogitsLoss?

__A.__

In PyTorch, _BCELoss_ calculates the cross entropy without the sigmoid whereas _BCEWithLogitsLoss_ does the does both sigmoid and binary cross entropy in a single function.

### Q13. Why can’t we use regular accuracy in a multi-label problem?

__A.__

With regular accuracy, we pick the value that is largest based on the scaled values between 0 and 1 after applying the sigmoid.

However, in case of multiple labels, there can be multiple values that we would like to pick.

For example, if the output of the sigmoid was [0.6, 0.1, 0.3], for regular accuracy, we would have picked, 0.6 as the final output. However, in case of multiple labels, we might have to end up picking none or all three of the above. We will depend on a threshold to decide which of the above outputs (labels) to pick. Hence, the regular accuracy will not work.

Put simply, we might do something like, (this is not the exact code, just a gist)
```python
>>> max(outputs) # for regular accuracy
>>> [x for x in outputs if x>=threshold] # for multi label accuracy
```

### Q14. When is it OK to tune a hyperparameter on the validation set?

__A.__

If tuning hyperparameters gives us smooth curves, it is ok to tune them further. What does a smooth curve mean? It means that there are no abrupt changes in the performance of the model due to changes in the hyperparameters, the performance is measured by any metric that suits the problem like accuracy.

### Q15. How is `y_range` implemented in fastai? (See if you can implement it yourself and test it without peeking!)

__A.__

`y_range` is implemented by using the sigmoid function. The `y_range` does the scaling on the sigmoid to squash the values between values provided as upper and lower bounds like shown below. Check the docstring below to know what each parameter is.

```python
def sigmoid_range(x, hi, lo):
  """
  Squashes (rescales) the values of x between hi and lo. Imagine a min-max scaler.

  :param: x: this is the input
  :param: hi: upper bound of the value
  :param: lo: lower bound of the value
  """
  return torch.sigmoid(x) * (hi-lo) + lo

```

Example usage: For point coordinates, the values are always in the range `-1 to 1`. So, we can pass `y_range=(-1, 1)` when instantiating the learner which says that the predictions should be in the same range.

```python
learn = some_learner(dataloader_obj,
                     pretrained_model_obj,
                     y_range=(-1,1))

```

### Q16. What is a regression problem? What loss function should you use for such a problem?

__A.__

A problem where the model is expected to output continuous numbers. For example, predicting price of a mobile phone based on other similar phones that have the same features like: battery life, quality of the cameras, RAM, GPU card, etc

In regression problems, we predict/output a number as opposed to a category in classification.

### Q17. What do you need to do to make sure the fastai library applies the same data augmentation to your input images and your target point coordinates?

__A.__

When passing the individual blocks to the `DataBlock` API, make sure to mention the last block to be a `PointBlock`.

Example code,

```python
blk = DataBlock(
    blocks=(ImageBlock, PointBlock),
    ...
)

```

## References
- [FastAI course](https://course.fast.ai/videos/?lesson=6)
- [Fastbook Free](https://github.com/fastai/fastbook/blob/master/06_multicat.ipynb)
- [Fastbook OReilly](https://www.oreilly.com/library/view/deep-learning-for/9781492045519/)
- [AiQuizzes](http://aiquizzes.com/)
