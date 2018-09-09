---
layout: post
title: "828L Lecture 4: Deep Learning"
date: "2018-09-06 12:23:12 -0400"
---

Office hours (starting Tuesday)
Monday: 5-6 (J)
Tuesday: 3-4 (D)
Wednesday: 10-11(D), 5-6 (J)
Thursday: 4-6 (Cz)


Last time we discussed Linear approaches to machine learning.  Support vector
machines were discussed for data that is linearly separable.

# Support Vector Machine's (SVMs)

The Perceptron and SVM both generate a hyperplane that divides the data between
2 parts, separating the classes. This hyperplane is defined by the equation
$$w^Tx=0$$ where $$0$$ defines the plane, positive values are in class 1, and
negative values are in class 2. Perceptrons find any hyperplane that separates
the data. SVMs find the maximum margin between the closest points in each
class.

As an equation this is expressed as: $$a^Tx_i >1$$ where $$x_i$$ is a training
element multiplied by the label so we can simplify the constraints. The margin
is at least as big as $$\gt \frac{1}{\lVert a \rVert}$$. The values are
required to be greater in magnitude than 1 because less than 1 is within the
margin.  The value $$a^Ty$$ doesn't really tell us how far from the hyper plane
the point is. If we scale the inequality by the $$\frac{1}{\lVert a \rVert}$$.
The goal then is to $$\min{\lVert a \rVert} $$, which maximizes the margin.
This is a nice convex optimization problem with one global minimizer.

The points closest to the hyperplane are called the support vectors. You can
think of the support vectors as what define the max margin separator. The other
points could be eliminated and the hyperplane would remain static. This is an
example of how the SVM is better than the perceptron, which is sensitive to
fluctuations in the data. However the SVM is sensitive to outliers. To prevent
outlier sensitivity we can add a regularization term such that we're minimizing
$$\min_{a,\psi_i}\frac{1}{2}\lVert a\rVert^2 + C\sum_i\psi_i$$ such that $$a^Ty
\ge 1 - \psi_i,\quad\psi_i\ge0$$. This adds a non-infinite cost to
mischaracterizing outlier points, we call it the hinge loss because it's 0 if
classified correctly and has a slope of $$-C$$ otherwise, hinging at the
hyperplane. SVMs historically are well applied to finding linear separators in
higher dimensional spaces.

SVMs were super popular until NNs ate their lunch; they still do really well on
problems with small datasets.


# Neural Networks (NNs)

The most straightforward variant of a Neural Network is a *Fully-Connected*,
*Feed-Forward* network. It is an acyclic graph of "units" or "nodes" organized
in layers where each layer is connected to every unit of the previous layer,
and every unit of the next layer. The input layer is layer 0, while the output
layer is layer $$L$$. A small $$\ell$$ denotes a given layer, while the
activation of a unit is $$a$$. The activation of the jth unit in the lth layer
is $$a_j^l$$.  The weight from unit $$k$$ in layer $$l-1$$ to unit $$j$$ in
layer $$l$$ is $$W_{j, k}^\ell$$. The Activation $$a_j^\ell =
\sigma(\sum_kw_k^\ell a_k^{\ell-1} + b_j^\ell)$$ We often separate the inner
term out and write $$z_j^\ell = (\sum_kw_k^\ell a_k^{\ell-1} + b_j^\ell)$$ so
the equation becomes $$a_j^\ell = \sigma(z_j^l)$$.

Our function $$\sigma(z)$$ is a non-linearity and we have many options. The
most widely used is ReLU where $$\sigma(z)=\max(0,z)$$. Other ones which have
fallen out of fashion include the sigmoid $$\sigma(z) = \frac{1}{1+e^{-z}}$$.
A final one is Tanh where $$2\text{sigmoid}(2z)-1$$. It looks like sigmoid but
is symmetric around $$z=0$$. Goodfellow et al. point out that the discussion of
a best non-linearity is often a waste of time for practitioners as even
theoretically suboptimal functions can give good results in practice. They
mention training a network with $$\cos$$ and getting good results.

ReLU allows you to consider the layers as computing a piecewise linear
function. It may similarly be considered as manipulating the space in which the
data lies so that it becomes linearly separable.

In the Weight matrix the ith row is all the weights going from all the
activation in the previous layer to the ith unit in the output layer.
