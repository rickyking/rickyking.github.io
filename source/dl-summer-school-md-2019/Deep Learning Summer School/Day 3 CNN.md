---
title: Day 3 CNN
date: 2019-10-07 14:32:46
---


[CS231n Convolutional Neural Networks for Visual Recognition](http://cs231n.github.io/convolutional-networks/)

# CNN - Part 1

Animation: 

[jerpint/cnn-cheatsheet](https://github.com/jerpint/cnn-cheatsheet)

## Introduction

CNN start at 2012 ImageNet competition with AlexNet.

CNN is encouraging NN to focus on local features.

[CS231n Convolutional Neural Networks for Visual Recognition](http://cs231n.github.io/convolutional-networks/)

![Day%203%20CNN/Untitled.png](Day%203%20CNN/Untitled.png)

## Intuition

CNN is smoothing as in signal processing:

![Day%203%20CNN/Untitled%201.png](Day%203%20CNN/Untitled%201.png)

## Notion

### Kernel

Yellow `w(a)` is kernel.

1D Convolution

![Day%203%20CNN/Untitled%202.png](Day%203%20CNN/Untitled%202.png)

2D Convolution

![Day%203%20CNN/Untitled%203.png](Day%203%20CNN/Untitled%203.png)

`K` will be learnt by NN.

CNN is technically as cross-correlations.

### Kernel tweaking parameter

`F`: the size of the kernel/filter, typically square

`S`: Stride, represents the number of pixels to move in  both (x,y), usually S=1 or S=2

### 2D Convolution Arithmetic

![Day%203%20CNN/Untitled%204.png](Day%203%20CNN/Untitled%204.png)

### Padding

Since output is **smaller** than the input. Padding is to increase the **input** size with some strategy. Padding is denoted as `P`.

![Day%203%20CNN/Untitled%205.png](Day%203%20CNN/Untitled%205.png)

3 ways of padding:

1. Valid convolution
2. Same convolution
3. Full convolution

Padding Strategies:

- Zero padding

![Day%203%20CNN/Untitled%206.png](Day%203%20CNN/Untitled%206.png)

- Reflect padding

![Day%203%20CNN/Untitled%207.png](Day%203%20CNN/Untitled%207.png)

- Symmetric padding

![Day%203%20CNN/Untitled%208.png](Day%203%20CNN/Untitled%208.png)

### Depth/Channels → `D`

We can use more than one kernel to compute many feature maps. The depth, or number of kernels, denoted as `D`, represents how many kernels will be used and will determine the depth of the volume (number of channels) of the output. The resulting feature maps are stacked to produce a volume.

[https://github.com/jerpint/cnn-cheatsheet/blob/master/assets/Conv2D_depth.gif](https://github.com/jerpint/cnn-cheatsheet/blob/master/assets/Conv2D_depth.gif)

### Bias

![Day%203%20CNN/Untitled%209.png](Day%203%20CNN/Untitled%209.png)

### Pooling

Most common type is **max-pooling**. Help NN less sensitive to fine details. → empirically giving good results. Object is to downsample the image.

![Day%203%20CNN/Untitled%2010.png](Day%203%20CNN/Untitled%2010.png)

### Dilated Convolution (atrous convolution)

Dilation coefficient `d` determines the pixel spacing between convolutions.

[Understanding 2D Dilated Convolution Operation with Examples in Numpy and Tensorflow with Interactive Code](https://towardsdatascience.com/understanding-2d-dilated-convolution-operation-with-examples-in-numpy-and-tensorflow-with-d376b3972b25)

### Transposed Convolution

**Transposed convolutions** are used to **increase** the resolution of a **feature map.** It can be thought of as an interpolation, or a convolution on the output. It is often used in the context of **upsampling** the output of a network.

[Convolution arithmetic tutorial - Theano 1.0.0 documentation](http://deeplearning.net/software/theano/tutorial/conv_arithmetic.html)

## Image Representations

Grayscale → 1 Channel

RGB representation → 3 channels

### 2D Convolution on an RGB image

![Day%203%20CNN/Untitled%2011.png](Day%203%20CNN/Untitled%2011.png)

![Day%203%20CNN/Untitled%2012.png](Day%203%20CNN/Untitled%2012.png)

To create depth, just use multiple kernels (`D`).

### 1 x 1 convolution

1 x 1 convolutions are useful in the context of non-linear channel reduction. They allow us to preserve the width and height and reduce on the depth. They also require few parameters.

## CNN Building Blocks

### Task

From Image to Classes

### Common Blocks

![Day%203%20CNN/Untitled%2013.png](Day%203%20CNN/Untitled%2013.png)

- Convolution: learned kernels
- Non-Linear Activation: i.e. ReLU or tanh
- Pooling: Max Pooling is commonly used in modern architectures.

Chain above `M` times:

![Day%203%20CNN/Untitled%2014.png](Day%203%20CNN/Untitled%2014.png)

## Motivations for CNNs

CNNs can learn useful features from images automatically. Instead of handcrafted, e.g. Sobel filter (extraction edges).

- It can be very hard and time consuming to handcraft these filters. Instead, we
use deep learning to **learn** the best filters.
- By applying convolutions to **images**, we can extract some high-level features
describing the images but it can be very challenging and limiting
- By applying convolutions to **features**, we can extract features of features at
different levels of abstraction.

![Day%203%20CNN/Untitled%2015.png](Day%203%20CNN/Untitled%2015.png)

- In MLP (Multi-layer Perceptron), each input can in theory contribute equally to a signal. CNNs introduce a way to focusing **hierarchically** at local features.

[](https://www.deeplearningbook.org/contents/convnets.html)

![Day%203%20CNN/Untitled%2016.png](Day%203%20CNN/Untitled%2016.png)

- **Receptive field** - Image patches contain information from larger patches of previous inputs. The resulting 3x3 patch seen here contains information from the previous 5x5 patch. The deeper you go, the wider the receptive field.

![Day%203%20CNN/Untitled%2017.png](Day%203%20CNN/Untitled%2017.png)

- **Parameter sharing**
    - weights are used at different areas of a same image which requires

        significantly less **memory** for models.

    - kernels can extract similar features at multiple places in an image
- Convolutions are **equivariant to translation** - i.e. translating an input in space
will result in a translated convolution output (e.g. shifted dogs in image)
- Useful features which have been learned can be **reused** at multiple regions in an
image.

### LeNet

Each Convolution layer is followed by Activation Function, such as `ReLU`.

![Day%203%20CNN/Untitled%2018.png](Day%203%20CNN/Untitled%2018.png)

[3D Visualization of a Convolutional Neural Network](http://scs.ryerson.ca/~aharley/vis/conv/)

### Global Average Pooling

Another method to flatten the volume is Global Average Pooling. It transforms a 3D volume to a 1D vector. It takes each 2D feature map and transforms them to singular values by taking the average of the feature map and concatenates them depth-wise.

[](https://arxiv.org/pdf/1312.4400v3.pdf)

# CNN - Part 2

## Gradient Descent in CNNs

1. Forward pass
2. Loss via backward pass
3. Update the tunable parameters

### Problem Setup

![Day%203%20CNN/Untitled%2019.png](Day%203%20CNN/Untitled%2019.png)

### Forward

![Day%203%20CNN/Untitled%2020.png](Day%203%20CNN/Untitled%2020.png)

### From Convolution to Matrix Multiplication

![Day%203%20CNN/Untitled%2021.png](Day%203%20CNN/Untitled%2021.png)

### Loss

Example is using L2 Norm as loss function.

![Day%203%20CNN/Untitled%2022.png](Day%203%20CNN/Untitled%2022.png)

We compute the error between the ground truth and the output predicted by the network.

### Gradient Descent

## Applications of CNNs

### Image Classification → is it a dog/bear/cat?

### Object Detection

Find a bounding box where the object is, as well as the category of the object the box belongs to.

### Image Segmentation

Which pixels correspond to the detected objects.

→ Mask_RCNN

[matterport/Mask_RCNN](https://github.com/matterport/Mask_RCNN)

→ AR in medical with microscope.

[An augmented reality microscope with real-time artificial intelligence integration for cancer diagnosis](https://www.nature.com/articles/s41591-019-0539-7.epdf?author_access_token=BI9AOTsesmNoV2lSdpucn9RgN0jAjWel9jnR3ZoTv0PDGU3ZwysZtsN41a2fOgaoj4PRxjTvAHjSFrKF_S_mq4QNNV8dNoxAjytIQuVz9vdjplLQHUSEPiIo392MzIJY8fqxLKHC5vIwNpLLEoXMnA%3D%3D)

### Caption Generation

A pipeline with CNN and NLP models to learn generate captions:

[](https://arxiv.org/pdf/1502.03044.pdf)

### Human Pose Estimation

[rachit2403/Open-Pose-Keras](https://github.com/rachit2403/Open-Pose-Keras)

[facebookresearch/DensePose](https://github.com/facebookresearch/DensePose)

### Road Mapping

![Day%203%20CNN/Untitled%2023.png](Day%203%20CNN/Untitled%2023.png)

[Mapping roads through deep learning and weakly supervised training](https://ai.facebook.com/blog/mapping-roads-through-deep-learning-and-weakly-supervised-training/)

### Sound Classification

Spectrograms of sounds can be computed using fourier transforms. The spectrograms can then be treated just like images in the context of sound classification.

![Day%203%20CNN/Untitled%2024.png](Day%203%20CNN/Untitled%2024.png)

[librosa.feature.melspectrogram - librosa 0.7.0 documentation](https://librosa.github.io/librosa/generated/librosa.feature.melspectrogram.html)

## GANs

Two separate networks compete against each other: generator (counterfeiter) and discriminator (art curator).

[Amateur restoration botches Jesus painting in Spain](https://www.pri.org/stories/2012-08-25/amateur-restoration-botches-jesus-painting-spain)

[Portrait of the artist as a conman](https://www.channel4.com/news/art-forgery-beltracchi-wolfgang-ernst-picasso-paraic-obrien)

### Definition

Mathematically, this can be expressed as a **MinMax** game, where the generator (G) tries to **minimize** the objective function V while the discriminator (D) tries to **maximize** it. Both networks are trained successively.

[](https://arxiv.org/pdf/1406.2661.pdf)

![Day%203%20CNN/Untitled%2025.png](Day%203%20CNN/Untitled%2025.png)

`D` and `G` are trained **iteratively.**

### DCGAN

similar idea to word2vec.

![Day%203%20CNN/Untitled%2026.png](Day%203%20CNN/Untitled%2026.png)

[](https://arxiv.org/pdf/1511.06434.pdf)

### Style Transfer

[](https://arxiv.org/pdf/1812.04948.pdf)

### CycleGAN

CycleGan introduced the concept of cycle-consistency loss to their GANs. This allowed training generators that would unpaired images from domain X to domain Y.

![Day%203%20CNN/Untitled%2027.png](Day%203%20CNN/Untitled%2027.png)

[Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks](https://junyanz.github.io/CycleGAN/)

### GANfield

[GANfield: Something, Something GAT Pun](https://vdalv.github.io/2018/12/04/ganfield.html)

## Datasets

- MNIST
- Fashion-MNIST
- CIFAR-10 (10 classes with 6000 images)
- CIFAR-100 (100 classes with 600 images)
- SVHN (Street View House Number dataset) → with bounding boxes, 600 000 labelled digits
- ImageNet: RGB with 256x256 resolution, of 3.2 million images
- COCO Dataset: **C**ommon **O**bjects in **CO**ntext with segmentation, rich level details

[COCO - Common Objects in Context](http://cocodataset.org/#home)

### Collecting your own dataset

- Look for similar datasets first
- Understand the kind of **labels** you need and how you want to represent them: from classification to objective detection to segmentation
- Labelling can be expensive and inaccurate
- Synthetic data (simulators, GANs, etc.) can work well to test ideas - it can also

    fail to generalize

- Be sure to have a robust and well guarded test set
- Always challenge the validity of your data

# CNN - Part 3 → Architectures

## Architecture evaluation and benchmarking

### Benchmarking

[Applications](https://keras.io/applications/)

Define `better` for a model is not obvious.

### Comparing models

Metric on publicly available dataset.

e.g. ILSVRC (ImageNet Large Scale Visual Recognition Challenge)

## Review of key CNN architectures

### AlexNet - Architecture

[Illustrated: 10 CNN Architectures](https://towardsdatascience.com/illustrated-10-cnn-architectures-95d78ace614d)

![Day%203%20CNN/Untitled%2028.png](Day%203%20CNN/Untitled%2028.png)

### AlexNet - Dropout

Turn of some connection with certain probability. 

AlexNet also used dropout in its architecture. This reduces the risk of overfitting and acts as a form of regularization. It can be thought of as training many models simultaneously, i.e. an ensemble of networks.

[](https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf)

### VGG - Deeper NN

[](https://arxiv.org/pdf/1409.1556.pdf)

We need to go deeper.

![Day%203%20CNN/Untitled%2029.png](Day%203%20CNN/Untitled%2029.png)

![Day%203%20CNN/Untitled%2030.png](Day%203%20CNN/Untitled%2030.png)

→ conv⟨receptive field size⟩-⟨number of channels⟩

They noticed that using multiple chains of 3x3 convolutions was more efficient than a single 7x7 convolution. Why?

Cheaper in parameter but same deceptive field.

> “It is easy to see that a stack of two 3×3 conv. layers (without spatial pooling in between) has an effective receptive field of 5×5; three such layers have a 7 × 7 effective receptive field.” - Simonyan et al.

### GoogLeNet

![Day%203%20CNN/Untitled%2031.png](Day%203%20CNN/Untitled%2031.png)

### GoogLeNet - Inception Module

[Going Deeper with Convolutions](https://arxiv.org/abs/1409.4842)

![Day%203%20CNN/Untitled%2032.png](Day%203%20CNN/Untitled%2032.png)

More layers but less parameters due to 1x1 convolution.

![Day%203%20CNN/Untitled%2033.png](Day%203%20CNN/Untitled%2033.png)

### ResNet

[](https://arxiv.org/pdf/1512.03385.pdf)

> “Driven by the significance of **depth**, a question arises: Is learning better networks as easy as **stacking more layers**? [...] Our 152-layer residual net is the **deepest** network ever presented on ImageNet, while still having **lower complexity** than VGG nets” 
- He et al.

![Day%203%20CNN/Untitled%2034.png](Day%203%20CNN/Untitled%2034.png)

Deeper network don't get the information from several layers before (information bottleneck). Hence ResNet introduce **Residual Blocks**.

### ResNet - Residual blocks

![Day%203%20CNN/Untitled%2035.png](Day%203%20CNN/Untitled%2035.png)

`Identity mapping`: the information from input of previous layers to pass information.

![Day%203%20CNN/Untitled%2036.png](Day%203%20CNN/Untitled%2036.png)

### ResNet - Batch Normalization

- Batch normalization was introduced as a form of **regularization** shortly after
GoogleNet.
- Prevents the network from getting stuck in the saturated modes
- It is used after each convolution in ResNet
- The network learns at each layer “how much” to normalize its inputs.

A scale of features maps based on mini-batch's mean and variance.

![Day%203%20CNN/Untitled%2037.png](Day%203%20CNN/Untitled%2037.png)

BatchNorm does 2 things: (1) normalize activations, and (2) introduce scaling and shifting parameters to each normalized activation. However, it turns out that (1) is not as important as (2). In the paper [How Does Batch Normalization Help Optimization?](https://t.co/mvCCL1DLYF), it was mentioned that “[BatchNorm] re-parametrizes the underlying optimization problem to make its landscape significantly smooth[er].” Intuition is this: because it’s now less bumpy, we can use a higher learning rate, hence faster convergence (see Fig. 3.1).

### EfficientNet

[](https://arxiv.org/pdf/1905.11946.pdf)

EfficientNet is currently the top performer on ILSVRC. It explores in a principled way how to scale networks in width, depth and resolution and proposes a new baseline architecture that is both faster and more accurate than previous SOTA.

![Day%203%20CNN/Untitled%2038.png](Day%203%20CNN/Untitled%2038.png)

## Transfer learning

We freeze trained CNN layers and retrain subsequent fully connected layers on our data. It works well if the domain of the new dataset is similar to ImageNet. Later we could unfreeze and retrain on convolutional layer.

![Day%203%20CNN/Untitled%2039.png](Day%203%20CNN/Untitled%2039.png)

### Backbone Networks

A lot of models will use a CNN backbone pre-trained on ImageNet as the starting point for different computer vision tasks. It serves as a generic feature extractor and can help complicated architectures converge quicker.

![Day%203%20CNN/Untitled%2040.png](Day%203%20CNN/Untitled%2040.png)

[Advancing state-of-the-art image recognition with deep learning on hashtags - Facebook Engineering](https://engineering.fb.com/ml-applications/advancing-state-of-the-art-image-recognition-with-deep-learning-on-hashtags/)

Use transfer learning from model trained on other dataset (#facebook) to smash the imageNet scores.

## Data augmentation

Data augmentation: artificially augment a dataset. Make sure the transformations making sense.

- Cropping
- Mirroring
- Rotation

## Structured outputs

Structured outputs allow us to predict **multiple outputs** that **relate** to one another. For example, in the case of an image, a structured output could be a **bounding box** or a per-pixel **segmentation mask** of our input.

![Day%203%20CNN/Untitled%2041.png](Day%203%20CNN/Untitled%2041.png)

### Bounding boxes

4*(x,y) pixel coordinates (8 total), but can be represented by 4 coordinates:

![Day%203%20CNN/Untitled%2042.png](Day%203%20CNN/Untitled%2042.png)

Intersection over Union (IoU) metric to measure how well the bounding box performs: 0 (worst) and 1 (best).

![Day%203%20CNN/Untitled%2043.png](Day%203%20CNN/Untitled%2043.png)

### Segmentation mask

A segmentation mask classifies pixels of the input image as belonging to a certain class or to the background. Classes are represented as integers. Segmentation masks can be overlaid on top of the original image.

Segmentation is done at the pixel level. In semantic segmentation, the entire image is classified, pixel by pixel. In instance segmentation, only regions of interest are segmented. Distinction between objects of the same class can be made.

![Day%203%20CNN/Untitled%2044.png](Day%203%20CNN/Untitled%2044.png)

[Mask R-CNN with OpenCV - PyImageSearch](https://www.pyimagesearch.com/2018/11/19/mask-r-cnn-with-opencv/)

### Faster R-CNN - Object Detection

[](https://arxiv.org/pdf/1506.01497.pdf)

![Day%203%20CNN/Untitled%2045.png](Day%203%20CNN/Untitled%2045.png)

### Mask R-CNN - Instance segmentation

[Image segmentation with Mask R-CNN](https://medium.com/@jonathan_hui/image-segmentation-with-mask-r-cnn-ebe6d793272)

![Day%203%20CNN/Untitled%2046.png](Day%203%20CNN/Untitled%2046.png)

### UNet - semantic segmentation

[](https://arxiv.org/pdf/1505.04597.pdf)

![Day%203%20CNN/Untitled%2047.png](Day%203%20CNN/Untitled%2047.png)

### SSD - Single Shot Detector → Realtime detection

![Day%203%20CNN/Untitled%2048.png](Day%203%20CNN/Untitled%2048.png)

### MobileNet

[](https://arxiv.org/pdf/1704.04861.pdf)

MobileNet is a backbone architecture that was introduced to **reduce** the computational complexity of the backbone models without sacrificing accuracy. It introduced the concept of *depthwise separable convolutions* which significantly **reduce the number of parameters** in a model.

### Model Comparison

[http://www.youtube.com/watch?v=V4P_ptn2FF4](http://www.youtube.com/watch?v=V4P_ptn2FF4)

### Speed vs. Accuracy

[](https://arxiv.org/pdf/1611.10012.pdf)

[Object detection: speed and accuracy comparison (Faster R-CNN, R-FCN, SSD, FPN, RetinaNet and YOLOv3)](https://medium.com/@jonathan_hui/object-detection-speed-and-accuracy-comparison-faster-r-cnn-r-fcn-ssd-and-yolo-5425656ae359)

## Interpreting Networks

[**GradCam](https://arxiv.org/abs/1610.02391): look at the gradient of a single-class prediction to interpret what the model is looking at.**

![Day%203%20CNN/Untitled%2049.png](Day%203%20CNN/Untitled%2049.png)

## FractalNet

[](https://arxiv.org/pdf/1605.07648.pdf)

![Day%203%20CNN/Untitled%2050.png](Day%203%20CNN/Untitled%2050.png)