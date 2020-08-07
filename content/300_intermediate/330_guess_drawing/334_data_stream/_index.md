---
title: "Sending results from AWS DeepLens to a data stream"
weight: 334
chapter: true
draft: false
tags:
  - intermediate
---
In this section, you learn how to send messages from AWS DeepLens to a Kinesis data stream by configuring an AWS IoT rule.

On the Kinesis console, create a new data stream.

For **Data stream name**, enter a name.

For **Number of shards**, choose 1.

Choose **Create data stream**.

![](/images/300_intermediate/330_guess_drawing/kinesis-1.jpg)

On the [AWS IoT console](http://console.aws.amazon.com/iot), under **Act**, choose **Rules**.

Choose **Create** to set up a rule to push MQTT messages from AWS DeepLens to the newly created data stream.

![](/images/300_intermediate/330_guess_drawing/iot-1.jpg)

On the **Create a rule** page, enter a name for your rule.

For **Rule query statement**, enter the DeepLens device MQTT topic.

Choose **Add action**.

![](/images/300_intermediate/330_guess_drawing/iot-2.jpg)

Choose **Send a message to an Amazon Kinesis Stream**.

Choose **Configuration**.

Choose the data stream you created earlier.

For **Partition key**, enter `${newuuid()}`.

Choose **Create a new role** or **Update role**.

Choose **Add action**.

Choose **Create rule** to finish the setup.

![](/images/300_intermediate/330_guess_drawing/iot-3.jpg)

Now that the rule is set up, MQTT messages are loaded into the data stream.

**Amazon Kinesis Data Streams is not currently available in the [AWS Free Tier](https://aws.amazon.com/free/)**, which offers a free trial for a group of AWS services. For more information about the pricing of Amazon Kinesis Data Streams, see [link](https://aws.amazon.com/blogs/machine-learning/building-a-pictionary-style-game-with-aws-deeplens-and-amazon-alexa/Amazon Kinesis Data Streams pricing).

We recommend that you delete the data stream after completing the tutorial because **charges occur on an active data stream even when you aren’t sending and receiving messages**.

