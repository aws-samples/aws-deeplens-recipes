---
title: "Collect and prepare training data"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 412
tags:
  - advanced
---
This walkthrough uses an ML algorithm called an ***image classification model***. These models learn to distinguish between different objects by observing many examples over many iterations. This post uses a technique called transfer learning to dramatically reduce the time and data required to train an image classification model. For more information about transfer learning with Amazon SageMaker built-in algorithms, see [How Image Classification Works](https://docs.aws.amazon.com/sagemaker/latest/dg/IC-HowItWorks.html). With transfer learning, you only need a few hundred images of each type of trash. As you add more training samples and vary the viewing angle and lighting for each type of trash, the model takes longer to train but improves its accuracy during inference, when you ask the model to classify trash items it has never seen before.

Before going out and collecting images yourself, consider using the many sources for images that are publicly available. Wewant images that have clear labels (often done by humans) on what’s inside the image. Here are some sources you could look for your use case:

* [AWS Open Data](https://aws.amazon.com/opendata) – Contains a variety of datasets sourced from trusted entities that share and open their datasets for general use. 
* [AWS Data Exchange](https://aws.amazon.com/data-exchange/) – Contains datasets that are both free and available for a fee or subscription charge. These are very well curated and labeled and therefore involve a charge in most cases. 
* [GitHub](https://github.com/) – Offers several public repos with image datasets. Make sure you comply with the terms and conditions and reference the original owners when your work is published.
* [Kaggle](https://www.kaggle.com/datasets) – Has a wide variety of public datasets. Also, they provide some interesting starter Jupyter notebooks. 
* Non-profit and [government organizations](https://data.gov/) – Often publish data for public use under certain terms. These can be a great source of data.
* [Amazon SageMaker Ground Truth](https://aws.amazon.com/sagemaker/groundtruth/) – Creates labeled datasets from your images. You can choose between automated labeling (recommended for common objects) or use human labelers or [AWS Marketplace](https://aws.amazon.com/marketplace) offerings for more specific labeling use cases. For more information, see [Build Highly Accurate Training Datasets with Amazon SageMaker Ground Truth](https://aws.amazon.com/getting-started/tutorials/build-training-datasets-amazon-sagemaker-ground-truth/).

A good practice for collecting images is to use pictures at different possible angles and lighting conditions to make the model more robust. The following image is an example of the type of image the model classifies into landfill, recycling, or compost.

![Image Example](/images/400_advanced/410_build_a_custom_ml/412_collect_training_data/datasetexample.jpg)

When you have your images for each type of trash, separate the images into folders. 

```
|-images

        |-Compost

        |-Landfill

        |-Recycle
```
After you have the images you want to train your ML model on, upload them to [Amazon S3](http://aws.amazon.com/s3). First, [create an S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html). For AWS DeepLens projects, the S3 bucket names must start with the prefix **deeplens-**.

This recipe provides a dataset of images labeled under the categories of recycling, landfill, and compost. 

