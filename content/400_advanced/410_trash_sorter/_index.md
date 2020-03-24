---
title: "Build a custom ML model to sort trash"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 410
tags:
  - advanced
---
## Deeplens Trash Classiffication Recipe

Today we’re going to show you how to build a prototype trash sorter using [AWS DeepLens](https://aws.amazon.com/deeplens/), AWS’s deep learning-enabled video camera designed for developers to learn machine learning in a fun, hands-on way. This prototype trash sorter project teaches you how to train image classification models with custom data. Image classification is a powerful machine learning technique where a machine learning model learns how to distinguish between many different objects inside images by observing many examples. You can apply techniques learned in this tutorial to solve problems that require sorting objects into different bins based on imagery (such as sorting fruits by size or grade) or detecting the existence of an object in an image (such as recognizing the type of object at self-checkout). 

This tutorial was inspired by the smart recycle arm project created for the AWS Public Sector Builders Fair. For more information, see [Demonstration: Automatic Recycling](https://www.youtube.com/watch?v=QF0QjRjBwFs) on YouTube.

{{< youtube QF0QjRjBwFs >}}

### Solution overview

This walkthrough includes the following steps:

1. Collect and prepare your own dataset to feed into an ML algorithm
1. Train a model with [Amazon SageMaker](https://aws.amazon.com/sagemaker/), a fully managed service that provides the ability to build, train, and deploy machine learning (ML) models quickly
1. Running the model locally on AWS DeepLens to predict types of trash without sending any data to the cloud
1. Optionally, after AWS DeepLens makes its prediction, you can set up AWS DeepLens to send a message to a Raspberry Pi via AWS IoT Greengrass to show you which bin to throw the item in.

The following diagram illustrates the solution architecture.

![Reference Architecture](/images/400_advanced/410_build_a_custom_ml/architecturediagram.png)

### Prerequisites

To complete this walkthrough, you must have the following prerequisites:

* AWS Account
* An AWS DeepLens device. Available on [Amazon.com](https://www.amazon.com/dp/B07JLSHR23) (US), [Amazon.ca](https://www.amazon.ca/dp/B07JLSHR23) (Canada), [Amazon.co.jp](https://www.amazon.co.jp/dp/B07F17K9J2) (Japan), [Amazon.de](https://www.amazon.de/dp/B07KYKY2K7) (Germany), [Amazon.fr](https://www.amazon.fr/dp/B07KYKY2K7) (France), [Amazon.es](https://www.amazon.es/dp/B07KYKY2K7) (Spain), [Amazon.it](https://www.amazon.it/dp/B07KYKY2K7) (Italy).
* Raspberry Pi with Sense HAT (optional)

