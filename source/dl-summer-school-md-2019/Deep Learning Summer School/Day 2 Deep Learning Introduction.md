---
title: Day 2 Deep Learning Introduction
date: 2019-10-07 14:32:46
---

# Introduction of DL

---

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/raw/master/presentations/day2/1_introduction_deep_learning.pdf)

## Intuition

![Day%202%20Deep%20Learning%20Introduction/Untitled.png](Day%202%20Deep%20Learning%20Introduction/Untitled.png)

Good representations are linearly separable.

## Modular approach: multi-layer perceptron

Local representations: classical

![Day%202%20Deep%20Learning%20Introduction/Untitled%201.png](Day%202%20Deep%20Learning%20Introduction/Untitled%201.png)

Pattern extractor

![Day%202%20Deep%20Learning%20Introduction/Untitled%202.png](Day%202%20Deep%20Learning%20Introduction/Untitled%202.png)

Activation function

![Day%202%20Deep%20Learning%20Introduction/Untitled%203.png](Day%202%20Deep%20Learning%20Introduction/Untitled%203.png)

Artificial neuron

![Day%202%20Deep%20Learning%20Introduction/Untitled%204.png](Day%202%20Deep%20Learning%20Introduction/Untitled%204.png)

Hidden Layer

![Day%202%20Deep%20Learning%20Introduction/Untitled%205.png](Day%202%20Deep%20Learning%20Introduction/Untitled%205.png)

Multilayer perceptron

![Day%202%20Deep%20Learning%20Introduction/Untitled%206.png](Day%202%20Deep%20Learning%20Introduction/Untitled%206.png)

### Why do we need activation functions?

- Composing many linear transformations is equivalent to a single linear
transformation.
- Theoretical results (e.g. **universal approximation theorem**) about the
representation power of deep neural networks with activation functions.
- Activation functions can **modulate** the representations.

### Diagram → Modules (模块化)

![Day%202%20Deep%20Learning%20Introduction/Untitled%207.png](Day%202%20Deep%20Learning%20Introduction/Untitled%207.png)

### Dense module: matrix-matrix multiplication

![Day%202%20Deep%20Learning%20Introduction/Untitled%208.png](Day%202%20Deep%20Learning%20Introduction/Untitled%208.png)

### Modular approach's advantages over classical diagrams

- Data and patterns are stocked in **tensors** (vector, matrix, N-d array).
- Deep learning operations are encapsulated in modules.
- A module can process examples in **parallel**.
- A module can be implemented with linear algebra operations, which are:
    - efficiently implemented in existing libraries,
    - efficiently executed on GPUs.
- We can use a modular approach to define architectures.

### Modular approach

![Day%202%20Deep%20Learning%20Introduction/Untitled%209.png](Day%202%20Deep%20Learning%20Introduction/Untitled%209.png)

- A module is an **elementary** building block that you can combine with others to create new modules.
- The output of a module is the input of another one.
- Many elementary modules are implemented efficiently in modern deep learning libraries.

### Activation Function: introduction of non-linearity

![Day%202%20Deep%20Learning%20Introduction/Untitled%2010.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2010.png)

### How to build a probabilistic model

→ Binary Classification (Cat/Not Cat)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2011.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2011.png)

- Dense Module for mapping to a single scalar
- A sigmoid module to normalize the scalar between 0 and 1

Output is the parameter *p* of a Bernoulli distribution:
- *p* is the probability of class 1
- *1-p* is the probability of class 0.

→ Multi-label Classification (**any** Cat,Car,Person,Tree,Dog,Bike in image)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2012.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2012.png)

- **Dense module** for mapping to the number of classes.
- A **sigmoid module** to normalize each component between 0
and 1.

→ multi-class Classification (**or** Cat/Car/Person/Tree/Dog/Bike in image)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2013.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2013.png)

- **Dense module** for mapping to the number of classes.
- A **softmax module** to normalize each component between 0
and 1 and to have their sum equals to 1.

**Softmax**

