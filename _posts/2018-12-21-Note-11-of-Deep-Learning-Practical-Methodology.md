---
layout: post
title: Note 10 of Deep Learning: Practical Methodology
categories: [Machine Learning, Deep Learning]
---
<script type="text/x-mathjax-config">MathJax.Hub.Config({tex2jax: {inlineMath:[['$','$']]}});</script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=default' async></script>

Practical design process:
1. Determine goals: what error metric to use and corresponding target value, both of which should be driven by the problem that the application is intended to solve.
2. Establish a working end-to-end pipeline and the estimation of the appropriate performance metrics as soon as possible.
3. Instrument the system well to determine bottlenecks, diagnose which parts are performing worse than expected and whether it is due to overfitting, underfitting or a defect in the data or implementation.
4. Based on specific findings from instruments, repeatedly make incremental changes, including gathering new data, tuning hyperparameters, changing algorithms.

## Performance Metrics
> Need to decide both an expected target value, according to previous benchmark results or error rate that is necessary for a system to be safe, and which metrics to use such as accuracy, precision, recall, etc.

**Precision** is the fraction of detections reported by the model that were correct; **Recall** is the fraction of true events that were detected. When using them, it’s common to plot a *PR curve* with precision on the y-axis and recall on the x-axis.

We can also convert precision $p$ and recall $r$ into an $F-score$ given by

$$ F = \frac{2pr}{p+r} $$

Another metric is **Coverage** which is the fraction of examples for which the machine learning system is able to produce a response. It is useful when the system is allowed to refuse to make a decision and deliver to human to make a decision.

Many metrics are available but what is more important is to *determine which performance metric to improve ahead of time and then concentrate on that.*

## Default Baseline Models
> The goal is to establish a reasonable end-to-end system as soon as possible, which can be used as a baseline.

A reasonable choice of optimization method is **SGD** with momentum with a decaying learning rate. Popular decay schemes include 1) decaying linearly until reaching a fixed minimum learning rate; 2) decaying exponentially; 3) decreasing learning rate by a factor of 2-10 each time validation error plateaus. Another reasonable alternative is **Adam**. 

**Batch normalization** may have a dramatic effect on optimization performance, especially for convolutional networks and networks with sigmoidal nonlinearities such as *sigmod* or *tanh*. Batch normalization should be introduced if optimization is likely problematic.

Some *mild forms of regularization* should be included from the start unless the training set contains tens of millions of examples. **Early stopping** should be used universally. **Dropout** is a regularizer compatible with many models.

## Determine whether to Gather More Data
> After implementing the baseline, it’s often better to gather more data than to improve learning algorithm or try out different algorithms.

If current performance on the training set is poor, there is not reason to gather more data since the learning algorithm even doesn’t utilize the training set available so far (, which means *underfitting*). So try improve the size of the model by adding more layers or hidden units in each layer, or by tuning hyperparameters such as learning rate.

If large or fine-tuned models still do not work well, the problem may appear to be the *quality of the training data*, such as too noisy or no  useful features. So turn to collect cleaner data or richer set of features.

If performance on training set is acceptable, then measure the performance on test set. If performance on test set is still acceptable, there is nothing left to be done (or you can try improve the learning algorithm). If performance on test set is poor (, which means *overfitting*), then gathering more data is one of the most effective solutions. An alternative of gathering more data, or say, reducing overfitting, is to reduce the size of the model or improve regularization by tuning hyperparameters such as weight decay coefficients or by adding regularization strategies such as dropout, L2 regularization.

When deciding to gather more data, it’s also necessary to decide *how much data to gather*. It’s useful to plot curves showing the relationship between *training set size* (may on a logarithmic scale) and *generalization error*.

## Selecting Hyperparameters
Learning rate may be the most important hyperparameter, so if you have no enough time, tune learning rate first.

## Debugging Strategies
1. *Visualize the model in action*: when training a object detection model, view some images with predicted result superimposed on it; when training a generative model of speech, listen to some of produced speech samples, etc. Directly observing the machine learning model performing its tasks can help you estimate if the *quantitative* performance result it achieves seem reasonable.
2. *Visualize the worst mistakes*: by viewing the training set examples that are the hardest to model correctly, one can often discover problems with the way the data has been preprocessed or labeled.
3. *Reasoning about software using train and test error*: if training error is low but test error is high, the software implementation should work properly, and the model overfits (or there is an error when saving the model and then reloading for evaluation).
4. Compare back-propagated derivatives to numerical derivatives.
5. Monitor histograms of activations and gradient.