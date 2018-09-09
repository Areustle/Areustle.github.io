---
layout: post
title: "Probability for Deep Learning"
date: "2018-09-02 16:16:10 -0400"
---


Machine learning algorithms are each complicated and diverse, but many
successful ones follow the general pattern outlined in [Goodfellow et
al.][1] of improving their results by reducing a cost function that
quantifies how wrong a given result is. In our search for generalized
machine learning algorithms we need also find generalized cost functions.
One highly successful method of generating useful generalized cost
functions for a given model is by employing the principle of maximum
likelihood. Before we explain that concept however, we must build up our
understanding of underlying concept of the KL Divergence. Then we can
compare Maximum Likelihood to its Bayesian alternative, the Maximum
A Posteriori estimation.

# Conditional Probabilities and Bayes' Law

![Puppy or Muffin](
https://image.slidesharecdn.com/gdgdcdevfest-googlemachinelearningapis-puppiesormuffins-160924211716/95/machine-learning-with-google-machine-learning-apis-puppy-or-muffin-38-638.jpg?cb=1474752243
)

The above image is a mosaic of smaller pictures containing either a small
dog or a breakfast snack. It is not immediately clear which is which even
to a human eye. However let us now add a bit information that some of
pictures contain fur. Given an image contains fur, it more
probabilistically contains a dog.

This conditional probability may be expressed as $$P(d\vert f)$$: the
probability that an image is of a dog given the image contains fur. Not
all dogs have fur, and not all furry things are dogs, but we're dealing
with probabilities rather than certainties. One may similarly ask about
the probability that the image is both furry and of a dog. This *joint
probability* is notated as $$P(d,f) = P(d\cap f)$$. Logically the
conditional probability is equivalent to the joint probability in the
situation where the condition is always true. The probability of dog given
fur must be equal to the probability of dog and fur restricted just to the
world of furry things. This is simply a restatement of conditional
probability. As such it is the basis of our definition. Restricting to the
world of furry things requires ignoring the rest of reality. Assuming we
know how probable furriness ($$P(f)$$) is we then normalize our joint
probability for this $$P(f)$$, i.e. divide. As such our definition for
conditional probability is $$ P(d\vert f) = \frac{P(d,f)}{P(f)} $$.


# Maximum Likelihood Estimators

Imagine we have a hidden box holding 3 pets. We know ahead of time that
the 3 pets are composed of dogs and/or cats. So we reasonably conclude
that the number of dogs in the box is in the range $$[0,3]$$. Let's call the
number of dogs in the box $$\theta\in[0,3]$$. How might we determine
a reasonable number for $$\theta$$? We don't know how to make
a sophisticated pet classification algorithm yet, so we're just going to
play with probabilities. Assume the rules of this game are that we can
pull 1 pet out of the box and check if it's a dog or a cat, then we
return it. Since the pets are returned, each sample from the box is
independent. Let's say we do this 4 times and observe the sequence
x=[dog,cat,dog,dog]. Which value of $$\theta$$ best explains this
observation?

The observation is set in stone, so we need a function of our parameter
$$\theta$$ that measures how likely that observation was. We can calculate the
joint probability of the observation as the product of the probabilities of
each sample under a particular value for the parameter $$\theta$$. This is
simply a joint probability calculation for i.i.d events. Such a joint
probability is the Likelihood function. In this case
$$L(\theta)=(\frac{\theta}{3})^3(1-\frac{\theta}{3})$$, since 3 of our 4 pulls
were a dog and the remaining one wasn't. For each of the 4 possible values of
$$\theta$$ we would then calculate the likelihood of seeing this observation
given our parameter as: 

| $$\theta$$ | $$L(\theta) = P(\vec{x};\theta)$$ | 
| :--------- | -------: |
| 0| 0 |
| 1| 0.0247 |
| 2| 0.0988 |
| 3| 0 |

In general we can define this likelihood function as a mapping 
$$\theta\mapsto P(\vec{x})$$ where $$\vec{x}$$ is the vector of 
samples. 

$$ L(\theta)=\prod_{i=1}^{n}{ P(\vec{x}^{(i)};\theta) } $$

If 


[1]:http://www.deeplearningbook.org/
