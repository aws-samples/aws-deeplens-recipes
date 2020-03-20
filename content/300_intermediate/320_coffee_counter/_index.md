---
title: "Track coffee consumption"
date: 2020-03-03T10:15:55-07:00
draft: false
weight: 320
---
In this recipe, we’ll show you how to build a simple face detection application that counts the number of cups of coffee that people drink and displays the tally on a leaderboard.

We will go through the following steps:

+ Step 1: Deploy a sample project
+ Step 2: Change the inference AWS Lambda function
+ Step 3: Create a coffee detection backend
+ Step 4: Deploy the app to AWS Elastic Beanstalk

## Prerequisites

For this tutorial you will need to know how to:

* [Register your DeepLens device](/100_getting_started/130_register_your_deeplens_device/)
* [Deploy a sample project](/200_begineer/210_deploy_a_sample_project/)

Please revisit these sections if you are not familiar with the steps.

#### Skills required

* Basic coding skills
* Familiarity with the command line interface

#### Time

* 2 hrs

## Project Overview

Let’s review the following architectural diagram for the project. The AWS DeepLens device enables you to run deep learning on the edge. It detects a scene and runs it against a face detection model.

When the model detects a face, it uploads a frame to Amazon S3. An AWS Lambda function then runs the frame against AWS Rekognition to detect a mug in the scene and check if a face has been detected before or if is it a new face. After a face is registered or recognized, it’s stored in [Amazon DynamoDB](https://aws.amazon.com/dynamodb/), which is used as an incremental counter for a web application.

![](/images/040_track_coffee_consumption/coffee-counter-1.gif)

Following this post, you’ll be able to replicate the architecture and get the necessary information to build an application like this.

![](/images/040_track_coffee_consumption/coffee-counter-2.jpg)