*In [deep learning](https://en.wikipedia.org/wiki/Deep_learning), the term **logits layer** is popularly used for the last neuron layer of neural network for classification task which produces raw prediction values as real numbers ranging from [-infinity, +infinity ]. — Wikipedia

Logits are the raw scores output by the last layer of a neural network. Before activation takes place.*

### Design patterns

- Encoder-decoder

    Learning a latent representation `z`:

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2014.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2014.png)

    e.g. **Autoencoder**, seq2seq (RNN), encoder-decoder network

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2015.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2015.png)

    - Multi-input encoder: encode and merge different information flows

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2016.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2016.png)

    e.g. 

    1. **process different modalities** (e.g. img, sound)
    2. Metric learning (Siamese: [https://www.cs.cmu.edu/~rsalakhu/papers/oneshot1.pdf](https://www.cs.cmu.edu/~rsalakhu/papers/oneshot1.pdf))
    3. Relation learning

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2017.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2017.png)

    - Multi-output decoder: common representation

    **Multi-task learning**

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2018.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2018.png)

    ![Day%202%20Deep%20Learning%20Introduction/Untitled%2019.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2019.png)

- Teacher-student networks: learn to predict the output of another model

    **Model compression**

![Day%202%20Deep%20Learning%20Introduction/Untitled%2020.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2020.png)

- Modulation: adaptive modulation of the representation
    - Gating (LSTM)
    - Skip-connections
    - Batch norm
    - FiLM
    - Attention Mechanism

![Day%202%20Deep%20Learning%20Introduction/Untitled%2021.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2021.png)

- Bottom-up approach

![Day%202%20Deep%20Learning%20Introduction/Untitled%2022.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2022.png)

- Adversarial
- Auto-regressive, ...

## Take-home message

- With deep learning, we can define hypothesis classes with
computational graphs (i.e., architectures) easily.
- Creating a graph for a given task and dataset is part of the expertise.
- An architecture often has many layers; each is providing a new distributed representation to the next one that will be easier to process.
- Design patterns for architecture are emerging in the literature.

# Computational graph & backpropagation

## Ingredients for supervised DL

- A task definition
- An evaluation metric
- A large amount of high-quality labeled data (>100 000)
- A learning algorithm:
    - **End-to-end differentiable computational graph**
    - **A gradient calculation algorithm: backpropagation**
    - A parameter optimizer

## Loss function

- Give feedback on the model response for every single example (iid).
- We average the loss over all examples.
- The averaged loss defines contour lines in the parameter space.

## Review of Calculus

### Function approximation

![Day%202%20Deep%20Learning%20Introduction/Untitled%2023.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2023.png)

### Multivariate function

![Day%202%20Deep%20Learning%20Introduction/Untitled%2024.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2024.png)

### Total derivative

![Day%202%20Deep%20Learning%20Introduction/Untitled%2025.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2025.png)

## Computational Graph

Simple example

![Day%202%20Deep%20Learning%20Introduction/Untitled%2026.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2026.png)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2027.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2027.png)

Complex example

![Day%202%20Deep%20Learning%20Introduction/Untitled%2028.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2028.png)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2029.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2029.png)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2030.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2030.png)

### Backpropagation

→ Forward

![Day%202%20Deep%20Learning%20Introduction/Untitled%2031.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2031.png)

→ Backward

![Day%202%20Deep%20Learning%20Introduction/Untitled%2032.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2032.png)

Differential programming

### Module definition: dense module

![Day%202%20Deep%20Learning%20Introduction/Untitled%2033.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2033.png)

### Example

![Day%202%20Deep%20Learning%20Introduction/Untitled%2034.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2034.png)

Dense module serves as the transformer of input matrix into *representation.*

Loss function (l) is used to compare the results and target, evaluate the impact of a parameter change:

$$l(\theta):=\frac{1}{N}\sum_{k=1}^{N}l_k(\theta)$$

where N: # examples, n: # parameters.

### So the implementation of a module consists of:

- Forward(x): data transformation
- Backward(g): gradient transformation (output->input)
- Update(g): calculation of the parameter gradient

### Backpropagation: efficient computation

- Backward: efficient calculation of the chain rule (matrix version)
- Optimization: Jacobian*gradients
- Several examples can be stacked

## Application of Backpropagation

→ parameter initialization

→ Adversarial example:

![Day%202%20Deep%20Learning%20Introduction/Untitled%2035.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2035.png)

