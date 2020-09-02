---
title: Day 1 AI/ML/DL Introduction
date: 2019-10-07 14:32:46
---

# Day 1: AI/ML/DL Introduction

# Mila Introduction

---

[Day%201%20AI%20ML%20DL%20Introduction/Mila-Intro_IVADO-DLSS_sept19_ENG.pdf](Day%201%20AI%20ML%20DL%20Introduction/Mila-Intro_IVADO-DLSS_sept19_ENG.pdf)

# Machine learning and experimental protocol

---

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day1/1_machine_learning_methodology.pdf)

## AI vs ML

ref: [http://aima.cs.berkeley.edu/cover.html](http://aima.cs.berkeley.edu/cover.html)

AI: simulate intelligence on a machine

ML: simulate learning from examples on a machine

DL: Set of machine learning techniques that implement the idea that **learning in high-dimensional space** occurs with **distributed representations** computed with **specific architectures**.

## ML methodology

1. Define a task to be done by the computer: `what` not `how`
2. Define the performance measures used to evaluate the quality of the algorithm performing the task.
3. Gather the data required to train models.
4. Perform the experiments to find the best models.
5. Deploy the best models in production.
6. Retrieve more data and iterate.

> When do you need ML: hard to program, complex data, adaptivity of the algorithm over time

### 1: Example of task:

"Detecting **violent** scenes in videos" / "Detecting **spam** emails" → **violent/Spam** can be varying by users

From task to realistic task:
→ Evidence gathered in research, available data to learning, computational resources
→ read scientific literature and attend scientific conferences

### 2: Good performance measures:

- evaluate if the algorithm successfully achieves a specific goal,
- be easy to interpret and communicate,
- be generic for evaluating different algorithms.

We must evaluate the performance measures in a **production context**.

### 3: Gather data:

- Annotating data takes time and costs money.
- Never sacrifice quality for quantity.
- Always use mechanisms to ensure **quality**.
- Identify opportunities for data collection. → feedback loop

## Experimental protocol

### Hyper-parameters:

- An hyperparameter is a parameter of the learning algorithm as
opposed to a model parameter,
- It cannot be optimized directly by the model optimizer,

Tool: Oríon ([https://orion.readthedocs.io](https://orion.readthedocs.io/))

### Experiment pipeline:

1. Define an experiment,
2. Create the input pipeline,
3. Create visualization tools,
4. Create the training loop,
5. Create the hyperparameter tuning loop.

### Reproducibility of Experiments:

[MLflow - A platform for the machine learning lifecycle](https://www.mlflow.org/)

Why we need experimental protocol: [http://www.youtube.com/watch?v=0hiXKTa735s](http://www.youtube.com/watch?v=0hiXKTa735s)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled.png)

Never use a training example for evaluating an algorithm; otherwise the performance metric will be higher than in production.

### Training / Validation / Test

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%201.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%201.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%202.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%202.png)

Check also Test result in within `Avg` and `Std` range

### **Deployment and Engineering**

DS should have the freedom to explore all possible models then put into the constraint of deployment and engineering.

How to detect and deal with prediction errors?
Other constraints must be respected:

- Latency

- Memory usage

- Computational power

How the new algorithm interacts with other components?
- If too complicated to analyze, A/B testing can be an alternative.

### What is data?

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%203.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%203.png)

Data should be:

- **self-contained**: all relevant features are present
- **independent**: no impact on one the other
- **identically distributed**: the probability of seeing an example is fixed

### **Pitfall of independence**

Temporal correlation: solution is buffer, question is buffer width

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%204.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%204.png)

Spatial Correlation: buffer

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%205.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%205.png)

**Pitfall of identically distributed → all year instead of season specific (on images)**

### **Specification of Structured data: don't say we have a lot of data but the structure**

### **Representation**

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%206.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%206.png)

There was actual machine: Mark 1 Perceptron

Text representations: dictionary based [tokenization] → Local representation [one-hot encoding]

### **Normalization**

Min-max feature scaling and standardization

# Introduction to ML

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day1/2_introduction_machine_learning.pdf)

## Types of Learning

### Supervised learning

→ regression

→ classification

→ multi-class: one class among a predefined set

→ multi-label: choose all relevant classes among a predefined set

### Active vs. passive learning

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%207.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%207.png)

### Unsupervised learning

Learn representation that will be useful. To get the probabilistic.

- Clustering
- Anomaly detection
- Data generation (auto-encoding)
    - Image (GAN), speech, text synthesis (GPT2),
    - model-based control, ...
- Semi-supervised learning → progress within 6 months

### Online vs. batch learning protocol

