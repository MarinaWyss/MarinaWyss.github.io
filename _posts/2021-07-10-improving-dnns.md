---
title: "Improving Neural Networks"
date: 2021-07-10
tags: [deep learning]
excerpt: "Addressing bias and variance in deep learning models."
mathjax: "true"
---
I’ve recently been working on a couple of deep learning projects -- one on predicting poverty from satellite imagery as part of a volunteer effort with DataKind, and one on text classification for my job at Coursera -- and I wanted to revisit ways to improve neural networks. Much of the content below is summarized from the deeplearning.ai Deep Learning Specialization I took last winter, supplemented with additional research. 

## Addressing Bias

Assuming the problem at hand is a solvable problem, it should always be possible to perform well on the training dataset. 

If a model has high bias, this can be addressed by:

* Properly scaling the data and initializing the parameters.
* Choosing the right kind of neural network.
* Improving the network architecture (i.e. using a bigger network).
* Training longer.
* Choosing appropriate activation functions, optimization algorithms, and hyperparameters.
* Trying an ensemble method.


### Data Scaling

The scale and distribution of input data may vary for each feature. If the input values are large, this can result in a model that learns large weights. Often models with large weights are unstable, have poor performance during learning, and are sensitive to input values so unable to successfully generalize. Similarly, having a target with a large value spread can result in a large error gradient and therefore large fluctuations in the weights, making learning unstable. 

Generally, input variables should be small values (ideally between 0-1) with a limited distribution (standard deviation around 1). If the input data is normally distributed then standardization is a good approach (subtract the mean and divide by the standard deviation). Alternatively, the data can be normalized (rescaled from 0-1).

The output variable should match the scale of the activation function of the output layer.


### Target Distribution

