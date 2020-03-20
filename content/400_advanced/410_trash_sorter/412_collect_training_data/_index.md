---
title: "Collect and prepare training data"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 412
tags:
  - advanced
---
The type of ML algorithm we will be using today is called an image classification model. These models learn to distinguish between different types of objects by observing many examples over many iterations. We will use a technique called transfer learning to dramatically reduce the time and data required to train an image classification model. For more information on transfer learning with Amazon SageMaker built-in algorithms, make sure you read the [documentation](https://docs.aws.amazon.com/sagemaker/latest/dg/IC-HowItWorks.html). We will only need to collect a couple hundred images of each type of trash to get good accuracy. As we add more training samples and vary the viewing angle, lighting for each type of trash, our model will take longer to train but will improve its accuracy during inference, when we ask the model to classify trash items it has never seen before. 

Before going out and collecting images yourself, consider using the many sources for images that are publicly available. Wewant images that have clear labels (often done by humans) on what’s inside the image. Here are some sources you could look for your use case:

1. **AWS Open datasets:** Contains a variety of datasets sourced from trusted entities that are willing to share and open their datasets for general use. For more information, visit https://aws.amazon.com/opendata
2. **AWS Data Exchange:** You can find datasets that are both free and available for a fee or subscription charge. These are very well curated and labeled and therefore involve a charge in most cases. For more information, visit https://aws.amazon.com/data-exchange/
3. **GitHub:** There are several public repositories that offer image datasets. Make sure you comply with the terms and conditions, and that you are referencing the original owners when your work is published.
4. **Kaggle:** Has a wide variety of public datasets, from disease statistics to bird pictures. Also, they provide some interesting starter Jupyter notebooks. For more information visit: https://www.kaggle.com/datasets
5. **US government** and a lot of non-profit and research organizations publish data for public use under certain terms for time to time. These can be a great source of data for more information visit  https://data.gov/
6. Finally you can collect, label and use your own datasets, **Amazon SageMaker Groundtruth** can be used to create labeled datasets from your images. You can choose between automated labeling(recommended for common objects) or use human labelers or marketplace offerings for more specialized labeling use cases such as medical imagery, geospatial imagery or hand writing recognition etc. to name a few https://aws.amazon.com/getting-started/tutorials/build-training-datasets-amazon-sagemaker-ground-truth/

We will show you how to train an ML model using data that you would collect. 

{{< figure src="/images/400_advanced/410_build_a_custom_ml/412_collect_training_data/datasetexample.jpg" title="A sample image, can you guess which category this would fall into? (hint: it’s not compost)" >}}

A good practice for collecting images is to take pictures at different possible angles and lighting conditions to make the model more robust.

Once we have the images for each type of trash, we separate the images of different types of trash into their own folders.

```
|-images

        |-Compost

        |-Landfill

        |-Recycle
```

Once we have the images we want to train our ML model on, we will upload it into Amazon S3.  First, create a [S3 bucket using the AWS Console](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html). For AWS DeepLens projects the Amazon S3 bucket names must start with the prefix “**deeplens-**”.

To save you some time, we put together a dataset of images already labeled under the categories Recycling, Landfill and Compost. We will load these images in the next section.
