---
title: "Setup"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 411
tags:
  - advanced
---
To create a custom image classification model, we need to use a graphics processing unit (GPU) enabled training job instance. GPUs are excellent at parallelizing the computations required to train a neural network for this project. In order to access a GPU-enabled training job instance, you must submit a request for a service limit increase to the [AWS Support Center](https://console.aws.amazon.com/support/home?#/case/create). You can follow the instructions [here]() to increase your limit. For this recipe we will use a single *ml.p2.xlarge* instance. 

### Request a GPU-enabled Amazon SageMaker Training Instance

1. Open the [AWS Support Center console](https://console.aws.amazon.com/support/home#/case/create).

1. On the **AWS Support Center** page, choose **Create Case** and then choose Service limit increase.

1. In the **Case classification** panel under **Limit type**, search for Amazon SageMaker.

1. In the **Request** panel, choose the **Region** that you are working in. For **Resource Type**, choose **SageMaker Training**.

1. For **Limit** choose **ml.p2.xlarge instances**.

1. For **New Limit Value**, verify that the value is **1**.

1. In **Case description**, provide a brief explanation of why you need the **Service limit increase**. For example, I need to use this GPU-enabled training job instance to train a deep learning model using TensorFlow. I'll use this model on an AWS DeepLens device.

1. In **Contact options**, provide some details about how you would like to be contacted by the AWS service support team on the status of your **Service limit increase** request.

1. Choose **Submit**.