I have learned that hard way that having a very different distribution for the train and test targets can be a major issue. This can be a particularly pronounced problem when limited data is available (so there isn't enough for a robust train-val-test split).

Rather than simply shuffling the dataset, a better alternative is to make the val/test sets come from the target distribution dataset, and the training set from the full dataset. This allows one to optimize for the target distribution, which is what we ultimately care about. See [this blog](https://www.freecodecamp.org/news/what-to-do-when-your-training-and-testing-data-come-from-different-distributions-d89674c6ecd8/) for a good summary of the problem.


### Parameter Initialization

If the weights are initialized to all zeros, this results in the network “failing to break symmetry.”  Basically, every neuron in each layer learns the same thing. Note that the bias can actually be safely initialized to zero.

So, rather than uniform initialization, weights are typically randomly initialized to small values (which keeps the cost lower). There are some considerations for how to properly randomly initialize weights, since weights that are too high or low can lead to the problems of “exploding” or “vanishing” gradients. 

Backpropogated gradients are either increased or minimized during each iteration of training; if weights are initialized too high, the gradient of the cost with respect to the parameters will also be high. This leads the cost to oscillate around its minimum value. If the weights are initialized too low, [“the gradients of the cost with respect to the parameters are too small, leading to convergence of the cost before it has reached the minimum value.”](https://www.deeplearning.ai/ai-notes/initialization/)

To avoid exploding or vanishing gradients, the mean of the activations should be zero, and the variance of the activations should be the same across every layer.

There are a few special methods for initializing weights that address these concerns. Specifically, Xavier initialization is often preferred for Sigmoid and Tanh, while He is favored for ReLU.


### Type of Neural Network

Some kinds of neural networks perform better on certain tasks than others. As a simple run-down:

* MLP: Simple tasks
* CNN: Images, video, and text classification
* RNN: Data in a sequence (like translations or audio)
* GNN: Problems that can be conceptualized as a graph (that's a lot of things!)
* Autoencoder: Data compression

[This blog](https://towardsdatascience.com/the-mostly-complete-chart-of-neural-networks-explained-3fb6f2367464) has a lot of detail about a good number of different potential neural network types.

In the future, I plan to do a more in-depth blog on CNNs for computer vision and sequence models.


### Network Architecture

The number of hidden layers and the number of neurons per hidden layer are both tunable hyperparameters. While there are some heuristics for determining *a priori* the best number of hidden layers and neurons, the most common approach is to start by finding a well-performing architecture for a similar problem, and trying some variations. Generally it is better to start simple, and compare the performance of more complicated variations. 

Note that network architecture interacts with the other hyperparameters of the network, and changes in one may affect the optimal values for another. 


### Activation Functions

I covered some common activation functions and use cases a while back. See [this blog!](https://marinawyss.github.io/activation_functions/)


### Optimization Algorithms

Optimizers are algorithms that change the attributes of the neural network (such as the weights and learning rate) to reduce the loss. Gradient descent is the OG, but there are several other options that often perform better. Below is a quick, barely-scratching-the-surface summary of the intuition behind several of the most important ones.

I covered basic *gradient descent* in [this blog](https://marinawyss.github.io/calculus/). It’s advantages are being easy to compute, implement, and understand. However, it’s prone to getting trapped in local minima, and can take a long time to converge since the weights are only updated after calculating the gradient on the entire dataset (which is also memory-intensive).

*Stochastic gradient descent* is a variation on gradient descent that updates the model parameters more frequently (after the computation of the loss on each individual training example). This allows for faster convergence, less memory use, and potentially finding new minima. On the flip side, stochastic gradient descent has high variance in model parameters, and may overshoot after finding the global minima.

*Mini-batch gradient descent* improves on both of the above by updating model parameters after a “batch” of training examples. This allows for frequently updating the model parameters while maintaining lower variance, while requiring a medium amount of memory.

*Momentum* improves on stochastic gradient descent by reducing the variance, spending up convergence towards the minima, and reduces fluctuations away from the minima. It does this by accumulating an exponentially-decaying moving average of past gradients to continue to move in their direction.

*RMSProp* is another option to speed up horizontal learning and slow down vertical learning. It’s essentially the same idea as momentum, except we take a square before the exponentially-weighted average, and update the parameters slightly differently.

*Adam* uses a combination of momentum, RMSProp, and bias correction, and is everyone’s favorite optimizer right now. It converges quickly, but is computationally costly.


## Additional Hyperparameters

There are several additional hyperparameters for neural networks, many of which vary by the type of network chosen. Some consistently important ones include:

* The learning rate $$alpha$$: If the learning rate is too large, gradient descent will overshoot the minima and diverge. However, if it is too small, the algorithm will require too many epochs to converge and can become trapped in local minima more easily. A few notes on how best to tune the learning rate include:
	- Experiment with very large and very small learning rates.
	- Grid search common learning rate values from the literature and see how far you can push the network.
	- Try a learning rate that decreases over epochs.
	- Try a learning rate that drops every fixed number of epochs by a percentage.
	- Try adding a momentum term then grid search learning rate and momentum together.
	- Larger networks need more training, and the reverse. If you add more neurons or more layers, increase your learning rate.
* Momentum: This can default to 0.9 as a starting point for smaller datasets, and progressively get closer to one with larger datasets.
* Betas and epsilon for the Adam optimizer: Defaults are 0.9, 0.999, 10^-8, and are rarely tuned.
* Learning rate decay: With learning rate decay, the learning rate is calculated each update (e.g. end of each mini-batch), which improves optimization and generalization. 
* Batch size: If we have a large data-set, we can split up the training into mini-batches. The batch size therefore defines how often the weights are updated. The choice of batch size depends on the training size; for smaller datasets (< 2k training examples), skip mini-batches all together. For larger datasets, try a grid-search of different mini-batch sizes in multiples of 8.


### Ensemble Methods

If individual neural networks are not performing well, it is possible to train different neural network architectures on different parts of the data, and combine their predictions to enhance predictive power.


## Addressing Variance

If a model is performing well on the training set, but poorly on the validation set, this overfitting can be addressed with a few techniques:

* L1 and L2 regularization
* Dropout
* Data augmentation
* Early stopping


### L1 and L2 Regularization

Regularization reduces overfitting by adding a penalty term that constrains the size of the weights in neural networks, or the coefficients in regression. In both cases, regularization discourages building an overly flexible or complex model.

L1 and L2 regularization are two of the most common regularization approaches, and both rely on adding a penalty term (lambda) to the cost function. Note that lambda is a hyperparameter that is tuned using cross-validation, with higher values associated with a stronger regularization effect.

The assumption is that a model with small weights is simpler than a model with large weights. The goal with regularization is to minimize the loss function plus the penalty term, which minimizes overfitting since a large lambda reduces the impact of some of the hidden units. Essentially it becomes too costly to have large weights, leading to a smoother model in which the output changes more slowly as the input changes. 

While L1 and L2 regularization are similar, L2 regularization pushes weights almost, but not quite to zero, while L1 regularization may push the weights completely to zero. This can be useful when trying to “compress” (limit the number of parameters in) a model.


### Dropout

Dropout is a common regularization technique in deep learning, and essentially just means randomly selecting some nodes to remove (along with their incoming and outgoing connections) in each layer. The network is otherwise trained normally. On the next iteration, a new set of nodes are randomly selected for removal. The percentage of neurons to drop on each iteration is a tunable hyperparameter.

Using dropout means that the model trained is slightly different on each iteration, making the weights reluctant to put too much emphasis on any single input. The weights spread out, and thus reduce overfitting.

Note that a challenge with using dropout is that the cost function is no longer well-defined, which is a problem when plotting the loss for debugging. However, this is easily fixed by temporarily removing dropout for debugging.


### Batch Normalization

Training deep neural networks with many layers is challenging, since they can be sensitive to the initial random weights and configuration of the learning algorithm. One reason is that the distribution of the inputs to layers deep in the network may change after each mini-batch when the weights are updated, so the learning algorithm is forever chasing a moving target (called "internal covariate shift").

Normalization is, of course, the process of transforming the data to have mean zero and standard deviation of one. With batch normalization, the inputs of each layer are standardized for each mini-batch right before or after the non-linear activation function, which stabilizes the learning process and leads to faster training and less over-fitting.


### Data Augmentation

If it was easy to just get more data, that would be an obvious solution. But this is, of course, rarely cheap or quick to do. However, there are often simple ways to artificially increase the training dataset size by artificially generating new data from what is already available. For example, rotating/flipping/cropping images, adding blur, or adding text translations.


### Early Stopping

Early stopping simply means stopping training when the performance on the validation data set starts to get worse.

The course I took recommended against this approach since it does not allow one to focus on one task at a time: In this case, first minimize loss, then address overfitting. Early stopping means that we may never properly minimize the loss. Therefore, other regularization techniques may be preferable.
