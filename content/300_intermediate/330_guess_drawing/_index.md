---
title: "Building a Pictionary-style game"
date: 2020-08-04T10:15:55-07:00
chapter: true
draft: false
weight: 330
tags:
  - intermediate
---
## Building a Pictionary-style game with AWS DeepLens and Amazon Alexa

Are you bored of the same old board games? Tired of going through the motions with charades week after week? In need of a fun and exciting way to mix up game night? Well we have a solution for you!

From the makers of [AWS DeepLens](https://aws.amazon.com/deeplens/), Guess My Drawing with DeepLens is a do-it-yourself recipe for building your very own Machine Learning (ML)-enabled Pictionary-style game! In this post, you learn how to harness the power of AWS DeepLens, the AWS programmable video camera for developers to learn ML, and [Amazon Alexa](https://developer.amazon.com/alexa), the Amazon cloud-based voice service.

You start by learning to deploy a trained model to AWS DeepLens that can recognize sketches drawn on a whiteboard and pair it with an Alexa skill that serves as the official scorekeeper.

When your recipe is complete, the fun begins!

### Solution overview

Guess My Drawing with AWS DeepLens uses Alexa to host a multi-player drawing challenge game. To get started, gather your game supplies mentioned in the **Prerequisites** section.

To initiate gameplay, simply say, “Alexa, play Guess My Drawing with DeepLens.” Alexa explains the game rules and asks how many players are playing the game. The players decide the turn order.

Alexa provides each player with a common word. For example, Alexa may say, “Your object to draw is bowtie.” The player has 12 seconds to draw it on a whiteboard without writing letters or words.

When time runs out, the player stops drawing and asks Alexa to share the results. The ML model running on AWS DeepLens predicts the object that you drew. If the object matches with what Alexa asks, Alexa awards 10 points. If DeepLens can’t correctly guess the drawing or the player takes more than 12 seconds to draw, no points are earned.

Alexa prompts the next participant with their word, repeating until all participants have taken a turn. After each round, Alexa provides a score update. The game ends after five rounds, and whoever has the highest score wins the game!

The following diagram shows the architecture of our solution.

![Architecture](/images/300_intermediate/330_guess_drawing/arch.jpg)

This tutorial includes the following steps:

1. Create an AWS DeepLens inference [AWS Lambda](https://aws.amazon.com/lambda/) function to isolate the drawing area and feed each camera frame into the model to generate predictions on the sketches.
2. Deploy a pre-trained trained model included in this post to AWS DeepLens to perform image classification.
3. Create an [AWS IoT Core](https://aws.amazon.com/iot-core/) rule to send the results to [Amazon Kinesis Data Streams](https://aws.amazon.com/kinesis/data-streams/).
4. Create a custom [Alexa skill](https://developer.amazon.com/en-US/alexa/alexa-skills-kit) with a different Lambda function to retrieve the detected objects from the Kinesis data stream and have Alexa verbalize the result to you.

### Prerequisites

Before you begin this tutorial, make sure you have the following prerequisites:

- An AWS account
- An AWS DeepLens device, available from the following:
  - [Amazon.com](https://www.amazon.com/dp/B07JLSHR23) (US)
  - [Amazon.ca](https://www.amazon.ca/dp/B07JLSHR23) (Canada)
  - [Amazon.co.jp](https://www.amazon.co.jp/dp/B07F17K9J2) (Japan)
  - [Amazon.de](https://www.amazon.de/dp/B07KYKY2K7) (Germany)
  - [Amazon.co.uk](https://www.amazon.co.uk/dp/B07KYLSRZM) (UK)
  - [Amazon.fr](https://www.amazon.fr/dp/B07KYKY2K7) (France)
  - [Amazon.es](https://www.amazon.es/dp/B07KYKY2K7) (Spain)
  - [Amazon.it](https://www.amazon.it/dp/B07KYKY2K7) (Italy)
- An Amazon Alexa device
- A whiteboard or paper pad (unlined)
- A marker or writing utensil