[Inceptionism: Going Deeper into Neural Networks](https://ai.googleblog.com/2015/06/inceptionism-going-deeper-into-neural.html)

## Take-home message

- We can compute the linear approximation of a function represented as
a computational graph.
- Backpropagation is a dynamic programming algorithm that computes
the gradient efficiently.
- Backpropagation performs a forward and a backward pass.

# Optimization in DL

---

## Ingredients for supervised DL

- A task definition
- **An evaluation metric: we want to maximize the model performance**
- A large amount of high-quality labeled data (>100 000)
- A learning algorithm:
    - End-to-end differentiable computational graph
    - A gradient calculation algorithm: backpropagation
    - **A parameter optimizer**

## Objective

Three concepts to remember:

1. Preconditioning
2. Momentum
3. Stochastic optimization

## Global v.s. Local minimum (min within a range)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2036.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2036.png)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2037.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2037.png)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2038.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2038.png)

### Linear approximation

![Day%202%20Deep%20Learning%20Introduction/Untitled%2039.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2039.png)

### How to find extrema?

However there are other points with a slope = 0.

![Day%202%20Deep%20Learning%20Introduction/Untitled%2040.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2040.png)

### Critical point

![Day%202%20Deep%20Learning%20Introduction/Untitled%2041.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2041.png)

If x* is an extremum (local or global), then it is a critical point.

The critical point does not imply an extremum. (e.g. saddle point is not an extremum)

### Parameter landscape for DL

![Day%202%20Deep%20Learning%20Introduction/Untitled%2042.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2042.png)

### Learning is not only optimizing

![Day%202%20Deep%20Learning%20Introduction/Untitled%2043.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2043.png)

Global minimum can be overfitting. Good model can stay in local minimum.

![Day%202%20Deep%20Learning%20Introduction/Untitled%2044.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2044.png)

Second order term is `q(h)` → convex optimization

### Quadratic function

![Day%202%20Deep%20Learning%20Introduction/Untitled%2045.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2045.png)

`q(h)` should be positive definite to make sure the extrema are minima.

### Gradient descent (steepest descent method)

![Day%202%20Deep%20Learning%20Introduction/Untitled%2046.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2046.png)

Eta is the learning rate, as well as hyperparameter.

### Conditioning problem

![Day%202%20Deep%20Learning%20Introduction/Untitled%2047.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2047.png)

### Problem of physical units

![Day%202%20Deep%20Learning%20Introduction/Untitled%2048.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2048.png)

## Preconditioning

![Day%202%20Deep%20Learning%20Introduction/Untitled%2049.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2049.png)

The size of M_t is number of parameters → the inverse operation is required. and M_t is diagonal. `f'` is the direction. M_t is positively definit

### RMSProp → Precoditioning > faster

![Day%202%20Deep%20Learning%20Introduction/Untitled%2050.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2050.png)

## Momentum

![Day%202%20Deep%20Learning%20Introduction/Untitled%2051.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2051.png)

**→ Gradient descent with momentum**

![Day%202%20Deep%20Learning%20Introduction/Untitled%2052.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2052.png)

Accelerate if the gradient was at the same direction as of last run. Keep the last run's direction.

![Day%202%20Deep%20Learning%20Introduction/Untitled%2053.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2053.png)

### Adam → Adaptive moment estimation

![Day%202%20Deep%20Learning%20Introduction/Untitled%2054.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2054.png)

Recommended as 1st run. Beta 1 and Beta 2 should be fixed.

## Stochastic optimization

### Stochastic gradient descent

![Day%202%20Deep%20Learning%20Introduction/Untitled%2055.png](Day%202%20Deep%20Learning%20Introduction/Untitled%2055.png)

Approximation is sampled to reduce the time of computation. `K << N`. Update the parameters by each mini-batch.

Be aware of the size of mini-batch to avoid left-over to be too small, don't make the size too big to make **stochastic process** work as regularization. **→ Mini Batch can be hyper-parameter, start with 2^n.**

**Stochastic optimization is having Convergence Problem.** 

However the Stochastic optimization can help escape the saddle point, and the validation/test set performs better. The noise can have a beneficial effect to regularize the model.