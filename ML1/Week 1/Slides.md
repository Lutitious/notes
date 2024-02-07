## Machine Learning
- Google search
- Google translate
- Spam filtering
- Speech recognition (eg, Amazon Alexa)
- Text prediction
- Amazon product recommendations

What is Machine Learning?
- The field of study that gives computers the ability to learn from data without being explicitly programmed.
![[Pasted image 20240123134739.png]]

## 3 types of machine learning
- Supervised learning
- Unsupervised learning
- Reinforcement learning
### Supervised learning
Successful machine learning requires 3 components:
1. Representative training data: machine learning model learns from data
	- Data should be annotated (refers to meaningful labels associated with the data) contains the right answers
2. Sophisticated methods for extracting features that are used to represent the data
	- Features: easily observable properties of the data based on which the model will try to make predictions
3. Sophisticated choice of machine learning algorithm
 
- Start from a training set that contains the right answers $y_i$ :
$$D = {\{(x_i , y_i )\}}^{N}_{i =1}$$
- Training set is a set of input features $x_i$ , and outputs $y_i$ .
- The $x_i$ can each be a vector of values, the $y_i$ are single elements/classes.
- A supervised learning algorithm can study the training set and learn to classify iris
flowers into the three different classes $y_i$ based on their features $x_i$ (Goodfellow et al., 2016)
- Learns a function f that maps from features $x_i$ to a class $y_i : f(x_i) = y_i$
- Given the $x_i$ , say what the correct $y_i$ is.
- Can predict a class using a weighted combination of the input features.
- So, again, given:
$$D = {\{(x_i , y_i )\}}^{N}_{i =1}$$ be able to predict the $y_j$ of some $x_j$ .
- Particularly interested in $x_j$ such that $(xj , yj ) \not \in D$.

### Generalisation
- We want a machine learning model that performs well on new, never-before seen data.
- That is equivalent to saying we want our model to generalise well.
- Important to generalise because of the long tail.
- In many domains, some examples are very common and some are very very rare.
- Word usage.
- So you are very unlikely to have seen all examples even with a big training set.
- For example, 20% of Google searches every day are unique.
![[Pasted image 20240123140052.png]]

### Classification
- Form of supervised learning.
- Here the y are class labels:
$$y ∈ \{1, . . . , C \}$$
where C is the number of classes.
- Common version of the problem is binary classification in which C = 2.
- In this case we often write the labels as {0, 1} (eg, spam or no spam).
- In binary classification we are often thinking “is the example in the class or not”.
- If C > 2 we have multiclass classification
	(e.g, iris example or sentiment classification).

- One way to think of classification is as function approximation.
- That is, we assume that:
y = f (x)
- Learning is then trying to estimate f , creating an estimate $\stackrel {\land}{f}$.
- After learning we are predicting an estimate of y using:
$$\stackrel {\land}{y} = \stackrel {\land}{f}(x)$$