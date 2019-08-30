---
title: The Machine Learning Series - Regression
date: 2019-08-14 19:30:50
---
### Week 0 LA

### Week 1 decision Theory: Regression


### Week 2: Regression
#### Linear Regression

Suppose,
y = f(x) which means we predict y given x,
the relation between y and x is said to be linear if the expectation of y given x is linear.
E[y|x] is linear.

To make the equation more fancy we can write,

y = f(x)= $$\beta_0 + \sum_{j=1}^{P} X_j \beta_0$$

where P is the total dimensions, $$\beta_0$$ is coefficient.

Now, the important thing to note is that linear regression is not as weak as it is projected to be. The reason is X in the above equation can be anything like $$X_1^2, X_2^2, ...,X_P^2$$ or it can be a combination like $$X_1X_2, X_2,X_3,..$$ and so on. So, Linear Regression actually works well for quite a good number of problems if tuned properly.

#### Training Data


#### Least Squares
