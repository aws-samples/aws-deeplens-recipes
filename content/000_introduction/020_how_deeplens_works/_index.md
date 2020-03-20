---
title: "How AWS DeepLens works"
weight: 20
chapter: true
draft: false
tags:
  - introduction
---

## How AWS DeepLens Works

AWS DeepLens enable developers to learn how to build machine learning (ML) powered applications by taking machine learning models and applying them to video frames to produce predictions.

You can run machine learning models onboard DeepLens without sending any data to the cloud or you can use DeepLens to send images back to be processed by cloud-based image analysis.

Throughout this site, you'll come across some terminology that you might not be familiar with. Here's how we'll use each of these terminologies through the site.

## Terminology

Model: A set of math operations that tell computers how to transform an input (for example an image) into an output (predictions). 

Training: The machine learning process through which a computer algoritms learns patterns in the data and how to tranform the inputs into the desired outputs. The output of the training process is a machine learning model.

Inference: The machine learning step where the ML model is shown new data and asked to make predictions based on what it has learned.

AWS DeepLens (Lambda) Inference Function - A user-defined code package that runs on the AWS DeepLens device to apply a machine learning model to camera frames and return predictions.

Project: An AWS DeepLens project is a package of an AWS Lambda function and a ML model that can be sent to the AWS DeepLens device.

Internet-of-Things (IoT) - The concept of connecting any electronnic device with the internet and/or each other. 

AWS IoT GreenGrass - An AWS service to manage, send and receive data from connected devices.