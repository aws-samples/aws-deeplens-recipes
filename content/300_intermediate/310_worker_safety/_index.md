---
title: "Worker Safety"
date: 2020-03-04T10:15:55-07:00
chapter: true
draft: false
weight: 32
---

Use AWS DeepLens and Amazon Rekognition to build an application that helps identify if a person at a construction site is wearing the right safety gear, in this case, a hard hat. 

### Learning objectives

In this lab you will learn the following:

- Create and deploy an object detection project to AWS DeepLens.
- Modify the AWS DeepLens object detection inference Lambda function to detect persons and upload the frame to Amazon S3.
- Create a Lambda function to identify persons who are not wearing safety hats.
- Analyze the results using AWS IoT , Amazon CloudWatch and a web dashboard.

### Prerequisites

For this tutorial you will need to know how to:

* [Register your DeepLens device](/100_getting_started/130_register_your_deeplens_device/)
* [Deploy a sample project](/200_begineer/210_deploy_a_sample_project/)

Please revisit these sections if you are not familiar with the steps.

You will also need an Amazon S3 bucket, see [here for how to create one](/100_getting_started/140_setup_data_storage_with_amazon_s3/).

#### Skills required

* Basic coding skills

#### Time

* 1-2 hrs

### Architecture

![](/images/030_detecting_hard_hats/arch.png)

Follow the modules below or refer to the online course to learn how to build the application in 30 minutes.

### Online course 

You can find the video version of this tutorial in an online course.

[![Online Course](/images/030_detecting_hard_hats/worker-safety-sc.png)](https://www.aws.training/learningobject/wbc?id=32077)

https://www.aws.training/Details/eLearning?id=32077