Online: each e.g. must be treated on-the-fly

Batch: DL

## Supervised learning

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%208.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%208.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%209.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%209.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2010.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2010.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2011.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2011.png)

0/1 loss for classification / Square loss for regression

### Definition of risk

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2012.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2012.png)

### Definition of the empirical risk

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2013.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2013.png)

Main question in ML

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2014.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2014.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2015.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2015.png)

### Bias vs. variance

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2016.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2016.png)

More data can improve theoretically:

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2017.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2017.png)

In practice No. 

Data is necessary, but not sufficient. → Dimensionality Curse

### Manifold Assumption

In high-dimension, a lower-dimensional manifold supports the data distribution. The data representation has too many degrees of freedom compared to the underlying system.

### Inductive bias

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2018.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2018.png)

## Hyper-parameter tuning and model selection

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2019.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2019.png)

Overfitting is diagnosed by set up validation set firstly then on test set

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2020.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2020.png)

Early stopping help reduce the overfitting

H can haave a complex structure, we find the model with minimal risk for each family with the **training set.** We evaluate the ERM predictors on **the validation set.** To avoid overfit of validation set, we use **test set** to avoid.

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2021.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2021.png)

# Machine Learning Tools

## Python & DS Eco-system

[](https://github.com/mila-iqia/ivado-mila-dl-school-2019/blob/master/presentations/day1/3_machine_learning_tools.pdf)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2022.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2022.png)

## Deep Learning Frameworks

A good deep learning framework will allow users to easily implement neural networks and facilitate deployment for production. These libraries are built around the idea of computational graphs that summarize the operations of a network and facilitate and optimize things like backpropagation and gradient descent.

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2023.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2023.png)

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2024.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2024.png)

## Project Management

Colab and **jupyter notebooks** are great tools for quick iteration, data visualization
and sharing ideas. **They are not ideal** for scaling and deploying **large projects** and
can get cluttered very quickly. Here are some tips for good python code and projects:

- Maintain an organized codebase
- Use Version Control for your code (Git)
- Use virtual environments
- Use unit tests

### Organized Codebase

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2025.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2025.png)

Use OOP as much as possible

### Git

`git blame`

### Virtual Environments → Conda

Since python relies on external libraries, versions matter. Libraries might get updated with **breaking changes** which can be dangerous for legacy code. The solution to this is **isolating** each project with their own virtual environment. Even python versions can have an impact on code execution.

### Unit Tests → pytest

[pytest: helps you write better programs - pytest documentation](https://docs.pytest.org/en/latest/)

It is good practice to write unit tests that you expect your code to pass every time you run it. This is a great way to catch bugs early and ensure that code works as expected in an explicit way.

## Experiment Management

[Tutorial](https://mlflow.org/docs/latest/tutorial.html)

[TensorBoard: Visualizing Learning | TensorFlow Core](https://www.tensorflow.org/guide/summaries_and_tensorboard)

### Hyperparameter Opt

Search Space → find best parameter

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2026.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2026.png)

### Exp. Management Libraries

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2027.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2027.png)

## Hardware

### GPU vs. CPU

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2028.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2028.png)

### GPU x CPU x TPU

As deep learning becomes more prevalent, chips have become increasingly specialized for deep learning. Google has developed their own Tensor Processing Unit (TPU) to rival the GPU.

### Performance

[tf.data Performance | TensorFlow Core](https://www.tensorflow.org/beta/guide/data_performance)

An optimized combined GPU and CPU resources to leverage whole experiences

### Edge Computing

CPU is good for inference, while on Edge Computing (i.e. on-device computation), GPUs access is not guaranteed.

### Cloud Computing Cost + Hardware

[DAWNBench](https://dawn.cs.stanford.edu/benchmark/)

[](https://towardsdatascience.com/maximize-your-gpu-dollars-a9133f4e546a)

### Cloud Computing Pipeline

![Day%201%20AI%20ML%20DL%20Introduction/Untitled%2029.png](Day%201%20AI%20ML%20DL%20Introduction/Untitled%2029.png)

# Tutorial

## **Data augmentation**

---

Very often, we are provided with a dataset and there are no means to collect more. In this section, we quickly explore the technique of data augmentation, which consists of altering the images in the training dataset without changing the associated labels. By doing so, it is possible to artificially augment the number of images in our training dataset. For example, we can think of the flipping or the cropping/resizing operations for altering an image in the dataset without modifying its associated label. As a result, by learning on an higher number of images we may observe some performance gains and/or better model generalization. However, since data augmentation is creating artificially new examples from existing ones, the independence assumption is not respected. Thus, **we should not use data augmentation for the validation and the test sets.**