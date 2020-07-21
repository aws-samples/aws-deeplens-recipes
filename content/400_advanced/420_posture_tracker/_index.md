---
title: "Build a posture tracker with GluonCV"
date: 2020-03-03T10:15:55-07:00
chapter: true
draft: false
weight: 420
tags:
  - advanced
---
## Build a posture tracker with GluonCV

Working from home can be a big change to your ergonomic setup, which can make it hard for you to keep a healthy posture and take frequent breaks throughout the day. To help you maintain good posture and have fun with machine learning (ML) in the process, this post shows you how to build a posture tracker project with [AWS DeepLens](https://aws.amazon.com/deeplens/), the AWS programmable video camera for developers to learn ML. You will learn how to use the latest pose estimation ML models from [GluonCV](https://gluon-cv.mxnet.io/) to map out body points from profile images of yourself working from home and send yourself text message alerts whenever your code detects bad posture. GluonCV is a computer vision library built on top of the Apache MXNet ML framework that provides off-the-shelf ML models from state-of-the-art deep learning research. With the ability run GluonCV models on AWS DeepLens, engineers, researchers, and students can quickly prototype products, validate new ideas, and learn computer vision. In addition to detecting bad posture, you will learn to analyze your posture data over time with [Amazon QuickSight](https://aws.amazon.com/quicksight/), an AWS service that lets you easily create and publish interactive dashboards from your data.

### Solution overview

This tutorial includes the following steps:

1. Experiment with AWS DeepLens and GluonCV
2. Classify postures with the GluonCV pose key points
3. Deploy pre-trained GluonCV models to AWS DeepLens
4. Send text message reminders to stretch when the tracker detects bad posture
5. Visualize your posture data over time with Amazon QuickSight

The following diagram shows the architecture of our posture tracker solution.

![Reference Architecture](/images/400_advanced/420_posture_tracker/posture-tracker-arch.png)

### Prerequisites

Before you begin this tutorial, make sure you have the following prerequisites:

- An AWS account
- An AWS DeepLens device. Available on the following Amazon websites:
  - [Amazon.com](https://www.amazon.com/dp/B07JLSHR23) (US)
  - [Amazon.ca](https://www.amazon.ca/dp/B07JLSHR23) (Canada)
  - [Amazon.](https://www.amazon.co.jp/dp/B07F17K9J2)[co.jp](https://www.amazon.co.jp/dp/B07F17K9J2) (Japan)
  - [Amazon.](https://www.amazon.de/dp/B07KYKY2K7)[de](https://www.amazon.de/dp/B07KYKY2K7) (Germany)
  - [Amazon.](https://www.amazon.co.uk/dp/B07KYLSRZM)[co.uk](https://www.amazon.co.uk/dp/B07KYLSRZM) (UK)
  - [Amazon.](https://www.amazon.fr/dp/B07KYKY2K7)[fr](https://www.amazon.fr/dp/B07KYKY2K7) (France)
  - [Amazon.](https://www.amazon.es/dp/B07KYKY2K7)[es](https://www.amazon.es/dp/B07KYKY2K7) (Spain)
  - [Amazon.](https://www.amazon.it/dp/B07KYKY2K7)[it](https://www.amazon.it/dp/B07KYKY2K7) (Italy)